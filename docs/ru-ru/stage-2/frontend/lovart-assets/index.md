<script setup>
import { relatedArticlesMap } from '@theme/data/relatedArticles'

const relatedArticles = relatedArticlesMap['en/stage-2/frontend/lovart-assets'] ?? []
</script>

# Начинаем с NanoBanana: создаём собственного агента для производства ассетов

## Глава 1. Сгенерируйте свой первый графический ассет за 1 минуту

Прежде чем углубляться в дизайн, стиль или промптинг, давайте сгенерируем первое изображение, сделав как можно меньше шагов.

### 1.1 Знакомство с NanoBanana

Прежде чем обсуждать стили дизайна и инженерию промптов, давайте разберёмся с кое-чем ещё более важным: **убедимся, что вы действительно можете сгенерировать изображение.**

Сегодняшние ведущие большие модели уже обладают возможностями генерации и редактирования изображений. Такие модели обычно называют **генеративными моделями.**

Чтобы максимально упростить процесс, в этом руководстве используется модель, которая уже обладает стабильными возможностями генерации и редактирования изображений, — NanoBanana. Это модель генерации изображений, выпущенная Google, официально называется **Gemini 3.1 Flash Image Preview**; она поддерживает генерацию изображений напрямую с помощью естественного языка, а также редактирование существующих изображений.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image1.png)

По возможностям она принципиально не отличается от других моделей, о которых вы могли слышать (таких как GPT-4o, Claude, Qwen, Midjourney и т. д.): **вы предоставляете описание, и модель генерирует результат.**

![](/ru-ru/stage-2/frontend/lovart-assets/images/image2.png)![](/ru-ru/stage-2/frontend/lovart-assets/images/image3.png)![](/ru-ru/stage-2/frontend/lovart-assets/images/image4.png)

Можете воспринимать её как «кисть». В этой главе нас интересует только одно:
👉 **сможет ли эта кисть сделать свой первый мазок в ваших руках.**

На практике NanoBanana можно использовать напрямую через официальные платформы, такие как **Google AI Studio**, или интегрировать в рабочие процессы разработки через **API**. В этом руководстве используется подход через API. Модель NanoBanana 2 также уже выпущена, и вы можете попробовать использовать новейшую большую модель.

### 1.2 Генерация уровня «Hello World»

Прежде чем начать, вам нужно выполнить всего три шага:

1. Создайте новую папку в Trae

![](/ru-ru/stage-2/frontend/lovart-assets/images/image5.png)

2. Создайте новый файл Python

![](/ru-ru/stage-2/frontend/lovart-assets/images/image6.png)

![](/ru-ru/stage-2/frontend/lovart-assets/images/image7.png)

![](/ru-ru/stage-2/frontend/lovart-assets/images/image8.png)

3. Вставьте полный код, приведённый ниже

Trae автоматически выполнит необходимую настройку окружения и установку зависимостей — никакой дополнительной конфигурации не требуется.

В коде используется API Key для NanoBanana. Процесс его получения мы здесь рассматривать не будем — главное, чтобы вы могли получить и подставить соответствующие параметры. **На данном этапе мы не ставим целью понять каждую строку кода, нам важно лишь, чтобы он успешно запустился.**

```Python
# /// script
# dependencies = [
#  "gradio>=4.0.0",
#  "pillow>=10.0.0",
#  "requests>=2.31.0",
# ]
# ///

import gradio as gr
import requests
import base64
from PIL import Image
import io
import os
import time
import re
from typing import Optional, Dict, Any, List

# Configure API information
NANOBANANA_API_URL: str = "YOUR API URL"
NANOBANANA_API_KEY: str = "YOUR API KEY"
OUTPUT_DIR: str = "outputs"

# Ensure output directory exists
os.makedirs(OUTPUT_DIR, exist_ok=True)

def image_to_base64_data_uri(image: Image.Image) -> str:
    """
    Convert a PIL image to an OpenAI API compatible data URI format.
    """
    buffer = io.BytesIO()
    # Convert to PNG for compatibility
    image.save(buffer, format="PNG")
    encoded = base64.b64encode(buffer.getvalue()).decode('utf-8')
    return f"data:image/png;base64,{encoded}"

def base64_to_image(base64_str: str) -> Optional[Image.Image]:
    """
    Convert a pure base64 string to a PIL Image.
    """
    try:
        image_bytes = base64.b64decode(base64_str)
        return Image.open(io.BytesIO(image_bytes))
    except Exception as e:
        print(f"Base64 decoding failed: {e}")
        return None

def extract_base64_from_response(content: Any) -> Optional[str]:
    """
    Core parsing logic: Extract image Base64 data from API response content.
    Compatible with both Markdown format and structured list format.
    """
    if not content:
        return None

    base64_data = None

    # 1. Try structured extraction (List)
    # Corresponding response format: [{"type": "image_url", "image_url": {"url": "data:..."}}]
    if isinstance(content, list):
        for part in reversed(content):  # Search in reverse, latest images are usually at the end
            if isinstance(part, dict):
                # Check image_url or output_image field
                img_field = part.get("image_url") or part.get("image") or part.get("output_image")
                if isinstance(img_field, dict):
                    url = img_field.get("url", "")
                    if url.startswith("data:image/") and "," in url:
                        return url.split(",", 1)[1].strip()

        # If no structured images in list, try concatenating text from list items to find Markdown
        text_parts = [
            str(p.get("text", ""))
            for p in content
            if isinstance(p, dict) and p.get("type") in ["text", "input_text"]
        ]
        content_str = "".join(text_parts)
    else:
        content_str = str(content)

    # 2. Try Markdown regex extraction (String)
    # Corresponding response format: "Here is your image: ![img](data:image/png;base64,AAAA...)"
    pattern = re.compile(r"!\[.*?\]\((data:image/[^;]+;base64,[^)]+)\)", re.IGNORECASE)
    match = pattern.search(content_str)

    if match:
        data_url = match.group(1)
        if "," in data_url:
            return data_url.split(",", 1)[1].strip()

    return None

def synthesize(prompt: str, input_image: Optional[Image.Image]) -> Optional[Image.Image]:
    """
    Call the Nanobanana API for generation.
    """
    if not prompt or not prompt.strip():
        gr.Warning("Please enter a prompt")
        return None

    print(f">>> Starting task: {prompt[:50]}...")

    headers = {
        "Content-Type": "application/json",
        "Authorization": f"Bearer {NANOBANANA_API_KEY}"
    }

    # Build payload conforming to OpenAI Vision / Chat standard
    messages = []

    if input_image is not None:
        # Image-to-image / multimodal input mode
        print(">>> Input image detected, using multimodal mode")
        img_base64 = image_to_base64_data_uri(input_image)
        messages.append({
            "role": "user",
            "content": [
                {"type": "text", "text": prompt},
                {"type": "image_url", "image_url": {"url": img_base64}}
            ]
        })
    else:
        # Text-to-image mode
        messages.append({
            "role": "user",
            "content": prompt
        })

    payload = {
        "messages": messages,
        # Use the model verified in the first code section
        "model": "gemini-2.5-flash-image",
        # Optional parameters, depending on API support
        "stream": False
    }

    try:
        # Increase timeout, image generation is usually slower
        response = requests.post(NANOBANANA_API_URL, headers=headers, json=payload, timeout=120)

        # Check HTTP status
        if response.status_code != 200:
            error_msg = f"API request failed: {response.status_code} - {response.text}"
            print(error_msg)
            gr.Error(error_msg)
            return None

        result = response.json()
        # Debug: Print first part of response for debugging
        print(f"API raw response (truncated): {str(result)[:200]}...")

        # Extract Content
        content = None
        if "choices" in result and len(result["choices"]) > 0:
            content = result["choices"][0].get("message", {}).get("content")

        if not content:
            gr.Warning("No content field in API response")
            return None

        # Use the previously verified logic to extract Base64
        base64_str = extract_base64_from_response(content)

        if base64_str:
            output_image = base64_to_image(base64_str)
            if output_image:
                return output_image

        # If no image was extracted, the model may have refused or only returned text
        text_content = str(content) if not isinstance(content, list) else " ".join([str(x) for x in content])
        gr.Info(f"No image generated, model returned text: {text_content[:100]}...")
        return None

    except requests.exceptions.Timeout:
        gr.Error("Request timed out, please try again later")
        return None
    except Exception as e:
        import traceback
        traceback.print_exc()
        gr.Error(f"An unknown error occurred: {str(e)}")
        return None

# Gradio interface configuration
with gr.Blocks(title="Nanobanana Image Generator") as app:
    gr.Markdown("# 🍌 Nanobanana Text/Image to Image")
    gr.Markdown("Based on Gemini-2.5-Flash-Image model, supports text-to-image and image-to-image.")

    with gr.Row():
        with gr.Column():
            prompt_input = gr.Textbox(
                label="Prompt",
                placeholder="e.g.: A cyberpunk cat holding a neon sign...",
                lines=3
            )
            image_input = gr.Image(
                label="Reference Image (optional, for image-to-image)",
                type="pil",
                height=300
            )
            submit_btn = gr.Button("Start Generation", variant="primary")

        with gr.Column():
            image_output = gr.Image(label="Generation Result", format="png")

    submit_btn.click(
        fn=synthesize,
        inputs=[prompt_input, image_input],
        outputs=image_output
    )

if __name__ == "__main__":
    app.launch(share=True)
```

Когда Trae сообщит об успешном запуске, нажмите на предоставленную им локальную ссылку (обычно http://127.0.0.1:7860).

![](/ru-ru/stage-2/frontend/lovart-assets/images/image9.png)

Если всё работает правильно, вы увидите рабочий интерфейс для рисования с помощью ИИ.

Этот интерфейс может выглядеть просто, но он уже обладает двумя самыми основными возможностями инструментов рисования коммерческого уровня: text-to-image (по тексту в изображение) и image-to-image (по изображению в изображение).

* **Левая сторона:** **область команд (зона ввода)** — здесь вы даёте инструкции.
* **Prompt (текстовое поле):** введите своё творческое описание (рекомендуется на английском).
* **Input Image (поле референсного изображения):**
  * **Режим text-to-image:** оставьте его **пустым**.
  * **Режим image-to-image:** перетащите сюда локальное изображение, и ИИ будет использовать его как основу для создания.
* **Кнопка Submit:** нажмите, чтобы отправить инструкцию и начать генерацию.
* **Правая сторона: область отображения (зона вывода)** — здесь происходит волшебство, тут будут появляться сгенерированные результаты.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image10.png)

Теперь можно попробовать сгенерировать ваше первое изображение!

В этом примере используется промпт:

> **A red apple**

Это намеренно упрощённый пример, который не включает никаких описаний стиля или параметров.

#### Реальный процесс

После запуска кода процесс можно свести к трём шагам:

1. Отправить текстовое описание модели
2. Модель генерирует соответствующее изображение
3. Изображение сохраняется как локальный файл

Через несколько секунд вы увидите сгенерированный результат локально. Поскольку генерация модели случайна, один и тот же промпт будет давать разные результаты. Вы можете генерировать несколько раз и выбрать понравившееся изображение.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image11.png)![](/ru-ru/stage-2/frontend/lovart-assets/images/image12.png)

Вы также можете обогатить промпт, добавив больше описаний и ограничений. Например, следующий промпт даст более выразительное изображение.

```Plain
"A hyper-realistic close-up of a fresh red apple with water droplets on its skin, sitting on a dark rustic wooden table. Cinematic dramatic lighting, rim light, shallow depth of field, bokeh background, 8k resolution, macro photography."
```

![](/ru-ru/stage-2/frontend/lovart-assets/images/image13.png)

Нажмите кнопку загрузки в области Output Image, чтобы сохранить изображение локально.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image14.png)

### 1.3 Типичные сценарии генерации ассетов для моделей изображений

В реальной работе генерация изображений большими моделями чаще используется для **эффективного производства дизайн-ассетов**, а не для создания отдельных художественных произведений.

Если посмотреть на популярные кейсы из маркетинговых аккаунтов, ориентированных на дизайн, вы обнаружите, что большая часть их продукции делится на две категории:

* **Text-to-image (от 0 к 1)**
* **Генерация изображений на основе референса (от 1 к N)**

#### Первое. Text-to-image — быстрое получение дизайн-ассетов

Эта категория сосредоточена на эффективности. Когда нужно заполнить дизайнерские пробелы (например, пустые состояния, аватары, иллюстрации), ИИ по сути выступает в роли **мгновенно генерируемой библиотеки изображений.**

1. ##### Генерация ассетов UI-дизайна

* Популярный тренд: глассморфизм и 3D-иконки в стиле «пластилина», которые часто встречаются на Dribbble
* Типичное представление: прозрачные материалы, свечение по краям, функциональные или погодные иконки в карамельных цветах

**Пример промпта:**

> A set of 3D weather icons (sun, cloud, rain), glassmorphism style, frosted glass texture, soft pastel gradient colors, soft studio lighting, isometric view, transparent background, 4k.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image15.png)

2. ##### Генерация логотипов

* Популярный тренд: минималистичные линии, геометрические сочетания для логотипов в техно-стиле
* Типичное представление: чёрно-белая цветовая схема, дизайн с использованием негативного пространства, чёткая айдентика бренда

**Пример промпта:**

> Minimalist vector logo design for a tech brand "Coffee Code", combining a coffee cup with coding brackets < >, flat design, solid black lines, white background, Paul Rand style, svg.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image16.png)

3. ##### Генерация пользовательских аватаров для сайта

* Популярный тренд: 3D-аватары, часто используемые на SaaS-сайтах, чтобы избежать проблем с авторскими правами на изображения реальных людей
* Типичное представление: дружелюбные выражения лиц, мультяшные пропорции, тяготение к стилю Pixar или Memoji

**Пример промпта:**

> Close-up portrait of a friendly young tech professional, smiling, Memoji 3D style, clay render, bright colors, soft lighting, solid plain background, Pixar character design.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image17.png)

4. ##### Генерация иллюстраций для статей

* Популярный тренд: абстрактные плоские иллюстрации, которые часто встречаются в блогах технологических компаний
* Типичное представление: фиолетово-синяя цветовая схема, преувеличенные пропорции персонажей, парящие элементы UI

**Пример промпта:**

> Editorial flat illustration representing remote work, a person sitting on a giant globe using a laptop, corporate memphis art style, vibrant colors (purple and teal), vector texture.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image18.png)

#### Второе. Генерация изображений на основе референса — поддержание визуальной согласованности

Эта категория больше сосредоточена на **масштабируемости**. Она используется, когда у вас уже есть удовлетворяющий вас основной визуал и нужно сгенерировать целый набор ассетов в едином стиле.

5. ##### Набор кнопок или интерактивных ассетов, похожих на основной визуал

В разработке игр согласованность UI крайне важна. Предположим, у вас уже есть кнопка «PLAY» для главного интерфейса, и теперь нужно расширить её до полного набора функциональных кнопок в едином стиле (например, пауза, настройки, домой). Опираясь только на ручную отрисовку, трудно обеспечить, чтобы каждая кнопка была идеально согласована по блеску, перспективе и цветовым значениям.

**Базовый порядок действий:**

1. Сохраните существующее изображение синей кнопки «PLAY»

![](/ru-ru/stage-2/frontend/lovart-assets/images/image19.png)

2. Перетащите его в область **Input Image** как референс-шаблон для последующей генерации
3. Оставьте описание стиля в промпте неизменным, меняйте только содержание основного объекта

При таком порядке действий, заменяя лишь описание объекта, вы можете получать кнопки с разными функциями, но в согласованном стиле.

**Пример промпта:**

**Вариант A: кнопка паузы (тип «иконка»)**

> A capsule-shaped game UI button with a white pause icon (two vertical bars) inside. Same glossy blue jelly style, shiny plastic texture, white thick outline, vector illustration, high quality.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image20.png)

**Вариант B: кнопка настроек (сложная иконка)**

> A capsule-shaped game UI button with a white gear icon (settings symbol) inside. Same glossy blue jelly style, shiny plastic texture, white thick outline, vector illustration, high quality.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image21.png)

**Вариант C: кнопка повтора (изменение формы)**

Если нужно изменить форму кнопки, вы можете напрямую описать форму в промпте. Модель попытается изменить структуру, сохранив характеристики материала.

> A round game UI button with a white circular arrow icon (replay symbol) inside. Same glossy blue jelly style, shiny plastic texture, white thick outline, vector illustration, high quality.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image22.png)

Благодаря этому набору операций вы можете не только заменять функции и иконки кнопок, но даже менять их форму, при этом все сгенерированные результаты остаются высоко согласованными по материалу, цветовой схеме и освещению. Именно в этом и заключается основная ценность больших моделей в сценариях генерации дизайн-ассетов.

## Глава 2. Более послушный помощник для генерации изображений — на примере Lovart

В первой части мы напрямую вызывали NanoBanana через код и познакомились с базовым процессом «ввод и генерация». Такой подход прекрасно работает, когда требования просты. Но когда задачи генерации начинают включать больше ограничений, например:

* нужно несколько изображений в согласованном стиле
* нужно многократно корректировать результат на основе уже имеющегося
* нужно динамически менять направление генерации в зависимости от ввода пользователя

подход с единичным вызовом постепенно становится недостаточным.

Именно здесь нужно ввести **AI Agent (интеллектуального агента)**. В этом разделе на примере **Lovart** показано, как меняется весь рабочий процесс, когда у модели генерации изображений появляется «слой мышления». Внимание! Это не реклама, а лишь способ помочь всем быстро ощутить удобство AI-агентов~

### 2.0 Знакомство с Lovart: ваш AI-агент для дизайна

Lovart — это веб-инструмент для дизайна на основе агента. По сравнению с обычными инструментами генерации изображений он добавляет перед генерацией слой «мышления и планирования».

![](/ru-ru/stage-2/frontend/lovart-assets/images/image23.png)

![](/ru-ru/stage-2/frontend/lovart-assets/images/image24.png)

После входа в Lovart вам нужно разобраться в основном со следующими элементами управления:

#### Выбор модели

Нажмите на иконку куба под полем ввода, чтобы увидеть доступные на данный момент модели генерации (такие как GPT Image, Flux и т. д.).

Чтобы сохранить согласованность с предыдущими примерами, в этом разделе в качестве базовой модели генерации по-прежнему используется NanoBanana.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image25.png)

#### Режим мышления

Это основной переключатель Lovart:

* **Быстрый режим (⚡):** близок к нативному API, быстрый отклик, подходит для единичной генерации с чётко заданной инструкцией
* **Режим мышления (💡):** режим агента — ИИ сначала разбивает требования на части, переписывает промпты, а затем выполняет генерацию

![](/ru-ru/stage-2/frontend/lovart-assets/images/image26.png)

![](/ru-ru/stage-2/frontend/lovart-assets/images/image27.png)

#### Доступ в интернет

После включения иконки глобуса агент может в процессе генерации получать информацию из интернета (например, дизайн-тренды, цветовые схемы) в качестве дополнительного входа.

### 2.1 Почему нативного API недостаточно?

Даже если вы уже можете генерировать изображения вполне приличного качества через Python, нативный API всё ещё имеет ограничения в сложных задачах. Ключевая причина в том, что нативный API по своей природе императивен. Когда вы просите его сгенерировать конкретный объект, он может выполнить это напрямую; но когда вход превращается в «спланируй полный набор игровых ассетов», он не станет самостоятельно разбивать цель на несколько выполнимых шагов.

Ключевое отличие Lovart заключается в его механизме агента. Между вводом пользователя и моделью генерации изображений он добавляет слой логики для понимания и планирования: сначала определяет намерение пользователя, затем декомпозирует задачи, переписывает промпты и только потом выполняет генерацию.

### 2.2 Практическая демонстрация: создаём набор IP-стикеров за 5 минут

Возьмём в качестве примера **«создание набора IP-стикеров с уткой-программистом»**, чтобы увидеть, как агент участвует во всём процессе.

#### Этап первый: планирование (способность агента к мышлению)

**Проблема нативного API:**
Вам нужно самостоятельно продумывать дизайн персонажа и его эмоциональные состояния, а также писать отдельные промпты для каждого изображения.

**Подход Lovart:**

1. Включите 💡 **режим мышления**
2. Введите одну-единственную инструкцию:

> Design a set of programmer duck IP sticker pack, flat style, cute

ИИ не начнёт сразу рисовать. Вместо этого он сначала ищет в интернете похожие дизайны утки-программиста. Затем он выдаёт декомпозированный план, автоматически генерируя сцены вроде Debug, Coffee Break, Panic и т. д., с соответствующими визуальными описаниями для каждой.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image28.png)![](/ru-ru/stage-2/frontend/lovart-assets/images/image29.png)

На этом шаге ИИ превращается из «исполнителя» в «планировщика». После того как ИИ закончит анализировать ваши требования, вы увидите в области холста Lovart разнообразные по стилю и содержанию изображения утки-программиста. Можно начинать отбирать понравившиеся стили.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image30.png)

#### Этап второй: согласованность (визуальная привязка на основе референса)

Изображения в Lovart — это не просто результаты; они также участвуют в последующей генерации.

##### Полное референсное изображение

* Выберите из набросков наиболее удовлетворяющую вас «эталонную утку», нажмите на соответствующее изображение в области холста
* Изображение автоматически появится в области диалога как Reference

![](/ru-ru/stage-2/frontend/lovart-assets/images/image31.png)

* Введите новое действие (например, happy) и запустите генерацию

Сгенерированный результат унаследует цветовую схему, пропорции и детали эталонного шаблона.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image32.png)

##### Частичный референс / объединение нескольких изображений

Помимо использования целого изображения в качестве референса, Lovart также поддерживает:

* **Выбор только частичной области изображения** (например, ссылку только на шляпу или выражение лица)

Нажмите на панель вкладок слева от области холста, выберите кнопку «Mark» и отметьте целевую область на изображении. Это содержимое автоматически синхронизируется с полем диалога. Например, здесь мы можем выбрать изменение цвета фона.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image33.png)

![](/ru-ru/stage-2/frontend/lovart-assets/images/image34.png)

![](/ru-ru/stage-2/frontend/lovart-assets/images/image35.png)

Вы можете увидеть, что в заново сгенерированном изображении изменился только цвет фона, что соответствует нашему введённому требованию.

* **Использование вложенных элементов из нескольких изображений по отдельности** с последующим их объединением для генерации новых результатов

Например: вы можете сохранить персонажа из изображения A в качестве основного объекта, заменив только шляпу на стиль из изображения B. Агент автоматически объединит эти визуальные ограничения в фоновом режиме.

На примере утки-программиста мы можем выбрать сохранение персонажа-утки из первого изображения и заменить его как основной объект во втором изображении.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image36.png)

![](/ru-ru/stage-2/frontend/lovart-assets/images/image37.png)

Итоговый результат получается весьма впечатляющим. Вы также можете попробовать другие сочетания!

#### Этап третий: доставка (вызов инструментов агентом)

После завершения генерации вы можете сразу выполнить: увеличение разрешения, удаление фона, стирание и другие операции.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image38.png)

![](/ru-ru/stage-2/frontend/lovart-assets/images/image39.png)

Это не простые фильтры — это результаты автоматической оркестрации различных инструментов агентом.

Как только базовый стиль определён, вы можете очень быстро сгенерировать целую серию изображений-стикеров.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image40.png)

В итоге мы получаем готовые к продакшену ассеты, которые можно сразу сдавать, а не просто демонстрационное изображение.

### 2.3 Информация об использовании и стоимости

Lovart использует модель оплаты по подписке, где разные тарифные планы соответствуют разным квотам использования и наборам разрешённых функций. Подробности смотрите на официальном сайте.

Это руководство не рекомендует и не сравнивает какие-либо конкретные тарифы; если у вас есть реальная потребность в использовании, вы можете выбрать апгрейд, исходя из личной ситуации.
В настоящее время поддерживается оплата через **Alipay** и другие методы.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image41.png)

#### Резюме

Lovart не заменяет базовую модель — напротив, благодаря своему механизму агента он поднимает генерацию изображений с уровня «единичного выполнения» до «непрерывного рабочего процесса».

Когда задачи начинают затрагивать планирование, согласованность и доставку, преимущества подобных инструментов становятся очень заметными.

## Глава 3. Создайте собственного умного помощника для рисования

Помимо прямого использования Lovart, мы также можем сами реализовать упрощённую версию помощника для рисования.

В этой главе в качестве примера используется «автоматическое иллюстрирование статей»: начиная с реальной проблемы, мы постепенно построим агента, обладающего способностью к мышлению.

### 3.1 Проблема: почему отправка статей напрямую в модели изображений не работает?

Если напрямую ввести длинную статью в NanoBanana и попросить иллюстрацию, обычно это не даёт идеального результата. Причина не в том, что модель «плохо рисует», а в том, что **она плохо понимает длинные тексты.**

Модели генерации изображений лучше подходят для обработки коротких и чётких визуальных описаний. Когда вход превращается в статью, содержащую структуру, ключевые тезисы и контекстные связи, модель не может определить, какое именно содержание действительно нужно выразить в изображении. Это часто приводит к результатам, которые отклоняются от основной темы или улавливают лишь разрозненные детали, не обладая способностью к общему обобщению.

По сути, у моделей изображений есть только способность к «выполнению», но отсутствует процесс анализа текста и взвешивания приоритетов.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image42.png)

### 3.2 Решение: используйте агента, чтобы отделить «понимание» от «выполнения»

Ключ к решению этой проблемы — не более сложные промпты, а **обдумывание задачи перед рисованием.** Поэтому мы вводим в процесс генерации отдельный «слой мышления» и используем его для построения простейшего работоспособного агента.

У этого агента только одна основная цель: **сделать итоговое сгенерированное изображение как можно ближе к истинному выразительному намерению пользователя.**

Весь процесс можно свести к следующему: **Ввод длинного текста → Понимание и оценка языковой моделью → Генерация подходящих визуальных промптов → Выполнение генерации моделью изображений → Вывод изображения**

![](/ru-ru/stage-2/frontend/lovart-assets/images/image43.png)

Так как же создаваемый нами агент может понять намерение пользователя?

Здесь мы решаем создать упрощённый **«слой мышления»** с тремя различными намерениями: некорректный ввод, прямая генерация изображения и длинный текст, требующий понимания.

В этом агенте распределение ролей можно свести к четырём пунктам:

1. **Языковая модель как ядро принятия решений**
   Она отвечает за понимание содержания статьи, оценку намерения пользователя при вводе и распределение задач по подходящим путям генерации, решая, «что делать дальше» и как генерировать промпты для изображений.
2. **Модель изображений как исполнитель**
   Модель изображений не участвует в понимании или оценке — она лишь получает хорошо организованные визуальные инструкции и сосредоточена на отрисовке изображения.
3. **Пользователь как направляющий участник**
   Помимо прямого ввода текста, пользователи также могут вручную корректировать сгенерированные промпты по ходу процесса или добавлять референсные изображения для помощи в генерации, тем самым направляя и тонко настраивая итоговый результат.
4. **Gradio и бэкенд-API как общая инфраструктура**
   Они отвечают за связывание интерфейса, вызовов модели и отображения результата, обеспечивая стабильную работу всего агента как полноценного веб-приложения.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image44.png)

### 3.3 Подготовка: получаем API

Звучит интересно, правда! Чтобы пройти весь описанный процесс, нам нужно подготовить всего два типа API.

#### Рука: NanoBanana API (генерация изображений)

Просто повторно используйте API Key и API URL, уже настроенные в Главе 1, — никакой дополнительной настройки не требуется.

#### Мозг: SiliconFlow API (текстовое мышление)

Нам нужна большая языковая модель, которая будет служить «слоем мышления». В этом руководстве используется модельный сервис, предоставляемый SiliconFlow: [https://cloud.siliconflow.cn](https://cloud.siliconflow.cn/)

![](/ru-ru/stage-2/frontend/lovart-assets/images/image45.png)

SiliconFlow предоставляет интерфейсы, совместимые со спецификацией OpenAI API, которые можно легко вызывать через стандартные сетевые запросы в вашем проекте. Здесь мы выбираем бесплатную модель Qwen2.5-7B-Instruct. Всё необходимое для вызова уже прописано в Prompt ниже. Перед началом вам нужно лишь зарегистрировать аккаунт на официальном сайте и создать API Key.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image46.png)

![](/ru-ru/stage-2/frontend/lovart-assets/images/image47.png)

Этот Key будет использоваться для последующих вызовов модели.

### 3.4 Создаём агента:

В этом эксперименте мы в основном используем Trae для написания кода. В данном руководстве используется модель Gemini-3-Pro-Preview. Общий подход таков: создать новый проект, скопировать полный Prompt ниже в поле диалога и отправить его, постепенно заменить API KEY, затем запустить код и завершить тестирование.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image48.png)

#### Фаза 1: базовый каркас Gradio Blocks и компоновка интерфейса

На этой фазе наша основная цель — сначала построить «внешний вид» для всего агента, реализовав дизайн фронтенд-страницы. Скопируйте следующий Prompt в поле диалога Trae, чтобы реализовать это, и вы получите локальную ссылку (обычно http://127.0.0.1:7860), где можно посмотреть интерфейс и проверить реализацию.

```Plain
Module 1: Gradio Blocks Basic Framework and Interface Layout
1. Task Objective
Based on Gradio 4.0.0+ Blocks layout, implement the basic interface for the "LLM + Nanobanana text-to-image" project, strictly following the fixed left-right split layout, initializing all UI components and setting correct initial states.

2. Tech Stack Requirements
Must use Gradio 4.0.0+ Blocks mode development, Interface mode is prohibited;
Dependencies: gradio>=4.0.0, pillow>=10.0.0 (import only, image processing logic not implemented yet);
Code must be a complete runnable Python file with all necessary import statements.

3. Interface Layout Rules (Core Constraints, Integrating Practical Details)
Overall Layout:
Page title: LLM-Driven Text-to-Image Full-Process Tool;
Fixed left-right split: left side takes 60% width, right side takes 40% width, using gr.Row and gr.Column to implement ratio control.
Left 60% (Prompt Generation Process Area) Component List:
input_text: gr.Textbox, label "Input Text (Tutorial Paragraph / Drawing Instruction)", lines=6, placeholder "Please enter the tutorial text that needs illustration or a direct drawing instruction...";
identify_intent_btn: gr.Button, value="Identify Intent", initial state normally clickable;
intent_status: gr.Textbox, label "Intent Type / Processing Status", lines=2, interactive=False, initial value "Intent not identified";
system_prompt: gr.Textbox, label "System Prompt (Editable only for article illustration intent)", lines=4, interactive=False, placeholder "LLM constraint rules for prompt generation...";
confirm_prompt_btn: gr.Button, value="Confirm Generate Image Prompt", interactive=False (initially disabled to prevent accidental clicks);
generation_prompt: gr.Textbox, label "Image Generation Prompt (Editable)", lines=3, interactive=True, initial value empty, placeholder "Generated English image prompt will be displayed here, supports manual editing...".
Right 40% (Nanobanana Image Generation Function Area) Component List:
ref_image: gr.Image, label "Reference Image (Optional, for Image-to-Image)", type=filepath, height=300, allows upload;
generate_btn: gr.Button, value="Generate Image", interactive=False (initially disabled, cannot click without a prompt);
result_image: gr.Image, label="Generation Result", type=pil, height=300, initial empty, interactive=False.

4. Interaction Logic Requirements
All component interactive initial states strictly follow the above configuration, dynamically updated by functions later;
Button disabled states should be visually apparent (grayed out) to prevent user misoperation.

5. Output Requirements
Generate complete Python code that only implements interface layout and component initialization, without any business logic;
Clear code comments, component naming consistent with the practical version (input_text/identify_intent_btn, etc.);
Code is directly runnable, interface structure matches the description exactly.
```

После того как вы откроете http://127.0.0.1:7860 в браузере, вы увидите, что Trae сгенерировал следующую веб-страницу в соответствии с нашими требованиями; она в целом им соответствует, и можно переходить к следующему шагу генерации.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image49.png)

#### Фаза 2: модуль распознавания намерений LLM (Siliconflow API)

При повседневном использовании VLM для рисования обычно встречаются три типичных сценария ввода:

1. Бессмысленное содержание, например «привет», «ты сегодня поел» и т. д., по которому невозможно создать соответствующее изображение.
2. Статьи/длинный текст с большим количеством слов, например структурированная статья примерно на 200 слов, которая требует сначала понять структуру и содержание статьи, а затем подумать, как сгенерировать изображение, полностью обобщающее текст.
3. Прямые инструкции по рисованию, например «помоги мне нарисовать собаку, принимающую ванну», где требование уже очень конкретно и изображение можно сгенерировать напрямую.

Как и раньше, скопируйте следующий Prompt в поле диалога Trae, чтобы реализовать это, и подставьте API, полученный на предыдущих шагах.

```Plain
Module 2: LLM Intent Recognition Module (Siliconflow API)
1. Task Objective
Based on the implemented Gradio interface, add click logic for the "Identify Intent" button, call the Siliconflow API to complete intent recognition, and link component states.

2. Tech Stack Requirements
Based on Gradio 4.0.0+ Blocks;
Dependencies: requests>=2.31.0, openai;
Output complete runnable Python file, including Module 1 interface + this module's logic.

3. Core Business Rules (Absolutely Must Not Deviate)
Intent Classification Rules (Only 3 categories, strictly return number + description)
1 = Meaningless content: only small talk, greetings, irrelevant conversation, no drawing or illustration needs (e.g., "hello" "did you eat");
2 = Article / Long text illustration need: user inputs a complete article, tutorial, paragraph, explanatory text, content is narrative / explanatory / instructional, implicitly implying the need to generate an illustration for this content, user doesn't need to explicitly say "illustrate this text";
3 = Direct drawing instruction: user inputs a short, clear drawing command, no long text background, directly requesting to draw something specific (e.g., "draw an Apple-style cat").
LLM Call Constraints (Integrating Practical Template)
Interface address: https://api.siliconflow.cn/v1/chat/completions;
Model: Qwen/Qwen2.5-7B-Instruct;
temperature=0.1;
Unified code definition:
python
Run
LLM_BASE_URL = "https://api.siliconflow.cn/v1"
LLM_API_KEY = ""  # User replaces themselves
LLM_MODEL = "Qwen/Qwen2.5-7B-Instruct"# Practical verified intent recognition template (hardcoded in code)
INTENT_PROMPT_TEMPLATE = """You need to identify the intent of the user's input text, only return one of the following 3 categories (format: number + description):
1 = Meaningless content; 2 = Article / Long text illustration need; 3 = Direct drawing instruction.

User input: {user_input}

Recognition result:
Only extract the number and description from the result, no additional content allowed."""

4. Component Linking Rules
Result is 1: intent_status displays "1 = Meaningless content: no drawing need", system_prompt remains disabled, confirm_prompt_btn disabled;
Result is 2: intent_status displays "2 = Article / Long text illustration need: generate illustration for input content", enable system_prompt and fill default rules, activate confirm_prompt_btn;
Result is 3: intent_status displays "3 = Direct drawing instruction: generate image based on instruction", system_prompt disabled and filled with default rules, activate confirm_prompt_btn.

5. Exception Handling
API exceptions, parsing exceptions all give friendly prompts, no crashes, components restore to initial state.

6. Output Requirements
Generate complete runnable code, just replace LLM_API_KEY to use, logic clear with complete comments, intent recognition template strictly uses the practical version.
```

Обновите ту же ссылку http://127.0.0.1:7860 и начните проверять, может ли система правильно определять три сценария.

1. Бессмысленное содержание: можно попробовать ввести «привет», «спасибо» и т. д. и убедиться, что оно корректно распознаётся.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image50.png)

2. Статья/длинный текст: здесь мы использовали отрывок, сгенерированный Doubao и описывающий искусственный интеллект. Вы также можете попробовать использовать для тестирования абзацы из собственных эссе.

```Plain
Artificial intelligence is reshaping the education ecosystem with unprecedented depth and breadth. Through adaptive learning algorithms, AI systems can build cognitive maps for each student, track their knowledge mastery trajectory in real-time, and dynamically adjust the difficulty and presentation of teaching content. In traditional classroom environments, teachers often struggle to simultaneously meet the needs of students with different learning styles and ability levels, while deep learning-based education platforms can analyze students' behavioral patterns in interactive simulation experiments, identify their subtle obstacles in understanding complex concepts like quantum mechanics or calculus, and provide precise cognitive scaffolding.

Advanced natural language processing engine-driven virtual tutors can not only deconstruct open-ended questions like "How to evaluate the impact of the French Revolution on modern democratic systems," but also guide Socratic dialogue to stimulate critical thinking. When students write essays about the impact of climate change on polar ecosystems, AI writing assistants can analyze the rigor of their argumentation logic, point out timeliness issues in data citations, and suggest more precise scientific terminology. In special education, computer vision technology enables AI to recognize non-verbal cues from children on the autism spectrum during social interactions and adjust intervention strategies, while affective computing algorithms help detect frustration during online learning and provide timely encouraging feedback.

However, this technological integration raises a series of ethical dilemmas. Algorithmic bias may inadvertently marginalize students from certain cultural backgrounds, transparency issues in data collection raise concerns about academic privacy, and over-reliance on automated grading systems may weaken teachers' deep understanding of students' thinking processes. More complexly, when AI begins generating highly realistic virtual laboratory experiences, we need to redefine the value of "practical experience" in education. The future education paradigm may evolve into human teachers focusing on cultivating creativity, empathy, and moral judgment, while AI systems assume the functions of knowledge transmission, skill training, and personalized assessment, forming a co-evolving educational symbiosis that both leverages machine computational advantages and preserves the unique warmth of human education.
```

Тоже успешно распознано~

![](/ru-ru/stage-2/frontend/lovart-assets/images/image51.png)

3. Прямая инструкция по рисованию: здесь мы ввели «Я хочу нарисовать кота», что также было точно распознано.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image52.png)

На этом этапе мы успешно реализовали вторую фазу — распознавание намерений.

#### Фаза 3: модуль генерации промптов для изображений (второй вызов LLM)

После распознавания намерения для статей или длинного текста остаётся ещё один очень важный шаг — генерация промптов для рисования, и это ключевой акцент данного агента.

```SQL
Module 3: Image Prompt Generation Module (LLM Second Call)
1. Task Objective
Based on intent recognition, implement the "Confirm Generate Image Prompt" button logic, call LLM to optimize text into English visual prompts suitable for drawing, fill into the edit box and link the "Generate Image" button.

2. Tech Stack Requirements
Same as Module 2, output complete code = Module 1 + Module 2 + this module;
Share the LLM_BASE_URL, LLM_API_KEY, LLM_MODEL defined in Module 2, no new keys needed.

3. Core Business Rules (Integrating Practical Prompt Assembly Logic)
Prompt Generation Input Rules (Must Strictly Follow)
Image prompt generation is no longer simple string concatenation, but building a standard Chat message list, code structure as follows:
python
Run
messages=[# System role: the final confirmed/edited system_prompt content from the web page{"role": "system", "content": final_system_prompt},# User role: carries data to be processed, clarifies task objective{"role": "user", "content": f"Please generate visual prompts for the following content:\n\n{user_input}"}]
When intent is 2: System content takes the user-edited system_prompt final version;
When intent is 3: System content takes the default rules filled in disabled state
user_input is the original text the user initially entered in the input_text box.
Practical Verified System Prompt Preset (Hardcoded in Code)
python
Run
SYSTEM_PROMPT_DEFAULT = """You are now an assistant for creating NanoBanana drawing prompts.
You need to process based on my content. The purpose of this image is to illustrate what this passage is saying, and let everyone understand the overall structure of this text.
It may include some PPT-like annotations (e.g., top left shows core viewpoint, bottom right shows data).
Design style requirements: minimalist, Apple Design Philosophy.
Constraint: Please directly return English prompts usable by NanoBanana, do not return any explanations, prefixes, or unnecessary words."""
LLM Call Constraints
Share the same LLM_BASE_URL, LLM_API_KEY, LLM_MODEL with Module 2;
temperature=0.7 (ensure prompt creativity and adaptability);
max_tokens=200 (limit output length, matching prompt constraints);
Strictly use the above standard Chat message list structure, string concatenation is prohibited.
Example Input/Output (Core Reference)
Example Input 1 (Article Illustration Intent): Original text: "How AI is changing education: With the development of AI technology, the role of teachers has shifted from knowledge transmitters to guides, AI assistants can help students complete personalized learning, and human-machine collaboration in classrooms has become the norm." Final System Prompt: SYSTEM_PROMPT_DEFAULT (unmodified) Expected output: "Minimalist illustration, Apple Design Philosophy, 1024x1024. Top left shows 'AI + Education' core concept, bottom right shows data of teacher-student-AI collaboration, soft color palette, clean lines, no redundant elements."
Example Input 2 (Direct Drawing Instruction): Original text: "Draw an Apple-style cat sitting next to a MacBook" Final System Prompt: SYSTEM_PROMPT_DEFAULT (disabled state) Expected output: "Minimalist cat, Apple style, 1024x1024, sitting next to a silver MacBook, clean white background, soft shadows, geometric shapes, no extra details."
Prompt Output Mandatory Constraints
Pure English, no Chinese;
Must include Apple Design Philosophy/Apple style + 1024x1024;
Length 50-200 characters, verified in code;
No additional explanations, prefixes, or unnecessary words, only return the prompt itself.

4. Component Linking Rules
Generation successful: fill prompt into generation_prompt box, activate generate_btn, append "Prompt generated successfully, can edit then generate image" to intent_status;
Generation failed: show specific reason (such as API call failure, length not met), generate_btn remains disabled, generation_prompt box empty;
User manually edits / clears generation_prompt box:
When cleared, automatically disable generate_btn;
When non-empty, keep generate_btn activated.

5. Exception Handling
API call failure: friendly prompt "Prompt generation failed: {specific error message}", no crash;
Prompt validation failure: clearly state reason (such as "Apple style not included" "length only 40 characters"), allow retry;
Response parsing failure: prompt "Unable to parse LLM return result, please retry".

6. Output Requirements
Complete runnable code, just replace LLM_API_KEY to use;
Clear code structure, complete comments, beautiful and concise interface;
Strictly implement standard Chat message list structure, parameters and example logic consistent;
Include prompt length and content validation logic, friendly error messages.
```

Аналогичным образом протестируйте на тексте из второй фазы.

Стоит отметить, что предустановленный System Prompt для генерации промптов изображений здесь следующий:

> You are now an assistant for creating NanoBanana drawing prompts.
> You need to process based on my content. The purpose of this image is to illustrate what this passage is saying, and let everyone understand the overall structure of this text.
> It may include some PPT-like annotations (e.g., top left shows core viewpoint, bottom right shows data).
> Design style requirements: minimalist, Apple Design Philosophy.
> Constraint: Please directly return English prompts usable by NanoBanana, do not return any explanations, prefixes, or unnecessary words.

Если вы хотите переключиться на другой предустановленный шаблон, вы можете изменить его в более раннем промпте или напрямую отредактировать через диалог в Trae.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image53.png)

Помимо изменения базового кода, мы также можем быстро редактировать прямо на веб-странице. Например, я добавил здесь фразу «add 'Pic Prompt' at the beginning», и вы можете увидеть, что заново сгенерированный промпт тоже содержит это в начале. Такой дизайн нужен, чтобы было легко быстро изменять System Prompt для генерации промптов, помогая нам быстро переключать стили.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image54.png)

#### Фаза 4: модуль text-to-image / image-to-image для Nanobanana

Наконец, мы добрались до последнего шага — без подключения модели генерации изображений это не полноценный агент!

```Bash
Module 4: Nanobanana Text-to-Image / Image-to-Image Module (Final Version)
1. Task Objective
Implement the "Generate Image" button logic, call the real Nanobanana API, support text-to-image / image-to-image, parse Base64 and display images.

2. Tech Stack Requirements
Based on Gradio 4.0.0+ Blocks;
Dependencies: requests, pillow, base64, io, re;
Complete code = Module 1+2+3 + this module.

3. Core API Configuration (Practical Verified Hardcoded)
Hardcoded configuration:
python
Run
# Hardcoded API configuration
NANOBANANA_API_URL = "https://api.zyai.online/v1/chat/completions"
NANOBANANA_MODEL = "gemini-2.5-flash-image"
NANOBANANA_API_KEY = ""  # User replaces themselves
Authentication: Header Authorization: Bearer {NANOBANANA_API_KEY}.

4. Image Preprocessing Requirements (Must Implement)
Implement function image_to_base64_data_uri (ref_image_path), core logic:
Convert PIL image to PNG format;
Auto-scale to 1024x1024 resolution;
Convert transparent channel to white background;
Encode as Base64, return format: data:image/png;base64,....

5. Request Construction Rules (Strictly Follow Practical Branch Logic)
Core Function Definition
Implement function generate_image (prompt, ref_image_path):
Parameters: prompt (generation_prompt box content), ref_image_path (ref_image uploaded file path);
Returns: PIL Image (displayed in result_image) or error message.
Logic Branch 1: Pure Text-to-Image (ref_image_path is empty)
python
Run
messages = [{"role": "user", "content": prompt}]
Logic Branch 2: Image-to-Image (ref_image_path has value)
python
Run
# First call image preprocessing function
image_base64 = image_to_base64_data_uri(ref_image_path)
messages = [{"role": "user","content": [{"type": "text", "text": prompt},{"type": "image_url", "image_url": {"url": image_base64}}]}]

6. Response Parsing Requirements (Must Be Compatible with Two Formats)
Extract image Base64 from choices[0].message.content, supporting:
Structured JSON returned image_url field;
Markdown format ![img](data:image/...);
Unified extraction of Base64 encoding, decode then convert to PIL Image return.

7. Component Linking and Exception Handling
Generation successful: display PIL Image in result_image, intent_status prompts "Image generation successful";
Generation / parsing / upload failure: display clear text message in intent_status (such as "Base64 parsing failed" "API call timed out"), no crash.

8. Output Requirements
Complete runnable code, just replace LLM_API_KEY and NANOBANANA_API_KEY to run directly, full process available, branch logic strictly matches practical version.
```

![](/ru-ru/stage-2/frontend/lovart-assets/images/image55.png)

Как захватывающе! Мы наконец-то успешно сгенерировали первое изображение с помощью этого агента. Присмотритесь к сгенерированному изображению — оно соответствует нашему тексту и промптам. На этом этапе вы в основном реализовали собственного агента!

![](/ru-ru/stage-2/frontend/lovart-assets/images/image56.png)

Мы также добавили функцию image-to-image — загрузите понравившееся изображение, и ИИ автоматически возьмёт за основу его стиль.

![](/ru-ru/stage-2/frontend/lovart-assets/images/image57.png)

Стоит упомянуть, что промпты, сгенерированные на предыдущих шагах, также можно редактировать на веб-странице, и в качестве финальной версии мы используем промпт в момент, когда кнопка окончательно нажата. Даже если я поменяю его здесь на «a cute cat», итоговое сгенерированное изображение будет всего лишь милым котёнком.

## Глава 4. Заключение

![](/ru-ru/stage-2/frontend/lovart-assets/images/image58.png)

**Ура! Наконец-то дописали.**

Честно говоря, даже я не смог сдержать долгого вздоха облегчения, когда закончил последнюю строку, — что уж говорить о вас, кто прошёл весь этот путь до самого конца. Уже само по себе впечатляюще то, что вы смогли полностью пройти весь этот процесс. Это значит, что вы действительно положили руки на клавиатуру и шаг за шагом довели дело до конца. Браво!

Пока я писал этот материал, я постоянно думал о том, что мы на самом деле хотим вам оставить. Ответ — это вовсе не названия моделей, параметры или какая-то фиксированная формула, а помощь в постепенной выработке чутья: какие вещи можно спокойно доверить ИИ для понимания и планирования, а где нужно просто, чтобы вы сами определили направление. Как только это разделение труда выстроено, многие процессы генерации, которые изначально казались сложными, начнут идти естественно.

Если оглянуться назад, этот путь на самом деле не так уж сложен. Разберитесь, какую проблему вы хотите решить, передайте длинный текст языковой модели для декомпозиции, затем передайте организованное визуальное намерение модели рисования для отрисовки и, наконец, упакуйте весь этот процесс в собственного маленького помощника. На этом этапе вы уже не просто «используете модель» — вы строите систему, которая может долго работать рядом с вами. И именно это данное руководство больше всего хочет вам дать.

Но вы уже отлично справились! Я уверен, что, дойдя до этого момента, вы уже получили начальное представление о Vibe Coding. Дайте себе небольшую передышку и хорошенько отдохните!

<RelatedArticlesSection
  title="Похожие статьи"
  description="Если вы хотите по-настоящему встроить «генерацию ассетов» в рабочий процесс своего продукта, можете продолжить этими главами."
  :items="relatedArticles"
/>

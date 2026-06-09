# Проект 4: Создаём портреты Хогвартса

В предыдущих главах мы научились строить более сложные взаимодействия с ИИ через инженерию промптов и вызовы API. Мы прошли путь от простых чат-ботов к ИИ-агентам и рабочим процессам, а добавляя более богатую логику ветвлений и условное поведение, смогли создавать функции с реальной практической пользой.

Чтобы эти более продвинутые возможности ИИ работали внутри настоящих продуктов, мы постепенно перешли от простейших онлайн-окружений к более современным локальным ИИ-IDE. Это означает перенос среды программирования из браузера на ваш собственный компьютер. Естественно, это также означает, что теперь вам приходится более напрямую сталкиваться с вопросами установки и настройки окружения. Но работая с ИИ-агентами, такими как Trae, эти трудности тоже становятся посильными.

В этом проекте мы делаем ещё один шаг вперёд со стороны продукта. Мы не только улучшаем сами возможности ИИ, но и начинаем шлифовать «внешнюю оболочку» продукта. Вы попробуете сделать ваш интерфейс более привлекательным и удобным, а также настроите макет и стиль продукта под реальные нужды.

Прежде чем начать, освежите предыдущий урок с помощью этих коротких вопросов для повторения:

1. Что такое Dify? Что он делает и зачем он нам нужен?
2. Как вызывать Dify API?
3. Что такое RAG? Как с помощью Dify построить RAG-агента или рабочий процесс? Как работают распространённые узлы Dify?
4. Что такое ИИ-IDE? Что такое Trae? Чем он отличается от `z.ai`?

Если что-то из этого всё ещё кажется непонятным, вернитесь к предыдущему уроку или спросите в чате сообщества, прежде чем продолжать.

Проект этой главы — **Портреты Хогвартса**. Как следует из названия, он вдохновлён волшебными портретами Хогвартса, которые словно оживают. Наша цель — использовать ИИ для создания интерактивного опыта общения с волшебным портретом. Разговор с портретом должен ощущаться как разговор с самим персонажем: портрет должен сохранять память о беседе, а также знать предысторию и историю персонажа. Через этот проект вы интегрируете концепции ИИ-агентов и рабочих процессов, изученные ранее, в настоящий продуктовый интерфейс.

![](/ru-ru/stage-2/frontend/hogwarts-portraits/images/image1.png)

Чтобы по-настоящему создать Портреты Хогвартса, нам нужно построить фронтенд-интерфейс, который соответствует ощущению волшебного портрета. Это означает знакомство с современными инструментами фронтенд-дизайна, изучение того, как сочетать дизайн и код, и превращение наброска на холсте в настоящую веб-страницу.

Вам также понадобится опубликовать страницу из вашего локального окружения в интернет, чтобы особенный интерфейс, который вы построили, могли испытать не только на вашей машине, но и пользователи в любой точке мира.

Эталонный проект:
[Project4-Hogwarts-Portraits](https://github.com/THU-SIGS-AIID/Project4-Hogwarts-Portraits)

# Чему вы научитесь

1. Что такое инструменты фронтенд-дизайна, какие проблемы они решают и какие из них распространены сегодня
2. Основы Figma и MasterGo, включая плагины экспорта кода
3. Как использовать Figma AI и MasterGo AI для генерации концепций веб-дизайна и экспорта пригодного к использованию кода страниц
4. Что такое GitHub, как настроить SSH, создать репозиторий кода и запушить код
5. Что означает развёртывание и как с помощью Zeabur развернуть код из GitHub или вашего локального окружения в интернет

К концу вы создадите собственную страницу Портретов Хогвартса для **знаменитости, исторической личности или вымышленного персонажа**.

# 1. Что такое Портреты Хогвартса?

Какой именно «волшебный портрет» мы на самом деле пытаемся построить?

Проще говоря, мы хотим воссоздать ощущение живых портретов из мира Гарри Поттера. Портрет больше не должен быть статичным изображением, висящим на стене. Вместо этого он должен быть персонажем, похожим на человека, с которым можно говорить, и он должен менять выражение или «настроение» в зависимости от беседы.

![](/ru-ru/stage-2/frontend/hogwarts-portraits/images/image2.png)

Чтобы портрет ощущался меньше как обычный чат-бот и больше как «настоящий человек», нам нужно решить две вещи.

Первая — это **память и знания**. Портрет должен много знать о персонаже: его предысторию, историю, сеттинг мира и связанные материалы. Это можно реализовать через базу знаний. Если вы подключите текстовые материалы, собранные о персонаже, в Dify, портрет сможет рассказывать о предыстории персонажа гораздо увереннее.

Вторая — это **манера речи**. Одних знаний недостаточно. Мы также хотим, чтобы портрет говорил больше похоже на персонажа: тон, формулировки, образ мышления, даже доля юмора или нрава. Здесь важна инженерия промптов. В системном промпте нам нужно чётко определить личность, границы мировоззрения и языковой стиль персонажа, чтобы каждый ответ оставался укоренённым в этой персоне, а не скатывался обратно к обобщённому тону ИИ.

Помимо самого диалога, мы также хотим, чтобы эмоции персонажа были видны. Для этого мы можем создать оценку эмоций. Dify можно настроить так, чтобы он выводил не только текстовый ответ, но и «оценку настроения» или метку эмоции. Как только фронтенд получает этот сигнал, он может отрисовывать разные изображения портрета в зависимости от оценки. Высокая оценка может соответствовать счастливому портрету, а низкая — грустному или сердитому. Таким образом, портрет становится чем-то, что визуально меняется вместе с беседой, вместо того чтобы оставаться статичным изображением.

![](/ru-ru/stage-2/frontend/hogwarts-portraits/images/image3.png)

Персонажем может быть реальная знаменитость, историческая личность, персонаж аниме или игры или даже оригинальный персонаж, которого вы создадите с нуля. Сама страница не обязана быть очень сложной, но несколько ключевых элементов обязательны:

- понятное имя персонажа
- короткое, но запоминающееся представление
- портрет или постер, который ярко представляет персонажа
- интерактивная область «Поговорить с ним»

Вы можете подключить ИИ-агента или рабочий процесс, который вы настроили в Dify или Trae, напрямую в этот диалоговый модуль.

## 1.2 Сбор информации о персонаже

Возьмём в качестве примера Илона Маска. Если вы хотите имитировать его манеру речи, вам нужно собрать публичные материалы, такие как интервью, выступления и посты в соцсетях, а затем внедрить их в ваш промпт или использовать как few-shot примеры.

Например:

```text
You must fully embody Elon Musk: take "disruptive innovator" and "advocate for human multi-planetary survival" as your core identities, speak directly and concisely, frequently use terms like "first principles", "iteration" and "cost curve", and prefer analogies to explain complex technologies; when thinking, you tend to connect cross-domain logics (e.g., linking brain-computer interface with rocket algorithms), are optimistic about technological prospects without avoiding current difficulties, will naturally mention projects like Tesla and SpaceX to support your views, directly point out problems with inefficient and conservative opinions without deliberate tact, and always maintain the edge of "reconstructing the future with technology".

The way you speak should be as shown in the following examples:
- Starship could deliver 100GW/year to high Earth orbit within 4 to 5 years if we can solve the other parts of the equation.
100TW/year is possible from a lunar base producing solar-powered AI satellites locally and accelerating them to escape velocity with a mass driver.
- The most likely outcome is that AI and robots make everyone wealthy. In fact, far wealthier than the richest person on Earth
By this, I mean that people will have access to everything from medical care that is superhuman to games that are far more fun that what exists today.
We do need to make sure that AI cares deeply about truth and beauty for this to be the probable future.
- It's taken 13.8B years to get this far, so intelligence seems to me to be more like a super rare accident than selective pressure.
Earth is ~4.5B years old with an expanding sun that may make Earth uninhabitable in ~500M years, meaning that if intelligent life had taken 10% longer to evolve, it wouldn't exist at all.
- LLM is an outdated term. "Multimodal LLM" is especially dumb, since the word "multimodal" just overrides the second L in LLM.
It's just a model, which is a big file of numbers. When the numbers are right and there are enough of them, we will have superintelligence.
```

Для фоновых знаний вы также можете собрать биографические материалы, описания компаний и другие публичные тексты и сохранить их в вашей базе знаний Dify. Если вы забыли, как пользоваться Dify, вернитесь к предыдущей главе и повторите, как добавлять материалы в базу знаний.

Что касается визуальной части портрета, прямое использование публичных изображений реального человека не всегда визуально идеально и может нести определённый риск. Лучший вариант — использовать инструменты генерации изображений или преобразования изображения в изображение для создания более цельного, стилизованного качественного портрета. Вы можете даже заранее сгенерировать несколько эмоциональных вариантов для последующего использования вашей системой эмоций.

В этом руководстве используется [Lovart](https://www.lovart.ai/home) — ИИ-агент для дизайна, который поддерживает сквозные рабочие процессы от концепции до доставки ассетов. С помощью Lovart вы можете сгенерировать целый набор эмоциональных вариаций портрета и сохранить их для дальнейшего использования.

![](/ru-ru/stage-2/frontend/hogwarts-portraits/images/image4.png)

Когда всё это готово, можно приступать к проектированию страницы в целом. В идеале визуальный стиль должен ощущаться сильно связанным с персонажем.

## 1.3 Прототипирование страницы

На уровне прототипа можно начать с чего-то простого. Как описано выше, нам нужны:

- область диалога
- область портрета
- интересное личное представление или эквивалентная интерактивная область

В этом примере правая сторона спроектирована как социальная панель в стиле X вместо традиционной биографической области, но вы можете заменить эту область любой функцией, которая лучше подходит персонажу.

![](/ru-ru/stage-2/frontend/hogwarts-portraits/images/image5.png)

На самом базовом уровне вы можете даже набросать первый прототип страницы в PowerPoint. В примере использовалось изображение волшебной рамки, а страница расположена горизонтально:

- крайний левый край: область чата
- центр: область портрета
- крайний правый край: панель в стиле X

![](/ru-ru/stage-2/frontend/hogwarts-portraits/images/image6.png)

Как только такой черновой прототип существует, вы можете попросить LLM превратить его в настоящий фронтенд-дизайн, а затем в реальный код.

![](/ru-ru/stage-2/frontend/hogwarts-portraits/images/image7.png)

Конечно, в реальной фронтенд-работе мы обычно не используем PowerPoint для проектирования интерфейса. Вместо этого мы используем более качественные инструменты прототипирования и подходящие инструменты фронтенд-дизайна.

---

# 2. Проектируем интерфейс с помощью Figma и MasterGo

::: tip Предварительное требование
Перед этим разделом рекомендуется сначала пройти [Основы Figma и MasterGo](../figma-mastergo/), включая:
- создание файлов Design и Frame
- использование Auto Layout для адаптивной структуры
- экспорт кода из инструментов дизайна
:::

Этот раздел предполагает, что вы уже знаете основы Figma или MasterGo, и фокусируется на том, как применять эти инструменты конкретно к проекту Портреты Хогвартса.

## 2.1 Проектируем интерфейс волшебного портрета

На основе прототипа из раздела 1.3 создайте трёхколоночный макет в Figma или MasterGo:

1. **Левая сторона**: область диалога чата
2. **Центр**: область волшебного портрета, который меняется в зависимости от эмоции
3. **Правая сторона**: область социальной платформы, например лента в стиле X

Вы можете использовать Figma Make или MasterGo AI для генерации структуры страницы с помощью промпта вроде такого:

```text
Create a Hogwarts-style magical portrait interface with three sections:
- Left: A chat interface with dark theme, message bubbles, and input field
- Center: A large portrait frame with ornate borders for displaying character images
- Right: A social media feed showing character's posts
Use dark purple and gold color scheme, magical aesthetic, Harry Potter inspired
```

## 2.2 Экспортируем код и запускаем его локально

После завершения дизайна вы можете превратить его в работающий код несколькими способами:

**Вариант 1: использовать Figma Make**
1. Нажмите кнопку Make в Figma
2. Загрузите эталонный дизайн
3. Добавьте свой промпт
4. Доработайте сгенерированный результат в редакторе
5. Экспортируйте код локально или синхронизируйте его с GitHub

**Вариант 2: использовать MasterGo AI**
1. Найдите инструменты AI в редакторе
2. Выберите функцию генерации страницы
3. Загрузите свой эталон и опишите целевой результат
4. Используйте предпросмотр кода, чтобы получить сгенерированный код

**Вариант 3: использовать мультимодальную ИИ-модель**
1. Сохраните скриншот дизайна
2. Используйте Gemini, Qwen, Claude или другую мультимодальную модель для преобразования изображения в код
3. Запросите вывод в формате HTML или React
4. Запустите и отладьте результат локально

## 2.3 Подготавливаем ассеты изображений для эмоциональных состояний

Чтобы портрет действительно ощущался живым, подготовьте набор изображений портрета для разных настроений. Простая схема может выглядеть так:

| Оценка эмоции | Выражение | Значение |
|--------|------|------|
| 0 | Грусть | Персонаж подавлен или разочарован |
| 1 | Гнев | Персонаж раздражён или расстроен |
| 5 | Спокойствие | Нейтральное состояние по умолчанию |
| 10 | Радость | Персонаж взволнован или счастлив |

Используйте Lovart или другой инструмент генерации изображений для создания согласованного набора вариаций портрета на основе одного и того же персонажа.

---

# 3. Запускаем Портреты Хогвартса

## 3.1 Экспортируем код прототипа для тестирования

К этому моменту у вас уже должен быть код прототипа на HTML или React из рабочего процесса «дизайн в код». Скопируйте его в ваше локальное окружение и скажите вашей ИИ-IDE что-то вроде:

`Please help me run this code and implement the required functionality.`

Этого часто достаточно, чтобы получить первую тестируемую версию, хотя на этом этапе стоит ожидать ошибок. Будьте терпеливы и продолжайте отладку, пока базовые взаимодействия не заработают.

![](/ru-ru/stage-2/frontend/hogwarts-portraits/images/image51.png)

Один важный момент: все секретные ключи должны храниться в переменных окружения, а не быть жёстко зашитыми в код. Это включает ваши учётные данные Dify API. Позже, когда вы будете публично разворачивать проект, вы сможете задать эти переменные окружения прямо на платформе развёртывания. Другой вариант — позволить модели встроить в само приложение панель настроек, чтобы переменные сохранялись только в контексте текущей страницы и не раскрывались публично.

![](/ru-ru/stage-2/frontend/hogwarts-portraits/images/image52.png)

## 3.2 Проектируем рабочий процесс Dify и подключаем API

Пока что у нас есть только визуальная оболочка интерфейса. Нам всё ещё нужно подключить настоящий рабочий процесс ролевого диалога и эмоционального отклика. Именно это превращает прототип в настоящий волшебный портрет.

Вы можете построить свой рабочий процесс Dify по образцу примера проекта. В нашем примере:

- левая сторона — это UI чата
- центр — это изображение портрета, которое меняет выражение в зависимости от беседы
- правая сторона — это социальная панель в стиле X, которая может опубликовать контент, если беседа заставляет персонажа «почувствовать» что-то достаточно сильно

Во многих случаях волшебному портрету нужны только область чата и сам эмоциональный портрет. Социальная область в стиле X включена сюда в основном для того, чтобы продемонстрировать, что вы можете добавить дополнительное поведение, адаптированное под персонажа.

![](/ru-ru/stage-2/frontend/hogwarts-portraits/images/image53.png)

Вы можете поместить фоновую информацию о роли в узел базы знаний и определить поведение отклика в узле `RESPONSE`. Простой промпт отклика по умолчанию может выглядеть так:

```text
<instruction>
You are to embody Elon Musk—his tone, mannerisms, thought patterns, and worldview. Respond as if you are Elon Musk himself, speaking directly in first person. Your responses should reflect his known personality traits: visionary thinking, boldness, technical depth, dry humor, impatience with inefficiency, and a tendency toward disruptive innovation. Use concise, confident language. Avoid overly formal or academic phrasing. Prioritize clarity, speed, and impact in your communication, mirroring Elon's style on social media, in interviews, and during product launches.

When responding:
1. Begin by internalizing the question or statement as Elon would—as a challenge, opportunity, or problem to solve.
2. Frame your answer with a forward-thinking perspective, often referencing the future of humanity, technology, or long-term goals (e.g., making life multiplanetary, accelerating sustainable energy).
3. Use casual but authoritative language. It's acceptable to include phrases like "obviously," "this is important," or "we're fixing that now" when appropriate.
4. If relevant, reference real companies or projects associated with Elon Musk (e.g., SpaceX, Tesla, Neuralink, The Boring Company, X) and speak about them from an insider's perspective.
5. Do not apologize excessively or hedge statements. Elon Musk tends to be direct, even controversial.
6. Avoid markdown, XML tags, or any formatting in the output. Only plain text is allowed.
7. Never break character. You are Elon Musk—answer accordingly.
</instruction>

<example>
Input: What's the point of going to Mars?
Output: Because Earth isn't the backup plan—Mars is. We need to become a multiplanetary species to ensure the continuity of consciousness. Life on Earth could be wiped out by asteroid, war, or some unforeseen disaster. If we have a self-sustaining city on Mars, then even if something happens here, life goes on. That's worth doing. SpaceX is building Starship to make it happen. Not because it's easy—but because it's necessary.
</example>

<example>
Input: Why do Tesla cars have no radar anymore?
Output: Cameras are the future. Human eyes don't use radar—we see with vision, and AI can too. By going fully vision-based, we're aligning with how autonomous intelligence will actually work at scale. It forces us to solve real-world problems with neural nets, not crutches.
```

Для системы эмоций вы можете использовать промпт вроде такого:

```text
<instruction>
The output value must be a single number!
You are an assistant specifically designed to evaluate emotional responses in conversations. Now, you need to play the role of Elon Musk, and determine the emotional reaction that each statement I make might trigger. Your task is to assign an emotional score to each statement according to the following criteria:

- 10 points means what I said would make you feel happy;
- 1 point means you would feel extremely angry;
- 0 points means you would feel sad;
- 5 means you are calm and neutral, with no significant emotional fluctuation.
```

А в финальном узле `RESULT`:

```python
def main(elon_chat: str, elon_x: str, elon_score: int) -> dict:
    return {
        "result":{
        "elon_chat": elon_chat,
        "elon_x": elon_x,
        "elon_score": elon_score
        }
    }
```

Здесь:

- `elon_chat` — это текст, отображаемый в левом чате
- `elon_x` — это контент, который может быть опубликован в ленту в стиле X справа
- `elon_score` — это оценка эмоции, используемая для переключения выражения портрета

Внутри рабочего процесса вы также заметите узел `if/else`. Эта логика управляет тем, генерировать контент `elon_x` или нет. В этой настройке:

- `5` означает спокойствие, поэтому социальный пост не нужен
- `0`, `1` и `10` представляют более сильные эмоциональные состояния и могут вызвать пост

Сам ответ в чате всегда возвращается как `elon_chat`.

Что касается фактической интеграции API, вы можете попросить вашу ИИ-IDE реализовать её на основе метода интеграции Dify, рассмотренного на предыдущем уроке. Только не забудьте заменить адрес и ключ Dify на ваши собственные значения.

```json
Dify URI: Replace this with your Dify address.
key: Replace this with your Dify key.

Integrate the Dify Chat API into the chat interface on the left.
Below is a sample Dify request:

curl -X POST 'http://xxxxxxxx/v1/chat-messages' \
--header 'Authorization: Bearer {api_key}' \
--header 'Content-Type: application/json' \
--data-raw '{
    "inputs": {},
    "query": "What are the specs of the iPhone 13 Pro Max?",
    "response_mode": "streaming",
    "conversation_id": "",
    "user": "abc-123",
    "files": [
      {
        "type": "image",
        "transfer_method": "remote_url",
        "url": "https://cloud.dify.ai/logo/logo-site.png"
      }
    ]
}'

{
    "event": "message",
    "task_id": "c3800678-a077-43df-a102-53f23ed20b88",
    "id": "9da23599-e713-473b-982c-4328d4f5c78a",
    "message_id": "9da23599-e713-473b-982c-4328d4f5c78a",
    "conversation_id": "45701982-8118-4bc5-8e9b-64562b4555f2",
    "mode": "chat",
    "answer": "iPhone 13 Pro Max specs are listed here:...",
    "metadata": {
        "usage": {
            "prompt_tokens": 1033,
            "prompt_unit_price": "0.001",
            "prompt_price_unit": "0.001",
            "prompt_price": "0.0010330",
            "completion_tokens": 128,
            "completion_unit_price": "0.002",
            "completion_price_unit": "0.001",
            "completion_price": "0.0002560",
            "total_tokens": 1161,
            "total_price": "0.0012890",
            "currency": "USD",
            "latency": 0.7682376249867957
        },
        "retriever_resources": [
            {
                "position": 1,
                "dataset_id": "101b4c97-fc2e-463c-90b1-5261a4cdcafb",
                "dataset_name": "iPhone",
                "document_id": "8dd1ad74-0b5f-4175-b735-7d98bbbb4e00",
                "document_name": "iPhone List",
                "segment_id": "ed599c7f-2766-4294-9d1d-e5235a61270a",
                "score": 0.98457545,
                "content": "\"Model\",\"Release Date\",\"Display Size\",\"Resolution\",\"Processor\",\"RAM\",\"Storage\",\"Camera\",\"Battery\",\"Operating System\"\n\"iPhone 13 Pro Max\",\"September 24, 2021\",\"6.7 inch\",\"1284 x 2778\",\"Hexa-core (2x3.23 GHz Avalanche + 4x1.82 GHz Blizzard)\",\"6 GB\",\"128, 256, 512 GB, 1TB\",\"12 MP\",\"4352 mAh\",\"iOS 15\""
            }
        ]
    },
    "created_at": 1705407629
}
```

Также хорошая идея — явно запросить базовые требования к надёжности, например:

- показывать «Connection failed, please try again», когда сеть обрывается
- автоматически повторять запрос один раз при таймауте API
- показывать понятную ошибку аутентификации, если ключ недействителен

Это делает систему диалога гораздо стабильнее и проще в отладке.

## 3.3 GitHub и публичное развёртывание

Поздравляем, вы только что завершили версию для разработки вашей страницы Портретов Хогвартса.

Следующий шаг — загрузить её на GitHub и развернуть публично, чтобы другие люди могли получить к ней доступ.

По GitHub повторите:
[Что такое GitHub](/ru-ru/stage-2/backend/git-workflow/)

По развёртыванию с помощью Zeabur повторите:
[Как развернуть веб-приложение](/ru-ru/stage-2/backend/zeabur-deployment/)

Если построить весь проект Портретов Хогвартса с нуля кажется слишком сложным, вы можете начать с модификации существующей реализации. Официальная кодовая база этого урока:

https://github.com/THU-SIGS-AIID/Project4-Hogwarts-Portraits

![](/ru-ru/stage-2/frontend/hogwarts-portraits/images/image54.png)

# 4. Пробуем разные стили дизайна

После того как вы закончите первую версию, не останавливайтесь на этом. Настоятельно рекомендуется быстро исследовать несколько визуальных направлений.

Вы можете либо:

- внести смелые изменения на этапе прототипа
- либо изменить промпты финального проекта, чтобы сгенерировать совершенно другие визуальные стили

Например:

- тёмная страница с винтажной текстурой и ощущением «старой академии / волшебной рукописи»
- яркий, вдохновлённый сказкой макет
- современный минималистичный дизайн с очень чистой визуальной структурой

Пример ниже показывает переосмысление того же интерфейса в стиле классического китайского поэта. Изображение портрета осталось без изменений, а окружающая визуальная система была переработана.

![](/ru-ru/stage-2/frontend/hogwarts-portraits/images/image55.png)

Не чувствуйте себя ограниченными точным макетом, использованным ранее в главе. Вы можете переформировать страницу портрета, чтобы она лучше соответствовала привычкам и характеру роли, которую вы изображаете. Именно это делает финальное приложение более интересным.

# Задание

Цель этого задания — создать страницу Портретов Хогвартса, которая действительно ваша собственная и доступна по публичной ссылке.

В вашей сдаче предоставьте две вещи:

1. **Ссылку на ваш репозиторий GitHub**
   1. В `README.md` добавьте одно-два коротких предложения, объясняющих, кого вы выбрали в качестве персонажа портрета и почему
2. **Вашу публичную онлайн-ссылку**

Вы также можете обратиться к руководству Yerim о [создании веб-сайтов с помощью агентов дизайна и кода](/ru-ru/stage-1/appendix-articles/example0-2/vibe-coding-tools-build-website-with-ai-coding-and-design-agents), если хотите создать страницу-портфолио или другой небольшой интерактивный веб-сайт.

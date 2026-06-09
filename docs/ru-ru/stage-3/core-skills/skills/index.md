# Полное руководство по Claude Code Skills

## Введение в Skills

**Claude Code Skills** — это возможность упаковывать специализированные знания, рабочие процессы и лучшие практики в переиспользуемые «наборы навыков».

Можно представить Skills как «учебники навыков», которыми оснащается Claude. Когда вам нужно, чтобы он выполнил конкретную задачу, вам больше не приходится снова и снова объяснять требования. Вместо этого он может сразу выполнить работу по стандартам, заранее заданным в Skill.

### Зачем нам нужны Skills?

До появления Skills использование Claude Code сопровождалось несколькими проблемами:

- **Повторяющиеся инструкции**: каждый раз приходилось объяснять, например, «какому стилю кода следовать» и «как должны выглядеть сообщения коммитов»
- **Знания не накапливались**: индивидуальный опыт членов команды по работе с Claude нельзя было разделить
- **Несогласованные стандарты**: разные люди, используя Claude, могли получить совершенно разные результаты
- **Низкая эффективность**: распространённые задачи каждый раз приходилось объяснять с нуля

Skills решают эти проблемы и превращают Claude в «опытного члена команды» — он знает соглашения вашего проекта, рабочие процессы и лучшие практики.

---

## Почему стоит изучать Skills именно сейчас?

**Skills становятся обязательной компетенцией для AI-инженеров**:

- **Высокий интерес сообщества**: связанные репозитории на GitHub быстро набирают звёзды. Например, проект OpenSkills уже достиг 7.2k звёзд, а Obsidian Skills набрал 6.6k звёзд всего за 9 дней
- **Официальная поддержка**: Anthropic поддерживает официальный репозиторий Skills, а Vercel выпустил Agent Skills и инструмент find-skills
- **Высокая практичность**: от код-ревью и операций с Git до создания видео и генерации презентаций — Skills охватывают множество сценариев. На платформе skills.sh уже есть популярные навыки с 60K+ подписок
- **Прирост эффективности**: настроил один раз, переиспользуй многократно, и пусть Claude действительно станет вашим «цифровым сотрудником»
- **Признание разработчиками**: рекомендуется многими техническими сообществами и широко считается ключевым инструментом для повышения эффективности AI-программирования

---

## Быстрый старт

Теперь, когда вы понимаете ценность Skills, давайте сразу их попробуем. В этом разделе вы установите свой первый Skill и выполните несколько интересных практических задач, чтобы быстро выработать интуицию.

### Шаг 1: Установите `find-skills` (настоятельно рекомендуется)

Прежде чем начать пользоваться Skills, настоятельно рекомендуется сначала установить `find-skills`. Это «лучший инструмент поиска навыков» в мире AI-агентов, у которого уже более 60K подписок.

**Что такое `find-skills`?**

Проще говоря, `find-skills` — это как «поисковая система магазина приложений» для AI-агентов. Когда вам нужно выполнить задачу, но у вас нет подходящего локального Skill, он автоматически найдёт и порекомендует наиболее подходящий.

**Установка `find-skills`:**

```bash
npx skills add vercel-labs/skills@find-skills -g -y
```

После установки вы можете просто сказать Claude, что вам нужно, и он автоматически использует `find-skills` для поиска подходящих навыков.

**Пример использования:**

```text
Мне нужно оптимизировать производительность React-компонента. Помоги найти, какие навыки я могу использовать.
```

Claude выполнит поиск через `find-skills`, а затем подскажет, какие подходящие навыки он нашёл, чтобы вы могли выбрать один для установки.

**Почему стоит сначала установить `find-skills`?**

До `find-skills`:
- вручную искать связанные навыки на GitHub
- копировать, устанавливать и настраивать их по одному
- многократно отлаживать и адаптировать их

После `find-skills`:
- описать потребность одним предложением
- ИИ автоматически находит наиболее подходящий навык
- установить в один клик и сразу использовать

**Примечание для пользователей Windows**: официальная версия имеет ограниченную поддержку Windows. Сообщество сделало совместимую с Windows версию, которая поддерживает CMD и PowerShell и добавляет поиск на китайском языке.

Скачать версию для Windows: [github.com/tongbei821/customize-skills](https://github.com/tongbei821/customize-skills/blob/main/findskills/SKILL.md)

Шаги установки:
1. Скачайте версию `SKILL.md` для Windows
2. Замените файл в `C:/Users/your-username/.agents/skills/find-skills`
3. Перезапустите Claude Code, и изменения вступят в силу

**Связанные ссылки**:
- [Официальный сайт Skills](https://skills.sh/) — просмотр всех доступных навыков
- [Репозиторий find-skills](https://github.com/vercel-labs/agent-skills) — официальный исходный код

### Установите и попробуйте свой первый Skill

После установки `find-skills` давайте воспользуемся им, чтобы найти и установить весёлый первый Skill: инструмент для создания видео Remotion.

#### Шаг 1: Используйте `find-skills` для поиска Remotion

Введите это в Claude Code:

```text
Помоги найти навыки, связанные с Remotion. Я хочу делать видео.
```

Claude выполнит поиск через `find-skills` и порекомендует `remotion-dev/skills`.

#### Шаг 2: Установите Remotion Skills

```bash
npx skills add remotion-dev/skills -g
```

#### Шаг 3: Используйте его, чтобы сделать что-нибудь интересное

Remotion — это фреймворк для создания видео с помощью кода на React. После установки этого Skill вы можете на естественном языке попросить Claude помочь вам написать код видео.

**Задача 1: Сделать классное видео с анимированным текстом**

```text
Сделай видео с помощью Remotion:
- 1920x1080, 5 секунд
- Строка текста "Hello World" влетает слева
- Одновременно с эффектами вращения и масштабирования
- Фон — градиент
```

Claude сгенерирует полный код Remotion, и вы сможете запустить его, чтобы увидеть анимацию.

**Задача 2: Сделать видео с визуализацией данных**

```text
Сделай 10-секундное видео, показывающее рост данных:
- Начни со столбчатой диаграммы
- Столбцы растут один за другим с анимацией
- Числа отсчитываются вверх
- В конце покажи крупный текст "300% роста"
```

**Задача 3: Сделать многосценное демонстрационное видео продукта**

```text
Сделай демонстрационное видео продукта с тремя сценами:
Сцена 1: Логотип проявляется, 2 секунды
Сцена 2: Возможности продукта появляются одна за другой, 3 секунды
Сцена 3: Всплывает CTA-кнопка, 2 секунды
Используй плавные переходы между каждой сценой
```

**Запуск кода**:

Код, который генерирует Claude, — это полноценный проект Remotion. Вы можете:

1. Создать новый проект: `npx create-video my-video`
2. Скопировать в него сгенерированный Claude код
3. Запустить предпросмотр: `npm start`
4. Отрендерить видео: `npm run build`

---

### Второй Skill: используйте `find-skills`, чтобы решить проблему «фронтенд выглядит уродливо и тормозит»

#### Шаг 1: Опишите свою проблему на естественном языке

Просто скажите Claude свою высокоуровневую потребность:

```text
Мой сайт выглядит устаревшим и медленно загружается. Помоги найти, какие навыки я могу использовать.
```

Или сформулируйте чуть конкретнее:

```text
Я хочу, чтобы фронтенд выглядел лучше и перестал так тормозить.
```

#### Шаг 2: Claude выполнит поиск с помощью `find-skills`

Claude выполнит поиск в базе данных skills.sh через `find-skills` и порекомендует подходящие навыки. Для запроса вроде «сделать красивее + уменьшить тормоза» он порекомендует:

**anthropics/skills/frontend-design** (официальный навык)

Этот навык специально создан для решения проблемы сгенерированных ИИ интерфейсов, которые «выглядят простовато и шаблонно», помогая Claude проектировать:

- уникальные визуальные стили, избегая привычного «шаблонного вида ИИ»
- профессиональные цветовые схемы и типографику
- плавные анимационные эффекты
- код продакшн-уровня с чистым кодом и естественно лучшей производительностью

#### Шаг 3: Установите и используйте его

**Установка**:

```bash
npx skills add anthropics/skills/frontend-design -g
```

**Задачи, которые вы можете выполнить с его помощью**:

```text
Помоги мне переработать дизайн этой страницы. Я хочу, чтобы она выглядела очень профессионально и не как сгенерированная ИИ.
```

```text
Этот UI слишком уродливый. Перепиши его в более современном стиле дизайна.
```

```text
Сделай дашборд с тёмной темой и сильным технологичным ощущением.
```

Claude будет следовать соглашениям этого навыка и поможет вам спроектировать:
- уникальное визуальное направление, например минимализм, ретрофутуризм или брутализм
- тщательно подобранные цвета и шрифты
- разумные отступы и компоновку
- плавную интерактивную анимацию

---

### Сравнение двух Skills

| Skills | Какую проблему решает? | Веселье |
|--------|-------------|---------|
| **remotion-dev/skills** | Делать видео с помощью кода | ⭐⭐⭐⭐⭐ |
| **anthropics/skills/frontend-design** | Сделать фронтенд красивее | ⭐⭐⭐⭐ |

---

### Третий Skill: используйте `frontend-slides`, чтобы быстро делать красивые презентации

#### Введение

**frontend-slides** — это Skill, который позволяет создавать красивые HTML-презентации на естественном языке, даже если вы не знаете ни CSS, ни JavaScript.

Его основная идея — «**показывай, а не рассказывай**». Если вы не можете чётко описать желаемый стиль дизайна, он сгенерирует для вас 3 визуальных превью на выбор, вместо того чтобы заставлять вас описывать абстрактные требования вроде «синий фон, крупный шрифт».

#### Установка `frontend-slides`

**Способ 1: установить вручную**

```bash
# Создать каталог навыка
mkdir -p ~/.claude/skills/frontend-slides

# Скачать файлы (или скопировать с GitHub)
# 1. Зайдите на https://github.com/zarazhangrui/frontend-slides
# 2. Скачайте SKILL.md и STYLE_PRESETS.md
# 3. Положите их в ~/.claude/skills/frontend-slides/
```

**Способ 2: установить с помощью `find-skills`**

```text
Помоги найти навык для создания презентаций
```

Claude выполнит поиск через `find-skills` и порекомендует `frontend-slides`.

#### Сценарии использования

**Сценарий 1: создать презентацию с нуля**

```text
/frontend-slides

Я хочу создать презентацию для привлечения инвестиций в проект AI-стартапа, около 10 слайдов
```

Claude проведёт вас через шаги:
1. заполнить содержимое каждого слайда: заголовки, пункты списка и изображения
2. описать ощущение, которое вы хотите вызвать: эффектное, профессиональное или тёплое
3. выбрать из 3 превью визуальных стилей
4. создать полную HTML-презентацию
5. открыть предпросмотр в браузере

**Сценарий 2: преобразовать файл PowerPoint**

```text
/frontend-slides

Преобразуй мою presentation.pptx в веб-презентацию
```

Claude:
1. извлечёт весь текст, изображения и заметки из PPT
2. покажет извлечённое содержимое для подтверждения
3. позволит выбрать визуальный стиль
4. сгенерирует HTML-презентацию, сохранив всё исходное содержимое

**Сценарий 3: быстро сгенерировать превью стилей**

```text
/frontend-slides

Я хочу сделать презентацию для технического доклада. Сначала покажи доступные визуальные стили.
```

Claude сразу сгенерирует 3 страницы превью в разных стилях:
- **Тёмные темы**: Neon Cyber, Terminal Green, Deep Space
- **Светлые темы**: Paper & Ink, Swiss Modern, Soft Pastel
- **Особые стили**: Brutalist, Gradient Wave

#### Встроенные визуальные стили

| Название стиля | Особенности | Подходящие сценарии |
|---------|------|---------|
| **Neon Cyber** | Футуристичный технологичный вид, эффекты частиц | Технические доклады, AI-продукты |
| **Midnight Executive** | Премиальный деловой вид, вызывающий доверие | Бизнес-отчёты, инвестиционные питчи |
| **Paper & Ink** | Редакторский стиль, литературная атмосфера | Создание контента, образовательный обмен |
| **Swiss Modern** | Чистая геометрия, стиль Bauhaus | Дизайн-портфолио, минимализм |
| **Brutalist** | Сырой, дерзкий, привлекающий внимание | Демонстрация искусства, личное самовыражение |

#### Результат на выходе

Сгенерированная презентация — это **однофайловый HTML**-документ, который включает:

- полные стили и код взаимодействия
- навигацию с клавиатуры стрелками и пробелом
- поддержку касаний и свайпов
- перелистывание слайдов колёсиком мыши
- индикаторы прогресса и навигационные точки
- анимацию, запускаемую при прокрутке
- адаптивный дизайн

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <!-- All styles are inlined, zero dependencies -->
</head>
<body>
    <section class="slide title-slide">
        <h1 class="reveal">Your Title</h1>
    </section>
    <!-- More slides... -->
</body>
</html>
```

#### Почему рекомендуется?

1. **Нулевые зависимости**: один HTML-файл, который откроется и через 10 лет
2. **Визуальный подбор**: не нужно описывать дизайн, просто выберите то, что нравится
3. **Конвертация PPT**: сохраните существующий контент и дайте ему лучшую визуальную оболочку
4. **Код продакшн-уровня**: доступный, с понятными комментариями и легко настраиваемый

**Связанные ссылки**:
- [Репозиторий frontend-slides на GitHub](https://github.com/zarazhangrui/frontend-slides) — 6.1k+ звёзд
- [Пример онлайн-предпросмотра](https://github.com/zarazhangrui/frontend-slides#output-example)

---

### Сравнение трёх Skills

| Skills | Какую проблему решает? | Веселье | Практичность |
|--------|-------------|---------|---------|
| **remotion-dev/skills** | Делать видео с помощью кода | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **anthropics/skills/frontend-design** | Сделать фронтенд красивее | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **frontend-slides** | Быстро делать красивые презентации | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |

---

### Как использовать их после установки

После установки не требуется никакой дополнительной настройки. Когда вы попросите Claude выполнить связанную задачу, он автоматически вызовет соответствующий Skill.

Просмотр установленных Skills:

```bash
npx skills list
```

---

## Что такое Skills?

### Основная концепция

**Skills — это «наборы навыков», хранящиеся в файловой системе**, и они могут включать:

- **SKILL.md**: файл определения навыка, обязательный
- **scripts/**: вспомогательные скрипты, опционально
- **templates/**: шаблоны вывода, опционально
- **references/**: справочная документация, опционально

### Skills и промпты

Возможно, вы задаётесь вопросом: чем Skills отличаются от прямой отправки промптов в Claude?

| Промпты | Skills |
|--------|--------|
| Временные, их приходится повторять каждый раз | Постоянные, написал один раз и переиспользуй многократно |
| Живут в истории диалога и расходуют токены | Загружаются по запросу и экономят токены |
| Нельзя разделить между сессиями | Можно разделять внутри команды |
| Сложно версионировать | Можно управлять с помощью Git |

### Два типа Skills

**Глобальные Skills (личные)**:
- расположение хранения: `~/.claude/skills/`
- область действия: все проекты
- подходящие сценарии: универсальные личные навыки

**Проектные Skills (командные)**:
- расположение хранения: `каталог-проекта/.claude/skills/`
- область действия: текущий проект
- подходящие сценарии: командный обмен и специфичные для проекта соглашения

### Как работают Skills

При запуске Claude Code он:

1. сканирует каталоги Skills
2. разбирает каждый файл `SKILL.md`
3. извлекает метаданные из YAML frontmatter
4. добавляет содержимое навыка в свою «базу знаний»
5. автоматически сопоставляет триггеры на основе описания

---

## Структура файла `SKILL.md`

### Базовая структура

Полный каталог Skill выглядит так:

```text
my-skill/
├── SKILL.md          # Required: skill definition file
├── scripts/          # Optional: helper scripts
├── templates/        # Optional: output templates
├── references/       # Optional: reference documents
└── examples/         # Optional: example files
```

### Шаблон `SKILL.md`

Файл `SKILL.md` состоит из двух частей:

**Часть 1: YAML Frontmatter (метаданные)**

```yaml
---
name: skill-name              # Skill name, becomes the /skill-name command
description: short description # Used for Claude's automatic trigger matching
category: development         # Category
tags:                         # Tags
  - code
  - automation
---
```

**Часть 2: содержимое Markdown (инструкции)**

```markdown
# Skill Title

## Use cases
When to use this skill

## Execution steps
1. Step one
2. Step two

## Notes
- Note 1
- Note 2
```

### Пояснение ключевых полей

| Поле | Обязательно | Пояснение |
|------|------|------|
| `name` | Да | Название навыка. Разрешены только строчные буквы, цифры и дефисы |
| `description` | Да | Описание навыка. Чем оно конкретнее, тем проще Claude сопоставить его автоматически |
| `category` | Нет | Метка категории |
| `tags` | Нет | Дополнительные метки категорий |
| `allowed-tools` | Нет | Инструменты, которые можно использовать без дополнительных разрешений |

---

## Skills и MCP: в чём разница?

Многие новички путают Skills и MCP, но это совершенно разные вещи.

### Основные различия

| Измерение | Skills | MCP |
|------|--------|-----|
| **Суть** | Знания и рабочий процесс | Инструменты и интерфейсы |
| **Что предоставляет** | Говорит ИИ «как это делать» | Даёт ИИ «что он может использовать» |
| **Расположение хранения** | Каталог `skills/` | MCP-сервер |
| **Формат конфигурации** | Файлы Markdown | JSON-файлы конфигурации |
| **Способ запуска** | `/skill-name` или автоматическое распознавание | Автоматически загружается через конфигурацию |

### Интуитивная аналогия

Если бы Claude был «работником»:

- **MCP** был бы «инструментами», выданными работнику, например гаечный ключ, компьютер и права доступа
- **Skills** были бы «руководством по эксплуатации», выданным работнику, например как делать код-ревью или как сдавать код

### Их взаимосвязь

Skills и MCP не конкурируют друг с другом. Они дополняют друг друга:

```text
User task -> Claude recognizes the requirement
               ↓
        Load relevant Skills (know how to do it)
               ↓
        Call tools through MCP (have tools available)
               ↓
        Complete the task
```

### Пример

**Сценарий: код-ревью**

- **Skills** определяют шаги ревью, чек-лист и формат вывода
- **MCP** предоставляет возможность доступа к GitHub PR и получения диффов кода

Работая вместе: Skills говорят Claude «как делать ревью», а MCP даёт Claude «возможность доступа к коду».

### Рекомендация по выбору

| Ваша потребность | Рекомендуемое решение |
|----------|----------|
| Нужно определить рабочий процесс | Используйте Skills |
| Нужен доступ к внешним данным | Используйте MCP |
| Нужно и то, и другое | Используйте их вместе |

---

## Распространённые ресурсы для получения Skills

### Официальные ресурсы

- [Официальный репозиторий Skills от Anthropic](https://github.com/anthropics/skills) — официально поддерживаемая коллекция навыков
- [Официальная документация Claude Code — Skills](https://docs.anthropic.com/ru-ru/docs/claude-code/configuration/skills) — официальная документация

### Ресурсы сообщества на GitHub

| Репозиторий | Описание |
|------|------|
| [shanraisshan/claude-code-best-practice](https://github.com/shanraisshan/claude-code-best-practice) | Поддерживается Борисом Черни, руководителем Claude Code, включает Skills, Agents, Hooks и многое другое |
| [affaan-m/everything-claude-code](https://github.com/affaan-m/everything-claude-code) | Комплексный набор инструментов, включая предварительно настроенные Skills |
| [JackyST0/awesome-agent-skills](https://github.com/JackyST0/awesome-agent-skills) | Отобранный список ресурсов Skills |
| [jeffallan/claude-skills](https://github.com/jeffallan/claude-skills) | 66 профессиональных навыков и 300+ справочных документов |
| [GitCode/awesome-claude-skills](https://gitcode.com/GitHub_Trending/aw/awesome-claude-skills) | Отобранная коллекция с открытым исходным кодом |

### Как устанавливать Skills из сообщества

С помощью `find-skills` вам нужно лишь сказать Claude, что вам нужно, и он автоматически выполнит поиск и порекомендует:

```text
Помоги найти навык, связанный с оптимизацией производительности React
```

Claude выполнит поиск в базе данных skills.sh через `find-skills`, затем перечислит наиболее подходящие навыки, и вы сможете выбрать один для установки.

**Советы по поиску**:

- используйте конкретные ключевые слова: `"react testing"` лучше, чем `"testing"`
- комбинируйте «область + действие»: `"nextjs deploy"`, `"typescript lint"`
- отдавайте предпочтение навыкам с большим числом установок, поскольку 10K+ обычно означает проверенный временем
- следите за списком трендов, чтобы обнаружить новые навыки

---

## Как создать собственные Skills

Создать Skills можно двумя способами: напрямую попросить Claude создать его для вас или воспользоваться специальным инструментом `skill-creator`.

### Способ 1: напрямую попросить Claude помочь создать

Это самый простой подход. Просто опишите Claude свою потребность на естественном языке.

**Пример**:

```text
Пожалуйста, помоги создать навык с именем "format-code" для автоматического форматирования кода.

Требования:
1. Автоматически определять язык программирования
2. Применять соответствующие правила форматирования
3. Возвращать дифф до и после форматирования
```

Claude автоматически:
1. создаст структуру каталогов
2. сгенерирует файл `SKILL.md`
3. заполнит YAML frontmatter
4. напишет содержимое навыка

**Подходящие сценарии**:
- быстрое создание простых навыков
- вы знаете, чего хотите, но не знакомы с форматом `SKILL.md`
- вы хотите быстро итеративно вносить изменения

### Способ 2: использовать `skill-creator`

`skill-creator` — это специальный инструмент для создания Skills. Он пошагово ведёт вас по процессу.

**Установка**:

```bash
npx skills add anthropics/skills@skill-creator -g
```

Или установите весь официальный репозиторий навыков:

```bash
npx skills add anthropics/skills -g
```

**Использование**:

```text
/skill-creator
```

Затем заполните подсказки:
- имя навыка
- описание функциональности
- сценарии использования
- шаги выполнения

`skill-creator`:
1. поможет вам прояснить назначение навыка
2. сгенерирует черновик `SKILL.md`
3. создаст тестовые случаи
4. запустит оценку и оптимизирует его

**Подходящие сценарии**:
- создание сложных навыков
- нужен более стандартный процесс создания
- хотите протестировать и проверить навык

### Сравнение двух способов

| Способ 1: прямое создание | Способ 2: `skill-creator` |
|-----------------|---------------------|
| Быстро и просто | Пошаговое руководство |
| Подходит для простых навыков | Подходит для сложных навыков |
| Завершается прямо в диалоге | Стандартизированный процесс |
| Гибкое изменение | Включает тестирование и проверку |

### Совет: как написать хорошее требование

**Хорошее описание требования**:

```text
Создай навык "git-commit", который автоматически коммитит код.

Шаги выполнения:
1. Проверить, какие файлы были изменены
2. Сгенерировать сообщение коммита по стандарту Conventional Commits
3. Выполнить git commit
4. Спросить, нужно ли запушить

Примечания:
- Проверить наличие конфиденциальной информации перед коммитом
- Не коммитить каталоги вроде dist/ или node_modules/
```

**Плохое описание требования**:

```text
Помоги мне написать навык для коммита кода
```

Это слишком расплывчато. Claude не будет точно знать, что именно ему нужно сделать.

---

## Распространённые примеры Skill

### Пример 1: Skill для код-ревью

Создайте каталог и файл:

```bash
mkdir -p ~/.claude/skills/review-pr
```

```bash
cat > ~/.claude/skills/review-pr/SKILL.md << 'EOF'
---
name: review-pr
description: Review Pull Requests for code quality, security, and test coverage
---

You are a senior code reviewer.

## Review workflow

1. **Code style check**
   - Does the code follow team conventions?
   - Are names clear?
   - Are comments sufficient?

2. **Security check**
   - Are there security vulnerabilities?
   - Is sensitive information exposed?
   - Is input validation complete?

3. **Testing check**
   - Are there enough tests?
   - Do test cases cover edge conditions?
   - Are the tests runnable?

4. **Overall evaluation**
   - What are the strengths?
   - What needs improvement?
   - Do you recommend approving the merge?

## Output format

Please output the review results in a clear structure using a list format.
EOF
```

Как им пользоваться:

```text
/review-pr
Пожалуйста, проверь PR текущей ветки
```

### Пример 2: Skill для автоматического коммита в Git

```bash
mkdir -p ~/.claude/skills/git-commit
```

```bash
cat > ~/.claude/skills/git-commit/SKILL.md << 'EOF'
---
name: git-commit
description: Automatically detect changes, generate a commit message, and commit the code
---

You are a skilled Git user.

## Execution workflow

1. **Check changes**
   Run `git status` to view modified files
   Run `git diff` to view detailed changes

2. **Generate commit message**
   Analyze the nature of the changes
   Generate a commit message that follows Conventional Commits
   Format: `type(scope): description`

3. **Security check**
   Check whether there is sensitive information such as keys, passwords, or tokens
   Check whether directories that should not be committed are included

4. **Execute after confirmation**
   Show the commit message for confirmation
   Run `git add` and `git commit`
   Ask whether a push is needed

## Notes

- Do not commit directories such as node_modules/, dist/, or .next/
- Run tests before committing to ensure the code works
- The commit message should clearly explain the change
EOF
```

Как им пользоваться:

```text
/git-commit
```

### Пример 3: Skill для генерации тестов

```bash
mkdir -p ~/.claude/skills/gen-test
```

```bash
cat > ~/.claude/skills/gen-test/SKILL.md << 'EOF'
---
name: gen-test
description: Automatically generate unit tests for code to ensure correctness
---

You are a test engineer.

## Workflow

1. **Analyze the code**
   - Understand the function or class
   - Identify inputs and outputs
   - Find edge cases

2. **Generate tests**
   - Use an appropriate test framework
   - Cover normal cases
   - Cover edge cases
   - Cover exceptional cases

3. **Validate the tests**
   - Make sure the tests can run
   - Make sure the tests can catch problems
   - Do not over-mock the implementation

## Test frameworks

- JavaScript/TypeScript: Jest or Vitest
- Python: pytest
- Go: testing package

## Output format

Output the test code first, then explain how to run the tests.
EOF
```

Как им пользоваться:

```text
/gen-test
Сгенерируй юнит-тесты для src/utils.ts
```

### Пример 4: Skill для генерации документации

```bash
mkdir -p ~/.claude/skills/gen-readme
```

```bash
cat > ~/.claude/skills/gen-readme/SKILL.md << 'EOF'
---
name: gen-readme
description: Automatically generate a README document for a project
---

You are a technical documentation expert.

## Workflow

1. **Analyze the project**
   - Scan the project directory structure
   - Check package.json or other configuration files
   - Read the existing code

2. **Generate content**
   - Project introduction
   - Installation steps
   - Usage instructions
   - API documentation
   - Development guide

3. **Formatting**
   - Use a clear section structure
   - Add code examples
   - Add appropriate badges
   - Add license information

## Standard README structure

- Project title and introduction
- Features
- Installation
- Quick start
- Usage instructions
- API documentation
- Development guide
- Contribution guide
- License
EOF
```

Как им пользоваться:

```text
/gen-readme
Сгенерируй README-документ для текущего проекта
```

---

## Продвинутые приёмы

### Сочетание Skills с Hooks

Hooks могут автоматически выполнять действия по определённым событиям. В сочетании со Skills они обеспечивают более мощную автоматизацию.

Например, автоматически форматировать код после сохранения:

```json
// .claude/hooks.json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": {
        "tool_name": "Edit"
      },
      "hook": {
        "type": "command",
        "command": "/format-code"  // Call the format-code skill
      }
    }]
  }
}
```

### Сочетание Skills с Commands

Commands — это простые сокращённые команды. Skills — это сложные рабочие процессы. Их можно использовать вместе.

### Командное взаимодействие

**Делитесь проектными Skills**:

1. поместите Skills в `.claude/skills/`
2. закоммитьте их в Git
3. члены команды смогут пользоваться ими после клонирования проекта

**Контроль версий**:

- Skills можно версионировать так же, как код
- каждый коммит может фиксировать изменения в Skills
- можно откатиться к старым версиям

---

## Часто задаваемые вопросы

### Q1: Почему Skill не сработал?

Возможные причины:
- неправильный формат YAML frontmatter
- описание недостаточно конкретно
- Claude Code не был перезапущен

Как решить:
- проверьте, корректен ли формат YAML
- улучшите описание и добавьте конкретные сценарии использования
- перезапустите Claude Code

### Q2: Как написать точное описание?

Хорошее описание включает:
- конкретную функцию навыка
- сценарий использования, например «когда пользователь упоминает...»
- триггерные ключевые слова

**Плохой пример**:
```text
description: Review code
```

**Хороший пример**:
```text
description: Review Pull Request code. Trigger when the user mentions PR, review, or code review.
```

### Q3: В чём разница между Skills и Commands?

| Commands | Skills |
|----------|--------|
| Простые сокращённые команды | Полноценные рабочие процессы |
| Один файл `.md` | Структура каталогов (`SKILL.md` + опциональные файлы) |
| Запускаются вручную | Могут запускаться автоматически |
| Подходят для простых операций | Подходят для сложных процессов |

### Q4: Как отлаживать Skill?

1. Используйте `/skills`, чтобы проверить, распознан ли навык
2. Напрямую введите имя навыка, чтобы запустить его вручную
3. Проверьте, корректно ли содержимое `SKILL.md`
4. Просмотрите логи Claude Code

---

## Справочные материалы

### Официальные ресурсы

- [Официальная документация Claude Code — Skills](https://docs.anthropic.com/ru-ru/docs/claude-code/configuration/skills)
- [Стандарт Agent Skills](https://agentskills.io/)
- [Инженерная статья Anthropic (практические идеи, стоящие за Agent Skills)](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)
- [Официальный репозиторий Skills от Anthropic на GitHub](https://github.com/anthropics/skills)
- [Документация VS Code Copilot Agent Skills](https://code.visualstudio.com/docs/copilot/customization/agent-skills)

### Каталоги ресурсов

- [skills.sh](https://skills.sh/) — магазин приложений Agent Skills от Vercel с библиотекой из 48 000+ навыков
- [find-skills](https://github.com/vercel-labs/agent-skills) — интеллектуальный инструмент поиска навыков с 60K+ подписок
- [Маркетплейс Skills (китайский интерфейс)](https://skillsmp.com/zh) — поиск и установка Skills из сообщества

### Проекты сообщества на GitHub

- [vercel-labs/agent-skills](https://github.com/vercel-labs/agent-skills) — официальная коллекция Agent Skills от Vercel Labs, включая find-skills
- [claude-code-best-practice](https://github.com/shanraisshan/claude-code-best-practice) — официальные лучшие практики, поддерживаемые Борисом Черни
- [everything-claude-code](https://github.com/affaan-m/everything-claude-code) — комплексный набор инструментов, включая предварительно настроенные Skills
- [awesome-claude-skills](https://github.com/ComposioHQ/awesome-claude-skills) — отобранный список ресурсов Skills
- [superpowers](https://github.com/obra/superpowers) — коллекция Skills для рабочих процессов автоматизации разработки ПО
- [jeffallan/claude-skills](https://github.com/jeffallan/claude-skills) — 66 профессиональных навыков и 300+ справочных документов
- [awesome-agent-skills](https://github.com/JackyST0/awesome-agent-skills) — отобранный список ресурсов

### Официальные примеры Skill

- [skill-creator](https://github.com/anthropics/skills/tree/main/skills/skill-creator) — навык для создания новых навыков
- [mcp-builder](https://github.com/anthropics/skills/tree/main/skills/mcp-builder) — навык для создания MCP-серверов
- [slack-gif-creator](https://github.com/anthropics/skills/tree/main/skills/slack-gif-creator) — навык для создания Slack GIF

### Китайские руководства

- [Полное руководство по продвинутой настройке и приёмам использования Claude Code](https://blog.csdn.net/2601_95335870/article/details/158460599)
- [Vibe Coding — сквозная практика с CLAUDE.md, Skills и Subagents](https://blog.csdn.net/yangshangwei/article/details/158319117)
- [Пошаговое руководство по кастомизации Claude Code Skills](https://m.blog.csdn.net/u010028049/article/details/157979705)

## Углублённое чтение: внутренний механизм Claude Skills

Далее мы глубже разберём, как Claude Skills работают внутри, чтобы вы не только знали, как их использовать, но и понимали, почему они спроектированы именно так.

### Взгляд с позиции первопринципов: динамическая инъекция контекста на основе промптов

Сначала усвойте один ключевой факт: **Skills — это не исполняемый код**.

Skills по своей сути — это высокоуровневые инструкции, или промпты, которые «инъецируются» в контекст Claude, когда это необходимо. Этот подход называется «**Prompt-based Dynamic Context Injection & Meta-Tool Architecture**».

```text
┌─────────────┐      ┌─────────────┐      ┌──────────────┐
│ User Request│ ───> │ LLM Matches │ ───> │ Trigger Skill│
└─────────────┘      │Description  │      └──────────────┘
                     └─────────────┘              │
                                                 ▼
                                          ┌──────────────┐
                                          │ Inject Full  │
                                          │ Instructions │
                                          └──────────────┘
                                                 │
                                                 ▼
                                          ┌──────────────┐
                                          │ Execute Task │
                                          └──────────────┘
```

### Трёхуровневая архитектура прогрессивной загрузки (оптимизация токенов)

Чтобы справляться с большим количеством Skills, не расходуя слишком много токенов, Claude использует умный трёхуровневый механизм загрузки:

| Уровень | Содержимое | Когда загружается | Стоимость в токенах |
|------|------|----------|-----------|
| **Уровень 1: Метаданные** | YAML frontmatter (`name + description`) | При запуске Claude | ~30-50 токенов/навык |
| **Уровень 2: Инструкции** | Полное содержимое `SKILL.md` | При запуске Skill | ~5 000 токенов |
| **Уровень 3: Ресурсы** | Скрипты, шаблоны, справочники | Доступ из файловой системы по запросу | Не добавляется в контекст |

**Преимущества этого подхода**:

- Предположим, у вас 100 Skills. При запуске расходуется лишь около 3 000-5 000 токенов на метаданные
- Только запущенный Skill загружает своё полное содержимое
- Ресурсные файлы, например справочные документы, никогда полностью не загружаются в контекст

**По сравнению с отсутствием Skills**:

```text
Without Skills: every conversation needs 50,000+ tokens to describe all capabilities
With Skills: startup ~100 tokens/skill + 5,000 tokens loaded on demand
Savings: on average 40,000+ tokens saved per conversation
```

### Механизм двойной инъекции контекста

Когда Skill активируется, система одновременно вносит две модификации:

**1. Инъекция в контекст диалога**

```javascript
// What the user sees (visible message)
<command-message>The "pdf" skill is loading</command-message>

// What the AI actually receives (hidden meta-message)
{
  isMeta: true,  // marked as a meta-message, not shown in the UI
  content: `
    # PDF Analysis Expert Instructions

    You are a professional PDF analysis expert. Workflow:
    1. Use pdftotext to extract text
    2. Analyze the document structure
    3. Generate a summary report
    ...
  `  // full SKILL.md content, possibly thousands of words
}
```

**2. Модификация контекста выполнения**

Помимо инъекции инструкций, Skill также может динамически изменять окружение Claude:

| Тип модификации | Пример | Пояснение |
|---------|------|------|
| **Права на инструменты** | `allowed-tools: "Bash(pdftotext:*)"` | Временно предоставить доступ к конкретному инструменту |
| **Переключение модели** | Переключиться с Sonnet на Opus | Некоторые сложные задачи требуют более сильного рассуждения |
| **Изоляция контекста** | Создать пространство дочерней сессии | Избежать загрязнения контекста основного диалога |

### Механизм маршрутизации, полностью основанный на рассуждениях LLM

Это очень важное проектное решение: **Claude Skills не используют жёстко закодированную маршрутизацию**.

| Традиционный подход | Claude Skills |
|---------|--------------|
| ❌ Сопоставление эмбеддингов | ✅ Чистое рассуждение LLM |
| ❌ Классификатор | ✅ Прямой проход через Transformer |
| ❌ Регулярные выражения или сопоставление ключевых слов | ✅ Понимание естественного языка |
| ❌ Отдельный алгоритм маршрутизации | ✅ Единое принятие решений моделью |

**Рабочий процесс**:

```text
1. The name and description of every Skill are formatted into the Skill tool description

2. Claude receives:
   - the user message
   - the list of available tools, including the Skill meta-tool
   - the Skill list, with name + description

3. Claude's natural language understanding matches the user's intent to a Skill description

4. When the match succeeds, it calls: command: "skill-name"
```

**Почему так спроектировано?**

**Жёстко закодированная маршрутизация требует**:
- дополнительных затрат на сопровождение
- не способна понимать сложные семантические связи
- сложности с обработкой нескольких языков
- отсутствия поддержки нечёткого сопоставления

**Чистое рассуждение LLM**:
- использует собственное понимание языка у Claude
- автоматически обрабатывает несколько языков, синонимы и нечёткие описания
- не требует дополнительного сопровождения
- делает решения по маршрутизации более интеллектуальными

### Механизм разбора файлов

**Структура файла `SKILL.md`**:

```bash
my-custom-skill/
├── SKILL.md              # Required: core definition file
├── config.json           # Optional: metadata config
├── README.md             # Recommended: usage documentation
├── scripts/              # Optional: executable scripts
├── templates/            # Optional: template folder
└── references/           # Optional: reference documents
```

**Поток разбора**:

```text
┌─────────────────────────────────────────────────────────────┐
│                    Claude Code startup                      │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│  Scan ~/.claude/skills/ and .claude/skills/ directories    │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│  Use the gray-matter library to parse each SKILL.md        │
│  YAML frontmatter                                           │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│  Validate required fields (name and description)           │
│  - name: max 64 characters, only lowercase letters,        │
│    numbers, and hyphens                                     │
│  - description: used for LLM automatic matching            │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│  Extract metadata and build the Skill list                 │
│  (only load name + description, not the full body)         │
└─────────────────────────────────────────────────────────────┘
```

### Пример полного потока выполнения

Рассмотрим весь поток на конкретном примере:

```text
User: "Help me analyze this PDF file"

═══════════════════════════════════════════════════════════════

Step 1: LLM decision
────────────────
Claude finds the description of the "pdf" skill in the Skill list:
  description: "Analyze PDF document content, extract text, generate a summary"

═══════════════════════════════════════════════════════════════

Step 2: System intervention
────────────────
Claude Code executes:
  1. Read ~/.claude/skills/pdf/SKILL.md
  2. Generate a visible message: "The pdf skill is loading"
  3. Generate a hidden meta-message: the full SKILL.md content
  4. Modify session permissions: allowed-tools = ["Bash(pdftotext:*)"]

═══════════════════════════════════════════════════════════════

Step 3: LLM execution
────────────────
Now Claude's context contains:
  - the original user request
  - the PDF expert workflow instructions
  - access permission to the pdftotext tool

Claude executes:
  1. Use pdftotext to extract the PDF text
  2. Analyze the content structure
  3. Generate a summary report
  4. Present the result to the user

═══════════════════════════════════════════════════════════════

Step 4: Dispose after use
────────────────
After the task is completed, the full Skill content is removed from context
(only the conversation history remains, not the full Skill instruction)
```

### Ключевые проектные новшества

| Новшество | Традиционный подход | Подход Skills | Преимущество |
|--------|---------|------------|------|
| **Источник возможностей** | Зафиксирован в весах модели | Динамически загружаемые промпты | Расширяемо и обновляемо |
| **Эффективность токенов** | Все возможности всегда в памяти | Загрузка по запросу | Экономия 80%+ токенов |
| **Управление знаниями** | Разбросаны в истории диалога | Модульная файловая система | Версионируемо и доступно для обмена |
| **Жизненный цикл** | Постоянно занимает место | Освобождается после использования | Более чистый контекст |

### Академические основания

Проектирование Claude Skills опирается на следующие исследования:

| Область исследований | Знаковая работа | Применено здесь как |
|---------|---------|---------|
| **Обучение с подкреплением** | Voyager (2023) | Идея накопления библиотеки навыков |
| **Когнитивная архитектура** | ACT-R, Soar | Разделение процедурной и декларативной памяти |
| **Иерархическая политика** | Options Framework | Трёхуровневая прогрессивная загрузка |

**Ключевой сдвиг в мышлении**:

```text
Traditional: AI needs to remember everything
      ↓
Skills: AI knows where to find specialized knowledge
      ↓
Result: more like the thinking pattern of a human expert
```

### Связь со стандартом Agent Skills

Claude Skills следует [открытому стандарту Agent Skills](https://agentskills.io/), что означает:

- ✅ Кроссплатформенная совместимость: такие инструменты, как Cursor, Windsurf и Aider, тоже его поддерживают
- ✅ Единый формат файлов: стандартизированная структура `SKILL.md`
- ✅ Взаимодействие: Skills можно использовать совместно в разных инструментах

```text
Agent Skills standard defines:
├── Required: SKILL.md file (metadata + instructions)
├── Optional: scripts/ (executable code)
├── Optional: references/ (knowledge base documents)
└── Optional: assets/ (templates and resources)
```

### Итог: почему этот дизайн гениален?

1. **Отделяет возможности от модели**: специализированные знания больше не зависят от обучения модели и могут обновляться в любой момент через Markdown-файлы

2. **Предельная эффективность токенов**: трёхуровневый механизм загрузки гарантирует загрузку только необходимого содержимого

3. **Использует собственные сильные стороны LLM**: маршрутизация и сопоставление полностью полагаются на понимание языка у Claude, без дополнительного алгоритма

4. **Дружелюбен к разработчикам**: создание Skill требует лишь написания Markdown, а не программирования

5. **Композируемость**: Skills могут ссылаться друг на друга и комбинироваться, образуя сложные рабочие процессы

6. **Освобождается после использования**: автоматически очищается после завершения и сохраняет контекст свежим

---

### Итог

Skills — это ключ к превращению Claude Code из «универсального ассистента» в «командного эксперта».

С помощью Skills вы можете:
- стандартизировать рабочие процессы
- переиспользовать командные знания
- повышать эффективность взаимодействия
- сокращать повторяющиеся объяснения

Запомните: **если вы заметили, что повторяете одну и ту же инструкцию дважды, стоит задуматься о создании Skill**.

Теперь идите и создайте свой первый Skill.

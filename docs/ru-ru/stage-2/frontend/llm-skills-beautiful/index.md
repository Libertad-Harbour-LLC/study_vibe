# Делаем интерфейсы красивыми с помощью LLM и Skills: промпты и рабочие процессы с плагинами

В предыдущих главах вы уже узнали, как превращать дизайны в код с помощью ИИ-IDE и как использовать библиотеки компонентов для быстрого построения интерфейсов. Но вы, возможно, также заметили неловкую проблему: **даже при одном и том же требовании страницы, сгенерированные ИИ, часто ощущаются немного шаблонными**. Шрифт всегда Inter, цветовая палитра — какой-то заезженный фиолетовый градиент, макет — идеально симметричная сетка карточек, а страница оставляет сильное ощущение «сгенерировано ИИ».

На самом деле это не совсем вина ИИ. Настоящая проблема в том, что вы никогда не сказали ему, какой **стиль** вы хотели.

Представьте поход в парикмахерскую. Если вы скажете только «постригите меня», стилист, вероятно, выберет что-то безопасное, но забывающееся. Но если вы скажете «я хочу мягкую японскую многослойную волну, занавес-чёлку, длину до плеч и выраженную текстуру», вы с гораздо большей вероятностью получите именно то, что хотите.

То же самое верно и для ИИ. **Ему нужно чёткое эстетическое направление**, прежде чем он сможет сгенерировать красивый и отличительный интерфейс.

Эта глава знакомит с двумя практичными способами сделать сгенерированные ИИ интерфейсы намного лучше:

1. **Хорошо продуманные шаблоны промптов**, чтобы вы могли описать именно ту эстетику, которую хотите
2. **Плагины фронтенд-Skills**, чтобы ИИ автоматически загружал переиспользуемые правила дизайна

## Чему вы научитесь

1. Почему сгенерированные ИИ интерфейсы по умолчанию часто выглядят «обычными»
2. Как описать стиль дизайна через 5 измерений: типографика, цвет, макет, движение и детали
3. Как использовать 3 полезных плагина Skills для улучшения внешнего вида UI
4. Как генерировать более красивые интерфейсы через промпты + Skills в трёх практических сценариях

## 1. Почему сгенерированные ИИ интерфейсы по умолчанию выглядят «обычными»?

ИИ обучался на огромных объёмах фронтенд-кода, и большая часть этого кода использует безопасные, многократно повторяющиеся решения:

| Измерение | Выбор ИИ по умолчанию | Проблема |
| :--- | :--- | :--- |
| Типографика | Inter, Roboto, Arial | Слишком распространённые, без индивидуальности |
| Цвет | Фиолетовые градиенты, синие основные цвета | Заезжены в техническом мире, утомляют визуально |
| Макет | Симметричные сетки, стопки карточек | Предсказуемые, не запоминающиеся |
| Движение | Появления плавным затуханием, простые эффекты hover | Недостаточно отточенные, не хватает глубины |
| Фон | Сплошные цвета, простые градиенты | Плоский и малотекстурный |

Каждое из этих решений само по себе нормально. Но **как только каждая сгенерированная ИИ страница использует их все, они начинают ощущаться шаблонными и взаимозаменяемыми**.

> 💡 **Ключевая мысль**: ИИ умеет проектировать, но по умолчанию он тяготеет к **статистическому среднему**. Ваша задача — сказать ему, как уйти от этого среднего.

## 2. Способ первый: описывайте стиль через промпты

### 2.1 5 измерений стиля дизайна

Чтобы сгенерировать визуально сильный интерфейс, опишите то, что вы хотите, по этим пяти измерениям:

| Измерение | Что описывать | Примеры ключевых слов |
| :--- | :--- | :--- |
| **Типографика** | Акцидентный шрифт для заголовков, читабельный основной шрифт для текста | Space Grotesk, Playfair Display, JetBrains Mono |
| **Цвет** | Основной цвет + акцентный цвет, не распределённые равномерно | Основной `#4F46E5` + акцентный `#F59E0B` |
| **Макет** | Асимметрия, наложение, структура, ломающая сетку | Bento Grid, асимметричные секции, плавающие элементы |
| **Движение** | Осмысленные загрузка страницы и микровзаимодействия | ступенчатые появления, движение по триггеру прокрутки |
| **Детали** | Фоны, тени, границы, текстуры | зернистость, геометрия, градиентная сетка |

### 2.2 Видим разницу: шаблонный промпт vs эстетичный промпт

Сравним два промпта для одной и той же лендинг-страницы.

**Шаблонный промпт:**

```text
Please build a landing page for an AI writing assistant. Include a navbar, hero section, feature section, pricing section, and footer.
```

**Улучшенный промпт:**

```text
Please build a landing page for an AI writing assistant with the following style requirements:

**Aesthetic style: Neubrutalism**

**Typography:**
- Headings: Space Grotesk, weight 700-900
- Body: IBM Plex Sans, weight 400

**Colors:**
- Primary: #000000
- Accent: #FF6B00
- Background: #FFFDF0
- Borders: 3px solid black

**Layout:**
- Asymmetrical composition
- Bold black dividers between regions
- Cards with hard shadows (box-shadow: 8px 8px 0px #000)
- Strong contrast through generous whitespace

**Motion:**
- Elements pop in from below on page load
- Buttons shift upward by 2px on hover

**Details:**
- All corners set to 0px
- Buttons should feel strongly 3D
- Add subtle grain texture to the background
```

Второй промпт даёт ИИ достаточно направления, чтобы создать что-то смелое и запоминающееся, а не просто функциональное.

### 2.3 Список ресурсов с фронтенд-Skills для улучшения внешнего вида

Вам не нужно изобретать каждый промпт стиля с нуля. Вот несколько полезных ресурсов:

| Репозиторий | Что содержит | Звёзды | Ссылка |
|:---|:---|:---|:---|
| **ui-ux-pro-max-skill** | 57 стилей + 95 цветовых систем + 56 пар шрифтов | 10k+ | [GitHub](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill) |
| **antigravity-awesome-skills** | Помогает избегать шаблонных визуальных паттернов ИИ | - | [GitHub](https://github.com/sickn33/antigravity-awesome-skills) |
| **superdesigndev/superdesign** | Инструментарий разработки UI, нативный для ИИ | 4.7k | [GitHub](https://github.com/superdesigndev/superdesign) |
| **anthropics/skills/frontend-design** | Официальный Skill фронтенд-дизайна от Anthropic | - | [GitHub](https://github.com/anthropics/skills) |

> 💡 Больше промптов стилей смотрите в [Приложении: Шпаргалка по промптам стилей](#style-prompts).

### 2.5 Три надёжных шаблона стилей

Вот три проверенных шаблона, которые вы можете напрямую копировать и адаптировать.

#### Шаблон 1: Minimalism

```text
**Aesthetic style: Minimalism**

**Typography:**
- Headings: PP Neue Montreal, weight 500-700
- Body: Inter, weight 400

**Colors:**
- Primary: #FFFFFF
- Text: #1A1A1A
- Accent: #3B82F6, used sparingly

**Layout:**
- Large amounts of whitespace (minimum 64px section padding)
- One-column or two-column centered layout
- Use spacing instead of divider lines

**Motion:**
- Slow fade-in transitions (duration 600ms)
- Soft color transitions on hover

**Details:**
- Radius: 8px
- Shadows: subtle (0 4px 12px rgba(0,0,0,0.08))
- No decorative background elements
```

#### Шаблон 2: Glassmorphism

```text
**Aesthetic style: Glassmorphism**

**Typography:**
- Headings: Outfit, weight 600-800
- Body: Plus Jakarta Sans, weight 400-500

**Colors:**
- Background: gradient from #667eea to #764ba2
- Card background: rgba(255, 255, 255, 0.1)
- Text: #FFFFFF

**Layout:**
- Floating card design
- Slight overlap between cards

**Motion:**
- Cards appear in staggered sequence on page load
- Cards scale to 1.05x on hover

**Details:**
- Radius: 20px
- Blur: backdrop-blur-xl
- Border: 1px rgba(255, 255, 255, 0.2)
- Subtle glow effects
```

#### Шаблон 3: Bento Grid

```text
**Aesthetic style: Bento Grid**

**Typography:**
- Headings: SF Pro Display, weight 700
- Body: SF Pro Text, weight 400

**Colors:**
- Background: #F5F5F7
- Cards: #FFFFFF
- Accent: #0071E3

**Layout:**
- Grid-based composition with mixed card sizes
- 16px gaps
- 24px radius

**Motion:**
- Subtle hover lift
- Press feedback on click

**Details:**
- Large cards for primary content
- Smaller cards for secondary info
- Use icons to replace some text
- Clean shadows (0 4px 24px rgba(0,0,0,0.06))
```

## 3. Способ второй: используйте плагины Skills для автоматической загрузки правил дизайна

Писать промпты стилей вручную каждый раз утомительно. **Skills** — это переиспользуемые пакеты правил дизайна, которые можно установить один раз и применять многократно.

### 3.1 Три Skills, которые делают интерфейсы красивее

| Skill | Ключевая сильная сторона | Команда установки |
| :--- | :--- | :--- |
| **UI/UX Pro Max** | 67 стилей, 96 цветовых систем, 57 комбинаций шрифтов | `npm install -g uipro-cli && uipro init --ai claude` |
| **frontend-design** | Официальный Skill от Anthropic, сосредоточенный на избегании шаблонной эстетики ИИ | `npx skills add anthropics/skills/frontend-design` |
| **SuperDesign** | Плагин IDE, который генерирует несколько вариантов дизайна | Найдите `SuperDesign` в маркетплейсе расширений VS Code |

### 3.2 Установка UI/UX Pro Max

UI/UX Pro Max — один из самых полных пакетов Skills с правилами дизайна из доступных. Он включает:

- **67 UI-стилей**: Glassmorphism, Neumorphism, Brutalism, Bento Grid и другие
- **96 цветовых систем**: организованы по типу продукта, например SaaS, e-commerce и социальные приложения
- **57 пар шрифтов**: проверенные комбинации от профессиональных дизайнеров
- **100+ правил дизайна**: отступы, радиус скругления, тени и многое другое

**Шаги установки:**

```bash
# 1. Install the CLI globally
npm install -g uipro-cli

# 2. Initialize it for your AI tool
uipro init --ai claude
# or
uipro init --ai cursor
# or
uipro init --ai trae
```

После установки вы можете просто сказать:

```text
Use UI/UX Pro Max's Glassmorphism style to build me a landing page for an AI writing assistant.
```

ИИ затем автоматически применит соответствующие соглашения по типографике, цвету и макету.

### 3.3 Установка официального Skill `frontend-design` от Anthropic

Это официальный Skill фронтенд-дизайна от Anthropic, сосредоточенный конкретно на предотвращении шаблонного вывода ИИ:

```bash
# Run in Claude Code
npx skills add anthropics/skills/frontend-design
```

После установки ИИ будет склонен избегать:

- ❌ Inter, Roboto, Arial
- ❌ Фиолетовых градиентных фонов
- ❌ Симметричных сеточных макетов
- ❌ Чрезмерно мягких теней

И вместо этого будет тяготеть к:

- ✅ Более отличительным комбинациям шрифтов
- ✅ Сильным основным цветам с более резкими акцентами
- ✅ Асимметричным или накладывающимся макетам
- ✅ Более текстурным фонам, таким как зернистость и геометрия

## 4. Практический сценарий первый: редизайн лендинг-страницы с эстетичными промптами

Возьмём то, что мы только что изучили, и превратим очень обычную лендинг-страницу в гораздо более привлекательную.

### 4.1 Простая версия

Начните с того, что даёт вам ИИ при шаблонном промпте:

```text
Please build a landing page for a pet adoption platform. Include:
- a navbar (logo, links, sign-up button)
- a hero section (headline, subheadline, CTA button, pet image)
- a pet gallery (three pet cards)
- an about-us section
- a footer
```

Результат, вероятно, будет работать, но будет ощущаться довольно средним.

### 4.2 Улучшенная версия

Теперь добавьте указания по стилю:

```text
Please build a landing page for a pet adoption platform with the following design requirements:

**Aesthetic style: warm, soft, with a hand-drawn feeling**

**Typography:**
- Headings: Nunito, weight 700-800
- Body: Nunito, weight 400-600

**Colors:**
- Primary: #FFB347
- Secondary: #FFCCB3
- Background: #FFF8F0
- Text: #5D4037

**Layout:**
- Rounded cards (border-radius: 24px)
- Slightly tilted cards at different angles
- Floating and overlapping elements

**Motion:**
- Elements slide in from both sides on page load
- Pet cards slightly rotate on hover like an animal tilting its head
- Buttons bounce on hover

**Details:**
- Use 16-24px radii throughout
- Warm soft shadows (0 8px 24px rgba(255,179,71,0.3))
- Add paw-print decorations in the background
- Use irregular image crops via clip-path
- Use outline-style hand-drawn icons
```

Эта версия сгенерирует гораздо более тёплый, эмоционально убедительный интерфейс.

## 5. Практический сценарий второй: быстро генерируем дашборды с помощью Skills

Skills особенно полезны для административных дашбордов и внутренних систем, где многие страницы используют один и тот же язык дизайна.

### 5.1 Используем UI/UX Pro Max

```text
Use UI/UX Pro Max's Dashboard Dark style and build a dashboard page for a SaaS admin panel that includes:

**Top:** Four stats cards (users, active users, revenue, API calls)

**Middle:**
- Left: 7-day user growth line chart
- Right: subscription plan distribution pie chart

**Bottom:** a recent activity list showing time, user, and action
```

Skill автоматически применит согласованный вид дашборда:

- тёмно-серые фоны, такие как `#1A1A2E`
- высококонтрастные карточки вроде `#16213E`
- яркие цвета данных, такие как синий, зелёный и оранжевый
- плавающие карточки с лёгкими эффектами glassmorphism

### 5.2 Используем `frontend-design`

```text
Use the frontend-design skill and build a homepage for a personal blog. Make it distinctive and full of personality.
```

ИИ обычно выберет более конкретное эстетическое направление, такое как ретрофутуризм или редакционный журнальный стиль, и реализует его с помощью решений по типографике, цвету и макету, которые вырываются из шаблонных паттернов.

## 6. Практический сценарий третий: создайте собственный Skill дизайн-системы

Если у вашего продукта уже есть фиксированный стиль бренда, вы можете создать собственный Skill, чтобы каждая сгенерированная ИИ страница автоматически ему следовала.

### 6.1 Создаём файл Skill

Создайте `.claude/skills/my-brand/SKILL.md` в вашем проекте:

````markdown
---
name: my-brand
description: My project's custom design system, ensuring every UI follows a consistent visual language
---

# My Project Design System

## Brand Colors
- Primary: #6366F1 (Indigo 500)
- Secondary: #8B5CF6 (Violet 500)
- Success: #10B981
- Warning: #F59E0B
- Error: #EF4444
- Background: #F9FAFB
- Card: #FFFFFF

## Typography
- Headings: Plus Jakarta Sans
  - H1: 700, 48px
  - H2: 600, 36px
  - H3: 600, 24px
- Body: Inter
  - Body: 400, 16px
  - Small: 400, 14px

## Spacing
- Base unit: 4px
- Component padding: 8px / 12px / 16px
- Section spacing: 24px / 32px / 48px
- Page margin: 64px

## Radius
- Buttons: 8px
- Cards: 12px
- Inputs: 8px
- Modals: 16px

## Shadows
- Small: 0 1px 3px rgba(0,0,0,0.1)
- Medium: 0 4px 12px rgba(0,0,0,0.1)
- Large: 0 8px 24px rgba(0,0,0,0.12)

## Motion
- Transition duration: 150ms / 300ms
- Easing: cubic-bezier(0.4, 0, 0.2, 1)
- Hover effect: slight scale-up (scale-105)

## Forbidden Styles
- Do not use purple gradient backgrounds
- Do not use fonts other than Inter for body text
- Do not use radii larger than 16px
- Do not use pure black (#000000); use #1F2937 instead
````

### 6.2 Используем свой кастомный Skill

После создания вы можете просто сказать:

```text
Use my-brand skill to build me a user settings page.
```

ИИ автоматически применит ваши цвета, шрифты, систему отступов и другие ограничения дизайна.

## 7. Итоги

Есть два основных способа заставить ИИ генерировать более красивые интерфейсы:

| Метод | Сильная сторона | Слабая сторона | Лучше всего для |
| :--- | :--- | :--- | :--- |
| **Описания промптами** | Гибко, легко варьировать каждый раз | Нужно повторять | Разовые страницы, исследование стилей |
| **Плагины Skills** | Установить один раз, польза сохраняется | Требует настройки | Проекты со стабильной визуальной системой |

**Предлагаемый рабочий процесс вайб-кодинга:**

1. **Фаза исследования**: пробуйте разные стили промптов, чтобы найти эстетическое направление, которое вам нравится
2. **После выбора стиля**: установите соответствующий Skill, такой как UI/UX Pro Max или `frontend-design`
3. **Для продуктов, ориентированных на бренд**: постройте собственный Skill, чтобы весь проект оставался визуально согласованным

### Практика

Попробуйте одно из следующего:

1. Сделайте редизайн одного из ваших предыдущих проектов с более сильным визуальным стилем, используя указания по дизайну на основе промптов
2. Установите UI/UX Pro Max и используйте один из его стилей для генерации новой страницы
3. Создайте собственный Skill дизайн-системы с предпочитаемыми вами цветами и типографикой

---

## Приложение: шпаргалка по стилям

| Стиль | Ключевые слова | Лучше всего для | Пример |
| :--- | :--- | :--- | :--- |
| **Minimalism** | пустое пространство, монохромная палитра, чистота | премиальные продукты, портфолио | Apple |
| **Glassmorphism** | матовое стекло, размытие, градиенты | SaaS-лендинги, технические инструменты | macOS Big Sur |
| **Neubrutalism** | толстые границы, жёсткие тени, сплошные заливки | креативные бренды, арт-сайты | Brassius |
| **Bento Grid** | модульные карточки, коллажные макеты | дашборды, витрины функций | маркетинговые страницы Apple |
| **Retro Futurism** | неон, synthwave, тёмный контраст | игры, музыка, развлечения | эстетика Stranger Things |
| **Hand-drawn** | неровность, мягкость, иллюстративность | образование, продукты для детей | вайб Duolingo |
| **Editorial / Magazine** | крупная типографика, асимметрия, пустое пространство | блоги, контентные сайты | макеты в духе Medium |
| **Dark Luxury** | глубокие тона, золотые акценты, тонкие детали | премиальные и люксовые продукты | сайты люксовых брендов |

## Приложение: шпаргалка по установке Skills

```bash
# UI/UX Pro Max
npm install -g uipro-cli
uipro init --ai claude

# Anthropic frontend-design
npx skills add anthropics/skills/frontend-design

# Anthropic brand-guidelines
npx skills add anthropics/skills/brand-guidelines

# Check installed Skills in Claude Code
/help
```

## Приложение: рекомендуемые цветовые системы

| Палитра | Основной | Акцент | Фон | Настроение |
| :--- | :--- | :--- | :--- | :--- |
| **Sunset** | #F97316 | #FBBF24 | #FFF7ED | тёплое, энергичное |
| **Ocean** | #0EA5E9 | #06B6D4 | #F0F9FF | свежее, профессиональное |
| **Forest** | #10B981 | #34D399 | #ECFDF5 | природное, здоровое |
| **Berry** | #8B5CF6 | #EC4899 | #FAF5FF | романтичное, креативное |
| **Coffee** | #78350F | #D97706 | #FFFBEB | тёплое, ретро |
| **Monostone** | #6B7280 | #9CA3AF | #F9FAFB | нейтральное, профессиональное |

## Приложение: шпаргалка по промптам стилей {#style-prompts}

Полезные визуальные направления, которые вы можете попробовать, запрашивая более красивые фронтенд-интерфейсы:

### Категории стилей

| Стиль | Английские ключевые слова | Ключевые визуальные черты | Фрагмент промпта-примера |
|:---|:---|:---|:---|
| **Pop Art** | Pop Art | Смелые цветовые контрасты, чёрные контуры, текстуры полутонов | Pop art style website, bold colors and comic dots, vibrant |
| **Minimalism** | Minimalism | Много пустого пространства, очень мало орнамента | Minimalist web design, ample white space, geometric, serene |
| **Abstract Expressionism** | Abstract Expressionism | Энергичные мазки, выразительные брызги | Abstract expressionism background, dynamic paint splashes, emotional |
| **Retro** | Retro / Vintage | Винтажная типографика, состаренные текстуры, ретро-палитры | Retro 80s website design, neon grid and synthwave color palette |
| **Cyberpunk** | Cyberpunk | Контраст неона на тёмном, эффекты глитча | Cyberpunk UI, neon lights on dark background, glitch effects |
| **Neumorphism** | Neumorphism | Мягкие блики и тени, выпуклые или вдавленные поверхности | Neumorphism design style, soft shadows, clean and modern |
| **Generative Art** | Generative Art | Алгоритмические текучие формы и паттерны | Generative art background, flowing algorithmic patterns, digital |
| **Acid Graphics** | Acid Graphics | Металлическая текстура, эффекты стекла, хаотичная типографика | Acid graphics web layout, glass morphism, chaotic typography |
| **Immersive 3D** | Immersive 3D | Сильно пространственные сцены и глубина продукта | Immersive 3D website, interactive product model in space |

# Прокачайте свой интерфейс с помощью современных библиотек компонентов

В предыдущих уроках вы уже научились проектировать интерфейсы с помощью инструментов дизайна, превращать дизайны в код с помощью AI IDE и даже довели до конца целый фронтенд-проект. Но вы могли заметить одну проблему: когда вы создаёте кнопки, формы и модальные окна с нуля, они работают, но им всё ещё немного не хватает уровня «профессионального продукта» — стили недостаточно согласованы, детали взаимодействия недостаточно плавные, а адаптация под разные экраны превращается в мучение.

Именно эту проблему и решают **библиотеки компонентов**.

Библиотека компонентов — это набор заранее спроектированных и готовых строительных блоков UI. Кнопки, поля ввода, выпадающие меню, диалоги, таблицы… эти элементы интерфейса многократно встречаются почти в каждом продукте. Библиотека компонентов уже построила и отшлифовала их для вас через масштабное реальное использование. Вы просто комбинируете их, как кубики Lego, и можете быстро собрать интерфейс профессионального уровня.

## Чему вы научитесь

1. Поймёте, что такое фронтенд-библиотека компонентов и почему в современной разработке почти всегда используют такую библиотеку
2. Познакомитесь с четырьмя репрезентативными библиотеками компонентов и сценариями, в которых каждая из них лучше всего проявляет себя
3. На трёх практических сценариях (лендинг, страница продукта, админ-панель) научитесь делать Vibe Coding с помощью AI IDE + библиотек компонентов
4. Научитесь читать документацию библиотек компонентов, чтобы находить подходящие компоненты и правильно их использовать

## 1. Зачем нам нужны библиотеки компонентов?

Представьте, что вы обставляете дом. Вы могли бы сами сделать стул из необработанного дерева, но обычный подход — купить его в IKEA: хороший дизайн, стабильное качество, понятная инструкция, и вам остаётся только собрать его дома.

Библиотеки компонентов — это «IKEA» фронтенд-разработки. То, что они предоставляют, — это не мебель, а детали интерфейса:

| Всё кодим вручную | Используем библиотеку компонентов |
| :--- | :--- |
| Стили, взаимодействия и анимацию вы делаете сами | Готово из коробки, с отшлифованными стилями и взаимодействиями |
| Кнопки могут выглядеть по-разному на разных страницах | Единый глобальный стиль и автоматическая согласованность |
| Адаптация под мобильные/планшеты требует дополнительной работы | Большинство библиотек компонентов уже включают поддержку адаптивности |
| Доступность легко упустить | Профессиональные библиотеки уже учитывают навигацию с клавиатуры, экранные читалки и многое другое |
| Разработка медленнее | Разработка быстрее, больше фокуса на бизнес-логике |

Если коротко: **библиотеки компонентов позволяют тратить время на «что строить», а не на «как это нарисовать».**

### Увидеть наглядно: одно и то же требование, с библиотекой компонентов и без неё

Одних слов недостаточно для убедительности. В Trae мы можем дважды использовать практически одинаковое требование: один раз без указания библиотеки, другой — с ней. Затем сравнить сгенерированные результаты.

**Промпт 1: без библиотеки компонентов**

```text
Please help me build a data dashboard page for an AI writing assistant, including:
- a top title bar and an export button
- four statistic cards showing user count, active users, document count, and revenue, with trend changes
- one line chart and one pie chart
- a user list table with pagination
- a left navigation sidebar
```

Результат при прямом запуске в Trae:

<!-- TODO: Заменить на скриншот дашборда, сгенерированного в Trae без библиотеки компонентов -->
<!-- ![Дашборд, сгенерированный Trae (без библиотеки компонентов)](images/compare-without-lib.png) -->

**Промпт 2: используем библиотеку компонентов shadcn/ui**

```text
Please help me build a data dashboard page for an AI writing assistant using the shadcn/ui component library, including:
- a top title bar and an export button
- four statistic cards showing user count, active users, document count, and revenue, with trend changes
- one line chart and one pie chart
- a user list table with pagination
- a left navigation sidebar
```

Результат при прямом запуске в Trae:

<!-- TODO: Заменить на скриншот дашборда, сгенерированного в Trae с shadcn/ui -->
<!-- ![Дашборд, сгенерированный Trae (с shadcn/ui)](images/compare-with-lib.png) -->

Одно и то же требование. Единственное отличие — добавление `shadcn/ui + Tailwind CSS` в начало промпта. Но сгенерированный результат выходит на совершенно другой уровень по визуальной согласованности, детализации взаимодействия и общей отшлифованности. Это и есть «бесплатный апгрейд», который дают библиотеки компонентов, — вам нужно лишь добавить в промпт одно название библиотеки.

## 2. Знакомство с четырьмя ключевыми библиотеками компонентов

Библиотек компонентов очень много (полный список — в [приложении](#appendix-more-component-libraries)), но для начала вам нужно понять лишь эти четыре репрезентативные:

| Библиотека компонентов | Фреймворк | Позиционирование в одну строку | Сайт |
| :--- | :--- | :--- | :--- |
| [Ant Design](https://ant.design) | React | Создана Ant Group; де-факто стандарт для корпоративных бэк-офисных систем, с очень широким охватом компонентов | ant.design |
| [shadcn/ui](https://ui.shadcn.com) | React | Без установки большого npm-пакета; копируете код компонентов прямо в свой проект, построена на Tailwind CSS, с максимальной свободой кастомизации | ui.shadcn.com |
| [HeroUI](https://heroui.com) (ранее NextUI) | React | Красивые стили по умолчанию и плавная анимация; отлично подходит для требовательных к визуалу лендингов и витрин продуктов | heroui.com |
| [Material UI](https://mui.com) | React | Самая зрелая библиотека компонентов для React, реализующая Google Material Design, с самой развитой экосистемой | mui.com |

> У пользователей Vue тоже богатый выбор: [Element Plus](https://element-plus.org) (самая популярная в Китае), [Ant Design Vue](https://antdv.com), [Naive UI](https://www.naiveui.com) и т. д. См. [приложение](#appendix-more-component-libraries).

Разные библиотеки хороши в разных сценариях. Далее на трёх реальных сценариях разработки вы на практике почувствуете, как делать Vibe Coding с помощью AI IDE + библиотек компонентов.

Чтобы показать разные стили и сильные стороны, мы намеренно используем в каждом сценарии разную библиотеку. Но обратите внимание: **это лишь для того, чтобы вы увидели больше вариантов**. В реальных проектах вы вполне можете придерживаться одной наиболее понравившейся библиотеки. Например, если вам нравится shadcn/ui, вы можете использовать её и для лендингов, и для страниц продукта, и для админ-систем. Выберите ту, которая вам нравится визуально и с которой комфортно работать, — это важнее всего.

## 3. Сценарий первый: создаём лендинг продукта с помощью HeroUI

**Сценарий**: вы создали AI-ассистента для письма, и вам нужен красивый лендинг, чтобы показать возможности продукта и привлечь регистрации пользователей. Лендинг должен обладать сильным визуальным воздействием, плавной анимацией и хорошо выглядеть на мобильных устройствах.

**Почему HeroUI**: у HeroUI очень отшлифованные стили по умолчанию и плавные переходы, что делает её идеальной для витринных страниц, обращённых к пользователю.

### 3.1 Создаём проект

```bash
# Use the official HeroUI CLI
npx create-heroui-app@latest ai-writer-landing
cd ai-writer-landing
npm install
```

<!-- TODO: Заменить на скриншот главной страницы HeroUI или витрины компонентов -->
<!-- ![Главная страница библиотеки компонентов HeroUI](images/heroui-homepage.png) -->

### 3.2 Генерируем лендинг с помощью AI IDE

Откройте свою AI IDE (Cursor, Trae и т. д.) и введите:

```text
Please help me build a landing page for an AI writing assistant using the HeroUI component library:

**Page structure:**
1. Top navigation bar: put Logo and product name on the left, three links "Features", "Pricing", "About" on the right, plus a "Get Started" button
2. Hero section: main headline "Make AI your writing partner", subtitle introducing product value, two buttons "Try Free" and "View Demo", and a product screenshot below
3. Feature section: three-column cards introducing "Smart Continuation", "Style Adjustment", and "Multilingual Translation"; each card should have icon, title, and description
4. Pricing section: three pricing cards (Free, Pro, Team), with Pro highlighted as recommended
5. Bottom CTA: one compelling line of copy and a signup button
6. Footer: copyright information and social media links

**Design requirements:**
- modern and professional look
- support dark mode
- should also look good on mobile
```

<!-- TODO: Заменить на скриншот процесса генерации в AI IDE или сгенерированного результата -->
<!-- ![Лендинг на HeroUI, сгенерированный ИИ](images/heroui-landing-result.png) -->

### 3.3 Ключевые компоненты, которые использует ИИ

В коде, сгенерированном ИИ, вы увидите следующие компоненты HeroUI:

```jsx
import {
  Navbar, NavbarBrand, NavbarContent, NavbarItem,
  Button,
  Card, CardHeader, CardBody, CardFooter,
  Divider,
  Link,
  Chip
} from '@heroui/react'
```

Роль каждого компонента:

| Компонент | Назначение | Место на лендинге |
| :--- | :--- | :--- |
| `Navbar` | Верхняя панель навигации | Верх страницы, фиксированная |
| `Button` | Кнопки с несколькими вариантами и цветами | CTA-кнопки, кнопки навигации |
| `Card` | Контейнер-карточка | Карточки возможностей, карточки тарифов |
| `Chip` | Небольшой бейдж/метка | Маркеры «Рекомендуется», «Самый популярный» |
| `Divider` | Разделительная линия | Визуальное разделение между секциями |

### 3.4 Итерация и доработка

Первая сгенерированная версия может быть неидеальной. Продолжите диалог с ИИ:

```text
Please help me improve the landing page:

1. Add a gradient color to the main headline, from blue to purple
2. Add a hover lift animation to feature cards
3. Highlight the Pro pricing card with a border and a "Most Popular" badge
4. On mobile, change the nav bar to a hamburger menu (three horizontal lines)
```

<!-- TODO: Заменить на скриншот лендинга после итерации -->
<!-- ![Лендинг после итерации](images/heroui-landing-iterated.png) -->

> **Основная идея Vibe Coding**: вам не нужно запоминать API каждого компонента. Просто опишите желаемый эффект на естественном языке, и ИИ подберёт подходящие компоненты и реализацию. Если что-то получилось не идеально — продолжайте итерировать в диалоге.

## 4. Сценарий второй: создаём интерфейс продукта с помощью shadcn/ui

**Сценарий**: вашему AI-ассистенту для письма нужен основной интерфейс для авторизованного пользователя — список документов слева, редактор справа, панель инструментов сверху. Это функциональная страница продукта, которой нужен сильно кастомизируемый UI.

**Почему shadcn/ui**: shadcn/ui помещает код компонентов прямо в ваш проект, так что вы можете свободно менять любую деталь. Для глубоко кастомизированных интерфейсов продукта эта модель «владения кодом» наиболее гибкая.

<!-- TODO: Заменить на скриншот главной страницы shadcn/ui или витрины компонентов -->
<!-- ![Главная страница библиотеки компонентов shadcn/ui](images/shadcn-homepage.png) -->

### 4.1 Создаём проект

```bash
# Create a Next.js project
npx create-next-app@latest ai-writer-app --typescript --tailwind --app
cd ai-writer-app

# Initialize shadcn/ui
npx shadcn@latest init

# Add components on demand (do not install everything at once)
npx shadcn@latest add button card input sidebar sheet dialog
```

Уникальная особенность shadcn/ui: каждый раз, когда вы делаете `add` компонента, он копирует исходный код в директорию `components/ui/` вашего проекта. Вы можете открыть эти файлы и напрямую редактировать стили и поведение.

### 4.2 Генерируем интерфейс продукта с помощью AI IDE

```text
Please help me build the main interface of an AI writing assistant using the shadcn/ui component library:

**Overall layout:**
- Left side: a collapsible sidebar, about 280px wide:
  - Put a "New Document" button at the top
  - Below is a document list; each document shows title and last edited time
  - Right-click on a document should allow rename or delete
- Right side: main editor area, split into upper and lower parts:
  - Top toolbar: editable document title, word count, "AI Continue" button, and an "Export" dropdown
  - Bottom editor area: one large text input filling remaining space

**Interaction details:**
- After clicking "AI Continue", the button shows loading state, and AI-generated text appears at the bottom of the editor (shown character by character like a typewriter)
- On mobile, the sidebar becomes a drawer that slides in from the left
- The currently selected document should be highlighted
```

<!-- TODO: Заменить на скриншот интерфейса продукта на shadcn/ui, сгенерированного ИИ -->
<!-- ![Страница продукта, сгенерированная ИИ с shadcn/ui](images/shadcn-product-result.png) -->

### 4.3 Ключевые компоненты, которые использует ИИ

```tsx
import { Button } from '@/components/ui/button'
import { Input } from '@/components/ui/input'
import { Card, CardContent, CardHeader } from '@/components/ui/card'
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuTrigger
} from '@/components/ui/dropdown-menu'
import {
  Sheet,
  SheetContent,
  SheetTrigger
} from '@/components/ui/sheet'
import {
  Sidebar,
  SidebarContent,
  SidebarHeader
} from '@/components/ui/sidebar'
```

| Компонент | Назначение | Место на странице продукта |
| :--- | :--- | :--- |
| `Sidebar` | Сворачиваемая боковая панель | Список документов слева |
| `Sheet` | Мобильная выезжающая панель | Замена боковой панели на мобильных |
| `DropdownMenu` | Выпадающее меню | Кнопка «Export», контекстное меню |
| `Dialog` | Диалог | Подтверждение переименования и удаления |
| `Button` | Кнопка, поддерживает варианты и состояние загрузки | Различные кнопки действий |
| `Input` | Поле ввода | Редактирование заголовка документа |

### 4.4 Настраиваем стили компонентов

Преимущество shadcn/ui в том, что вы можете напрямую изменять исходный код компонентов. Например, если вы хотите больший радиус скругления углов кнопки:

```text
Please edit components/ui/button.tsx,
change all default button radius from rounded-md to rounded-xl,
and add a subtle shadow effect to the primary variant.
```

ИИ напрямую изменит файлы компонентов в вашем проекте, а не будет переопределять стили npm-пакета — в этом и заключается ценность «владения кодом» shadcn/ui.

<!-- TODO: Заменить на скриншот, показывающий, что исходные файлы компонентов shadcn/ui напрямую редактируются в проекте -->
<!-- ![Код компонентов shadcn/ui напрямую редактируется в проекте](images/shadcn-code-ownership.png) -->

## 5. Сценарий третий: создаём админ-панель с помощью Ant Design

**Сценарий**: после запуска вашего AI-ассистента для письма вам нужен административный бэкенд, чтобы просматривать данные пользователей, управлять содержимым документов и обрабатывать платные заказы. Суть админ-систем — отображение данных и эффективность операций.

**Почему Ant Design**: у Ant Design самый глубокий багаж в области бэк-офисных систем. Таблицы, формы, графики и другие бизнес-компоненты готовы из коробки, со множеством встроенных корпоративных паттернов взаимодействия (массовые действия, расширенные фильтры, экспорт данных и т. д.).

<!-- TODO: Заменить на скриншот главной страницы Ant Design или витрины Pro Components -->
<!-- ![Главная страница библиотеки компонентов Ant Design](images/antd-homepage.png) -->

### 5.1 Создаём проект

```bash
# Use Ant Design Pro scaffolding (built-in layout, routing, permissions)
npx create-umi@latest ai-writer-admin
# Choose the Ant Design Pro template
cd ai-writer-admin
npm install
```

Или начните с нуля:

```bash
npx create-react-app ai-writer-admin --template typescript
cd ai-writer-admin
npm install antd @ant-design/icons @ant-design/pro-components
```

### 5.2 Генерируем административный бэкенд с помощью AI IDE

```text
Please help me build an admin backend for an AI writing assistant using the Ant Design component library:

**Overall layout:**
- Left side menu: Dashboard, User Management, Document Management, Order Management, System Settings
- Top area shows breadcrumb navigation

**User Management page:**
- Top area has four stats cards: total users, today's new users, active users, paid users
- Search/filter area: search by username, select registration time range, filter by user status, plus "Search" and "Reset" buttons
- User table:
  - Show avatar, username, email, registration time, subscription plan (distinguished by different tag colors), status, operations
  - 20 rows per page, with pagination
  - Support batch selection, batch disable, or export
  - Operation column: view details, edit, disable (disable requires secondary confirmation)
- Clicking "View Details" opens a right-side drawer showing detailed user information and recent document list
```

<!-- TODO: Заменить на скриншот административного интерфейса на Ant Design, сгенерированного ИИ -->
<!-- ![Административный интерфейс на Ant Design, сгенерированный ИИ](images/antd-admin-result.png) -->

### 5.3 Ключевые компоненты, которые использует ИИ

```tsx
import { PageContainer, ProLayout } from '@ant-design/pro-components'
import { ProTable } from '@ant-design/pro-components'
import { StatisticCard } from '@ant-design/pro-components'
import {
  Button, Tag, Badge, Space, Drawer,
  Popconfirm, message, Modal
} from 'antd'
import {
  UserOutlined, SearchOutlined, ExportOutlined
} from '@ant-design/icons'
```

| Компонент | Назначение | Место в бэкенде |
| :--- | :--- | :--- |
| `ProLayout` | Общий каркас компоновки админки | Каркас страницы (меню + область контента) |
| `ProTable` | Продвинутая таблица со встроенным поиском, пагинацией, настройкой столбцов | Список пользователей, список документов, список заказов |
| `StatisticCard` | Карточка статистики данных | Дашборд и обзор в верхней части страницы |
| `Tag` / `Badge` | Метки статуса | Тарифные планы, статус пользователя |
| `Drawer` | Боковая выезжающая панель | Детали пользователя, формы редактирования |
| `Popconfirm` | Всплывающее подтверждение | Опасные действия, такие как удаление/блокировка |

### 5.4 Продолжаем итерировать: добавляем дашборд

```text
Please help me build a dashboard page:

1. Top four statistic cards: total users, total documents, today's API calls, monthly revenue. Each card should show value and period-over-period change (up or down)
2. Put two charts in the middle:
   - Left: user growth line chart for the last 7 days
   - Right: pie chart of subscription plan distribution
3. Bottom: recent operation log table, showing time, user, operation type, details

Use Ant Design components for layout, and you can use Ant Design Charts for charts.
```

<!-- TODO: Заменить на скриншот страницы дашборда -->
<!-- ![Результат страницы дашборда на Ant Design](images/antd-dashboard-result.png) -->

> **Совет по Vibe Coding для админ-систем**: структуры административных страниц относительно фиксированы (таблица + поиск + модальное окно), поэтому они идеально подходят для пакетной генерации с ИИ. Сначала можно попросить ИИ сгенерировать одну страницу «Управление пользователями» в качестве шаблона, а затем сказать: «На основе той же структуры сгенерируй страницу управления документами». ИИ переиспользует тот же паттерн компоновки.

## 6. Учимся читать документацию: «руководство» библиотек компонентов

В Vibe Coding ИИ пишет за вас большую часть кода. Но когда сгенерированный результат неверен или когда вы хотите тонко настроить поведение компонента, **чтение документации** — самый быстрый способ это решить.

Возьмём для примера Ant Design. URL его документации: `https://ant.design/components/overview-cn`

Стандартный рабочий процесс работы с документацией:

1. **Уточните потребность**: например, «мне нужен выбор строк в таблице».
2. **Поищите в документации**: найдите «Table» и зайдите на страницу компонента таблицы
3. **Посмотрите примеры**: у каждого компонента есть несколько живых примеров; найдите пример с «выбираемыми строками»
4. **Скопируйте код**: скопируйте код примера в свой проект
5. **Проверьте таблицу API**: внизу страницы найдите полную конфигурацию для `rowSelection`

> Вы также можете отправлять ссылки на документацию прямо в свою AI IDE: «Обратись к API rowSelection в https://ant.design/components/table-cn и помоги мне добавить массовый выбор в таблицу пользователей». Если дать ИИ ссылку на документацию, сгенерированный код будет точнее.

Быстрые ссылки на документацию каждой библиотеки:

| Библиотека компонентов | URL документации |
| :--- | :--- |
| Ant Design | `https://ant.design/components/overview-cn` |
| shadcn/ui | `https://ui.shadcn.com/docs/components` |
| HeroUI | `https://heroui.com/docs/components` |
| Material UI | `https://mui.com/material-ui/all-components/` |
| Element Plus | `https://element-plus.org/zh-CN/component/overview.html` |

## 7. Заключение

Три практических сценария охватывают самые распространённые потребности фронтенд-разработки:

| Сценарий | Рекомендуемая библиотека компонентов | Основные сильные стороны |
| :--- | :--- | :--- |
| Лендинг / витринная страница | HeroUI | Красивые стили по умолчанию, плавная анимация, сильное визуальное воздействие |
| Функциональная страница продукта | shadcn/ui | Полный контроль над кодом, гибкая глубокая кастомизация |
| Админ-система | Ant Design | Богатые бизнес-компоненты, таблицы/формы готовы из коробки |

Сводка по рабочему процессу Vibe Coding:

1. Выберите подходящую библиотеку компонентов в зависимости от сценария
2. Используйте AI IDE, чтобы описать желаемую структуру страницы и взаимодействия
3. ИИ генерирует первую версию кода, а вы просматриваете результат
4. Продолжайте итерировать на естественном языке
5. Когда застреваете на деталях — читайте документацию библиотеки компонентов

### Практика

Выберите один из сценариев ниже и выполните его с нуля с помощью AI IDE + библиотеки компонентов:

1. С помощью HeroUI создайте витринный лендинг для проекта, который вы сделали ранее (например, Hogwarts Portraits)
2. С помощью shadcn/ui создайте основной интерфейс для приложения заметок (боковая панель + редактор)
3. С помощью Ant Design создайте простой бэкенд управления контентом (список статей + форма создания статьи)

---

## Приложение: больше библиотек компонентов

Помимо четырёх ключевых библиотек, рассмотренных в основном тексте, во фронтенд-экосистеме есть множество отличных библиотек компонентов. Ниже они сгруппированы по фреймворкам, чтобы помочь вам выбрать в зависимости от потребностей проекта.

### Экосистема Vue

| Библиотека компонентов | Stars | Описание | Подходящие сценарии |
| :--- | :--- | :--- | :--- |
| [Element Plus](https://element-plus.org) | ~27k | Корпоративная библиотека компонентов для Vue 3 от команды Ele.me, наиболее широко используемая в Китае, отличная китайская экосистема | Бэк-офисные админ-системы |
| [Vuetify](https://vuetifyjs.com) | ~41k | Самая популярная библиотека компонентов Vue в стиле Material Design, 80+ компонентов, полная документация | Проекты в стиле Google-дизайна |
| [Ant Design Vue](https://antdv.com) | ~21k | Библиотека компонентов для Vue 3 на основе системы Ant Design, единая спецификация дизайна | Корпоративные бэк-офисные системы |
| [Naive UI](https://www.naiveui.com) | ~18k | Написана на TypeScript, с высокой настраиваемостью тем, без зависимости от CSS-препроцессоров | Проекты с уникальными потребностями в дизайне |
| [Quasar](https://quasar.dev) | ~27k | Одна кодовая база для SPA, SSR, PWA, мобильных и десктопных приложений | Кросс-платформенные проекты |
| [Vant](https://vant-ui.github.io/vant) | ~24k | Лёгкая мобильная библиотека компонентов от Youzan, покрывающая распространённые потребности e-commerce | Мобильные H5-страницы |
| [PrimeVue](https://primevue.org) | ~14k | 90+ компонентов, несколько тем (Material, Bootstrap и т. д.) | Проекты, которым нужны богатые компоненты и поддержка нескольких тем |
| [Arco Design Vue](https://arco.design/vue) | ~3k | Создана ByteDance, высокое качество компонентов, встроенная тёмная тема | Бэк-офисные продукты |
| [TDesign Vue Next](https://tdesign.tencent.com/vue-next) | ~2k | Создана Tencent, единый язык дизайна, охватывает распространённые десктопные сценарии | Проекты экосистемы Tencent или корпоративные проекты |

### Экосистема React

| Библиотека компонентов | Stars | Описание | Подходящие сценарии |
| :--- | :--- | :--- | :--- |
| [Material UI (MUI)](https://mui.com) | ~95k | Давно зарекомендовавшая себя реализация Google Material Design, наиболее полный набор компонентов, самая зрелая экосистема | Быстрое создание корпоративных приложений |
| [Ant Design](https://ant.design) | ~94k | Создана Ant Group, множество качественных бизнес-компонентов, доминирует среди китайских разработчиков | Корпоративные бэк-офисные системы |
| [shadcn/ui](https://ui.shadcn.com) | ~83k | Копирование кода в проект вместо установки через npm, на основе Radix UI + Tailwind CSS, полностью управляемая | Сильно кастомизированные проекты |
| [Chakra UI](https://chakra-ui.com) | ~39k | Фокус на удобстве для разработчика, лаконичный API, встроенная поддержка доступности | Быстрая разработка прототипов |
| [Mantine](https://mantine.dev) | ~28k | 100+ компонентов и 50+ хуков, включая продвинутые компоненты вроде выбора даты и редакторов форматированного текста | Команды, которым нужно цельное решение «всё из коробки» |
| [Headless UI](https://headlessui.com) | ~27k | Библиотека компонентов без стилей от Tailwind Labs, поддерживает и React, и Vue | Лучше всего с Tailwind CSS |
| [HeroUI](https://heroui.com) | ~24k | На основе Tailwind CSS + React Aria, красиво по умолчанию, плавная анимация | Проекты, нацеленные на визуальное качество |
| [Radix UI](https://www.radix-ui.com) | ~17k | Библиотека примитивных компонентов без стилей, сфокусированная на доступности и поведении; базовый слой shadcn/ui | Создание собственных дизайн-систем |

#### Экосистема расширений shadcn/ui

Помимо универсальных библиотек компонентов выше, экосистема shadcn/ui также породила множество библиотек-расширений, основанных на той же философии и предлагающих дифференцированный выбор для конкретных сценариев. Эти расширения тоже используют модель «копирования кода в проект», давая разработчикам полный контроль над исходным кодом.

| Библиотека компонентов | Описание | Подходящие сценарии |
| :--- | :--- | :--- |
| [Aceternity UI](https://ui.aceternity.com) | 200+ компонентов продакшен-уровня, включая светящиеся карточки, градиентный текст, 3D-планету и другие характерные визуальные компоненты | Высокоотшлифованные лендинги, SaaS-продукты |
| [Tailark UI](https://tailark.com) | Коллекция блоков для маркетинговых сайтов, включая частые модули вроде витрин продуктов, отзывов и CTA-кнопок | Маркетинговые лендинги, сайты продуктов |
| [UI Tripled](https://ui.tripled.work) | Компоненты с динамическим взаимодействием на основе Framer Motion, включая модальные окна, навигацию, анимацию карточек | Креативные инструменты, личные портфолио |
| [Neobrutalism UI](https://neobrutalism.dev) | Стиль необрутализма с толстыми линиями, высоким контрастом и яркими цветами | Персонализированные брендовые сайты, креативные проекты |
| [REUI](https://reui.io) | 967+ паттернов композиции компонентов из реальных бизнес-сценариев | Корпоративные бэкенды, сложные формы |
| [Cult UI](https://cult-ui.com) | Более изящные взаимодействия и визуальная отшлифованность, включая составные компоненты вроде таблиц данных и панелей фильтров | Качественные коммерческие продукты |
| [Kibo UI](https://kibo-ui.com) | Продвинутые бизнес-компоненты, такие как выбор цвета, редактор форматированного текста, загрузка файлов | Админ-системы, инструментальные продукты |
| [Kokonut UI](https://kokonutui.com) | 100+ компонентов + 7+ готовых шаблонов, свежий и минималистичный стиль | SaaS-сайты, блоги, e-commerce |
| [Commerce UI](https://ui.stackzero.co) | Специализирована под сценарии e-commerce, включая карточки товаров, корзину, формы оформления заказа | E-commerce-платформы |
| [shadcnblocks](https://shadcnblocks.com) | 1373 UI-блока + 13 готовых шаблонов, наиболее полный набор ресурсов | Все сценарии |
| [Shoogle](https://shoogle.dev) | Агрегирующая поисковая платформа для экосистемы shadcn/ui | Быстрый поиск ресурсов |
| [Discover All Shadcn](https://allshadcn.com) | Агрегирующая навигация по ресурсам | Быстрый поиск ресурсов |

> **Зачем выбирать расширения shadcn/ui?** Эти расширения наследуют философию «владения кодом» shadcn/ui, добавляя при этом глубокую кастомизацию под конкретные сценарии. В эпоху Vibe Coding они помогают быстро находить компоненты, соответствующие вашим целям дизайна, уходить от однообразных мейнстримных UI-паттернов и создавать более дифференцированные продукты.

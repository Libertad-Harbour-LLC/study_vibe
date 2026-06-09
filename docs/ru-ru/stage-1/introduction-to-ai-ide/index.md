---
title: 'Начальный уровень 2: Изучаем инструменты для AI-кодинга'
description: 'Переходим от веб-AI-кодинга к локальной разработке: разбираемся в различии между IDE и AI IDE, создаём игру «Змейка» в Trae и осваиваем практические приёмы взаимодействия с AI.'
---

# Начальный уровень 2: Изучаем инструменты для AI-программирования

## Обзор главы

<script setup>
const duration = 'Около <strong>1 дня</strong>, можно проходить в несколько подходов'
</script>

<ChapterIntroduction :duration="duration" :tags="['Настройка локальной среды разработки', 'IDE против AI IDE', 'Приёмы эффективной разработки']" coreOutput="1 оригинальная игра, которую вы создадите" expectedOutput="Создано с помощью Trae">

Ранее мы попробовали AI-программирование на z.ai, но у веб-версии много ограничений — вы **не можете сохранять свою работу в любой момент**, вам **сложно управлять файлами** и вы **не можете работать со сложными проектами**. Эта глава поможет вам перенести среду разработки на свой собственный компьютер, чтобы вы могли **по-настоящему создавать вещи самостоятельно**.

Сначала мы проясним, **в чём разница между IDE и AI IDE** и почему последняя может **удвоить вашу эффективность**. Затем мы **шаг за шагом проведём вас** через создание игры «Змейка» локально с помощью Trae, охватив **полный рабочий процесс** от установки до запуска. В конце мы поделимся несколькими **практическими советами** по общению с AI, чтобы вы могли избежать распространённых ошибок.

После прохождения этой главы вы **освоите рабочий процесс разработки, похожий на тот, что используют профессиональные программисты**.

::: tip 💡 Продвинутый совет
Если у вас есть некоторый опыт программирования и вы хотите использовать более мощные инструменты с самого начала, вы можете обратиться к разделу [Современные CLI-инструменты для кодинга](../../stage-2/backend/modern-cli/), чтобы вести разработку из командной строки.
:::

</ChapterIntroduction>

<div style="margin: 50px 0;">
  <ClientOnly>
    <StepBar :active="0" :items="[
      { title: 'Понимание среды', description: 'IDE против AI IDE' },
      { title: 'Практика', description: 'Создаём «Змейку» в Trae' },
      { title: 'Глубокое изучение инструмента', description: 'Изучаем интерфейс IDE' },
      { title: 'Навыки общения', description: 'Эффективно общаемся с AI' }
    ]" />
  </ClientOnly>
</div>

## 1. Какая среда и какие инструменты нужны, чтобы писать код

### 1.1 Смена мышления: когда сомневаешься — сначала спроси AI

Прежде чем мы представим различные среды и инструменты, вот важное напоминание: вам нужно **изменить свои привычки мышления**.

При традиционном обучении программированию, если вам нужно установить Python, настроить Conda или исправить ошибку при установке npm, вы обычно открывали бы поисковик, находили туториал и выполняли шаги один за другим. Если по пути возникала ошибка, вы искали бы её текст и пробовали снова и снова.

Неправильно! ❌

В эпоху AI, особенно при использовании AI IDE, запомните один ключевой принцип: **для любой задачи вы можете сначала спросить AI или даже поручить ему сделать это за вас.**

- **Не знаете, как настроить среду?** Просто спросите AI в боковой панели: «Я хочу писать на Python. Пожалуйста, проверь, установлен ли Python, и если нет — установи его для меня.»
- **Сеть зависла?** Если установка зависимостей бесконечно крутится или выдаёт ошибки, просто скиньте ошибку AI: «Загрузка не удалась. Это проблема с сетью? Можешь помочь переключиться на другое зеркало?»
- **Не можете вспомнить команды?** Не нужно запоминать команды Git или Conda. Просто скажите AI: «Помоги мне создать новое виртуальное окружение под названием demo.»

### 1.2 Зачем нужны среда и инструменты

Переход от «попыток написать несколько строк кода» к «созданию проекта, который можно поддерживать долгое время» требует совершенно других сред и инструментов.

Теоретически вы могли бы писать код во встроенном системном Блокноте, но проблемы быстро дадут о себе знать:

- **Весь код — это обычный чёрный текст** — ключевые слова, строки и комментарии смешаны вместе, из-за чего сложно увидеть структуру с первого взгляда
- **Нет умных подсказок** — приходится полностью набирать каждое слово вручную, а одна опечатка означает многократную перепроверку кода
- **Файлы превращаются в хаос** — приходится переключаться туда-сюда между десятками файлов, часто не находя нужную строку для редактирования
- **Отладка превращается в гадание** — когда программа падает, вы не знаете, что пошло не так, и можете лишь построчно добавлять операторы вывода

Именно поэтому вам нужна IDE (интегрированная среда разработки). Она отображает код разными цветами, даёт автоподсказки по мере набора, организует файлы по проектам и позволяет шаг за шагом отслеживать ошибки — делая разработку эффективнее и менее подверженной ошибкам.

## 2. Что такое IDE и зачем она нужна

::: info Совет перед чтением
Если вы ещё не знакомы с тем, что такое IDE и за что отвечает каждый элемент интерфейса, мы рекомендуем сначала прочитать [Основы IDE](/ru-ru/appendix/2-development-tools/ide-basics), чтобы изучить базовые понятия и общие функции.
:::

На заре программирования всё, что нам было нужно, — это простой текстовый редактор и обработчик языка. Но по мере усложнения проектов разработчикам срочно понадобился инструмент, который мог бы эффективно управлять файлами, поддерживать подсветку синтаксиса и обеспечивать отладку — так и родилась интегрированная среда разработки (IDE).

Вы можете представить IDE как программу, специально созданную для того, чтобы «редактировать, управлять, запускать и отлаживать» код. Ранние IDE выглядели очень «примитивно» и управлялись почти полностью с клавиатуры.

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/image1.png)![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/image2.png)

Терминальный интерфейс — источник изображения: https://en.wikipedia.org/wiki/File:Emacs-screenshot.png

Известные и зрелые «встроенные IDE», такие как `Vim`, обычно используются для работы с удалёнными серверами.

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/image3.png)

Для большей эффективности нам нужны современные IDE, поддерживающие работу мышью, в которые обычно входят:

- **Редактор исходного кода**: подсветка синтаксиса, автодополнение.
- **Инструменты сборки и запуска**: встроенный компилятор/интерпретатор.
- **Отладчик**: отладка по точкам останова, просмотр значений переменных.

Современные IDE часто также включают встроенные инструменты вроде Git. Самая популярная — **[Visual Studio Code (VS Code)](https://code.visualstudio.com/)** от Microsoft, которая легковесна и расширяема. Хотя существуют и профессиональные IDE, такие как набор от JetBrains, VS Code наиболее дружелюбна к новичкам.

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/image4.png)

Ключевая философия VS Code — «всё является плагином». Благодаря системе плагинов она поддерживает различные языки — установите плагин Python, и она станет Python IDE, установите плагин C++, и она станет C++ IDE. Без плагинов это просто продвинутый текстовый редактор.

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/image5.png)

Вы даже можете использовать её для редактирования документов Markdown.

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/image6.png)

Короче говоря, IDE — это набор инструментов, который помогает разработчикам эффективно писать код и запускать программы.

Более подробные объяснения смотрите в [разделе визуализации виртуальной IDE в Приложении](/ru-ru/appendix/2-development-tools/ide-basics).

## 3. Чем AI IDE отличается от обычной IDE

Обычная IDE (например, исходная VS Code) — это по сути «ящик с инструментами»:
вы можете открывать проекты, писать код, запускать и отлаживать, устанавливать плагины — но при условии, что вы сами знаете, что нужно делать и как это делать:

- Когда возникает ошибка, вы сами читаете сообщение и выясняете, в какой строке проблема;
- Когда вы хотите добавить новую страницу или эндпоинт API, вы сами находите нужный файл и пишете код;
- Когда вы хотите настроить среду или собрать проект, вы сами ищете документацию и выполняете шаги.

Но в AI IDE вы можете напрямую использовать большую языковую модель, чтобы она помогала вам писать код и изменять файлы:

- Просто скажите «сделай страницу входа», и она сначала сгенерирует базовую структуру кода;
- Скиньте ей сообщение об ошибке и связанный код, и пусть она проанализирует причину и предложит исправления;
- После вашего подтверждения позвольте ей автоматически создавать файлы, массово редактировать код и выполнять рутинную работу между файлами.

Например, вы можете выделить фрагмент кода и попросить «отрефакторь это» или «добавь комментарии». Вы также можете спросить в боковой панели «Как устроен этот проект?» и указать область контекста с помощью `@имяфайла` или `@весь проект`, выполнив утомительные операции по созданию файлов, написанию кода и запуску одной фразой.

В последней версии VS Code ассистент на основе большой языковой модели уже встроен. Вы можете вести с моделью разговор обо всей кодовой базе, конкретном файле или даже конкретной функции. Вы также можете использовать её так же, как инструменты автокодинга, которыми вы пользовались в вебе — отправляйте свои требования как промпты встроенному агенту-кодеру, и пусть он автоматически реализует нужные вам функции, создаёт файлы, изменяет код, настраивает среду и многое другое.

Вы можете скачать и установить VS Code, нажать на значок боковой панели в правом верхнем углу и открыть область AI-функций, чтобы испытать эти возможности.

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/image7.png)

Однако VS Code — не та IDE, у которой самые сильные AI-возможности. Для сценариев, требующих интенсивного AI-кодинга, мы часто хотим использовать «более умные и эффективные» инструменты — хорошая AI IDE может существенно сэкономить время на написании кода и исправлении багов. Ниже мы представим несколько популярных AI IDE. Вы можете выбрать любую AI IDE исходя из личных предпочтений.

Поскольку VS Code имеет открытый исходный код (любой может скачать исходники и скомпилировать их самостоятельно), подавляющее большинство AI IDE на рынке сегодня построены на основе VS Code. Поэтому вам не нужно беспокоиться о том, что придётся «изучать много разных IDE» — **если вы знакомы с основами VS Code**, переход на эти AI IDE не потребует начинать с нуля.

В целом различия между AI IDE сводятся в основном к четырём аспектам: цена; доступные типы моделей (некоторые продвинутые модели могут быть ограничены в определённых регионах); возможности агента (насколько он умён и способен в помощи с кодингом); скорость и производительность. Вы можете выбирать на основе собственных результатов тестирования — лучший инструмент тот, что лучше работает именно для вас.

> Типичные AI IDE обычно обладают следующими ключевыми возможностями:
>
> - Умная генерация и автодополнение кода: в традиционных IDE мы обычно набираем несколько символов, чтобы автоматически дополнить имена переменных или функций. В современных AI IDE вы можете написать несколько строк псевдокода или просто описать свои требования, и IDE автоматически дополнит всю логику или даже сгенерирует крупные блоки кода по инструкциям.
> - Понимание кода и ответы на вопросы: IDE может понимать и отвечать на вопросы о конкретном фрагменте кода, файле или даже структуре каталогов всего проекта.
> - Рефакторинг и оптимизация кода: IDE может переписывать или оптимизировать логику реализации указанных фрагментов кода в соответствии с вашим намерением.
> - Автоматическая генерация тестов: IDE может автоматически генерировать тестовый код для разных функций и модулей, что упрощает целевое тестирование.
> - Выполнение задач в стиле агента: умные агенты могут автоматически генерировать, собирать, устанавливать, запускать и изменять код, частично заменяя работу младших инженеров-программистов во многих задачах.

::: details Antigravity

### [Antigravity](https://antigravity.google/)

Antigravity — это совершенно новая AI IDE, выпущенная Google в ноябре 2025 года вместе с Gemini 3, использующая модель разработки «Agent-First». В отличие от традиционного AI-кодинга, Antigravity делает AI-агента «активным исполнителем», способным напрямую управлять редактором, терминалом, браузером и другими инструментами, беря на себя больше работы по «выполнению», «планированию» и «проверке». Разработчикам нужно лишь выразить намерение высокого уровня, и агент автоматически разобьёт задачи, составит планы, выполнит код, запустит тесты и сгенерирует результаты. Поддерживается переключение между несколькими моделями, включая Gemini 3 Pro, Claude Sonnet 4.5 и другие. В настоящее время доступна как публичная превью-версия и поддерживает Windows, macOS и Linux.
:::

::: details Trae

### [Trae](https://www.trae.ai/)

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/image8.png)

Trae — это AI-ассистент для программирования, разработанный ByteDance, который поддерживает более 100 языков программирования и может интегрироваться в основные IDE. В число его функций входят: генерация кода из естественного языка, автоматическая отладка и преобразование дизайн-макетов в компоненты React/Vue. После обновления в августе 2025 года в Trae добавились умный импорт зависимостей, предложения по переименованию, управление чек-листами задач и многое другое. Режим SOLO также начал поддерживать генерацию серверного кода и редактирование документов технической архитектуры.
:::

::: details Cursor

### [Cursor](https://cursor.com/)

Cursor — это AI-редактор кода, разработанный Anysphere, построенный на кастомизированной VS Code, с оптимизациями, ориентированными на крупные кодовые базы и сценарии совместной работы с несколькими файлами. Он поддерживает такие модели, как GPT-4o и Claude 3.7. Режим Claude Max, представленный в 2025 году, способен работать с проектами на миллионы строк кода. В версии Pro убраны ограничения на количество запросов, что делает её идеальной для сложных корпоративных проектов.

В настоящее время Cursor, пожалуй, одна из лучших AI IDE с графическим интерфейсом по совокупному опыту использования, с большой пользовательской базой и частыми обновлениями функций. Его главный недостаток — более высокая цена: версия Pro стоит около $20 в месяц.

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/image9.png)
:::

::: details Qoder

### [Qoder](https://qoder.com/)

Qoder — это AI IDE от Alibaba, которая делает акцент на «прозрачном взаимодействии» и «улучшенных возможностях инженерии контекста». Она поддерживает разбиение задач на несколько шагов через Action Flow и в реальном времени отслеживает выполнение AI. Она также поддерживает динамическую маршрутизацию между несколькими моделями и управление конечным автоматом состояний задач, что делает её идеальной для управления архитектурой в средних и крупных проектах и «обратного инжиниринга» при анализе устаревших систем.

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/image10.png)
:::

::: details CodeBuddy

### [CodeBuddy](https://www.codebuddy.com/)

CodeBuddy — это AI-инструмент для программирования от Tencent Cloud, который делает акцент на поддержке команд на китайском языке и возможностях соответствия требованиям корпоративного уровня. Он предлагает автодополнение кода, пакетный код-ревью и переключение между несколькими моделями. Его агент Craft может выполнять генерацию кода для нескольких файлов и интеграцию API. Корпоративная версия поддерживает приватное развёртывание и прошла сертификацию безопасности 3-го уровня, что делает её подходящей для отраслей с высокими требованиями к безопасности данных, таких как финансы и здравоохранение.

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/image11.png)
:::

::: details VS Code + Cline

### VS Code + [Cline](https://cline.bot/)

Cline — это плагин AI-агента для программирования для VS Code (Visual Studio Code), который может гибко переключаться между разными большими моделями за счёт настройки разных эндпоинтов API. Cline поддерживает мультимодальный ввод, расширения инструментов MCP и мониторинг затрат, причём все операции требуют подтверждения пользователя перед выполнением. Он идеален для быстрой проверки идей или интеграции с существующими рабочими процессами разработки. Базовые функции бесплатны, а корпоративная версия поддерживает развёртывание моделей в приватных средах.

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/image13.png)

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/image14.png)
:::

::: details Kiro

### [Kiro](https://kiro.dev/)

Kiro — это AI IDE для программирования от AWS (Amazon Web Services), глубоко интегрированная с Amazon Bedrock и экосистемой облачных сервисов AWS. Она поддерживает несколько больших моделей, включая Claude и Nova, что делает её особенно подходящей для сценариев разработки, требующих тесной интеграции с облачными сервисами AWS. Kiro предоставляет умную генерацию кода, автоматизированное тестирование и бесшовную интеграцию с ресурсами AWS (такими как Lambda, S3, DynamoDB), предлагая уникальные преимущества для разработки облачных приложений.

> **Примечание**: если вы хотите использовать модели Anthropic Claude, вам нужно будет использовать в качестве IDE Cursor, Kiro или Antigravity. У этих IDE есть официальные партнёрства или глубокие интеграции с Anthropic, что обеспечивает более стабильный и полноценный опыт работы с моделями Claude.
:::

<div style="margin: 50px 0;">
  <ClientOnly>
    <StepBar :active="1" :items="[
      { title: 'Понимание среды', description: 'IDE против AI IDE' },
      { title: 'Практика', description: 'Создаём «Змейку» в Trae' },
      { title: 'Глубокое изучение инструмента', description: 'Изучаем интерфейс IDE' },
      { title: 'Навыки общения', description: 'Эффективно общаемся с AI' }
    ]" />
  </ClientOnly>
</div>

## 4. Практика: создаём игру «Змейка» локально с помощью AI IDE

Предыдущие разделы были в основном о «концепциях» и «различиях». В этом разделе мы превратим абстрактные концепции в конкретные действия через полноценное практическое упражнение: **создаём новую пустую папку -> открываем её в AI IDE -> общаемся в боковой панели и поручаем ей создать игру «Змейка» с нуля на React.** Здесь мы будем использовать Trae в качестве примера, поэтому сначала нам нужно установить его и понять, что такое Trae.

::: tip 💡 Быстрый совет: бесшовный переход из веба на локальную машину
Если вы ранее разрабатывали проекты на z.ai или других веб-платформах AI-программирования, вы можете скачать код напрямую на свою локальную машину и открыть его в AI IDE, чтобы продолжить разработку. Так вы сохраните свою предыдущую работу и при этом получите более мощную AI-помощь локальной IDE.

Шаги просты:
1. Нажмите кнопку загрузки на платформах вроде z.ai, чтобы сохранить проект локально
2. Распакуйте и откройте папку в AI IDE, такой как Trae/Cursor
3. Продолжайте общаться с AI в боковой панели, чтобы итеративно улучшать свой проект
:::

### 4.1 Подготовка: установите Trae и узнайте о нём

#### 4.1.1 Что такое Trae

Полное название Trae можно понимать как «The Real AI Engineer» (настоящий AI-инженер). Это адаптивная интегрированная среда разработки с AI (IDE), разработанная ByteDance. Она построена на основе популярной VS Code, а значит, если вы уже знакомы с VS Code, компоновка интерфейса и базовые операции Trae покажутся вам очень знакомыми и удобными.

Ключевая цель Trae — быть «умным партнёром по программированию» для разработчика. Благодаря глубокой интеграции AI он может автоматически выполнять большой объём повторяющейся работы, обеспечивая вам более интуитивный и эффективный опыт разработки. Это не просто «инструмент автодополнения кода» — он стремится помогать на протяжении всего рабочего процесса разработки: от создания проектов, написания кода, отладки, тестирования до развёртывания.

#### 4.1.2 Установка Trae

Trae существует в международной версии и в версии для Китая. Международная версия требует доступа к зарубежным сетям, но позволяет использовать новейшие зарубежные модели вроде GPT-5. Версия для Китая в основном поддерживает новейшие отечественные большие модели, такие как GLM, Qwen, Kimi и другие.

Загрузка международной версии: https://www.trae.ai/
Загрузка версии для Китая: https://www.trae.cn/

##### Цены и варианты использования Trae

::: info 💡 Советы по выбору версии (для новичков рекомендуется версия CN)
- **Для новичков мы настоятельно рекомендуем скачать версию для Китая (версия CN, trae.cn)** — в настоящее время она обеспечивает лучший общий опыт и бесплатна в использовании, без необходимости в зарубежной сети
- Если вам нужно использовать зарубежные модели вроде GPT-5 и ваши сетевые условия это позволяют, вы можете выбрать международную версию
- Если у вас уже есть API Key стороннего поставщика моделей, подключение сторонних моделей даёт гибкий контроль над затратами
:::

> 💡 **В настоящее время рекомендуется: используйте бесплатные модели OpenRouter для тестирования**
>
> На момент написания этого туториала (2026-02-12) вы всё ещё можете бесплатно попробовать модели StepFun. См. раздел 4.2 ниже о том, как подключить модель `stepfun/step-3.5-flash:free`.

Что касается затрат и вариантов использования Trae, вот несколько вариантов на выбор:

- **Версия для Китая CN (настоятельно рекомендуется)**: базовое использование бесплатно, и в настоящее время она обеспечивает лучший общий опыт, чем международная версия, — идеальна для новичков. Из-за большого числа пользователей вам иногда может потребоваться подождать в очереди.
- **Международная версия**: подписка стоит около $3 в месяц, давая доступ к зарубежным моделям вроде GPT-5, но требует доступа к зарубежной сети.
- **Интеграция сторонних моделей**: если у вас уже есть Token API от отечественного поставщика больших моделей (например, DeepSeek, Tongyi Qianwen, Kimi и т. д.), вы можете подключить эти API через настройку сторонних моделей в Trae. Крупные облачные провайдеры (такие как Alibaba Cloud, Tencent Cloud, Baidu Cloud и др.) обычно предлагают подписки Coding Plan, которые позволяют использовать их API больших моделей по более выгодным ценам. Так вы можете свободно выбирать предпочитаемую модель, контролируя при этом затраты.

Мы рекомендуем новичкам начать с бесплатной версии для Китая CN (загрузка: https://www.trae.cn/), которая в настоящее время предлагает лучший опыт и полностью бесплатна. Если вы столкнётесь с проблемами очередей или вам понадобится более стабильный сервис, рассмотрите подключение сторонней модели и покупку соответствующего Coding Plan облачного провайдера.

#### 4.1.3 Trae Interface Overview

In terms of interface design, Trae is very similar to the VS Code we use daily: the same classic three-column layout with a file explorer on the left, an editing area in the center, and an extension panel on the right.

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/image17.png)

The sidebar on the right is the Copilot interaction window, which can also be thought of as the Agent window. If you can't see it right away, click the sidebar icon in the top-right corner of Trae to open it.

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/image18.png)

After opening the sidebar, you'll see a `Builder` option — this is the Agent mode. Simply put, it's like a "local version" of z.ai that can operate your local environment, install runtime environments, open web pages, and more.

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/image19.png)

After clicking "Builder," you'll see "Chat" mode and "Builder with MCP" mode:

- **Chat Mode**: Primarily used for chatting about the code in your current folder, or as a general chat model. (You can open a folder through the "File" menu in the top-left corner and edit within that folder. In this case, any files Builder creates or modifies will only happen inside this folder.)
- **Builder with MCP Mode**: Provides the Agent with more available tools (such as connecting the language model with other software, querying weather, etc.). You can simply understand it as: MCP makes it easier for the language model to call various external tools.

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/image20.png)

In the area below, you'll also see model selection options — click to change the current large model. In the China version, you can choose domestic models like Kimi k2 or GLM. If you're using the international version of Trae, you can also select overseas models like ChatGPT or Claude. However, since domestic large models are developing very rapidly, Kimi, Qwen, GLM, and others already offer experiences close to Claude 3.5 or 3.7 in many tasks, which is more than sufficient for daily development. There's no strict requirement to use the international or China version here.

**Note that we don't recommend using Auto mode (automatic model selection). For the international version, we recommend using Gemini or GPT models. For the China version, we recommend trying domestic models like Kimi k2, Minimax, or GLM.** Different models suit different use cases — there's no dogmatic rule about which is better. When you hit a wall with one model, try switching to another. Through multiple tests, you'll find the best results for your own workflow.

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/image21.png)

That's a brief introduction to Trae. Next, let's revisit what we did previously on z.ai and try doing the same thing in Trae.

### 4.2 Step 1: Create an Empty Folder and Open It with an AI IDE

Before getting started, we first need to prepare a clean project working directory.
For this section's example, you can create a new empty folder named `snake-game-react` on your local machine.

Then, open your installed AI IDE, select "Open Folder" on the startup screen, and import the empty folder as the project root directory. You can also drag the folder directly into the IDE window to open it. At this point, the file explorer on the left won't show any code files, indicating that we're starting from a completely blank project state.

::: details 📚 Optional: Connect a Cloud Service Provider's API or Coding Plan

This section introduces how to connect a cloud service provider's API or Coding Plan for more stable and frequent model calls. Screenshots of the Trae integration are provided at the end.

**What Is a Coding Plan**

A Coding Plan is a subscription offered by major cloud service providers. After purchasing, you can **use the provider's large model API without limits or at high frequency** for a certain period. Compared to per-token billing, a Coding Plan is more like a "monthly package" — you pay a fixed fee and can use it freely without worrying about per-call charges.

**Why Purchase a Coding Plan**

You might ask: since you can call large models directly via API, why buy a Coding Plan? The main reason is: **unlimited usage**. The core advantage of a Coding Plan is that you can call the large model anytime, as frequently as you want, without worrying about costs exploding or constantly checking billing statements.

**Recommended Domestic Cloud Service Coding Plans**

Here are recommended Coding Plan options from major domestic cloud service providers:

- Zhipu AI (BigModel Plan): https://bigmodel.cn/glm-coding
- Volcengine (ByteDance Cloud AI Plan): https://www.volcengine.com/activity/codingplan

> 💡 **You can also directly connect a large model API**
> Besides Coding Plans, you can also directly connect various model APIs through Add Model. You can refer to the method below for connecting the OpenRouter StepFun free API to integrate it with Trae. Testing shows it meets basic programming needs.
> If you need to top up, we suggest starting with a small amount (e.g., 10 RMB) to see how long it lasts, such as with cost-effective models like DeepSeek.

**How to Connect a Coding Plan**

Connecting a Coding Plan is very simple and takes just a few minutes:

1. Visit your chosen cloud service provider's website (e.g., Zhipu AI: https://bigmodel.cn/glm-coding, Volcengine: https://www.volcengine.com/activity/codingplan)
2. Register an account and log in
3. Find the "Pricing" or "Coding Plan" page
4. Choose a plan that suits you and complete the payment
5. After payment, you'll receive an API Key or Plan ID

::: tip 🎯 Custom Model Recommendations

When connecting custom models in Trae, we **recommend using the OpenRouter approach by default**. OpenRouter provides a unified API interface for conveniently connecting to multiple large language models.

**As of February 12, 2026, you can still use StepFun's free API:**

- **`stepfun/step-3.5-flash:free`**: A free model from StepFun that can be directly connected in Trae.

**Other free models:**

- **`openrouter/free`**: A model option that uses free LLM APIs by default. You can use it directly in Trae's Custom Model integration (just enter the model ID), experiencing AI programming features without any cost.

These free options are great for beginners. Before committing to production use, you can familiarize yourself with the AI IDE workflow through these free options.

**Optional: Connect a Large Model API (Using DeepSeek as an Example)**

1. Visit the DeepSeek platform: https://platform.deepseek.com/usage
2. Register an account and log in
3. Purchase a 10 RMB token package on the top-up page
4. After topping up, create and copy an API Key on the API Keys page
5. In Trae, click **"Add Model"**, find DeepSeek, select the corresponding model, and enter the API Key to start using it

Through the interface below, you can successfully add a model (note: after selecting the model option, **make sure to scroll all the way to the bottom** — there's a "Custom Model" option. Click it to enter a model ID, where you can type the recommended model IDs like `stepfun/step-3.5-flash:free`. Also click "Get Key" below to visit the official website and obtain the corresponding API Key.)

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/index-2026-02-12-14-14-51.png)

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/index-2026-02-12-14-15-29.png)
:::

### 4.3 Step 2: Chat in the Sidebar and Have AI Design a Snake Game with React

Next, open the AI chat sidebar: usually by pressing `Ctrl+L` or clicking the chat icon on the right. Then enter a clear prompt:

> Please implement a Snake game using React architecture, including keyboard controls, growing and scoring when eating food, and displaying "Game Over" with restart support when hitting walls or itself. After implementation, help me start this project. If any program environment is not installed, automatically install the missing environment.

During this process, you need to realize that AI is not just a chat model—it can help you operate your local environment: creating files, installing dependencies, executing startup commands, etc. You can directly describe your goals in natural language, and let AI decide which specific commands to execute and how to organize the code.

If problems occur during execution, AI will display errors and solutions in the conversation. You can continue to have it adjust through dialogue without having to remember all command details yourself.

::: warning ⚠️ Important Note
As shown in the figure below, **sometimes the AI Agent will pause during execution because it needs to wait for you to input some information for interaction**, such as entering a created name, or pressing Enter to confirm command execution, or clicking a command to execute. Usually we just press Enter directly. If you're unsure what this step requires, you can take a screenshot of the current interface and ask the large model what operation should be performed.
:::

As shown, here we need to click Run to confirm:
![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/index-2026-01-09-10-52-55.png)

As shown, here we just need to input y to confirm:
![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/index-2026-01-09-10-53-24.png)

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/index-2026-01-09-10-26-33.png)

As shown, here we are creating a template but don't know how to operate. We can take a screenshot of this part and ask the large model:

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/index-2026-01-09-10-29-12.png)

Another reason the AI Agent pauses during execution is because it has started a "service." Our Snake game itself is a type of "service." If you see a URL with the following command, it means the Agent has executed a local computer service for us. We can visit the corresponding URL to access our Snake game. Since the service needs to run continuously, it will pause here. We just need to click the `Skip` button.

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/index-2026-01-09-10-30-51.png)

During this process, if you encounter some terms and content you don't understand, don't worry. You can refer to the "Computer Terminology Explanation" section in the appendix, or directly consult AI, or ask questions in time!

If you encounter unexpected phenomena during the process, such as the snake not ending the game when hitting a wall, or the snake not moving after clicking start, you just need to describe the phenomenon to the sidebar Agent. If you encounter error problems, remember to take a screenshot or copy the error to the sidebar Agent. If it still can't be solved after multiple attempts, please try changing the model.

After a short while, we can get results similar to z.ai:

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/index-2026-01-09-10-33-37.png)

We can click the checkmark in the bottom right corner to confirm code changes, or click the `Cancel` button to cancel changes. Or click on the "2 files need review" area to expand and view the modified code.

It's also worth noting that since code modifications may not always be correct, we need to know that all IDE Agents support code rollback. For example, if I accidentally made a wrong modification operation here, or if the result of this operation is unsatisfactory, after the modification is complete, we can return to the input box area and click the Revert button to roll back the operation to the state before modification. You can modify the input text for another operation:

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/index-2026-01-09-10-42-53.png)

### 4.4 Step 3 (Optional): Ask AI About Code Implementation Details

When the Snake game is running normally, if you're not yet familiar with frontend or React, you can continue in the same chat window and ask AI to guide you through the code in as colloquial a way as possible. You don't need to switch tools or deliberately look through documentation—just keep asking questions about the current project.

A practical approach is to have AI first give an overall explanation of "how the game moves," then break it down into specific details. For example, you can directly ask:

> "Please explain from top to bottom how this Snake game moves step by step? Try to use as few technical terms as possible."

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/index-2026-01-09-10-44-36.png)

Then follow up on key points based on its answer, such as:

> "What data structure is used to record each segment of the snake's body on the screen? Can you give an analogy?"
> "How do you control 'moving once every while'? Which section of code is this in?"
> "When the snake eats food, what steps do you take? Where is the logic that determines it ate something?"
> "Where in the code are hitting walls and hitting itself judged respectively?"

If you see a certain file (like `SnakeGame.tsx`) but have no idea what it's doing, you can also directly ask AI to explain it in sections:

> "Please explain `SnakeGame.tsx` in several functional blocks: what is each block roughly responsible for, using simpler language."

In this round of dialogue, you can treat any word you don't understand as an entry point for follow-up questions, such as:

> "What exactly does 'state' mean in what you just said? Can you explain it with a real-life example?"
> "What does 'timer' mainly do here? What would happen if it were removed?"

Through this method, your goal is not to memorize all concepts at once, but to first understand three things: what core data exists in this game (snake, food, score, game state, etc.), when this data changes (moving, eating food, game over, etc.), and which small section of code corresponds to each change. Once these three points are clear, you can basically understand the main logic of this code.

### 4.5 Step 4: Have AI Make the Interface Look Better

First, a reminder for beginners: don't just tell AI "I want to make this interface look better." This statement is too vague even for human designers, let alone models—what style does "good-looking" mean, which parts need adjustment, is it a layout problem or a color problem? AI can't read all this from your one sentence. To make AI truly produce results close to what you have in mind, you need to learn to break down the vague goal of "I want it to look good" into a series of specific, executable small requirements.

For example, many people initially say something like this:

> "I want to make this interface look a bit better."

Instead, you can first give a set of overall requirements:

> "Please help me beautify the game interface overall:
>
> - Center the game area, don't stick it to the top-left corner;
> - Change to a lighter background color to make the snake and food more prominent;
> - Enlarge the score and place it in a prominent position;
> - Use blue as the main color scheme to beautify the overall color scheme and buttons."

If you want clearer feedback when the game ends, you can further supplement:

> "When the game ends, please display 'Game Over' in the center of the screen, with a 'Restart' button below it that can reset the game."

AI will directly modify React components and styles based on your description. After saving, refresh the browser to see the new interface. If the effect still differs from what you imagined, you can continue making small adjustments, such as:

> "Make the score a bit larger and the color more prominent."
> "Make the game area more compact with some margin around it."
> "Change the restart button to a blue rounded style, centered below the prompt."

At this stage, if a modification causes an error, you don't need to troubleshoot it yourself. Just copy the error message to the chat window, or provide a brief description like "This is the error that appeared after I beautified the interface," and let AI locate and fix it within the current project context. This way you can gradually polish a running demo into a small finished product with a clear interface and smooth interactions through the cycle of "continuous dialogue, continuous refreshing."

### 4.6 (Optional) Reference z.ai Architecture to Modify Snake Results

For vibe coding beginners, the hardest thing is not knowing what counts as "best practices" or what architecture is most suitable; because you don't know computer basics, you can't guide AI well. The solution to this problem is "direct reference." Remember when we said you can view code in z.ai? In fact, the corresponding README (the part used in projects to introduce functionality and technical architecture) already gives a best architecture reference:

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/index-2026-01-09-10-49-33.png)

If we want the local result to match the z.ai result as closely as possible, we can copy all the content of this README and paste it into Trae's sidebar, asking it to modify the local code according to the README architecture.

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/index-2026-01-09-10-50-31.png)

Finally, we can get page design styles highly similar to z.ai:

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/index-2026-01-09-11-00-57.png)

<div style="margin: 50px 0;">
  <ClientOnly>
    <StepBar :active="2" :items="[
      { title: 'Understanding the Environment', description: 'IDE vs AI IDE' },
      { title: 'Hands-on Practice', description: 'Build Snake with Trae' },
      { title: 'Tool Deep Dive', description: 'Explore the IDE Interface' },
      { title: 'Communication Skills', description: 'Talk to AI Effectively' }
    ]" />
  </ClientOnly>
</div>

## 5. What Each Button on the Interface Does

In the above operations, we've quickly run through the minimum program generation loop, but we're still not familiar with the IDE. To thoroughly familiarize ourselves with this tool that we'll be working with long-term, we'll provide in-depth explanations of every detail of the IDE in this section. Starting with the interface, different AI IDEs have slightly different interfaces, but most follow the [VS Code layout](https://code.visualstudio.com/docs/getstarted/getting-started).

![](../../../zh-cn/stage-1/introduction-to-ai-ide/images/image32.webp)

The specific function of each part is:

- **Title Bar**: Displays file name and window control buttons.
- **Activity Bar**: Switches between functional views like files and search.
- **Side Bar**: Displays specific content like file lists.
- **Editor Groups**: The core area for writing code.
- **Breadcrumbs**: Shows file path and supports navigation.
- **Minimap**: Quick preview and positioning of code.
- **Panel**: Contains terminal and output windows.
- **Status Bar**: Displays current environment status.

For more detailed explanations, please refer to the [Virtual IDE Visualization section in the Appendix](/ru-ru/appendix/2-development-tools/ide-basics).

<div style="margin: 50px 0;">
  <ClientOnly>
    <StepBar :active="3" :items="[
      { title: 'Understanding the Environment', description: 'IDE vs AI IDE' },
      { title: 'Hands-on Practice', description: 'Build Snake with Trae' },
      { title: 'Tool Deep Dive', description: 'Explore the IDE Interface' },
      { title: 'Communication Skills', description: 'Talk to AI Effectively' }
    ]" />
  </ClientOnly>
</div>

## 6. How to Talk to AI Effectively

As AI capabilities become stronger and stronger, we can delegate much of the "programmer writes code" work to AI. However, in actual use, you'll find that using the same AI, some people can get a working small project in a few sentences, while others chat for a long time but get results completely different from what they wanted. The difference often lies not in "who is smarter," but in—whether the way you talk to AI is specific enough and step-by-step enough. This section introduces some questioning methods suitable for complete beginners from several common scenarios, helping you more stably get usable results from AI.

### 6.1 Clarify Your Requirements: From "Vague Idea" to "Specific Description"

Many people, when first using AI, are accustomed to saying only one very general sentence, such as:

> "Help me make a webpage."
> "Help me write a small program."

In this case, AI can only "imagine" what you want, so it will casually give you something that looks quite complete, but often differs greatly from what you really want to do. To make AI understand you better, you need to break down the "idea in your head" and explain it step by step.

You can supplement from these aspects:

1. **Tell it what you're using this thing for**
   For example, don't just say "personal website," but say:
   - "I want to make a personal profile webpage with only one page of content, to send to recruiters."

2. **Tell it roughly what blocks of content you need**
   No need to use professional terms, just describe what you hope appears on the page, such as:
   - "The page should have three sections: at the top is my name and a self-introduction sentence, the middle lists several work experiences, and the bottom puts email and WeChat ID."

3. **Tell it your level and limitations**
   Let AI do it in a way that beginners can accept, such as:
   - "I can't write code at all, please use the simplest method so I can directly copy it into one file and open it in the browser."

4. **Tell it how you hope to get the results**
   For example:
   - "Please give me complete code that can be directly saved as `index.html` and opened in the browser."

Putting it together, you can say this to AI:

> "I can't write code at all and want to make a personal profile webpage with only one page of content, to send to recruiters.
> The page needs three sections: the top line is my name and a self-introduction sentence, the middle is several work experiences, and the bottom is email and WeChat ID.

When you clarify this information, AI can get closer to your real needs, rather than casually giving you something "that looks impressive but is useless."

### 6.2 Use the Right Rhythm: "Get It Running" First, Then Gradually Make It Complex

For complete beginners, the most common pitfall is: wanting to make something "very complete" and "with many features" right from the start.
For example:

> "Help me make a website like Taobao."
> "Help me make a system with registration, login, and ordering."

The result is often: AI gives you a large chunk of code, which either won't open or has errors everywhere after you copy it; you also can't understand where the problem is, and finally have to give up.

A better approach is to **actively control the rhythm**, letting AI follow you step by step, rather than throwing everything at you at once. You can request in this order:

1. **First step: Ask for a "minimal example"**
   Only check one thing: can you see something in the browser?
   For example:

   > "Please first give me the simplest example, as long as I can see a line saying 'This is my homepage' in the browser.
   > Then tell me step by step: what should the file name be, how should I save it, and how to open it."

2. **Second step: Slowly add complete content on this basis**
   After you confirm "I can indeed see that line of text," then say:

   > "On the basis of what we just had, help me add a 'Work Experience' area and send me the complete code again. Don't just send the changed parts."

3. **Third step: After the structure is almost done, then consider whether it looks good**
   For example:
   > "Now the page can display content normally. Next, please help me beautify it a bit: center it overall, make the title larger, and use a more comfortable font. Please give me the updated complete code."

With each addition, you run it once first to confirm there really is a change before letting AI continue. This way, even if something goes wrong at any step, you can quickly return to the "previous version that was working" state without having to start completely from scratch.

### 6.3 Make Good Use of Screenshots and Copying: If You Can't Say It, "Throw the Screen at AI"

Many difficulties complete beginners encounter don't lie in "not knowing how to modify code," but in **not knowing how to describe the problem**.
For example:

- A bunch of English errors suddenly pop up in the browser, which you completely don't understand.
- The webpage layout is different from what you wanted, but you don't know what words to use to describe it.

In these cases, you don't need to force out professional terms. The simplest way is to **throw what you see directly at AI**.

You can do this:

1. **Copy error text**
   When you see a string of red error messages, you can directly copy them out and say:

   > "This is the complete error message that appeared after I ran it. I don't understand this English, please first explain in words that ordinary people can understand what this roughly means.
   > Then tell me what is the simplest way I should modify it now."

2. **Show AI a screenshot**
   If you feel "this page just looks wrong" but can't describe it, you can:
   - Take a screenshot of the current page;
   - Copy the entire section of code you're using to AI;
   - Then explain:
     > "This is what the page looks like now, this is my current complete code.
     > I originally wanted it to be a three-column layout, but now it's become one column. Please help me find the reason and give me a corrected complete code."

   ::: tip 💡 Supplementary Note on Screenshot Functionality

   It's important to note that **not all AI models support "looking at pictures."** This involves two different concepts:

   - **Pure text large models (LLM)**: Can only process text input and cannot recognize image content. If you send it a screenshot, it will either refuse to process it or cannot correctly understand the information in the image.

   - **Multimodal models**: Can process multiple types of input such as text and images simultaneously, can "understand" the screenshots you send, and give suggestions based on the image content.

   **Common model capability reference** (taking models available in Trae as an example):

   | Model | Supports Image Input |
   |------|-----------------|
   | Doubao-Seed Series | ✅ Supported |
   | GLM-4.7 / 4.6 | ❌ Not Supported |
   | MiniMax-M2.7 / M2.5 | ❌ Not Supported |
   | DeepSeek-V3.1 | ❌ Not Supported |
   | Kimi-K2.5 | ✅ Supported |
   | Kimi-K2-0905 | ❌ Not Supported |
   | Qwen-3-Coder | ❌ Not Supported |
   | Gemini Series | ✅ Supported |
   | GPT Series | ✅ Supported |

   **Usage suggestion**: If you want AI to help you troubleshoot interface problems through screenshots, please first confirm that the model you are using supports image input. If not supported, you can use text to describe the problem, or copy and paste error messages to AI.

   :::

3. **Encounter a webpage you like and want to make something similar**
   No need to say "what is this layout called," just:
   - Take a screenshot or copy the page's main title and paragraphs;
   - Then say:
     > "I want to make a page with a similar structure to this, doesn't need to be exactly the same.
     > Please help me build a similar framework with simpler code, then I'll replace the text with my own."

Simply put: you're responsible for "moving what you see to AI," then using the simplest words to say "I hope it becomes like this"; the rest of "translating into code, explaining terms, finding problems" is left to AI.

### 6.4 When AI-Generated Code Doesn't Work: A Universal Response Method

In actual practice, you will definitely encounter this situation:
AI seriously gave you a piece of code, and you honestly copied it in, but the result is either a blank browser page or completely different from what it said.
This doesn't mean you "can't learn," nor does it mean AI is completely wrong, but rather that you and AI are still missing a few rounds of "back-and-forth confirmation."

When code "doesn't work," you can follow this fixed process to talk to AI:

1. **First clearly state "what you did + what it looks like now"**
   Avoid just saying "won't open" or "not working." You can describe it like this:

   > After opening, the page is completely blank, not showing the welcome text you mentioned.
   > I opened the relevant page, and the part I just mentioned is not there, so this still doesn't work.

2. **Send AI your current complete code**
   Many times the problem is: you copied one line less, or mixed content from the previous and current times together.
   You can say:

   > "Below is all the code currently in my file.
   > Please compare to see if anything is missing, written wrong, or in the wrong order.
   > Please directly give me a corrected complete code, don't just send a small section."

3. **If there are error prompts, provide them together**
   For example, errors that pop up in the top-right corner of the browser, or some red text at the bottom. You can:
   - Copy out the error text;
   - Or take a screenshot;
   - Then say:
     > "This is the error prompt I see. I completely don't understand it, please first explain in simple terms what this problem roughly is, then tell me which lines need to be modified most urgently now."

4. **Ask the other party to use "beginner mode" to explain step by step**
   You can directly state your situation and ask it not to skip intermediate steps:

   > "I can't write code at all, please tell me step by step:
   > Step 1: which line to modify,
   > Step 2: how to save,
   > Step 3: how to reopen or refresh the page.
   > Please write out each step in complete sentences."

5. **Finally, ask it to help you do a "what you should see" comparison**
   For example:
   > Please first say, according to your corrected code, what content should I normally see when I open the webpage.

As long as you follow this process to interact with AI, most "code not working" situations can be resolved in a few rounds of back-and-forth.
At the same time, you will gradually become familiar with common problem types, and next time you encounter similar situations, you can solve them directly.

## 7. Summary and Next Steps

In this chapter, you completed an upgrade from "playing an AI-generated Snake in a webpage" to "building a small game yourself with an AI IDE locally." You roughly figured out three things: why writing code can't be separated from an IDE like VS Code; on this basis, adding AI (Trae, Cursor, etc.) makes the IDE no longer just a toolbox, but adds an "intern engineer" who can understand natural language, help you create files, install environments, and modify code; and what each area of the IDE interface (left files, bottom terminal, middle editing area, right AI panel) is responsible for, so you're no longer confused when using it.

More importantly, you've actually run through a complete process once: create an empty folder locally → open with AI IDE → describe requirements in sidebar dialogue → let AI generate project and start development server → when problems occur, throw "phenomenon + complete code + error screenshot" to AI together, asking it to fix step by step in "beginner mode." In this process, you also practiced how to write more effective prompts: clarify goals, content structure, and your level, control the rhythm well, from "get it running first" to "then make it look good, make it fun."

In the next chapter, we'll shift focus from "knowing how to use tools" to "making a prototype that people actually want to use": starting from the user perspective, designing rules, interactions, and feedback, then letting AI help you turn these ideas into a product prototype.

## 8. 📚 Assignment: Make a More Complex Game with Local AI IDE

<el-card shadow="hover" style="margin: 20px 0; border-radius: 12px;">
  <template #header>
    <div style="font-weight: bold; font-size: 16px;">🚀 Challenge Task: Build Your Own Game</div>
  </template>

  <p>
    You've already made a Snake game with a local AI IDE. Now please challenge yourself with a slightly more complex small game, walking through the complete process of "describe requirements → generate project → run locally → debug and iterate."
  </p>

  <ol>
    <li>
      <strong>Choose a game more complex than Snake</strong>
      <ul>
        <li>Could be Tetris, Whack-a-Mole, Minesweeper, 2048, Aircraft Battle, etc.</li>
        <li>Or a simple original game you imagine yourself</li>
      </ul>
    </li>
    <li>
      <strong>Must use local AI IDE to complete the entire process</strong>
      <ul>
        <li>Create a new empty folder and open it with AI IDE</li>
        <li>Describe your game requirements clearly in the sidebar chat</li>
        <li>Let AI be responsible for creating files, building project structure, and implementing main logic</li>
        <li>Start the development server locally to ensure the game can run normally</li>
      </ul>
    </li>
    <li>
      <strong>Have basic "playability" and feedback</strong>
      <ul>
        <li>At least include three states: start, in-progress, and end</li>
        <li>Players have clear operation methods (keyboard or mouse)</li>
        <li>Clear score or progress feedback on the screen</li>
      </ul>
    </li>
    <li>
      <strong>At least 2+ rounds of iteration</strong>
      <ul>
        <li>First round: let AI make a "playable" version</li>
        <li>Second round and beyond: gradually propose specific improvements (style, difficulty, interaction optimization, etc.)</li>
      </ul>
    </li>
  </ol>
</el-card>

# Appendix

<el-card id="appendix-nav" shadow="hover" style="margin-top: 40px; margin-bottom: 24px; border-left: 5px solid #E6A23C;">
  <div style="font-weight: bold; margin-bottom: 8px;">Appendix Navigation</div>
  <div style="color: #606266; font-size: 14px; line-height: 1.6; margin-bottom: 12px;">
    Here are "look up when needed" supplementary materials: come back when you encounter terms you don't understand or can't find interface entries.
  </div>
  <el-row :gutter="16">
    <el-col :span="12">
      <a href="#appendix-1-map" style="text-decoration: none; color: inherit;"><b>Appendix 1: Common Computer Terminology Quick Reference</b></a><br/>
      <span style="font-size: 12px; color: #909399">When you see computer terms you don't understand, quickly look up their meanings here. Recommended to read through once.</span>
    </el-col>
    <el-col :span="12">
      <a href="/ru-ru/appendix/2-development-tools/ide-basics" style="text-decoration: none; color: inherit;"><b>Appendix 2: Visual Studio Code Menu Bar Analysis</b></a><br/>
      <span style="font-size: 12px; color: #909399">When you don't know what the AI IDE interface is for, use the following content to consult with AI, or view directly.</span>
    </el-col>
  </el-row>
  <div style="margin-top: 12px; font-size: 12px; color: #909399;">
    Support: Press Ctrl/⌘+F to search for keywords; when encountering new words, you can copy errors and let AI explain in "beginner mode."
  </div>
</el-card>

# Appendix 1: Common Computer Terminology Quick Reference

<el-card id="appendix-1-map" shadow="hover" style="margin-top: 40px; margin-bottom: 20px; border-left: 5px solid #409EFF;">
  <div style="font-weight: bold; margin-bottom: 10px;">🗺️ Terminology Map: What You'll Encounter Here...</div>
  <el-row :gutter="20">
    <el-col :span="6">
      <a href="#term-tool-ui" style="text-decoration: none; color: inherit;">🖥️ <b>Tool Interface</b></a><br/>
      <span style="font-size: 12px; color: #909399">IDE / Terminal / Panel</span>
    </el-col>
    <el-col :span="6">
      <a href="#term-network" style="text-decoration: none; color: inherit;">🌐 <b>Network Services</b></a><br/>
      <span style="font-size: 12px; color: #909399">URL / Port / Local</span>
    </el-col>
    <el-col :span="6">
      <a href="#term-frontend-backend" style="text-decoration: none; color: inherit;">⚙️ <b>Frontend & Backend</b></a><br/>
      <span style="font-size: 12px; color: #909399">API / JSON / Interface</span>
    </el-col>
    <el-col :span="6">
      <a href="#term-code-basic" style="text-decoration: none; color: inherit;">📝 <b>Code Basics</b></a><br/>
      <span style="font-size: 12px; color: #909399">Variable / Function / Component</span>
    </el-col>
  </el-row>
  <el-row :gutter="20" style="margin-top: 10px;">
    <el-col :span="6">
      <a href="#term-debug" style="text-decoration: none; color: inherit;">🐞 <b>Debugging</b></a><br/>
      <span style="font-size: 12px; color: #909399">Bug / Breakpoint / Log</span>
    </el-col>
    <el-col :span="6">
      <a href="#term-project" style="text-decoration: none; color: inherit;">📂 <b>Project Management</b></a><br/>
      <span style="font-size: 12px; color: #909399">Git / Repository / Commit</span>
    </el-col>
    <el-col :span="6">
      <a href="#term-ai-tool" style="text-decoration: none; color: inherit;">🤖 <b>AI Tools</b></a><br/>
      <span style="font-size: 12px; color: #909399">Agent / Model / Key</span>
    </el-col>
    <el-col :span="6">
      <a href="#term-browser" style="text-decoration: none; color: inherit;">🛠️ <b>Browser</b></a><br/>
      <span style="font-size: 12px; color: #909399">DevTools / Console</span>
    </el-col>
  </el-row>
</el-card>

You don't need to deliberately memorize this section. What's more important is to first establish an impression in your mind.

## <span id="term-tool-ui">[1. Words Related to "Tool Interface"](#appendix-1-map)</span>

### 1. IDE, Editor, Terminal

**IDE (Integrated Development Environment)**
You can think of an IDE as a "programmer's workbench":

- One side is a writing desk (editor),
- One side has power outlets and buttons (run, debug),
- Drawers contain various small tools (search, version management).
  VS Code, Trae, Cursor all belong to IDEs or tools based on IDEs.

**Code Editor (Editor)**
More like an "advanced notepad," only responsible for:

- Letting you type code;
- Using colors to distinguish different content (syntax highlighting);
- Giving you auto-completion.
  The area in the IDE where you write code is the code editor.

**Terminal / Command Line (Terminal / Command Line Window)**
A window with black background and white text, where you **input commands** for the computer to work:

- For example: `npm run dev` means "help me start the development server";
- `python main.py` means "run this Python file."
  You can think of it as: "You send the computer text message commands one by one, and it replies with execution results in text."

### 2. Several Common Areas in the IDE

**Activity Bar**
The row of small vertical icons on the far left, like "function tabs":

- Click file icon → file list displays on the left;
- Click magnifying glass icon → left becomes search;
- Click Git icon → left displays version management.

**Side Bar**
The large area to the right of the Activity Bar, specifically displaying content for the current mode:

- File mode: shows files and folders in the project;
- Search mode: shows search results list;
- Source control mode: shows which files have been modified.

**Editor Area**
The largest area in the middle, where you actually see and modify content after opening a file;
The tabs above are "which files are currently open."

**Panel**
Generally at the bottom, common types include:

- Terminal: input commands to run projects;
- Problems: lists error files and line numbers;
- Output: some tool-printed runtime information;
- Debug Console: output during debugging.

**Status Bar**
The thin bar at the very bottom:

- Displays what language the current file is (JS, HTML, Python, etc.);
- Displays whether indentation is "2 spaces" or "4 spaces";
- Displays whether there are errors, what the current Git branch is.
  You can think of it as "a small health check of the current editing environment."

## <span id="term-network">[2. Words Related to "Webpage / Network / Service"](#appendix-1-map)</span>

### 1. URL, HTTP, Port, Local Service

**URL (Web Address)**
That string of things in the browser address bar, such as:

- `https://www.trae.cn/`
- `http://localhost:3000/`
  It's like "the complete address of a room in the internet world."

**HTTP / HTTPS**
The `http://` or `https://` you see at the beginning of a URL:

- HTTP: ordinary transmission method;
- HTTPS: adds a layer of encryption, more secure.
  You can first remember: "When writing webpage addresses, usually start with `http` or `https`."

**Port (Port)**
You can imagine a computer as a building, and ports are **room numbers for each room**:

- `:3000` means room 3000;
- The same computer can run multiple services simultaneously, each occupying a port.
  `http://localhost:3000` means "access the service running in room 3000 on my own computer."

**Local (Local / localhost)**
Refers to your own computer.

- `localhost` can be understood as "this machine itself."
  When you access `http://localhost:3000`, you're actually interacting with a program running on your own computer, not accessing someone else's server online.

**Service (Service / Server)**
A "service" is a **program that keeps running in the background, always listening for your commands**:

- Web service: when a browser accesses an address, it returns webpage content;
- Game service: responsible for managing matches, saves, leaderboards, etc.
  Executing `npm run dev` in the terminal to start a project is essentially "opening a web service locally."

## <span id="term-frontend-backend">[3. Words Related to "Frontend / Backend / Data"](#appendix-1-map)</span>

### 1. Frontend, Backend

**Frontend**
The part that users **can see and click**:

- Buttons, text, images, animations on webpages;
- Pages written in React / Vue.
  Responsible for displaying interfaces and responding to user operations (clicks, inputs, drags, etc.).

**Backend**
The part that users **cannot see**, running on the server:

- Storing and reading data (user information, orders, scores, etc.);
- Executing business rules (login verification, permission judgment).
  You can think of frontend as "storefront and clerk," and backend as "warehouse and ledger system."

### 2. Interface, Request, Response, JSON

**Interface / API**
A set of "question + answer" rules agreed upon in advance between frontend and backend.

- Frontend says: "I'll ask you using this address, this format";
- Backend says: "I'll return results to you in this format."

**Request (Request)**
A "question" sent from frontend to backend:

- Where is the request going (URL);
- What method is used (GET, POST, etc.);
- What parameters are brought (such as user ID).

**Response (Response)**
The "answer" given by backend to frontend:

- Status code (200 success, 404 not found, 500 server error);
- Actual data (mostly JSON).

**JSON**
A format for representing data using **syntax very similar to JavaScript code**, such as:

```json
{
  "name": "Alice",
  "score": 120
}
```

Can be understood as "a machine version of key-value notepad," often used by frontend and backend to exchange data.

## <span id="term-code-basic">[4. Words Related to "Writing Code Itself"](#appendix-1-map)</span>

### 1. Variable, Identifier, State

**Variable (Variable)**
"A label attached to a piece of data."

- For example, recording the score as `score`;
- Later using the name `score`, you can read and write this data:

```js
let score = 0
score = score + 10
```

**Identifier (Identifier)**
A general term for "various names you give yourself":

- Variable name: `score`
- Function name: `moveSnake`
- Component name: `SnakeGame`
  Like naming folders "Photos," "Work," "Bills" for easy distinction between different "things" in code.

**State (State)**
The "key situation record" of the program's current state:

- Whether the game has ended;
- Which grid the snake is currently on;
- What the current score is.
  In React, it's generally understood this way: **when state changes, the interface must follow and update**.

### 2. Function, Component, Module

**Function (Function)**
Package something that "can be done repeatedly" and give it a name:

```js
function sayHello(name) {
  console.log('Hello, ' + name)
}
```

Later, just writing `sayHello('Bob')` equals executing those lines again.

**Component (Component)**
In frontend, "a small interface + small logic that can be reused":

- A button can be a component;
- A top navigation can be a component;
- The entire game area can also be a component.
  Components can be assembled together, like building with LEGO.

**Module (Module)**
"A file composed of a group of related codes":

- `snakeLogic.ts` specifically stores code related to "how the snake moves";
- `score.ts` specifically stores code for calculating scores.
  Modules can "import / export" between each other, like tools in different drawers.

### 3. Syntax, Programming Language, Framework

**Syntax (Syntax)**
The "grammar rules" and "punctuation habits" of a programming language:

- Strings need quotes;
- Whether to write a semicolon at the end of each statement;
- Code blocks need to be wrapped in `{}`.
  Writing syntax errors, compilers / interpreters will directly report "syntax errors."

**Programming Language (Programming Language)**
A complete set of rules and vocabulary for communicating with computers, such as:

- JavaScript, Python, Java, C++, Go...
  Different languages are suitable for different things, have different writing styles and tool ecosystems.

**Framework (Framework)**
A large set of code and patterns that others have "pre-built the skeleton" for you:

- Frontend: React, Vue (helping you handle interface updates, state management, etc.);
- Backend: Django, Spring Boot, etc.
  You're essentially "filling in content on a ready-made skeleton," much easier than building from scratch.

## <span id="term-debug">[5. Words Related to "Debugging / Troubleshooting"](#appendix-1-map)</span>

### 1. Bug, Error, Log / console.log

**Bug**
When program behavior differs from what you expect, that's a bug:

- Buttons that should appear don't appear;
- Should add 10 points but added a bunch more;
- Page shows white screen as soon as it opens.

**Error Message (Error Message)**
That "scary-looking" English that appears on the screen / in the terminal after a program crashes.
Although ugly, it usually tells you:

- Roughly where the error is;
- Which file, near which line needs checking.
  You can directly copy it and throw it to AI for translation and analysis.

**Log (Log)**
What the program "says" during operation.
Most common in frontend is:

```js
console.log('Current score', score)
```

You can think of it as: **actively reporting numbers at key steps to confirm whether the program is running as you expect**.

> **What is console.log?**
>
> - `console` can be understood as "a small blackboard for debugging";
> - `.log` is "writing a line on the small blackboard";
> - Press F12 in the browser to open the Console panel in developer tools to see these outputs.

### 2. Debug, Breakpoint, Step-by-Step Execution, Snapshot

**Debug (Debug / Debugging)**
When a program has problems, instead of randomly modifying:

- Let the program pause at a certain line (breakpoint);
- Look at the value of each variable at the moment;
- Walk through step by step, observing "where it starts to go wrong."

**Breakpoint (Breakpoint)**
You can think of a breakpoint as "a pause button inserted at this line":

- Programs normally run all the way through;
- When running to the line where you inserted the breakpoint, it will temporarily stop and wait for your inspection.

**Step-by-Step Execution (Step)**
After stopping from a breakpoint, you can choose:

- Execute line by line (step over);
- Go inside a certain function to see details (step into).
  Like watching a dance broken down into moves, rather than watching a fast-forward video directly.

**Snapshot (Snapshot) — Simplified Understanding**
Here "snapshot" can be understood as:

> **Taking a photo of the "current state" at a certain point in time for future comparison.**
> In actual tools, "snapshot" may refer to:

- The complete state of the project at the moment of a commit;
- The overall situation of memory / variables at a certain point during debugging.
  Just remember this analogy for now: **snapshot ≈ a photo of state at a certain moment**.

## <span id="term-project">[6. Words Related to "Project Management"](#appendix-1-map)</span>

### 1. Project, Workspace, Folder

**Project (Project)**
For implementing an application, placed in the same folder:

- Source code files
- Configuration files
- Assets (images, audio, etc.)

**Workspace (Workspace)**
A concept used by VS Code / Trae to describe "what group of things is currently open this time":

- Opening a folder → a simple workspace;
- Sometimes multiple folders are combined into a multi-project workspace.

### 2. Git, Repository, Commit

**Git (Version Control Tool)**
Can be understood as a "time machine" for projects:

- After each batch of modifications, you can "take a version photo";
- When needed in the future, you can return to a certain historical state.

**Repository (Repository / Repo)**
After enabling Git, that project folder with "version records" is called a "repository."

**Commit (Commit)**
Every time you feel "this round of modifications counts as a meaningful milestone," you can:

- Write a description (such as: `Add score panel`);
- Package all current modifications into a version;
- Git will save the state at this moment.
  This action is called "making a commit."

## <span id="term-ai-tool">[7. Words Related to "AI Development Tools"](#appendix-1-map)</span>

### 1. AI IDE, Agent, SOLO Mode

**AI IDE**
On the basis of ordinary IDEs, adds a layer of AI that "can understand human language and take action itself":

- You say "make a Snake game," it can help you set up the project, write code;
- You give it a screenshot of an error, it can first explain then try to fix;
- It can modify across multiple files together, not just complete line by line.

**Agent (Agent)**
You can think of an Agent as an **AI junior engineer on long-term standby**:

- Will read your project structure;
- Will break down tasks (install dependencies first, then generate code, then run project);
- After errors occur, will adjust plans based on error information.

**SOLO Mode (taking Trae as an example)**
Means:

> You only need to clearly state the "destination,"
> It plans the "route" itself,
> Executes step by step locally,
> Only asks whether to continue at key nodes midway.

### 2. Model, Key (API Key)

**Model (Model, here specifically referring to large language models)**
This word can be simply understood as "that big AI brain behind it":

- Such as GPT, Claude, Kimi, GLM, etc.;
- Different models have different levels in "understanding Chinese," "writing code," "reasoning";
- AI IDEs usually allow switching between different models in dropdown menus.

**Key / API Key**
You can understand an API Key as **a very long "advanced password + ID number,"**
Its only function is:

> Tell someone else's server: "I'm which user, please allow me to use your AI service, and help me keep accounts."

Key points:

- This thing is usually a long string of random letters and numbers;
- Can't be sent to public places (repositories, screenshots, group chats), others can impersonate your account if they get it;
- Filling in the API Key in the tool is like "inserting the key into the lock," after which the tool can help you call the corresponding AI service.

## <span id="term-browser">[8. Words Related to "Browser / Developer Tools"](#appendix-1-map)</span>

**Chrome (Google Browser)**
One of the most commonly used browsers for frontend development now:

- Opens webpages fast;
- Comes with relatively strong "developer tools" for easy problem checking.

**Refresh (Refresh / Reload)**
Reload the current webpage:

- After modifying frontend code, if there are no automatic refresh tools, you need to manually refresh to see the effect.

**Developer Tools (DevTools)**
A set of tool panels in the browser specifically for developers:

- View webpage structure (Elements);
- View styles (Styles);
- Check errors and logs (Console);
- Check network requests (Network).
  In Chrome, usually opened by pressing `F12` or `Ctrl+Shift+I`.

**Console (Console)**
A tab in developer tools, specifically displaying:

- The output of your `console.log(...)`;
- Errors that occurred during operation (red text).
  You can think of it as "the program's chat box":
- When the program has something to say, it writes here;
- This is what you most often look at when debugging.

If you encounter new words in the learning process later, you can also have AI assist you in supplementing all content in this style:

- First write a sentence about "what it does";
- Then write a sentence about "what you can imagine it as";
- Finally give a particularly simple small example.
  This way your "personal glossary" will grow longer and more practical, gradually enabling better communication with computers.

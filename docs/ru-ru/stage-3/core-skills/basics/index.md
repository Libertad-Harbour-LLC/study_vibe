# Базовое руководство по быстрому старту с Claude Code

Claude Code — это официальный AI-нативный инструмент для программирования от Anthropic. Он интегрирует возможности больших языковых моделей прямо в терминал, так что вы можете выполнять задачи программирования, сотрудничая с ИИ на естественном языке. В отличие от традиционных инструментов автодополнения кода, Claude Code понимает контекст всего проекта и выполняет сложные задачи разработки. От генерации кода до рефакторинга, от отладки до написания документации — он справляется со всем этим.

Эта глава поможет вам быстро освоить основные приёмы работы с Claude Code, включая установку и настройку, базовые операции, практические техники и часто используемые команды. Используете ли вы инструмент для ИИ-программирования впервые или хотите работать с Claude Code эффективнее — здесь вы найдёте то, что вам нужно.

---

## Быстрая установка

Claude Code построен на Node.js, поэтому перед установкой убедитесь, что в вашей системе установлен Node.js версии 18 или выше. Процесс очень прост и обычно занимает всего несколько минут.

### Зачем вам нужен Claude Code

В традиционных рабочих процессах разработки разработчики постоянно переключаются между редактором, терминалом, браузером и документацией. Claude Code объединяет эти процессы в одном интерфейсе: в одном и том же окне терминала вы можете писать код, запускать тесты, читать документацию и даже взаимодействовать с коллегами. Что ещё важнее, он понимает структуру вашего проекта и запоминает ваши привычки в написании кода, становясь настоящим помощником в программировании.

### Способ 1: Ручная установка

Ручная установка подходит разработчикам, которые любят полностью контролировать каждый шаг, и она также помогает чётко понять компоненты инструмента.

```bash
# Install Claude Code CLI globally
# Use -g to install command globally, so it can be used in any directory
npm install -g @anthropic-ai/claude-code

# Verify installation
# If version is shown (for example 0.1.25), installation succeeded
claude --version
```

В процессе установки npm автоматически скачивает зависимости и настраивает переменные окружения. Если вы столкнулись с проблемами прав доступа, попробуйте `sudo` (macOS/Linux) или запустите терминал от имени администратора (Windows).

### Способ 2: Поручите установку ИИ-агенту

Если вы уже используете другие ИИ-помощники для программирования (например, Cursor, Windsurf или ИИ-агента в этом проекте), вы можете поручить им выполнить установку за вас. Преимущество в том, что ИИ может автоматически определить ваше окружение, разрешить конфликты зависимостей и выбрать оптимальный способ установки для вашей системы.

**Вы можете просто сказать:**

```text
Help me install Anthropic Claude Code.
```

Или более конкретно:

```text
Install Claude Code CLI and check whether my Node.js version is compatible.
```

ИИ-агент выполнит следующее:
1. Проверит текущую версию Node.js
2. Предложит обновить её, если требования не выполнены
3. Запустит команды установки
4. Проверит результат установки
5. Попробует автоматически исправить ошибки, если они возникнут

### Первый запуск и инициализация

После установки перейдите в каталог вашего проекта и запустите Claude Code:

```bash
# Enter project directory (Claude Code works in current directory)
cd /path/to/your/project

# Start Claude Code
claude
```

При первом запуске Claude Code проведёт вас через несколько важных шагов настройки:

1. **Вход в аккаунт Anthropic**: для использования Claude Code вам нужен аккаунт Anthropic. Если у вас его нет, вам будет предложено зарегистрироваться.
2. **Выбор тарифного плана**:
   - **Бесплатный план**: подходит для личного обучения и лёгкого использования, с ограничениями на количество запросов
   - **План Pro**: подходит для профессиональных разработчиков, с большей квотой и приоритетным временем отклика
3. **Принятие условий**: прочитайте и примите условия использования и политику конфиденциальности Anthropic
4. **Опционально: настройка ключа API**: если у вас есть собственный ключ (например, от стороннего провайдера), настройте его здесь

::: info Особое замечание для пользователей из материкового Китая

По сетевым причинам пользователи из материкового Китая могут не иметь возможности напрямую обращаться к официальным сервисам Anthropic. Claude Code поддерживает сторонние сервисы, совместимые с форматом Anthropic API, и это технически осуществимо.

**У вас есть два варианта:**

1. **Использовать токен API напрямую**: купите токен у провайдера, совместимого с Anthropic API, и настройте его через переменные окружения
2. **Использовать тарифный план Coding Plan**: некоторые провайдеры предлагают планы, оптимизированные для программирования, которые обычно более выгодны для сценариев написания кода

**Рекомендуемый подход**: поручите настройку ИИ-агенту. Вам нужно лишь предоставить конфигурационные данные провайдера (адрес API, ключ и т. д.), и ИИ сможет корректно настроить переменные окружения.

**Подробное руководство по настройке:** [Как установить claudecode и настроить переменные окружения](/ru-ru/stage-2/backend/modern-cli/)

:::

---

## Быстрый старт: проведите несколько небольших экспериментов

После установки не спешите браться за серьёзные проекты. Сначала проведите несколько небольших экспериментов, чтобы понять, как работает Claude Code. Эти три эксперимента выстроены от простого к сложному и соответствуют трём ключевым возможностям: понимание естественного языка, генерация контента и выполнение кода.

### Эксперимент 1: Диалог — почувствуйте понимание ИИ

Цель — ощутить, как Claude Code понимает естественный язык. В отличие от обычных поисковых систем, Claude Code понимает контекст, поддерживает многоходовый диалог и корректирует ответы на основе вашей обратной связи.

**Попробуйте такие запросы:**

```text
Hello, who are you?
```

Claude представляется как Claude Code, ИИ-помощник для программирования от Anthropic.

```text
What is a closure? Give me the too-long-didnt-read version.
```

Обратите внимание, как Claude воспринимает подсказку «too-long-didnt-read» и даёт краткое, но точное объяснение.

```text
What is the difference between JavaScript and TypeScript?
```

Это технический вопрос на сравнение. Проверьте, даёт ли Claude структурированный и глубокий ответ.

**Суть эксперимента**: обратите внимание на стиль ответов Claude. Обычно он сначала приводит главный вывод, а затем детали. Этот стиль «перевёрнутой пирамиды» отлично подходит для быстрого получения информации.

### Эксперимент 2: Сгенерируйте документ Markdown — испытайте создание контента

Этот эксперимент демонстрирует способность Claude Code генерировать контент. Для разработчиков написание документации часто бывает мучительным. Claude может быстро сгенерировать понятную и полную документацию на основе требований.

**Введите эту инструкцию:**

```text
Write a Markdown document of commonly used Git commands.
Requirements: include command, explanation, and example.
```

**Что делает Claude:**

1. Анализирует ваше требование: распространённые команды Git, формат Markdown и три элемента (команда/объяснение/пример)
2. Планирует структуру документа: обычно сгруппированную по сценариям использования (инициализация, ежедневная разработка, работа с ветками, удалённое взаимодействие и т. д.)
3. Генерирует контент: краткое объяснение и практичные примеры для каждой команды
4. Форматирует вывод: использует синтаксис Markdown и правильную структуру

**Пример ожидаемого вывода**:

```markdown
# Common Git Command Cheat Sheet

## Initialize Repository

| Command | Explanation | Example |
|------|------|------|
| `git init` | Initialize new repository | `git init my-project` |
| `git clone` | Clone remote repository | `git clone https://github.com/user/repo.git` |

...
```

**Дополнительные попытки**: вы можете добавлять дополнительные требования, например «добавь комментарии на русском», «отсортируй по частоте использования», «включи обработку распространённых ошибок» и т. д., и наблюдать, как Claude адаптирует вывод.

### Эксперимент 3: Напишите и запустите игру — сквозной процесс программирования

Это самый сложный эксперимент. Он демонстрирует полный рабочий процесс Claude Code: понять требование, написать код, создать файлы, запустить программу и обработать ошибки. Благодаря ему вы по-настоящему ощутите мощь ИИ-помощника для программирования.

**Введите эту инструкцию:**

```text
Write a Snake game in Python.
Requirements:
1. Use pygame
2. Show score
3. Press ESC to exit

After writing, help me run it.
```

**Claude выполняет следующие шаги:**

**Шаг 1: Проверка окружения**
- Проверяет, установлен ли Python
- Проверяет, доступен ли pygame
- Предлагает установить, если его нет

**Шаг 2: Написание кода**
- Создаёт точку входа игры (например, `snake_game.py`)
- Реализует движение, генерацию еды, обнаружение столкновений
- Добавляет отображение счёта
- Реализует выход по ESC

**Шаг 3: Запуск игры**
- Выполняет Python-скрипт и запускает игру
- Открывается окно игры, управляйте змейкой стрелками

**Шаг 4: Дальнейшая поддержка**
- Если есть баг, вы можете прямо сказать «змейка проходит сквозь стены, исправь это»
- Если хотите больше функций, например «увеличивай сложность с ростом счёта», Claude может продолжать вносить изменения

**Ценность этого эксперимента:**

1. **Проверка настройки**: убедитесь, что Claude Code корректно выполняет код
2. **Опыт взаимодействия**: почувствуйте совместную разработку с ИИ
3. **Уверенность**: увидите, как ИИ создаёт готовую к запуску программу от начала до конца

**Частые вопросы:**

- **В: Что делать, если pygame не установлен?**
  - О: Claude обнаружит это и предложит `pip install pygame`, или вы можете попросить Claude установить его

- **В: После запуска игры терминал занят, что делать?**
  - О: Нажмите ESC, чтобы выйти из игры, или продолжайте использовать Claude Code в другом окне терминала

- **В: Можно ли сменить язык?**
  - О: Конечно. Попробуйте «напиши на JavaScript», «сделай с помощью HTML5 Canvas» и т. д.

---

## Основные техники

Освойте эти техники, и эффективность вашей работы с Claude Code может вырасти в несколько раз. Они взяты из реальной практики разработки и охватывают часто встречающиеся сценарии.

### Техника 1: Двойное нажатие Esc для отката диалога — отмена ошибочных действий

Это самое распространённое и важное сочетание клавиш в Claude Code. В процессе совместной работы вы можете опечататься, дать неверную инструкцию или остаться недовольны ответом. Двойное нажатие Esc даёт быструю «перемотку времени назад».

**Подробности о сочетаниях клавиш:**

```text
Press Esc once     -> clear current input (similar to Ctrl+C)
Press Esc twice    -> roll back to previous conversation state (undo previous turn)
Press Esc three times -> clear all conversation history (start over)
```

**Сценарии использования:**

- **Случай A**: вы случайно отправили неверную инструкцию, и Claude начал её выполнять. Быстро нажмите Esc дважды, чтобы вернуться к состоянию до выполнения.
- **Случай B**: ответ Claude — не то, что вы хотели, и вы хотите переформулировать. Двойное Esc, чтобы отменить и спросить заново.
- **Случай C**: в диалоге много ходов и контекст запутался. Тройное Esc, чтобы очистить и начать заново.

**Важное замечание**: двойное Esc откатывает **состояние диалога**, а не изменения в коде. Если Claude уже отредактировал файлы, эти правки не отменяются автоматически. Их нужно восстанавливать вручную через Git.

**Рекомендация**: перед потенциально крупными изменениями кода сохраняйте текущее состояние (`git commit` или `git stash`), чтобы восстановление было простым.

### Техника 2: Используйте @ для ссылки на файлы — точный контроль контекста

Хотя Claude Code может читать файлы проекта автоматически, явная ссылка на файлы делает намерение яснее и позволяет не тратить токены на нерелевантные файлы.

**Базовое использование:**

Вместо расплывчатого:

```text
Explain src/utils.ts
```

Используйте явную ссылку:

```text
@src/utils.ts Explain this file
```

**Продвинутое использование:**

**Сравнение нескольких файлов:**
```text
@src/app.tsx @src/components/Header.tsx What is the relationship between these two files?
```

**Ссылка на каталог:**
```text
@src/components/ Summarize all components under this directory
```

**Ссылка на конкретные строки (с редактором):**
```text
@src/utils.ts:45-60 Explain what this code does
```

**Советы по использованию:**

1. **Автодополнение по Tab**: введите `@`, затем нажмите Tab, Claude покажет список файлов в текущем каталоге, и вы можете выбрать стрелками
2. **Относительные пути**: поддерживаются ссылки вроде `@./config.json` или `@../shared/types.ts`
3. **Нечёткое сопоставление**: допускаются частичные имена файлов, например `@utils` может соответствовать `src/utils.ts` или `src/utils/index.ts`

### Техника 3: Используйте ! для выполнения команд — интеграция с терминалом

В Claude Code встроено выполнение команд. Вы можете запускать команды, не переключаясь в другой терминал.

**Базовое использование:**

```text
!npm test           # run tests
!git status         # check Git status
!ls -la             # list files
```

**Практические сценарии:**

**Сценарий: запуск тестов и анализ сбоя**
```text
!npm test
# after failure
Analyze test failure reasons and fix code
```

**Сценарий: просмотр diff в Git**
```text
!git diff
# ask Claude to explain changes
Summarize key changes in this diff
```

**Сценарий: сборка проекта**
```text
!npm run build
# if build fails
Build failed, help me fix it
```

**Замечание о безопасности:**

Для чувствительных команд (например, `rm -rf`, `sudo`) Claude Code запрашивает подтверждение. Это защитный механизм. Подтверждайте внимательно.

### Техника 4: /plan перед написанием кода — правильный способ работы со сложными задачами

Для сложных задач прямое написание кода часто неэффективно. `/plan` переводит в режим планирования: сначала определяется план реализации, а затем он выполняется шаг за шагом.

**Использование:**

```text
/plan
I want to add user authentication. Please create an implementation plan.
```

**Что делает Claude:**

1. **Анализирует требование**
2. **Оценивает текущий проект и стек технологий**
3. **Создаёт пошаговый план**
4. **Обсуждает и корректирует на основе вашей обратной связи**

**Пример вывода:**

```text
📋 User Authentication Implementation Plan

Phase 1: Database design
- [ ] Create users table (id, email, password_hash, created_at)
- [ ] Create sessions table (id, user_id, expires_at)

Phase 2: Backend API
- [ ] POST /api/auth/register - register
- [ ] POST /api/auth/login - login
- [ ] POST /api/auth/logout - logout
- [ ] GET /api/auth/me - get current user

Phase 3: Frontend integration
- [ ] Create login page
- [ ] Create register page
- [ ] Add route guards

Phase 4: Testing
- [ ] Write unit tests
- [ ] Write integration tests

Which phase do you want to start with? Or should we adjust the plan?
```

**Лучшие практики:**

- Для задач длительностью более 30 минут сначала используйте `/plan`
- Выполняйте фаза за фазой и проверяйте каждую фазу
- Если требования изменились, перезапустите `/plan` для корректировки

### Техника 5: /init автоматически генерирует конфигурацию — быстрая инициализация проекта

`/init` — одна из самых мощных команд Claude Code. Она автоматически сканирует ваш проект, определяет стек технологий и структуру и генерирует полный `CLAUDE.md`.

**Использование:**

```text
/init
```

**Claude выполняет:**

1. **Сканирование структуры проекта**: определяет фреймворк/язык/инструменты сборки
2. **Анализ конфигурационных файлов**: читает package.json, tsconfig.json и т. д.
3. **Определение стиля**: соглашения об именовании и организация файлов
4. **Генерация CLAUDE.md**

**Пример сгенерированного CLAUDE.md:**

```text
# My Project

## Tech Stack
- Framework: Next.js 14 (App Router)
- Language: TypeScript
- Styling: Tailwind CSS
- State: Zustand
- Database: Prisma + PostgreSQL

## Common Commands

\`\`\`bash
npm run dev      # start dev server
npm run build    # production build
npm run test     # run tests
npx prisma migrate dev  # DB migration
\`\`\`

## Code Conventions
- Use function components + Hooks
- File naming: PascalCase (components), camelCase (utility funcs)
- Commit style: Conventional Commits
```

**Почему это важно:**

`CLAUDE.md` — это «память проекта» Claude Code. При каждом запуске Claude читает этот файл и понимает контекст проекта. Это означает:

- вам не нужно повторно объяснять фреймворк и стек технологий
- Claude следует вашим соглашениям и лучшим практикам
- новые члены команды могут быстрее влиться в работу

**Рекомендация**: после инициализации проекта сразу запустите `/init`, затем доработайте сгенерированную конфигурацию, чтобы она соответствовала реальности.

### Техника 6: /compact сжимает контекст — экономия токенов

Окно контекста Claude Code ограничено (часто около 200K токенов). Длинные диалоги потребляют много токенов, увеличивают стоимость и могут вытеснить важную раннюю информацию из контекста.

**Использование:**

```text
/compact
```

**Как это работает:**

`/compact` анализирует историю чата, извлекает ключевую информацию (принятые решения, сгенерированный код, подтверждённые требования) и создаёт краткое резюме. Дальнейший диалог основывается на этом резюме, а не на полной истории.

**Когда использовать:**

- после 5-6 ходов
- когда Claude как будто «забывает» предыдущий контекст
- при переходе к новой подзадаче с сохранением ключевого фона

**Рекомендация:**

```text
# compress after long conversation
/compact

# keep working
Now that user module is done, let's build order module.
```

### Техника 7: Используйте Claude Code для помощи с коммитами в Git

В Claude Code рекомендуемый процесс коммита таков: позвольте Claude изучить diff и составить сообщение коммита, а затем сами выполните стандартные команды Git. Это понятно и даёт вам ещё одну точку проверки перед коммитом.

Официальные источники:

- [Встроенные команды](https://code.claude.com/docs/ru-ru/commands)
- [Поиск плагинов](https://code.claude.com/docs/ru-ru/discover-plugins)

**Рекомендуемый рабочий процесс:**

```bash
# 1. Check current changes
/diff
!git status

# 2. Ask Claude to summarize and generate commit message
Based on current git diff, generate a Conventional Commits message,
and explain in Chinese why this category is appropriate.

# 3. After you confirm, run standard Git commit
!git add -A
!git commit -m "feat(docs): update Claude Code workflow guidance"
```

**Преимущества этого подхода:**

1. **Соответствует текущим официальным возможностям**: нет зависимости от удалённых встроенных команд
2. **Прозрачность**: проверка diff и сообщения коммита перед отправкой
3. **Переносимость**: тот же процесс работает в других ИИ-IDE или в чистом Git

**Если вам нужен опыт «коммита одной командой»:**

Claude Code теперь рекомендует расширение на основе плагинов. Например, `commit-commands` предоставляет команды вроде `/commit-commands:commit`.

```bash
# 1. Add plugin marketplace example
/plugin marketplace add anthropics/claude-code

# 2. Install commit workflow plugin
/plugin install commit-commands@anthropics-claude-code

# 3. Reload plugins
/reload-plugins

# 4. Use plugin command to commit
/commit-commands:commit
```

**Дополнительные замечания:**

- `/commit-commands:commit` предоставляется плагином, а не текущей встроенной командой по умолчанию
- если вам нужно лишь изучить изменения перед коммитом, используйте `/diff` или попросите Claude объяснить `git diff`
- официальная команда `/review` также помечена как устаревшая; для аналогичных возможностей используйте плагин или процесс проверки на естественном языке

### Техника 8: Shift+Tab — автоприём, повышение плавности работы

По умолчанию Claude запрашивает подтверждение перед редактированием кода. Это полезно на этапе обучения, но позже может казаться медленным. `Shift+Tab` включает режим автоприёма для более быстрой итерации.

**Использование:**

- нажмите `Shift+Tab` -> вход в режим автоприёма
- нажмите `Shift+Tab` снова -> выход из режима автоприёма

**Сравнение режимов:**

| Режим | Поведение | Сценарий использования |
|------|------|----------|
| Режим по умолчанию | Запрашивать подтверждение для каждой правки | Этап обучения, важный код |
| Автоприём | Применять правки сразу | После освоения, быстрая итерация |

**Замечания:**

- В режиме автоприёма Claude редактирует файлы напрямую без повторного подтверждения
- Рекомендуется сочетать с Git, чтобы откат был простым
- Для чувствительных операций (удаление файлов, изменение ключевых конфигураций) Claude всё равно спрашивает

### Техника 9: Ctrl+C для отмены операции — экстренный тормоз

Когда Claude выполняет длительную задачу или вы поняли, что дали неверную инструкцию, `Ctrl+C` — это экстренный тормоз.

**Использование:**

- нажмите `Ctrl+C` один раз -> отменить текущую выполняемую операцию
- нажмите `Ctrl+C` дважды -> полностью выйти из Claude Code

**Сценарии использования:**

- нужно прервать долго выполняющуюся команду
- Claude генерирует большой нерелевантный код
- обнаружена неверная инструкция, и вы хотите немедленно остановиться

**Отличие от двойного Esc:**

- `Ctrl+C`: остановить текущую **операцию** (выполняющуюся команду / генерацию кода)
- `двойное Esc`: откатить **состояние диалога** (отменить предыдущий ход)

### Техника 10: /context — проверка использования контекста, оптимизация затрат на токены

`/context` отображает использование контекста в текущей сессии, помогая понять расход токенов и оптимизировать затраты.

**Использование:**

```text
/context
```

**Пример вывода:**

```text
📊 Context Usage

Token usage: 45,230 / 200,000 (22.6%)
File references: 12 files
Conversation rounds: 8

Top token-consuming files:
1. src/api/users.ts (3,420 tokens)
2. node_modules/@types/react/index.d.ts (2,890 tokens)
3. src/components/Dashboard.tsx (1,560 tokens)

Suggestions:
- Current usage is healthy, no compression needed
- To reduce usage, add node_modules into .claudeignore
```

**Как использовать эту информацию:**

1. **Выявление больших файлов**: если один файл потребляет много токенов, проверьте, действительно ли он нужен
2. **Оптимизация .claudeignore**: игнорируйте нерелевантные файлы (node_modules, артефакты сборки и т. д.)
3. **Решение о сжатии**: когда использование превышает 70%, рассмотрите `/compact`

### Техника 11: /resume — восстановление сессии, переключение между многозадачными диалогами

При работе над несколькими задачами вы можете вести несколько потоков диалога. `/resume` позволяет вернуться к контексту предыдущей сессии в текущем чате, без перезапуска.

**Использование:**

```text
/resume
```

**Как это работает:**

Claude Code автоматически записывает предыдущие сессии. Когда вы запускаете `/resume`, он переключается на контекст предыдущей сессии и сохраняет всё содержание и состояние прежнего обсуждения.

**Сценарии использования:**

**Случай A: параллельная многозадачность**
```text
# Task 1: fix bug
claude> Fix login-page validation issue
# ... one conversation ...

# Task 2: add feature (new thread)
claude> Add user registration feature
# ... another conversation ...

# Switch back to task 1
claude> /resume
# Continue previous bug-fix work
```

**Случай B: временный поиск, затем возврат**
```text
claude> Explain this algorithm
# ... discuss algorithm ...

claude> /resume
# Return to previous coding work
```

**Случай C: возобновление после прерывания**
```text
claude> Continue previous work
# If you interrupted before, /resume brings you back
```

**Сравнение со связанными командами:**

| Команда | Функция | Сценарий |
|------|------|----------|
| `/resume` | Вернуться к предыдущей сессии в текущем чате | Переключение между задачами |
| `claude -c` | Продолжить самую последнюю сессию | Повторное подключение после выхода |
| `claude -r` | Восстановить предыдущую сессию | Восстановление прежнего состояния после выхода |
| `двойное Esc` | Откатить один ход | Отменить самый последний ход диалога |

**Рекомендации:**

1. **Управление многозадачностью**: `/resume` эффективнее, чем повторное объяснение контекста
2. **Память сессий**: каждая сессия имеет независимый контекст; `/resume` его сохраняет
3. **Использование с /compact**: в длинных сессиях сначала сожмите, затем переключитесь через resume, чтобы контекст оставался чистым

---

## Основная конфигурация

Разумная конфигурация помогает Claude Code лучше подходить вашему проекту и команде. В этом разделе объясняются роль конфигурации, приоритеты и оптимизация для разных сценариев использования.

### Расположение файлов конфигурации и приоритет

Claude Code использует многоуровневую стратегию конфигурации. У разных уровней разные область действия и приоритет. Понимание этого позволяет гибко управлять настройками.

**Приоритет конфигурации (от высокого к низкому):**

| Расположение | Область | Назначение | Коммитить в Git |
|------|--------|------|--------------|
| `.claude/settings.local.json` | локальный проект | личные предпочтения | ❌ нет |
| `.claude/settings.json` | общий для проекта | конфигурация для всей команды | ✅ да |
| `~/.claude/settings.json` | глобальный | личные значения по умолчанию | ❌ нет |

**Правила слияния:**

- Конфигурация с более высоким приоритетом переопределяет тот же ключ с более низким приоритетом
- Непротиворечивые ключи объединяются
- Конфигурация проекта переопределяет глобальную конфигурацию
- Локальная личная конфигурация переопределяет общую конфигурацию проекта

**Практические сценарии:**

**Сценарий 1: командный проект**
```text
~/.claude/settings.json          # your personal default editor settings
.claude/settings.json            # team coding standards and permission config
.claude/settings.local.json      # your debug preferences and theme settings
```

**Scenario 2: personal project**
```text
~/.claude/settings.json          # global default config
.claude/settings.json            # project-specific config (e.g. special permission rules)
```

### CLAUDE.md — память проекта

`CLAUDE.md` — самый важный файл конфигурации Claude Code. Он играет роль «руководства» по проекту. При каждом запуске Claude Code читает `CLAUDE.md` в текущем каталоге, понимая контекст, стек технологий и соглашения.

**Почему CLAUDE.md так важен:**

Представьте, что вы присоединяетесь к новому проекту: вам нужно изучить стек технологий, соглашения о написании кода и распространённые команды. Обычно это занимает часы изучения документации/кода и расспросов коллег. С `CLAUDE.md` Claude знает всё это при запуске, и вы можете сразу же эффективно работать вместе.

**Минимальный жизнеспособный шаблон:**

```text
# [Project Name]

## Tech Stack
- Framework: React 18 + TypeScript
- State: Zustand
- Styling: Tailwind CSS
- Build tool: Vite

## Common Commands

\`\`\`bash
npm run dev      # start development server (port 5173)
npm run test     # run unit tests
npm run build    # production build
npm run lint     # lint checks
\`\`\`

## Code Conventions
- Components use function components + Hooks
- Naming: PascalCase (components), camelCase (utility funcs)
- Git commits use Conventional Commits
- All API calls must go through unified request wrapper
```

**Полный шаблон (рекомендуется):**

```text
# [Project Name]

## Project Overview
One-sentence description of main functionality and target users.

## Tech Stack
### Frontend
- Framework: React 18 + TypeScript
- Router: React Router v6
- State: Zustand + React Query
- Styling: Tailwind CSS + Headless UI
- Build: Vite

### Backend (if applicable)
- Runtime: Node.js + Express
- Database: PostgreSQL + Prisma
- Auth: JWT + bcrypt

## Project Structure

\`\`\`
src/
├── components/      # reusable components
├── pages/           # page components
├── hooks/           # custom Hooks
├── lib/             # utility functions
├── types/           # TypeScript types
└── api/             # API calls
\`\`\`

## Common Commands

\`\`\`bash
# development
npm run dev              # start dev server
npm run dev:mock         # use mock data in development

# testing
npm run test             # run all tests
npm run test:watch       # watch mode
npm run test:coverage    # generate coverage report

# code quality
npm run lint             # ESLint check
npm run lint:fix         # auto-fix ESLint issues
npm run format           # Prettier format
npm run typecheck        # TypeScript type check

# build
npm run build            # production build
npm run preview          # preview production build
\`\`\`

## Development Rules
### Code style
- Use function components, avoid class components
- Prefer custom Hooks for logic abstraction
- Component props must define TypeScript interfaces

### Git workflow
- Branch prefix: `feature/`, `fix/`, `refactor/`
- Commit messages follow Conventional Commits
- PR must pass CI and code review

### Performance requirements
- Component lazy loading to reduce first-screen load time
- Use WebP images and enable lazy loading
- Keep API response time under 200ms

## Environment Variables

\`\`\`bash
# .env.local
VITE_API_BASE_URL=http://localhost:3000
VITE_APP_NAME=MyApp
\`\`\`

## Common Issues

### Dev server failed to start?

Check whether port 5173 is occupied, or try `npm run dev -- --port 3000`

### Type errors?

Run `npm run typecheck` to see detailed errors
```

**Быстрая генерация CLAUDE.md:**

Если ваш проект существует, но в нём нет `CLAUDE.md`, запустите `/init`:

```bash
claude
# inside Claude Code
/init
```

Claude анализирует структуру проекта, package.json и текущий код, затем генерирует практичный `CLAUDE.md`. После генерации просмотрите и скорректируйте его вручную.

### .claudeignore — экономия токенов

`.claudeignore` сообщает Claude Code, какие файлы не следует читать в контекст. Правильная настройка может значительно сократить расход токенов (часто на 40-60%) и повысить скорость отклика.

**Зачем нужен .claudeignore:**

Когда Claude Code пытается понять проект, он читает связанные файлы. Некоторые файлы не помогают пониманию и могут:
- потреблять много токенов (например, файлы определений типов в node_modules)
- вносить шум (логи, артефакты сборки)
- содержать чувствительную информацию (файлы .env)

**Рекомендуемая конфигурация:**

```text
# ===== dependencies =====
# huge third-party code, usually unnecessary for Claude context
node_modules/
.pnp/
.pnp.js

# ===== build outputs =====
# generated artifacts, not source logic
dist/
build/
.next/
out/
*.tsbuildinfo

# ===== logs =====
# runtime logs, no value for understanding architecture
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*
pnpm-debug.log*
lerna-debug.log*

# ===== testing outputs =====
coverage/
.nyc_output/

# ===== editor / IDE =====
.vscode/*
!.vscode/extensions.json
.idea/
*.suo
*.ntvs*
*.njsproj
*.sln
*.sw?

# ===== system files =====
.DS_Store
Thumbs.db

# ===== env files =====
.env
.env.local
.env.*.local

# ===== large binary assets =====
*.png
*.jpg
*.jpeg
*.gif
*.svg
*.ico
*.mp4
*.webm

# ===== lock files (optional) =====
# If you do not need Claude to analyze dependency versions, ignore these
# package-lock.json
# yarn.lock
# pnpm-lock.yaml
```

**Советы по конфигурации:**

1. **Начните с минимума**: сначала игнорируйте node_modules и артефакты сборки, затем наблюдайте за расходом токенов
2. **Настраивайте под проект**: проект с большим количеством изображений -> игнорируйте форматы изображений; проект документации -> сохраняйте Markdown
3. **Регулярно оптимизируйте**: используйте `/context`, чтобы увидеть файлы, потребляющие больше всего токенов, и решить, стоит ли их игнорировать

### Конфигурация прав доступа

По умолчанию Claude Code запрашивает подтверждение перед чувствительными операциями. Через раздел `permissions` в `settings.json` вы можете управлять тем, какие действия разрешаются автоматически, требуют подтверждения или полностью запрещены.

**Структура конфигурации прав доступа:**

```json
{
  "permissions": {
    "allow": [
      // auto-allow without asking
    ],
    "ask": [
      // ask before execution
    ],
    "deny": [
      // fully deny
    ]
  }
}
```

**Синтаксис правил:**

Правила прав доступа используют формат `ActionType(pattern)`:

| Тип действия | Описание | Пример |
|----------|------|------|
| `Bash` | выполнить команду в терминале | `Bash(git status)` |
| `Edit` | редактировать файл | `Edit(src/**/*.ts)` |
| `Read` | читать файл | `Read(README.md)` |
| `Write` | создать файл | `Write(src/components/*.tsx)` |

**Поддержка подстановочных символов:**

- `*` соответствует произвольным символам (кроме `/`)
- `**` соответствует произвольным путям
- `?` соответствует одному символу

**Пример реальной конфигурации:**

```json
{
  "permissions": {
    "allow": [
      "Bash(git status)",
      "Bash(git log:*)",
      "Bash(git diff:*)",
      "Bash(npm test:*)",
      "Bash(npm run lint:*)",
      "Edit(src/**/*.{ts,tsx})",
      "Edit(tests/**/*.test.ts)",
      "Read(src/**/*.ts)",
      "Write(src/components/*.tsx)"
    ],
    "ask": [
      "Bash(git commit:*)",
      "Bash(git push:*)",
      "Bash(git pull:*)",
      "Bash(npm install:*)",
      "Bash(npm run build)",
      "Edit(package.json)",
      "Edit(tsconfig.json)",
      "Read(.env)",
      "Read(config/secrets.*)"
    ],
    "deny": [
      "Bash(rm -rf:*)",
      "Bash(sudo:*)",
      "Bash(curl * | sh)",
      "Bash(wget * | sh)",
      "Edit(.git/*)",
      "Write(/etc/*)",
      "Read(/etc/passwd)"
    ]
  }
}
```

**Рекомендации по конфигурации:**

1. **Этап разработки**: относительно мягкие права доступа для более быстрой итерации
2. **Этап продакшена**: более строгие права доступа, особенно для операций развёртывания и работы с чувствительными данными
3. **Командная работа**: разместите базовые правила в общем `settings.json`, личные настройки — в `settings.local.json`

### Каталог Rules

В крупных проектах единый `CLAUDE.md` может стать раздутым и трудным в сопровождении. Claude Code поддерживает модульное управление через **каталог Rules**, разбивая соглашения по темам на отдельные файлы.

**Структура каталога:**

```text
.claude/
├── settings.json          # main config file
├── CLAUDE.md              # project overview (still needed)
└── rules/                 # rules directory
    ├── 00-security.md     # security rules (global)
    ├── 01-coding-style.md # coding style rules (global)
    ├── 10-api.md          # API dev rules
    ├── 11-frontend.md     # frontend dev rules
    ├── 12-backend.md      # backend dev rules
    └── 20-testing.md      # testing rules
```

**Рекомендация по именованию файлов:**

Используйте числовые префиксы (`00-`, `01-`) для контроля порядка загрузки: сначала базовые правила, затем специфические.

**Формат файла правил:**

Файлы правил поддерживают YAML frontmatter для определения применимости:

```markdown
---
# Optional: paths where this rule applies
globs:
  - "src/api/**/*.ts"
  - "src/services/**/*.ts"

# Optional: commands where this rule applies
commands:
  - "generate api"
  - "create endpoint"

# Optional: rule priority (smaller number = higher priority)
priority: 10
---

# API Development Rules

## Route design
- RESTful style, use plural nouns
- Versioning: /api/v1/users
- Nested resources: /api/v1/users/123/orders

## Request/response format
- Use JSON consistently
- Error response must include code and message
- Pagination response uses { data, pagination } structure

## Security requirements
- All endpoints must verify authentication (except public endpoints)
- Sensitive operations require secondary confirmation
- Implement rate limiting to prevent abuse
```

**Наследование и переопределение правил:**

- Глобальные правила (без frontmatter или с `globs: *`) применяются ко всем файлам
- Правила для конкретных путей применяются только к соответствующим файлам
- При конфликте правил побеждает правило с более высоким приоритетом
- Специфические правила могут переопределять глобальные правила

**Примеры сценариев использования:**

**Сценарий 1: проект с разделением фронтенда и бэкенда**
```text
.claude/rules/
├── 00-general.md          # general standards (commit message, naming)
├── 10-backend.md          # backend standards (NestJS-specific)
├── 11-frontend.md         # frontend standards (React-specific)
└── 20-database.md         # database standards (Prisma-specific)
```

**Сценарий 2: микросервисная архитектура**
```text
.claude/rules/
├── 00-global/             # global rules
│   ├── security.md
│   └── logging.md
├── 10-services/           # service-specific rules
│   ├── user-service.md
│   ├── order-service.md
│   └── payment-service.md
└── 20-shared/             # shared component rules
    ├── shared-lib.md
    └── common-utils.md
```

**Рекомендация по миграции:**

Если у вас уже есть очень большой `CLAUDE.md`, перенесите его в каталог Rules следующим образом:

1. Создайте `.claude/rules/`
2. Разбейте `CLAUDE.md` по темам
3. Добавьте подходящий frontmatter в каждый файл правил
4. Сохраните `CLAUDE.md` как обзор проекта и вынесите подробные стандарты наружу
5. Протестируйте и убедитесь, что правила загружаются корректно

---

## Основные команды для работы

Claude Code предоставляет богатый набор операционных команд для эффективного взаимодействия с ИИ. Эти команды делятся на категории: Slash-команды (встроенные функции), система символов (короткие операции) и инструкции на естественном языке (повседневная разработка).

### Краткий справочник по Slash-командам

Slash-команды — это встроенные операции, начинающиеся с `/`. Они предоставляют стандартизированные действия, такие как инициализация проекта, управление конфигурацией и проверка статуса.

| Команда | Функция | Сценарий использования |
|------|------|----------|
| `/help` | Показать все команды | быстрый поиск, когда вы забыли команды |
| `/init` | Инициализировать проект и сгенерировать CLAUDE.md | новый проект или добавление конфигурации |
| `/plan` | Войти в режим планирования | создать план перед сложными задачами |
| `/clear` | Очистить историю диалога | начать заново, когда контекст запутался |
| `/compact` | Сжать контекст | сэкономить токены после долгого чата |
| `/diff` | Открыть интерактивный просмотр diff | изучить текущие незакоммиченные изменения |
| `/plugin` | Управлять плагинами | установить расширения для коммитов/проверки |
| `/context` | Показать использование контекста | оптимизировать затраты на токены |
| `/cost` | Показать стоимость сессии | мониторинг стоимости использования |
| `/config` | Открыть панель конфигурации | обновить настройки |
| `/permissions` | Управление правами доступа | настроить права на операции |
| `/model` | Переключить модель | выбрать другие модели |

**Пример комбинации команд:**

```bash
# complete development workflow
/plan                    # 1. create plan
# ... execute development ...
/diff                    # 2. inspect changes
Generate a commit message from current diff
!git add -A              # 3. stage changes
!git commit -m "..."     # 4. commit
/cost                    # 5. check cost
```

### Система символов

Система символов — это механизм сокращённых операций в Claude Code. Специальные символы быстро запускают определённые возможности.

| Символ | Название | Назначение | Пример |
|------|------|------|------|
| `/` | Slash-команда | выполнить встроенную операцию | `/help`, `/plan` |
| `@` | Ссылка через At | ссылка на файл/каталог | `@src/app.tsx` |
| `!` | Режим Bang | выполнить команду в терминале | `!npm test` |
| `&` | Фоновый запуск | запустить задачу в фоне | `&npm run dev` |

**Советы по комбинированию символов:**

```bash
# combine symbols
@src/utils.ts !npm test
# meaning: read utils.ts, then run tests

@src/components/ @src/pages/ compare structures of these two directories
# meaning: reference two directories simultaneously for comparison

!git diff @src/app.tsx explain these changes
# meaning: inspect Git diff and ask Claude to explain specific file changes
```

### Операции с файлами

Операции с файлами — самые распространённые повседневные действия: чтение, редактирование, создание и удаление файлов.

**Чтение файлов:**

```bash
# basic read
@src/app.tsx explain this file

# read + analyze
@src/utils/helpers.ts find potential performance issues

# compare read
@src/components/OldButton.tsx @src/components/NewButton.tsx compare differences
```

**Редактирование файлов:**

```bash
# simple edit
Modify formatDate in src/utils/date.ts to support Chinese locale format

# complex edit
@src/api/users.ts Refactor this file:
1. Extract duplicated error handling into shared handleError
2. Replace Promise chains with async/await
3. Add JSDoc comments

# batch edit
Convert all class components under src/components/ into function components
```

**Создание файлов:**

```bash
# create one file
Create src/components/UserCard.tsx, a card component to display user info

# create related files
Create user module:
1. src/types/user.ts - define User interface
2. src/api/users.ts - user API calls
3. src/components/UserCard.tsx - user card component
4. src/hooks/useUser.ts - hook to fetch user data
```

**Удаление файлов:**

```bash
# delete with confirmation
Delete src/old-component.tsx (this component is no longer used)

# Claude asks for confirmation and may suggest checking references first
```

### Операции с Git

Claude Code глубоко интегрирован с Git, так что вы можете выполнять полный процесс контроля версий, не покидая терминал.

**Проверка статуса:**

```bash
# show Git status
Show git status and uncommitted changes

# detailed diff
!git diff
Explain changes in src/api/users.ts
```

**Создание коммитов:**

```bash
# inspect changes
/diff

# generate commit message
Generate a Conventional Commit message from current git diff

# commit manually
!git add -A
!git commit -m "..."
```

**Операции с ветками:**

```bash
# create feature branch
!git checkout -b feature/user-authentication

# after implementation
Generate commit message based on current changes
!git add -A
!git commit -m "..."
!git push -u origin feature/user-authentication
```

**Пример полного рабочего процесса Git:**

```bash
# 1. start new feature
!git checkout -b feature/payment-integration

# 2. develop feature (with Claude assistance)
Create payment module with Alipay and WeChat Pay

# 3. run tests
!npm test

# 4. inspect changes
/diff

# 5. generate and confirm commit message
Generate a Conventional Commit message from current git diff
!git add -A
!git commit -m "..."

# 6. push remote
!git push -u origin feature/payment-integration

# 7. create PR (optional, with GitHub CLI)
!gh pr create --title "feat: add payment integration" --body "Support Alipay and WeChat Pay"
```

### Операции с кодом

Операции с кодом — основные сильные стороны Claude Code: генерация, объяснение, рефакторинг и оптимизация.

**Генерация кода:**

```bash
# generate component
Create a React Hook to manage auth state, including login/logout/permission checks

# generate utility function
Create a date-formatting utility that supports relative time (e.g. "2 hours ago")

# generate complete module
Create order module with:
- order list page
- order detail page
- create-order API
- order status management
```

**Объяснение кода:**

```bash
# line-by-line explanation
Explain src/algorithms/quicksort.ts line by line

# high-level explanation
@src/services/payment.ts explain architecture design of this module

# explain complex logic
Explain what reduce in src/utils/dataTransformer.ts is doing
```

**Рефакторинг кода:**

```bash
# architecture refactor
Convert class components in src/components/ to function components

# performance refactor
Optimize rendering performance in src/App.tsx, reduce unnecessary re-renders

# cleanup refactor
@src/utils/helpers.ts Refactor this file:
1. Delete unused functions
2. Extract repeated logic into shared utilities
3. Add type definitions
4. Improve function naming
```

**Отладка кода:**

```bash
# error analysis
npm test failed, analyze root cause and fix it

# performance analysis
@src/components/DataTable.tsx This component renders slowly, find bottlenecks

# log analysis
!cat logs/error.log
Analyze these error logs and identify root cause
```

### Операции с тестами

Тестирование необходимо для обеспечения качества. Claude Code может помочь сгенерировать тесты, запустить их и проанализировать результаты.

**Генерация тестов:**

```bash
# unit tests
Generate unit tests for src/utils/math.ts, including boundary cases

# component tests
Generate React Testing Library tests for src/components/UserForm.tsx

# integration tests
Create integration test for user registration flow from form submission to DB write
```

**Запуск и отладка тестов:**

```bash
# run tests
!npm test

# debug failed tests
Analyze failure reasons and fix
@tests/auth.test.ts

# coverage check
!npm run test:coverage
Which code paths are not covered?
```

**Рекомендация по стратегии тестирования:**

```bash
I added user authentication. Please:
1. Generate unit tests for auth.service.ts
2. Generate component tests for LoginForm
3. Run all tests and ensure pass
```

### Цепочки команд и составление рабочих процессов

Самый эффективный способ использования Claude Code — объединение команд в полноценные рабочие процессы.

**Сценарий 1: процесс исправления багов**

```bash
# 1. inspect issue
!npm test
Tests failed, analyze why

# 2. locate issue
@src/utils/validation.ts Is the issue in this file?

# 3. fix issue
Fix isEmail in validation.ts to correctly handle addresses containing +

# 4. verify fix
!npm test

# 5. commit fix
Generate a fix-type commit message from current diff
!git add -A
!git commit -m "fix: ..."
```

**Сценарий 2: процесс ревью кода**

```bash
# 1. inspect changes
!git diff --stat
Which files changed?

# 2. detailed review
@src/components/ Review these component changes

# 3. suggest improvements
What improvements should be made based on this review?

# 4. implement improvements
Optimize performance of UserList component

# 5. final review
/diff
Review current changes and point out potential risks and improvements
```

**Сценарий 3: процесс разработки новой функции**

```bash
# 1. plan first
/plan
I want to add shopping cart feature

# 2. create branch
!git checkout -b feature/shopping-cart

# 3. implement feature
Implement step by step according to plan

# 4. add tests
Generate tests for shopping cart module

# 5. run tests
!npm test

# 6. code review
/diff
Please do a code review on current diff

# 7. commit
Generate commit message for this feature development
!git add -A
!git commit -m "feat: ..."
!git push
```

---

## Часто задаваемые вопросы

При использовании Claude Code вы можете столкнуться с различными проблемами. В этом разделе собраны распространённые проблемы и их решения.

### Токены расходуются слишком быстро?

Быстрый расход токенов — одна из самых распространённых проблем. Ниже приведена полная стратегия оптимизации.

**Диагностика:**

Сначала запустите `/context`, чтобы изучить текущий расход токенов:

```text
/context
```

Обратите внимание на:
- **Уровень использования токенов**: если более 70%, рассмотрите сжатие контекста
- **Количество файлов в ссылках**: чем больше файлов, тем выше расход токенов
- **Большие файлы**: проверьте, какие файлы потребляют больше всего токенов

**Стратегия оптимизации:**

**1. Улучшите .claudeignore**

Убедитесь, что `.claudeignore` включает ненужные файлы:

```text
# must ignore
node_modules/
dist/
build/
*.log
.env

# project-specific
# React
.next/
out/

# Vue
.nuxt/
.output/

# generic
.vscode/
.idea/
coverage/
*.min.js
*.bundle.js
```

**2. Регулярно сжимайте контекст**

Длинные диалоги накапливают много токенов. Рекомендуется запускать `/compact` каждые 5-6 ходов:

```text
# after long conversation
/compact

# continue
Now let's implement order module...
```

**3. Точно ссылайтесь на файлы**

Избегайте ссылок на весь каталог, если это не нужно:

```bash
# not recommended
@src/ Explain this code

# recommended
@src/utils/auth.ts @src/components/Login.tsx Explain login flow
```

**4. Избегайте чтения огромных файлов**

Если `/context` показывает, что один файл потребляет много токенов, подумайте:
- действительно ли он вам нужен?
- можно ли сослаться только на его фрагмент?
- можно ли разбить этот файл на меньшие модули?

### Claude не понимает проект?

Если Claude отвечает неточно или повторно спрашивает базовую информацию о проекте, ему не хватает контекста проекта.

**Решения:**

**1. Сгенерируйте CLAUDE.md**

Запустите `/init`, чтобы сгенерировать конфигурацию проекта:

```bash
/init
```

После генерации проверьте:
- точно ли описан проект?
- полон ли стек технологий?
- правильны ли распространённые команды?
- ясны ли соглашения о написании кода?

**2. Отредактируйте CLAUDE.md вручную**

Если автоматически сгенерированная конфигурация недостаточно подробна, добавьте:

```markdown
## Project-Specific Information

### Architecture Decisions
- Why choose X over Y?
- What are core design patterns?

### Common Pitfalls
- When using useEffect, watch out for...
- DB queries must...

### Third-Party Integrations
- Payments via Stripe
- Email via SendGrid
- File storage via AWS S3
```

**3. Используйте каталог Rules**

Для крупных проектов организуйте соглашения в Rules:

```text
.claude/rules/
├── 00-architecture.md    # architecture overview
├── 01-coding-style.md    # coding style
├── 10-frontend.md        # frontend rules
├── 11-backend.md         # backend rules
└── 20-testing.md         # testing rules
```

**4. Добавляйте контекст в запрос при необходимости**

Для конкретных задач добавляйте релевантный фон:

```text
We use a custom useAuth Hook for authentication.
It returns { user, login, logout, isLoading }.
Please build a user-menu component based on this Hook.
```

### Как откатывать операции?

Claude Code предоставляет несколько механизмов отката для разных сценариев.

**Сценарий 1: откат состояния диалога**

Если вы лишь опечатались или вам не нравится ответ:

```text
Double Esc  -> rollback previous turn
Triple Esc  -> clear all conversation history
```

**Примечание**: это откатывает только состояние диалога, а не правки файлов.

**Сценарий 2: отмена правок файлов**

Если Claude уже изменил файлы, отмените изменения вручную:

```bash
# check changes
!git status
!git diff

# revert one file
git checkout -- src/utils/helpers.ts

# revert all working tree changes
git checkout -- .

# if already committed
# soft rollback (keep changes)
git reset --soft HEAD~1

# hard rollback (discard changes)
git reset --hard HEAD~1
```

**Сценарий 3: превентивно используйте рабочий процесс Git**

Лучшая практика: сохраняйте текущую работу перед сессией Claude:

```bash
# save current state before starting
git add .
git commit -m "WIP: before Claude Code session"
# or use stash
git stash push -m "before claude"

# develop with Claude Code...

# if result is unsatisfactory, full rollback
git reset --hard HEAD~1
# or
git stash pop
```

### Слишком много запросов на подтверждение прав?

Частые подтверждения прав снижают эффективность. Правильная конфигурация прав может сделать рабочий процесс более плавным.

**Модель прав доступа:**

Права в Claude Code имеют три уровня:
- **allow**: разрешить автоматически
- **ask**: спрашивать перед выполнением
- **deny**: полностью запретить

**Оптимизированная конфигурация:**

Отредактируйте `.claude/settings.json`:

```json
{
  "permissions": {
    "allow": [
      // Git read operations
      "Bash(git status)",
      "Bash(git log:*)",
      "Bash(git diff:*)",
      "Bash(git branch)",

      // test and checks
      "Bash(npm test:*)",
      "Bash(npm run lint:*)",
      "Bash(npm run typecheck)",

      // dev server
      "Bash(npm run dev:*)",

      // source edits
      "Edit(src/**/*.{ts,tsx})",
      "Edit(tests/**/*.test.ts)",
      "Write(src/**/*.ts)"
    ],
    "ask": [
      // Git write operations
      "Bash(git commit:*)",
      "Bash(git push:*)",
      "Bash(git pull:*)",

      // package management
      "Bash(npm install:*)",
      "Bash(npm uninstall:*)",

      // build and deployment
      "Bash(npm run build)",
      "Bash(npm run deploy:*)",

      // config file edits
      "Edit(package.json)",
      "Edit(tsconfig.json)",

      // sensitive file reads
      "Read(.env)",
      "Read(config/secrets.*)"
    ],
    "deny": [
      // dangerous commands
      "Bash(rm -rf:*)",
      "Bash(sudo:*)",
      "Bash(curl * | sh)",
      "Bash(wget * | sh)",

      // system files
      "Edit(/etc/*)",
      "Write(/usr/*)",

      // Git internals
      "Edit(.git/*)"
    ]
  }
}
```

**Постепенная стратегия настройки прав:**

- **Этап обучения**: оставьте значения по умолчанию и разберитесь, что Claude пытается выполнить
- **Этап освоения**: добавьте распространённые безопасные операции (вроде git status, npm test) в allow
- **Этап высокой эффективности**: создавайте детальные правила на основе особенностей проекта

### Как использовать в материковом Китае?

Из-за сетевых ограничений пользователи в Китае могут не иметь прямого доступа к официальным сервисам Anthropic. Вот несколько вариантов.

**Вариант 1: использовать прокси-сервис API**

Многие облачные провайдеры предлагают прокси-сервис API, совместимый с Anthropic:

```bash
# set env vars
export ANTHROPIC_BASE_URL="https://your-api-proxy.com/v1"
export ANTHROPIC_API_KEY="your-api-key"

# start Claude Code
claude
```

**Вариант 2: использовать сторонние инструменты, совместимые с Claude Code**

Некоторые местные провайдеры предлагают совместимые инструменты:

```bash
# install compatible version
npm install -g @some-provider/claude-code

# configure API key
claude config set api.key your-api-key
claude config set api.baseUrl https://api.some-provider.com
```

**Вариант 3: использовать другие инструменты для ИI-программирования**

Если Claude Code недоступен, рассмотрите альтернативы:

| Инструмент | Особенности | Сценарий использования |
|------|------|----------|
| Cursor | на базе VS Code, полнофункциональный | полноценный опыт работы в IDE |
| GitHub Copilot | сильное автодополнение | в основном автодополнение кода |
| Tongyi Lingma | местный продукт, стабилен в Китае | местная среда разработки |
| Codeium | щедрая бесплатная квота | при ограниченном бюджете |

**Вариант 4: поручить настройку ИИ-агенту**

Если вы не уверены, как настроить, спросите ИИ-агента:

```text
I want to use Claude Code, but I cannot directly access it in mainland China.
I bought an API from provider XXX.
API endpoint is https://api.xxx.com,
key is sk-xxx.

Please configure environment variables so Claude Code can work correctly.
```

**Частые вопросы:**

- **В: после настройки всё равно не удаётся подключиться?**
  - О: проверьте правильность адреса API, включая путь `/v1`
  - О: проверьте действительность ключа API и баланс
  - О: проверьте, нужен ли локальной сети прокси

- **В: ответы приходят медленно?**
  - О: выберите провайдера с более близким географическим регионом
  - О: используйте план, оптимизированный для программирования, вместо общего плана API
  - О: используйте `/compact` для сокращения расхода токенов

- **В: некоторые функции недоступны?**
  - О: некоторые сторонние провайдеры могут не полностью поддерживать все функции Claude Code
  - О: посмотрите в документации провайдера, какой набор функций поддерживается

---

## Справочные материалы

- [Официальная документация Claude Code](https://code.claude.com/docs)
- [Claude Code на GitHub](https://github.com/anthropics/claude-code)
- [Everything Claude Code](https://github.com/affaan-m/everything-claude-code)

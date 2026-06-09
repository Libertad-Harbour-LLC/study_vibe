# Полное руководство по Claude Agent SDK

## Введение

Возможно, вы уже использовали базовый API Claude: отправляете одно сообщение, получаете один ответ, как в обычном чате. Но если вы хотите, чтобы Claude помог вам читать файлы, запускать команды, искать код, исправлять ошибки, самостоятельно проверять результат и продолжать итерации, такая «автономная работа» базовому API не под силу.

Claude Agent SDK создан именно для этого сценария. Он упаковывает все возможности Claude Code - чтение и запись файлов, выполнение команд, поиск по коду, редактирование файлов, работа с веб-страницами - в программируемую библиотеку. Вам не нужно самостоятельно писать цикл вызова инструментов. Claude может автономно выполнять инструменты и автономно итерировать, пока задача действительно не будет завершена.

Если коротко: базовый SDK - это «вы спрашиваете, он отвечает»; Agent SDK - это «вы поручаете, он работает».

---

## В чём отличие от базового SDK?

Сначала посмотрите на код, и разница станет очевидна:

```python
# Basic anthropic SDK: you must write your own loop to handle tool calls
import anthropic

client = anthropic.Anthropic()
response = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Fix the bug in auth.py"}],
    tools=[...]  # You must define tools yourself
)
# Claude asks to call some tool
while response.stop_reason == "tool_use":
    result = your_tool_executor(response.tool_use)  # You must execute it yourself
    response = client.messages.create(tool_result=result, **params)  # You must feed it back yourself
```

```python
# Agent SDK: one block and done, Claude reads files, finds bugs, and edits code by itself
from claude_agent_sdk import query, ClaudeAgentOptions

async for message in query(
    prompt="Fix the bug in auth.py",
    options=ClaudeAgentOptions(allowed_tools=["Read", "Edit", "Bash"]),
):
    print(message)  # Claude reads files, locates issues, and edits code by itself
```

Разница очевидна:

| Параметр сравнения | Базовый anthropic SDK | Claude Agent SDK |
|--------|-------------------|-----------------|
| Выполнение инструментов | Реализуете вы | Берёт на себя Claude |
| Цикл инструментов | Реализуете вы | Встроенный agent loop |
| Встроенные инструменты | Нет, всё определяете сами | Чтение/запись файлов, Bash, поиск и многое другое из коробки |
| Управление контекстом | Поддерживаете вы | Автосжатие и автоуправление |
| Лучше всего подходит для | Чат, генерация, простое использование инструментов | Автономное выполнение сложных задач |

---

## Чем он отличается от других agent-фреймворков?

На рынке много agent-фреймворков - LangChain, LlamaIndex, CrewAI, AutoGPT и другие. Чем уникален Claude Agent SDK по сравнению с ними?

> 📚 **Подробное сравнение смотрите в приложении**: [Сравнение основных agent-фреймворков](/ru-ru/appendix/8-artificial-intelligence/ai-agents.html)

Если коротко:

| Фреймворк | Наиболее подходящий сценарий |
|------|-------------|
| **Claude Agent SDK** | Дать Claude автономно выполнять написание кода, операции с файлами и выполнение команд |
| **LangChain** | Создание сложных универсальных ИИ-приложений с глубоко настраиваемыми процессами |
| **CrewAI** | Моделирование сценариев совместной работы нескольких ролей (виртуальные команды, ролевые игры) |
| **LlamaIndex** | Создание систем ответов на вопросы по базе знаний, связывающих корпоративные данные с LLM |

---

## Установка и настройка

### Установка

Для Python нужен 3.10+, а для TypeScript - Node.js 18+:

```bash
# Python
pip install claude-agent-sdk

# TypeScript
npm install @anthropic-ai/claude-agent-sdk
```

### Аутентификация

Просто задайте переменную окружения с ключом API:

```bash
export ANTHROPIC_API_KEY=your-api-key
```

Также поддерживается аутентификация через облачные платформы:
- AWS Bedrock: задайте `CLAUDE_CODE_USE_BEDROCK=1` + учётные данные AWS
- Google Vertex AI: задайте `CLAUDE_CODE_USE_VERTEX=1` + учётные данные GCP
- Microsoft Azure: задайте `CLAUDE_CODE_USE_FOUNDRY=1` + учётные данные Azure

### Пользовательская конечная точка API

Если вы используете прокси, шлюз или самостоятельно размещённую конечную точку API, вы можете изменить URL API по умолчанию через параметр `env`:

```python
from claude_agent_sdk import query, ClaudeAgentOptions

async for message in query(
    prompt="Hello",
    options=ClaudeAgentOptions(
        env={
            "ANTHROPIC_BASE_URL": "https://your-proxy.example.com",
            "ANTHROPIC_API_KEY": "your-api-key",
        }
    ),
):
    print(message)
```

У `ClaudeAgentOptions` нет прямого параметра `base_url`, но поле `env` может передавать произвольные переменные окружения в нижележащий Claude Code CLI. Распространённые переменные окружения:

| Переменная окружения | Назначение |
|---------|------|
| `ANTHROPIC_BASE_URL` | Пользовательская конечная точка API (прокси, шлюз) |
| `ANTHROPIC_API_KEY` | Ключ API |
| `ANTHROPIC_AUTH_TOKEN` | Альтернативный токен аутентификации |
| `ANTHROPIC_CUSTOM_HEADERS` | Пользовательские заголовки запросов |

---

## Основные концепции

Принцип работы Agent SDK можно выразить одной фразой: **сбор контекста -> выполнение действий -> проверка результатов -> повтор**.

Именно так работают разработчики-люди: сначала читают код, затем изменяют его, потом запускают тесты и проверяют результаты. Если что-то не так, продолжают итерации. Agent SDK автоматизирует этот цикл.

### Два режима использования

**Режим 1: функция `query()` - без сохранения состояния, подходит для разовых задач**

```python
import asyncio
from claude_agent_sdk import query, ClaudeAgentOptions

async def main():
    async for message in query(
        prompt="What files are in this directory?",
        options=ClaudeAgentOptions(allowed_tools=["Bash", "Glob"]),
    ):
        if hasattr(message, "result"):
            print(message.result)

asyncio.run(main())
```

**Режим 2: `ClaudeSDKClient` - с сохранением состояния, подходит для многоходового диалога**

Используйте этот режим, когда нужно сохранять контекст и взаимодействовать в несколько ходов. Например, сначала попросить Claude прочитать один модуль, затем попросить его найти все места вызова этого модуля - на втором ходе он по-прежнему помнит то, что прочитал на первом.

```python
import asyncio
from claude_agent_sdk import query, ClaudeAgentOptions

async def main():
    session_id = None

    # Turn 1: read the auth module
    async for message in query(
        prompt="Read the authentication module code",
        options=ClaudeAgentOptions(allowed_tools=["Read", "Glob"]),
    ):
        if hasattr(message, "subtype") and message.subtype == "init":
            session_id = message.session_id

    # Turn 2: continue based on previous context
    async for message in query(
        prompt="Find all places that call it",
        options=ClaudeAgentOptions(resume=session_id),
    ):
        if hasattr(message, "result"):
            print(message.result)

asyncio.run(main())
```

---

## Встроенные инструменты: готовы к использованию

Это одна из лучших сторон Agent SDK - вам не нужно самостоятельно реализовывать какие-либо инструменты, Claude может использовать их напрямую:

| Инструмент | Возможность | Типичное применение |
|------|------|---------|
| Read | Чтение файлов | Просмотр кода, чтение конфигов |
| Write | Создание файлов | Генерация новых файлов |
| Edit | Точное редактирование файлов | Исправление ошибок, рефакторинг |
| Bash | Запуск команд терминала | Запуск тестов, установка зависимостей, операции git |
| Glob | Поиск файлов по шаблону | `**/*.py`, `src/**/*.ts` |
| Grep | Поиск содержимого по регулярным выражениям | Поиск определений функций, TODO |
| WebSearch | Поиск по веб-страницам | Поиск документации, поиск подходов |
| WebFetch | Получение веб-содержимого | Чтение онлайн-документации |
| Task | Запуск суб-агентов | Параллельное выполнение подзадач |

Используйте `allowed_tools`, чтобы управлять тем, какие инструменты может использовать агент:

```python
# Read-only agent: can inspect but cannot modify
options = ClaudeAgentOptions(
    allowed_tools=["Read", "Glob", "Grep"],
    permission_mode="bypassPermissions"
)

# Full agent: can read, write, and execute commands
options = ClaudeAgentOptions(
    allowed_tools=["Read", "Write", "Edit", "Bash", "Glob", "Grep"]
)
```

---

## Продвинутые возможности

### Hooks: вставка собственной логики в ключевых точках

Hooks позволяют внедрять собственный код в критические моменты выполнения агента - например, для логирования, перехвата рискованных операций и аудита изменений файлов.

Поддерживаемые типы hooks включают: `PreToolUse` (перед выполнением инструмента), `PostToolUse` (после выполнения инструмента), `Stop` (когда агент останавливается), `SessionStart`, `SessionEnd` и другие.

```python
from datetime import datetime
from claude_agent_sdk import query, ClaudeAgentOptions, HookMatcher

# Record an audit log every time a file is modified
async def log_file_change(input_data, tool_use_id, context):
    file_path = input_data.get("tool_input", {}).get("file_path", "unknown")
    with open("./audit.log", "a") as f:
        f.write(f"{datetime.now()}: modified {file_path}\n")
    return {}

async def main():
    async for message in query(
        prompt="Refactor utils.py for better readability",
        options=ClaudeAgentOptions(
            permission_mode="acceptEdits",
            hooks={
                "PostToolUse": [
                    HookMatcher(matcher="Edit|Write", hooks=[log_file_change])
                ]
            },
        ),
    ):
        if hasattr(message, "result"):
            print(message.result)
```

Практическое применение:
- Аудит-логирование: запись каждой операции, выполненной агентом
- Перехват в целях безопасности: блокировка изменений критически важных файлов
- Push-уведомления: отправка сообщений при завершении задач агента
- Мониторинг затрат: подсчёт вызовов инструментов и использования токенов

### Суб-агенты: разделение больших задач между специалистами

Когда задача достаточно сложна, вы можете определить несколько специализированных суб-агентов и поручить главному агенту делегировать им подзадачи. У каждого суб-агента свои инструкции и права на инструменты, изолированные друг от друга.

```python
from claude_agent_sdk import query, ClaudeAgentOptions, AgentDefinition

async for message in query(
    prompt="Use the code-reviewer agent to review this project's code quality",
    options=ClaudeAgentOptions(
        allowed_tools=["Read", "Glob", "Grep", "Task"],
        agents={
            "code-reviewer": AgentDefinition(
                description="Professional code reviewer responsible for quality and security reviews",
                prompt="Analyze code quality, identify potential issues, and provide improvement suggestions.",
                tools=["Read", "Glob", "Grep"],
            ),
            "test-writer": AgentDefinition(
                description="Testing specialist responsible for writing unit tests",
                prompt="Write unit tests for functions that are missing tests.",
                tools=["Read", "Write", "Bash"],
            ),
        },
    ),
):
    if hasattr(message, "result"):
        print(message.result)
```

Сообщения от суб-агентов содержат поле `parent_tool_use_id`, что упрощает отслеживание того, какие сообщения от какого суб-агента пришли.

### Интеграция MCP: подключение к внешнему миру

Через Model Context Protocol (MCP) ваш агент может подключаться к внешним системам, таким как базы данных, браузеры и сторонние API. Сообщество уже предоставляет [сотни MCP-серверов](https://github.com/modelcontextprotocol/servers), которые можно использовать напрямую.

```python
# Connect Playwright so the agent can operate a browser
async for message in query(
    prompt="Open example.com and describe what you see",
    options=ClaudeAgentOptions(
        mcp_servers={
            "playwright": {
                "command": "npx",
                "args": ["@playwright/mcp@latest"]
            }
        }
    ),
):
    if hasattr(message, "result"):
        print(message.result)
```

Распространённые сценарии интеграции MCP:
- Playwright: автоматизация браузера, скрапинг страниц, заполнение форм
- PostgreSQL/MySQL: прямые запросы и операции с базами данных
- Slack/Email: отправка уведомлений и сообщений
- GitHub: работа с PR, Issues и репозиториями

---

## Что можно с его помощью создать? Практические сценарии

После знакомства с возможностями самый важный вопрос: что это реально умеет? Ниже приведены реальные сценарии, проверенные сообществом.

### Сценарий 1: агент для автоматического исправления ошибок

Дайте ему описание ошибки, и он сможет найти код, локализовать проблему, исправить её и запустить тесты для проверки:

```python
async for message in query(
    prompt="Users report occasional HTTP 500 errors during login. Investigate and fix code under src/auth/",
    options=ClaudeAgentOptions(
        allowed_tools=["Read", "Edit", "Bash", "Glob", "Grep"],
        permission_mode="acceptEdits",
    ),
):
    print(message)
```

Claude выполнит grep по логам, прочитает связанный код, найдёт ошибку, изменит код и запустит тесты, чтобы подтвердить исправление.

### Сценарий 2: агент для код-ревью

Создайте агент код-ревью, работающий только на чтение, который проверяет качество без внесения каких-либо изменений:

```python
async for message in query(
    prompt="Review code under src/ with focus on security vulnerabilities, performance issues, and coding conventions",
    options=ClaudeAgentOptions(
        allowed_tools=["Read", "Glob", "Grep"],
        permission_mode="bypassPermissions",
    ),
):
    if hasattr(message, "result"):
        print(message.result)
```

### Сценарий 3: интеграция с CI/CD

В CI-конвейере дайте агенту проанализировать падающие тесты и попытаться автоматически исправить их:

```python
async for message in query(
    prompt="Run npm test, analyze failing test cases, and fix the code so all tests pass",
    options=ClaudeAgentOptions(
        allowed_tools=["Read", "Edit", "Bash", "Glob"],
        max_turns=20,
    ),
):
    print(message)
```

Это важное преимущество Agent SDK над CLI - CLI хорош, когда за терминалом сидит человек, тогда как SDK идеально подходит для встраивания в автоматизированные рабочие процессы.

### Сценарий 4: исследовательский агент

Дайте агенту искать в интернете, читать документацию, синтезировать информацию и готовить отчёт:

```python
async for message in query(
    prompt="Research mainstream Python Web frameworks in 2026. Compare FastAPI, Django, and Litestar, then write a technical selection report to report.md",
    options=ClaudeAgentOptions(
        allowed_tools=["WebSearch", "WebFetch", "Write"],
    ),
):
    print(message)
```

### Сценарий 5: full-stack агент с возможностями браузера

Подключив Playwright через MCP, агент может не только писать код, но и открывать браузер для проверки результатов:

```python
async for message in query(
    prompt="Fix the homepage style issue, then open a browser and take screenshots to verify the result",
    options=ClaudeAgentOptions(
        allowed_tools=["Read", "Edit", "Bash"],
        mcp_servers={
            "playwright": {
                "command": "npx",
                "args": ["@playwright/mcp@latest"]
            }
        },
    ),
):
    print(message)
```

### Быстрый справочник по сценариям

| Сценарий | Основные инструменты | Сложность |
|------|---------|------|
| Автоисправление ошибок | Read, Edit, Bash, Grep | Начальный |
| Код-ревью | Read, Glob, Grep | Начальный |
| Автоисправление в CI/CD | Read, Edit, Bash | Средний |
| Технический исследовательский отчёт | WebSearch, WebFetch, Write | Начальный |
| Автоматизация браузера | MCP (Playwright) | Средний |
| Совместная работа нескольких агентов | Task + AgentDefinition | Продвинутый |
| Операции с базами данных | MCP (PostgreSQL/MySQL) | Средний |
| Помощник по email/уведомлениям | MCP (Slack/Email) | Средний |

---

## Когда стоит использовать Agent SDK?

Не каждому сценарию нужен Agent SDK. Важно выбрать правильный инструмент:

| Что вы хотите сделать | Рекомендуемый инструмент |
|-----------|---------|
| Простой чат, генерация текста, перевод | Базовый `anthropic` SDK |
| Разовое использование инструмента (запрос погоды, арифметика) | Базовый `anthropic` SDK |
| Автономное выполнение многошаговых задач разработки | Agent SDK |
| Встраивание в конвейеры CI/CD | Agent SDK |
| Создание приложений, работающих с файловой системой | Agent SDK |
| Повседневная интерактивная разработка | Claude Code CLI |
| Разовые быстрые задачи | Claude Code CLI |

Если коротко: если ваша задача требует, чтобы Claude «работал руками» самостоятельно (читал файлы, редактировал код, запускал команды), используйте Agent SDK. Если вам нужны только вопросы и ответы, достаточно базового SDK.

---

## Корпоративная практика: построение конвейера-страховки качества кода

Все предыдущие сценарии использовали один агент для одной задачи. В реальных корпоративных средах вам нужен полный конвейер - несколько агентов, объединённых в цепочку, каждый этап с чётким входом/выходом, плюс аудит, откат и уведомления.

Сейчас мы построим реальный сценарий: после каждой отправки PR автоматически запускается **код-ревью -> сканирование безопасности -> автоисправление -> проверка тестами -> генерация отчёта** как полный конвейер.

### Проектирование архитектуры

```text
PR submitted
  │
  ▼
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  Code Review │───▶│ Security Scan│───▶│   Auto Fix   │
│    Agent     │    │    Agent     │    │    Agent     │
│ (read-only)  │    │ (read-only)  │    │ (writable)   │
└─────────────┘    └─────────────┘    └─────────────┘
                                            │
                                            ▼
                                     ┌─────────────┐    ┌─────────────┐
                                     │ Test Verify  │───▶│ Report Build │
                                     │    Agent     │    │    Agent     │
                                     │   (Bash)     │    │   (Write)    │
                                     └─────────────┘    └─────────────┘
                                                              │
                                                              ▼
                                                       Slack notification
```

Основная идея: **каждый агент делает одну вещь, права минимизированы, а результаты передаются последовательно**.

### Шаг 1: определение каркаса конвейера

```python
import asyncio
import json
from datetime import datetime
from claude_agent_sdk import query, ClaudeAgentOptions, HookMatcher

# Audit log: record every operation by every agent
audit_log = []

async def audit_hook(input_data, tool_use_id, context):
    audit_log.append({
        "time": datetime.now().isoformat(),
        "tool": input_data.get("tool_name"),
        "input": input_data.get("tool_input", {}),
    })
    return {}

# Shared hook config: all agents share audit capability
audit_hooks = {
    "PostToolUse": [HookMatcher(matcher=".*", hooks=[audit_hook])]
}
```

### Шаг 2: агент код-ревью (только чтение)

```python
async def run_code_review(pr_diff: str) -> str:
    """Read-only agent, reviews code quality and outputs a structured report"""
    result_text = ""
    async for message in query(
        prompt=f"""Review the following PR diff from these dimensions:
1. Code conventions: naming, formatting, comments
2. Logic issues: edge cases, null pointer risks, race conditions
3. Performance risks: N+1 queries, memory leaks, unnecessary loops
4. Maintainability: oversized functions, unclear responsibilities, magic numbers

PR Diff:
{pr_diff}

Output JSON format: {{"issues": [{{"severity": "high/medium/low", "file": "...", "line": ..., "description": "..."}}], "summary": "..."}}""",
        options=ClaudeAgentOptions(
            allowed_tools=["Read", "Glob", "Grep"],
            permission_mode="bypassPermissions",
            hooks=audit_hooks,
            max_turns=10,
        ),
    ):
        if hasattr(message, "result"):
            result_text = message.result
    return result_text
```

### Шаг 3: агент сканирования безопасности (только чтение)

```python
async def run_security_scan() -> str:
    """Read-only agent focused on vulnerability scanning"""
    result_text = ""
    async for message in query(
        prompt="""Scan the project code for security vulnerabilities:
1. SQL injection, XSS, CSRF
2. Hardcoded keys or credentials
3. Insecure dependency versions
4. Missing permission checks

Output JSON: {{"vulnerabilities": [{{"severity": "critical/high/medium", "type": "...", "file": "...", "description": "...", "fix_suggestion": "..."}}]}}""",
        options=ClaudeAgentOptions(
            allowed_tools=["Read", "Glob", "Grep", "Bash"],
            permission_mode="bypassPermissions",
            hooks=audit_hooks,
            max_turns=15,
        ),
    ):
        if hasattr(message, "result"):
            result_text = message.result
    return result_text
```

### Шаг 4: агент автоисправления (с правом записи)

```python
async def run_auto_fix(review_result: str, security_result: str) -> str:
    """Writable agent that auto-fixes code based on review and scan results"""
    result_text = ""
    async for message in query(
        prompt=f"""Fix code according to the following review results:

Code review report:
{review_result}

Security scan report:
{security_result}

Fix rules:
1. Only fix issues with severity high or critical
2. Run related tests after each change to ensure no existing functionality is broken
3. Do not refactor unrelated code, apply minimal fixes only
4. Output the list of modified files after completion""",
        options=ClaudeAgentOptions(
            allowed_tools=["Read", "Edit", "Bash", "Glob", "Grep"],
            permission_mode="acceptEdits",
            hooks=audit_hooks,
            max_turns=30,
        ),
    ):
        if hasattr(message, "result"):
            result_text = message.result
    return result_text
```

### Шаг 5: проверка тестами + генерация отчёта

```python
async def run_test_and_report(fix_result: str) -> str:
    """Run tests and generate final report"""
    result_text = ""
    async for message in query(
        prompt=f"""Execute these actions:
1. Run the full test suite (npm test or pytest)
2. Compute test pass rate
3. Generate a Markdown quality report into pr-report.md, including:
   - Count of issues found in code review and severity distribution
   - Number of security vulnerabilities
   - Auto-fix changes: {fix_result}
   - Test pass rate
   - Final conclusion: whether merge is recommended""",
        options=ClaudeAgentOptions(
            allowed_tools=["Read", "Bash", "Write", "Glob"],
            hooks=audit_hooks,
            max_turns=15,
        ),
    ):
        if hasattr(message, "result"):
            result_text = message.result
    return result_text
```

### Шаг 6: объединение всего конвейера в цепочку

```python
import subprocess

async def run_pipeline():
    """Full PR quality-guard pipeline"""
    print("🔍 Stage 1/4: code review...")
    pr_diff = subprocess.run(
        ["git", "diff", "main...HEAD"], capture_output=True, text=True
    ).stdout
    review_result = await run_code_review(pr_diff)

    print("🛡️ Stage 2/4: security scan...")
    security_result = await run_security_scan()

    print("🔧 Stage 3/4: auto-fix...")
    fix_result = await run_auto_fix(review_result, security_result)

    print("✅ Stage 4/4: test verification + report generation...")
    report = await run_test_and_report(fix_result)

    # Save audit log
    with open("audit-log.json", "w") as f:
        json.dump(audit_log, f, indent=2, ensure_ascii=False)

    print(f"Pipeline finished, audit log saved ({len(audit_log)} operation records)")
    return report

asyncio.run(run_pipeline())
```

### Подход к корпоративному проектированию

Этот конвейер отражает несколько ключевых принципов корпоративного проектирования:

**Минимальные привилегии**: агенты code-review и security-scan работают только на чтение и не могут случайно изменить код. Только агент автоисправления имеет право записи, и даже оно ограничено `acceptEdits`.

**Возможность аудита**: каждый шаг каждого агента логируется через Hooks. Если что-то пойдёт не так, вы сможете отследить, какой агент, что и когда сделал.

**Цепочка результатов**: вывод каждого агента становится входом следующего агента. Результаты ревью передаются в автоисправление; результаты автоисправления передаются в проверку тестами. У каждого этапа чёткий контракт входа/выхода.

**Контроль затрат**: у каждого агента есть ограничение `max_turns`, чтобы предотвратить бесконтрольные циклы. В продакшене вы также можете добавить `max_budget_usd` для контроля бюджета.

**Расширяемость**: хотите ещё один этап, например «агент проверки документации» или «агент бенчмарка производительности»? Добавьте новую функцию и вставьте её в конвейер.

Эту модель можно встроить напрямую в GitHub Actions или GitLab CI, автоматически запускать при каждом PR и действительно реализовать «защитные ограждения качества кода, управляемые ИИ».

---

## Обработка ошибок

Agent SDK предоставляет чёткие типы исключений, чтобы вы могли построить надёжную отказоустойчивость в продакшене:

```python
from claude_agent_sdk import query, CLINotFoundError, ProcessError

try:
    async for msg in query(prompt="Analyze code"):
        print(msg)
except CLINotFoundError:
    print("Claude Code CLI is not installed. Please install it first.")
except ProcessError as e:
    print(f"Process exited unexpectedly with exit code: {e.exit_code}")
```

---

## Заключение

Основная ценность Claude Agent SDK в том, что он превращает «рассуждения модели» в «контролируемое выполнение». Он не просто генерирует текст. Он действительно может выполнять задачи внутри поддающейся аудиту, ограниченной системы инструментов.

Запомните строку из официального блога Anthropic: философия проектирования Agent SDK - «дайте агенту компьютер и позвольте ему работать как человеку».

Хорошее agent-приложение = чёткий дизайн инструментов + явные границы задач + надлежащий человеческий надзор. Инструменты дают агенту возможности, границы дают ему ограничения, а надзор даёт вам уверенность. Ни одного из трёх компонентов не должно недоставать.

---

## Справочные материалы

### Официальные ресурсы

- [Официальная документация Agent SDK](https://platform.claude.com/docs/ru-ru/agent-sdk/overview) - наиболее авторитетный справочник
- [GitHub - claude-agent-sdk-python](https://github.com/anthropics/claude-code-sdk-python) - исходный код Python SDK
- [GitHub - claude-agent-sdk-typescript](https://github.com/anthropics/claude-agent-sdk-typescript) - исходный код TypeScript SDK
- [Демонстрационные проекты Agent SDK](https://github.com/anthropics/claude-agent-sdk-demos) - email-помощник, исследовательский агент и другие

### Блоги и руководства

- [Building agents with the Claude Agent SDK](https://claude.com/blog/building-agents-with-the-claude-agent-sdk) - инженерный блог Anthropic о философии проектирования и архитектуре
- [Claude Agent SDK Python Study Guide](https://redreamality.com/blog/claude-agent-sdk-python-) - полное руководство с нуля
- [Claude Agent SDK Full Tutorial](https://blog.wenhaofree.com/ru-ru/posts/articles/claude-agent-sdk-tutorial/) - практическое руководство по системам инструментов, Agent Loop и контролируемому выполнению
- [12 Practical Agent SDK Scenarios](https://skywork.ai/blog/claude-agent-sdk-use-cases-2025/) - охватывает программирование, данные, автоматизацию и другое
- [Step-by-Step Agent Tutorial](https://skywork.ai/blog/how-to-use-claude-agent-sdk-step-by-step-ai-agent-tutorial/) - двойное руководство по TypeScript + Python

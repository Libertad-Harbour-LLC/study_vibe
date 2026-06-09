# Полное руководство по Claude Code MCP

## Что такое Claude Code MCP?

**Claude Code** — это официальный инструмент командной строки на базе ИИ от Anthropic, а **MCP (Model Context Protocol)** — это протокол, который позволяет Claude Code подключаться к внешним инструментам и сервисам.

Проще говоря, MCP превращает Claude Code из ИИ-ассистента, умеющего лишь читать и записывать локальные файлы, в супер-ассистента, который может обращаться к GitHub, базам данных, API и облачным сервисам.

## Зачем использовать MCP в Claude Code?

### Claude Code без MCP

```text
What you can do:
✓ Read local files
✓ Edit code
✓ Run commands
✓ Use Bash tools

What you cannot do:
✗ View your GitHub Issues
✗ Access a cloud database
✗ Call external APIs
✗ Get real-time weather
```

### Claude Code с MCP

```text
What you can do:
✓ All original functions
✓ View / create GitHub Issues and PRs
✓ Query SQLite and PostgreSQL databases
✓ Access external services such as Notion and Slack
✓ Get real-time weather and map data
✓ Browser automation
✓ ...and more
```

## Быстрый старт

### Шаг 1: разберитесь, где находятся файлы конфигурации

Файлы конфигурации MCP для Claude Code расположены здесь:

| Уровень | Путь к файлу конфигурации | Область действия |
|-----|-------------|----------|
| **Уровень пользователя** | `~/.claude.json` | Все проекты |
| **Уровень проекта** | `.claude/mcp.json` | Текущий проект |

Рекомендуется в первую очередь использовать **конфигурацию уровня проекта**, чтобы разные проекты могли использовать разные MCP-сервисы.

### Шаг 2: добавляйте MCP-серверы на естественном языке

В Claude Code не нужно вручную редактировать файлы конфигурации или запоминать команды. Вы можете описать желаемое на естественном языке:

```text
You: Help me add a GitHub MCP server. My token is ghp_xxx

Claude: I'll help you configure the GitHub MCP server...

[Automatically updates .claude/mcp.json]
```

```text
You: Add a SQLite database server. The database file is at ./data/app.db

Claude: Okay, I'll configure the SQLite MCP server...
```

```text
You: Add an HTTP-type MCP server with the address https://api.example.com/mcp

Claude: I'll add that remote MCP server...
```

### Шаг 3: проверьте конфигурацию

Просто спросите Claude Code напрямую:

```text
You: What MCP servers are available now?

Claude: Currently configured MCP servers:
• github - GitHub integration
• sqlite - SQLite database
• filesystem - Filesystem access
```

Или используйте диагностическую команду:

```text
/doctor
```

### Шаг 4: начинайте использовать

После успешной настройки вы можете напрямую вызывать функции MCP на естественном языке:

```text
You: Help me create an Issue on GitHub

Claude: I can help you create a GitHub Issue. Please tell me:
- the repository address, for example owner/repo
- the Issue title
- the Issue description
```

## Управление на естественном языке в Claude Code

### Просмотр MCP-серверов и управление ими

Вы можете взаимодействовать с Claude Code полностью на естественном языке:

```text
You: List all configured MCP servers

You: Check the connection status of the MCP servers

You: Delete the MCP server named notion

You: Update the token for the github server
```

### Диагностика проблем

Когда вы сталкиваетесь с проблемами:

```text
You: Check what's wrong with the MCP connection

Claude: [will automatically run diagnostics, analyze configuration files, and check server status]
```

## Подробно о методах конфигурации

### Конфигурация уровня пользователя, глобальная

Отредактируйте `~/.claude.json`:

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/Users/yourname/Documents"]
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "your-token"
      }
    }
  }
}
```

### Конфигурация уровня проекта, рекомендуется

Отредактируйте `.claude/mcp.json` в корне проекта:

```json
{
  "mcpServers": {
    "project-db": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-sqlite", "--db-path", "./data/app.db"]
    }
  }
}
```

**Преимущества конфигурации уровня проекта:**

- Члены команды могут совместно использовать конфигурацию, закоммитив её в Git
- Разные проекты могут использовать разные MCP-сервисы
- Конфигурация более гибкая и не загрязняет глобальные настройки

### Режимы транспорта

Claude Code поддерживает три режима транспорта:

#### STDIO, локальный процесс

```json
{
  "mcpServers": {
    "local-tool": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path"]
    }
  }
}
```

#### HTTP, удалённый сервис

```json
{
  "mcpServers": {
    "remote-api": {
      "url": "https://api.example.com/mcp",
      "transport": "http",
      "headers": {
        "Authorization": "Bearer your-token"
      }
    }
  }
}
```

#### SSE, события, отправляемые сервером

```json
{
  "mcpServers": {
    "streaming": {
      "url": "https://api.example.com/sse",
      "transport": "sse"
    }
  }
}
```

## Практические примеры

### Пример 1: автоматизация рабочего процесса GitHub

```text
You: Help me push the current changes to GitHub, then create a PR titled "Add new feature"

Claude:
1. Check current git status...
2. Create a new branch feature/new-feature...
3. Commit changes...
4. Push to remote...
5. Call github_create_pull_request to create the PR...
6. PR created: https://github.com/owner/repo/pull/123
```

### Пример 2: запрос к базе данных

```text
You: Query the 10 most recently registered users in the database

Claude:
1. Connect to the SQLite database...
2. Execute query: SELECT * FROM users ORDER BY created_at DESC LIMIT 10
3. Return results:
   - Zhang San (zhang@example.com) - 2025-02-28
   - Li Si (li@example.com) - 2025-02-27
   ...
```

### Пример 3: анализ скриншота веб-страницы

```text
You: Open https://example.com, take a screenshot, and analyze the page design

Claude:
1. Launch browser...
2. Navigate to https://example.com...
3. Capture screenshot...
4. [Returns screenshot]
5. Page design analysis:
   - The layout uses a centered design with a max width of 1200px
   - The main color is blue (#3b82f6)
   - The navigation bar is fixed to the top
   ...
```

### Пример 4: интеграция с Notion

```text
You: Save the notes I just wrote to Notion

Claude:
1. Use the Notion MCP server...
2. Create a new page...
3. Saved: https://notion.so/page/xxx
```

## Советы по отладке

### Диагностика на естественном языке

Когда что-то идёт не так, просто скажите об этом Claude Code напрямую:

```text
You: My MCP server cannot connect. Please check it for me

You: The GitHub MCP tool call failed. What is the reason?

You: Why does the sqlite server always show "connecting"?
```

Claude Code автоматически:

1. Проверит формат файла конфигурации
2. Проверит переменные окружения
3. Протестирует подключение к серверу
4. Предложит конкретные варианты исправления

### Устранение распространённых проблем

| Проблема | Возможная причина | Решение |
|-----|---------|----------|
| Сервер не подключён | Ошибка в формате файла конфигурации | Проверьте синтаксис JSON |
| Не удаётся вызвать инструмент | Недостаточно прав | Проверьте переменные окружения |
| Тайм-аут подключения | Проблема с сетью | Проверьте URL или сеть |
| Процесс падает | Ошибка в коде сервера | Проверьте журналы сервера |

### Команда ручной диагностики

```text
/doctor
```

Пример вывода:

```text
System Diagnostic Report:
===============

Claude Code: v2.5.0 ✓
Node.js: v20.0.0 ✓

MCP server status:
• github: ✓ Connected (12 tools)
• sqlite: ✗ Connection failed - Database file not found
• puppeteer: ✓ Connected (8 tools)

Suggestions:
1. Check whether the sqlite database path is correct
2. Make sure the .claude/mcp.json format is correct
```

## Лучшие практики

### 1. Отдавайте предпочтение конфигурации уровня проекта

**Почему рекомендуется конфигурация уровня проекта?**

Разным проектам часто нужны разные MCP-сервисы. Например, фронтенд-проекту могут понадобиться инструменты для тестирования в браузере, а бэкенд-проекту — подключения к базам данных. С конфигурацией уровня проекта у каждого проекта может быть свой выделенный набор MCP-серверов, что позволяет избежать хаоса одной большой глобальной конфигурации.

Что ещё важнее, конфигурацию уровня проекта можно закоммитить в Git. После клонирования проекта члены команды смогут сразу использовать те же MCP-сервисы без необходимости всё перенастраивать.

```text
Project A, frontend project -> .claude/mcp.json contains browser testing MCP
Project B, backend project -> .claude/mcp.json contains database MCP
```

### 2. Храните конфиденциальную информацию в переменных окружения

**Никогда не зашивайте секреты прямо в файл конфигурации.**

Файлы конфигурации могут быть случайно закоммичены в Git, что приведёт к утечке ключей. Правильный подход — хранить конфиденциальные значения в переменных окружения и ссылаться из файла конфигурации только на имена переменных. Тогда даже если файл конфигурации станет публичным, настоящие секреты всё равно останутся скрытыми.

```json
{
  "env": {
    "GITHUB_TOKEN": "$GITHUB_TOKEN",
    "GITHUB_TOKEN": "ghp_abc123"
  }
}
```

Первый вариант хорош, потому что читает значение из переменной окружения. Второй вариант плох, потому что зашивает секрет напрямую.

### 3. Фиксируйте версии

**Зачем фиксировать версии?**

По умолчанию `npx -y` всегда использует последнюю версию MCP-сервера. Это может привести к проблемам: новая версия может внести несовместимые изменения, либо пакет может быть внезапно удалён или переименован.

Добавляя `@version` к имени пакета, вы гарантируете, что всегда будет использоваться проверенная версия, и снижаете число неприятных сюрпризов от автоматических обновлений.

```json
{
  "command": "npx",
  "args": ["-y", "@modelcontextprotocol/server-github@1.2.3"]
}
```

### 4. Документируйте свою конфигурацию MCP

**Помогите коллегам быстро разобраться в настройке MCP**

Когда в проект входит несколько MCP-серверов, новые члены команды могут не понимать, для чего нужен каждый сервер и какая конфигурация ему требуется. Создание файла `README.md` в каталоге `.claude/`, описывающего назначение каждого сервера, требуемую конфигурацию и способ получения учётных данных, может существенно снизить затраты на коммуникацию.

Создайте `.claude/README.md` в своём проекте:

```markdown
# MCP Configuration Notes

MCP servers used in this project:

## github
Used for GitHub automation. Requires GITHUB_TOKEN.

## sqlite
Connects to ./data/app.db for querying and modifying data.

## puppeteer
Used for E2E testing.
```

## Claude Code и Claude Desktop

| Возможность | Claude Code | Claude Desktop |
|-----|-------------|----------------|
| **Файл конфигурации** | `~/.claude.json` или `.claude/mcp.json` | `claude_desktop_config.json` |
| **Конфигурация уровня проекта** | ✓ Поддерживается | ✗ Не поддерживается |
| **Управление на естественном языке** | ✓ Поддерживается | ✗ Требуется ручное редактирование |
| **Диагностика** | ✓ `/doctor` | ✗ Отсутствует |
| **Горячая перезагрузка** | ✓ Автоматически | ✗ Требуется перезапуск приложения |
| **Сценарии использования** | Рабочий процесс разработки, CI/CD | Повседневное использование, офисные задачи |

## Распространённые MCP-серверы

> 💡 Полный список MCP-серверов смотрите в приложении: [Каталог MCP-серверов](/ru-ru/appendix/mcp-servers/)

### Сервер GitHub

**Функция:** Issues, PR, управление репозиторием

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "your-token"
      }
    }
  }
}
```

**Получить токен можно здесь:** https://github.com/settings/tokens

### Сервер SQLite

**Функция:** запросы к базам данных SQLite и управление ими

```json
{
  "mcpServers": {
    "sqlite": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-sqlite", "--db-path", "./data/database.db"]
    }
  }
}
```

### Сервер файловой системы

**Функция:** доступ к файлам внутри указанного каталога

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/Users/yourname/Documents"]
    }
  }
}
```

### Автоматизация браузера Puppeteer

**Функция:** управление браузером, скриншоты, автоматизированное тестирование

```json
{
  "mcpServers": {
    "puppeteer": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-puppeteer"]
    }
  }
}
```

### Сервер поиска Brave

**Функция:** веб-поиск

```json
{
  "mcpServers": {
    "brave-search": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-brave-search"],
      "env": {
        "BRAVE_API_KEY": "your-brave-api-key"
      }
    }
  }
}
```

## Справочные ресурсы

### Официальная документация

- [Официальная документация Claude Code — MCP](https://docs.anthropic.com/zh-CN/docs/claude-code/mcp)
- [Официальный сайт MCP](https://modelcontextprotocol.io/)
- [Документация спецификации MCP](https://modelcontextprotocol.io/specification/)
- [Репозиторий MCP на GitHub](https://github.com/modelcontextprotocol)

### Официальные серверы

- [@modelcontextprotocol/server-github](https://github.com/modelcontextprotocol/servers/tree/main/src/github) — интеграция с GitHub
- [@modelcontextprotocol/server-sqlite](https://github.com/modelcontextprotocol/servers/tree/main/src/sqlite) — база данных SQLite
- [@modelcontextprotocol/server-postgres](https://github.com/modelcontextprotocol/servers/tree/main/src/postgres) — база данных PostgreSQL
- [@modelcontextprotocol/server-filesystem](https://github.com/modelcontextprotocol/servers/tree/main/src/filesystem) — доступ к файловой системе
- [@modelcontextprotocol/server-puppeteer](https://github.com/modelcontextprotocol/servers/tree/main/src/puppeteer) — автоматизация браузера
- [@modelcontextprotocol/server-fetch](https://github.com/modelcontextprotocol/servers/tree/main/src/fetch) — загрузка веб-страниц
- [@modelcontextprotocol/server-brave-search](https://github.com/modelcontextprotocol/servers/tree/main/src/brave-search) — поиск Brave
- [@modelcontextprotocol/server-git](https://github.com/modelcontextprotocol/servers/tree/main/src/git) — операции Git

### Обучающие статьи

- [Подробное объяснение принципов и практики MCP](https://view.inews.qq.com/a/20250414A023WV00)
- [Архитектура MCP (Model Context Protocol) и принципы его работы](https://m.toutiao.com/w/1826385835060307/)
- [Новейшее руководство по большим моделям 2025: от знакомства до мастерского владения протоколом MCP](https://m.blog.csdn.net/weixin_45653328/article/details/150916706)
- [Изучаем MCP с нуля (8) — создаём MCP-сервер](https://juejin.cn/post/7582510291667419187)

### Руководства по конфигурации

- [Лучшие практики Claude Code](https://www.anthropic.com/engineering/claude-code-best-practices)
- [Полное руководство по конфигурации Claude Code](https://juejin.cn/post/7576838552472043563)

### Учебные материалы по разработке

- [Дружелюбное к новичкам практическое руководство по MCP-серверу на TypeScript и Python](https://m.blog.csdn.net/ztt123654/article/details/150844207)
- [Полное руководство по созданию MCP-сервера: исчерпывающие учебники по TypeScript и Python](https://m.blog.csdn.net/gitblog_00703/article/details/154862128)
- [Создаём простейший MCP-сервер на TypeScript](https://m.blog.csdn.net/weixin_45653525/article/details/148433757)
- [Генерация MCP-сервера на TypeScript с помощью контейнерных приложений Azure](https://learn.microsoft.com/ru-ru/azure/developer/ai/build-mcp-server-ts)

### Ресурсы MCP-серверов

- [Awesome MCP Servers](https://github.com/punkpeye/awesome-mcp-servers) — самый полный список MCP-серверов
- [Официальный реестр MCP](https://registry.modelcontextprotocol.io) — официальный магазин приложений Anthropic
- [MCP.so](https://mcp.so) — сообществ­енный центр MCP-серверов
- [Glama.ai MCP](https://glama.ai/mcp/servers) — каталог MCP с рейтингами и отзывами
- [Smithery](https://smithery.ai) — маркетплейс MCP-серверов
- [MCPHub](https://mcphub.io/registry) — каталог с понятным интерфейсом
- [LobeHub MCP](https://lobehub.com/zh/mcp) — китайский каталог MCP

### Сервисы карт и погоды

- [Amap MCP Server](https://lobehub.com/zh/mcp/luozengchang-mcp-amap)
- [Документация Tencent Location Service MCP](https://lbs.qq.com/service/MCPServer/MCPServerGuide/overview)
- [Caiyun Weather MCP Server](https://github.com/caiyunapp/mcp-caiyun-weather)
- [OpenWeatherMap MCP Server](https://github.com/CodeByWaqas/weather-mcp-server)

### Ресурсы сообщества

- [Everything Claude Code Config](https://github.com/affaan-m/everything-claude-code) — коллекция конфигураций Claude Code промышленного уровня
- [AI Coding Guide](https://github.com/hacket/AICodingGuide) — китайский путь обучения Claude Code

### Реальные примеры применения

- [BlenderMCP — 3D-моделирование на базе ИИ](https://github.com/Belthur/blender-mcp) — 4100+ ⭐
- [15 лучших практик использования MCP в продакшене](https://learn.microsoft.com/ru-ru/azure/azure-functions/scenario-mcp-apps)

# Полное руководство по Claude Agent Teams

## Введение в Agent Teams

**Agent Teams** — это революционная функция Claude Code, которая позволяет **нескольким независимым экземплярам ИИ взаимодействовать как настоящая команда разработчиков**.

Представьте, что раньше использование Claude Code было похоже на работу руководителя проекта с одним исключительно способным помощником. Каким бы сложным ни было задание, всю работу выполнял только этот один помощник. Теперь, с Agent Teams, вы можете собрать полноценную ИИ-команду разработки: один участник может заниматься фронтендом, другой — бэкендом, третий — тестированием, и они могут **работать одновременно, общаться друг с другом и совместно выполнять сложные задачи**.

### От одного помощника к командной работе

Прежде чем углубиться в Agent Teams, давайте сначала разберёмся, какую проблему эта функция решает.

**Ограничения режима одного ИИ**:

Когда вы используете один экземпляр Claude для работы над сложным проектом, вы сталкиваетесь со следующими узкими местами:

- **Узкое место последовательной обработки**: ИИ может делать только одно дело за раз. Например, при рефакторинге проекта ему может потребоваться сначала проанализировать модуль аутентификации, затем модуль базы данных и, наконец, модуль API. Эти шаги приходится выполнять последовательно, даже если они не зависят друг от друга.

- **Проблема переполнения контекста**: вся информация находится в одном окне диалога. По мере того как диалог удлиняется, важные ранние детали могут затеряться, и ИИ может забыть ключевые решения, обсуждённые ранее.

- **Ограничение единственной точки зрения**: думает только один ИИ, поэтому нет обсуждения и проверки с разных сторон. Когда возникают сложные проектные решения, нет «коллеги», с которым можно поспорить или который предложит иную точку зрения.

- **Потолок эффективности**: крупные рефакторинги или многомодульная разработка занимают много времени, и нет способа ускорить их за счёт параллелизма.

**Решение Agent Teams**:

Agent Teams решает эти проблемы через **параллельное взаимодействие нескольких экземпляров**:

- **Настоящая параллельная работа**: несколько ИИ могут работать над разными задачами одновременно. Один может заниматься UI фронтенда, другой — API бэкенда, третий — проектированием базы данных, не мешая друг другу.

- **Независимые пространства контекста**: каждый участник команды имеет собственное полное окно контекста на 200K токенов, поэтому важная информация не «забывается» из-за того, что диалог стал слишком длинным.

- **Способность к командному взаимодействию**: участники могут напрямую общаться, обсуждать проектные решения и проверять качество кода друг друга, как настоящая команда разработчиков.

- **Значительный прирост эффективности**: согласно внутренним тестам Anthropic, эффективность при крупномасштабных рефакторингах проектов может вырасти примерно на 50%.

---

## Agent Teams против Subagent

Прежде чем глубже погрузиться в архитектуру Agent Teams, стоит сначала прояснить распространённую путаницу: **в чём разница между Agent Teams и Subagent**?

Обе функции связаны с «взаимодействием нескольких ИИ», но их модели взаимодействия совершенно разные и подходят для разных сценариев.

### Ключевые отличия с первого взгляда

| Параметр | Subagent | Agent Teams |
|---------|-------------------|----------------------|
| **Топология** | Звездообразная топология: все субагенты отчитываются перед главным агентом | Сетчатая топология: участники могут общаться друг с другом |
| **Способ коммуникации** | Главный агент явно передаёт информацию через промпты, а субагенты возвращают результаты по завершении | Участники могут напрямую общаться, обсуждать и координироваться |
| **Управление контекстом** | Каждый субагент имеет независимый контекст, а главный агент передаёт только необходимую информацию | Каждый участник имеет полностью независимый контекст |
| **Параллелизм** | Могут работать параллельно, но цепочка взаимодействия по-прежнему центрируется на главном агенте | Настоящая параллельная разработка и взаимодействие |
| **Координация задач** | Главный агент централизованно распределяет и координирует всё | Участники могут более автономно брать на себя задачи |
| **Стоимость** | Невысокая. Расход токенов накапливается при параллельной работе нескольких субагентов | Выше. Участники работают независимо и общаются чаще |

### Наглядная аналогия

**Subagent — это как**: руководитель, выписывающий отдельные наряды на задачи нескольким помощникам. Каждый помощник работает самостоятельно по своему наряду, а по завершении лишь возвращает результат руководителю. Помощники не общаются друг с другом напрямую, и руководитель не видит полного хода их размышлений во время работы.

```
You → Main Agent → Subagent A: "Analyze this file"
You → Main Agent → Subagent B: "Search for that function"
         ↓
    Subagent A completes → reports result to Main Agent
    Subagent B completes → reports result to Main Agent
         ↓
    Main Agent synthesizes the results → reports back to you
```

**Agent Teams — это как**: руководитель проекта, ведущий настоящую команду разработчиков. Участники команды могут напрямую общаться, обсуждать и взаимодействовать, а не передавать каждую деталь через руководителя проекта.

```
You → Team Lead: "Build a user authentication feature"
         ↓
    Team Lead creates the team and assigns tasks
         ↓
    Teammate A: "@Teammate B, is the API interface design ready?"
    Teammate B: "Yes, here's the format..."
    Teammate C: "I reviewed the interface and found something we should discuss..."
         ↓
    Team members collaborate to finish the work → Team Lead synthesizes the result → reports back to you
```

### Когда что использовать

**Используйте Subagent, когда**:

- У вас есть быстрая, чёткая, единичная задача, например «найди этот код ошибки»
- Задачи мало зависят друг от друга
- Вам нужно параллельное выполнение, но не нужно постоянное обсуждение между участниками

**Используйте Agent Teams, когда**:

- Вы выполняете сложный рефакторинг системы, охватывающий несколько модулей
- Вам нужен анализ и обсуждение с разных сторон, например, когда эксперт по безопасности и эксперт по производительности спорят о решении
- Вам нужна настоящая параллельная разработка, когда фронтенд, бэкенд и тестирование идут одновременно
- Задачи требуют частой координации и обмена информацией

### Краткое резюме

- **Subagent**: инструмент распределения задач, который разбивает большую задачу на меньшие и распределяет их между разными «работниками»
- **Agent Teams**: настоящая совместная команда, где участники могут общаться, обсуждать и работать вместе, как настоящая команда

---

## Основная архитектура

Agent Teams — это не просто функция «открыть несколько экземпляров». Это полноценная **система взаимодействия нескольких агентов**. Чтобы понять её, нужно разобраться в её основных компонентах и в том, как они работают вместе.

### Состав команды

Команда Agent Team состоит из четырёх основных компонентов, каждый из которых имеет свою зону ответственности и работает совместно для выполнения сложных задач.

**Team Lead**

Team Lead — это «мозг» и «координатор» всей команды. Он не выполняет задачи по написанию кода напрямую. Вместо этого он отвечает за:

- **Анализ требований и декомпозицию задач**: разбиение сложных требований пользователя на несколько подзадач, которые могут выполняться параллельно
- **Создание команды и управление ею**: решение о том, сколько участников нужно и что должен делать каждый из них
- **Назначение и планирование задач**: распределение задач между подходящими участниками и управление зависимостями задач
- **Синтез результатов и контроль качества**: сбор работы каждого участника, её интеграция и финальная проверка

**Teammates**

Teammates — это фактические «разработчики», выполняющие работу. Каждый Teammate — это независимый экземпляр Claude:

- **Независимое окно контекста**: каждый участник имеет полное окно контекста на 200K токенов, полностью изолированное от Team Lead и других участников
- **Полные права на инструменты**: они могут использовать все инструменты, такие как Read, Write, Edit и Bash
- **Автономный выбор задач**: они могут самостоятельно выбирать и брать задачи с общей доски задач
- **Способность к прямому общению**: они могут напрямую общаться с другими участниками, а не всегда действовать через Team Lead

**TaskList**

TaskList — это «инструмент управления проектами» команды, похожий на Jira или Trello:

- **Управление статусами задач**: каждая задача имеет чёткий статус: `pending`, `in_progress` или `completed`
- **Управление зависимостями**: задачи могут определять зависимости, и зависимые задачи могут начаться только после завершения предшествующих
- **Механизм автоматической разблокировки**: когда одна задача завершается, система автоматически проверяет и разблокирует задачи, ожидающие её
- **Механизм блокировки файлов**: когда участник берёт и начинает задачу, в каталоге задач создаётся файл блокировки, чтобы несколько участников не редактировали один и тот же файл одновременно

**Messaging System**

Система обмена сообщениями — это «чат-инструмент» между участниками команды:

- **Связь точка-точка**: участник A может отправить сообщение напрямую участнику B
- **Широковещательные объявления**: сообщение можно отправить сразу всем участникам
- **Основана на файловой системе**: сообщения хранятся в виде JSON-файлов в `~/.claude/teams/{team-name}/inboxes/`
- **Не требует сети**: всё работает полностью через локальную файловую систему, без сетевого подключения или прослушивания портов

### Процесс взаимодействия

Типичный рабочий процесс Agent Teams выглядит так:

```
The user submits a complex requirement
       ↓
Team Lead analyzes the requirement and breaks it into tasks
       ↓
Creates team members and initializes TaskList
       ↓
       ├─→ Teammate A claims Task 1 ─┐
       ├─→ Teammate B claims Task 2 ─┼→ Run in parallel
       ├─→ Teammate C claims Task 3 ─┤
       │                             ↓
       └──────────────────────────── Members coordinate through the messaging system
                                     ↓
                          Once all tasks are complete, Team Lead synthesizes the result
                                     ↓
                          Final output is delivered to the user
```

### Структура файловой системы

Agent Teams создаёт выделенные каталоги в вашей локальной файловой системе для управления состоянием команды:

```
~/.claude/
├── teams/
│   └── {team-name}/
│       ├── config.json          # Team config (member list, model selection, etc.)
│       └── inboxes/
│           ├── team-lead.json   # Team Lead inbox
│           ├── teammate-1.json  # Member 1 inbox
│           └── teammate-2.json  # Member 2 inbox
└── tasks/
    └── {team-name}/
        ├── task-1.json          # Detailed info for Task 1
        ├── task-2.json          # Detailed info for Task 2
        └── current_tasks/
            └── parse_if_statement.txt  # Lock file created while a task is running
```

Преимущество такого подхода — **полная прозрачность**: вы можете в любой момент просмотреть состояние команды, ход выполнения задач и историю общения между участниками.

---

## Быстрый старт

### Включение экспериментальной функции

Agent Teams в настоящее время является **экспериментальной функцией** и по умолчанию отключена. Чтобы её использовать, сначала нужно её включить.

**Самый простой способ: позвольте Claude Code включить её за вас**

Введите это прямо в Claude Code:

```
Help me enable Agent Teams in settings.json
```

Or:

```
Enable the experimental feature agentTeams
```

Claude Code автоматически изменит `~/.claude/settings.json` и добавит следующую конфигурацию:

```json
{
  "experimental": {
    "agentTeams": true
  }
}
```

**Перезапустите Claude Code**

После добавления конфигурации **полностью закройте и перезапустите Claude Code**, и функция вступит в силу.

**Ручная настройка (если автоматический способ не сработал)**:

Вы можете вручную отредактировать `~/.claude/settings.json` и добавить или изменить:

```json
{
  "experimental": {
    "agentTeams": true
  }
}
```

**Как проверить, что функция включена**

После перезапуска Claude Code попробуйте такой диалог:

```
You: Can you help me create an Agent Team?

Claude: Yes! I can help you create an Agent Team to collaborate on a task...
```

Если Claude понимает запрос на создание команды и отвечает на него, значит функция успешно включена.

### Настройка визуального режима (опционально)

Если вы хотите видеть работу участников команды в реальном времени, вы можете настроить **режим отображения с разделёнными панелями**.

**Позвольте Claude Code настроить это за вас**:

Введите это прямо в Claude Code:

```
Help me enable split-pane display mode for Agent Teams in settings.json, using tmux
```

Or:

```
Configure agent-teams to use split-panes mode
```

**Установите tmux (если у вас его нет)**:

Если `tmux` ещё не установлен, вы можете попросить Claude Code установить его:

```
Help me install tmux
```

Claude Code автоматически выполнит подходящую команду установки в зависимости от вашей операционной системы, будь то macOS или Linux.

**Как выглядит результат настройки**:

После настройки участники команды будут работать в разных панелях tmux, и вы сможете видеть весь их вывод одновременно, как на «стене мониторинга».

```
┌─────────────────┬─────────────────┬─────────────────┐
│  Teammate 1     │  Teammate 2     │  Teammate 3     │
│  Analyzing code │  Building API   │  Writing tests  │
│  ...            │  ...            │  ...            │
│                 │                 │                 │
└─────────────────┴─────────────────┴─────────────────┘
```

**Ручная настройка (если автоматический способ не сработал)**:

Вы можете вручную отредактировать `~/.claude/settings.json`:

```json
{
  "experimental": {
    "agentTeams": true
  },
  "agent-teams": {
    "displayMode": "split-panes",
    "terminalMultiplexer": "tmux"
  }
}
```

---

### Практический пример: создание RPG-игры в стиле Pokemon с помощью Agent Teams

Давайте ощутим мощь Agent Teams на примере полноценного проекта. Этот пример покажет, как несколько участников ИИ-команды могут совместно создать RPG-игру с нуля, включая боевую систему, диалоговые функции и элементы исследования мира.

**Требования к проекту**:

Создать веб-RPG в стиле Pokemon со следующими функциями:

- **Система персонажа**: игрок может создать персонажа с уровнем, HP, атакой, защитой и другими характеристиками
- **Боевая система**: пошаговый бой с вариантами атаки, навыков, предметов и бегства
- **Система монстров**: несколько диких монстров с разными характеристиками и навыками
- **Диалоговая система**: разговоры с NPC и побочные задания
- **Исследование карты**: простая 2D-карта, по которой игрок может перемещаться между сценами
- **Система сохранения**: сохранение прогресса игры, включая уровень, позицию, выполненные задания и многое другое
- **Звуковые эффекты и анимация**: визуальные и звуковые эффекты для атак, урона и повышения уровня

**Введите это в Claude Code**:

```
I want to build a Pokemon-style web RPG game.

Create a team to collaborate on development:

Team member responsibilities:
- Teammate A (Game Architect): design the overall architecture, define the game state machine, and plan the data structures
- Teammate B (Battle System): implement turn-based combat logic, the skill system, and damage calculation
- Teammate C (Dialogue System): implement NPC dialogue, the quest system, and story scripts
- Teammate D (Map Rendering): use Canvas to implement 2D map rendering, character movement, and scene switching
- Teammate E (UI & Audio): design the game interface, battle UI, and sound playback

Technical requirements:
- Use React + TypeScript + Vite
- Use Tailwind CSS for styling
- Use Framer Motion for animation
- Use Zustand for state management
- Turn-based battle system
- Save data with localStorage
- Use the Web Audio API for sound

Use Sonnet for each member, and Opus for the Team Lead.

First ask the architect to design the overall solution. After the data structures are defined, let the other members develop in parallel.
```

**Как Claude это выполнит**:

**Фаза 1: проектирование архитектуры**

Team Lead сначала попросит Teammate A, архитектора игры, спроектировать общую структуру:

```
Teammate A is designing the game architecture...

Tech stack: React 18 + TypeScript + Vite + Tailwind CSS + Framer Motion + Zustand

Project initialization:
npm create vite@latest pokemon-rpg -- --template react-ts
npm install zustand framer-motion
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p

File structure:
src/
├── App.tsx                 # Main app component
├── main.tsx                # Entry file
├── types/
│   ├── player.ts           # Player type definitions
│   ├── monster.ts          # Monster type definitions
│   ├── skill.ts            # Skill type definitions
│   └── game.ts             # Game state types
├── stores/
│   └── gameStore.ts        # Zustand state management
├── components/
│   ├── map/                # Map-related components
│   │   ├── MapCanvas.tsx   # Map Canvas
│   │   ├── PlayerSprite.tsx # Player sprite
│   │   └── NPCSprite.tsx   # NPC sprite
│   ├── battle/             # Battle-related components
│   │   ├── BattleScreen.tsx    # Battle screen
│   │   ├── BattleMenu.tsx      # Battle menu
│   │   ├── HPBar.tsx           # HP bar component
│   │   └── BattleLog.tsx       # Battle log
│   ├── dialog/             # Dialogue-related components
│   │   ├── DialogBox.tsx   # Dialogue box
│   │   └── Choices.tsx     # Choices component
│   └── ui/                 # Shared UI components
│       ├── Button.tsx
│       ├── ProgressBar.tsx
│       └── StatCard.tsx
├── systems/
│   ├── BattleSystem.ts     # Battle system logic
│   ├── DialogSystem.ts     # Dialogue system logic
│   ├── QuestSystem.ts      # Quest system logic
│   └── MapSystem.ts        # Map system logic
├── hooks/
│   ├── useBattle.ts        # Battle hook
│   ├── useDialog.ts        # Dialogue hook
│   └── useAudio.ts         # Audio hook
├── data/
│   ├── monsters.ts         # Monster data
│   ├── skills.ts           # Skill data
│   ├── dialogues.ts        # Dialogue scripts
│   └── maps.ts             # Map data
└── utils/
    ├── audio.ts            # Audio utilities
    └── storage.ts          # Save utilities

Core data structure design:

// types/player.ts - Player type
interface Player {
  id: string;
  name: string;
  level: number;
  exp: number;
  maxExp: number;
  hp: number;
  maxHp: number;
  attack: number;
  defense: number;
  skills: Skill[];
  position: { x: number; y: number };
  direction: 'UP' | 'DOWN' | 'LEFT' | 'RIGHT';
  gold: number;
  items: string[];
}

// types/skill.ts - Skill type
  id: string,
  name: string,
  type: 'attack' | 'heal' | 'buff' | 'special',
  power: number,
  accuracy: number,
  pp: number,
  maxPp: number,
  effect?: string
}

// Game state
{
  screen: 'map' | 'battle' | 'dialog' | 'menu',
  player: Player,
  currentMap: string,
  position: {x: number, y: number},
  inBattle: boolean,
  dialogQueue: Array<Dialog>,
  gameData: {
    completedQuests: string[],
    items: string[],
    visitedMaps: string[]
  }
}

Battle system design:
- Turn-based, with the player acting first
- Each turn the player can choose: attack, skill, item, or flee
- Skills have PP limits and must be restored when depleted
- Damage formula: (attacker attack / defender defense) × skill power × random factor (0.85~1.0)

Dialogue system design:
- Typewriter effect for text display
- Supports branching options
- Supports conditional triggers, such as after a quest is completed
- NPCs have facing direction, and conversations trigger automatically when the player approaches
```

**Фаза 2: параллельная разработка**

После подтверждения архитектуры Team Lead создаёт список задач, и остальные участники начинают работать параллельно:

```
Task list:
├── [Teammate B] Implement battle system core logic (in progress...)
├── [Teammate C] Implement dialogue and quest systems (in progress...)
├── [Teammate D] Implement 2D map rendering (in progress...)
└── [Teammate E] Design UI and audio (in progress...)
```

<details>
<summary>📁 Teammate B: основной код боевой системы</summary>

```javascript
// battle.js - Battle system
class BattleSystem {
  constructor(player, monster) {
    this.player = player;
    this.monster = monster;
    this.turn = 'player';
    this.log = [];
    this.state = 'active'; // active, victory, defeat, flee
  }

  // Player attack
  playerAttack(skill) {
    if (this.turn !== 'player') return;

    const damage = this.calculateDamage(this.player, this.monster, skill);
    this.monster.hp = Math.max(0, this.monster.hp - damage);

    this.log.push(`${this.player.name} used ${skill.name}!`);
    this.log.push(`It dealt ${damage} damage!`);

    // Skill effect
    if (skill.effect) {
      this.applyEffect(this.player, this.monster, skill.effect);
    }

    // Check whether battle is over
    if (this.monster.hp <= 0) {
      this.state = 'victory';
      this.log.push(`${this.monster.name} collapsed!`);
      this.giveExp();
    } else {
      this.turn = 'monster';
      setTimeout(() => this.monsterAttack(), 1000);
    }
  }

  // Monster attack
  monsterAttack() {
    if (this.state !== 'active') return;

    // Randomly choose a skill
    const skill = this.monster.skills[Math.floor(Math.random() * this.monster.skills.length)];
    const damage = this.calculateDamage(this.monster, this.player, skill);

    this.player.hp = Math.max(0, this.player.hp - damage);

    this.log.push(`${this.monster.name} used ${skill.name}!`);
    this.log.push(`It dealt ${damage} damage!`);

    if (this.player.hp <= 0) {
      this.state = 'defeat';
      this.log.push(`${this.player.name} fell...`);
    } else {
      this.turn = 'player';
    }
  }

  // Damage calculation
  calculateDamage(attacker, defender, skill) {
    const levelFactor = (2 * attacker.level / 5 + 2);
    const attackDefense = attacker.attack / defender.defense;
    const baseDamage = levelFactor * attackDefense * skill.power + 2;
    const randomFactor = 0.85 + Math.random() * 0.15;

    // Type advantage bonus (simplified)
    let typeBonus = 1;
    // if (skill.type > defender.type) typeBonus = 1.5;

    return Math.floor(baseDamage * randomFactor * typeBonus);
  }

  // Apply skill effect
  applyEffect(user, target, effect) {
    switch(effect) {
      case 'burn':
        this.log.push(`${target.name} was burned!`);
        break;
      case 'heal':
        const healAmount = Math.floor(user.maxHp * 0.3);
        user.hp = Math.min(user.maxHp, user.hp + healAmount);
        this.log.push(`${user.name} recovered ${healAmount} HP!`);
        break;
      case 'buff':
        user.attack = Math.floor(user.attack * 1.2);
        this.log.push(`${user.name}'s attack increased!`);
        break;
    }
  }

  // Gain experience
  giveExp() {
    const baseExp = this.monster.level * 50;
    const expGain = Math.floor(baseExp * (1 + this.player.level / 10));

    this.player.exp += expGain;
    this.log.push(`${this.player.name} gained ${expGain} EXP!`);

    // Level-up check
    while (this.player.exp >= this.player.maxExp) {
      this.levelUp();
    }
  }

  // Level up
  levelUp() {
    this.player.level++;
    this.player.exp -= this.player.maxExp;
    this.player.maxExp = Math.floor(this.player.maxExp * 1.5);

    // Stat growth
    const hpGain = 10 + Math.floor(Math.random() * 5);
    const atkGain = 3 + Math.floor(Math.random() * 2);
    const defGain = 2 + Math.floor(Math.random() * 2);

    this.player.maxHp += hpGain;
    this.player.hp = this.player.maxHp;
    this.player.attack += atkGain;
    this.player.defense += defGain;

    this.log.push(`${this.player.name} leveled up to ${this.player.level}!`);
    this.log.push(`HP +${hpGain}, ATK +${atkGain}, DEF +${defGain}`);
  }

  // Flee
  flee() {
    if (Math.random() < 0.7) {
      this.state = 'flee';
      this.log.push('You fled successfully!');
      return true;
    } else {
      this.log.push('Failed to flee!');
      this.turn = 'monster';
      setTimeout(() => this.monsterAttack(), 1000);
      return false;
    }
  }
}

// monster.js - Monster data
const MONSTER_DATA = [
  {
    id: 'slime',
    name: 'Slime',
    baseHp: 30,
    baseAtk: 8,
    baseDef: 5,
    skills: [
      {id: 'tackle', name: 'Tackle', type: 'attack', power: 40, accuracy: 100, pp: 35}
    ],
    expGain: 20
  },
  {
    id: 'goblin',
    name: 'Goblin',
    baseHp: 45,
    baseAtk: 12,
    baseDef: 8,
    skills: [
      {id: 'tackle', name: 'Tackle', type: 'attack', power: 40, accuracy: 100, pp: 35},
      {id: 'scratch', name: 'Scratch', type: 'attack', power: 55, accuracy: 100, pp: 25}
    ],
    expGain: 35
  },
  {
    id: 'dragon',
    name: 'Young Dragon',
    baseHp: 80,
    baseAtk: 20,
    baseDef: 15,
    skills: [
      {id: 'scratch', name: 'Scratch', type: 'attack', power: 55, accuracy: 100, pp: 25},
      {id: 'ember', name: 'Ember', type: 'attack', power: 70, accuracy: 90, pp: 15},
      {id: 'growl', name: 'Growl', type: 'buff', power: 0, accuracy: 100, pp: 20}
    ],
    expGain: 80
  }
];
```

</details>

<details>
<summary>📁 Teammate C: код диалоговой системы и системы заданий</summary>

```javascript
// dialog.js - Dialogue system
class DialogSystem {
  constructor() {
    this.dialogQueue = [];
    this.currentDialog = null;
    this.isShowing = false;
    this.onComplete = null;
  }

  // Show dialogue
  showDialog(dialog, onComplete) {
    this.dialogQueue = Array.isArray(dialog) ? dialog : [dialog];
    this.onComplete = onComplete;
    this.isShowing = true;
    this.showNext();
  }

  // Show the next dialogue item
  showNext() {
    if (this.dialogQueue.length === 0) {
      this.isShowing = false;
      if (this.onComplete) this.onComplete();
      return;
    }

    this.currentDialog = this.dialogQueue.shift();

    // Handle special dialogue types
    if (typeof this.currentDialog === 'function') {
      this.currentDialog();
      this.showNext();
      return;
    }

    this.renderDialog();
  }

  // Render the dialogue box
  renderDialog() {
    const dialogBox = document.getElementById('dialogBox');
    const speakerEl = document.getElementById('dialogSpeaker');
    const textEl = document.getElementById('dialogText');

    if (this.currentDialog.speaker) {
      speakerEl.textContent = this.currentDialog.speaker;
      speakerEl.style.display = 'block';
    } else {
      speakerEl.style.display = 'none';
    }

    // Typewriter effect
    textEl.textContent = '';
    let i = 0;
    const text = this.currentDialog.text;
    const speed = this.currentDialog.speed || 30;

    const typeWriter = setInterval(() => {
      if (i < text.length) {
        textEl.textContent += text.charAt(i);
        i++;
      } else {
        clearInterval(typeWriter);
      }
    }, speed);

    // Show choices, if any
    this.renderChoices();
  }

  // Render choices
  renderChoices() {
    if (!this.currentDialog.choices) return;

    const choicesEl = document.getElementById('dialogChoices');
    choicesEl.innerHTML = '';
    choicesEl.style.display = 'block';

    this.currentDialog.choices.forEach(choice => {
      const btn = document.createElement('button');
      btn.textContent = choice.text;
      btn.onclick = () => {
        if (choice.condition === undefined || choice.condition()) {
          this.dialogQueue = [];
          this.showDialog(choice.dialog, this.onComplete);
        }
      };
      choicesEl.appendChild(btn);
    });
  }

  // Next
  next() {
    if (this.currentDialog && this.currentDialog.choices) return; // must choose when options exist
    this.showNext();
  }
}

// Quest system
class QuestSystem {
  constructor() {
    this.quests = {};
    this.activeQuests = [];
    this.completedQuests = [];
  }

  // Accept a quest
  acceptQuest(questId) {
    if (this.completedQuests.includes(questId)) return false;
    if (this.activeQuests.includes(questId)) return false;

    this.activeQuests.push(questId);
    return true;
  }

  // Update quest progress
  updateProgress(type, target) {
    this.activeQuests.forEach(questId => {
      const quest = this.quests[questId];
      if (!quest) return;

      quest.objectives.forEach(obj => {
        if (obj.type === type && obj.target === target && !obj.completed) {
          obj.current = (obj.current || 0) + 1;
          if (obj.current >= obj.required) {
            obj.completed = true;
          }
        }
      });

      this.checkCompletion(questId);
    });
  }

  // Check quest completion
  checkCompletion(questId) {
    const quest = this.quests[questId];
    if (!quest) return;

    const allComplete = quest.objectives.every(obj => obj.completed);
    if (allComplete) {
      this.completeQuest(questId);
    }
  }

  // Complete quest
  completeQuest(questId) {
    const index = this.activeQuests.indexOf(questId);
    if (index > -1) {
      this.activeQuests.splice(index, 1);
      this.completedQuests.push(questId);

      // Give rewards
      const quest = this.quests[questId];
      this.giveRewards(quest.rewards);
    }
  }

  // Give rewards
  giveRewards(rewards) {
    if (rewards.exp) player.gainExp(rewards.exp);
    if (rewards.gold) player.gold += rewards.gold;
    if (rewards.items) rewards.items.forEach(item => player.addItem(item));
  }
}

// dialogues.js - Dialogue script examples
const DIALOGUES = {
  villageChief: {
    firstMeeting: [
      {speaker: 'Village Chief', text: 'Oh, adventurer... you finally arrived.'},
      {speaker: 'Village Chief', text: 'Lately, many wild monsters have appeared near our village, and everyone is frightened.'},
      {speaker: 'Village Chief', text: 'If you can help drive them away, I would be deeply grateful!'},
      {
        choices: [
          {text: 'Okay, I accept this quest', dialog: () => {
            quests.acceptQuest('defeatMonsters');
            return [
              {speaker: 'Village Chief', text: 'Wonderful! Please defeat 3 slimes to the north.'},
              {speaker: 'System', text: 'Quest [Drive Away the Slimes] accepted!'}
            ];
          }},
          {text: 'I am a little busy right now', dialog: [
            {speaker: 'Village Chief', text: 'All right. Come back when you are ready.'}
          ]}
        ]
      }
    ],
    afterQuest: [
      {speaker: 'Village Chief', text: 'You really did it! Thank you so much!'},
      {speaker: 'System', text: 'Quest [Drive Away the Slimes] completed! You gained 100 EXP!'},
      {speaker: 'Village Chief', text: 'Please take this. It is a small token of my thanks.'}
    ]
  },

  shopkeeper: [
    {speaker: 'Shopkeeper', text: 'Welcome! Looking for something?'},
    {
      choices: [
        {text: 'Browse goods', dialog: () => {
          game.openShop();
          return [{speaker: 'Shopkeeper', text: 'Take whatever catches your eye!'}];
        }},
        {text: 'Leave', dialog: [{speaker: 'Shopkeeper', text: 'Come again next time!'}]}
      ]
    }
  ]
};
```

</details>

<details>
<summary>📁 Teammate D: код системы рендеринга 2D-карты</summary>

```javascript
// map.js - Map rendering system
class MapRenderer {
  constructor(canvas) {
    this.canvas = canvas;
    this.ctx = canvas.getContext('2d');
    this.tileSize = 32;
    this.currentMap = null;
    this.player = null;
    this.npcs = [];
    this.camera = {x: 0, y: 0};
  }

  // Load map
  loadMap(mapData) {
    this.currentMap = mapData;
    this.npcs = mapData.npcs || [];
    this.updateCamera();
  }

  // Render the map
  render() {
    if (!this.currentMap) return;

    // Clear the canvas
    this.ctx.fillStyle = '#000';
    this.ctx.fillRect(0, 0, this.canvas.width, this.canvas.height);

    // Save context
    this.ctx.save();

    // Apply camera offset
    this.ctx.translate(-this.camera.x, -this.camera.y);

    // Render map layers
    this.renderLayers();

    // Render NPCs
    this.renderNPCs();

    // Render player
    this.renderPlayer();

    // Restore context
    this.ctx.restore();
  }

  // Render map layers
  renderLayers() {
    const map = this.currentMap;

    for (let layer = 0; layer < map.layers.length; layer++) {
      const data = map.layers[layer].data;

      for (let y = 0; y < map.height; y++) {
        for (let x = 0; x < map.width; x++) {
          const tileId = data[y * map.width + x];
          if (tileId === 0) continue;

          const tileX = x * this.tileSize;
          const tileY = y * this.tileSize;

          this.renderTile(tileX, tileY, tileId);
        }
      }
    }
  }

  // Render a single tile
  renderTile(x, y, tileId) {
    // Draw different tiles based on tile ID
    const tileType = this.getTileType(tileId);

    switch(tileType) {
      case 'grass':
        this.ctx.fillStyle = '#4a8f4a';
        this.ctx.fillRect(x, y, this.tileSize, this.tileSize);
        // Grass texture
        this.ctx.fillStyle = '#3d7f3d';
        for (let i = 0; i < 3; i++) {
          const px = x + Math.random() * this.tileSize;
          const py = y + Math.random() * this.tileSize;
          this.ctx.fillRect(px, py, 2, 2);
        }
        break;

      case 'water':
        this.ctx.fillStyle = '#4a90d9';
        this.ctx.fillRect(x, y, this.tileSize, this.tileSize);
        // Ripple effect
        const wave = Math.sin(Date.now() / 500 + x / 20) * 2;
        this.ctx.fillStyle = '#5aa0e9';
        this.ctx.fillRect(x, y + 10 + wave, this.tileSize, 2);
        break;

      case 'wall':
        this.ctx.fillStyle = '#8b7355';
        this.ctx.fillRect(x, y, this.tileSize, this.tileSize);
        this.ctx.fillStyle = '#7a6248';
        this.ctx.fillRect(x + 2, y + 2, this.tileSize - 4, this.tileSize - 4);
        break;

      case 'path':
        this.ctx.fillStyle = '#c4a77d';
        this.ctx.fillRect(x, y, this.tileSize, this.tileSize);
        break;

      case 'house':
        this.ctx.fillStyle = '#a0522d';
        this.ctx.fillRect(x, y, this.tileSize, this.tileSize);
        // Roof
        this.ctx.fillStyle = '#8b4513';
        this.ctx.beginPath();
        this.ctx.moveTo(x, y);
        this.ctx.lineTo(x + this.tileSize / 2, y - 10);
        this.ctx.lineTo(x + this.tileSize, y);
        this.ctx.fill();
        break;
    }
  }

  // Get tile type
  getTileType(tileId) {
    const types = {
      1: 'grass', 2: 'water', 3: 'wall', 4: 'path', 5: 'house'
    };
    return types[tileId] || 'grass';
  }

  // Render NPCs
  renderNPCs() {
    this.npcs.forEach(npc => {
      const x = npc.x * this.tileSize;
      const y = npc.y * this.tileSize;

      // Draw NPC
      this.ctx.fillStyle = npc.color || '#ff6b6b';
      this.ctx.beginPath();
      this.ctx.arc(
        x + this.tileSize / 2,
        y + this.tileSize / 2,
        this.tileSize / 3,
        0,
        Math.PI * 2
      );
      this.ctx.fill();

      // Draw name
      this.ctx.fillStyle = '#fff';
      this.ctx.font = '10px Arial';
      this.ctx.textAlign = 'center';
      this.ctx.fillText(npc.name, x + this.tileSize / 2, y - 5);
    });
  }

  // Render player
  renderPlayer() {
    if (!this.player) return;

    const x = this.player.x * this.tileSize;
    const y = this.player.y * this.tileSize;

    // Player body
    this.ctx.fillStyle = '#4ecdc4';
    this.ctx.beginPath();
    this.ctx.arc(
      x + this.tileSize / 2,
      y + this.tileSize / 2,
      this.tileSize / 3,
      0,
      Math.PI * 2
    );
    this.ctx.fill();

    // Player direction indicator
    const directions = {UP: [0, -8], DOWN: [0, 8], LEFT: [-8, 0], RIGHT: [8, 0]};
    const [dx, dy] = directions[this.player.direction] || [0, 0];

    this.ctx.fillStyle = '#2d3436';
    this.ctx.beginPath();
    this.ctx.arc(
      x + this.tileSize / 2 + dx,
      y + this.tileSize / 2 + dy,
      4,
      0,
      Math.PI * 2
    );
    this.ctx.fill();
  }

  // Update camera position
  updateCamera() {
    if (!this.player) return;

    // Camera follows player and keeps them centered
    const targetX = this.player.x * this.tileSize - this.canvas.width / 2;
    const targetY = this.player.y * this.tileSize - this.canvas.height / 2;

    // Smooth movement
    this.camera.x += (targetX - this.camera.x) * 0.1;
    this.camera.y += (targetY - this.camera.y) * 0.1;

    // Prevent camera from going beyond map bounds
    const maxX = this.currentMap.width * this.tileSize - this.canvas.width;
    const maxY = this.currentMap.height * this.tileSize - this.canvas.height;
    this.camera.x = Math.max(0, Math.min(this.camera.x, maxX));
    this.camera.y = Math.max(0, Math.min(this.camera.y, maxY));
  }

  // Check collision
  checkCollision(x, y) {
    // Check map bounds
    if (x < 0 || x >= this.currentMap.width || y < 0 || y >= this.currentMap.height) {
      return true;
    }

    // Check tile collision
    const tileId = this.currentMap.layers[0].data[y * this.currentMap.width + x];
    const solidTiles = [3, 5]; // walls and houses are obstacles

    if (solidTiles.includes(tileId)) {
      return true;
    }

    // Check NPC collision
    for (const npc of this.npcs) {
      if (npc.x === x && npc.y === y) {
        // Trigger NPC dialogue
        this.triggerNPC(npc);
        return true;
      }
    }

    return false;
  }

  // Trigger NPC dialogue
  triggerNPC(npc) {
    if (npc.dialogue) {
      game.dialogSystem.showDialog(npc.dialogue);
    }
  }
}

// Example map data
const VILLAGE_MAP = {
  name: 'Starter Village',
  width: 20,
  height: 15,
  layers: [
    {
      name: 'ground',
      data: [
        // Map data (simplified)
        1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,
        1,4,4,4,1,1,5,5,5,1,1,4,4,4,4,1,1,1,1,1,
        1,4,1,4,1,1,5,5,5,1,1,4,1,1,4,1,1,1,1,1,
        1,4,4,4,1,1,1,1,1,1,1,4,4,4,4,1,2,2,1,1,
        1,1,1,1,1,1,4,4,4,1,1,1,1,1,1,1,2,2,1,1,
        1,4,4,4,1,1,4,4,4,1,1,1,1,1,1,1,2,2,1,1,
        1,4,1,4,1,1,1,1,1,1,1,4,4,4,1,1,1,1,1,1,
        1,4,4,4,1,1,1,1,1,1,1,4,1,1,4,1,1,1,1,1,
        // ... more map data
      ]
    }
  ],
  npcs: [
    {
      id: 'village_chief',
      name: 'Village Chief',
      x: 5,
      y: 5,
      color: '#ffd93d',
      dialogue: DIALOGUES.villageChief.firstMeeting,
      direction: 'DOWN'
    },
    {
      id: 'shopkeeper',
      name: 'Shopkeeper',
      x: 15,
      y: 8,
      color: '#6bcf7f',
      dialogue: DIALOGUES.shopkeeper,
      direction: 'DOWN'
    }
  ],
  exits: [
    {x: 10, y: 0, to: 'forest_map', spawnX: 5, spawnY: 14}
  ]
};
```

</details>

<details>
<summary>📁 Teammate E: код боевого интерфейса</summary>

```html
<!-- Battle screen HTML -->
<div id="battleScreen" class="screen hidden">
  <!-- Enemy area -->
  <div class="enemy-area">
    <div class="monster-sprite">
      <canvas id="monsterSprite" width="128" height="128"></canvas>
    </div>
    <div class="monster-info">
      <div class="name" id="enemyName">Slime</div>
      <div class="level">Lv. <span id="enemyLevel">3</span></div>
      <div class="hp-bar">
        <div class="hp-fill" id="enemyHpBar" style="width: 100%"></div>
      </div>
      <div class="hp-text">
        <span id="enemyHp">30</span> / <span id="enemyMaxHp">30</span>
      </div>
    </div>
  </div>

  <!-- Player area -->
  <div class="player-area">
    <div class="player-info">
      <div class="name" id="playerName">Hero</div>
      <div class="level">Lv. <span id="playerLevel">5</span></div>
      <div class="hp-bar">
        <div class="hp-fill" id="playerHpBar" style="width: 80%"></div>
      </div>
      <div class="hp-text">
        <span id="playerHp">80</span> / <span id="playerMaxHp">100</span>
      </div>
      <div class="exp-bar">
        <div class="exp-fill" id="expBar" style="width: 60%"></div>
      </div>
    </div>
    <div class="player-sprite">
      <canvas id="playerSprite" width="128" height="128"></canvas>
    </div>
  </div>

  <!-- Battle menu -->
  <div class="battle-menu" id="battleMenu">
    <div class="menu-row">
      <button class="menu-btn" data-action="attack">Attack</button>
      <button class="menu-btn" data-action="skills">Skills</button>
      <button class="menu-btn" data-action="items">Items</button>
      <button class="menu-btn" data-action="flee">Flee</button>
    </div>
  </div>

  <!-- Skill submenu -->
  <div class="submenu hidden" id="skillsMenu">
    <div class="submenu-title">Choose a skill</div>
    <div class="submenu-list" id="skillsList"></div>
    <button class="back-btn" onclick="hideSubmenu()">Back</button>
  </div>

  <!-- Battle log -->
  <div class="battle-log">
    <div id="battleLog"></div>
  </div>
</div>
```

```css
/* battle.css - Battle screen styles */
.battle-screen {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(180deg, #87ceeb 0%, #e0f7fa 50%, #4a5568 50%, #2d3748 100%);
  display: flex;
  flex-direction: column;
}

.enemy-area {
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 40px;
}

.monster-sprite canvas {
  image-rendering: pixelated;
  filter: drop-shadow(0 4px 8px rgba(0,0,0,0.3));
  animation: float 2s ease-in-out infinite;
}

@keyframes float {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-10px); }
}

.monster-info {
  margin-left: 40px;
  text-align: center;
}

.monster-info .name {
  font-size: 24px;
  font-weight: bold;
  color: #2d3748;
}

.monster-info .level {
  font-size: 14px;
  color: #718096;
  margin: 8px 0;
}

.hp-bar {
  width: 200px;
  height: 20px;
  background: #e2e8f0;
  border-radius: 10px;
  overflow: hidden;
  border: 2px solid #4a5568;
}

.hp-fill {
  height: 100%;
  background: linear-gradient(90deg, #48bb78, #38a169);
  transition: width 0.3s ease;
}

.hp-text {
  margin-top: 8px;
  font-size: 14px;
  color: #4a5568;
}

.player-area {
  flex: 1;
  display: flex;
  justify-content: space-between;
  align-items: flex-end;
  padding: 40px;
}

.player-info {
  background: rgba(255,255,255,0.9);
  border-radius: 12px;
  padding: 20px;
  border: 3px solid #4a5568;
}

.exp-bar {
  width: 200px;
  height: 8px;
  background: #e2e8f0;
  border-radius: 4px;
  margin-top: 8px;
}

.exp-fill {
  height: 100%;
  background: linear-gradient(90deg, #4299e1, #3182ce);
  border-radius: 4px;
}

.battle-menu {
  background: rgba(255,255,255,0.95);
  border: 3px solid #4a5568;
  border-radius: 12px;
  padding: 20px;
  margin: 0 40px 40px;
}

.menu-row {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 12px;
}

.menu-btn {
  padding: 16px 24px;
  font-size: 18px;
  font-weight: bold;
  background: linear-gradient(180deg, #fff 0%, #e2e8f0 100%);
  border: 2px solid #4a5568;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.2s;
}

.menu-btn:hover {
  background: linear-gradient(180deg, #4299e1 0%, #3182ce 100%);
  color: white;
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0,0,0,0.2);
}

.battle-log {
  position: absolute;
  bottom: 120px;
  left: 40px;
  right: 40px;
  max-height: 100px;
  overflow-y: auto;
  background: rgba(0,0,0,0.7);
  border-radius: 8px;
  padding: 12px;
}

#battleLog {
  color: #fff;
  font-size: 14px;
  line-height: 1.8;
}

.log-entry {
  margin-bottom: 4px;
  opacity: 0;
  animation: fadeIn 0.3s forwards;
}

@keyframes fadeIn {
  to { opacity: 1; }
}

/* Hit animation */
@keyframes shake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-5px); }
  75% { transform: translateX(5px); }
}

.shake {
  animation: shake 0.3s ease-in-out;
}

/* Attack animation */
@keyframes attackRight {
  0% { transform: translateX(0); }
  50% { transform: translateX(30px); }
  100% { transform: translateX(0); }
}

.attack-right {
  animation: attackRight 0.3s ease-in-out;
}
```

</details>

<details>
<summary>📁 Код аудиосистемы</summary>

```javascript
// audio.js - Audio system
class AudioManager {
  constructor() {
    this.audioContext = null;
    this.sounds = {};
    this.musicVolume = 0.3;
    this.sfxVolume = 0.5;
    this.currentBgm = null;
  }

  // Initialize audio context
  init() {
    if (!this.audioContext) {
      this.audioContext = new (window.AudioContext || window.webkitAudioContext)();
    }
    if (this.audioContext.state === 'suspended') {
      this.audioContext.resume();
    }
  }

  // Play background music
  playBgm(bgmName) {
    if (this.currentBgm === bgmName) return;

    this.stopBgm();

    // Use oscillators to generate simple BGM
    this.currentBgm = bgmName;
    this.playGeneratedBgm(bgmName);
  }

  // Generate simple background music
  playGeneratedBgm(type) {
    const melodies = {
      battle: [262, 294, 330, 262, 294, 330, 349, 330],
      village: [330, 349, 392, 349, 330, 294, 262, 294],
      victory: [392, 440, 494, 523, 494, 440, 392, 349]
    };

    const melody = melodies[type] || melodies.village;
    let noteIndex = 0;

    const playNote = () => {
      if (this.currentBgm !== type) return;

      const osc = this.audioContext.createOscillator();
      const gain = this.audioContext.createGain();

      osc.connect(gain);
      gain.connect(this.audioContext.destination);

      osc.frequency.value = melody[noteIndex];
      osc.type = 'triangle';

      gain.gain.setValueAtTime(this.musicVolume, this.audioContext.currentTime);
      gain.gain.exponentialRampToValueAtTime(
        0.01,
        this.audioContext.currentTime + 0.4
      );

      osc.start(this.audioContext.currentTime);
      osc.stop(this.audioContext.currentTime + 0.4);

      noteIndex = (noteIndex + 1) % melody.length;
      setTimeout(playNote, 500);
    };

    playNote();
  }

  // Stop background music
  stopBgm() {
    this.currentBgm = null;
  }

  // Play sound effect
  playSfx(sfxName) {
    this.init();

    switch(sfxName) {
      case 'attack':
        this.playAttackSound();
        break;
      case 'hit':
        this.playHitSound();
        break;
      case 'victory':
        this.playVictorySound();
        break;
      case 'levelup':
        this.playLevelUpSound();
        break;
      case 'dialog':
        this.playDialogSound();
        break;
    }
  }

  // Attack sound effect
  playAttackSound() {
    const osc = this.audioContext.createOscillator();
    const gain = this.audioContext.createGain();

    osc.connect(gain);
    gain.connect(this.audioContext.destination);

    osc.frequency.setValueAtTime(200, this.audioContext.currentTime);
    osc.frequency.exponentialRampToValueAtTime(
      100,
      this.audioContext.currentTime + 0.1
    );
    osc.type = 'sawtooth';

    gain.gain.setValueAtTime(this.sfxVolume, this.audioContext.currentTime);
    gain.gain.exponentialRampToValueAtTime(
      0.01,
      this.audioContext.currentTime + 0.1
    );

    osc.start(this.audioContext.currentTime);
    osc.stop(this.audioContext.currentTime + 0.1);
  }

  // Hit sound effect
  playHitSound() {
    const osc = this.audioContext.createOscillator();
    const gain = this.audioContext.createGain();

    osc.connect(gain);
    gain.connect(this.audioContext.destination);

    osc.frequency.value = 100;
    osc.type = 'square';

    gain.gain.setValueAtTime(this.sfxVolume * 0.8, this.audioContext.currentTime);
    gain.gain.exponentialRampToValueAtTime(
      0.01,
      this.audioContext.currentTime + 0.2
    );

    osc.start(this.audioContext.currentTime);
    osc.stop(this.audioContext.currentTime + 0.2);
  }

  // Victory sound effect
  playVictorySound() {
    const notes = [523, 659, 784, 1047];
    notes.forEach((freq, i) => {
      setTimeout(() => {
        const osc = this.audioContext.createOscillator();
        const gain = this.audioContext.createGain();

        osc.connect(gain);
        gain.connect(this.audioContext.destination);

        osc.frequency.value = freq;
        osc.type = 'sine';

        gain.gain.setValueAtTime(this.sfxVolume, this.audioContext.currentTime);
        gain.gain.exponentialRampToValueAtTime(
          0.01,
          this.audioContext.currentTime + 0.5
        );

        osc.start(this.audioContext.currentTime);
        osc.stop(this.audioContext.currentTime + 0.5);
      }, i * 150);
    });
  }

  // Level-up sound effect
  playLevelUpSound() {
    const notes = [392, 523, 659, 784, 1047];
    notes.forEach((freq, i) => {
      setTimeout(() => {
        const osc = this.audioContext.createOscillator();
        const gain = this.audioContext.createGain();

        osc.connect(gain);
        gain.connect(this.audioContext.destination);

        osc.frequency.value = freq;
        osc.type = 'triangle';

        gain.gain.setValueAtTime(this.sfxVolume, this.audioContext.currentTime);
        gain.gain.exponentialRampToValueAtTime(
          0.01,
          this.audioContext.currentTime + 0.3
        );

        osc.start(this.audioContext.currentTime);
        osc.stop(this.audioContext.currentTime + 0.3);
      }, i * 100);
    });
  }

  // Dialogue sound effect
  playDialogSound() {
    const osc = this.audioContext.createOscillator();
    const gain = this.audioContext.createGain();

    osc.connect(gain);
    gain.connect(this.audioContext.destination);

    osc.frequency.value = 800;
    osc.type = 'sine';

    gain.gain.setValueAtTime(this.sfxVolume * 0.3, this.audioContext.currentTime);
    gain.gain.exponentialRampToValueAtTime(
      0.01,
      this.audioContext.currentTime + 0.05
    );

    osc.start(this.audioContext.currentTime);
    osc.stop(this.audioContext.currentTime + 0.05);
  }
}
```

</details>

**Диалог взаимодействия между участниками**:

```
Teammate B → Teammate C:
"The battle system is done. When the player wins, it calls giveExp() to level up.
Please check the quest system and make sure level-up data is saved correctly."

Teammate C → Teammate B:
"Got it. The quest system stores game data with localStorage,
including level, experience, and the list of completed quests. I'll add an autosave mechanism."

Teammate D → All:
"The map rendering system is finished, and the NPC facing data is now connected to the dialogue system.
When the player faces an NPC, dialogue will trigger automatically. Please confirm the trigger logic in the dialogue system."

Teammate C → Teammate D:
"Confirmed. DialogSystem has a showDialog() method that can accept a dialogue array.
I'll make sure all NPC dialogue data follows that format."

Teammate E → Teammate B:
"The battle UI is finished, but I need real-time player and monster data to update the HP bars.
Does the battle system provide a callback?"

Teammate B → Teammate E:
"Yes. BattleSystem has an onUpdate callback that fires at the end of each turn.
You can register that callback to update the UI."

Teammate E → Teammate D:
"When switching maps, we need to reposition the camera.
Does MapRenderer provide an updateCamera() method?"

Teammate D → Teammate E:
"Yes. updateCamera() is called automatically after every loadMap().
You can also call it manually after the player moves to smoothly update the camera."
```

**Фаза 3: интеграция и тестирование**

После завершения всех компонентов Team Lead отвечает за интеграцию:

<details>
<summary>📁 Код главного контроллера игры</summary>

```javascript
// game.js - Main game controller
class Game {
  constructor() {
    this.state = 'map'; // map, battle, dialog, menu
    this.canvas = document.getElementById('gameCanvas');
    this.ctx = this.canvas.getContext('2d');

    // Initialize each system
    this.player = this.createPlayer();
    this.mapRenderer = new MapRenderer(this.canvas);
    this.battleSystem = null;
    this.dialogSystem = new DialogSystem();
    this.questSystem = new QuestSystem();
    this.audioManager = new AudioManager();

    // Load map
    this.currentMapId = 'village';
    this.mapRenderer.loadMap(VILLAGE_MAP);
    this.mapRenderer.player = this.player;

    // Input handling
    this.setupInput();

    // Start game loop
    this.lastTime = 0;
    this.gameLoop = this.gameLoop.bind(this);
    requestAnimationFrame(this.gameLoop);

    // Auto-load save
    this.loadGame();
  }

  // Create player
  createPlayer() {
    return {
      name: 'Hero',
      level: 1,
      exp: 0,
      maxExp: 100,
      hp: 50,
      maxHp: 50,
      attack: 15,
      defense: 10,
      skills: [
        {id: 'tackle', name: 'Tackle', type: 'attack', power: 40, accuracy: 100, pp: 35}
      ],
      x: 10,
      y: 7,
      direction: 'DOWN',
      gold: 100,
      items: ['potion', 'potion', 'antidote']
    };
  }

  // Set up input handling
  setupInput() {
    document.addEventListener('keydown', (e) => {
      if (this.state === 'map') {
        this.handleMapInput(e);
      } else if (this.state === 'dialog') {
        this.handleDialogInput(e);
      } else if (this.state === 'battle') {
        this.handleBattleInput(e);
      }
    });
  }

  // Map input handling
  handleMapInput(e) {
    if (this.dialogSystem.isShowing) {
      if (e.key === ' ' || e.key === 'Enter') {
        this.dialogSystem.next();
      }
      return;
    }

    let dx = 0, dy = 0;
    switch(e.key) {
      case 'ArrowUp': case 'w': dy = -1; this.player.direction = 'UP'; break;
      case 'ArrowDown': case 's': dy = 1; this.player.direction = 'DOWN'; break;
      case 'ArrowLeft': case 'a': dx = -1; this.player.direction = 'LEFT'; break;
      case 'ArrowRight': case 'd': dx = 1; this.player.direction = 'RIGHT'; break;
      default: return;
    }

    const newX = this.player.x + dx;
    const newY = this.player.y + dy;

    if (!this.mapRenderer.checkCollision(newX, newY)) {
      this.player.x = newX;
      this.player.y = newY;
      this.mapRenderer.updateCamera();

      // Check random battle
      if (Math.random() < 0.05) {
        this.startBattle();
      }

      // Save game
      this.saveGame();
    }
  }

  // Dialogue input handling
  handleDialogInput(e) {
    if (e.key === ' ' || e.key === 'Enter') {
      this.dialogSystem.next();
      if (!this.dialogSystem.isShowing) {
        this.state = 'map';
      }
    }
  }

  // Battle input handling
  handleBattleInput(e) {
    if (!this.battleSystem) return;
    if (this.battleSystem.turn !== 'player') return;
  }

  // Start battle
  startBattle(monsterData) {
    // Randomly choose a monster
    const randomMonster = MONSTER_DATA[Math.floor(Math.random() * MONSTER_DATA.length)];

    // Create monster instance
    const monster = {
      ...randomMonster,
      level: Math.max(1, this.player.level + Math.floor(Math.random() * 3) - 1),
      hp: randomMonster.baseHp + randomMonster.baseHp * 0.2 * this.player.level,
      maxHp: randomMonster.baseHp + randomMonster.baseHp * 0.2 * this.player.level,
      attack: randomMonster.baseAtk + randomMonster.baseAtk * 0.15 * this.player.level,
      defense: randomMonster.baseDef + randomMonster.baseDef * 0.1 * this.player.level
    };

    this.battleSystem = new BattleSystem(this.player, monster);
    this.state = 'battle';

    // Play battle music
    this.audioManager.playBgm('battle');

    // Show battle screen
    document.getElementById('battleScreen').classList.remove('hidden');
    document.getElementById('mapScreen').classList.add('hidden');

    // Update battle UI
    this.updateBattleUI();
  }

  // Update battle UI
  updateBattleUI() {
    if (!this.battleSystem) return;

    const player = this.battleSystem.player;
    const monster = this.battleSystem.monster;

    document.getElementById('playerName').textContent = player.name;
    document.getElementById('playerLevel').textContent = player.level;
    document.getElementById('playerHp').textContent = Math.floor(player.hp);
    document.getElementById('playerMaxHp').textContent = player.maxHp;
    document.getElementById('playerHpBar').style.width =
      (player.hp / player.maxHp * 100) + '%';

    document.getElementById('enemyName').textContent = monster.name;
    document.getElementById('enemyLevel').textContent = monster.level;
    document.getElementById('enemyHp').textContent = Math.floor(monster.hp);
    document.getElementById('enemyMaxHp').textContent = Math.floor(monster.maxHp);
    document.getElementById('enemyHpBar').style.width =
      (monster.hp / monster.maxHp * 100) + '%';

    // Update battle log
    const logEl = document.getElementById('battleLog');
    this.battleSystem.log.forEach(log => {
      const entry = document.createElement('div');
      entry.className = 'log-entry';
      entry.textContent = log;
      logEl.appendChild(entry);
    });
    logEl.scrollTop = logEl.scrollHeight;
  }

  // End battle
  endBattle() {
    this.state = 'map';
    this.battleSystem = null;

    // Hide battle screen
    document.getElementById('battleScreen').classList.add('hidden');
    document.getElementById('mapScreen').classList.remove('hidden');

    // Play map music
    this.audioManager.playBgm('village');

    // Save game
    this.saveGame();
  }

  // Save game
  saveGame() {
    const saveData = {
      player: this.player,
      currentMapId: this.currentMapId,
      completedQuests: this.questSystem.completedQuests,
      timestamp: Date.now()
    };

    localStorage.setItem('rpgSave', JSON.stringify(saveData));
  }

  // Load game
  loadGame() {
    const saveData = localStorage.getItem('rpgSave');
    if (saveData) {
      const data = JSON.parse(saveData);
      this.player = {...this.player, ...data.player};
      this.questSystem.completedQuests = data.completedQuests || [];
      this.currentMapId = data.currentMapId || 'village';
    }
  }

  // Main game loop
  gameLoop(timestamp) {
    const deltaTime = timestamp - this.lastTime;
    this.lastTime = timestamp;

    // Clear canvas
    this.ctx.fillStyle = '#000';
    this.ctx.fillRect(0, 0, this.canvas.width, this.canvas.height);

    // Render by state
    if (this.state === 'map') {
      this.mapRenderer.render();
    }

    requestAnimationFrame(this.gameLoop);
  }
}

// Start the game
window.addEventListener('DOMContentLoaded', () => {
  window.game = new Game();
});
```

</details>

**Итоговый результат**:

Примерно за 1-2 часа полностью функциональная RPG в стиле Pokemon готова!

```
Project summary:
✅ Game architecture design - Teammate A
✅ Turn-based battle system - Teammate B
✅ Dialogue and quest system - Teammate C
✅ 2D map rendering - Teammate D
✅ UI and sound effects - Teammate E

Project files:
├── index.html (120 lines)
├── css/
│   ├── main.css (100 lines)
│   ├── battle.css (180 lines)
│   └── dialog.css (80 lines)
├── js/
│   ├── game.js (250 lines)
│   ├── state.js (60 lines)
│   ├── player.js (50 lines)
│   ├── monster.js (80 lines)
│   ├── battle.js (220 lines)
│   ├── dialog.js (180 lines)
│   ├── map.js (280 lines)
│   └── audio.js (150 lines)
└── data/
    ├── monsters.js (100 lines)
    ├── skills.js (80 lines)
    └── dialogues.js (120 lines)

Total: about 2050 lines of code, completed collaboratively by 5 AI team members!

Game features:
🎮 Turn-based battle system (attack, skills, items, flee)
💬 NPC dialogue system (typewriter effect, branching choices)
📜 Quest system (accept quests, update progress, completion rewards)
🗺️ 2D map exploration (multi-scene transitions, NPC interaction)
💾 Autosave (progress stored with localStorage)
🔊 Sound effects and BGM (Web Audio API)
📊 Character growth (experience, leveling up, stat increases)
```

**Наблюдайте за работой команды**:

Если вы настроили режим разделённых панелей tmux, вы увидите несколько окон терминала, работающих одновременно:

```
┌─────────────────┬─────────────────┬─────────────────┐
│  Teammate B     │  Teammate C     │  Teammate D     │
│  Implementing   │  Writing        │  Rendering      │
│  damage formula │  dialogue       │  tiles          │
│                 │  scripts        │                 │
│  "Teammate E,   │  "Is            │  "The monsters  │
│   is the HP bar │   MapRenderer   │   need attack   │
│   width a       │   ready yet?"   │   animations..."│
│   percentage?"  │                 │                 │
└─────────────────┴─────────────────┴─────────────────┘
```

**Ключевые выводы**:

Этот практический пример демонстрирует несколько основных преимуществ Agent Teams:

1. **Настоящая параллельная разработка**: 5 участников разрабатывают разные игровые системы одновременно
2. **Управление сложным проектом**: более 2000 строк кода структурированно разбиваются и интегрируются
3. **Специализированное разделение труда**: у боя, диалогов, карт и UI есть свой выделенный ответственный
4. **Координация интерфейсов**: участники согласовывают интерфейсы и форматы данных через систему обмена сообщениями
5. **Быстрая поставка**: работа, на которую у одного человека ушли бы недели, может быть выполнена командой за несколько часов

Вы можете попробовать запустить эту игру самостоятельно и ощутить, как ИИ-команда совместно создаёт RPG в стиле Pokemon.

---

### Одиночный промпт против Agent Teams: проверьте сами

Чтобы вы могли непосредственнее ощутить мощь Agent Teams, мы подготовили два тестовых плана, которые вы можете попробовать самостоятельно и сравнить.

#### Тестовый план A: подход с одиночным промптом

Это традиционный подход: использовать один полный промпт и попросить ИИ разработать игру.

**Введите это в Claude Code**:

```
Help me build a Pokemon-style web RPG game with the following features:
- Character system (level, HP, attack, defense)
- Turn-based battle system (attack, skills, items, flee)
- NPC dialogue system
- 2D map exploration
- Save system
- Audio system

Use React + TypeScript + Vite + Tailwind CSS.
Please give me complete code that can run directly.
```

**Ожидаемый результат**:

| Пункт | Ожидаемая ситуация |
|------|---------|
| **Качество кода** | ИИ попытается сгенерировать весь код, но из-за ограничений контекста многие детали будут опущены или заменены комментариями |
| **Полнота функций** | Основные функции могут присутствовать, но многие продвинутые функции будут отсутствовать или упрощены |
| **Работоспособность** | Возможны баги, и перед запуском вам может потребоваться несколько раундов отладки |
| **Время разработки** | Один диалог может занять от 30 до 60 минут с многократными обменами репликами |
| **Объём кода** | Около 500-800 строк, поскольку ИИ склонен сжимать код |

**Проблемы, с которыми вы можете столкнуться**:

1. **Код обрывается**: у ответов ИИ есть ограничения по длине, поэтому генерация может остановиться на полпути
2. **Неполные функции**: диалоговая система может быть лишь базовой версией без системы заданий
3. **Отсутствующие детали**: аудиосистема может остаться в виде комментария TODO
4. **Сложно отлаживать**: если в коде есть проблемы, вам придётся просить ИИ исправить их в том же диалоге, и контекст становится всё более запутанным

#### Тестовый план B: подход Agent Teams

Это подход, описанный в этой статье: позволить нескольким участникам ИИ-команды совместно вести разработку.

**Введите это в Claude Code** (после включения Agent Teams):

```
I want to build a Pokemon-style web RPG game.

Create a team to collaborate on development:

Team member responsibilities:
- Teammate A (Game Architect): design the overall architecture, define the game state machine, and plan the data structures
- Teammate B (Battle System): implement turn-based combat logic, the skill system, and damage calculation
- Teammate C (Dialogue System): implement NPC dialogue, the quest system, and story scripts
- Teammate D (Map Rendering): use Canvas to implement 2D map rendering, character movement, and scene transitions
- Teammate E (UI & Audio): design the game interface, battle UI, and sound playback

Technical requirements:
- Use plain HTML/CSS/JavaScript
- Use Canvas to render the game screen
- Turn-based battle system
- Save data with localStorage
- Use the Web Audio API for sound

Use Sonnet for each member, and Opus for the Team Lead.

First ask the architect to design the overall solution. After the data structures are defined, let the other members develop in parallel.
```

**Ожидаемый результат**:

| Пункт | Ожидаемая ситуация |
|------|---------|
| **Качество кода** | Каждый участник сосредоточен на своей области, поэтому код более профессионален и полон |
| **Полнота функций** | Все функции реализованы полнее, включая систему заданий и многосценовые карты |
| **Работоспособность** | Участники взаимно проверяют интерфейсы друг друга, поэтому проблем с интеграцией меньше |
| **Время разработки** | Около 1-2 часов на реализацию всех функций, поскольку разработка идёт параллельно |
| **Объём кода** | Около 2000+ строк, с полной реализацией вместо сжатого кода |

#### Таблица количественного сравнения

| Параметр | Одиночный промпт | Agent Teams |
|---------|-------------|-------------|
| **Всего строк кода** | 500-800 строк | 2000+ строк |
| **Время разработки** | 30-60 минут, но функции неполны | 1-2 часа, с полными функциями |
| **Полнота функций** | 60-70% | 95%+ |
| **Поддерживаемость** | Средняя, обычно один большой файл | Высокая, с модульной структурой |
| **Количество багов** | Выше, поскольку проверки меньше | Ниже, поскольку участники проверяют друг друга |
| **Расширяемость в будущем** | Сложная, поскольку код сильно связан | Проще, поскольку структура модульная |
| **Расход токенов** | ~50K токенов | ~200K токенов (5 участников) |
| **Стоимость** | ~$0.50 | ~$2.00 |

#### Рекомендуемый процесс реального тестирования

**Шаг 1: сначала протестируйте подход с одиночным промптом**

```
1. Open a new Claude Code conversation
2. Use the prompt from "Test Plan A" above
3. Record: how long did it take? How many lines of code were produced? Which features were missing?
```

**Шаг 2: затем протестируйте подход Agent Teams**

```
1. Confirm that Agent Teams has been enabled
2. Use the prompt from "Test Plan B" above
3. Observe: how do team members collaborate? Is the code more complete?
```

**Шаг 3: сравните два результата**

```
1. Run both versions of the code separately
2. Compare the feature lists: which features are missing in the single-prompt version?
3. Compare the code structure: is the Agent Teams version more modular?
4. Evaluate: if you wanted to continue developing this game, which version would be easier to extend?
```

#### Почему возникают эти различия?

**Ограничения подхода с одиночным промптом**:

1. **Давление контекста**: ИИ должен обработать всё в одном ответе, поэтому упрощение неизбежно
2. **Рассеянное внимание**: бой, диалоги, карта и UI борются за внимание, поэтому детали легко упустить
3. **Нет совместной проверки**: никто не проверяет, совпадают ли интерфейсы, поэтому баги более вероятны

**Преимущества Agent Teams**:

1. **Специализированное разделение труда**: каждый участник сосредоточен на одной области и может глубоко вникнуть в детали
2. **Параллельная обработка**: разработка боя, диалогов и карты идёт одновременно, повышая эффективность
3. **Взаимная проверка**: участники согласовывают интерфейсы друг с другом, снижая проблемы интеграции
4. **Независимый контекст**: каждый участник имеет собственный контекст на 200K и не мешает остальным

#### Заключение

Основная ценность Agent Teams не просто в том, что это «быстрее», а в том, что это **«полнее и профессиональнее»**.

- Для простых проектов вроде «Змейки» достаточно одиночного промпта
- Для сложных проектов вроде RPG в стиле Pokemon Agent Teams может дать лучшие результаты

Главное — **выбрать подходящий инструмент**: не используйте Agent Teams для переименования переменной и не используйте одиночный промпт для создания полноценной RPG-игры.

---

## Лучшие практики

Agent Teams — мощный инструмент, но чтобы использовать его хорошо, нужно понимать некоторые лучшие практики. Эти уроки взяты из реального опыта сообщества и помогут вам избежать распространённых ошибок, получая максимум пользы от командного взаимодействия.

### Практика 1: контракт прежде всего

Прежде чем несколько агентов начнут работать параллельно, потратьте время на определение чёткого «контракта», то есть соглашения об интерфейсах.

**Почему это важно**:

Предположим, Teammate A отвечает за API бэкенда, а Teammate B — за интеграцию фронтенда. Если они начнут одновременно, не согласовав сначала формат интерфейса, может произойти примерно следующее:

```
Teammate A: implemented POST /api/login and expects {username, password}
Teammate B: implemented the frontend call and sends {user, pass}
Result: they do not match, and rework is required
```

**Как это сделать**:

Прежде чем запускать команду, сначала попросите Claude спроектировать интерфейсы:

```
Do not start development yet. First help me design the interfaces for the user authentication system:

1. The request and response formats for the login interface
2. The request and response formats for the registration interface
3. The password reset flow and interfaces
4. The error-handling conventions

Write these interfaces down clearly, and only then let the team begin development.
```

**Контракт должен включать**:

- Сигнатуры функций и структуры данных
- Форматы JSON для ввода и вывода
- Значения кодов состояния HTTP
- Соглашения об обработке ошибок
- Правила валидации полей

### Практика 2: распределяйте модели разумно

Разные задачи требуют разных моделей. Правильное распределение моделей помогает сбалансировать качество и стоимость.

**Используйте Opus для Team Lead**:

Team Lead занимается декомпозицией задач и синтезом результатов, что требует более сильных способностей к рассуждению, поэтому рекомендуется Opus:

```
Create a team where the Team Lead uses Opus for overall planning and final review.
The Teammates use Sonnet for implementation work.
```

**Используйте Sonnet для Teammates**:

Для конкретной работы по написанию кода и тестированию Sonnet вполне справляется и значительно дешевле:

- Opus 4.6: около $15 за миллион выходных токенов
- Sonnet 4.5: около $3 за миллион выходных токенов

Использование Sonnet для участников может значительно снизить общую стоимость.

**Используйте Haiku в особых случаях**:

Для простых задач, таких как обновление документации или небольшое написание тестов, можно рассмотреть Haiku, около $0.80 за миллион выходных токенов.

### Практика 3: контролируйте детализацию задач

Слишком крупные или слишком мелкие задачи одинаково вредят эффективности. Нужно найти правильную детализацию.

**Эмпирическое правило**:

Каждая задача должна быть чем-то, что один участник может самостоятельно выполнить за **15-30 минут**.

**Задача слишком крупная**:

```
Bad: implement the user authentication system
```

Эта задача слишком широкая. Она содержит несколько подзадач, и одному человеку потребуется много времени, чтобы её завершить, что сводит на нет преимущество параллелизма.

**Задача слишком мелкая**:

```
Bad: create an empty file called auth.js
```

Эта задача слишком крошечная. Участники тратят больше времени на координацию, чем на реальную работу.

**Подходящая детализация**:

```
Good: implement the login API, including:
1. The POST /api/login endpoint
2. Username and password validation
3. JWT token response
4. Error handling
```

У этой задачи чёткие границы и результаты. Один человек может выполнить её самостоятельно, и она не чрезмерно раздроблена.

**Рекомендуемая настройка**:

Пусть каждый участник владеет **5-6 задачами среднего размера**. Это даёт достаточный параллелизм, не делая затраты на координацию слишком высокими.

### Практика 4: избегайте конфликтов файлов

Одновременное изменение одного и того же файла несколькими участниками — самая распространённая проблема в Agent Teams.

**Принцип распределения**:

Старайтесь, чтобы разные участники владели **разными файлами**:

```
Good:
- Teammate A: owns all files under src/auth/
- Teammate B: owns all files under src/api/
- Teammate C: owns all files under tests/auth/

Bad:
- Teammate A and Teammate B both modify src/app.js
```

**Если один и тот же файл всё же нужно изменить**:

Спроектируйте этап последовательного редактирования:

```
Phase 1 (parallel):
- Teammate A: analyze what functionality needs to be added to auth.js
- Teammate B: design the new feature interface
- Teammate C: write the test cases

Phase 2 (serial):
- Team Lead synthesizes all inputs
- One member modifies auth.js in a single integrated pass
```

### Practice 5: provide rich initial context

When Teammates start, their conversation history is empty. They do not know what the Team Lead and the user discussed before.

**Wrong approach**:

```
Create the team and let the members start working.
```

Members will start in a fog: what project is this? What tech stack is it using? What exactly should they build?

**Correct approach**:

```
This is a React + Node.js e-commerce project using TypeScript.

The project structure is:
- src/frontend/: React frontend code
- src/backend/: Node.js backend code
- prisma/: database models

Code style:
- Use function components and Hooks
- Use Express.js on the backend
- Use PostgreSQL for the database

Now create a team and have the members add user authentication under src/auth/.
```

Only with sufficient context can members work efficiently.

### Practice 6: research before implementation

Do not let members start coding immediately. Ask them to research and design the solution first.

**Two-phase process**:

**Phase 1: research and design**

```
Create a team. In phase one, do research:
- One member investigates existing authentication approaches (JWT vs Session)
- One member analyzes the project's tech stack and determines best practices
- One member designs the database schema

After the research is complete, let the members discuss through the messaging system and settle on a final plan.
```

**Phase 2: implementation**

```
After the plan is finalized, begin implementation:
- One member implements the backend authentication logic
- One member implements the frontend login page
- One member writes tests
```

The benefit of doing it this way is that you can **discover architecture mismatches early**, instead of realizing halfway through implementation that the plan does not work.

### Practice 7: monitor and intervene actively

Even if you configured automation, you should still actively monitor the team's work status.

**Use split-pane mode**:

If you configured tmux panes, you can see all members' output in real time:

```
┌─────────────────┬─────────────────┐
│  Teammate 1     │  Teammate 2     │
│  Analyzing code │  Implementing   │
│  ...            │  API...         │
│                 │                 │
│  Wait, this     │                 │
│  approach seems │                 │
│  wrong...       │                 │
└─────────────────┴─────────────────┘
```

When you notice that a member is going in the wrong direction, you can intervene quickly:

```
@Teammate1 Stop for a moment. Your analysis is headed in the wrong direction. The authentication module should be under src/auth/, not src/user/.
```

**Check task status regularly**:

Use the TaskList command to inspect the status of all tasks:

```
/tasks
```

This shows all task states so you can see what is completed, what is still running, and what is blocked.

---

## Suitable scenarios

Agent Teams is powerful, but not every task is suitable for it. Understanding the right scenarios helps you choose correctly.

### Scenarios where Agent Teams fits well

**Complex system refactors**

When the refactor spans multiple modules with clear boundaries:

```
Scenario: split a monolithic application into microservices

Create a team:
- Teammate A: analyze dependencies in the user module
- Teammate B: analyze dependencies in the order module
- Teammate C: analyze dependencies in the payment module
- Teammate D: design the inter-service communication protocol
```

These modules can be analyzed simultaneously, and the final result can be synthesized later, which is much faster than analyzing them serially.

**Multi-angle code review**

When you need to review code from several dimensions:

```
Scenario: conduct a full security review of the payment module

Create a team:
- Teammate A: focus on security vulnerabilities (SQL injection, XSS, etc.)
- Teammate B: inspect performance issues (N+1 queries, memory leaks, etc.)
- Teammate C: verify completeness of error handling
- Teammate D: evaluate test coverage
```

Each member focuses on one dimension, making the review deeper, and the final report more complete.

**Parallel frontend and backend development**

When you need to build frontend and backend at the same time:

```
Scenario: build a user management feature

Create a team:
- Teammate A (frontend): implement the user list page
- Teammate B (frontend): implement the user edit page
- Teammate C (backend): implement the CRUD API
- Teammate D (coordination): design the API contract and make sure frontend and backend stay aligned
```

Frontend and backend can move in parallel as long as the API contract is defined first, following the contract-first principle.

**Competitive debugging**

When you have multiple possible solutions:

```
Scenario: fix a complex bug with two possible repair strategies

Create a team:
- Teammate A: implement solution 1
- Teammate B: implement solution 2
- Teammate C: evaluate the pros and cons of both
```

Both solutions can be implemented and tested in parallel, and the better one can be chosen afterward.

**Documentation generation**

When you need to produce a large amount of documentation:

```
Scenario: write documentation for the whole project

Create a team:
- Teammate A: write API documentation
- Teammate B: write the deployment guide
- Teammate C: write the development guide
- Teammate D: write the troubleshooting manual
```

Multiple documents can be written at the same time, greatly improving efficiency.

### Scenarios where Agent Teams is not a good fit

**Simple modification tasks**

```
Not suitable: variable renaming, single bug fixes, tiny feature additions
```

For these tasks, the cost of starting a team is greater than the actual work.

**Highly serial tasks**

```
Not suitable: tasks that must happen strictly in sequence
```

If task B cannot start until task A finishes, there is no real space for parallelism.

**Cost-sensitive tasks**

Agent Teams consumes **2 to 4 times** the tokens of a single instance, depending on the team size. If cost is the primary concern, a single instance may be the better choice.

### Decision flowchart

```
Are there multiple independent subtasks?
    │
    ├─ No → Use a single instance
    │
    └─ Yes →
         │
         Can the subtasks be assigned to different files?
         │
         ├─ No → Consider serial execution or split the task further
         │
         └─ Yes →
              │
              Is the cost acceptable (2-4x)?
              │
              ├─ No → Use a single instance
              │
              └─ Yes → Use Agent Teams ✓
```

---

## Cost and performance

Using Agent Teams increases cost, but it can also produce significant efficiency gains. Understanding this tradeoff helps you make informed decisions.

### Cost analysis

**Token consumption and team size**

The token consumption of Agent Teams is roughly **linear** with team size:

| Team size | Relative cost | Suitable scenario |
|---------|---------|---------|
| 1 person (single instance) | 1x | Simple tasks |
| 2-person team | 2-2.5x | Medium complexity |
| 3-person team | 3-4x | Complex tasks |
| 5+ person team | 5-6x+ | Large projects |

**Why it is not perfectly linear**:

- **Startup cost**: each member must receive initial context when it starts
- **Coordination cost**: communication between members through the messaging system also consumes tokens
- **Team Lead cost**: Team Lead usually uses Opus, which is more expensive

**Concrete example numbers** (Claude 4.5 Sonnet):

- Input: $3 per million tokens
- Output: $15 per million tokens

Suppose a task requires:
- Team Lead (Opus): 50K input + 20K output ≈ $2.25
- 3 Teammates (Sonnet): each 30K input + 15K output ≈ $2.7 × 3 = $8.1
- **Total**: about $10.35

The same task on a single Sonnet instance:
- 100K input + 50K output ≈ $1.05

**Cost multiplier**: about 10x

**But time saved**: potentially reduced from 3 hours to 1 hour

### Efficiency gains

**Anthropic internal testing data**:

- Large project refactors: around **50%** improvement in efficiency
- Parallel multi-module development: around **60-70%** improvement
- Documentation generation tasks: around **80%** improvement

**Real case**:

Anthropic's engineering team once used **16 parallel agents** to build a C compiler in about 2 weeks that could compile the Linux 6.9 kernel, around 100,000 lines of Rust code, and it passed 99% of GCC tests.

### Cost optimization strategies

**Strategy 1: mix models**

```
Team Lead: Opus (strong reasoning needed)
Teammates: Sonnet (high value for cost)
Simple tasks: Haiku (cheapest)
```

**Strategy 2: adjust team size dynamically**

```
Analysis phase: 5-person team (multi-angle analysis)
Implementation phase: 3-person team (parallel coding)
Testing phase: 2-person team (testing and fixing)
```

**Strategy 3: use Agent Teams only in selected phases**

Do not use Agent Teams for the entire project. Use it only in the most complex phases:

```
Phase 1 (requirements analysis): single instance
Phase 2 (architecture design): Agent Teams (multiple plans explored in parallel)
Phase 3 (coding): single instance
Phase 4 (code review): Agent Teams (multi-angle review)
Phase 5 (documentation): Agent Teams (parallel writing)
```

### When it is worth it

**Worth it when**:

- The project timeline is tight, and the value of efficiency gains exceeds the token cost
- The task is highly complex, and a single instance is likely to miss details
- You need multi-angle analysis and validation

**Not worth it when**:

- The task is simple, and the overhead of starting a team is too high
- Cost is highly sensitive and the token budget is limited
- The task is highly serial and offers no space for parallelism

---

## Frequently asked questions

### Q1: Is Agent Teams stable? Can it be used in production?

Agent Teams is currently an **experimental feature**, so there may still be bugs and unstable behavior. Recommendations:

- Back up important projects first
- Start with small projects so you can test and get familiar with it
- Follow official release notes to see improvements in new versions
- Report issues to the official team promptly when they appear

### Q2: How many members can I create at most?

There is no hard theoretical limit, but from a practical perspective:

- Small projects: 2 to 3 people
- Medium projects: 3 to 5 people
- Large projects: 5 to 10 people

Too many members introduce the following problems:

- Coordination overhead rises sharply
- Token usage grows linearly
- File conflict probability increases
- Monitoring and management become harder

### Q3: Can team members see each other's context?

**No**. Every Teammate has a completely independent context window. They communicate through the messaging system rather than sharing context directly.

This is a deliberate design choice, and the benefits are:

- One member's reasoning is not polluted by another member's reasoning
- Context does not become chaotic because conversations are too long
- It is closer to how a real team works, where everyone has their own mind

### Q4: How do I switch between different members?

If split-pane mode is not configured, you can use shortcut keys:

- `Shift+Up`: switch to the previous member
- `Shift+Down`: switch to the next member
- `Ctrl+O`: return to the Team Lead

### Q5: What if a task fails?

If one member's task fails:

1. Check the cause of failure by reading that member's output log
2. Reassign the task to another member if needed
3. Intervene manually and help unblock the issue directly

### Q6: Can I add or remove members midway through the process?

Yes. You can issue commands to the Team Lead at any time:

```
Add a new member and let it handle XXX.
```

```
Let Teammate 3 leave the team after finishing the current task.
```

### Q7: Can Agent Teams be used together with MCP and Skills?

Absolutely. In fact, they work even better together:

- **Agent Teams + Skills**: each member can carry different skills
- **Agent Teams + MCP**: different members can access external resources through different MCP servers

```
Create a team:
- Teammate A: carries the frontend-design Skill and is responsible for UI
- Teammate B: accesses the repository through GitHub MCP and handles PR management
- Teammate C: queries data through Database MCP and handles analysis
```

---

## References

### Official resources

- [Official Claude Code documentation](https://docs.anthropic.com/ru-ru/docs/claude-code) - Complete Claude Code documentation
- [Anthropic engineering blog](https://www.anthropic.com/engineering) - Official technical blog and updates

### Agent Teams tutorial collection

**Complete guides in Chinese**:

- [Claude Code Agent Teams complete guide: from introduction to hands-on practice](https://m.blog.csdn.net/u010634066/article/details/157903022) - Includes configuration details, hands-on examples, and the striking case where 16 parallel agents built a C compiler
- [Collaborative development with Claude Code Agent Team: a complete hands-on guide](https://m.blog.csdn.net/u010028049/article/details/158126612) - Full collaborative project workflow
- [Step-by-step guide to setting up and using Claude Code Agent Teams](https://cloud.tencent.com/developer/article/2630088) - Tencent Cloud tutorial with detailed setup instructions

**Getting started in practice**:

- [Hands-on with native Claude Code Agent Teams: from enabling it to running a three-person team](https://www.cnblogs.com/147api/p/19606317) - Three-person team walkthrough
- [Fresh beginner practice with Claude Code Agent Teams](https://m.toutiao.com/article/7606744384960266793/) - Beginner-friendly introduction with best practices such as contract-first
- [No more going solo: let 7 Claudes help you develop at the same time with Agent Teams](https://m.toutiao.com/a7605229732241736202/) - Case study of a 7-person team

**Best practices**:

- [Agent Teams best practices: contract-first, task granularity, and model assignment](https://blog.csdn.net/sinat_37574187/article/details/144727588) - Detailed explanation of 7 best practices
- [A seven-year big-tech veteran's Claude Code field manual: eight rules from beginner to expert](https://new.qq.com/rain/a/20260111A02HE900) - Enterprise-level real-world experience

**Principles and comparisons**:

- [Claude Code Agent Teams: the right way to do multi-agent collaboration](https://post.m.smzdm.com/p/adoezrmz/) - Deep analysis of multi-agent collaboration
- [Claude Code multi-agent team development: the complete guide from principles to pitfalls](https://m.toutiao.com/a7605229732241736202/) - Principles and pitfalls from real usage

### Official guide translations

- [Claude officially released the "Agent Building Guide" (with PDF download)](https://m.blog.csdn.net/sinat_37574187/article/details/144724124) - Official Agent Building Guide
- [Full translated version of Claude's official "Guide to Building Effective Agents"](https://m.blog.csdn.net/gyn_enyaer/article/details/144827922) - Full Chinese translation

### Related technologies

- [Agent Skills standard](https://agentskills.io/) - The Skills ecosystem
- [skills.sh - Agent Skills app store](https://skills.sh/) - 70,000+ skill library

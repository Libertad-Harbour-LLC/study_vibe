# Подробное руководство по TypeScript

::: tip Предисловие
Вы уже умеете писать на JavaScript, но, возможно, сталкивались с такими проблемами:
- Переменной присвоен неверный тип, и это обнаруживается только во время выполнения
- В имени свойства объекта опечатка, отладка занимает полдня
- Неверный тип параметра функции, переписываешь снова и снова

TypeScript — это инструмент, который помогает обнаружить эти проблемы до запуска кода. Прочитав эту статью, вы поймёте, почему TypeScript повышает качество кода, разберётесь в основных концепциях — аннотациях типов, интерфейсах, дженериках — и сможете лучше использовать сгенерированный ИИ код в vibecoding.
:::

**Чему научит вас эта статья?**

| Глава | Содержание | Что вы сможете после изучения |
|-----|------|-----------|
| **Глава 1** | Что такое TypeScript | Понять его связь с JavaScript |
| **Глава 2** | Базовые аннотации типов | Узнать, как помечать тип переменной |
| **Глава 3** | Типы объектов и интерфейсы | Определять типы структур данных |
| **Глава 4** | Типы функций | Помечать типы параметров и возвращаемых значений функций |
| **Глава 5** | Дженерики | Писать переиспользуемый типобезопасный код |
| **Глава 6** | Вывод типов и практические приёмы | Знать, когда нужна явная аннотация |

---

## 1. Что такое TypeScript

::: tip 🤔 Ключевой вопрос
**JavaScript уже достаточно, зачем же нужен TypeScript?** Стоит ли учить ещё один синтаксис?
:::

### 1.1 От «ошибки во время выполнения» к «обнаружению при компиляции»

<div style="display: flex; gap: 20px; margin: 20px 0;">
<div style="flex: 1; padding: 16px; border: 1px solid #e4e7ed; border-radius: 12px;">

**🔴 Боли JavaScript**
- Ошибки типов обнаруживаются только во время выполнения
- Опечатки сложно заметить
- При рефакторинге легко что-то упустить
- Подсказки IDE недостаточно точны

*Как текстовый редактор без проверки орфографии*

</div>
<div style="flex: 1; padding: 16px; border: 1px solid #e4e7ed; border-radius: 12px;">

**✅ Преимущества TypeScript**
- Ошибки обнаруживаются уже при написании кода
- Умные подсказки точнее
- Рефакторинг безопаснее
- Код легче поддерживать

*Как редактор с проверкой орфографии и подсветкой синтаксиса*

</div>
</div>

**Поймём связь двух одной фразой:**

| Технология | Метафора | Назначение |
|------|------|------|
| **JavaScript** | Сырьё | Код, который можно сразу выполнить |
| **TypeScript** | Чертёж + контроль качества | Добавляет JavaScript проверку типов и в итоге компилируется в JavaScript |

### 1.2 Почему в vibecoding тоже нужен TypeScript?

::: warning ИИ тоже допускает ошибки в коде
Один разработчик сгенерировал с помощью ИИ функцию управления пользователями. Написанный ИИ код на JavaScript работал, но была проблема: возраст пользователя должен быть числом, но иногда ему по ошибке присваивалась строка.

В итоге при вычислении «совершеннолетний ли» строка "25" обрабатывалась как строка, из-за чего проверка не срабатывала. Этот баг скрывался долго, пока какой-то пользователь не ввёл нечисловые символы, и он не проявился.

Если бы использовался TypeScript, этот код выдал бы ошибку уже при написании: `Тип "string" нельзя присвоить типу "number"`.

**В этом и ценность TypeScript — когда ИИ ошибается с типом, вы можете обнаружить это сразу.**
:::

### 1.3 На самом деле TypeScript выглядит так

TypeScript — не совершенно новый язык, это лишь «надмножество» JavaScript:

```typescript
// 这是有效的 JavaScript，也是有效的 TypeScript
const name = "张三"
const age = 25
function greet(user) {
  return `Hello ${user}`
}

// 这是 TypeScript 特有的类型注解
const name2: string = "李四"
const age2: number = 30
function greet2(user: string): string {
  return `Hello ${user}`
}
```

**Ключевое понимание:**
- Весь код на JavaScript является корректным кодом на TypeScript
- TypeScript добавляет опциональные **аннотации типов**
- TypeScript в итоге компилируется в JavaScript для выполнения

::: info 💡 Ключевая идея
TypeScript не меняет способ выполнения кода, он лишь помогает проверить корректность типов при компиляции. **Вы можете внедрять TypeScript постепенно** — начиная с добавления типов ключевым переменным.
:::

---

## 2. Базовые аннотации типов

::: tip 🤔 Ключевой вопрос
**Как сказать TypeScript, какого типа должна быть переменная?** Каков синтаксис аннотаций типов?
:::

### 2.1 Синтаксис аннотаций типов

Аннотация типа — это добавление `: тип` после имени переменной:

```typescript
// 语法：变量名: 类型 = 值
const name: string = "张三"
let age: number = 25
let isStudent: boolean = true
```

👇 **Попробуйте сами**: добавьте переменным аннотации типов

<TypeAnnotationDemo />

::: details 🔍 Почему в некоторых местах не нужна аннотация типа?
TypeScript может автоматически вывести тип на основе присваивания:

```typescript
// 这些不需要类型注解，TypeScript 能自动推断
const name = "张三"      // 推断为 string
const age = 25          // 推断为 number
const isActive = true   // 推断为 boolean

// 这些情况需要显式注解
let data  // ❌ 错误：不能推断类型
let data: any  // ✅ 可以，但失去了类型检查的好处

function add(a, b) {  // ❌ 参数类型不明确
  return a + b
}

function add2(a: number, b: number): number {  // ✅ 类型明确
  return a + b
}
```
:::

### 2.2 Базовые типы

TypeScript поддерживает все базовые типы JavaScript:

| Тип | Описание | Пример |
|------|------|------|
| `string` | Строка | `"hello"`, `'你好'` |
| `number` | Число (целые и дробные) | `42`, `3.14` |
| `boolean` | Логическое значение | `true`, `false` |
| `null` / `undefined` | Пустое значение | `null`, `undefined` |
| `array` | Массив | `number[]`, `string[]` |
| `object` | Объект | `{ name: string; age: number }` |

**Два способа записи типа массива:**

```typescript
// 写法 1：类型[]（更常用）
const numbers: number[] = [1, 2, 3, 4, 5]
const names: string[] = ["张三", "李四", "王五"]

// 写法 2：Array<类型>
const numbers2: Array<number> = [1, 2, 3, 4, 5]
const names2: Array<string> = ["张三", "李四", "王五"]
```

**Особые типы:**

```typescript
// any：任意类型（慎用，相当于关闭类型检查）
let data: any = 42
data = "现在可以是字符串"
data = { name: "张三" }  // 也可以是对象

// unknown：类型安全的 any
let value: unknown = 42
// if (typeof value === "number") {
//   console.log(value + 10)  // 需要先检查类型才能用
// }

// void：没有返回值
function log(message: string): void {
  console.log(message)
}

// never：永远不会返回
function error(message: string): never {
  throw new Error(message)
}
```

::: info 💡 Приёмы распознавания
- Увидев `: string` → это аннотация типа string
- Увидев `: number[]` → это аннотация массива чисел
- Увидев `: void` → у этой функции нет возвращаемого значения
:::

---

## 3. Типы объектов и интерфейсы

::: tip 🤔 Ключевой вопрос
**Как определить тип объекта?** Какого типа должны быть свойства объекта?
:::

### 3.1 Интерфейс (Interface): определяем «форму» объекта

Интерфейс — основной способ определения типа объекта в TypeScript:

```typescript
// 定义一个 User 接口
interface User {
  id: number
  name: string
  email: string
  age?: number  // 可选属性
}

// 使用接口
const user: User = {
  id: 1,
  name: "张三",
  email: "zhangsan@example.com",
  age: 25
}

// age 是可选的，可以不提供
const user2: User = {
  id: 2,
  name: "李四",
  email: "lisi@example.com"
}
```

👇 **Попробуйте сами**: создайте объект, соответствующий определению интерфейса

<InterfaceDemo />

::: details 🔍 Другие особенности интерфейсов
```typescript
// 只读属性
interface User {
  readonly id: number  // id 创建后不能修改
  name: string
}

const user: User = {
  id: 1,
  name: "张三"
}

user.id = 2  // ❌ 错误：不能修改只读属性
user.name = "李四"  // ✅ 可以修改

// 函数类型
interface User {
  name: string
  greet: () => string  // greet 是一个函数，返回 string
}

const user: User = {
  name: "张三",
  greet: () => "Hello"
}

// 继承接口
interface Admin extends User {
  permissions: string[]
}

const admin: Admin = {
  name: "管理员",
  greet: () => "Hello Admin",
  permissions: ["read", "write", "delete"]
}
```
:::

### 3.2 Псевдоним типа (Type Alias)

Помимо интерфейса, можно определить псевдоним типа через `type`:

```typescript
// 类型别名
type User = {
  id: number
  name: string
  email: string
}

// 联合类型
type Status = "pending" | "success" | "error"

const status: Status = "success"  // ✅
// const status2: Status = "failed"  // ❌ 错误：不在联合类型中

// 交叉类型（合并多个类型）
type User = {
  id: number
  name: string
}

type Timestamp = {
  createdAt: Date
  updatedAt: Date
}

type UserWithTimestamp = User & Timestamp

const user: UserWithTimestamp = {
  id: 1,
  name: "张三",
  createdAt: new Date(),
  updatedAt: new Date()
}
```

**Интерфейс vs псевдоним типа:**

| Характеристика | interface | type |
|------|-----------|------|
| Расширение | `extends` | `&` пересечение типов |
| Повторное объявление | Автоматически объединяется | Выдаёт ошибку |
| Сценарий применения | Форма объекта, классы | Объединение типов, пересечение типов, псевдонимы базовых типов |

::: info 💡 Приёмы распознавания
- Увидев `interface` → это определение типа объекта
- Увидев `type` → это создание псевдонима типа
- Увидев `?` → это опциональное свойство
- Увидев `readonly` → это свойство только для чтения
:::

---

## 4. Типы функций

::: tip 🤔 Ключевой вопрос
**Как пометить типы параметров и возвращаемого значения функции?**
:::

### 4.1 Типы параметров и тип возвращаемого значения

```typescript
// 完整的函数类型注解
function add(a: number, b: number): number {
  return a + b
}

// 箭头函数
const multiply = (a: number, b: number): number => {
  return a * b
}

// 没有返回值
function log(message: string): void {
  console.log(message)
}

// 返回多种类型（联合类型）
function parseInput(input: string): number | string {
  const num = parseFloat(input)
  return isNaN(num) ? input : num
}
```

### 4.2 Опциональные параметры и параметры по умолчанию

```typescript
// 可选参数（用 ? 标记）
function greet(name: string, title?: string): string {
  return title ? `${title} ${name}` : name
}

greet("张三")  // "张三"
greet("张三", "先生")  // "先生 张三"

// 默认参数
function greet2(name: string, title: string = "朋友"): string {
  return `${title} ${name}`
}

greet2("李四")  // "朋友 李四"
greet2("李四", "博士")  // "博士 李四"
```

### 4.3 Тип функции как параметр

```typescript
// 接受函数作为参数
function calculate(
  a: number,
  b: number,
  operation: (x: number, y: number) => number
): number {
  return operation(a, b)
}

calculate(10, 5, (x, y) => x + y)  // 15
calculate(10, 5, (x, y) => x * y)  // 50

// 更清晰的写法：先定义函数类型
type Operation = (x: number, y: number) => number

function calculate2(
  a: number,
  b: number,
  operation: Operation
): number {
  return operation(a, b)
}
```

::: info 💡 Приёмы распознавания
- Увидев `(a: number, b: number) => number` → это тип функции, описывающий параметры и возвращаемое значение
- Увидев `: void` → у функции нет возвращаемого значения
- Увидев `?` → параметр опциональный
:::

---

## 5. Дженерики

::: tip 🤔 Ключевой вопрос
**Как писать код, который обрабатывает разные типы, но сохраняет типобезопасность?**
:::

### 5.1 Базовая концепция дженериков

Дженерики позволяют при определении функции, интерфейса или класса не указывать конкретный тип заранее, а указывать его при использовании:

```typescript
// 泛型函数：T 是类型变量
function identity<T>(arg: T): T {
  return arg
}

// 使用时明确指定类型
const num1 = identity<number>(42)  // 类型是 number
const str1 = identity<string>("hello")  // 类型是 string

// 类型推断：TypeScript 能自动推断
const num2 = identity(42)  // 推断为 number
const str2 = identity("hello")  // 推断为 string
```

👇 **Попробуйте сами**: используйте дженерики для обработки данных разных типов

<GenericDemo />

### 5.2 Ограничения дженериков

Ограничиваем дженерик условием, которому он должен удовлетворять:

```typescript
// 约束 T 必须有 length 属性
interface HasLength {
  length: number
}

function logLength<T extends HasLength>(arg: T): void {
  console.log(arg.length)
}

logLength("hello")  // ✅ 字符串有 length
logLength([1, 2, 3])  // ✅ 数组有 length
// logLength(42)  // ❌ 数字没有 length 属性
```

### 5.3 Дженерик-интерфейсы и классы

```typescript
// 泛型接口
interface Box<T> {
  value: T
  getValue(): T
}

const numberBox: Box<number> = {
  value: 42,
  getValue: () => 42
}

const stringBox: Box<string> = {
  value: "hello",
  getValue: () => "hello"
}

// 泛型类
class Storage<T> {
  private items: T[] = []

  add(item: T): void {
    this.items.push(item)
  }

  get(index: number): T {
    return this.items[index]
  }
}

const numberStorage = new Storage<number>()
numberStorage.add(1)
numberStorage.add(2)
// numberStorage.add("string")  // ❌ 错误

const stringStorage = new Storage<string>()
stringStorage.add("hello")
// stringStorage.add(1)  // ❌ 错误
```

::: info 💡 Приёмы распознавания
- Увидев `<T>` → это переменная типа-дженерика
- Увидев `<T extends SomeType>` → ограничение дженерика
- Увидев `Array<T>` или `Promise<T>` → встроенный дженерик-тип
:::

---

## 6. Вывод типов и практические приёмы

::: tip 🤔 Ключевой вопрос
**Когда нужна явная аннотация типа? Когда можно полагаться на вывод?**
:::

### 6.1 Вывод типов

TypeScript может автоматически вывести тип на основе контекста:

```typescript
// 变量初始化时的推断
const name = "张三"  // 推断为 string
const age = 25  // 推断为 number
const isActive = true  // 推断为 boolean

// 数组推断
const numbers = [1, 2, 3]  // 推断为 number[]
const mixed = [1, "hello", true]  // 推断为 (number | string | boolean)[]

// 函数返回值推断
function add(a: number, b: number) {
  return a + b  // 推断返回值为 number
}
```

👇 **Попробуйте сами**: понаблюдайте, как TypeScript выводит типы

<TypeInferenceDemo />

### 6.2 Когда использовать явную аннотацию типа

::: details Сценарии, где рекомендуется использовать вывод типов
```typescript
// ✅ 推荐：简单的字面量赋值
const count = 0
const name = "张三"
const isActive = true

// ✅ 推荐：函数返回值可以推断
function getUserId(user: User) {
  return user.id  // 推断为 number
}
```
:::

::: details Сценарии, где рекомендуется использовать явную аннотацию
```typescript
// ✅ 推荐：函数参数（必须）
function add(a: number, b: number) {
  return a + b
}

// ✅ 推荐：对象属性类型不明确
const user: {
  id: number
  name: string
  metadata: Record<string, any>
} = {
  id: 1,
  name: "张三",
  metadata: {}  // 可能推断为 {}，需要明确指定
}

// ✅ 推荐：函数返回类型复杂
function getUser(): User | null {
  // ...
  return null
}

// ✅ 推荐：公共 API
export function calculateTotal(prices: number[]): number {
  return prices.reduce((sum, price) => sum + price, 0)
}
```
:::

### 6.3 Защитники типов

Проверка типа во время выполнения:

```typescript
// typeof 类型守卫
function processValue(value: string | number) {
  if (typeof value === "string") {
    // 这里 TypeScript 知道 value 是 string
    console.log(value.toUpperCase())
  } else {
    // 这里 TypeScript 知道 value 是 number
    console.log(value * 2)
  }
}

// instanceof 类型守卫
class Dog {
  bark() {
    console.log("汪汪")
  }
}

class Cat {
  meow() {
    console.log("喵喵")
  }
}

function makeSound(animal: Dog | Cat) {
  if (animal instanceof Dog) {
    animal.bark()  // TypeScript 知道这是 Dog
  } else {
    animal.meow()  // TypeScript 知道这是 Cat
  }
}

// 自定义类型守卫
interface User {
  name: string
  email: string
}

function isUser(value: any): value is User {
  return (
    typeof value === "object" &&
    value !== null &&
    typeof value.name === "string" &&
    typeof value.email === "string"
  )
}

function processValue(value: unknown) {
  if (isUser(value)) {
    // 这里 value 是 User
    console.log(value.name)
  }
}
```

### 6.4 Полезные служебные типы

TypeScript предоставляет несколько встроенных служебных типов:

```typescript
// Partial：将所有属性变为可选
interface User {
  id: number
  name: string
  email: string
}

type PartialUser = Partial<User>
// 等价于：{ id?: number; name?: string; email?: string }

// Required：将所有属性变为必需
type RequiredUser = Required<PartialUser>
// 等价于：{ id: number; name: number; email: string }

// Pick：只保留指定的属性
type UserBasicInfo = Pick<User, "id" | "name">
// 等价于：{ id: number; name: string }

// Omit：排除指定的属性
type UserWithoutEmail = Omit<User, "email">
// 等价于：{ id: number; name: string }

// Record：创建对象类型
type UserRoles = Record<string, boolean>
// 等价于：{ [key: string]: boolean }
```

---

## 7. Практические приёмы: использование TypeScript в vibecoding

::: tip 🤔 Ключевой вопрос
**Как лучше использовать TypeScript при разработке с помощью ИИ?**
:::

### 7.1 Заставить ИИ генерировать типобезопасный код

**❌ Плохой промпт:**
```
帮我写一个用户管理功能
```

**✅ Хороший промпт:**
```
帮我写一个用户管理功能，使用 TypeScript。

数据结构定义如下：
interface User {
  id: number
  name: string
  email: string
  age: number
}

需要实现：
1. 获取用户列表：返回 User[]
2. 创建用户：接受 Partial<User>，返回 User
3. 更新用户：接受 id 和 Partial<User>，返回 User
4. 删除用户：接受 id，返回 void

请确保所有函数都有完整的类型注解。
```

### 7.2 Понимание сообщений об ошибках TypeScript

**Распространённые ошибки и их значение:**

| Сообщение об ошибке | Значение | Способ решения |
|---------|------|---------|
| `Type 'X' is not assignable to type 'Y'` | Тип X нельзя присвоить типу Y | Проверьте, совпадают ли типы, или выполните приведение типа |
| `Property 'X' does not exist on type 'Y'` | На типе Y нет свойства X | Проверьте написание имени свойства или определите это свойство |
| `Argument of type 'X' is not assignable to parameter of type 'Y'` | Тип аргумента не совпадает | Проверьте типы аргументов при вызове функции |
| `Type 'X' is missing the following properties from type 'Y'` | У типа X отсутствуют некоторые свойства типа Y | Дополните недостающие свойства |

### 7.3 Постепенное внедрение TypeScript

Если у вас есть проект на JavaScript, можно постепенно мигрировать на TypeScript:

1. **Шаг 1: переименуйте файл в `.ts`**
   ```bash
   # 从 utils.js 改为 utils.ts
   mv utils.js utils.ts
   ```

2. **Шаг 2: исправьте очевидные ошибки типов**
   ```typescript
   // 如果报错：Parameter 'a' implicitly has an 'any' type
   // 添加类型注解
   function add(a: number, b: number) {
     return a + b
   }
   ```

3. **Шаг 3: постепенно добавляйте определения типов**
   ```typescript
   // 先用 any 快速修复
   function processUser(user: any) {
     // ...
   }

   // 后续再完善类型
   interface User {
     id: number
     name: string
   }

   function processUser(user: User) {
     // ...
   }
   ```

4. **Шаг 4: включите более строгую проверку типов**
   ```json
   // tsconfig.json
   {
     "compilerOptions": {
       "strict": true,  // 启用严格模式
       "noImplicitAny": true,  // 禁止隐式 any
       "strictNullChecks": true  // 严格空值检查
     }
   }
   ```

---

## 8. Код, который вы теперь должны уметь распознавать

- Увидев `: string` → это аннотация типа string
- Увидев `: number[]` → это аннотация массива чисел
- Увидев `interface User` → это определение типа объекта
- Увидев `type User =` → это псевдоним типа
- Увидев `<T>` → это дженерик
- Увидев `extends` → наследование интерфейса или ограничение дженерика
- Увидев `?` → опциональное свойство
- Увидев `readonly` → свойство только для чтения
- Увидев `|` → объединение типов
- Увидев `&` → пересечение типов

**Если вы внимательно прочитали разделы «Подробнее» в каждой главе, то освоили ещё и эти ключевые концепции:**

- **Аннотация типа**: явно сообщает TypeScript тип переменной
- **Интерфейс**: определяет структуру и тип объекта
- **Дженерики**: написание переиспользуемого типобезопасного кода
- **Вывод типов**: TypeScript автоматически выводит типы
- **Защитники типов**: проверка типа во время выполнения
- **Служебные типы**: Partial, Required, Pick, Omit и др.

::: info 💡 Так говорите ИИ при возникновении проблемы
- «Как написать аннотацию типа для этой функции? Параметр — X, возвращаемое значение — Y»
- «Помоги определить интерфейс, описывающий эту структуру данных: ...»
- «Что означает эта ошибка TypeScript? Как её исправить?»
- «Как добавить ограничение этой дженерик-функции, чтобы гарантировать, что у T есть некое свойство?»
:::

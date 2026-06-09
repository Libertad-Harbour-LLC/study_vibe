# Проектирование архитектуры бэкенд-проекта

::: tip 🎯 Ключевой вопрос
**От простого скрипта до крупной распределённой системы — как выбрать подходящую архитектуру для бэкенд-проектов разного масштаба и на разных языках?** Это как спросить: от семейной мастерской до большой фабрики, как проектировать разные производственные линии в зависимости от объёма выпуска и технологии? Хорошая бэкенд-архитектура должна эволюционировать вместе с ростом бизнеса, одновременно полностью раскрывая особенности языка.
:::

---

## 1. Эволюция архитектуры: от скрипта к системе

### 1.1 Деление уровней архитектуры по количеству пользователей

Архитектура бэкенд-проекта должна соответствовать масштабу бизнеса и количеству пользователей:

| Уровень | Пользователей | Конкурентность | Типичный сценарий | Ключевой фокус |
|------|--------|--------|----------|------------|
| **Начальный** | < 1k | < 100 | Личные проекты, MVP, внутренние инструменты | Быстрая разработка, простое развёртывание |
| **Продвинутый** | 1k-100k | 100-10k | Корпоративные системы, SaaS, средние и малые платформы | Слоистая архитектура, стандарты кода |
| **Корпоративный** | > 100k | > 10k | Крупные платформы, интернет-приложения | Микросервисы, высокая доступность, оптимизация производительности |

### 1.2 Выбор стиля архитектуры по особенностям языка

У разных языков программирования разная философия проектирования и экосистема, и проектирование архитектуры должно следовать особенностям языка:

| Язык | Философия проектирования | Рекомендуемый стиль архитектуры | Представительные фреймворки |
|------|----------|--------------|----------|
| **Node.js** | Событийная модель, неблокирующий I/O | Слоистая архитектура + асинхронные потоки | Express, NestJS, Fastify |
| **Python** | Лаконичность и элегантность, быстрая разработка | MTV/MVC, слоистая архитектура | Django, Flask, FastAPI |
| **Go** | Простота и эффективность, нативная конкурентность | Лаконичные слои, микросервисы | Gin, Echo, Fiber |
| **Java** | Корпоративный уровень, строгая типизация | Строгое деление на слои, предметно-ориентированное | Spring Boot, Spring Cloud |

::: tip 💡 Принципы выбора архитектуры
1. **Не переусложняйте**: маленьким проектам нужна простая архитектура, сложная архитектура нужна только большим
2. **Следуйте особенностям языка**: не пытайтесь писать код в стиле Java на Python
3. **Постепенная эволюция**: начинайте с простого, постепенно оптимизируйте по мере роста бизнеса
4. **Знакомство команды**: выбирайте стиль архитектуры, знакомый команде, чтобы снизить стоимость обучения
:::

---

## 2. Начальная архитектура (пользователей < 1k)

### 2.1 Сценарии применения

- Личные проекты, учебные упражнения
- MVP стартапа (минимально жизнеспособный продукт)
- Внутренние инструменты, админ-панели
- Проверка прототипа, демонстрация концепции

### 2.2 Node.js — стиль лаконичного скрипта

**Особенность**: один файл или простое деление, быстрый выход в продакшен

```
my-node-api/
├── src/
│   ├── app.js              # точка входа приложения
│   ├── routes.js           # определение маршрутов
│   ├── db.js               # подключение к базе данных
│   └── utils.js            # вспомогательные функции
├── .env                    # переменные окружения
├── package.json
└── README.md
```

**Пример кода**:

```javascript
// src/app.js
const express = require('express');
const app = express();

app.use(express.json());

// Маршруты прямо в точке входа (подходит для малого числа эндпоинтов)
app.get('/users', async (req, res) => {
  const users = await db.query('SELECT * FROM users');
  res.json(users);
});

app.post('/users', async (req, res) => {
  const { name, email } = req.body;
  const result = await db.query(
    'INSERT INTO users (name, email) VALUES (?, ?)',
    [name, email]
  );
  res.status(201).json({ id: result.insertId });
});

app.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

**Открытые проекты для справки**:
- [expressjs/express](https://github.com/expressjs/express) - официальные примеры
- [vercel/micro](https://github.com/vercel/micro) - микросервисный стиль

### 2.3 Python — стиль быстрого прототипа

**Особенность**: используя лаконичность Python, быстро реализовать функциональность

```
my-python-api/
├── app.py                  # основное приложение
├── models.py               # модели данных
├── config.py               # конфигурация
├── requirements.txt
└── README.md
```

**Пример кода (Flask)**:

```python
# app.py
from flask import Flask, request, jsonify
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///app.db'
db = SQLAlchemy(app)

# Определение модели
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(80), nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)

# Маршруты
@app.route('/users', methods=['GET'])
def get_users():
    users = User.query.all()
    return jsonify([{'id': u.id, 'name': u.name, 'email': u.email} for u in users])

@app.route('/users', methods=['POST'])
def create_user():
    data = request.json
    user = User(name=data['name'], email=data['email'])
    db.session.add(user)
    db.session.commit()
    return jsonify({'id': user.id}), 201

if __name__ == '__main__':
    app.run(debug=True)
```

**Открытые проекты для справки**:
- [pallets/flask](https://github.com/pallets/flask) - официальные примеры
- [tiangolo/fastapi](https://github.com/tiangolo/fastapi) - современный асинхронный стиль

### 2.4 Go — стиль лаконичной стандартной библиотеки

**Особенность**: используя стандартную библиотеку Go, минимум зависимостей

```
my-go-api/
├── main.go                 # точка входа
├── handlers.go             # обработчики
├── models.go               # модели
├── db.go                   # база данных
├── go.mod
└── README.md
```

**Пример кода**:

```go
// main.go
package main

import (
    "database/sql"
    "encoding/json"
    "log"
    "net/http"
    _ "github.com/mattn/go-sqlite3"
)

type User struct {
    ID    int    `json:"id"`
    Name  string `json:"name"`
    Email string `json:"email"`
}

var db *sql.DB

func main() {
    var err error
    db, err = sql.Open("sqlite3", "./app.db")
    if err != nil {
        log.Fatal(err)
    }

    http.HandleFunc("/users", usersHandler)
    log.Println("Server starting on :8080")
    log.Fatal(http.ListenAndServe(":8080", nil))
}

func usersHandler(w http.ResponseWriter, r *http.Request) {
    switch r.Method {
    case http.MethodGet:
        getUsers(w, r)
    case http.MethodPost:
        createUser(w, r)
    }
}

func getUsers(w http.ResponseWriter, r *http.Request) {
    rows, _ := db.Query("SELECT id, name, email FROM users")
    defer rows.Close()

    var users []User
    for rows.Next() {
        var u User
        rows.Scan(&u.ID, &u.Name, &u.Email)
        users = append(users, u)
    }

    json.NewEncoder(w).Encode(users)
}
```

**Открытые проекты для справки**:
- [golang/go](https://github.com/golang/go) - примеры стандартной библиотеки
- [go-chi/chi](https://github.com/go-chi/chi) - лёгкий роутер

### 2.5 Java — стартовый стиль Spring Boot

**Особенность**: используя автоконфигурацию Spring Boot, быстрый старт

```
my-spring-app/
├── src/main/java/com/example/
│   ├── controller/
│   │   └── UserController.java
│   ├── model/
│   │   └── User.java
│   ├── repository/
│   │   └── UserRepository.java
│   └── Application.java
├── src/main/resources/
│   └── application.yml
├── pom.xml
└── README.md
```

**Пример кода**:

```java
// Application.java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}

// User.java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String email;
    // getters and setters
}

// UserRepository.java
public interface UserRepository extends JpaRepository<User, Long> {
}

// UserController.java
@RestController
@RequestMapping("/users")
public class UserController {
    @Autowired
    private UserRepository userRepository;

    @GetMapping
    public List<User> getAllUsers() {
        return userRepository.findAll();
    }

    @PostMapping
    public User createUser(@RequestBody User user) {
        return userRepository.save(user);
    }
}
```

**Открытые проекты для справки**:
- [spring-projects/spring-boot](https://github.com/spring-projects/spring-boot) - официальные примеры
- [spring-projects/spring-petclinic](https://github.com/spring-projects/spring-petclinic) - классический пример

---

## 3. Продвинутая архитектура (пользователей 1k-100k)

### 3.1 Сценарии применения

- Корпоративные системы управления (ERP, CRM, OA)
- SaaS-приложения
- Платформы электронной коммерции
- Проекты, требующие работы нескольких команд

### 3.2 Подробно о слоистой архитектуре

Для продвинутых проектов рекомендуется **четырёхслойная архитектура** (Controller-Service-Repository-Model):

```
project/
├── src/
│   ├── controllers/          # слой контроля: обработка HTTP-запросов
│   ├── services/             # слой сервисов: бизнес-логика
│   ├── repositories/         # слой данных: доступ к данным
│   ├── models/               # слой моделей: структуры данных
│   ├── middlewares/          # промежуточное ПО
│   ├── utils/                # вспомогательные функции
│   ├── config/               # конфигурация
│   └── routes/               # определение маршрутов
├── tests/
├── docs/
└── scripts/
```

### 3.3 Node.js — корпоративное деление на слои

**Открытые проекты для справки**:
- [nestjs/nest](https://github.com/nestjs/nest) - корпоративный фреймворк Node.js
- [goldbergyoni/nodebestpractices](https://github.com/goldbergyoni/nodebestpractices) - лучшие практики Node.js

```
node-enterprise/
├── src/
│   ├── modules/              # организация по функциональным модулям
│   │   ├── users/
│   │   │   ├── users.controller.ts
│   │   │   ├── users.service.ts
│   │   │   ├── users.repository.ts
│   │   │   ├── users.module.ts
│   │   │   └── dto/
│   │   ├── orders/
│   │   └── products/
│   ├── common/               # общие модули
│   │   ├── filters/          # фильтры исключений
│   │   ├── guards/           # гарды
│   │   ├── interceptors/     # перехватчики
│   │   └── pipes/            # пайпы
│   ├── config/
│   └── main.ts
```

**Пример кода NestJS**:

```typescript
// users/users.controller.ts
@Controller('users')
export class UsersController {
  constructor(private readonly usersService: UsersService) {}

  @Get()
  findAll(@Query() query: QueryUserDto) {
    return this.usersService.findAll(query);
  }

  @Post()
  create(@Body() createUserDto: CreateUserDto) {
    return this.usersService.create(createUserDto);
  }
}

// users/users.service.ts
@Injectable()
export class UsersService {
  constructor(
    @InjectRepository(User)
    private usersRepository: Repository<User>,
  ) {}

  async findAll(query: QueryUserDto) {
    const [data, total] = await this.usersRepository.findAndCount({
      skip: (query.page - 1) * query.limit,
      take: query.limit,
    });
    return { data, total };
  }

  async create(createUserDto: CreateUserDto) {
    const user = this.usersRepository.create(createUserDto);
    return this.usersRepository.save(user);
  }
}
```

### 3.4 Python — стиль Django/DRF

**Открытые проекты для справки**:
- [django/django](https://github.com/django/django) - официальный проект
- [encode/django-rest-framework](https://github.com/encode/django-rest-framework) - REST-фреймворк
- [cookiecutter/cookiecutter-django](https://github.com/cookiecutter/cookiecutter-django) - шаблон проекта

```
django-enterprise/
├── apps/
│   ├── users/                # приложение пользователей
│   │   ├── models.py
│   │   ├── views.py          # API-представления
│   │   ├── serializers.py    # сериализаторы
│   │   ├── permissions.py    # права доступа
│   │   ├── urls.py
│   │   └── tests/
│   ├── orders/
│   └── products/
├── config/                   # конфигурация проекта
│   ├── settings/
│   │   ├── base.py
│   │   ├── development.py
│   │   └── production.py
│   ├── urls.py
│   └── wsgi.py
├── utils/                    # общие инструменты
├── templates/
├── static/
└── manage.py
```

**Пример кода Django REST Framework**:

```python
# users/models.py
from django.contrib.auth.models import AbstractUser

class User(AbstractUser):
    phone = models.CharField(max_length=20, blank=True)
    avatar = models.URLField(blank=True)

# users/serializers.py
from rest_framework import serializers

class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['id', 'username', 'email', 'phone', 'avatar']

# users/views.py
from rest_framework import viewsets, permissions
from rest_framework.decorators import action

class UserViewSet(viewsets.ModelViewSet):
    queryset = User.objects.all()
    serializer_class = UserSerializer
    permission_classes = [permissions.IsAuthenticated]

    @action(detail=False, methods=['get'])
    def me(self, request):
        serializer = self.get_serializer(request.user)
        return Response(serializer.data)

# users/urls.py
from rest_framework.routers import DefaultRouter

router = DefaultRouter()
router.register(r'users', UserViewSet)

urlpatterns = router.urls
```

### 3.5 Go — стиль чистой архитектуры

**Открытые проекты для справки**:
- [gin-gonic/gin](https://github.com/gin-gonic/gin) - веб-фреймворк
- [go-kit/kit](https://github.com/go-kit/kit) - набор инструментов для микросервисов
- [bxcodec/go-clean-arch](https://github.com/bxcodec/go-clean-arch) - пример чистой архитектуры

```
go-enterprise/
├── cmd/
│   └── api/                  # точка входа приложения
│       └── main.go
├── internal/                 # приватный код
│   ├── domain/               # доменный слой (сущности, интерфейсы)
│   │   ├── user.go
│   │   └── repository.go
│   ├── usecase/              # слой сценариев (бизнес-логика)
│   │   └── user_usecase.go
│   ├── delivery/             # транспортный слой (HTTP/gRPC)
│   │   └── http/
│   │       └── user_handler.go
│   ├── repository/           # слой репозиториев (доступ к данным)
│   │   └── user_repository.go
│   └── config/
├── pkg/                      # публичные библиотеки
├── migrations/
└── go.mod
```

**Пример кода чистой архитектуры**:

```go
// domain/user.go
type User struct {
    ID        int64     `json:"id"`
    Username  string    `json:"username"`
    Email     string    `json:"email"`
    CreatedAt time.Time `json:"created_at"`
}

// domain/repository.go
type UserRepository interface {
    GetByID(ctx context.Context, id int64) (*User, error)
    GetByEmail(ctx context.Context, email string) (*User, error)
    Create(ctx context.Context, user *User) error
    Update(ctx context.Context, user *User) error
}

// usecase/user_usecase.go
type UserUsecase struct {
    userRepo UserRepository
}

func (u *UserUsecase) GetByID(ctx context.Context, id int64) (*User, error) {
    return u.userRepo.GetByID(ctx, id)
}

func (u *UserUsecase) Create(ctx context.Context, user *User) error {
    // Бизнес-логика: проверяем, не существует ли уже email
    existing, _ := u.userRepo.GetByEmail(ctx, user.Email)
    if existing != nil {
        return errors.New("email already exists")
    }
    return u.userRepo.Create(ctx, user)
}

// delivery/http/user_handler.go
type UserHandler struct {
    UserUsecase *usecase.UserUsecase
}

func (h *UserHandler) GetUser(c *gin.Context) {
    id, _ := strconv.ParseInt(c.Param("id"), 10, 64)
    user, err := h.UserUsecase.GetByID(c.Request.Context(), id)
    if err != nil {
        c.JSON(404, gin.H{"error": "user not found"})
        return
    }
    c.JSON(200, user)
}
```

### 3.6 Java — корпоративный уровень Spring Boot

**Открытые проекты для справки**:
- [spring-projects/spring-boot](https://github.com/spring-projects/spring-boot)
- [spring-cloud-samples](https://github.com/spring-cloud-samples) - примеры микросервисов
- [ali-baba/spring-cloud-alibaba](https://github.com/alibaba/spring-cloud-alibaba) - микросервисы Alibaba

```
spring-enterprise/
├── src/main/java/com/example/
│   ├── application/          # слой приложения
│   │   ├── controller/       # контроллеры
│   │   ├── dto/              # объекты передачи данных
│   │   └── assembler/        # сборщики
│   ├── domain/               # доменный слой
│   │   ├── entity/           # сущности
│   │   ├── valueobject/      # объекты-значения
│   │   ├── repository/       # интерфейсы репозиториев
│   │   └── service/          # доменные сервисы
│   ├── infrastructure/       # слой инфраструктуры
│   │   ├── repository/       # реализации репозиториев
│   │   ├── config/           # конфигурация
│   │   └── common/           # вспомогательные классы
│   └── Application.java
├── src/main/resources/
│   ├── application.yml
│   └── mapper/
└── src/test/
```

**Пример кода предметно-ориентированного проектирования (DDD)**:

```java
// domain/entity/User.java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(nullable = false)
    private String username;
    
    @Column(nullable = false, unique = true)
    private String email;
    
    @Embedded
    private UserStatus status;
    
    // Доменные методы
    public void deactivate() {
        this.status = UserStatus.INACTIVE;
    }
    
    public boolean isActive() {
        return this.status == UserStatus.ACTIVE;
    }
}

// domain/repository/UserRepository.java
public interface UserRepository {
    Optional<User> findById(Long id);
    Optional<User> findByEmail(String email);
    User save(User user);
    void delete(User user);
}

// application/controller/UserController.java
@RestController
@RequestMapping("/api/v1/users")
@RequiredArgsConstructor
public class UserController {
    private final UserService userService;
    private final UserAssembler userAssembler;

    @GetMapping("/{id}")
    public ResponseEntity<UserDTO> getUser(@PathVariable Long id) {
        User user = userService.findById(id);
        return ResponseEntity.ok(userAssembler.toDTO(user));
    }

    @PostMapping
    public ResponseEntity<UserDTO> createUser(@RequestBody @Valid CreateUserRequest request) {
        User user = userService.createUser(request);
        return ResponseEntity.status(HttpStatus.CREATED)
                .body(userAssembler.toDTO(user));
    }
}

// infrastructure/repository/UserRepositoryImpl.java
@Repository
@RequiredArgsConstructor
public class UserRepositoryImpl implements UserRepository {
    private final UserJpaRepository jpaRepository;

    @Override
    public Optional<User> findById(Long id) {
        return jpaRepository.findById(id);
    }

    @Override
    public User save(User user) {
        return jpaRepository.save(user);
    }
}
```

---

## 4. Корпоративная архитектура (пользователей > 100k)

### 4.1 Сценарии применения

- Крупные интернет-платформы
- Финансовые торговые системы
- Высоконагруженные системы электронной коммерции
- Крупные проекты, требующие работы нескольких команд

### 4.2 Микросервисная архитектура

Когда монолитное приложение не может удовлетворить требования, нужно рассмотреть микросервисную архитектуру:

```
microservices-platform/
├── api-gateway/              # API-шлюз
│   ├── src/
│   └── Dockerfile
├── services/                 # бизнес-сервисы
│   ├── user-service/         # сервис пользователей
│   ├── order-service/        # сервис заказов
│   ├── product-service/      # сервис товаров
│   └── payment-service/      # сервис оплаты
├── shared/                   # общие библиотеки
│   ├── proto/                # Protocol Buffers
│   ├── common-lib/
│   └── event-contracts/
├── infrastructure/           # инфраструктура
│   ├── docker-compose.yml
│   ├── kubernetes/
│   └── terraform/
└── docs/
```

### 4.3 Микросервисные фреймворки для каждого языка

| Язык | Микросервисный фреймворк | Обнаружение сервисов | Центр конфигурации | Трассировка |
|------|------------|----------|----------|----------|
| **Node.js** | NestJS + gRPC | Consul | etcd | Jaeger |
| **Python** | FastAPI + Nameko | Eureka | Consul | Zipkin |
| **Go** | Go-kit + gRPC | etcd | etcd | OpenTelemetry |
| **Java** | Spring Cloud | Nacos | Nacos | SkyWalking |

### 4.4 Проектирование репозитория кода (Monorepo vs Polyrepo)

**Monorepo (единый репозиторий)**:

```
monorepo/
├── services/
│   ├── user-service/         # независимый сервис
│   │   ├── src/
│   │   ├── package.json
│   │   └── Dockerfile
│   ├── order-service/
│   └── product-service/
├── shared/
│   ├── types/                # общие типы
│   ├── utils/                # общие инструменты
│   └── proto/                # общие протоколы
├── packages/
│   ├── eslint-config/        # общая конфигурация ESLint
│   └── ts-config/            # общая конфигурация TS
├── docker-compose.yml
└── package.json              # корневой package.json
```

**Плюсы**:
- Удобный обмен кодом
- Единая сборка и выпуск
- Лёгкий рефакторинг

**Минусы**:
- Громоздкий репозиторий кода
- Сложное управление правами доступа

**Polyrepo (множество репозиториев)**:

Каждый сервис в отдельном репозитории:
- `github.com/company/user-service`
- `github.com/company/order-service`
- `github.com/company/shared-lib`

**Плюсы**:
- Независимая эволюция сервисов
- Автономия команд
- Чёткие права доступа

**Минусы**:
- Сложный обмен кодом
- Сложное управление версиями

### 4.5 Проектирование слоя данных

**Стратегия выбора базы данных**:

| Тип данных | Рекомендуемая база данных | Сценарий применения |
|----------|------------|----------|
| Реляционные данные | PostgreSQL | Пользователи, заказы, товары |
| Кэш | Redis | Сессии, горячие данные |
| Поиск | Elasticsearch | Поиск товаров, логи |
| Временные ряды | InfluxDB/TimescaleDB | Мониторинг, метрики |
| Документные данные | MongoDB | Логи, конфигурация |

**Проектирование слоя доступа к данным**:

```
data-layer/
├── primary-db/               # основная база данных
│   ├── master/               # база для записи
│   └── slaves/               # базы для чтения
├── cache-layer/              # слой кэширования
│   ├── redis-cluster/
│   └── local-cache/
├── search-engine/            # поисковый движок
│   └── elasticsearch/
└── message-queue/            # очередь сообщений
    ├── kafka/
    └── rabbitmq/
```

---

## 5. Справочник стандартов архитектуры открытых проектов

### 5.1 Экосистема Node.js

**Официальная структура проекта Express.js**:
```
express-project/
├── bin/                      # скрипты запуска
├── public/                   # статические ресурсы
├── routes/                   # маршруты
├── views/                    # представления
├── app.js                    # конфигурация приложения
└── package.json
```

**Официальные рекомендации NestJS**:
```
nest-project/
├── src/
│   ├── modules/              # функциональные модули
│   ├── common/               # общие модули
│   ├── config/
│   └── main.ts
├── test/
└── nest-cli.json
```

### 5.2 Экосистема Python

**Официальная структура проекта Django**:
```
django-project/
├── project_name/             # конфигурация проекта
├── apps/                     # каталог приложений
├── templates/
├── static/
├── media/
└── manage.py
```

**Структура проекта FastAPI**:
```
fastapi-project/
├── app/
│   ├── api/
│   │   ├── deps.py           # зависимости
│   │   └── v1/
│   │       └── endpoints/
│   ├── core/                 # основная конфигурация
│   ├── db/                   # база данных
│   ├── models/               # модели
│   ├── schemas/              # модели Pydantic
│   └── main.py
├── tests/
└── alembic/                  # миграции
```

### 5.3 Экосистема Go

**Стандартная структура проекта**:
```
go-project/
├── cmd/                      # точка входа приложения
│   └── app/
│       └── main.go
├── internal/                 # приватный код
├── pkg/                      # публичные библиотеки
├── api/                      # определение API
├── web/                      # статические ресурсы
├── configs/                  # конфигурация
├── scripts/                  # скрипты
└── go.mod
```

**Справка**:
- [golang-standards/project-layout](https://github.com/golang-standards/project-layout)

### 5.4 Экосистема Java

**Официальная структура Spring Boot**:
```
spring-boot-project/
├── src/main/java/com/example/
│   ├── controller/
│   ├── service/
│   ├── repository/
│   ├── entity/
│   ├── dto/
│   ├── config/
│   └── Application.java
├── src/main/resources/
│   ├── static/
│   ├── templates/
│   └── application.yml
└── src/test/
```

**Руководство по разработке на Java от Alibaba**:
- Чёткое деление на слои: controller/service/manager/dao
- Доменная модель: различение DO/DTO/BO/VO
- Структура пакетов: деление по функциональным модулям

---

## 6. Дорожная карта эволюции архитектуры

### 6.1 Пример эволюции

```
Этап 1: монолитное приложение (начальный уровень)
    ↓ рост числа пользователей, расширение команды
Этап 2: слоистая архитектура (продвинутый уровень)
    ↓ сложный бизнес, работа нескольких команд
Этап 3: модульность/микросервисы (корпоративный уровень)
    ↓ требования высокой нагрузки и доступности
Этап 4: облачно-нативная архитектура (уровень платформы)
```

### 6.2 Когда обновлять архитектуру?

| Сигнал | Текущий уровень | Рекомендуемое обновление |
|------|----------|----------|
| Файлов кода > 50 | Начальный | Продвинутый |
| Время сборки > 5 минут | Продвинутый | Модульность |
| Команда > 10 человек | Продвинутый | Микросервисы |
| DAU > 100 тысяч | Продвинутый | Корпоративный |
| Многоязычный технологический стек | Монолит | Микросервисы |

---

## 7. Заключение

::: tip 💡 Ключевая идея
**Архитектура служит бизнесу, а не наоборот.**

**Выбор по количеству пользователей**:
- **< 1k**: простой скрипт, быстрый выход в продакшен
- **1k-100k**: слоистая архитектура, стандарты кода
- **> 100k**: микросервисы, проектирование высокой доступности

**Выбор по языку**:
- **Node.js**: используйте асинхронные особенности, подходит для I/O-интенсивных задач
- **Python**: быстрая разработка, подходит для обработки данных и AI
- **Go**: высокая производительность, подходит для облачно-нативных решений и микросервисов
- **Java**: корпоративный уровень, подходит для крупных сложных систем

**Общие принципы**:
1. **Постепенная эволюция**: начинайте с простого, развивайтесь вместе с бизнесом
2. **Соглашения важнее конфигурации**: единые стандарты, снижение затрат на коммуникацию
3. **Автоматизированное тестирование**: гарантия безопасности рефакторинга
4. **Документация прежде всего**: архитектурные решения нужно фиксировать

**Конечная цель**: чтобы код, как фабричный цех, эффективно работал независимо от масштаба.
:::

---

## Справочные ресурсы

### Открытые проекты
- [nestjs/nest](https://github.com/nestjs/nest) - корпоративный фреймворк Node.js
- [django/django](https://github.com/django/django) - веб-фреймворк Python
- [gin-gonic/gin](https://github.com/gin-gonic/gin) - веб-фреймворк Go
- [spring-projects/spring-boot](https://github.com/spring-projects/spring-boot) - фреймворк Java

### Руководства по архитектуре
- [goldbergyoni/nodebestpractices](https://github.com/goldbergyoni/nodebestpractices) - лучшие практики Node.js
- [golang-standards/project-layout](https://github.com/golang-standards/project-layout) - структура проекта Go
- [cookiecutter/cookiecutter-django](https://github.com/cookiecutter/cookiecutter-django) - шаблон проекта Django
- [ali-baba/spring-cloud-alibaba](https://github.com/alibaba/spring-cloud-alibaba) - микросервисы Alibaba

### Книги
- «Clean Architecture» - Robert C. Martin
- «Building Microservices» - Sam Newman
- «Designing Data-Intensive Applications» - Martin Kleppmann

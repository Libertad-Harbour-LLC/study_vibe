# Как создать промышленное десктопное приложение на Qt: система HMI для мониторинга насоса

# Глава 1. Что такое промышленный HMI и разработка на Qt

В этом руководстве мы пройдём полный замкнутый цикл: создадим с нуля систему HMI (Human-Machine Interface, человеко-машинный интерфейс) промышленного уровня для мониторинга насоса на Qt. Она сможет читать данные датчиков в реальном времени, рисовать графики тренда давления, запускать автоматические аварийные сигналы при превышении порога и записывать журналы неисправностей. Весь процесс использует бесплатное симуляционное ПО на ПК вместо реального промышленного оборудования.

Для этого руководства вам как минимум потребуется:

- Компьютер (Windows или Mac, Windows рекомендуется для лучшей совместимости с промышленным ПО)
- Среда разработки Qt 6.5 (Qt Creator + модули Qt Serial Bus + Qt Charts)
- Симуляционное ПО Modbus Slave (бесплатная загрузка, работает как «виртуальный насос»)
- Ваш AI-ассистент для написания кода (Cursor / Trae / Claude Code)

> **Ноль оборудования, ноль затрат**: используйте бесплатное симуляционное ПО для ПК (Modbus Slave) в качестве нижнего уровня устройства; покупать оборудование не нужно. Используйте официальные модули Qt `QModbusTcpClient` + Qt Charts напрямую, ручной разбор протокола не требуется. После запуска вы увидите тренды давления в реальном времени, всплывающие аварийные сигналы при превышении порога и журналы неисправностей, что соответствует реальному заводскому рабочему процессу.

## 1.1 Что такое верхний и нижний уровень управления?

В промышленной автоматизации есть две концепции, которые вы обязательно должны понимать: **верхний уровень управления** (upper computer) и **нижний уровень управления** (lower computer).

**Нижний уровень управления**: «руки и ноги» на месте

Нижний уровень управления — это контроллер, который напрямую взаимодействует с физическими устройствами. На заводах это обычно **PLC (Programmable Logic Controller, программируемый логический контроллер)** или **датчик**, отвечающий за:

* чтение полевых данных (температура, давление, расход, уровень жидкости и т. д.)
* управление действиями устройства (запуск насоса, закрытие клапана, регулировка скорости и т. д.)
* автоматическое выполнение предопределённой логики (например, остановить насос при превышении давлением порога)

Вы можете думать о нижнем уровне управления как о «рабочем» в заводском цехе. Ему не нужно сложное мышление, но он должен надёжно выполнять задачи.

**Верхний уровень управления**: «глаза и мозг» в диспетчерской

Верхний уровень управления — это программное обеспечение для мониторинга, работающее на ПК или промышленном компьютере, то есть **HMI (Human-Machine Interface)**, который мы сегодня создадим. Он отвечает за:

* отображение полевых данных в реальном времени (числа, графики, анимации)
* запись исторических данных и журналов аварийных сигналов
* обеспечение удалённого управления для операторов
* предоставление анализа данных и отчётов

Вы можете думать о верхнем уровне управления как о «центре мониторинга» завода. Операторы могут понимать состояние предприятия с экрана.

**Как они взаимодействуют?**

Верхний и нижний уровни управления обмениваются данными через **промышленные коммуникационные протоколы**. Самый распространённый из них — **Modbus**, «ветеран»-протокол, появившийся в 1979 году. Он всё ещё широко используется, потому что прост, надёжен и поддерживается практически всеми промышленными устройствами.

```text
Control room                           Factory site
┌──────────┐    Modbus protocol    ┌──────────┐
│ Upper    │ ◄──────────────────►  │ Lower    │
│ computer │   "Tell me pressure"  │ computer │
│ (Qt HMI) │   "Pressure is 1.20MPa"│ (PLC/Sensor)
│ Display  │                       │ Read data│
│ Log data │                       │ Control  │
│ Alarms   │                       │ Protect  │
└──────────┘                       └──────────┘
```

<!-- ![placeholder: Diagram of upper vs lower computer relationship: PC screen (upper computer) on the left, PLC and pump (lower computer) on the right, connected via Modbus](../../../../ru-ru/stage-3/cross-platform/qt-industrial-hmi/images/image1.png) -->

## 1.2 Что такое протокол Modbus?

Modbus — это «общий язык» промышленной связи. Он определяет, как верхний и нижний уровни управления «разговаривают».

**Всего две основные концепции:**

* **Регистр (Register)**: «ячейки» данных в нижнем уровне управления. У каждой есть адрес (`0`, `1`, `2`, ...), хранящий число. Например, адрес `0` хранит давление, а адрес `1` хранит температуру.
* **Операции чтения/записи**: верхний уровень управления может читать регистры (получать данные) или записывать в регистры (отправлять команды управления).

**Два распространённых варианта Modbus:**

| Вариант | Транспорт | Типичный сценарий |
|------|---------|---------|
| Modbus RTU | Последовательный порт (RS-485/RS-232) | Малое расстояние, прямое подключение устройства |
| Modbus TCP | Ethernet (TCP/IP) | Большое расстояние, сетевая связь |

В этом руководстве используется **Modbus TCP**. Поскольку он сетевой, приложение верхнего уровня и симулятор нижнего уровня могут работать на одной машине без физической проводки.

## 1.3 Почему выбираем Qt?

Qt — один из лучших фреймворков для промышленного ПО. Многие интерфейсы мониторинга на заводах, в больницах и транспортных системах созданы на Qt. Причины просты:

| Преимущество | Пояснение |
|------|------|
| Кроссплатформенность | Одна кодовая база компилируется под Windows, Linux и встраиваемые устройства |
| Встроенная поддержка промышленных протоколов | Qt Serial Bus поддерживает Modbus нативно, сторонняя библиотека не требуется |
| Мощные графики | Qt Charts предоставляет профессиональные графики в реальном времени |
| Высокая производительность | Основа на C++ подходит для обновления данных в реальном времени |
| Зрелость и стабильность | 30-летняя история, проверена в промышленной сфере |

## 1.4 Что мы создаём?

Мы создадим **систему HMI для мониторинга насоса**, имитирующую мониторинг давления насоса на реальном заводе:

| Функция | Описание |
|------|------|
| Чтение данных в реальном времени | Чтение давления с нижнего уровня управления каждую секунду |
| График тренда давления | Линейный график давления за последние 60 секунд |
| Аварийный сигнал при превышении порога | Всплывающее предупреждение и красный UI при превышении давлением порога |
| Журнал неисправностей | Запись всех аварийных событий в базу данных для исторических запросов |
| Ручное управление | Запуск/остановка насоса в один клик (запись в регистр нижнего уровня управления) |

<!-- ![placeholder: Pump monitoring HMI preview showing real-time pressure number, trend chart, alarm indicator, start/stop button, and log list](../../../../ru-ru/stage-3/cross-platform/qt-industrial-hmi/images/image2.png) -->

## 1.5 План руководства

Мы пройдём весь путь в следующие шаги:

1. **Подготовка окружения и симулированного нижнего уровня управления** (2 минуты): установить Qt 6.5 и симулятор Modbus Slave
2. **Создание проекта Qt и подключение Modbus** (3 минуты): установить связь между приложением верхнего уровня и симулятором
3. **Реализация чтения и отображения в реальном времени** (3 минуты): таймерное чтение давления и обновления UI
4. **Рисование графика тренда давления в реальном времени** (3 минуты): динамический линейный график с Qt Charts
5. **Реализация аварийных сигналов и журналов неисправностей** (3 минуты): аварийный сигнал при превышении порога + логирование в SQLite
6. **Упаковка и развёртывание** (опционально): упаковать приложение в самостоятельный исполняемый файл

# Глава 2. Подготовка окружения и симулированного нижнего уровня управления (2 минуты)

## 2.1 Установка Qt 6.5

Qt предоставляет бесплатную версию с открытым исходным кодом, которой достаточно для этого руководства.

1. Посетите [официальный сайт Qt](https://www.qt.io/download-qt-installer) и скачайте Qt Online Installer
2. Запустите установщик, войдите или зарегистрируйте аккаунт Qt (бесплатно)
3. В выборе компонентов отметьте:
   - **Qt 6.5.x** (или новее)
   - **Qt Serial Bus** в разделе **Additional Libraries** (поддержка Modbus)
   - **Qt Charts** в разделе **Additional Libraries** (отрисовка графиков)
   - **Qt Creator** (IDE, обычно выбран по умолчанию)
4. Нажмите установить и подождите

> **Совет**: если Qt уже установлен, но не хватает Serial Bus или Charts, перезапустите Qt Maintenance Tool и добавьте компоненты.

<!-- ![placeholder: Qt installer component selection screenshot highlighting Qt Serial Bus and Qt Charts](../../../../ru-ru/stage-3/cross-platform/qt-industrial-hmi/images/image3.png) -->

## 2.2 Установка Modbus Slave: ваш «виртуальный насос»

Modbus Slave — это бесплатный симулятор подчинённого устройства Modbus. Он может имитировать промышленное устройство (PLC/датчик) на вашем компьютере, чтобы приложению верхнего уровня было с чем взаимодействовать.

1. Посетите [modbustools.com](https://www.modbustools.com/modbus_slave.html) и скачайте Modbus Slave
2. Установите и откройте его
3. Настройте подключение:
   - Меню **Connection -> Connect**
   - Выберите **Modbus TCP/IP**
   - IP-адрес: `127.0.0.1` (localhost)
   - Порт: `502` (стандартный порт Modbus TCP)
   - Нажмите **OK** для прослушивания

4. Задайте симулированные данные:
   - Вы увидите таблицу регистров, каждая строка — это адрес регистра (`0`, `1`, `2`, ...)
   - Дважды щёлкните значение по адресу **0**, измените на **120** (означает давление 1,20 МПа, делится на 100 в приложении)
   - Дважды щёлкните значение по адресу **1**, измените на **350** (означает температуру 35,0 °C)
   - Дважды щёлкните значение по адресу **2**, измените на **1** (состояние насоса: `1=работает`, `0=остановлен`)

Теперь Modbus Slave — ваш «виртуальный насос 24/7». Держите окно открытым; он будет непрерывно отвечать на запросы чтения/записи.

<!-- ![placeholder: Modbus Slave screenshot showing TCP config and simulated register values](../../../../ru-ru/stage-3/cross-platform/qt-industrial-hmi/images/image4.png) -->

> **Совет по динамической симуляции**: Modbus Slave поддерживает автоинкремент/случайные изменения. Щёлкните правой кнопкой по значению регистра и выберите «Auto increment» или «Random», чтобы имитировать реалистичные колебания датчиков.

# Глава 3. Создание проекта Qt и подключение Modbus (3 минуты)

## 3.1 Создание нового проекта Qt

Откройте Qt Creator и создайте новый проект:

1. Нажмите **File -> New Project**
2. Выберите **Application (Qt) -> Qt Widgets Application**
3. Имя проекта: **PumpHMI**
4. Выберите установленный комплект Qt 6.5
5. Завершите создание

Откройте `PumpHMI.pro` (или `CMakeLists.txt`, если используете CMake) и добавьте ключевые модули:

```pro
QT += core gui widgets serialbus charts sql
```

| Модуль | Назначение |
|------|------|
| `serialbus` | Предоставляет `QModbusTcpClient` для связи по Modbus TCP |
| `charts` | Предоставляет `QChart`, `QLineSeries` для графика тренда в реальном времени |
| `sql` | Предоставляет `QSqlDatabase` для журналов неисправностей SQLite |

Если используете CMake, эквивалентная конфигурация:

```cmake
find_package(Qt6 REQUIRED COMPONENTS Widgets SerialBus Charts Sql)
target_link_libraries(PumpHMI PRIVATE
    Qt6::Widgets Qt6::SerialBus Qt6::Charts Qt6::Sql)
```

## 3.2 Объявление основных членов

Попросите AI сгенерировать заголовочный файл:

```text
Please help me write mainwindow.h with core members for pump monitoring HMI:
1. QModbusTcpClient for Modbus TCP communication
2. QTimer for timed data reading
3. QChart + QLineSeries for real-time trend chart
4. QSqlDatabase for fault log storage
5. UI elements: pressure label, status indicator, start/stop button, log table
```

Основной заголовок:

```cpp
// mainwindow.h
#ifndef MAINWINDOW_H
#define MAINWINDOW_H

#include <QMainWindow>
#include <QModbusTcpClient>
#include <QModbusDataUnit>
#include <QTimer>
#include <QtCharts>
#include <QSqlDatabase>
#include <QLabel>
#include <QPushButton>
#include <QTableWidget>

class MainWindow : public QMainWindow {
    Q_OBJECT

public:
    explicit MainWindow(QWidget *parent = nullptr);
    ~MainWindow();

private slots:
    void connectModbus();        // connect lower computer
    void readPressure();         // timed pressure read
    void onReadReady();          // read callback
    void triggerAlarm(float v);  // trigger alarm
    void togglePump();           // start/stop pump

private:
    // Modbus communication
    QModbusTcpClient *m_modbusClient = nullptr;
    QTimer *m_pollTimer = nullptr;

    // Real-time chart
    QChart *m_chart = nullptr;
    QLineSeries *m_series = nullptr;
    QDateTimeAxis *m_axisX = nullptr;
    QValueAxis *m_axisY = nullptr;

    // Database
    QSqlDatabase m_db;

    // UI elements
    QLabel *m_pressureLabel = nullptr;    // pressure display
    QLabel *m_statusLight = nullptr;      // status indicator
    QPushButton *m_pumpButton = nullptr;  // start/stop button
    QTableWidget *m_logTable = nullptr;   // log table

    // Alarm threshold
    float m_alarmThreshold = 1.50f;  // alarm above 1.50 MPa
    bool m_pumpRunning = false;

    void setupUI();
    void setupDatabase();
    void logAlarm(float pressure, const QString &message);
};

#endif // MAINWINDOW_H
```

<!-- ![placeholder: Screenshot of mainwindow.h in Qt Creator](../../../../ru-ru/stage-3/cross-platform/qt-industrial-hmi/images/image5.png) -->

## 3.3 Установка соединения Modbus TCP

Реализуйте логику подключения в `mainwindow.cpp`:

```cpp
// mainwindow.cpp - connection section
void MainWindow::connectModbus()
{
    m_modbusClient = new QModbusTcpClient(this);

    // Connect to Modbus Slave simulator
    m_modbusClient->setConnectionParameter(
        QModbusDevice::NetworkPortParameter, 502);
    m_modbusClient->setConnectionParameter(
        QModbusDevice::NetworkAddressParameter, "127.0.0.1");
    m_modbusClient->setTimeout(1000);       // 1s timeout
    m_modbusClient->setNumberOfRetries(3);  // retry 3 times

    if (!m_modbusClient->connectDevice()) {
        statusBar()->showMessage("Failed to connect lower computer!", 3000);
        return;
    }

    statusBar()->showMessage("Connected to lower computer (127.0.0.1:502)", 3000);

    // Start timer, read once per second
    m_pollTimer = new QTimer(this);
    connect(m_pollTimer, &QTimer::timeout, this, &MainWindow::readPressure);
    m_pollTimer->start(1000);  // 1000ms = 1s
}
```

**Пояснения к коду:**

| Код | Значение |
|------|------|
| `QModbusTcpClient` | Встроенный в Qt клиент Modbus TCP, связывается с нижним уровнем управления |
| `NetworkPortParameter, 502` | Подключение к порту `502` (как в конфигурации Modbus Slave) |
| `NetworkAddressParameter, "127.0.0.1"` | Подключение к localhost (симулятор работает локально) |
| `m_pollTimer->start(1000)` | Вызов `readPressure()` каждую секунду |

## 3.4 Чтение данных давления

```cpp
// mainwindow.cpp - reading section
void MainWindow::readPressure()
{
    if (!m_modbusClient || m_modbusClient->state() != QModbusDevice::ConnectedState)
        return;

    // Build read request: start at address 0, read 3 holding registers
    QModbusDataUnit readUnit(
        QModbusDataUnit::HoldingRegisters,  // register type
        0,                                   // start address
        3                                    // quantity
    );

    // Send async read request
    if (auto *reply = m_modbusClient->sendReadRequest(readUnit, 1)) {
        if (!reply->isFinished()) {
            connect(reply, &QModbusReply::finished,
                    this, &MainWindow::onReadReady);
        } else {
            delete reply;  // broadcast request, delete directly
        }
    }
}

void MainWindow::onReadReady()
{
    auto *reply = qobject_cast<QModbusReply *>(sender());
    if (!reply) return;

    if (reply->error() == QModbusDevice::NoError) {
        const QModbusDataUnit unit = reply->result();

        // Parse values (divide register value for real units)
        float pressure = unit.value(0) / 100.0f;   // addr 0: pressure (MPa)
        float temperature = unit.value(1) / 10.0f;  // addr 1: temperature (°C)
        int pumpStatus = unit.value(2);              // addr 2: pump state

        // Update UI
        m_pressureLabel->setText(
            QString("%1 MPa").arg(pressure, 0, 'f', 2));

        // Check alarm
        if (pressure > m_alarmThreshold) {
            triggerAlarm(pressure);
        }

        // Update trend chart (implemented next chapter)
        // updateChart(pressure);

    } else {
        statusBar()->showMessage(
            QString("Read failed: %1").arg(reply->errorString()), 2000);
    }

    reply->deleteLater();
}
```

**Поток чтения Modbus:**

```text
readPressure() triggered by timer
    -> Build QModbusDataUnit ("read addresses 0-2")
    -> sendReadRequest() async send (UI not blocked)
    -> lower computer returns data
    -> onReadReady() triggered
    -> parse register values and update UI
```

<!-- ![placeholder: Running app screenshot showing real-time pressure updates and status bar "connected to lower computer"](../../../../ru-ru/stage-3/cross-platform/qt-industrial-hmi/images/image6.png) -->

# Глава 4. Рисование тренда давления в реальном времени (3 минуты)

## 4.1 Инициализация графика

Qt Charts предоставляет профессиональные компоненты графиков. Попросите AI инициализировать в конструкторе:

```text
Please help me initialize Qt Charts real-time line chart in MainWindow constructor:
1. Create QChart and QLineSeries
2. X axis uses QDateTimeAxis, showing latest 60 seconds
3. Y axis uses QValueAxis, range 0-3.0 MPa
4. Line color blue, width 2px
5. Place chart into QChartView and add to layout
```

Основной код:

```cpp
// mainwindow.cpp - chart initialization
void MainWindow::setupChart()
{
    m_series = new QLineSeries();
    m_series->setName("Pressure (MPa)");
    m_series->setPen(QPen(QColor("#2196F3"), 2));

    m_chart = new QChart();
    m_chart->addSeries(m_series);
    m_chart->setTitle("Real-time Pressure Trend");
    m_chart->setAnimationOptions(QChart::NoAnimation); // no animation for real-time data

    // X axis: time
    m_axisX = new QDateTimeAxis();
    m_axisX->setFormat("HH:mm:ss");
    m_axisX->setTitleText("Time");
    m_chart->addAxis(m_axisX, Qt::AlignBottom);
    m_series->attachAxis(m_axisX);

    // Y axis: pressure
    m_axisY = new QValueAxis();
    m_axisY->setRange(0, 3.0);
    m_axisY->setTitleText("Pressure (MPa)");
    m_axisY->setLabelFormat("%.1f");
    m_chart->addAxis(m_axisY, Qt::AlignLeft);
    m_series->attachAxis(m_axisY);

    // Create chart view
    QChartView *chartView = new QChartView(m_chart);
    chartView->setRenderHint(QPainter::Antialiasing);

    // Add to layout (assuming existing centralLayout)
    centralLayout->addWidget(chartView);
}
```

## 4.2 Обновление графика в реальном времени

Каждый раз, когда считывается новое значение давления, добавляйте одну точку и оставляйте только последние 60 секунд:

```cpp
// mainwindow.cpp - chart updates
void MainWindow::updateChart(float pressure)
{
    QDateTime now = QDateTime::currentDateTime();

    // Append new point
    m_series->append(now.toMSecsSinceEpoch(), pressure);

    // Keep only latest 60s data
    QDateTime cutoff = now.addSecs(-60);
    while (m_series->count() > 0 &&
           m_series->at(0).x() < cutoff.toMSecsSinceEpoch()) {
        m_series->remove(0);
    }

    // Update X axis range: always show latest 60s
    m_axisX->setRange(cutoff, now);
}
```

Затем вызовите его в `onReadReady()`:

```cpp
// Add after pressure parsing in onReadReady():
updateChart(pressure);
```

Теперь запустите программу. Вы увидите синюю линию, обновляющуюся в реальном времени, одна точка в секунду, всегда показывающую последние 60 секунд. Если вы вручную измените значения регистров в Modbus Slave, линия сразу отразит изменения.

<!-- ![placeholder: Real-time pressure trend screenshot showing scrolling blue line, time X-axis, pressure Y-axis](../../../../ru-ru/stage-3/cross-platform/qt-industrial-hmi/images/image7.png) -->

> **Совет по производительности**: `QChart::NoAnimation` важен. Данные в реальном времени обновляются каждую секунду; анимации могут вызывать подтормаживание UI. Это распространённая практика для промышленных HMI.

# Глава 5. Система аварийных сигналов и журналы неисправностей (3 минуты)

## 5.1 Аварийный сигнал при превышении порога

Когда давление превышает порог, нам нужно: красное предупреждение в UI + всплывающее оповещение + запись в журнал.

```cpp
// mainwindow.cpp - alarm logic
void MainWindow::triggerAlarm(float pressure)
{
    // Turn UI red
    m_pressureLabel->setStyleSheet(
        "color: white; background-color: #F44336;"
        "font-size: 32px; padding: 10px; border-radius: 8px;");

    // Status indicator red
    m_statusLight->setStyleSheet(
        "background-color: #F44336; border-radius: 12px;"
        "min-width: 24px; min-height: 24px;");

    // Popup alarm (only first time crossing threshold to avoid repeated popups)
    static bool alarmActive = false;
    if (!alarmActive) {
        alarmActive = true;
        QMessageBox::warning(this, "Pressure Alarm",
            QString("Current pressure %1 MPa exceeds threshold %2 MPa!\nPlease check pump status immediately.")
                .arg(pressure, 0, 'f', 2)
                .arg(m_alarmThreshold, 0, 'f', 2));
    }

    // Record to DB
    logAlarm(pressure,
        QString("Pressure over threshold: %1 MPa > %2 MPa")
            .arg(pressure, 0, 'f', 2)
            .arg(m_alarmThreshold, 0, 'f', 2));

    // Reset when pressure returns to normal
    if (pressure <= m_alarmThreshold) {
        alarmActive = false;
        m_pressureLabel->setStyleSheet(
            "color: #2196F3; font-size: 32px; padding: 10px;");
        m_statusLight->setStyleSheet(
            "background-color: #4CAF50; border-radius: 12px;"
            "min-width: 24px; min-height: 24px;");
    }
}
```

<!-- ![placeholder: Over-threshold alarm screenshot showing red pressure background, red indicator, and alarm popup](../../../../ru-ru/stage-3/cross-platform/qt-industrial-hmi/images/image8.png) -->

## 5.2 Журналы неисправностей SQLite

Промышленные системы обязаны логировать все аварийные события для прослеживаемости. Мы используем SQLite:

```cpp
// mainwindow.cpp - database initialization
void MainWindow::setupDatabase()
{
    m_db = QSqlDatabase::addDatabase("QSQLITE");
    m_db.setDatabaseName("pump_alarm_log.db");

    if (!m_db.open()) {
        qWarning() << "Cannot open database:" << m_db.lastError().text();
        return;
    }

    // Create alarm table
    QSqlQuery query;
    query.exec(
        "CREATE TABLE IF NOT EXISTS alarm_log ("
        "  id INTEGER PRIMARY KEY AUTOINCREMENT,"
        "  timestamp DATETIME DEFAULT CURRENT_TIMESTAMP,"
        "  pressure REAL,"
        "  message TEXT"
        ")"
    );
}
```

## 5.3 Логирование и отображение записей

```cpp
// mainwindow.cpp - write logs
void MainWindow::logAlarm(float pressure, const QString &message)
{
    // Write to DB
    QSqlQuery query;
    query.prepare(
        "INSERT INTO alarm_log (pressure, message) VALUES (?, ?)");
    query.addBindValue(pressure);
    query.addBindValue(message);
    query.exec();

    // Update on-screen table
    int row = m_logTable->rowCount();
    m_logTable->insertRow(row);
    m_logTable->setItem(row, 0,
        new QTableWidgetItem(
            QDateTime::currentDateTime().toString("yyyy-MM-dd HH:mm:ss")));
    m_logTable->setItem(row, 1,
        new QTableWidgetItem(QString::number(pressure, 'f', 2)));
    m_logTable->setItem(row, 2,
        new QTableWidgetItem(message));

    // Auto-scroll to latest row
    m_logTable->scrollToBottom();
}
```

Таблица журнала имеет три столбца: время, значение давления и сообщение об аварии. Каждый аварийный сигнал добавляет одну строку и сохраняется в SQLite.

<!-- ![placeholder: Fault log table screenshot with multiple records including timestamp, pressure, and alarm message](../../../../ru-ru/stage-3/cross-platform/qt-industrial-hmi/images/image9.png) -->

## 5.4 Ручной запуск/остановка насоса

Помимо чтения данных, верхний уровень управления должен также управлять нижним уровнем. Мы делаем это путём записи значений в регистры:

```cpp
// mainwindow.cpp - pump control
void MainWindow::togglePump()
{
    if (!m_modbusClient || m_modbusClient->state() != QModbusDevice::ConnectedState)
        return;

    m_pumpRunning = !m_pumpRunning;

    // Build write request: write 1 (start) or 0 (stop) to address 2
    QModbusDataUnit writeUnit(
        QModbusDataUnit::HoldingRegisters, 2, 1);
    writeUnit.setValue(0, m_pumpRunning ? 1 : 0);

    if (auto *reply = m_modbusClient->sendWriteRequest(writeUnit, 1)) {
        connect(reply, &QModbusReply::finished, this, [this, reply]() {
            if (reply->error() == QModbusDevice::NoError) {
                m_pumpButton->setText(m_pumpRunning ? "Stop Pump" : "Start Pump");
                m_pumpButton->setStyleSheet(m_pumpRunning
                    ? "background-color: #F44336; color: white; padding: 12px;"
                    : "background-color: #4CAF50; color: white; padding: 12px;");
                statusBar()->showMessage(
                    m_pumpRunning ? "Pump started" : "Pump stopped", 2000);
            }
            reply->deleteLater();
        });
    }
}
```

В Modbus Slave вы увидите, как адрес `2` переключается между `0` и `1` при нажатии кнопки. Это процесс «управления» со стороны верхнего уровня.

<!-- ![placeholder: Pump start/stop button screenshot showing green "Start Pump" and red "Stop Pump" states](../../../../ru-ru/stage-3/cross-platform/qt-industrial-hmi/images/image10.png) -->

# Глава 6. Упаковка и развёртывание (опционально)

## 6.1 Упаковка с помощью windeployqt / macdeployqt

Qt предоставляет официальные инструменты развёртывания для автоматического сбора необходимых динамических библиотек.

**Windows:**

```bash
# Build Release first, then run in build directory:
windeployqt PumpHMI.exe
```

`windeployqt` копирует DLL Qt, плагины, файлы переводов и т. д. рядом с исполняемым файлом. Эту упакованную папку можно отправить напрямую.

**macOS:**

```bash
macdeployqt PumpHMI.app -dmg
```

Это генерирует образ установщика `.dmg`.

## 6.2 Создание установщика с помощью Qt Installer Framework

Если вы хотите профессиональный мастер установки («Далее -> Далее -> Готово»), используйте Qt Installer Framework:

```text
Please help me create an installer for PumpHMI with Qt Installer Framework:
1. Create installer directory structure (config, packages)
2. Configure config.xml (installer name, version, target directory)
3. Put windeployqt output files into packages/com.example.pumphmi/data/
4. Run binarycreator to generate installer
```

<!-- ![placeholder: PumpHMI setup wizard screenshot showing install path and progress](../../../../ru-ru/stage-3/cross-platform/qt-industrial-hmi/images/image11.png) -->

# Глава 7. Заключение

Поздравляем! Вы создали с нуля систему HMI для мониторинга насоса промышленного уровня. Вспомним:

1. Разобрались в основных концепциях верхнего уровня управления, нижнего уровня управления и протокола Modbus
2. Имитировали «виртуальный насос» с помощью Modbus Slave, без реального оборудования
3. Построили связь между верхним и нижним уровнями с помощью Qt `QModbusTcpClient`
4. Нарисовали прокручивающийся график тренда давления в реальном времени с помощью Qt Charts
5. Реализовали всплывающие аварийные сигналы при превышении порога и журналы неисправностей SQLite
6. Реализовали удалённое управление запуском/остановкой насоса

Весь процесс не использовал реального промышленного оборудования, но архитектура и функции соответствуют реальным заводским системам HMI. Если вы замените Modbus Slave реальным PLC, это приложение можно будет использовать в продакшен-сценариях напрямую.

**Продвинутые направления:**

* **Мониторинг нескольких устройств**: подключите несколько нижних уровней управления и используйте вкладки/разделённые представления для данных разных устройств
* **Воспроизведение истории**: читайте исторические данные из SQLite и воспроизводите графики тренда с элементами управления временной шкалой
* **Протокол OPC UA**: Modbus подходит для более простых сценариев; сложные промышленные системы часто используют OPC UA, который также поддерживается Qt (модуль Qt OPC UA)
* **Веб-удалённый мониторинг**: используйте Qt WebSocket для отправки данных в реальном времени в браузер для просмотра с мобильных устройств
* **AI-прогнозное обслуживание**: подавайте исторические данные давления в ML-модели для заблаговременного прогнозирования отказов

***Используйте код для защиты каждого устройства в промышленной эксплуатации.***

# Источники

* [Документация Qt Serial Bus](https://doc.qt.io/qt-6/qtserialbus-index.html)
* [Пример клиента Qt Modbus TCP](https://doc.qt.io/qt-6/qtserialbus-modbus-client-example.html)
* [Документация Qt Charts](https://doc.qt.io/qt-6/qtcharts-index.html)
* [Спецификации протокола Modbus](https://modbus.org/specs.php)
* [Симулятор Modbus Slave](https://www.modbustools.com/modbus_slave.html)
* [Документация Qt Installer Framework](https://doc.qt.io/qtinstallerframework/)

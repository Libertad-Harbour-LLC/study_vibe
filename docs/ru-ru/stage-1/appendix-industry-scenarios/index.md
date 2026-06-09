---
title: 'Справочник прикладных сценариев для отраслей B2B'
description: 'Этот документ обобщает практические применения LLM в корпоративных сценариях B2B, включая конкретные направления в таких отраслях, как производство, интеллектуальное обслуживание клиентов, образование, интеллектуальное программирование, здравоохранение, кибербезопасность, финансовые услуги и корпоративные операции. Он предоставляет практические рекомендации для разработчиков, создающих AI-приложения для корпоративных клиентов.'
---

<script setup>
import { computed, ref } from 'vue'

const duration = 'Примерно <strong>6 часов</strong>'

const interestPoint = ref('')
const purpose = ref('')

const topicPool = {
  'manufacturing': [
    { title: 'Платформа AI-проектирования экстерьера электробусов', desc: 'Концептуальный дизайн экстерьера на основе моделей генерации изображений' },
    { title: 'Интеллектуальный помощник для проектирования и проверки чертежей', desc: 'Создание базы знаний корпоративных стандартов проектирования с помощью RAG' },
    { title: 'Автоматическая генерация и управление технической документацией', desc: 'Автогенерация спецификаций продуктов и руководств по эксплуатации на основе LLM' },
    { title: 'Помощник автогенерации отчётов об осмотре производственного оборудования', desc: 'Голосовое описание состояния оборудования, генерация структурированного отчёта об осмотре' },
    { title: 'Помощник по диагностике неисправностей промышленного оборудования', desc: 'Создание векторной базы знаний из исторических случаев неисправностей' }
  ],
  'customer-service': [
    { title: 'Многоканальная система автоответов и генерации тикетов', desc: 'Подключение сообщений из разных каналов, LLM понимает намерения и генерирует ответы' },
    { title: 'Помощник по выявлению потенциальных клиентов и рекомендациям по работе с ними', desc: 'Анализ истории диалогов, выявление клиентов с высоким намерением' },
    { title: 'Интеллектуальный поиск и Q&A-дворецкий по внутренним знаниям компании', desc: 'Создание векторной базы знаний из внутренних документов' },
    { title: 'Инструмент умного резюмирования диалогов и генерации тикетов', desc: 'Автогенерация резюме диалогов и извлечение ключевой информации' },
    { title: 'Система базы знаний с рекомендациями золотых скриптов для клиентского сервиса', desc: 'Анализ лучших кейсов, извлечение шаблонов золотых скриптов' }
  ],
  'education': [
    { title: 'Персонализированное планирование пути изучения языка и интеллектуальная система обучения', desc: 'Оценка уровня учащегося, планирование ежедневных учебных задач' },
    { title: 'Платформа автонаписания учебных планов и доставки образовательных ресурсов', desc: 'Генерация структуры учебного плана на основе программы курса' },
    { title: 'Система автопроверки домашних заданий и диагностического анализа обучения', desc: 'Автопроверка субъективных заданий и генерация рекомендаций по оценке' },
    { title: 'Построение модели компетенций должности и карта обучения', desc: 'Анализ описания должности (JD) для извлечения требований к компетенциям' },
    { title: 'Индивидуальная сценарная практика иностранного языка', desc: 'LLM играет разные роли для практики устного диалога' }
  ],
  'programming': [
    { title: 'Помощник умного автодополнения кода и автоисправления багов', desc: 'Плагин IDE предоставляет подсказки автодополнения кода в реальном времени' },
    { title: 'Платформа Low-Code-разработки приложений и автоматизации процессов', desc: 'Требования на естественном языке преобразуются в Low-Code-конфигурацию' },
    { title: 'Система генерации модульных тестов', desc: 'AST разбирает исходный код, генерирует тест-кейсы для граничных условий' },
    { title: 'Инструмент интеллектуального анализа кода и миграции языков', desc: 'Анализ качества кода и предоставление рекомендаций по оптимизации' },
    { title: 'Инструмент автогенерации фронтенд-кода UI', desc: 'Распознавание изображений макетов, генерация адаптивного CSS' }
  ],
  'healthcare': [
    { title: 'Помощник интеллектуальной интерпретации результатов медицинских анализов', desc: 'OCR распознаёт ключевые показатели, интерпретирует аномальные значения' },
    { title: 'Эксперт по консультациям о здоровье на основе поиска по знаниям', desc: 'Построение медицинского графа знаний, ответы через RAG-поиск' },
    { title: 'Платформа анализа данных и принятия решений в клинических исследованиях', desc: 'Интеграция данных EMR, помощь в генерации кода статистического анализа' },
    { title: 'Инструмент автогенерации отчётов медицинской визуализации', desc: 'Описание особенностей снимков, автогенерация структурированных отчётов' },
    { title: 'Интеллектуальный помощник напоминаний о приёме лекарств при хронических заболеваниях', desc: 'Генерация персонализированных напоминаний о лекарствах, поддержка проверки противопоказаний' }
  ],
  'security': [
    { title: 'Движок обнаружения и устранения уязвимостей безопасности кода', desc: 'SAST сканирует код, анализирует природу уязвимостей' },
    { title: 'Система интеллектуального выявления и блокировки фишинговых писем, сгенерированных AI', desc: 'Анализ содержимого писем, выявление фишинговых писем, сгенерированных AI' },
    { title: 'Помощник автогенерации ежедневных отчётов по безопасности', desc: 'Агрегация логов, автоизвлечение ключевых событий' },
    { title: 'Помощник интеллектуальной генерации отчётов о тестировании на проникновение', desc: 'Автогенерация отчётов из описаний уязвимостей' },
    { title: 'Помощник интеллектуального запроса и анализа разведданных об угрозах', desc: 'Подключение разведданных из множества источников, интерпретация их содержимого' }
  ],
  'finance': [
    { title: 'Помощник интеллектуальной генерации отчётов кредитной экспертизы', desc: 'Ввод финансовых данных, автогенерация отчёта кредитной экспертизы' },
    { title: 'Интеллектуальный советник по управлению капиталом для private banking', desc: 'Анализ риск-профиля клиента, генерация рекомендаций по распределению активов' },
    { title: 'Помощник интеллектуальной генерации проспекта IPO и проверки соответствия', desc: 'Модульные шаблоны, автозаполнение описаний бизнеса' },
    { title: 'Система автогенерации корпоративной финансовой отчётности и раннего предупреждения о бизнес-аномалиях', desc: 'Автогенерация финансового анализа и управленческих комментариев' },
    { title: 'Интеллектуальный тренер по отработке скриптов для страховых агентов', desc: 'Симуляция диалога, оценка соответствия скрипта и убедительности' }
  ],
  'enterprise': [
    { title: 'Платформа проверки соответствия и рекомендаций по правкам контрактов на всём жизненном цикле', desc: 'Сравнение положений с базой регламентов, генерация отчёта о проверке соответствия' },
    { title: 'Транскрипция продажных диалогов и рекомендация скриптов', desc: 'ASR-транскрипция, анализ диалога и рекомендация золотых скриптов' },
    { title: 'Система интеллектуальной генерации и дизайна маркетингового контента', desc: 'Генерация маркетинговых текстов и извлечение преимуществ' },
    { title: 'Платформа анализа рекламных размещений конкурентов', desc: 'Сбор рекламы конкурентов, анализ стратегий размещения' },
    { title: 'Система интеллектуального анализа трендовых тем по всей сети и рекомендации контента', desc: 'Анализ горячих трендов и рекомендация углов подачи тем' }
  ],
  'content': [
    { title: 'Платформа помощи в создании контента для кино и романов', desc: 'Предоставление сюжетных набросков, проработки персонажей, генерации диалогов' },
    { title: 'Интеллектуальный помощник написания брендовых историй и PR-статей', desc: 'Ввод ключевых слов бренда, генерация текстов в разных стилях' },
    { title: 'Система интерактивных стримов с виртуальным цифровым человеком и управления трансляциями', desc: 'Цифровой человек + TTS-голос + диалог LLM' },
    { title: 'Генерация сценариев коротких видео и интеллектуальный монтаж', desc: 'Генерация сценариев и раскадровок коротких видео' },
    { title: 'Система интеллектуальной генерации и дизайна маркетингового контента', desc: 'Генерация маркетинговых текстов и извлечение преимуществ' }
  ],
  'government': [
    { title: 'Система интеллектуальной голосовой навигации и автодиспетчеризации горячей линии госуслуг 12345', desc: 'Распознавание речи, понимание запросов и интеллектуальная диспетчеризация' },
    { title: 'Робот интеллектуального сопровождения и Q&A по политике в центрах госуслуг', desc: 'RAG-поиск по базе знаний госуслуг' },
    { title: 'Платформа интеллектуального подбора государственных программ и точечной доставки предприятиям', desc: 'Профиль предприятия автоматически сопоставляется с применимыми программами' },
    { title: 'Помощник интеллектуальной предпроверки административных материалов и проверки соответствия', desc: 'OCR-распознавание и извлечение ключевой информации' },
    { title: 'Платформа интеллектуального выявления и диспетчеризации городских событий', desc: 'Выявление типов событий и диспетчеризация' }
  ],
  'legal': [
    { title: 'Агент "охотник за рисками" контрактов в один клик', desc: 'Выявление потенциальных проблем по чек-листу рисков' },
    { title: 'AI-консультант интеллектуальной оценки шансов на выигрыш по аналогичным делам', desc: 'Извлечение признаков дела, поиск и сопоставление аналогичных дел' },
    { title: 'Радар мониторинга изменений законодательства в реальном времени и анализа влияния на бизнес', desc: 'Разбор содержания изменений и оценка влияния на бизнес' },
    { title: 'Инструмент AIGC-автоподготовки юридических писем', desc: 'Ввод фактических обстоятельств, генерация стандартных юридических писем' },
    { title: 'Плагин "перевода" сложных юридических терминов на понятный язык', desc: 'Генерация простых для понимания объяснений' }
  ],
  'travel': [
    { title: 'Генератор путеводителей "для ленивых" на основе AIGC', desc: 'Генерация ежедневных маршрутов' },
    { title: 'Робот прогноза трендов цен на авиабилеты и отели по всей сети и автозахвата низких цен', desc: 'ML-модель прогнозирует тренды цен' },
    { title: 'Помощник интеллектуальной предпроверки визовых документов и автозаполнения форм', desc: 'OCR-распознавание и проверка полноты информации' },
    { title: 'Дворецкий перевода речи в реальном времени и визуального перевода меню для зарубежных поездок', desc: 'Офлайн-перевод речи, OCR изображений меню' },
    { title: 'Помощник автогенерации красивых заметок о путешествиях и постов из маршрута поездки', desc: 'Извлечение информации из фото, генерация текстов путевых заметок' }
  ],
  'emotion': [
    { title: 'Виртуальный партнёр для круглосуточного глубокого общения на основе LLM', desc: 'Система памяти хранит историю диалогов' },
    { title: 'AI-консультант мультимодального распознавания эмоций и психологической поддержки', desc: 'Анализ тона голоса + распознавание эмоций по тексту' },
    { title: 'Цифровой человек для когнитивных тренировок и пробуждения памяти у пожилых с болезнью Альцгеймера', desc: 'Когнитивные игры-тренировки, старые фото пробуждают воспоминания' },
    { title: 'AIGC-тренер по социальной практике для людей с социальной тревожностью', desc: 'Симуляция виртуальных социальных сценариев' },
    { title: 'Помощник круглосуточного мониторинга настроения и AI-стимулирования позитивных эмоций', desc: 'Анализ трендов настроения и генерация мотивирующего контента' }
  ],
  'entertainment': [
    { title: 'Движок автономных решений NPC в открытом мире на основе LLM', desc: 'Дерево поведения NPC, объединённое с решениями LLM' },
    { title: 'Инструмент AIGC-развёртывания сюжета и помощи ведущему в иммерсивных детективных играх', desc: 'Выбор игроков запускает ветви сюжета' },
    { title: 'Генеративный модификатор концовок интерактивных романов', desc: 'Выбор читателя влияет на направление сюжета' },
    { title: 'AI-комментатор и CV-анализ видео для киберспортивных игр', desc: 'Анализ игровых кадров в реальном времени' },
    { title: 'Система автогенерации аудиокниг с многоролевым TTS-синтезом голоса', desc: 'Распределение ролей по тексту, генерация персонализированных голосов' }
  ],
  'ecommerce': [
    { title: 'Инструмент пакетного создания высококонверсионных страниц товаров на основе AIGC', desc: 'Генерация текстов о преимуществах и описаний сцен' },
    { title: 'Фабрика AI-примерки на виртуальной модели и генерации демонстрационных видео для одежды', desc: 'Генерация эффекта примерки на виртуальной модели' },
    { title: 'Помощник многоязычной локализации и редактуры с LLM для трансграничной торговли', desc: 'Многоязычный перевод описаний товаров' },
    { title: 'Круглосуточная система продаж через стримы с AIGC-цифровым человеком', desc: 'Цифровой человек + генерация скриптов в реальном времени' },
    { title: 'Движок AI-аналитики рыночных трендов и прогноза хитов', desc: 'Анализ горячих трендов, рекомендации по выбору товаров' }
  ],
  'energy': [
    { title: 'AI-консультант анализа бытового энергопотребления и стратегий энергосбережения', desc: 'Анализ паттернов потребления, генерация рекомендаций по энергосбережению' },
    { title: 'Система CV-распознавания дефектов фотоэлектрических модулей с дронов', desc: 'Облёт и съёмка дроном, анализ термоинфракрасных изображений' },
    { title: 'Агент AI-прогноза трендов цен на спотовой торговле электроэнергией и стратегий автоприбыли', desc: 'Модель прогноза цен, генерация стратегий' },
    { title: 'Помощник AI-автоматического расчёта углеродного следа по всей цепочке и генерации ESG-отчётов', desc: 'Расчёт по коэффициентам выбросов углерода, генерация ESG-отчётов' },
    { title: 'Система AI-прогноза нагрузки энергосети при экстремальной погоде и командования аварийной диспетчеризацией', desc: 'Модель прогноза нагрузки, генерация диспетчерских стратегий' }
  ],
  'av-media': [
    { title: 'Инструмент AI-выявления ярких моментов в длинных видео и автонарезки коротких роликов', desc: 'Анализ содержимого видео, распознавание ключевых кадров' },
    { title: 'Помощник AI-разделения фонового шума в видео и улучшения голоса', desc: 'Модель разделения аудио, удаление фонового шума' },
    { title: 'Рабочая станция 4K-реставрации старых изображений со сверхразрешением и AI-колоризации', desc: 'Модель видео-сверхразрешения, AI-автоколоризация' },
    { title: 'Система преобразования текста в реалистичный TTS-голос с управлением эмоциями', desc: 'Многоголосая TTS-модель, управление эмоциями' },
    { title: 'Помощник AI-транскрипции записей встреч и извлечения задач к исполнению', desc: 'Транскрипция с разделением голосов участников многолюдной встречи' }
  ],
  'ai-marketing': [
    { title: 'Движок AIGC-автонаписания вирусных текстов для Xiaohongshu', desc: 'Генерация рекомендательных текстов, оптимизация эмодзи' },
    { title: 'Инструмент AI-вёрстки маркетинговых постеров и адаптации под разные размеры', desc: 'Интеллектуальный подбор шаблонов постеров' },
    { title: 'Платформа AIGC-генерации креативных LOGO и построения системы VI', desc: 'Креативная генерация LOGO, генерация стандартов VI' },
    { title: 'Помощник AI-отслеживания трендовых тем по всей сети и генерации трендового маркетингового креатива', desc: 'Анализ маркетинговых углов, генерация креативных решений' },
    { title: 'Помощник AIGC-генерации креативных сценариев коротких видео и руководства по раскадровке', desc: 'Генерация сценариев и раскадровок, рекомендации по съёмке' }
  ],
  'data-intelligence': [
    { title: 'Инструмент автогенерации SQL-запросов из естественного языка', desc: 'Запрос на естественном языке преобразуется в SQL' },
    { title: 'Система интеллектуальной инвентаризации и классификации каталога данных предприятия', desc: 'Сбор метаданных, автоклассификация' },
    { title: 'Движок автообнаружения аномалий качества данных и рекомендаций по исправлению', desc: 'Движок правил + ML-модель обнаруживают аномалии' },
    { title: 'Помощник интеллектуальной генерации отчётов и настройки визуализации', desc: 'Диалоговая генерация конфигурации отчётов' },
    { title: 'Помощник интеллектуального Q&A по определениям метрик данных', desc: 'Создание базы знаний из документов с определениями метрик' }
  ]
}

const recommendationMap = {
  'creative-content': {
    'increase-efficiency': ['content', 'av-media', 'ai-marketing', 'entertainment'],
    'reduce-cost': ['content', 'ecommerce', 'ai-marketing'],
    'improve-experience': ['entertainment', 'emotion', 'travel', 'content'],
    'innovate-business': ['ai-marketing', 'content', 'av-media', 'entertainment']
  },
  'tech-service': {
    'increase-efficiency': ['programming', 'enterprise', 'data-intelligence', 'customer-service'],
    'reduce-cost': ['programming', 'enterprise', 'manufacturing'],
    'improve-experience': ['customer-service', 'enterprise', 'programming'],
    'innovate-business': ['data-intelligence', 'programming', 'security', 'enterprise']
  },
  'data-intel': {
    'increase-efficiency': ['data-intelligence', 'finance', 'enterprise', 'manufacturing'],
    'reduce-cost': ['data-intelligence', 'manufacturing', 'energy'],
    'improve-experience': ['data-intelligence', 'customer-service', 'ecommerce'],
    'innovate-business': ['data-intelligence', 'finance', 'security', 'ai-marketing']
  },
  'user-service': {
    'increase-efficiency': ['customer-service', 'ecommerce', 'travel', 'enterprise'],
    'reduce-cost': ['customer-service', 'ecommerce', 'enterprise'],
    'improve-experience': ['customer-service', 'emotion', 'travel', 'ecommerce', 'entertainment'],
    'innovate-business': ['ecommerce', 'travel', 'emotion', 'entertainment']
  },
  'industry-solution': {
    'increase-efficiency': ['manufacturing', 'healthcare', 'finance', 'government'],
    'reduce-cost': ['manufacturing', 'energy', 'enterprise', 'finance'],
    'improve-experience': ['healthcare', 'education', 'government', 'travel'],
    'innovate-business': ['finance', 'security', 'legal', 'healthcare', 'government']
  }
}

const interestOptions = [
  { label: 'Генерация креативного контента', value: 'creative-content', desc: 'Тексты, изображения, видео и другой креативный контент' },
  { label: 'Инструменты технических сервисов', value: 'tech-service', desc: 'Инструменты разработки, автоматизация, помощь с кодом' },
  { label: 'Анализ данных и аналитика', value: 'data-intel', desc: 'Анализ данных, прогнозирование, интеллектуальное принятие решений' },
  { label: 'Опыт обслуживания пользователей', value: 'user-service', desc: 'Клиентский сервис, маркетинг, пользовательский опыт' },
  { label: 'Отраслевые решения', value: 'industry-solution', desc: 'Глубокие применения для конкретных отраслей' }
]

const purposeOptions = [
  { label: 'Повысить эффективность', value: 'increase-efficiency', desc: 'Автоматизация, ускорение процессов' },
  { label: 'Снизить издержки', value: 'reduce-cost', desc: 'Сокращение трудозатрат, оптимизация ресурсов' },
  { label: 'Улучшить опыт', value: 'improve-experience', desc: 'Удовлетворённость пользователей, качество сервиса' },
  { label: 'Бизнес-инновации', value: 'innovate-business', desc: 'Новые продукты, новые модели' }
]

const industries = [
  { key: 'manufacturing', name: 'Производство', anchor: '#_1-manufacturing-industry' },
  { key: 'customer-service', name: 'Интеллектуальный клиентский сервис', anchor: '#_2-intelligent-customer-service' },
  { key: 'education', name: 'Образование', anchor: '#_3-education-industry' },
  { key: 'programming', name: 'Интеллектуальное программирование', anchor: '#_4-intelligent-programming' },
  { key: 'healthcare', name: 'Здравоохранение', anchor: '#_5-healthcare' },
  { key: 'security', name: 'Кибербезопасность', anchor: '#_6-network-security' },
  { key: 'finance', name: 'Финансы и страхование', anchor: '#_7-finance-insurance' },
  { key: 'enterprise', name: 'Корпоративные сервисы', anchor: '#_8-enterprise-services' },
  { key: 'content', name: 'Производство контента и операции', anchor: '#_9-content-production-operations' },
  { key: 'government', name: 'Умное госуправление', anchor: '#_10-smart-government-management' },
  { key: 'legal', name: 'Юридические вопросы и управление контрактами', anchor: '#_11-legal-affairs-contract-management' },
  { key: 'travel', name: 'Туризм и транспортные услуги', anchor: '#_12-travel-transportation-services' },
  { key: 'emotion', name: 'Эмоциональное сопровождение', anchor: '#_13-emotional-companionship' },
  { key: 'entertainment', name: 'Досуг и развлечения', anchor: '#_14-leisure-entertainment' },
  { key: 'ecommerce', name: 'Услуги электронной коммерции', anchor: '#_15-ecommerce-services' },
  { key: 'energy', name: 'Энергетика', anchor: '#_16-energy' },
  { key: 'av-media', name: 'Аудио и видео', anchor: '#_17-audio-video' },
  { key: 'ai-marketing', name: 'AI-маркетинг', anchor: '#_18-ai-marketing' },
  { key: 'data-intelligence', name: 'Аналитика данных', anchor: '#_19-data-intelligence' }
]

const recommendationTopics = computed(() => {
  if (!interestPoint.value || !purpose.value) return []
  
  const keys = recommendationMap[interestPoint.value]?.[purpose.value] || []
  const topics = []
  
  keys.forEach(key => {
    const industry = industries.find(item => item.key === key)
    const industryTopics = topicPool[key] || []
    
    if (industry && industryTopics.length > 0) {
      const count = Math.floor(Math.random() * 2) + 1
      const shuffled = [...industryTopics].sort(() => Math.random() - 0.5)
      const selected = shuffled.slice(0, Math.min(count, shuffled.length))
      
      selected.forEach(topic => {
        topics.push({
          ...topic,
          industryKey: key,
          industryName: industry.name,
          industryAnchor: industry.anchor
        })
      })
    }
  })
  
  return topics.sort(() => Math.random() - 0.5).slice(0, 8)
})

const currentSelection = computed(() => {
  const interest = interestOptions.find(i => i.value === interestPoint.value)
  const pur = purposeOptions.find(p => p.value === purpose.value)
  return {
    interest: interest?.label || '',
    purpose: pur?.label || ''
  }
})

const scrollToAnchor = (anchor) => {
  setTimeout(() => {
    let element = document.querySelector(anchor)
    
    if (!element) {
      const altAnchor = anchor.replace('#_', '#')
      element = document.querySelector(altAnchor)
    }
    
    if (!element) {
      const anchorText = decodeURIComponent(anchor.replace('#', '').replace(/^_/, ''))
      const headings = document.querySelectorAll('h2, h3')
      
      for (let heading of headings) {
        const headingText = heading.textContent.trim()
        const cleanHeading = headingText.replace(/^\d+\.\s*/, '')
        if (cleanHeading === anchorText || headingText.includes(anchorText)) {
          element = heading
          break
        }
      }
    }
    
    if (element) {
      element.scrollIntoView({ 
        behavior: 'smooth',
        block: 'start'
      })
      element.style.backgroundColor = '#f0f9ff'
      element.style.transition = 'background-color 0.3s'
      element.style.padding = '8px'
      element.style.borderRadius = '4px'
      setTimeout(() => {
        element.style.backgroundColor = ''
        element.style.padding = ''
      }, 2000)
    }
  }, 100)
}

const resetSelection = () => {
  interestPoint.value = ''
  purpose.value = ''
}
</script>

# Справочник прикладных сценариев для отраслей B2B

## Обзор главы

<ChapterIntroduction :duration="duration" :tags="['B2B-приложения', 'Отраслевые применения', 'AI-сценарии', 'Справочник внедрений', 'Отраслевые решения']" coreOutput="Изучить более 15 прикладных сценариев в отраслях B2B" expectedOutput="Найти направления проектов, подходящие для корпоративных клиентов">

Этот документ обобщает **применения больших языковых моделей (LLM) в корпоративных сценариях B2B**. В отличие от B2C, ориентированного на пользовательский опыт и эмоции, продукты B2B больше сосредоточены на **решении реальных бизнес-задач, повышении эффективности и снижении издержек**. Каждый сценарий обладает **реальной осуществимостью внедрения** и охватывает полный путь размышлений от **анализа требований до технической реализации**, что подходит разработчикам AI-приложений, ориентированным на корпоративных клиентов.

</ChapterIntroduction>

## Быстрый выбор отраслевого направления

<el-card shadow="hover" style="margin-top: 16px; margin-bottom: 24px; border-left: 5px solid #409EFF;">
  <div style="font-weight: 600; margin-bottom: 8px;">Найдите подходящий вам прикладной сценарий</div>
  <div style="color: #606266; font-size: 14px; line-height: 1.6; margin-bottom: 12px;">
    Выберите интересующее вас направление и целевую задачу. Система порекомендует связанные отраслевые сценарии. Нажмите на строку, чтобы перейти к соответствующей главе.
  </div>
  <el-row :gutter="16">
    <el-col :span="12">
      <el-select v-model="interestPoint" placeholder="Выберите интересующее направление" style="width: 100%;">
        <el-option
          v-for="item in interestOptions"
          :key="item.value"
          :label="item.label"
          :value="item.value"
        >
          <div style="font-weight: 500;">{{ item.label }}</div>
          <div style="font-size: 12px; color: #909399;">{{ item.desc }}</div>
        </el-option>
      </el-select>
    </el-col>
    <el-col :span="12">
      <el-select v-model="purpose" placeholder="Выберите цель" style="width: 100%;">
        <el-option
          v-for="item in purposeOptions"
          :key="item.value"
          :label="item.label"
          :value="item.value"
        >
          <div style="font-weight: 500;">{{ item.label }}</div>
          <div style="font-size: 12px; color: #909399;">{{ item.desc }}</div>
        </el-option>
      </el-select>
    </el-col>
  </el-row>
  
  <div v-if="recommendationTopics.length > 0" style="margin-top: 16px;">
    <div style="font-weight: 600; margin-bottom: 10px; color: #409EFF;">
      Рекомендованных вам сценариев: {{ recommendationTopics.length }}
      <span style="font-weight: normal; color: #909399; font-size: 13px; margin-left: 8px;">
        ({{ currentSelection.interest }} + {{ currentSelection.purpose }})
      </span>
    </div>
    <el-table
      :data="recommendationTopics"
      style="width: 100%; cursor: pointer;"
      @row-click="(row) => scrollToAnchor(row.industryAnchor)"
      highlight-current-row
    >
      <el-table-column prop="title" label="Прикладной сценарий" min-width="300">
        <template #default="scope">
          <div style="font-weight: 500; color: #303133;">{{ scope.row.title }}</div>
          <div style="font-size: 12px; color: #909399; margin-top: 4px;">{{ scope.row.desc }}</div>
        </template>
      </el-table-column>
      <el-table-column prop="industryName" label="Отрасль" width="180" align="center">
        <template #default="scope">
          <el-tag type="info" effect="light" size="small">{{ scope.row.industryName }}</el-tag>
        </template>
      </el-table-column>
    </el-table>
    <div style="margin-top: 10px; font-size: 12px; color: #909399;">
      💡 Нажмите на любую строку таблицы, чтобы перейти к соответствующему разделу отрасли
    </div>
  </div>

  <div v-else-if="!interestPoint || !purpose" style="margin-top: 14px; color: #909399; font-size: 13px;">
    <span v-if="!interestPoint && !purpose">💡 Пожалуйста, выберите и интересующее направление, и цель</span>
    <span v-else-if="!interestPoint">💡 Пожалуйста, выберите интересующее направление</span>
    <span v-else>💡 Пожалуйста, выберите цель</span>
  </div>

  <div v-if="interestPoint || purpose" style="margin-top: 12px;">
    <el-button size="small" @click="resetSelection">Сбросить выбор</el-button>
  </div>
</el-card>

---

## Industry Quick Overview

### Mainstream Technology Choices

In AI application development, common technical directions include:

1. **LLM (Large Language Models)**: Strong in natural language tasks such as dialogue, text generation, summarization, and translation. Suitable for intelligent customer service, content creation, and knowledge Q&A applications.
2. **VLM (Vision-Language Models)**: Combines visual understanding and language reasoning to support image description, visual Q&A, and multimodal generation. Useful for medical imaging analysis, industrial inspection, and creative design scenarios.
3. **GenAI (Generative AI)**: Covers text generation, image generation (for example Stable Diffusion, DALL-E), video generation, and more. It rapidly produces creative outputs for design support, marketing asset creation, and training content.

### Selection Strategy

Learners can choose directions based on these dimensions:

1. **Interest-first**: Start from industries or technologies you are personally interested in to keep momentum.
   - Interested in creative design: Try content production or industrial design applications
   - Interested in technical challenge: Try cybersecurity or healthcare applications
   - Interested in social value: Try smart government or education applications
2. **Industry fit**: Match your background and resource advantages.
   - Manufacturing practitioners: Prioritize manufacturing and enterprise-service applications
   - Educators: Prioritize education and content production applications
   - Healthcare practitioners: Explore healthcare and health management applications
3. **Technical difficulty**: Pick complexity based on your current foundation.
   - Beginner: Intelligent customer service, content creation, basic Q&A systems
   - Intermediate: Industrial quality inspection, medical image analysis, coding assistants
   - Advanced: Financial risk control, cybersecurity, complex multimodal systems

---

## 1. Manufacturing Industry

> 💡 **Core Concept**: AI empowers traditional manufacturing to achieve intelligent transformation

| No. | Application Scenario Name | Application Scenario Function |
| :--: | ------------ | ------------ |
| 1 | New Energy Bus Exterior AI-Assisted Design Platform | Integrates image generation models for exterior concept design; generates multiple design schemes based on requirements |
| 2 | Intelligent Drawing Design & Review Assistant | Builds enterprise design specification knowledge base using RAG; provides intelligent review suggestions |
| 3 | Technical Documentation Auto-Generation System | LLM auto-generates product specifications, operation manuals; supports multi-format export |
| 4 | Production Equipment Inspection Report Auto-Generation | Voice input describes equipment status; structured inspection report auto-generated |
| 5 | Industrial Equipment Fault Diagnosis Q&A | Builds vector knowledge base from historical fault cases; provides intelligent diagnosis suggestions |
| 6 | LLM Information-Retrieval Data Warehouse | Uses Text-to-SQL to convert natural-language queries into database queries; Superset visualizes results; Doris or ClickHouse as OLAP engine |
| 7 | Industrial Equipment Fault-Diagnosis Knowledge Q&A Assistant | Builds a vector knowledge base from historical fault cases; LLM provides diagnosis suggestions and solution plans based on fault descriptions |
| 8 | Production Quality Inspection Report Generation and Defect Classification | OCR identifies defects in inspection photos; LLM generates structured quality reports and classifies defect type and severity |
| 9 | Inventory Counting Assistant and Inventory Report Generation | Inputs stocktaking data; LLM compares with system inventory and generates discrepancy reports with abnormal-inventory alerts |
| 10 | Process Optimization Suggestion Intelligent Q&A System | Builds a RAG knowledge base from process documents; LLM provides optimization suggestions based on production issues |

---

## 2. Intelligent Customer Service

> 💡 **Core Concept**: Empowers customer service with AI to achieve 24/7 intelligent response

| No. | Application Scenario Name | Application Scenario Function |
| :--: | ------------ | ------------ |
| 1 | Multi-Channel Intelligent Customer Service Auto-Reply | Connects to website, APP, WeChat, and other channels; LLM understands intent and generates responses |
| 2 | Potential Customer Mining & Follow-up Assistant | Analyzes historical conversation records; identifies high-intent leads for sales follow-up |
| 3 | Enterprise Internal Knowledge Intelligent Q&A | Builds vector knowledge base from internal documents; provides precise Q&A service for employees |
| 4 | Customer Service Conversation Smart Summary | Automatically generates conversation summaries; extracts key information and creates follow-up tickets |
| 5 | Golden Script Recommendation Knowledge Base | Analyzes excellent service cases; extracts golden scripts for team sharing and training |
| 6 | Customer Service Script Compliance Auto-Check Assistant | Customer-service staff input reply drafts; LLM checks script compliance and sensitive words in real time and provides revision suggestions |
| 7 | Customer Service Ticket Auto-Summary and Classification Tool | LLM summarizes long conversations and auto-classifies tags; Elasticsearch supports full-text ticket search |
| 8 | Customer Emotion Monitoring and Abnormality Alert Tool | Real-time analysis of voice tone and text sentiment; LLM identifies abnormal emotions and triggers alerts with WebSocket push |
| 9 | Golden Script Recommendation Knowledge-Base System for Customer Service | LLM analyzes excellent customer-service conversations, refines high-performing templates, and recommends scripts based on context |
| 10 | Intelligent Outbound-Call Conversation Analysis and QA Assistant | After outbound-call recording transcription, LLM extracts key information; automatically generates QA reports and improvement suggestions |

---

## 3. Education Industry

> 💡 **Core Concept**: Personalized learning powered by AI to achieve adaptive education

| No. | Application Scenario Name | Application Scenario Function |
| :--: | ------------ | ------------ |
| 1 | Personalized Language Learning Path Planning | Evaluates learner level; generates personalized daily/weekly learning task plans |
| 2 | Lesson Plan Auto-Generation Platform | Inputs course outline; AI generates complete lesson plans including teaching objectives and processes |
| 3 | Homework Auto-Grading & Learning Diagnosis | OCR recognizes handwritten answers; AI provides grading and improvement suggestions |
| 4 | Job Competency Model & Learning Map | Analyzes job requirements; generates competency models and corresponding learning paths |
| 5 | Foreign Language Oral Practice with AI | LLM plays role-play partners; simulates various real-life scenarios for speaking practice |
| 6 | School-Based Curriculum Construction and Courseware Production Tool | LLM analyzes school characteristics and student needs to generate curriculum frameworks; integrates PPT generation APIs for automatic courseware creation |
| 7 | College-Application Recommendation and Career Planning Platform | LLM analyzes candidate scores, ranking, interests, and other factors, then combines admissions data to recommend schools and majors |
| 8 | Youth Programming Code Assistant | LLM explains code logic and provides coding guidance; supports switching between block languages and Python |
| 9 | Knowledge-Point Mind Map Auto-Generation and Learning-Path Recommendation Tool | Input course topics; LLM automatically generates knowledge maps and recommends next-step learning content based on progress |
| 10 | Chinese/English Essay Auto-Scoring and Correction Engine | LLM scores from dimensions such as idea, structure, language, and diversity, and generates annotations with high-quality sample comparison |

---

## 4. Intelligent Programming

> 💡 **Core Concept**: AI assists development to improve programmer productivity

| No. | Application Scenario Name | Application Scenario Function |
| :--: | ------------ | ------------ |
| 1 | Intelligent Code Completion & Bug Fix | IDE plugin provides real-time code completion suggestions; automatically fixes simple bugs |
| 2 | Low-Code Application Builder | Natural language describes requirements; AI converts to low-code visual configurations |
| 3 | Unit Test Auto-Generation | Analyzes source code structure; generates boundary condition test cases automatically |
| 4 | Code Quality Analysis Tool | Analyzes code complexity, security vulnerabilities; provides optimization recommendations |
| 5 | UI Code Auto-Generation from Design | Uploads design draft images; AI generates responsive HTML/CSS code |
| 6 | Natural Language to SQL Auto-Generation Tool | LLM converts natural-language data requests to SQL and supports complex multi-table joins and aggregation queries |
| 7 | API Automated Testing and Documentation Generation Platform | LLM analyzes code comments and API definitions, auto-generates test cases and API docs, and integrates Postman for test execution |
| 8 | System Log Analysis and Fault Localization | ELK Stack collects log data; LLM extracts key anomaly information and locates root causes, then recommends fixes |
| 9 | Frontend UI Code Auto-Generation Tool | OCR recognizes layout structures from design images; LLM generates responsive CSS and component code with TailwindCSS integration |
| 10 | Intelligent Database Schema Design and Modeling Assistant | Input business requirement docs to LLM to auto-generate ER diagrams and schema definitions; supports exporting MySQL/PostgreSQL DDL scripts |

---

## 5. Healthcare

> 💡 **Core Concept**: AI assists medical diagnosis to improve healthcare service efficiency

| No. | Application Scenario Name | Application Scenario Function |
| :--: | ------------ | ------------ |
| 1 | Medical Test Report Interpretation | OCR recognizes test indicators; intelligently interprets abnormal values and gives suggestions |
| 2 | Health Consultation Expert | Builds medical knowledge graph; provides professional health Q&A based on user symptoms |
| 3 | Clinical Research Data Analysis Platform | Integrates EMR data; assists in generating statistical analysis code for research |
| 4 | Medical Imaging Report Auto-Generation | Describes imaging features; generates structured medical imaging reports |
| 5 | Chronic Disease Medication Reminder | Generates personalized medication plans; supports drug interaction and contraindication checks |
| 6 | Drug Package-Insert Intelligent Q&A Assistant | Upload package-insert images or input drug names; LLM answers dosage, side effects, and precautions |
| 7 | Disease Knowledge Popular-Science Article Generator | Input disease name and audience type; LLM generates easy-to-understand educational content and supports multiple versions |
| 8 | Medical Imaging Report Auto-Generation Tool | Radiologists describe imaging features; LLM auto-generates structured report content and supports common exam templates |
| 9 | Surgical Record Intelligent Generation and Archiving Assistant | Voice input records key surgical steps; LLM generates structured surgical records and auto-links surgery codes |
| 10 | Chronic Disease Medication Reminder Intelligent Assistant | Patients input medication lists; LLM generates personalized reminders and supports contraindication checking and interactive Q&A |

---

## 6. Network Security

> 💡 **Core Concept**: AI empowers security operations to achieve intelligent threat detection and response

| No. | Application Scenario Name | Application Scenario Function |
| :--: | ------------ | ------------ |
| 1 | Code Security Vulnerability Detection | Static analysis scans code; identifies and suggests fixes for security vulnerabilities |
| 2 | AI Phishing Email Detection | Analyzes email content; identifies AI-generated phishing emails |
| 3 | Security Operations Daily Report | Aggregates security logs; automatically extracts and generates daily reports |
| 4 | Penetration Test Report Generation | Inputs vulnerability descriptions; AI generates complete penetration test reports |
| 5 | Threat Intelligence Analysis Assistant | Connects to threat intelligence sources; interprets and analyzes potential threats |
| 6 | Malicious Code Protection and Privacy Compliance Monitoring | Sandboxes suspicious-file behavior; LLM identifies malicious features and generates signatures; scans sensitive data exposure |
| 7 | Security Configuration Compliance Checklist Generation Tool | Input target system type; LLM generates configuration checklists supporting standards such as MLPS 2.0 and CIS |
| 8 | Threat Intelligence Intelligent Query and Analysis Assistant | Connects multi-source threat intelligence (open-source/commercial); LLM interprets intelligence and links it with enterprise assets |
| 9 | Security Incident Postmortem Report Generation Assistant | After incidents, LLM auto-generates timeline-based postmortem reports with root-cause analysis and remediation suggestions |
| 10 | Global Threat Intelligence Monitoring and Alert Center | Crawlers collect global security news and vulnerability disclosures; LLM extracts key information, assesses impact, and sends alerts |

---

## 7. Finance & Insurance

> 💡 **Core Concept**: AI empowers financial services to achieve intelligent risk control and wealth management

| No. | Application Scenario Name | Application Scenario Function |
| :--: | ------------ | ------------ |
| 1 | Credit Due Diligence Report Generation | Inputs enterprise financial data; AI generates comprehensive credit due diligence reports |
| 2 | Private Bank Wealth Management Advisor | Analyzes client risk preference; generates personalized asset allocation strategies |
| 3 | IPO Prospectus Generation & Compliance Check | Uses modular templates; auto-fills business descriptions with compliance verification |
| 4 | Financial Report & Anomaly Warning | Auto-generates financial analysis reports; monitors business anomalies in real-time |
| 5 | Insurance Agent Practice Coach | Simulates customer scenarios; evaluates script compliance and persuasion skills |
| 6 | Compliance Case Intelligent Retrieval and Q&A Assistant | Builds knowledge bases from regulatory penalty cases; LLM answers compliance questions and provides relevant case references |
| 7 | Insurance Agent Intelligent Script Practice | LLM plays different customer personas for simulation and evaluates script compliance and persuasion with transcription analysis |
| 8 | Insurance Product Clause Analysis and Competitor Comparison Platform | Parses clauses structurally; LLM generates feature summaries and key cautions |
| 9 | Customer Script Emotion Recognition Service | Combines voice-emotion recognition with script-compliance checks and gives real-time coaching suggestions |
| 10 | Insurance Claim Progress Intelligent Query and Dialogue Assistant | Users input policy or case numbers; LLM queries claim status and answers claim-related questions |

---

## 8. Enterprise Services

> 💡 **Core Concept**: AI empowers enterprise operations to achieve efficiency improvement and cost reduction

| No. | Application Scenario Name | Application Scenario Function |
| :--: | ------------ | ------------ |
| 1 | Contract Compliance Review Platform | Compares contract clauses with regulations; generates compliance review reports |
| 2 | Sales Conversation Analysis & Script Recommendation | Transcribes sales calls; analyzes conversation and recommends improvement strategies |
| 3 | Marketing Content Auto-Generation | Generates marketing copy, social media posts, and advertising scripts |
| 4 | Competitor Ad Analysis Platform | Collects and analyzes competitor advertising strategies |
| 5 | Hot Topic Analysis & Content Recommendation | Analyzes trending topics; recommends content creation angles |
| 6 | Resume Intelligent Parsing and Job Matching System | Parses resume PDFs to extract key information; LLM matches suitable roles and generates interview suggestions; integrates with ATS systems |
| 7 | Employee Onboarding Guidance and Q&A Assistant | Uses RAG retrieval over onboarding docs; LLM answers common new-hire questions |
| 8 | Employee Performance Feedback and OKR Management Platform | Collects OKR data; LLM analyzes goal completion and generates feedback suggestions with 360-feedback integration |
| 9 | Intelligent Meeting Minutes and To-Do Management | Transcribes meeting recordings; LLM extracts key points and action items; auto-creates tasks in task systems |
| 10 | Invoice Recognition and Expense Reimbursement Auto-Processing | OCR recognizes invoice fields and automatically checks authenticity and reimbursement compliance; integrates with finance systems |

---

## 9. Content Production & Operations

> 💡 **Core Concept**: AI empowers content creation to achieve efficient and high-quality output

| No. | Application Scenario Name | Application Scenario Function |
| :--: | ------------ | ------------ |
| 1 | Film & Novel Creation Assistant | Generates story outlines, character settings, and dialogue scripts |
| 2 | Brand Story & PR Writing Assistant | Inputs brand keywords; generates multi-style PR articles |
| 3 | Digital Human Live Streaming System | Creates digital human anchors; generates real-time dialogue for live streaming |
| 4 | Short Video Script & Editing | Generates short video scripts; provides intelligent editing suggestions |
| 5 | Marketing Content Design System | Generates advertising copy and designs marketing materials |
| 6 | Intelligent Marketing Content Generation and Design System | Input product information; LLM generates marketing copy and selling-point extraction; integrates with template-design tools |
| 7 | Multi-Platform Ad ROI Real-Time Monitoring and Strategy Optimization System | Connect ad-platform APIs for data collection; LLM analyzes performance and generates optimization suggestions with anomaly alerts |
| 8 | Search-Engine Keyword and Traffic Analysis | Collect keyword-tool data; LLM analyzes trend and competition and recommends topic direction |
| 9 | Competitor Ad Placement Analysis Platform | Uses third-party data APIs to collect competitor ads; LLM analyzes placement strategy and creative patterns |
| 10 | Full-Network Hot Topic Analysis and Content Recommendation System | Collects trending data; LLM analyzes trend shifts and recommends content angles with calendar scheduling |

---

## 10. Smart Government

> 💡 **Core Concept**: AI empowers government services to achieve intelligent governance

| No. | Application Scenario Name | Application Scenario Function |
| :--: | ------------ | ------------ |
| 1 | 12345 Hotline Intelligent Routing | Voice recognition understands citizen requests; intelligently routes to departments |
| 2 | Government Service Q&A Robot | Builds government knowledge base; provides policy consultation services |
| 3 | Enterprise Policy Matching Platform | Analyzes enterprise profiles; intelligently matches applicable support policies |
| 4 | Approval Materials Pre-Review | OCR recognizes application materials; automatically checks completeness |
| 5 | City Grid Event Management | Identifies event types from reports; intelligently dispatches to responsible departments |
| 6 | Social Sentiment Big-Data Analysis and Risk Early Warning System | Fuses multiple sources such as hotlines, online sentiment, and field visits; LLM identifies risk hotspots |
| 7 | Government Archive Digitization Recognition and Intelligent Filing Platform | OCR recognizes archive text; LLM extracts key information and auto-classifies; supports full-text retrieval |
| 8 | Emergency Command and Rescue Resource Intelligent Dispatch Platform | Collects emergency-event data; LLM generates emergency response plans with resource-dispatch optimization |
| 9 | Grid-Based Atmospheric Pollution Monitoring and Precision Traceability System | Collects air-quality sensor data; CV identifies pollution sources; LLM analyzes trends and traces causes |
| 10 | Public-Safety Incident Intelligent Risk Warning Assistant | Integrates historical events and real-time reports; LLM estimates risk levels and outputs warning recommendations |

---

## 11. Legal Affairs

> 💡 **Core Concept**: AI empowers legal services to achieve intelligent contract review and case analysis

| No. | Application Scenario Name | Application Scenario Function |
| :--: | ------------ | ------------ |
| 1 | Contract Risk Vulnerability Detection | Compares contracts against risk checklists; identifies potential legal risks |
| 2 | Case Win Rate Analysis | Analyzes case features; retrieves similar cases and predicts outcomes |
| 3 | Legal Regulation Change Monitoring | Monitors regulatory updates; analyzes impact on business operations |
| 4 | Legal Letter Auto-Drafting | Inputs case facts; AI generates standard legal letters |
| 5 | Legal Terms Plain Language Explanation | Translates complex legal terms into easy-to-understand language |
| 6 | Courtroom Recording Real-Time Transcription and Dispute-Focus Extraction Recorder | ASR transcribes hearing audio; LLM extracts dispute focuses and key arguments with timestamps |
| 7 | Full-Network IP Infringement Clue Monitoring and Blockchain Evidence Preservation System | Monitors e-commerce and social media infringement; automatically collects and preserves evidence |
| 8 | LLM-Based IPO Prospectus Key-Data Consistency Check and Risk Alert Agent | Compares data across prospectus sections; LLM identifies inconsistencies and abnormal values with risk tags |
| 9 | Complex Legal Clause "Translation" Plugin in Plain Language | Users select legal clauses and LLM outputs understandable explanations |
| 10 | Case Evidence-Chain Intelligent Structuring and Visualization System | Upload evidence materials; LLM analyzes evidence relationships and timelines |

---

## 12. Travel & Transportation

> 💡 **Core Concept**: AI empowers travel services to achieve personalized travel planning

| No. | Application Scenario Name | Application Scenario Function |
| :--: | ------------ | ------------ |
| 1 | Lazy Travel Guide Generator | Inputs travel preferences; AI generates daily itinerary with recommendations |
| 2 | Flight & Hotel Price Prediction | Uses ML models to predict price trends; suggests optimal booking timing |
| 3 | Visa Materials Pre-Review | OCR recognizes visa materials; automatically checks for completeness |
| 4 | Real-Time Translation for Travel | Offline voice translation; recognizes and translates menu images abroad |
| 5 | Travel Notes Auto-Generation | Extracts information from travel photos; generates shareable travel journals |
| 6 | Data-Driven Hotel "Pitfall Avoidance" Analyzer Based on Real Reviews | Collects hotel review data; LLM extracts positive and negative keyword patterns |
| 7 | Immersive Destination VR Preview and Virtual Room Selection Platform | Collects 360-degree panoramas; VR enables immersive previews and virtual room tours |
| 8 | Travel Footprint Auto-Generated Travel Notes and Social Copy Assistant | Extracts time/location metadata from photos; LLM generates travel notes with template-based layout |
| 9 | Enterprise Travel Invoice Aggregation and Compliance Reimbursement Management Platform | Connects travel-platform APIs for automatic invoice collection and compliance checks |
| 10 | Scenic-Area Crowd Congestion Prediction and Off-Peak Route Navigation | Collects scenic-area crowd data; ML predicts congestion windows and recommends off-peak routes |

---

## 13. Emotional Companionship

> 💡 **Core Concept**: AI provides 24/7 emotional support and psychological companionship

| No. | Application Scenario Name | Application Scenario Function |
| :--: | ------------ | ------------ |
| 1 | Virtual Companion | LLM-based AI companion with memory system; provides emotional support |
| 2 | Emotional Recognition & Counseling | Analyzes voice tone and text emotion; provides professional psychological suggestions |
| 3 | Cognitive Training for Elderly | Provides cognitive games; uses old photos to trigger memory for dementia patients |
| 4 | Social Anxiety Practice Coach | Creates virtual social scenarios; helps practice social interactions |
| 5 | Mood Monitoring & Incentive Assistant | Analyzes mood patterns; generates positive encouragement content |
| 6 | Generative AI Customized Bedtime Story Machine for Children | Parents input themes/preferences; LLM generates customized stories with background music support |
| 7 | Deceased Digital-Life Reconstruction and LLM Cross-Time Dialogue System | Trains personalized models from pre-death voice/text data and generates memory-based conversations |
| 8 | MBTI-Based AI Personality Mirror and Empathetic Chatbot | Inputs MBTI results; LLM outputs personality analysis and empathetic responses with match suggestions |
| 9 | Privacy-Protected AI Confession Tree-Hole for Teenagers | Anonymous channel for emotional expression; LLM provides listening/suggestions with sensitive-word alerts |
| 10 | Self-Evolving AI Virtual Pet Growth System | Trains pet personality models and supports interaction-driven growth and virtual customization |

---

## 14. Leisure & Entertainment

> 💡 **Core Concept**: AI creates immersive entertainment experiences

| No. | Application Scenario Name | Application Scenario Function |
| :--: | ------------ | ------------ |
| 1 | Game NPC Autonomous Decision Engine | LLM-driven NPCs with autonomous decision-making capabilities |
| 2 | Script Murder Story Deduction | AI generates story branches based on player choices |
| 3 | Interactive Novel Story Generator | Reader choices affect story development |
| 4 | Esports Game Analysis & Commentary | Real-time game analysis with AI-powered commentary |
| 5 | Audiobook Auto-Generation | Converts text to audio with character-specific voices |
| 6 | Personalized Humor Content Recommendation Algorithm Engine | Builds user-interest profiles and recommends matching humor content |
| 7 | AI Smart Vocal Tuning and KTV Voice Enhancement Software | Performs denoising and vocal enhancement with AI tuning algorithms |
| 8 | Film/TV Character-Centric Plot Extraction and Editing Tool | Analyzes video content, extracts character-related clips, and auto-generates edited cuts |
| 9 | Multi-Role TTS Audiobook Auto-Generation System | Assigns text roles and generates personalized voices with background music/effects |
| 10 | Board-Game Reinforcement-Learning Review Coach | Analyzes game records, simulates AI opponents, and generates review suggestions |

---

## 15. Ecommerce Services

> 💡 **Core Concept**: AI empowers ecommerce to achieve intelligent operations

| No. | Application Scenario Name | Application Scenario Function |
| :--: | ------------ | ------------ |
| 1 | Product Detail Page Generator | Generates high-converting product descriptions and marketing copy |
| 2 | Virtual Try-On | AI generates virtual model try-on effects |
| 3 | Multi-Language Translation | Localizes product descriptions for international markets |
| 4 | Digital Human Live Streaming | AI-powered virtual streamers for 24/7 live commerce |
| 5 | Trend Analysis & Product Selection | Analyzes market trends; suggests trending products to sell |
| 6 | Full-Network Same-Product AI Price Comparison and Trend Prediction Plugin | Crawls e-commerce prices, displays comparison charts, and predicts price trends |
| 7 | Buyer-Show Image AI Selection and Short-Video Synthesis Platform | Scores buyer-show images, auto-recommends high-quality content, and synthesizes short videos from templates |
| 8 | LLM-Based Real-Time Sales Dialogue Voice Analysis and Golden-Script Recommendation | ASR transcribes calls and performs real-time script compliance checks with recommendation output |
| 9 | Market Trend AI Insight and Best-Seller Prediction Engine | Collects and analyzes social media and e-commerce data; LLM identifies trend hotspots and recommends product choices |
| 10 | Private-Domain User Profiling AI Clustering and Precision Operations System | Clusters user behavior data, generates profile tags, and triggers automated marketing flows |

---

## 16. Energy

> 💡 **Core Concept**: AI empowers energy management for intelligent grid operations

| No. | Application Scenario Name | Application Scenario Function |
| :--: | ------------ | ------------ |
| 1 | Home Energy Analysis | Analyzes household electricity usage patterns; provides energy-saving suggestions |
| 2 | Solar Panel Defect Detection | Drone-captured images analyzed by CV for defect identification |
| 3 | Electricity Price Prediction | ML predicts spot prices; generates trading strategies |
| 4 | Carbon Emission Calculation | Auto-calculates enterprise carbon footprint; generates ESG reports |
| 5 | Grid Load Prediction | Predicts grid load under extreme weather; generates dispatch plans |
| 6 | Gas-Station Violation AI Video Recognition and Alert Guard | Analyzes surveillance video and detects violations (calling/smoking, etc.) with alert pushes |
| 7 | Long-Distance Oil/Gas Pipeline Leak Acoustic AI Monitoring and Precision Positioning System | Collects acoustic-sensor data for leak detection and localization algorithms |
| 8 | Virtual Power Plant Resource Aggregation and AI Power-Trading Decision System | Connects distributed resources for aggregated optimization dispatch and strategy execution |
| 9 | Mine Personnel AI Position Tracking and Dangerous-Area Intrusion Alarm | Uses UWB/Bluetooth positioning for trajectory tracking and geofenced danger-zone alerts |
| 10 | Energy-Storage Battery Health AI Assessment and Thermal-Runaway Warning | Monitors battery runtime data, evaluates health status, and triggers thermal-risk alerts |

---

## 17. Audio & Video

> 💡 **Core Concept**: AI empowers audio/video production for efficient content creation

| No. | Application Scenario Name | Application Scenario Function |
| :--: | ------------ | ------------ |
| 1 | Video Highlight Detection | AI identifies highlights from long videos; auto-generates short clips |
| 2 | Audio Noise Reduction | Separates vocals from background noise; enhances audio quality |
| 3 | Video Restoration & Colorization | 4K super-resolution; AI adds color to black and white footage |
| 4 | Text-to-Speech with Emotion | Generates natural-sounding speech with emotional expression |
| 5 | Meeting Transcription | Multi-speaker voice separation; generates meeting transcripts with action items |
| 6 | Video Object Removal AI Engine | Uses object tracking and inpainting to remove unwanted objects with frame-level consistency |
| 7 | Copyright-Safe Background Music AIGC Auto-Composer | Uses music-generation models with controllable emotional style and copyright checks |
| 8 | Specific-Person Voice Clone and Voice Conversion Software | Trains timbre models from small voice samples and supports voice conversion |
| 9 | One-Click Script-to-Storyboard and AI Dynamic Preview Video Platform | Parses scripts into storyboards and auto-generates previsualization videos |
| 10 | Meeting Recording AI Smart Transcription and Core To-Do Extraction Assistant | Performs multi-speaker transcription and LLM-based to-do extraction with timestamps |

---

## 18. AI Marketing

> 💡 **Core Concept**: AI empowers marketing to achieve data-driven creative campaigns

| No. | Application Scenario Name | Application Scenario Function |
| :--: | ------------ | ------------ |
| 1 | Social Media Viral Copy Generator | Generates Xiaohongshu-style posts with optimized emojis |
| 2 | Marketing Poster Designer | AI designs posters with multi-size adaptation |
| 3 | Logo & Brand Design | Generates brand logos; creates complete VI systems |
| 4 | Trend Analysis & Content Ideas | Tracks trending topics; suggests marketing angles |
| 5 | Video Script Generator | Generates short video scripts with shooting suggestions |
| 6 | Competitor Marketing Strategy Deep Analysis and AI Weekly Report Generator | Collects/analyzes competitor content, extracts strategy insights, and auto-generates weekly reports |
| 7 | Search-Engine Keyword AI Layout and Traffic Article Batch Writing | Analyzes keywords, generates articles at scale, and gives SEO optimization recommendations |
| 8 | Personalized Marketing Email AI Writing Expert | Uses user-profile data for personalized content generation with A/B testing |
| 9 | Brand Reputation Full-Network Monitoring and Crisis AI Alert Radar | Collects network sentiment data, runs sentiment analysis, and pushes crisis alerts |
| 10 | Short-Video Script Creative AIGC Generation and Storyboard Guidance Assistant | Inputs themes and outputs scripts, storyboards, and practical shooting guidance |

---

## 19. Data Intelligence

> 💡 **Core Concept**: AI makes data accessible to everyone through natural language

| No. | Application Scenario Name | Application Scenario Function |
| :--: | ------------ | ------------ |
| 1 | Natural Language to SQL | Converts natural language queries to SQL statements |
| 2 | Data Asset Catalog | Auto-catalogs and classifies enterprise data assets |
| 3 | Data Quality Monitoring | Detects data anomalies; suggests fixes |
| 4 | Report Generator | Creates reports and dashboards through conversation |
| 5 | Metric Q&A Assistant | Answers questions about data metric definitions and calculations |
| 6 | Intelligent Data-Report Interpretation and Trend Analysis Assistant | Upload report images or input data; VLM interprets chart content and analyzes trends |
| 7 | Intelligent DB-Schema Interpretation and Query-Example Generation Assistant | Input table names or field descriptions; LLM generates schema explanations and sample SQL |
| 8 | Enterprise Master-Data Intelligent Alignment and AI Dedup Governance | Matches master data across sources, identifies duplicates, and supports merge-rule configuration |
| 9 | Data Requirement Doc to Test-Case Intelligent Conversion Tool | Input data requirement descriptions; LLM generates test scenarios and validation test cases |
| 10 | Data Metric-Definition Intelligent Q&A Assistant | Builds a knowledge base from metric-definition docs; LLM answers definition and calculation logic questions |

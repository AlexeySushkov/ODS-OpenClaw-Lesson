# Семинар провел Алексей Сушков - главный специалист Nexign
Алексей Сушков - главный специалист Nexign, участвует в развитии сообщества ИИ, реализует собственные pet-проекты, делится результатами исследований в [**статьях на Habr**](https://habr.com/ru/users/AlexeySushkov/)

![Nexign](https://github.com/AlexeySushkov/ODS-OpenClaw-Lesson/blob/main/images/Nexign2.png)

# OpenClaw: личный ИИ-агент в рабочей среде
## План семинара
- Что такое OpenClaw: архитектура и принципы
- Разворачиваем опенсорсный агент, который живёт в ваших мессенджерах и реально делает дела
- Установка и онбординг: свой агент за вечер
- Подключение к рабочим инструментам: Telegram, поиск, почта, календарь
- Skills: используем готовые навыки из ClawHub и пишем собственные
- Безопасность и оптимизация: какие права давать агенту и как уменьшить потребление токенов
- Практика: агент, который разбирает дела и осуществляет поиск информации (дайджест новостей по утрам)
- Лайфхаки, полезные команды, сравнение n8n и OpenClaw: [**материалы семинара**](https://github.com/AlexeySushkov/ODS-OpenClaw-Lesson)

# Кейсы 
- Morning briefing (новости, задачи, погода, письма)
- Диспетчер входящих задач
- Авто-календарь и напоминания
- Умная сортировка почты
- Заказ еды по рецептам
- Транскрибация + git commit
- Бот для поддержки: кодинг + фиксы по репортам пользователей
- Ищет тренды AI, генерит идею, делает код, пушит в git, присылает ссылку - каждый день!
- Поиск по персональной информации

Кейс для семинара: реализация Morning briefing, который состоит из 3 частей: 
- Задачи - интеграция с Google календарем
- Новости - интеграция с Интернетом
- Шутка дня - реализация custom skill

![Screenshot](https://github.com/AlexeySushkov/ODS-OpenClaw-Lesson/blob/main/images/tg1.png)  
  
# Архитектура 
Моя статья про теорию AI-агентов [**Сделай бота для работы**](https://habr.com/ru/articles/979830/)
## Описание схемы 
- Channels / Управление / Scheduling - мессенджеры, почта, календарь, веб-интерфейс, cron-задачи
- OpenClaw Gateway - приём и нормализация сообщений, Webhook-парсер, аутентификация, rate-limiting, очередь событий
- LLM - мозг системы, генерация решений, реализация Event loop, AI-роутер, сбор контекста, управление сессиями
- Skill Engine - исполнение действий, реестр навыков, синхронизация с ClawHub
- Memory / конфиги / Хранилище - хранение состояния, сессии, логи/задачи, кэш
- Actions / Tools - внешние интеграции, MCP, облака, сторонние API

![Схема](https://github.com/AlexeySushkov/ODS-OpenClaw-Lesson/blob/main/images/OpenClaw2.png)


# Установка 
## Предусловия
- Telegram BotFather 
- Google API Keys
- Tavily - поиск в интернете 
- Платная подписка на LLM или бесплатная с лимитами, или локальная модель
  - Huggingface: https://huggingface.co/
  - Openrouter: https://openrouter.ai/ 
- Где устанавливать
  - Свой ноут с отдельной VM (VirtualBox) - ограничения по безопасности и работе 24/7
  - VPS (Virtual Private Server) - платный, минимальная конфигурация: 2 ядра CPU, 4 ГБ RAM, 40 ГБ HDD

## Инструкции 
- [**Selectel: OpenClaw: установка и первые впечатления**](https://habr.com/ru/companies/selectel/articles/1009278/)
- Видео с обзором установки и кейсами из официальной документации OpenClaw: 
  - [**ClawdBot (OpenClaw): The self-hosted AI that Siri should have been (Full setup)**](https://www.youtube.com/watch?v=SaWSPZoPX34)
  - [**OpenClaw (Clawdbot) use cases: 9 automations + 4 wild builds that actually work**](https://www.youtube.com/watch?v=52kOmSQGt_E)

## Пошаговое руководство по установке OpenClaw на чистый Ubuntu
### Базовая настройка Ubuntu
- Обновление системы
- Создание отдельного пользователя openclaw (никогда не используйте root)
- Настройка файрвола (локально или облачного) - открыть только нужные порты 
- Настройка SSH-доступа по ключам, отключить вход по паролю

### Установка OpenClaw
- Переключитесь на пользователя openclaw
- Установите пакетный менеджер Homebrew для установки скиллов
  - используйте apt для установки системных зависимостей и ядра системы
  - brew - для пользовательских приложений и утилит, которых нет в официальных репозиториях
- Установите Node.js 22+
- Установите OpenClaw, используйте рекомендованный скрипт или npm
- Запустите мастер настройки:
  - Выбор AI-провайдера и ввод API-ключа
  - Настройка каналов связи (Telegram)
  - Установка сервиса systemd для автозапуска
  - Настройка Skills: Gmail, календарь, поиск в Интернете (можно потом)

### После установки OpenClaw
- Установите Nerve (если надо управлять несколькими агентами)
- Спаривание устройств 
- Пробросьте (форвард) порты
- Пропишите в UI токен Gateway из openclaw.json

### Вызов UI
- Openclaw dashboard: http://localhost:18789/ 
- Nerve dashboard: http://localhost:3080/

# Настройка 
## Настройка личности агента
- При первом запуске агента (BOOTSTRAP) заполняются файлы:
  - BOOTSTRAP.md - запускает процесс (удаляется)
  - IDENTITY.md - заполняется: имя, стиль, emoji
  - USER.md - заполняется: как обращаться, таймзона
  - SOUL.md - заполняется: границы, тон общения
  - AGENTS.md - заполняется: правила работы
После этого - это ваш личный агент, который знает себя, знает вас и знает, как работать.

## Настройка Heartbeat
- Посмотреть последний:
```bash
openclaw system heartbeat last
```
- Отключить временно
```bash
openclaw system heartbeat disable
```
- Отключить постоянно:
  - Сконфигурировать Heartbeat (~/.openclaw/openclaw.json)
  - Проверить валидность конфига
```bash
python3 -m json.tool ~/.openclaw/openclaw.json > /dev/null && echo "JSON OK"
```
   - Перегружаем 
```bash
openclaw gateway restart
```
  - Посмотреть логи — не должно быть новых записей "heartbeat"
```bash
openclaw logs 2>&1 | grep -i heartbeat | tail -10
```
  - Подождать ~35 минут и убедиться, что новых запусков нет

## Устанавка аватара 
Аватар - это аватар Агента, а не мой (User)
- Загружаем png
```bash
~/.openclaw/workspace/avatars
```
- Редактируем IDENTITY.MD
- Перегружаем 
```bash
openclaw gateway restart
```
- Запускаем новый чат 

## LLM меняем модель
модель в 
```bash
~/.openclaw/openclaw.json
```
Было:
```json
"model": {
  "primary": "openrouter/auto"
},
"models": {
  "openrouter/auto": {
    "alias": "OpenRouter"
  }
}
```

Стало (пример с Haiku 4.5):
```json
"model": {
  "primary": "openrouter/anthropic/claude-haiku-4.5"
},
"models": {
  "openrouter/anthropic/claude-haiku-4.5": {
    "alias": "Haiku 4.5"
  }
}
```
Опционально) Добавьте резервную модель

Чтобы система не ломалась, если основная модель недоступна:

```json
"model": {
  "primary": "openrouter/anthropic/claude-haiku-4.5",
  "fallbacks": [
    "openrouter/google/gemini-2.0-flash",
    "openrouter/deepseek/deepseek-chat"
  ]
},
"models": {
  "openrouter/anthropic/claude-haiku-4.5": { "alias": "Haiku 4.5" },
  "openrouter/google/gemini-2.0-flash": { "alias": "Gemini Flash" },
  "openrouter/deepseek/deepseek-chat": { "alias": "DeepSeek" }
}
```

Сохраните и проверка

Проверка синтаксиса (опционально)
```bash
python3 -m json.tool ~/.openclaw/openclaw.json > /dev/null && echo "✓ JSON OK"
```

# Перезагрузите конфиг 
```bash
openclaw gateway restart
```

Проверка: работает ли новая модель?

Задать вопрос  Какую модель ты используешь сейчас? Назови провайдера и точное название модели.
Отправьте тестовый запрос: openclaw ask "Какая сегодня дата? Ответь кратко." Посмотрите логи — должна быть запись с новой моделью

```bash
openclaw logs 2>&1 | grep -i "model\|haiku\|gemini" | tail -10
```

 Проверьте расходы в дашборде OpenRouter https://openrouter.ai/activity

# Skills 
Устанавливаем skill gog - Google Workspace CLI для Gmail, Calendar, Drive, Contacts, Sheets, Docs

## установи skill gog
```bash
brew install steipete/tap/gogcli
```

## Генерация client_secret.json
Этот файл нельзя скачать готовым - его нужно сгенерировать самостоятельно в Google Cloud Console специально для gog. Следуйте этой инструкции:
- Перейдите в Google Cloud Console и авторизуйтесь под своей учетной записью Google.
- Откройте страницу: Google Cloud Console.
- Создайте новый проект (рекомендуется).
- Включите необходимые API. В боковом меню перейдите в раздел «API и сервисы» > «Библиотека». В строке поиска найдите и поочередно включите (Включить) все API, которые планируете использовать. Минимальный набор для gog auth add:
  - Gmail API
  - Google Calendar API
  - Google Drive API
  - People API (для контактов)
- Создайте учетные данные (OAuth Client ID).
  - В боковом меню перейдите в раздел «API и сервисы» > «Учетные данные».
  - Нажмите синюю кнопку «+ Создать учетные данные» и выберите «OAuth client ID».
  - В открывшейся форме:
    - Тип приложения: Выберите «Десктопное приложение» (Desktop app) . Это важно, так как gog запускается на вашем компьютере.
    - Имя: Введите любое понятное имя, например, Gog CLI on my Ubuntu.
    - Остальные поля можно оставить пустыми.
  - Нажмите кнопку «Создать».
- Скачайте файл с учетными данными.
  - Сразу после создания появится всплывающее окно с вашим Client ID и Client Secret.
  - Нажмите синюю кнопку «Скачать JSON» (Download JSON).
  - Этот скачанный файл и есть ваш client_secret.json. Он будет иметь имя вида client_secret_ваш-id.apps.googleusercontent.com.json. Для простоты вы можете переименовать его в client_secret.json.

## Куда положить client_secret.json
- Рекомендуемый вариант - cохраните файл в папку для конфигураций ~/.config/gogcli/.
- Важно: Чтобы gog распознал файл в будущем без указания пути, его следует переименовать в credentials.json и положить в папку ~/.config/gogcli/


## Как использовать с gog
Теперь его можете передать в команду gog auth credentials. Вы можете использовать абсолютный или относительный путь к файлу.
```bash
gog auth credentials ~/.config/gogcli/credentials.json
```
## Настройка в OpenClaw

- После успешного выполнения этой команды можно добавить ваш аккаунт. Обратите внимание на  --manual
```bash
gog auth add Alexey.P.Sushkov@gmail.com --services gmail,calendar,drive,contacts,sheets,docs --manual
```
- Проверить, что все прошло успешно, можно командой:
```bash
gog auth list
```
- Вы должны увидеть ваш email в списке авторизованных аккаунтов.
- Проверка календаря 
```bash
gog calendar events e5b2dxxxxxxxxxxxxxxxxxxxxxxxxxxxxx4c690f@group.calendar.google.com  --from 2026-04-01 --to 2026-04-30
```
- Поскольку вы работаете на VPS и выполняете команды из скриптов/автоматизации, лучше переключиться на файловое хранилище. Это полностью убирает необходимость в паролях и безопаснее, чем хранить пароль в открытом виде в ~/.bashrc.
- После переключения на файловое хранилище команда gog calendar events будет работать без любых переменных окружения и без запроса пароля.
- При дальнейшей работе учесть, что невозможно выборочно отозвать доступ для одного API, оставив другой, в рамках одного OAuth 2.0 Client ID. Отзыв токена всегда аннулирует все разрешения, выданные пользователем для этого Client ID.
   
# Сustom skill
Способы
- "Пожалуйста, сделай мне навык summarize через ClawHub"
- Через файлы

# Безопасность  

## Инфраструктура и сеть
- Отдельный user
- Отключены неиспользуемые порты
- SSH только по ключам

## Аутентификация и управление доступом
- Доступ к админ панелям только по localhost
- Принцип наименьших привилегий: каждый skill получает только нужные scopes

## Секреты и конфигурация
- Пароли / API токены не хранятся в конфигах, а в .env или парольных менеджерах, секьюрных хранилищах

## Защита AI-агентов
- Ограничения на токены 
- max retries: 3
- timeout: 10 мин
- Approval workflow: для деструктивных действий (удаление, массовая рассылка)
- Проверяйте код навыков из ClawHub перед установкой — зафиксированы случаи вредоносных пакетов
  
## Логирование, мониторинг и реагирование
- Централизованный сбор
- Исключать конфиденциальную и персональную информацию
- Смотреть каждый вечер Dashboards: latency, token cost, tool success rate, error rate, queue depth

# Лайфхаки 
- Замена на openrouter/auto на claude-haiku-4.5 - экономия до 80%.
- Использовать мощную модель для размышления, и дешевые для выполнения
- Продукт сырой, документация не полная, неопределенность где что делать 
- Новый чат в Telegram начинать после изменений конфига 

# Полезные команды

Версия должно быть  больше v2026.3.24+
```bash
openclaw --version
```
Перезагрузка gateway
```bash
openclaw gateway restart
```
```bash
pkill -f "openclaw.*gateway" && sleep 2 && openclaw gateway start &
```
Проверить валидность конфига
```bash
python3 -m json.tool ~/.openclaw/openclaw.json > /dev/null && echo "JSON OK"
```
Отключить heartbeat 
аудит безопасности
```bash
openclaw security audit
```
```bash
openclaw security audit --deep
```
Применить авто-исправления (осторожно!)
```bash
openclaw security audit --fix
```

# Итоги 
- OpenClaw запущен и доступен по localhost
- Подключены каналы (TG + Gmail/Calendar)
- Установлен skill из ClawHub (gog, tavily) + написан кастомный (шутки, summarize)
- Агент читает события в календаре, ищет новости и и отправляет в TG

# Сравнение n8n и OpenClaw
OpenClaw - пожиратель токенов номер 1 в мире: https://openrouter.ai/rankings 

|  | **n8n** | **OpenClaw** |
| :---: | :---: | :---: |
| **Принцип работы** | Четкий, визуальный сценарий (Workflow).| Задача на естественном языке. AI-агент сам решает, какие инструменты (скиллы) использовать и в какой последовательности, какие LLM использовать |
| **Надежность и предсказуемость** | Высокая | Вероятностная |
| **Уровень автономии** | 1-3 | 3-4 |
| **Безопасность** | Изоляция workflow, только необходимые права | Максимальные права (бета-проект для хобби) |
| **Область применения** | Детерминированные рабочие процессы.<br><br>Системы интеграции и автоматизации.<br><br>Предсказуемые AI-агенты | Персональный ассистент |
| **LLM** | Применение LLM точечно при необходимости | LLM – основа, обязательная часть |
| **Open Source & Self-Hosting** | Да | Да |
| **Расширяемость** | Ноды (nodes) | Скиллы (skills) |
| **Цена** | Предсказуемая по запросу | Наоборот |


## Если использовать OpenClaw, то необходимо осознанно принимать риски! Визуализация
![n8n vs OpenClaw](https://github.com/AlexeySushkov/ODS-OpenClaw-Lesson/blob/main/images/n8n%20vs%20OpenClaw.png) 


# Запись стрима (необработанная)

- https://www.youtube.com/live/ymkvLJtAKEI
- https://vkvideo.ru/video-229052741_456239051?list=ln-HdUc8gB8NH50rOSJjN
  



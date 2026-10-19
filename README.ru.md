<div align="center">

```
██╗██████╗ ██╗   ██╗ ██████╗ ██╗  ██╗███████╗
██║██╔══██╗██║   ██║██╔═══██╗██║ ██╔╝██╔════╝
██║██████╔╝██║   ██║██║   ██║█████╔╝ █████╗
██║██╔══██╗██║   ██║██║   ██║██╔═██╗ ██╔══╝
██║██║  ██║╚██████╔╝╚██████╔╝██║  ██╗███████╗
╚═╝╚═╝  ╚═╝ ╚═════╝  ╚═════╝ ╚═╝  ╚═╝╚══════╝
```

# iruoke-bulk-apikey

**Массовое создание аккаунтов Iruoke с автоматически сгенерированными API-ключами — инференс, совместимый с OpenAI, + коннектор 9Router.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-8%20served-412991?style=for-the-badge)](https://api.iruoke.top/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Быстрый старт](#-quick-start) · [🎁 Бесплатный тариф](#-free-tier--how-much-you-get) · [🤖 Модели](#-models--all-8-of-them) · [🧪 Использование API](#-how-to-use-the-api) · [✅ Проверено](#-verified-working--every-key-is-tested) · [🧭 Как это работает](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Опции](#️-cli-options) · [🛰️ Прокси](#️-behind-a-proxy) · [🐞 Проблемы](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Зачем это нужно

Iruoke — это панель New-API с той же **«nofield»-дверью**, что и у её собратьев: `/api/user/register` принимает имя пользователя и пароль, **без почтового ящика, без капчи**, а токен аккаунта создаётся с `unlimited_quota: true`. Что делает эту панель достойной репозитория — это полоса, которую она отдаёт бесплатно: **`longcat-2.5-preview-free`** отвечает на каждом свежем аккаунте, в обычной группе `default` — привязка к группе не нужна. Единица масштабирования — *один аккаунт на ключ*, и каждый ключ в `keys.txt` был подтверждён живым вызовом чата: **58 ключей** во флоте оператора (44 из первой волны двери, ещё 14 созданы на скорости **5.4 с/ключ, 14/14 живых** в масштабном прогоне, который вывел пул за порог).

## 🎁 Бесплатный тариф — сколько вы получаете

**Без почтового ящика, без капчи** — аккаунт и есть бесплатная вещь, а токен создаётся с `unlimited_quota: true`.

| Что | Значение |
| --- | --- |
| Требуется карта | **Нет** |
| Капча / Turnstile | **нет** |
| Почтовый ящик | **не используется** — вызов регистрации отправляет только `{username, password}` |
| Бесплатная полоса | `longcat-2.5-preview-free` в группе `default`, привязка к группе не требуется |
| Токен | `unlimited_quota: true`, `expired_time: -1` |
| Единица масштабирования | **один аккаунт = один ключ** → `--count N` означает «N аккаунтов» |

> 💡 Бесплатный тариф устанавливается Iruoke и может измениться. Проверяйте актуальные цифры на [api.iruoke.top](https://api.iruoke.top) — и пусть собственный шлюз инструмента решает:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Модели — все 8

Панель оценивает **8 идентификаторов, и у каждого есть включённая группа** (`GET /api/pricing`). Перечислены только те идентификаторы, которые ответили живым **200** с реальным ключом:

| ID модели | ✓ | Группа | Примечания |
| --- | --- | --- | --- |
| `longcat-2.5-preview-free` | ✓ | `default` | бесплатная полоса — самый дешёвый идентификатор, поэтому шлюз останавливается здесь на каждом аккаунте |

Идентификаторы, которые панель предлагает в `default` и которые этот рельс **не** прошёл через шлюз: `mimo-v2.6-flash`, `deepseek-v4.1-flash`. Они остаются в резервном порядке шлюза и вне проверенной таблицы.

## 🧪 Как использовать API

```bash
curl https://api.iruoke.top/v1/chat/completions \
  -H "Authorization: Bearer <your key>" \
  -H "Content-Type: application/json" \
  -d '{"model":"longcat-2.5-preview-free","messages":[{"role":"user","content":"hi"}],"max_tokens":16}'
```

## 🛠 Быстрый старт

```bash
# clone or fork this repository, then:
cd iruoke-bulk-apikey

# 1) your proxy list, one per line (http://… or socks5://…)
printf 'http://127.0.0.1:8080\n' > proxies.txt

# 2) go
node src/launcher.js              # interactive
# or directly:
node src/cli.js --count 50 --conc 2
```

`keys.txt` записывается **только** после того, как живое завершение чата подтвердит ключ.

| Режим | Что делает |
| --- | --- |
| `run` (по умолчанию) | создаёт аккаунты до `--count`, проверяет каждый ключ живым чатом |
| `--verify-only` | повторно проходит по `keys.txt`, перемещая всё, что не прошло, в `keys-ungated.txt` |
| `--screen` | тестирует ваш список прокси против панели → `proxies.live.txt` |
| `--probe` | проверка окружения: панель, цены, порядок шлюза, выходы |
| `--collect` | пересобирает `keys.txt` из `accounts.json` (каждая строка с `chat_status: 200`) |

### 🖱️ Лаунчер

Лаунчер запрашивает список прокси, количество и модель, запоминает их в `launcher.config.json` и запускает CLI с нужными флагами.

## ✅ Проверено в работе — каждый ключ протестирован

| Проверка | Результат |
| --- | --- |
| `GET /api/status` | **200** — регистрация открыта, без капчи, без шага с почтой |
| `GET /api/pricing` | **200** — 8 оценённых идентификаторов, все с включённой группой; самый дешёвый — `longcat-2.5-preview-free` |
| `POST /api/user/register` | **200** `{username,password}` — без поля почтового ящика (5xx повторяется, затем повторяется с полем e-mail) |
| `POST /api/user/login` | **200** — `access_token` (cookie + `New-Api-User` как запасной вариант для старых форков) |
| `GET /api/user/self` | **200** — чтение квоты для записи аккаунта |
| `POST /api/token/` → `POST /api/token/<id>/key` | токен создан (`unlimited_quota: true`), открытый ключ раскрыт |
| `POST /v1/chat/completions` — выполняется для **каждого** ключа перед записью | **200** с реальным содержимым (`choices`, без `error`) |
| ключ, не прошедший проверку чата | никогда не записывается в `keys.txt` |

Измерено во флоте оператора (рельс двери, 2026-10-19): **14/14 попыток живых**, всего 76 с при 3 воркерах → **5.4 с/ключ**, каждый ключ проверен на `longcat-2.5-preview-free`.

## 🧭 Как это работает

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Iruoke page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.iruoke.top/v1/chat/completions → real content = the key is live
```

| Шаг | Вызов | Примечания |
| --- | --- | --- |
| регистрация | `POST /api/user/register {username,password}` | без поля e-mail; 5xx повторяется через 2.5 с, затем вызов повторяется с адресом |
| вход | `POST /api/user/login {username,password}` | `access_token`, плюс cookie сессии для форков, которые возвращают только cookie |
| аккаунт | `GET /api/user/self` | id + квота |
| цены | `GET /api/pricing` | самая дешёвая чат-модель и её `enable_groups[0]` |
| ключ | `POST /api/token/ {group}` → `POST /api/token/<id>/key` | `unlimited_quota: true` |
| проверка | `POST /v1/chat/completions` | требуется живой 200 перед `keys.txt`; 429 — сигнал темпа (подождать 2.5 с, повторить ту же модель) |

## ⚙️ Опции CLI

```bash
node src/cli.js [mode] [flags]
```

| Флаг | По умолчанию | Значение |
| --- | --- | --- |
| `--count N` | 10 | аккаунтов (= ключей) для создания |
| `--conc N` | 2 | аккаунтов в работе (макс. 16) — один выход закрепляется за воркером |
| `--host <name>` | `api.iruoke.top` | хост панели (тот же рельс работает на зеркалах этой панели) |
| `--model <id>` | самый дешёвый из оценённых | закрепить шлюз за одним ID модели |
| `--group <name>` | группа модели | закрепить группу токена |
| `--pool <file>` | `proxies.txt` | список прокси (также работает переменная окружения `IRUOKE_PROXIES`) |
| `--gap MS` | 800 | пауза между моделями внутри одного аккаунта |
| `--tries N` | 2 | выходов пробуется на аккаунт |

Стартовая проверка проходит по началу пула, пока какой-нибудь выход не ответит панели, так что мёртвая первая строка не останавливает прогон.

## 🛰️ За прокси

Каждый запрос уходит через выход из вашего пула — инструмент никогда не обращается к панели напрямую, и **один аккаунт живёт на одном выходе** всю свою жизнь (панель привязывает сессию к IP). Измерено: **9 выходов** в пуле оператора могли достичь этого хоста во время масштабного прогона, и создание шло при conc 2-3 без единого 503.

## 📄 Файлы

| Путь | Содержимое |
| --- | --- |
| `keys.txt` | ключи с chat-200, по одному в строке — игнорируется git |
| `keys-ungated.txt` | ключи, не прошедшие последний прогон `--verify-only` — повторите позже |
| `accounts.json` | имя пользователя / квота / группа / модель / статус чата для каждого аккаунта — игнорируется git |
| `proxies.txt` | ваш список выходов — игнорируется git |

## 🔌 Подключение к 9Router

[9Router](https://9router.com) — это самостоятельно размещаемый роутер, совместимый с OpenAI. Одна команда превращает `keys.txt` в провайдер-узел с рабочими комбинациями:

```bash
node src/connect-9router.js
```

По умолчанию: `--base http://localhost:20128`, `--password 123456`, `--name "Iruoke"`, `--prefix iruoke`, `--base-url https://api.iruoke.top/v1`.

## 🔑 Что даёт один ключ

| | |
| --- | --- |
| 🏢 Провайдер | **Iruoke** — [api.iruoke.top](https://api.iruoke.top) |
| 🎁 Что даёт провайдер | один аккаунт на ключ — регистрация без почтового ящика, бесплатная полоса longcat, каждый ключ проверен чатом |
| 🔑 Что даёт один ключ | один API-ключ, привязанный к своему аккаунту: 8 идентификаторов моделей на указанном ниже эндпоинте, в пределах лимитов вашего тарифа. Квота живёт на **аккаунте**, и именно поэтому этот инструмент создаёт один аккаунт на ключ |
| 🌐 Форма API | Совместим с OpenAI: `POST https://api.iruoke.top/v1/chat/completions` |
| 🔒 Ваши данные | связь только с api.iruoke.top и настроенным вами бэкендом почтового ящика; ключи остаются на вашей машине |

## 🐞 Проблемы

Если панель закроет свою дверь, уберёт бесплатную полосу или изменит карту цен, инструмент остановится на этом шаге и сообщит об этом (`--probe` покажет, что он видит). **[Откройте issue](../../issues)** с командой, которую вы запустили, и точным выводом; PR приветствуются.

## 📄 Лицензия

MIT © 2026 — см. [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 iruoke-bulk-apikey contributors

</div>
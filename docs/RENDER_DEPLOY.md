# Деплой бота на Render.com (Docker)

## Что понадобится

- Репозиторий на GitHub или GitLab (код бота уже там)
- Аккаунт на [Render.com](https://render.com)
- Токен бота от @BotFather

---

## Шаг 1. PostgreSQL на Render

1. Зайдите в [Render Dashboard](https://dashboard.render.com/) → **New** → **PostgreSQL**.
2. Задайте имя (например, `wordly-db`), регион, план (например, Free).
3. Нажмите **Create Database**.
4. После создания откройте базу и скопируйте **Internal Database URL** (для сервисов в одном аккаунте) или **External Database URL** (если будете подключаться извне).  
   Формат: `postgresql://user:password@host:port/database`  
   Эта строка понадобится как `DB_URL` для бота.

---

## Шаг 2. Web Service для бота (Docker)

1. В Dashboard: **New** → **Web Service**.
2. Подключите репозиторий (GitHub/GitLab), выберите репозиторий с ботом.
3. Настройки:
   - **Name** — например, `wordly-bot`.
   - **Region** — тот же, что у базы (лучше для Internal URL).
   - **Root Directory** — укажите `bot`.  
     (Render будет собирать образ из папки `bot/`, где лежит Dockerfile и `src/`.)
   - **Runtime** — **Docker** (в списке выберите Docker).
   - **Dockerfile Path** — оставьте пустым или укажите `Dockerfile` (он ищется относительно Root Directory, т.е. в `bot/`).
4. **Environment** — добавьте переменные:
   - `TOKEN` — токен бота от @BotFather.
   - `DB_URL` — вставьте скопированный **Internal Database URL** (или External) из шага 1.
5. План (Free/Starter и т.д.) — выберите по необходимости.
6. Нажмите **Create Web Service**.

Render соберёт образ по `bot/Dockerfile` и запустит контейнер. Бот будет подключаться к Telegram и к PostgreSQL по `DB_URL`.

---

## Шаг 3. Проверка

- В логах сервиса в Render не должно быть ошибок подключения к БД и к Telegram.
- Напишите боту в Telegram команду `/start` — должен прийти ответ.

---

## Важно

- **Секреты:** `TOKEN` и `DB_URL` задавайте только в Environment в Render, не коммитьте их в репозиторий.
- **Internal vs External URL:** для сервиса бота на Render лучше использовать **Internal Database URL** (доступ по внутренней сети, быстрее и без лишнего расхода на трафик).
- **Free tier:** на бесплатном плане сервис может «засыпать» после неактивности; при необходимости выберите платный план, чтобы бот работал без остановок.
- Если в логах видно ошибки по подключению к БД — проверьте, что `DB_URL` скопирован полностью (с паролем и без лишних пробелов).

---

## Краткий чеклист

1. Создать PostgreSQL на Render, скопировать Internal Database URL.
2. Создать Web Service → привязать репозиторий.
3. Root Directory: `bot`, Runtime: Docker.
4. Добавить env: `TOKEN`, `DB_URL`.
5. Создать сервис и дождаться деплоя.
6. Проверить бота в Telegram.

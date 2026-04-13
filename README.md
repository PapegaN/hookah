# hookah

Главный репозиторий проекта управления кальянной. Здесь живут orchestration-слой, общая документация и git-ссылки на прикладные репозитории:

- `hookah-web`
- `hookah-api`
- `hookah-db`

## Что внутри

- `compose.yaml` — общий запуск всей системы в Docker.
- `AGENTS.md` — правила работы с агентом и коммитами.
- `ARCHITECTURE.md` — архитектурный каркас проекта.
- сабмодули `hookah-web`, `hookah-api`, `hookah-db` — отдельные репозитории приложения.

## Запуск всей системы

```bash
cp .env.example .env
docker compose up --build
```

После старта будут доступны:

- frontend: `http://localhost:8080`
- backend health: `http://localhost:3000/api/v1/health`
- swagger: `http://localhost:3000/api/docs`
- postgres: `localhost:5432`

## Что важно сейчас

- `api` работает поверх реальной PostgreSQL-схемы, а не in-memory demo store.
- В `compose` есть сервис `db-migrate`, который применяет миграции и сиды при каждом запуске стека даже на существующем volume.
- В админке есть страница настроек с экспортом справочников, пользователей, заказов и полным backup/import.

## Запуск частей по отдельности

```bash
docker compose up --build db
docker compose up --build api
docker compose up --build web
```

## Работа с сабмодулями

```bash
git submodule update --init --recursive
```

Рабочий цикл:

1. Сделать коммит внутри нужного сабмодуля.
2. Вернуться в корень проекта.
3. Зафиксировать обновлённый gitlink сабмодуля в главном репозитории.
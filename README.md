# hookah

Главный репозиторий проекта управления кальянной. Здесь живут orchestration-слой, общая документация и git-ссылки на прикладные репозитории:

- `hookah-web`
- `hookah-api`
- `hookah-db`

## Структура

- `compose.yaml` — общий запуск всей системы в Docker;
- `AGENTS.md` — правила работы с агентом и коммитами;
- `ARCHITECTURE.md` — архитектурный каркас проекта;
- сабмодули `hookah-web`, `hookah-api`, `hookah-db` — отдельные репозитории приложения.

## Как запускать все вместе

```bash
cp .env.example .env
docker compose up --build
```

После старта сервисы будут доступны по адресам:

- frontend: `http://localhost:8080`
- backend: `http://localhost:3000/api/v1/health`
- swagger: `http://localhost:3000/api/docs`
- postgres: `localhost:5432`

## Как запускать части по отдельности

Через общий compose:

```bash
docker compose up --build db
docker compose up --build api
docker compose up --build web
```

Или через Dockerfile внутри каждого дочернего репозитория.

## Работа с сабмодулями

Если главный репозиторий уже склонирован, подтянуть содержимое сабмодулей можно так:

```bash
git submodule update --init --recursive
```

При изменении дочернего репозитория рабочий цикл такой:

1. Сделать коммит внутри нужного сабмодуля.
2. Вернуться в корень проекта.
3. Зафиксировать обновленный указатель сабмодуля в главном репозитории.

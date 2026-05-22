# Release Controller

## Описание

Контроллер канареечных релизов: поэтапное переключение трафика, pause/resume, rollback и принудительный переход на 100%.

## Основные возможности

- создание release
- управление стадиями release
- loop-триггер для фонового шага контроллера

## Структура проекта

- `app/` - код сервиса (FastAPI, config, domain handlers)
- `deploy/` - служебные файлы для роли сервиса в деплое
- `pyproject.toml` - зависимости и метаданные проекта
- `Dockerfile` - сборка контейнера
- `.env.example` - пример переменных окружения

## Быстрый старт (локально)

1. Установить зависимости:
   `uv sync --frozen --extra dev`
2. Запустить сервис:
   `uv run uvicorn app.main:app --host 0.0.0.0 --port 8000`
3. Проверить health:
   `curl http://127.0.0.1:8000/health`

## Переменные окружения

- `SERVICE_ROLE` - роль сервиса в control plane
- `SERVICE_NAME` - техническое имя сервиса
- `POSTGRES_DSN` - строка подключения к PostgreSQL
- `PROMETHEUS_BASE_URL` - адрес Prometheus
- `SERVICE_TO_SERVICE_URLS_JSON` - карта внутренних URL сервисов

## Docker

- Сборка: `docker build -t release_controller:local .`
- Запуск: `docker run --rm -p 8000:8000 --env-file .env release_controller:local`

## Деплой

Файлы для деплоя лежат в `deploy/`.

## Основные API ручки

- `GET /releases`
- `POST /releases`
- `GET /releases/{release_id}`
- `POST /releases/{release_id}/pause`
- `POST /releases/{release_id}/resume`
- `POST /releases/{release_id}/rollback`
- `POST /releases/{release_id}/skip-to-100`
- `POST /internal/release-loop/tick`

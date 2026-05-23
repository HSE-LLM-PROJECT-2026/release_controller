# Release Controller

## Описание

Этот репозиторий содержит сервис управления канареечными релизами. Контроллер ведет rollout, проверяет SLO-метрики и меняет веса трафика через routing service.

## Основные возможности
- создание release-задачи
- пошаговый canary rollout
- pause/resume релиза
- rollback при нарушении SLO
- быстрое переключение на 100 процентов
- служебная tick-ручка для release loop

## Структура проекта

- `app/` — основной код приложения
  - `main.py` — FastAPI-приложение и HTTP-ручки
  - `config.py` — настройки сервиса

- `deploy/` — файлы и переменные для развертывания
- `.env.example` — пример переменных окружения
- `Dockerfile` — сборка Docker-образа
- `pyproject.toml` — зависимости и настройки Python-проекта
- `requirements.txt` — список зависимостей для совместимого запуска без uv

## Быстрый старт локально

1. Установите зависимости:
   ```bash
   uv sync
   ```

2. Создайте `.env` на основе `.env.example`:
   ```bash
   cp .env.example .env
   ```

3. Запустите сервис:
   ```bash
   uv run uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload
   ```

Если `uv` не используется, можно запустить через обычный virtualenv:

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload
```

## Переменные окружения
- `DATABASE_URL`
- `ROUTING_SERVICE_URL`
- `DEPLOYMENT_SERVICE_URL`
- `PROMETHEUS_URL`
- `SECURITY_SERVICE_URL`
- `SERVICE_TOKEN`
- `LOG_LEVEL`

Пример `.env`:

```env
DATABASE_URL=postgresql://postgres:postgres@localhost:5432/llm_platform
SERVICE_TOKEN=change-me
LOG_LEVEL=INFO
```

## Основные API-ручки
- `GET /health`
- `GET /livez`
- `GET /service-info`
- `GET /releases`
- `POST /releases`
- `GET /releases/{release_id}`
- `POST /releases/{release_id}/pause`
- `POST /releases/{release_id}/resume`
- `POST /releases/{release_id}/rollback`
- `POST /tick`

## Сборка и запуск в Docker

```bash
docker build -t hse-llm-project-2026/release_controller:local .
docker run --env-file .env -p 8000:8000 hse-llm-project-2026/release_controller:local
```

## Деплой в Kubernetes

Файлы развертывания лежат в папке `deploy/`. Для сервисов, которые уже подключены к стенду, используются Helm values и deploy-скрипты из соответствующего репозитория или общего инфраструктурного пайплайна.

## Метрики и документация

- Swagger UI: `/docs`
- OpenAPI: `/openapi.json`
- Health check: `/health`
- Liveness check: `/livez`

## Автор

Igor Malysh

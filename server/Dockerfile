# syntax=docker/dockerfile:1

# Build the React frontend.
FROM node:20-alpine AS frontend-builder

WORKDIR /frontend

COPY frontend/package.json frontend/package-lock.json ./
RUN npm ci

COPY frontend/ ./
RUN npm run build

# Run the Django backend.
FROM python:3.12.0-slim-bookworm

ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    APP=/app

WORKDIR ${APP}

COPY requirements.txt ${APP}/requirements.txt

RUN python -m pip install --upgrade pip && \
    python -m pip install --no-cache-dir -r ${APP}/requirements.txt

COPY . ${APP}
COPY --from=frontend-builder /frontend/build ${APP}/frontend/build

RUN chmod +x ${APP}/entrypoint.sh

EXPOSE 8000

ENTRYPOINT ["/bin/sh", "/app/entrypoint.sh"]

CMD ["gunicorn", "--bind", "0.0.0.0:8000", "--workers", "3", "djangoproj.wsgi:application"]

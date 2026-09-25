# === ЭТАП 1: Builder) ===
FROM python:3.11-alpine AS builder
WORKDIR /app

RUN apk add --no-cache \
    gcc \
    musl-dev \
    python3-dev

COPY requirements.txt .

RUN pip install --no-cache-dir \
    --prefix=/install \
    -r requirements.txt

# === ЭТАП 2: Runtime ===

FROM python:3.11-alpine
WORKDIR /app
ENV LOG_DIR=/var/log/app

HEALTHCHECK --interval=30s --timeout=5s \
    CMD python -c "import urllib.request; urllib.request.urlopen('http://localhost:8080/health')" || exit 1

RUN addgroup -S appgroup
RUN adduser -S -D -G appgroup appuser

RUN mkdir -p /var/log/app
RUN chown appuser /var/log/app

VOLUME /var/log/app

USER appuser

COPY --from=builder /install /usr/local
COPY main.py .

EXPOSE 8080

CMD ["python", "main.py"]


# itw-infra

**itw-infra** — общий инфраструктурный репозиторий для локальной разработки и тестирования микросервисов экосистемы **ITWizardry**.

Репозиторий предоставляет единый dev-стек и используется сервисами:
- `user-service`
- `notification-service`
- (в дальнейшем) `config-server`, `api-gateway`, `monitoring`

Инфраструктура поднимается один раз и переиспользуется всеми сервисами.

---

## 🎯 Цель

- Единая точка запуска инфраструктуры
- Чёткое разделение infra и application-кода
- Production-подобная структура для локальной разработки
- Упрощение CI/CD и onboarding новых разработчиков

---

## 📦 Состав инфраструктуры

| Компонент | Назначение |
|---------|------------|
| **PostgreSQL** | Основная база данных сервисов |
| **Kafka** | Брокер событий |
| **ZooKeeper** | Координация Kafka (dev-mode) |
| **MailHog** | SMTP-сервер + Web UI |
| **Docker Network** | Общая сеть для сервисов |

---

## 🧩 Архитектура

```
[user-service] ─┐
                ├── Kafka (user.notifications) ──▶ [notification-service]
[config-server] ┘

[user-service] ──▶ PostgreSQL

[notification-service] ──▶ MailHog (SMTP)
```

---

## ⚙️ Требования

- Docker
- Docker Compose v2

---

## 🚀 Быстрый старт

### 1. Подготовка окружения

```bash
cp .env.example .env
```

При необходимости отредактируйте порты и параметры.

### 2. Запуск инфраструктуры

```bash
docker compose up -d
```

### 3. Проверка состояния

```bash
docker compose ps
```

---

## 🌐 Порты

| Сервис | Адрес |
|------|-------|
| PostgreSQL | `localhost:5432` |
| ZooKeeper | `localhost:2181` |
| Kafka | `localhost:9092` |
| Kafka (internal) | `kafka:29092` |
| MailHog UI | http://localhost:8025 |
| MailHog SMTP | `localhost:1025` |

---

## 🧪 Smoke tests (быстрая проверка)

### PostgreSQL
```bash
docker compose exec postgres psql   -U ${POSTGRES_USER:-user_service_user}   -d ${POSTGRES_DB:-user_service}   -c "SELECT 1;"
```

### MailHog UI
```bash
curl -s http://localhost:8025 > /dev/null && echo "MailHog UI доступен"
```

### Kafka (топики)
```bash
docker compose exec kafka kafka-topics --create   --topic test-topic   --bootstrap-server localhost:9092   --partitions 1   --replication-factor 1

docker compose exec kafka kafka-topics --list   --bootstrap-server localhost:9092
```

### Kafka (producer / consumer)

Consumer:
```bash
docker compose exec kafka kafka-console-consumer   --bootstrap-server localhost:9092   --topic test-topic   --from-beginning
```

Producer:
```bash
docker compose exec -T kafka kafka-console-producer   --bootstrap-server localhost:9092   --topic test-topic
```

---

## 🔧 Использование в сервисах

### Kafka
```properties
spring.kafka.bootstrap-servers=kafka:29092
```

### PostgreSQL
```properties
spring.datasource.url=jdbc:postgresql://itw-postgres:5432/user_service
```

### SMTP
```properties
spring.mail.host=itw-mailhog
spring.mail.port=1025
```

---

## ❤️ Healthcheck ZooKeeper

В образе `confluentinc/cp-zookeeper` 4LW-команды (`ruok`, `imok`) могут быть
ограничены whitelist'ом, поэтому healthcheck реализован через проверку
доступности TCP-порта `2181`.

Это стабильный и переносимый вариант для dev-среды.

---

## 🔐 Переменные окружения

- `.env` — локальный файл, **не коммитится**
- `.env.example` — шаблон для настройки окружения

---

## 🧪 Среда использования

Инфраструктура предназначена для:
- локальной разработки
- интеграционных тестов
- демонстрационных стендов

Не предназначена для production без доработок.

---

## 🗺 Roadmap

- [ ] Config Server
- [ ] API Gateway
- [ ] Kafka UI
- [ ] Observability (Prometheus + Grafana)
- [ ] Profiles: dev / test

---

## 👤 Автор

Mikhail Latypov  
GitHub: https://github.com/hodkonem

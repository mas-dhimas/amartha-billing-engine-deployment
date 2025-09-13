# Amartha Billing Engine – Dockerized Environment

This project provides a complete **local development environment** for the Amartha backend test application using Docker Compose. It includes:

- 🚀 `backend-service` (Amartha Test GO Application)
- 🐘 `PostgreSQL` database (v13)

---

## 📦 Services

### 1. **PostgreSQL** (`db`)
- Image: `postgres:13-alpine`
- Exposes port `5432`
- Uses `.env` values for:
  - `POSTGRES_DB`
  - `POSTGRES_USER`
  - `POSTGRES_PASSWORD`
- Health check via `pg_isready`

### 2. **Backend Migration Service (`backend-service-migrate`)**
- Image: `pendekaribukota/amartha-test:latest`
- Purpose: Runs one-time migration on service start
- Depends On: `db` (waits for database to be healthy)
- Command: `./amartha-billing-engine --migrate`
- Restart Policy: `no` (runs once and stops)

### 3. **Backend API Service (`backend-service`)**
- Image: `pendekaribukota/amartha-test:latest`
- Reads config via environment variables (from `.env`)
- Depends on healthy `db`
- Health check via `http://localhost:8080/healthcheck`

---

## 🚀 Getting Started

### 1. Create `.env`

You **must create** a `.env` file in the project root:

```env
# Postgres
SOURCE_DATA_POSTGRESDB_SERVER=db
SOURCE_DATA_POSTGRESDB_PORT=5432
SOURCE_DATA_POSTGRESDB_NAME=Amartha_db
SOURCE_DATA_POSTGRESDB_USERNAME=Amartha_user
SOURCE_DATA_POSTGRESDB_PASSWORD=secret
SOURCE_DATA_POSTGRESDB_TIMEOUT=5
SOURCE_DATA_POSTGRESDB_MAX_CONNS=10
SOURCE_DATA_POSTGRESDB_MIN_CONNS=1
SOURCE_DATA_POSTGRESDB_MAX_CONN_LIFETIME=300
SOURCE_DATA_POSTGRESDB_MAX_CONN_IDLE_TIME=60

# Backend
SERVICE_DATA_PORT=8080
SERVICE_DATA_LOG_LEVEL=debug
SERVICE_DATA_RATE_LIMIT=100 # hit per second
```

> 📝 Adjust values as needed.

---

### 2. Build and Run

```bash
docker compose up
```

The backend service will wait for database to become healthy before starting.


## 📥 Accessing Services

- **Backend API**: [http://localhost:8080/api/v1/](http://localhost:8080/api/v1/)
## Available Endpoints
| Method | Endpoint           | Description                                               |
| ------ | ------------------ | --------------------------------------------------------- |
| GET    | `/healthcheck`     | Returns a simple status to confirm the service is alive   |
| POST   | `/api/v1/loan`     | Make a new loan                                           |
| GET    | `/api/v1/loan/outstanding` | Get outstanding per loan                          |
| GET    | `/api/v1/customer/status`  | Check if customer is delinquent or not            |
| POST   | `/api/v1/payment`  | Make a payment for specific loan                          |

- **Postgres**: `localhost:5432` (user/pass from `.env`)

---

## 🛑 Stopping & Cleaning

To stop and remove containers + volumes:

```bash
docker compose down -v
```

---


## 📂 Volumes

- `amartha_postgres_data`: Persists PostgreSQL data

---

## 🧪 Healthchecks

- PostgreSQL: `pg_isready`
- Ensures backend only starts after dependencies are ready.

---

## 📖 Notes
- You can mount a local config file into the container if needed using `volumes`.
- Config is passed at runtime using environment variables (`-config=...`).
---

## 📎 Useful Commands

| Task                        | Command                                     |
|-----------------------------|---------------------------------------------|
| Run backend manually        | `docker-compose run backend-service bash`   |\
| Rebuild everything          | `docker-compose up --build --force-recreate`|
| Enter PostgreSQL            | `docker exec -it <db_container> psql -U ...`|

---

## 👨‍💻 Maintainers

Adhimas W Ramadhana, Amartha engineering test @ 2025
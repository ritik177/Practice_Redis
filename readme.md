# Redis Practice

This repository contains practice examples for learning Redis with a local Docker setup. The current project also includes a simple Node.js API that connects to Redis and MongoDB.

## Project Structure

```text
.
├── 01 foundation of redis/
│   └── readme.md
├── 02 setup-local-redis/
│   ├── src/
│   │   └── index.js
│   ├── package.json
│   ├── package-lock.json
│   └── readme.md
├── docker-compose.yml
└── readme.md
```

## Prerequisites

Make sure you have the following installed:

- Docker
- Docker Compose
- Node.js
- npm

## Services

The `docker-compose.yml` file starts two local services:

| Service | Container Name | Port |
| --- | --- | --- |
| Redis | `practice-redis` | `6379` |
| MongoDB | `practice-mongo` | `27017` |

Redis is configured with append-only persistence enabled.

## Start Redis and MongoDB

From the repository root, run:

```bash
docker compose up
```

To run the services in the background, use:

```bash
docker compose up -d
```

To stop the services, run:

```bash
docker compose down
```

## Run the Node.js App

Go to the local Redis setup project:

```bash
cd "02 setup-local-redis"
```

Install dependencies:

```bash
npm install
```

Start the app:

```bash
npm run dev
```

The server runs on:

```text
http://localhost:3000
```

## Test the Endpoints

Check Redis connection:

```bash
curl http://localhost:3000/redis
```

Expected response:

```json
{
  "redis": "PONG"
}
```

Check MongoDB connection:

```bash
curl http://localhost:3000/mongo
```

Expected response:

```json
{
  "mongo": "connected",
  "database": "practice_mongo"
}
```

## Environment Variables

The app uses these default connection URLs:

```text
REDIS_URL=redis://localhost:6379
MONGODB_URL=mongodb://localhost:27017/practice_mongo
```

You can override them when starting the app:

```bash
REDIS_URL=redis://localhost:6379 MONGODB_URL=mongodb://localhost:27017/practice_mongo npm run dev
```

## Notes

- Keep Docker running before starting the Node.js app.
- The `node_modules/` directory is ignored by Git inside the `02 setup-local-redis` project.
- Redis data and MongoDB data are stored in Docker volumes named `redis-data` and `mongo-data`.

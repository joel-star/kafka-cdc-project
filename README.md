# Kafka CDC Pipeline: PostgreSQL → Kafka → MongoDB Atlas

An end-to-end Change Data Capture (CDC) pipeline that streams row-level
changes from PostgreSQL into MongoDB in real time, using Debezium and
Kafka Connect — no polling, no custom ETL scripts.

## Architecture
PostgreSQL (WAL)
→ Debezium source connector (Kafka Connect)
→ Kafka topic: cdc.public.orders
→ MongoDB sink connector (Kafka Connect)
→ MongoDB Atlas

AKHQ provides a web UI to monitor Kafka topics, messages, and connector status.

## Stack

- **Kafka** (KRaft mode — no Zookeeper)
- **Kafka Connect** with:
  - Debezium PostgreSQL source connector
  - MongoDB Kafka sink connector
- **PostgreSQL** (source database)
- **MongoDB Atlas** (sink, free M0 tier)
- **AKHQ** (Kafka + Kafka Connect monitoring UI)
- **Docker Compose** (orchestration)

## How it works

1. Debezium reads PostgreSQL's write-ahead log (WAL) directly — capturing every
   insert/update/delete as a change event, without querying the tables.
2. Each change event is published to a Kafka topic (`cdc.public.orders`).
3. The MongoDB sink connector consumes from that topic and writes the current
   row state into a MongoDB Atlas collection.
4. An `ExtractNewRecordState` transform unwraps Debezium's change-event envelope
   so only clean row data lands in MongoDB.

## Setup

1. Clone this repo
2. Copy `register-mongodb-sink.template.json` to `register-mongodb-sink.json`
   and replace `REPLACE_WITH_YOUR_MONGODB_URI` with your MongoDB Atlas connection string
3. `docker compose up -d`
4. Create the source table in Postgres and register the Debezium connector
   (see `register-postgres-connector.json`)
5. Register the MongoDB sink connector:
curl -X POST -H "Content-Type: application/json"
--data @register-mongodb-sink.json
http://localhost:8083/connectors
6. Insert/update rows in Postgres and watch them appear in MongoDB Atlas

## What I learned building this

- How logical replication (WAL) enables low-overhead CDC vs. polling
- Kafka Connect's REST-driven connector model (source vs. sink connectors)
- Debezium's change-event envelope structure (before/after/op)
- Externalizing secrets from connector configs instead of hardcoding credentials
- Running Kafka in KRaft mode without Zookeeper

## Status

Core pipeline complete and verified end-to-end (insert + update propagation confirmed)

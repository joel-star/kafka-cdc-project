# Kafka CDC Pipeline: Postgres → Kafka → MongoDB

End-to-end Change Data Capture pipeline using Debezium and Kafka Connect,
streaming row-level database changes into MongoDB in real time.

## Architecture
Postgres (WAL) → Debezium source connector → Kafka topic → Sink connector → MongoDB

## Stack
- Kafka (KRaft mode, no Zookeeper)
- Kafka Connect + Debezium
- PostgreSQL (source)
- MongoDB Atlas (sink)
- AKHQ (Kafka + Kafka Connect monitoring)

## Status
🚧 Work in progress — building incrementally, step by step.

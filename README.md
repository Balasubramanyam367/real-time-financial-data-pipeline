# Real-Time Financial Data Pipeline

## Overview
This project implements a production-style real-time data pipeline to ingest,
process, and analyze financial transaction events with strong reliability
guarantees.

## Architecture
![Architecture Diagram](diagrams/architecture.png)

## Tech Stack
- Apache Kafka
- Apache Spark (Structured Streaming)
- Delta Lake
- AWS S3
- Apache Airflow
- Python
- Great Expectations

## Data Flow
1. Transaction events are published to Kafka topics.
2. Spark Structured Streaming consumes events using event-time processing.
3. Raw events are stored in the Bronze layer.
4. Cleaned and deduplicated data is written to the Silver layer.
5. Aggregated, analytics-ready tables are created in the Gold layer.
6. Airflow orchestrates batch jobs and downstream dependencies.

## Challenges & Debugging
### Out-of-Order and Late-Arriving Events
- Issue: Events arrived late or out of order, causing incorrect aggregations.
- Debugging: Inspected event timestamps and Kafka offsets.
- Fix: Implemented event-time watermarking in Spark.

### Duplicate Transactions
- Issue: Duplicate messages caused inflated metrics.
- Debugging: Detected duplicate transaction IDs during validation.
- Fix: Implemented idempotent writes using Delta Lake merge logic.

### Pipeline Failures and Recovery
- Issue: Streaming jobs failed during redeployments.
- Debugging: Analyzed Spark checkpoints and logs.
- Fix: Restarted jobs from checkpoints with no data loss.

## Data Quality & Reliability
- Schema validation using Great Expectations
- Checkpoint-based fault tolerance
- Idempotent writes to prevent duplication

## How to Run Locally
```bash
docker-compose up
spark-submit src/processing/streaming_job.py

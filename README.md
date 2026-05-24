<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/0ff5d7fa-3dfb-4439-9f27-95ed86a1b123" />


```markdown
# Real-Time E-Commerce Kafka Pipeline

## 📌 Overview
This project implements a **real-time data streaming pipeline** for an e-commerce platform using **Apache Kafka, Python, and Snowflake**. It demonstrates how raw customer events (page views, cart additions, purchases) can be ingested, validated, transformed, and persisted into a cloud data warehouse for analytics.

The pipeline is designed with **scalability, reproducibility, and auditability** in mind, following a layered architecture (**Bronze → Silver → Gold**).

---

## ⚙️ Architecture

### Data Flow
1. **Producers**
   - `producer.py` simulates customer events and publishes them to Kafka topic `raw_events`.

2. **Raw Event Consumer**
   - `kafka_processor.py` consumes from `raw_events`.
   - Cleans and validates events.
   - Publishes valid events to `processed_events`.

3. **Clean Event Consumer**
   - `snowflake_consumer.py` consumes from `clean_events`.
   - Buffers events in batches.
   - Uses `write_pandas` to insert into Snowflake table `KAFKA_EVENTS_SILVER`.

4. **Snowflake**
   - Stores events in structured tables for BI dashboards and analytics.

---

## 🛠️ Tech Stack
- **Apache Kafka** – event streaming backbone  
- **Python** – `kafka-python`, `snowflake-connector-python`  
- **Snowflake** – cloud data warehouse  
- **Docker** – optional, for reproducible environment  
- **Pandas** – batch inserts into Snowflake  

---

## 📂 Project Structure
```
kafkaRealTimeEcommerce/
│
├── producer.py            # Publishes simulated events to Kafka

├── kafka_processor.py     # Cleans/validates raw events

├── snowflake_consumer.py  # Loads clean events into Snowflake

├── requirements.txt       # Python dependencies

└── README.md              # Project documentation
```

---

## 🚀 Setup Instructions

### 1. Environment
```bash
python -m venv venv
venv\Scripts\activate   # Windows
source venv/bin/activate # Linux/Mac
pip install -r requirements.txt
```

### 2. Kafka
Start Kafka broker (e.g., via Docker Compose).  
Create topics:
```bash
kafka-topics.sh --create --topic raw_events --bootstrap-server localhost:9092 --partitions 3 --replication-factor 1
kafka-topics.sh --create --topic processed_events --bootstrap-server localhost:9092 --partitions 3 --replication-factor 1
kafka-topics.sh --create --topic clean_events --bootstrap-server localhost:9092 --partitions 3 --replication-factor 1
```

### 3. Snowflake
Create schema and table:
```sql
CREATE DATABASE KAFKA_DB;
CREATE SCHEMA STREAMING;

CREATE OR REPLACE TABLE KAFKA_EVENTS_SILVER (
  EVENT_ID STRING,
  CUSTOMER_ID STRING,
  EVENT_TYPE STRING,
  AMOUNT NUMBER,
  CURRENCY STRING,
  EVENT_TIMESTAMP TIMESTAMP_NTZ
);
```

### 4. Run Pipeline
```bash
python producer.py
python kafka_processor.py
python snowflake_consumer.py
```

---

## 🧪 Validation Rules
Events are considered valid if:
- `customer_id` is present  
- `event_type ∈ {PAGE_VIEW, ADD_TO_CART, PURCHASE}`  
- `amount > 0`  
- `currency` is not null  
- `is_valid != False`  

Invalid events are discarded.

---

## 📊 Analytics Layer
- **Bronze Layer** → Raw Kafka events (`raw_events`)  
- **Silver Layer** → Cleaned, validated events in Snowflake (`KAFKA_EVENTS_SILVER`)  
- **Gold Layer** → Aggregated metrics for BI dashboards (e.g., revenue per customer, conversion funnel)  

---

## 🔒 Reliability & Commit Strategy
- Consumers use **manual offset commits** (`enable_auto_commit=False`) to ensure **at‑least‑once delivery**.  
- Offsets are committed only after successful batch inserts into Snowflake.  
- If batch insert fails, offsets are not committed → messages are retried.  

---

## 📈 Future Enhancements
- Add **schema registry** for event validation.  
- Implement **exactly‑once semantics** with Kafka transactions.  
- Build **Power BI / Snowsight dashboards** on top of Gold layer.  
- Add **health checks and monitoring** with Prometheus/Grafana.  

---

## 🧑‍💻 Audience
This README is intended for **data engineers, architects, and developers** familiar with Kafka, Python, and Snowflake. It assumes knowledge of streaming concepts, ETL pipelines, and cloud data warehousing.
```

```


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

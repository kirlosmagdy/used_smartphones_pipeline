# Used Phone Marketplace Data Pipeline

## Project Overview

This repository demonstrates a **near-real-time data pipeline** for a used phone marketplace. The pipeline ingests, processes, and stores data from multiple sources into a centralized warehouse, enabling analytics and insights.

## Architecture Overview

```
[Data Sources] → [Kafka (Ingestion)] → [Spark Streaming (Processing)] → [Snowflake (Storage)] → [dbt + BI Tools (Analytics)]
```

### Key Components

* **Data Sources**: Web scrapers (Mobile Misr, Noon), internal transactions, user activity, AI pricing data, and manual market data.
* **Ingestion Layer**: Apache Kafka for real-time streaming.
* **Processing Layer**: Apache Spark Streaming for transformations.
* **Orchestration**: Apache Airflow for workflow management.
* **Transformation**: dbt for warehouse modeling and testing.
* **Storage**: Snowflake as the data warehouse.
* **Analytics**: BI tools (Power BI, Tableau) for visualization.

## Why These Technologies

| Layer          | Technology     | Reason                                            |
| -------------- | -------------- | ------------------------------------------------- |
| Ingestion      | Apache Kafka   | High throughput, decoupling producers/consumers   |
| Processing     | Apache Spark   | Scalable transformations for batch and stream     |
| Orchestration  | Apache Airflow | Reliable scheduling, monitoring, and retries      |
| Transformation | dbt            | SQL-based transformations, testing, documentation |
| Storage        | Snowflake      | Scalable, performant, managed data warehouse      |

## Data Flow

1. **Web Scrapers** collect used smartphone listings.
2. **Kafka** receives streaming events from scrapers, app logs, and AI pricing engine.
3. **Spark Streaming** consumes and transforms data before loading to Snowflake.
4. **Airflow** orchestrates the scraping, streaming, and dbt transformation workflows.
5. **dbt** builds analytics-ready star schema tables for BI and data science use cases.



## Warehouse Schema Example

```

FACT: fact_used_phone_listings
- listing_id (UUID)
- date_id (FK)
- product_id (FK)
- source_id (FK)
- seller_id (FK)
- url_id (FK)
- date_key (FK)
- price_amount
- price_currency



DIM: dim_product
- product_id (PK)
- brand
- model
- storage_gb


DIM: dim_source
- source_id (PK)
- source_domain
- source_type


DIM: dim_listing_url
- url_id (PK)
- full_url


DIM: dim_seller
- seller_id (PK)
- seller_name


DIM: dim_date
- date_id (PK)
- scraped_date
- ingestion_date


```

### **Schema Diagram:**
```


                                    ┌─────────────┐
                                    │  dim_date   │
                                    └──────┬──────┘
                                           │
                                           │
┌─────────────┐          ┌─────────────────▼─────────────────┐          ┌──────────────────┐
│ dim_product │◄─────────┤      fact_phone_listings          ├─────────►│ dim_listing_url  │
└─────────────┘          └─────────────────┬─────────────────┘          └──────────────────┘
                                           │
                                           │
                                ┌──────────┼──────────┐
                                │                     │
                         ┌──────▼──────┐       ┌──────▼──────┐
                         │ dim_source  │       │ dim_seller  │
                         └─────────────┘       └─────────────┘
                                           



```

## Why Star Schema?

Simplicity: Star schemas are easy to understand and query with simple joins.

Performance: Optimized for analytical queries and aggregations in BI tools.

Scalability: Handles large datasets efficiently by separating facts from dimensions.

Flexibility: New dimensions or facts can be added without impacting existing ones.


## Performance & Quality

* **Partitioning** by `ingest_date` for faster queries.
* **Data Quality** with dbt tests and Great Expectations.
* **Monitoring** via Airflow and Kafka metrics.
* **Schema Evolution** handled with dbt version control.


## How to Run

1. Clone the repository.
2. Install dependencies listed in `requirements.txt`.
3. Run scrapers to generate CSV data.
4. Start Kafka and Spark for streaming.
5. Load processed data into Snowflake using Airflow.
6. Run dbt to transform and build analytics models.

## Key Takeaways

* Demonstrates complete data engineering lifecycle.
* Combines batch and streaming data ingestion.
* Uses modern tools: Kafka, Spark, Airflow, dbt, Snowflake.
* Scalable, modular, and production-ready design.






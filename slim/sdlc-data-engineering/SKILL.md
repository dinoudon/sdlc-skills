---
name: sdlc-data-engineering
description: "Data engineering: pipelines, data quality, data mesh, data lakehouse, ETL/ELT, streaming architecture."
version: 6.3.0
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [data-engineering, pipelines, data-quality, data-mesh, data-lakehouse, etl, streaming]
---

---
name: sdlc-data-engineering
description: "Data engineering: pipelines, data quality, data mesh, data lakehouse, ETL/ELT, streaming architecture."
version: 6.3.0
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [data-engineering, pipelines, data-quality, data-mesh, data-lakehouse, etl, streaming]
---

# Data Engineering Skill

Build reliable, scalable data systems. Covers data pipelines, data quality, data mesh, data lakehouse, ETL/ELT, and stream
## When to Use This Skill
- Building data pipelines
- Implementing data quality checks
- Designing data architecture (mesh, lakehouse)
- Setting up ETL/ELT workflows
- Implementing streaming data systems
- Data modeling and schema design
- Data governance and lineage
- Performance optimization for data systems
## Overview
Data engineering is the discipline of designing, building, and maintaining systems for collecting, storing, and analyzing data. It combines software engineering with data-specific concerns like quality, governance, and scalability.
```
Data Pipeline:
  - Series of data processing steps
  - Extract, Transform, Load (ETL)
  - Extract, Load, Transform (ELT)
  - Batch and streaming variants

Data Quality:
  - Accuracy, completeness, consistency
  - Timeliness, validity, uniqueness
  - Monitoring and alerting
  - Data profiling and validation

Data Mesh:
  - Decentralized data ownership
  - Data as a product
```
## Step 1: Data Pipeline Design
```
Batch pipeline:
  - Process data in scheduled intervals
  - Higher latency (minutes to hours)
  - Lower complexity
  - Good for reporting, analytics

Streaming pipeline:
  - Process data in real-time
  - Low latency (milliseconds to seconds)
  - Higher complexity
  - Good for monitoring, alerts

Hybrid pipeline:
  - Combine batch and streaming
  - Lambda architecture
```
## Step 2: Data Quality
```
Accuracy:
  - Data correctly represents reality
  - No errors or mistakes
  - Verified against source

Completeness:
  - All required data present
  - No missing values
  - All records captured

Consistency:
  - Data same across systems
  - No contradictions
  - Uniform format

```
## Step 3: Data Modeling
```
Dimensional modeling:
  - Star schema
  - Snowflake schema
  - Fact and dimension tables
  - Good for analytics

  Star schema:
    Fact table (measures)
      ├── Dimension 1 (who)
      ├── Dimension 2 (what)
      ├── Dimension 3 (when)
      └── Dimension 4 (where)

Data Vault:
  - Hub, Link, Satellite tables
```
## Step 4: Data Warehouse
```
Layers:
  Raw/Staging:
    - Raw data from sources
    - No transformations
    - Full history
  
  Integration:
    - Cleaned and validated
    - Conformed dimensions
    - Business logic applied
  
  Presentation:
    - Optimized for queries
    - Aggregated tables
    - Views for consumers
```
## Step 5: Data Mesh
```
Domain ownership:
  - Each domain owns its data
  - Domain experts manage data
  - Decentralized decision-making
  - Accountability and ownership

Data as a product:
  - Data treated as product
  - Product thinking for data
  - SLAs and quality guarantees
  - Self-service consumption

Self-serve data platform:
  - Platform for data producers
  - Tools and infrastructure
```
## Step 6: Data Lakehouse
```
Components:
  - Storage layer (data lake)
  - Table format (Delta, Iceberg, Hudi)
  - Query engine (Spark, Trino, Athena)
  - Catalog (Hive Metastore, Unity Catalog)

Benefits:
  - ACID transactions
  - Schema enforcement
  - Time travel
  - Unified analytics
  - Cost-effective storage

Table formats:
  Delta Lake:
```
## Step 7: Streaming Architecture
```
Event streaming:
  - Continuous flow of events
  - Immutable event log
  - Replay capability
  - Real-time processing

Message queues:
  - Point-to-point messaging
  - Task distribution
  - Buffering
  - Decoupling

Stream processing:
  - Process events as they arrive
  - Windowed aggregations
```
## Step 8: Data Orchestration
```
Apache Airflow:
  - DAG-based workflows
  - Python-defined pipelines
  - Rich ecosystem
  - Most popular

  Example DAG:
    from airflow import DAG
    from airflow.operators.python import PythonOperator
    from datetime import datetime
    
    with DAG('etl_pipeline', start_date=datetime(2024, 1, 1)) as dag:
        extract = PythonOperator(
            task_id='extract',
            python_callable=extract_data
```
## Step 9: Data Governance
```
Data catalog:
  - Inventory of data assets
  - Metadata management
  - Search and discovery
  - Tools: DataHub, Amundsen, OpenMetadata

Data lineage:
  - Track data flow
  - Understand dependencies
  - Impact analysis
  - Tools: OpenLineage, Marquez, Atlan

Data access:
  - Role-based access control
  - Column-level security
```
## Step 10: Performance Optimization
```
Partitioning:
  - Partition by frequently filtered columns
  - Reduce data scanned
  - Balance partition size
  
  Example:
    -- Partition by date
    CREATE TABLE orders (
      order_id INT64,
      order_date DATE,
      total_amount NUMERIC
    )
    PARTITION BY order_date;
    
    -- Query benefits
```
## Related Skills
- [sdlc-observability](sdlc-observability): Monitoring data pipelines and quality
- [sdlc-architecture-design](sdlc-architecture-design): System design for data systems
- [sdlc-testing-qa](sdlc-testing-qa): Testing data pipelines
- [sdlc-deployment](sdlc-deployment): Deploying data infrastructure
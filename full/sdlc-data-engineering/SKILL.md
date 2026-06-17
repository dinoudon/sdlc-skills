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

Build reliable, scalable data systems. Covers data pipelines, data quality, data mesh, data lakehouse, ETL/ELT, and streaming architecture.

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

### Key Concepts

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
  - Self-serve data platform
  - Federated computational governance

Data Lakehouse:
  - Combines data lake and data warehouse
  - ACID transactions on data lake
  - Schema enforcement
  - Unified analytics

Streaming Architecture:
  - Real-time data processing
  - Event-driven systems
  - Message queues and streams
  - Complex event processing
```

---

## Step 1: Data Pipeline Design

### Pipeline Architecture

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
  - Kappa architecture
  - Best of both worlds

Pipeline components:
  - Source (database, API, file)
  - Ingestion (connector, extractor)
  - Processing (transform, enrich)
  - Storage (data lake, warehouse)
  - Serving (API, dashboard)
  - Orchestration (scheduler, workflow)
```

### ETL vs ELT

```
ETL (Extract, Transform, Load):
  - Transform before loading
  - Traditional approach
  - Better for structured data
  - Tools: Informatica, Talend, SSIS

ELT (Extract, Load, Transform):
  - Load raw data first
  - Transform in target system
  - Better for cloud data warehouses
  - Tools: dbt, Airflow, Fivetran

When to use ETL:
  - Data needs heavy transformation
  - Target system has limited compute
  - Compliance requires pre-processing
  - Legacy systems

When to use ELT:
  - Cloud data warehouse (Snowflake, BigQuery)
  - Raw data needed for flexibility
  - Transformations are complex
  - Need for data lineage

Example ELT pipeline:
  1. Extract from source (Fivetran, Airbyte)
  2. Load to staging (raw data)
  3. Transform in warehouse (dbt)
  4. Serve to consumers (views, tables)
```

---

## Step 2: Data Quality

### Quality Dimensions

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

Timeliness:
  - Data available when needed
  - Fresh enough for use case
  - Updated regularly

Validity:
  - Data conforms to rules
  - Correct data types
  - Within acceptable ranges

Uniqueness:
  - No duplicate records
  - Proper deduplication
  - Unique identifiers
```

### Quality Implementation

```
Data profiling:
  - Understand data structure
  - Identify patterns
  - Detect anomalies
  - Tools: Great Expectations, pandas-profiling

Data validation:
  - Check against rules
  - Schema validation
  - Business rule validation
  - Tools: Great Expectations, Deequ

Data monitoring:
  - Track quality over time
  - Alert on degradation
  - Quality dashboards
  - Tools: Monte Carlo, Bigeye

Example (Great Expectations):
  import great_expectations as gx
  
  context = gx.get_context()
  
  # Define expectations
  expectations = [
    gx.expectations.ExpectColumnValuesToNotBeNull(column="user_id"),
    gx.expectations.ExpectColumnValuesToBeUnique(column="user_id"),
    gx.expectations.ExpectColumnValuesToBeBetween(
        column="age", min_value=0, max_value=150
    ),
  ]
  
  # Validate data
  result = context.run_checkpoint(
      checkpoint_name="my_checkpoint",
      batch_request=batch_request,
  )
```

---

## Step 3: Data Modeling

### Modeling Approaches

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
  - Highly normalized
  - Good for audit and history
  - Complex but flexible

  Components:
    Hub: Business keys
    Link: Relationships
    Satellite: Context and history

Activity schema:
  - Event-based modeling
  - User actions as first-class
  - Good for behavioral analytics
  
  Tables:
    Activities (events)
    Entities (users, items)
    Links (relationships)

One Big Table (OBT):
  - Denormalized for performance
  - Simple to query
  - Good for specific use cases
  - Trade storage for speed
```

### Schema Design

```
Column naming:
  - snake_case for all columns
  - Prefix with table name for clarity
  - Consistent naming conventions
  
  Good: user_id, order_date, total_amount
  Bad: userId, OrderDate, Total

Data types:
  - Use appropriate types
  - Avoid VARCHAR for everything
  - Use TIMESTAMP for dates
  - Use DECIMAL for money

Partitioning:
  - Partition by date (most common)
  - Partition by region
  - Partition by status
  - Balance partition size

Clustering:
  - Cluster by frequently filtered columns
  - Improve query performance
  - Reduce scan costs
  
  Example (BigQuery):
    CREATE TABLE orders (
      order_id INT64,
      customer_id INT64,
      order_date DATE,
      total_amount NUMERIC
    )
    PARTITION BY order_date
    CLUSTER BY customer_id
```

---

## Step 4: Data Warehouse

### Warehouse Architecture

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

Modern data stack:
  - Ingestion: Fivetran, Airbyte
  - Storage: Snowflake, BigQuery, Redshift
  - Transformation: dbt
  - Orchestration: Airflow, Dagster
  - BI: Looker, Tableau, Metabase
```

### dbt (Data Build Tool)

```
Project structure:
  dbt_project.yml
  models/
    staging/
      stg_orders.sql
      stg_customers.sql
    marts/
      finance/
        fct_orders.sql
        dim_customers.sql
  tests/
  macros/
  seeds/

Example model:
  -- models/marts/finance/fct_orders.sql
  
  WITH orders AS (
    SELECT * FROM {{ ref('stg_orders') }}
  ),
  
  customers AS (
    SELECT * FROM {{ ref('stg_customers') }}
  ),
  
  final AS (
    SELECT
      orders.order_id,
      orders.order_date,
      customers.customer_name,
      orders.total_amount,
      orders.status
    FROM orders
    LEFT JOIN customers ON orders.customer_id = customers.customer_id
  )
  
  SELECT * FROM final

Testing:
  -- schema.yml
  version: 2
  
  models:
    - name: fct_orders
      columns:
        - name: order_id
          tests:
            - unique
            - not_null
        - name: total_amount
          tests:
            - positive_value
```

---

## Step 5: Data Mesh

### Mesh Principles

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
  - Reduce friction
  - Enable autonomy

Federated computational governance:
  - Global standards
  - Local implementation
  - Interoperability
  - Security and compliance
```

### Implementation

```
Data product structure:
  - Input ports (data sources)
  - Output ports (data consumers)
  - Discoverability (catalog)
  - Quality (SLAs, monitoring)
  - Lineage (dependencies)
  - Access control (permissions)

Example data product:
  -- Product: Customer 360
  -- Domain: Marketing
  -- SLA: 99.9% availability, <1hr freshness
  
  CREATE TABLE customer_360 (
    customer_id STRING,
    name STRING,
    email STRING,
    segment STRING,
    lifetime_value NUMERIC,
    last_activity TIMESTAMP,
    _updated_at TIMESTAMP
  );
  
  -- Quality checks
  ASSERT customer_id IS UNIQUE;
  ASSERT email IS NOT NULL;
  ASSERT lifetime_value >= 0;

Platform capabilities:
  - Data catalog (discovery)
  - Data lineage (dependencies)
  - Data quality (monitoring)
  - Access control (governance)
  - Compute (processing)
  - Storage (persistence)
```

---

## Step 6: Data Lakehouse

### Lakehouse Architecture

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
    - Databricks' format
    - ACID on data lake
    - Time travel
    - Schema evolution
  
  Apache Iceberg:
    - Open format
    - Vendor neutral
    - Hidden partitioning
    - Schema evolution
  
  Apache Hudi:
    - Uber's format
    - Incremental processing
    - Upsert support
    - Near real-time
```

### Implementation

```
Example (Delta Lake):
  from delta.tables import DeltaTable
  from pyspark.sql import SparkSession
  
  spark = SparkSession.builder.appName("Lakehouse").getOrCreate()
  
  # Write data
  df.write.format("delta").save("/data/orders")
  
  # Read data
  df = spark.read.format("delta").load("/data/orders")
  
  # Time travel
  df_v1 = spark.read.format("delta").option("versionAsOf", 0).load("/data/orders")
  
  # Upsert
  deltaTable = DeltaTable.forPath(spark, "/data/orders")
  deltaTable.alias("target").merge(
      source.alias("source"),
      "target.order_id = source.order_id"
  ).whenMatchedUpdateAll().whenNotMatchedInsertAll().execute()

Example (Iceberg):
  -- Create table
  CREATE TABLE orders (
    order_id BIGINT,
    customer_id BIGINT,
    order_date DATE,
    total_amount DECIMAL(10,2)
  )
  USING iceberg
  PARTITIONED BY (order_date);
  
  -- Time travel
  SELECT * FROM orders TIMESTAMP AS OF '2024-01-01';
  SELECT * FROM orders VERSION AS OF 1234;
```

---

## Step 7: Streaming Architecture

### Streaming Concepts

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
  - Stateful processing
  - Complex event processing

Tools:
  Apache Kafka:
    - Distributed event streaming
    - High throughput
    - Durability
    - Ecosystem (Kafka Streams, ksqlDB)
  
  Apache Flink:
    - Stream processing framework
    - Exactly-once semantics
    - Stateful processing
    - Low latency
  
  Apache Pulsar:
    - Multi-tenant messaging
    - Geo-replication
    - Tiered storage
    - Unified messaging
```

### Kafka Implementation

```
Producer:
  from kafka import KafkaProducer
  
  producer = KafkaProducer(
      bootstrap_servers=['localhost:9092'],
      value_serializer=lambda v: json.dumps(v).encode('utf-8')
  )
  
  # Send message
  producer.send('orders', value={'order_id': 123, 'amount': 99.99})
  producer.flush()

Consumer:
  from kafka import KafkaConsumer
  
  consumer = KafkaConsumer(
      'orders',
      bootstrap_servers=['localhost:9092'],
      auto_offset_reset='earliest',
      value_deserializer=lambda m: json.loads(m.decode('utf-8'))
  )
  
  for message in consumer:
      order = message.value
      process_order(order)

Stream processing (Kafka Streams):
  KStream<String, Order> orders = builder.stream("orders");
  
  KTable<String, OrderStats> stats = orders
      .groupByKey()
      .aggregate(
          OrderStats::new,
          (key, order, stats) -> stats.add(order)
      );
  
  stats.toStream().to("order-stats");
```

---

## Step 8: Data Orchestration

### Orchestration Tools

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
        )
        
        transform = PythonOperator(
            task_id='transform',
            python_callable=transform_data
        )
        
        load = PythonOperator(
            task_id='load',
            python_callable=load_data
        )
        
        extract >> transform >> load

Dagster:
  - Asset-based orchestration
  - Type system
  - Testing built-in
  - Modern approach

  Example:
    from dagster import asset, Definitions
    
    @asset
    def orders():
        return extract_orders()
    
    @asset
    def customers():
        return extract_customers()
    
    @asset
    def order_summary(orders, customers):
        return transform(orders, customers)

Prefect:
  - Pythonic workflows
  - Dynamic workflows
  - Cloud-native
  - Easy to use

  Example:
    from prefect import flow, task
    
    @task
    def extract():
        return get_data()
    
    @task
    def transform(data):
        return process(data)
    
    @flow
    def etl_pipeline():
        data = extract()
        result = transform(data)
        return result
```

---

## Step 9: Data Governance

### Governance Framework

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
  - Row-level security
  - Tools: Apache Ranger, Privacera

Data privacy:
  - PII detection
  - Data masking
  - Consent management
  - Tools: BigID, OneTrust, TrustArc

Compliance:
  - GDPR, CCPA, HIPAA
  - Data retention policies
  - Audit trails
  - Tools: Collibra, Alation
```

### Implementation

```
Access control:
  -- Grant access
  GRANT SELECT ON schema.table TO ROLE analyst;
  
  -- Column-level security
  CREATE VIEW orders_masked AS
  SELECT
    order_id,
    CASE WHEN current_role() = 'admin' THEN email ELSE '***' END AS email,
    total_amount
  FROM orders;

Data classification:
  -- Tag PII columns
  ALTER TABLE customers ALTER COLUMN email SET TAG 'pii' = 'true';
  ALTER TABLE customers ALTER COLUMN ssn SET TAG 'pii' = 'true';
  
  -- Query metadata
  SELECT column_name, tag_name, tag_value
  FROM information_schema.column_tags
  WHERE tag_name = 'pii';

Lineage tracking:
  -- OpenLineage integration
  from openlineage.client import OpenLineageClient
  
  client = OpenLineageClient()
  client.emit(
      RunEvent(
          eventType=RunState.COMPLETE,
          run=Run(runId="run-123"),
          job=Job(namespace="my-namespace", name="etl-job"),
          inputs=[Dataset(namespace="my-namespace", name="raw_orders")],
          outputs=[Dataset(namespace="my-namespace", name="clean_orders")]
      )
  )
```

---

## Step 10: Performance Optimization

### Query Optimization

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
    SELECT * FROM orders WHERE order_date = '2024-01-01';
    -- Only scans partition for 2024-01-01

Clustering:
  - Sort data within partitions
  - Improve query performance
  - Reduce scan costs
  
  Example:
    CREATE TABLE orders (
      order_id INT64,
      customer_id INT64,
      order_date DATE,
      total_amount NUMERIC
    )
    PARTITION BY order_date
    CLUSTER BY customer_id;

Materialized views:
  - Pre-computed results
  - Automatic refresh
  - Improve query performance
  
  Example:
    CREATE MATERIALIZED VIEW daily_sales AS
    SELECT
      order_date,
      COUNT(*) as order_count,
      SUM(total_amount) as total_sales
    FROM orders
    GROUP BY order_date;
```

### Scaling Strategies

```
Horizontal scaling:
  - Add more nodes
  - Distribute data
  - Parallel processing
  
  Tools:
    - Spark (distributed processing)
    - Presto/Trino (distributed queries)
    - Snowflake (auto-scaling)

Vertical scaling:
  - Larger instances
  - More memory
  - Faster storage
  
  When to use:
    - Single-node bottlenecks
    - Memory-intensive operations
    - I/O bound workloads

Caching:
  - Query result caching
  - Data caching
  - Metadata caching
  
  Tools:
    - Redis (in-memory cache)
    - Memcached (distributed cache)
    - Cloud provider caches

Cost optimization:
  - Use appropriate storage tier
  - Compress data
  - Delete old data
  - Use spot instances
  - Right-size compute
```

---

## Related Skills

- [sdlc-observability](sdlc-observability): Monitoring data pipelines and quality
- [sdlc-architecture-design](sdlc-architecture-design): System design for data systems
- [sdlc-testing-qa](sdlc-testing-qa): Testing data pipelines
- [sdlc-deployment](sdlc-deployment): Deploying data infrastructure

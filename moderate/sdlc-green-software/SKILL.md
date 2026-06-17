---
name: sdlc-green-software
description: "Green software engineering: sustainability, carbon-aware computing, energy-efficient architecture, eco-friendly development."
version: 6.3.0
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [green-software, sustainability, carbon-aware, energy-efficient, eco-friendly, climate]
---

# Green Software Skill

Build sustainable, energy-efficient software systems. Covers carbon-aware computing, energy-efficient architecture, and eco-friendly development practices.

## When to Use This Skill

- Reducing software carbon footprint
- Implementing carbon-aware systems
- Optimizing energy efficiency
- Measuring environmental impact
- Sustainable development practices
- Green cloud computing
- Carbon-aware scheduling
- Eco-friendly architecture

## Overview

Green software engineering applies sustainability principles to software design, development, and operation. It aims to minimize the environmental impact of software systems while maintaining functionality and performance.

### Key Concepts

```
Carbon footprint:
  - CO2 equivalent emissions
  - Direct (energy consumption)
  - Indirect (cloud, infrastructure)
  - Embodied (hardware manufacturing)

Carbon-aware:
  - Adjust behavior based on carbon intensity
  - Use clean energy when available
  - Shift workloads to greener times
  - Location-aware computing

Energy efficiency:
  - Minimize energy consumption
  - Optimize resource usage
  - Reduce waste
  - Efficient algorithms

Sustainability:
  - Environmental responsibility
  - Long-term thinking
  - Resource conservation
  - Circular economy
```

---

## Step 1: Measuring Carbon Footprint

### Carbon Accounting

```
Scope 1: Direct emissions
  - On-site energy consumption
  - Company vehicles
  - Refrigerants
  
Scope 2: Indirect emissions (electricity)
  - Purchased electricity
  - Data center power
  - Cloud computing
  
Scope 3: Other indirect emissions
  - Business travel
  - Employee commuting
  - Supply chain
  - End-of-life disposal

Software carbon intensity:
  SCI = (E * I) + M per R
  
  Where:
    E = Energy consumed by software
    I = Location-based carbon intensity
    M = Embodied emissions
    R = Functional unit (requests, users, etc.)
```

### Measurement Tools

```
Cloud carbon calculators:
  AWS Customer Carbon Footprint Tool:
    - Built into AWS
    - Service-level breakdown
    - Historical trends
    - Recommendations
  
  Google Cloud Carbon Footprint:
    - Project-level tracking
    - Service breakdown
    - Carbon-free energy percentage
  
  Azure Emissions Impact Dashboard:
    - Subscription-level
    - Service breakdown
    - Comparison to on-premises

Application-level tools:
  Green Metrics Tool:
    - Measure energy consumption
    - Track carbon emissions
    - Compare implementations
  
  Scaphandre:
    - Power consumption monitoring
    - Process-level tracking
    - RAPL integration
  
  Kepler:
    - Kubernetes power monitoring
    - Container-level tracking
    - Prometheus integration

Example (AWS):
  import boto3
  
  # Get carbon footprint
  client = boto3.client('ce')
  
  response = client.get_cost_and_usage(
      TimePeriod={
          'Start': '2024-01-01',
          'End': '2024-01-31'
      },
      Granularity='MONTHLY',
      Metrics=['BlendedCost'],
      GroupBy=[
          {'Type': 'DIMENSION', 'Key': 'SERVICE'}
      ]
  )
```

---

## Step 2: Carbon-Aware Computing

### Carbon Intensity

```
Definition:
  - CO2 emitted per kWh of electricity
  - Varies by location and time
  - Lower with renewable energy
  - Measured in gCO2/kWh

Factors:
  - Energy source (coal, gas, nuclear, solar, wind)
  - Time of day (peak vs off-peak)
  - Season (heating, cooling demand)
  - Grid mix (regional differences)

Data sources:
  Watttime:
    - Real-time carbon intensity
    - Grid-level data
    - API access
  
  ElectricityMaps:
    - Carbon intensity by region
    - Historical data
    - Forecasts
  
  Carbon-aware SDK:
    - Microsoft's open-source tool
    - Multiple data sources
    - Easy integration
```

### Carbon-Aware Scheduling

```
Concept:
  - Schedule workloads when carbon intensity is low
  - Use clean energy when available
  - Delay non-urgent tasks
  - Location-aware placement

Implementation:
  from carbonaware import CarbonAwareSDK
  
  sdk = CarbonAwareSDK()
  
  # Get best time to run
  best_time = sdk.get_best_execution_time(
      location="westus",
      start_time=datetime.now(),
      end_time=datetime.now() + timedelta(hours=24),
      duration=timedelta(hours=1)
  )
  
  # Schedule job
  scheduler.schedule(job, at=best_time)

Kubernetes integration:
  # Carbon-aware pod scheduling
  apiVersion: v1
  kind: Pod
  metadata:
    name: batch-job
    annotations:
      carbon-aware: "true"
      carbon-threshold: "100"  # gCO2/kWh
  spec:
    containers:
      - name: job
        image: mybatchjob:latest

Azure Carbon-Aware SDK:
  # Get emissions data
  import carbonaware
  
  emissions = carbonaware.get_emissions_data(
      location="westus",
      start_time=datetime.now(),
      end_time=datetime.now() + timedelta(hours=24)
  )
  
  # Find lowest carbon time
  best_time = min(emissions, key=lambda e: e.carbon_intensity)
```

---

## Step 3: Energy-Efficient Architecture

### Efficient Design Patterns

```
Lazy loading:
  - Load resources only when needed
  - Reduce initial load
  - Save bandwidth and energy
  
  Example:
    // JavaScript
    const image = new Image();
    image.loading = 'lazy';
    image.src = 'large-image.jpg';

Caching:
  - Cache frequently accessed data
  - Reduce computation
  - Save network requests
  
  Example:
    # Redis caching
    import redis
    
    cache = redis.Redis()
    
    def get_data(key):
        # Check cache first
        cached = cache.get(key)
        if cached:
            return json.loads(cached)
        
        # Fetch from database
        data = fetch_from_db(key)
        
        # Cache for 1 hour
        cache.setex(key, 3600, json.dumps(data))
        
        return data

Pagination:
  - Load data in chunks
  - Reduce memory usage
  - Save bandwidth
  
  Example:
    # API pagination
    GET /api/users?page=1&limit=50
    
    # Database pagination
    SELECT * FROM users
    ORDER BY id
    LIMIT 50 OFFSET 0

Compression:
  - Compress data before transfer
  - Reduce bandwidth
  - Save energy
  
  Example:
    # HTTP compression
    Accept-Encoding: gzip, deflate, br
    
    # Gzip response
    import gzip
    
    def compress_response(data):
        return gzip.compress(data.encode())
```

### Efficient Algorithms

```
Algorithm complexity:
  - Choose O(n log n) over O(n²)
  - Use hash tables for lookups
  - Avoid nested loops when possible
  
  Example:
    # Bad: O(n²)
    def find_duplicates_slow(arr):
        duplicates = []
        for i in range(len(arr)):
            for j in range(i + 1, len(arr)):
                if arr[i] == arr[j]:
                    duplicates.append(arr[i])
        return duplicates
    
    # Good: O(n)
    def find_duplicates_fast(arr):
        seen = set()
        duplicates = []
        for item in arr:
            if item in seen:
                duplicates.append(item)
            seen.add(item)
        return duplicates

Data structures:
  - Use appropriate data structures
  - Consider memory vs speed tradeoffs
  - Profile before optimizing
  
  Example:
    # List vs Set for membership testing
    # List: O(n) lookup
    my_list = [1, 2, 3, 4, 5]
    if 3 in my_list:  # O(n)
        pass
    
    # Set: O(1) lookup
    my_set = {1, 2, 3, 4, 5}
    if 3 in my_set:  # O(1)
        pass
```

---

## Step 4: Green Cloud Computing

### Cloud Optimization

```
Right-sizing:
  - Match resources to workload
  - Avoid over-provisioning
  - Use appropriate instance types
  
  Tools:
    - AWS Compute Optimizer
    - Azure Advisor
    - Google Recommender
  
  Example:
    # AWS right-sizing
    aws compute-optimizer get-ec2-instance-recommendations \
        --instance-arns arn:aws:ec2:us-east-1:123456789:instance/i-1234567890abcdef0

Auto-scaling:
  - Scale based on demand
  - Reduce idle resources
  - Save costs and energy
  
  Example (AWS):
    aws autoscaling create-auto-scaling-group \
        --auto-scaling-group-name my-asg \
        --min-size 1 \
        --max-size 10 \
        --desired-capacity 2

Spot instances:
  - Use spare capacity
  - Significant cost savings
  - Interruptible workloads
  
  Example (AWS):
    aws ec2 request-spot-instances \
        --spot-price "0.05" \
        --instance-count 1 \
        --type "one-time" \
        --launch-specification file://spec.json

Serverless:
  - Pay only for what you use
  - No idle resources
  - Automatic scaling
  
  Example (AWS Lambda):
    def handler(event, context):
        # Process event
        return {
            'statusCode': 200,
            'body': json.dumps('Success')
        }
```

### Green Regions

```
Carbon-aware region selection:
  - Choose regions with clean energy
  - Consider data sovereignty
  - Balance latency and carbon
  
  Carbon intensity by region (approximate):
    - Sweden (Stockholm): 10-20 gCO2/kWh
    - Norway (Oslo): 10-20 gCO2/kWh
    - France (Paris): 50-80 gCO2/kWh
    - Canada (Montreal): 20-40 gCO2/kWh
    - US West (Oregon): 50-100 gCO2/kWh
    - US East (Virginia): 300-400 gCO2/kWh
    - Asia (Singapore): 400-500 gCO2/kWh

Implementation:
  # AWS region selection
  import boto3
  
  def get_green_region():
      regions = ['eu-north-1', 'eu-west-1', 'us-west-2']
      
      # Get carbon intensity for each region
      carbon_data = {}
      for region in regions:
          carbon_data[region] = get_carbon_intensity(region)
      
      # Return region with lowest carbon intensity
      return min(carbon_data, key=carbon_data.get)
```

---

## Step 5: Sustainable Development Practices

### Green Coding

```
Code efficiency:
  - Write efficient algorithms
  - Optimize database queries
  - Reduce unnecessary computation
  - Profile and optimize hot paths
  
  Example:
    # Bad: Multiple database queries
    def get_user_orders_slow(user_id):
        user = db.query(f"SELECT * FROM users WHERE id = {user_id}")
        orders = db.query(f"SELECT * FROM orders WHERE user_id = {user_id}")
        return {'user': user, 'orders': orders}
    
    # Good: Single query with join
    def get_user_orders_fast(user_id):
        return db.query("""
            SELECT u.*, o.*
            FROM users u
            JOIN orders o ON u.id = o.user_id
            WHERE u.id = %s
        """, (user_id,))

Memory management:
  - Free unused resources
  - Use generators for large datasets
  - Avoid memory leaks
  - Profile memory usage
  
  Example:
    # Bad: Load all data into memory
    def process_all_data():
        data = load_all_data()  # Could be GBs
        for item in data:
            process(item)
    
    # Good: Use generator
    def process_all_data():
        for item in load_data_generator():  # One at a time
            process(item)

Network efficiency:
  - Minimize API calls
  - Use batch operations
  - Compress data
  - Cache responses
  
  Example:
    # Bad: Individual API calls
    for user_id in user_ids:
        user = api.get_user(user_id)
        process(user)
    
    # Good: Batch API call
    users = api.get_users_batch(user_ids)
    for user in users:
        process(user)
```

### Sustainable Testing

```
Test efficiency:
  - Run only necessary tests
  - Use test parallelization
  - Cache test results
  - Skip unchanged tests
  
  Example (pytest):
    # Run only changed tests
    pytest --lf  # Last failed
    pytest -x    # Stop on first failure
    pytest -n auto  # Parallel execution

Test environment:
  - Use lightweight test environments
  - Share test infrastructure
  - Clean up after tests
  - Use mocks and stubs
  
  Example:
    # Mock external services
    from unittest.mock import patch
    
    @patch('external_api.call')
    def test_my_function(mock_call):
        mock_call.return_value = {'status': 'ok'}
        result = my_function()
        assert result == expected
```

---

## Step 6: Green Data Management

### Data Efficiency

```
Data minimization:
  - Collect only necessary data
  - Delete old data
  - Compress stored data
  - Use appropriate data types
  
  Example:
    # Bad: Store everything
    user_data = {
        'name': 'John',
        'email': 'john@example.com',
        'address': '123 Main St',
        'phone': '555-1234',
        'ssn': '123-45-6789',
        'preferences': {...},
        'history': [...]
    }
    
    # Good: Store only what's needed
    user_data = {
        'name': 'John',
        'email': 'john@example.com'
    }

Data compression:
  - Compress at rest
  - Compress in transit
  - Use efficient formats
  
  Example:
    # Parquet for analytics
    import pandas as pd
    
    df = pd.DataFrame(data)
    df.to_parquet('data.parquet', compression='snappy')
    
    # Avro for streaming
    from fastavro import writer
    
    with open('data.avro', 'wb') as f:
        writer(f, schema, records)

Data lifecycle:
  - Archive old data
  - Delete unnecessary data
  - Use tiered storage
  
  Example (S3):
    # Lifecycle policy
    {
        "Rules": [
            {
                "ID": "ArchiveOldData",
                "Status": "Enabled",
                "Filter": {"Prefix": "logs/"},
                "Transitions": [
                    {
                        "Days": 30,
                        "StorageClass": "GLACIER"
                    }
                ],
                "Expiration": {
                    "Days": 365
                }
            }
        ]
    }
```

---

## Step 7: Green CI/CD

### Sustainable Pipelines

```
Pipeline optimization:
  - Cache dependencies
  - Parallelize stages
  - Skip unnecessary steps
  - Use efficient tools
  
  Example (GitHub Actions):
    name: CI
    
    on: [push, pull_request]
    
    jobs:
      build:
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v3
          
          # Cache dependencies
          - uses: actions/cache@v3
            with:
              path: ~/.npm
              key: npm-${{ hashFiles('package-lock.json') }}
          
          # Run tests in parallel
          - run: npm test -- --parallel

Build optimization:
  - Use multi-stage builds
  - Minimize image size
  - Cache layers
  - Use Alpine images
  
  Example (Dockerfile):
    # Multi-stage build
    FROM node:18-alpine AS builder
    WORKDIR /app
    COPY package*.json ./
    RUN npm ci --only=production
    
    FROM node:18-alpine
    WORKDIR /app
    COPY --from=builder /app/node_modules ./node_modules
    COPY . .
    EXPOSE 3000
    CMD ["node", "server.js"]

Artifact management:
  - Clean up old artifacts
  - Compress artifacts
  - Use artifact caching
  - Delete unnecessary files
  
  Example (GitHub Actions):
    - name: Upload artifact
      uses: actions/upload-artifact@v3
      with:
        name: build
        path: dist/
        retention-days: 7  # Auto-delete after 7 days
```

---

## Step 8: Green Monitoring

### Sustainable Observability

```
Efficient logging:
  - Log only necessary data
  - Use structured logging
  - Set appropriate log levels
  - Rotate and archive logs
  
  Example:
    # Structured logging
    import structlog
    
    logger = structlog.get_logger()
    
    logger.info("user_login",
        user_id=123,
        ip="192.168.1.1",
        timestamp=datetime.utcnow().isoformat()
    )

Efficient metrics:
  - Collect essential metrics
  - Use appropriate granularity
  - Aggregate data
  - Delete old metrics
  
  Example (Prometheus):
    # Efficient metric
    http_requests_total{method="GET", status="200"} 1234
    
    # Not every request
    http_request_details{user_id="123", path="/api/users"} 1

Efficient tracing:
  - Sample traces
  - Use head-based sampling
  - Reduce trace data
  - Archive old traces
  
  Example (OpenTelemetry):
    from opentelemetry import trace
    
    # Sample 10% of traces
    sampler = trace.sampling.TraceIdRatioBased(0.1)
    tracer = trace.get_tracer(__name__, sampler=sampler)
```

---

## Step 9: Green Software Patterns

### Sustainable Patterns

```
Carbon-aware pattern:
  - Monitor carbon intensity
  - Adjust workload timing
  - Use clean energy
  - Shift to greener regions
  
  Implementation:
    def carbon_aware_execute(task):
        carbon_intensity = get_carbon_intensity()
        
        if carbon_intensity > THRESHOLD:
            # Defer to cleaner time
            schedule_for_later(task)
        else:
            # Execute now
            execute(task)

Efficiency pattern:
  - Optimize resource usage
  - Reduce waste
  - Cache aggressively
  - Compress data
  
  Implementation:
    def efficient_process(data):
        # Cache check
        cached = cache.get(data.key)
        if cached:
            return cached
        
        # Process efficiently
        result = optimized_algorithm(data)
        
        # Cache result
        cache.set(data.key, result)
        
        return result

Longevity pattern:
  - Design for long life
  - Use stable technologies
  - Plan for maintenance
  - Avoid technical debt
  
  Implementation:
    - Choose mature frameworks
    - Write maintainable code
    - Document decisions
    - Plan for upgrades
```

---

## Step 10: Green Software Tools

### Measurement Tools

```
Green Metrics Tool:
  - Measure energy consumption
  - Track carbon emissions
  - Compare implementations
  - Open source
  
  Usage:
    gmt run my-application
    gmt report my-application

Scaphandre:
  - Power consumption monitoring
  - Process-level tracking
  - RAPL integration
  - Prometheus exporter
  
  Usage:
    scaphandre prometheus

Kepler:
  - Kubernetes power monitoring
  - Container-level tracking
  - Prometheus integration
  - eBPF-based
  
  Usage:
    kubectl apply -f kepler.yaml
```

### Optimization Tools

```
Cloud Carbon Footprint:
  - AWS, GCP, Azure support
  - Carbon estimation
  - Recommendations
  - Open source
  
  Usage:
    npx @cloud-carbon-footprint/cli

Greenframe:
  - Frontend carbon footprint
  - Browser-based testing
  - CI/CD integration
  
  Usage:
    greenframe analyze https://myapp.com

Website Carbon Calculator:
  - Website carbon footprint
  - Page-level analysis
  - Comparison to other sites
  
  Usage:
    https://www.websitecarbon.com/
```

---

## Related Skills

- [sdlc-architecture-design](sdlc-architecture-design): Sustainable architecture patterns
- [sdlc-deployment](sdlc-deployment): Green deployment strategies
- [sdlc-observability](sdlc-observability): Efficient monitoring
- [sdlc-platform-engineering](sdlc-platform-engineering): Green platform practices

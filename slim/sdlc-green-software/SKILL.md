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

Build sustainable, energy-efficient software systems. Covers carbon-aware computing, energy-efficient a
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
```
## Step 1: Measuring Carbon Footprint
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
```
## Step 2: Carbon-Aware Computing
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
```
## Step 3: Energy-Efficient Architecture
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
```
## Step 4: Green Cloud Computing
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

```
## Step 5: Sustainable Development Practices
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
```
## Step 6: Green Data Management
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
```
## Step 7: Green CI/CD
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
```
## Step 8: Green Monitoring
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
```
## Step 9: Green Software Patterns
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
```
## Step 10: Green Software Tools
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
```
## Related Skills
- [sdlc-architecture-design](sdlc-architecture-design): Sustainable architecture patterns
- [sdlc-deployment](sdlc-deployment): Green deployment strategies
- [sdlc-observability](sdlc-observability): Efficient monitoring
- [sdlc-platform-engineering](sdlc-platform-engineering): Green platform practices
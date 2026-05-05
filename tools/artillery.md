# Artillery

## What it is

Open-source load testing and performance benchmarking tool for APIs and web applications. Uses YAML configuration to define test scenarios and generates detailed performance reports.

## Problem it solves

- **Performance validation**: Ensures APIs can handle expected traffic
- **Bottleneck identification**: Finds performance issues before production
- **Capacity planning**: Determines infrastructure requirements
- **Regression testing**: Catches performance degradations
- **Stress testing**: Validates system limits and failure modes

## When to use it

- **API development**: Test endpoint performance under load
- **Pre-deployment validation**: Verify production readiness
- **Capacity planning**: Determine scaling requirements
- **Performance regression**: Catch slowdowns in new releases
- **Stress testing**: Find breaking points and failure modes
- **CI/CD pipelines**: Automated performance checks

## When NOT to use it

- **Unit testing**: Use unit test frameworks instead
- **Functional testing**: Use testing frameworks like Jest or Cypress
- **Simple smoke tests**: Use curl or simple HTTP clients
- **UI testing**: Use browser automation tools
- **Micro-benchmarks**: Use specialized benchmarking libraries

## Integration pattern

### 1. YAML Configuration
```yaml
# artillery-config.yml
config:
  target: 'https://api.example.com'
  phases:
    - duration: 60
      arrivalRate: 10
      name: "Warm up"
    - duration: 120
      arrivalRate: 50
      name: "Load test"
  processor: "./custom-processor.js"

scenarios:
  - name: "Login and get data"
    weight: 70
    flow:
      - post:
          url: "/auth/login"
          json:
            username: "{{ username }}"
            password: "{{ password }}"
          capture:
            - json: "$.token"
              as: "authToken"
      - get:
          url: "/api/user/profile"
          headers:
            Authorization: "Bearer {{ authToken }}"
```

### 2. Custom Processors
```javascript
// custom-processor.js
module.exports = {
  beforeRequest: function(requestParams, context, ee) {
    // Modify request before sending
    requestParams.headers['X-Request-ID'] = generateUUID();
    return requestParams;
  },
  
  afterResponse: function(requestParams, response, context, ee) {
    // Process response
    if (response.statusCode === 200) {
      context.successCount = (context.successCount || 0) + 1;
    }
    return response;
  }
};
```

### 3. CLI Usage
```bash
# Run basic load test
artillery run artillery-config.yml

# Run with environment variables
artillery run artillery-config.yml --environment staging

# Generate HTML report
artillery report output.json --output report.html

# Run multiple scenarios
artillery run load-test.yml --target https://api.example.com
```

### 4. Node.js Integration
```javascript
const artillery = require('artillery');

const runTest = async () => {
  const results = await artillery.run({
    config: require('./artillery-config.yml'),
    target: 'https://api.example.com'
  });
  
  console.log('Test completed:', results.aggregate);
};

runTest().catch(console.error);
```

## Security considerations

### 1. Test data protection
- Never use production credentials in test configs
- Use separate test accounts and data
- Sanitize logs to avoid leaking sensitive information
- Store test credentials in environment variables

### 2. Test environment isolation
- Run tests against dedicated test environments
- Ensure test data doesn't affect production systems
- Use rate limiting to avoid overwhelming services
- Monitor test impact on shared resources

### 3. Network security
- Use VPN or secure connections for remote testing
- Validate SSL certificates in test configurations
- Secure test endpoints with proper authentication
- Monitor for unauthorized access during tests

## Example usage

### API Load Testing
```yaml
# api-load-test.yml
config:
  target: 'https://api.staging.example.com'
  phases:
    - duration: 300
      arrivalRate: 20
      name: "Sustained load"
  payload:
    path: "test-data.csv"
    fields:
      - "username"
      - "password"

scenarios:
  - name: "User workflow"
    flow:
      - post:
          url: "/auth/login"
          json:
            username: "{{ username }}"
            password: "{{ password }}"
          capture:
            - json: "$.token"
              as: "token"
      - get:
          url: "/api/dashboard"
          headers:
            Authorization: "Bearer {{ token }}"
      - post:
          url: "/api/data"
          headers:
            Authorization: "Bearer {{ token }}"
          json:
            action: "sync"
```

### CI/CD Integration
```bash
#!/bin/bash
# performance-test.sh

echo "Running API performance tests..."

# Run artillery test
artillery run tests/load-test.yml --target $API_ENDPOINT --environment $ENVIRONMENT

# Check if performance meets SLA
if [ $? -eq 0 ]; then
  echo "Performance tests passed"
  exit 0
else
  echo "Performance tests failed"
  exit 1
fi
```

### Docker Integration
```dockerfile
FROM node:18-alpine

RUN npm install -g artillery
COPY tests/ /tests/
WORKDIR /tests

CMD ["artillery", "run", "load-test.yml"]
```

## Configuration

### Environment Variables
```
ARTILLERY_TARGET=https://api.example.com
ARTILLERY_ENVIRONMENT=staging
ARTILLERY_DURATION=300
ARTILLERY_ARRIVAL_RATE=20
```

### Test Data Setup
```csv
# test-data.csv
username,password
testuser1,password123
testuser2,password456
testuser3,password789
```

## Monitoring and Analysis

### Key Metrics
- **Response time**: Average, median, 95th percentile
- **Throughput**: Requests per second
- **Error rate**: Percentage of failed requests
- **Latency distribution**: Response time percentiles
- **Resource utilization**: CPU, memory, network

### Report Analysis
```bash
# Generate detailed report
artillery report output.json

# Export to CSV for analysis
artillery report output.json --output results.csv

# Compare multiple test runs
artillery compare run1.json run2.json
```

### Alerting Setup
```javascript
// Custom processor for alerting
module.exports = {
  afterResponse: function(requestParams, response, context, ee) {
    if (response.statusCode >= 500) {
      // Send alert to monitoring system
      sendAlert('Server error detected', {
        status: response.statusCode,
        url: requestParams.url,
        timestamp: new Date()
      });
    }
    return response;
  }
};
```

## Best Practices

### Test Design
- **Realistic scenarios**: Model actual user behavior
- **Gradual ramp-up**: Start with low load, increase gradually
- **Multiple phases**: Test different load levels
- **Sufficient duration**: Run tests long enough to stabilize
- **Variety of endpoints**: Test critical paths and edge cases

### Configuration Management
- **Environment-specific configs**: Separate configs for dev/staging/prod
- **Parameterized values**: Use variables for URLs and credentials
- **Modular scenarios**: Break complex workflows into reusable scenarios
- **Version control**: Track test configurations in git

### Performance Optimization
- **Monitor resources**: Track CPU, memory, and network usage
- **Identify bottlenecks**: Focus on slow endpoints first
- **Baseline establishment**: Create performance benchmarks
- **Regression detection**: Compare with previous test results

## Common pitfalls

- **Unrealistic load**: Test with traffic patterns that don't match reality
- **Insufficient warm-up**: Not allowing systems to reach steady state
- **Ignoring errors**: Failing to investigate failed requests
- **Test data issues**: Using insufficient or incorrect test data
- **Environment differences**: Testing against environments that don't match production
- **Monitoring gaps**: Not collecting sufficient metrics during tests

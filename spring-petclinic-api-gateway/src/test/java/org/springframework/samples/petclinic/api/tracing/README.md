# Distributed Tracing in Spring PetClinic Microservices

This document explains how distributed tracing is implemented in the Spring PetClinic Microservices application and how to validate trace logs in the local Zipkin instance.

## Implementation Overview

The Spring PetClinic Microservices application uses Micrometer Tracing with Brave and Zipkin to implement distributed tracing. This allows tracking requests as they flow through the different microservices.

Key components:
- **Micrometer Tracing**: Provides the API for creating and managing traces
- **Brave**: Implementation of the tracing API
- **Zipkin**: Distributed tracing system for collecting and visualizing trace data

## Trace ID and Span ID Logging

All key API endpoints in the application log their trace ID and span ID. This makes it possible to correlate logs across services and track the flow of requests.

Example log message:
```
Finding owner with ID: 1, traceId: 4bf92f3577b34da6a3ce929d0e0e4736, spanId: a2fb4a1d1a96d312
```

## Validating Trace Logs in Zipkin

To validate that trace logs are properly sent to Zipkin:

1. **Start the application**:
   ```
   ./mvnw spring-boot:run
   ```

2. **Start Zipkin** (if not already running):
   ```
   docker run -d -p 9411:9411 openzipkin/zipkin
   ```

3. **Generate some traffic**:
   - Access the application at http://localhost:8080
   - Navigate through different pages
   - Create, read, update owners and pets

4. **Access the Zipkin UI**:
   - Open http://localhost:9411 in your browser
   - Click on "Find Traces" to see the collected traces
   - You can filter traces by service name, operation name, or duration

5. **Examine a trace**:
   - Click on a trace to see its details
   - You'll see a timeline of spans across different services
   - Each span represents an operation in a service
   - You can see the duration of each span and how they relate to each other

6. **Verify trace propagation**:
   - Check that a single trace ID is maintained across multiple services
   - Verify that parent-child relationships between spans are correct
   - Confirm that the trace includes all services involved in processing the request

## Running the Integration Test

The `TracingIntegrationTest` class demonstrates how to verify trace ID propagation:

1. **Run the test**:
   ```
   ./mvnw test -Dtest=TracingIntegrationTest
   ```

2. **Check the logs**:
   - Look for log messages with trace IDs and span IDs
   - Verify that the same trace ID appears in logs from different services

3. **Check Zipkin**:
   - Open http://localhost:9411
   - Search for the trace ID from the logs
   - Verify that the trace includes spans from all services involved

## Troubleshooting

If traces are not appearing in Zipkin:

1. **Check application.yml**:
   - Verify that tracing is enabled
   - Check that the sampling probability is set to 1.0 for development

2. **Check connectivity**:
   - Ensure Zipkin is running and accessible
   - Verify that services can connect to Zipkin

3. **Check logs**:
   - Look for any errors related to tracing or Zipkin
   - Verify that trace IDs are being logged

## Additional Resources

- [Micrometer Tracing Documentation](https://micrometer.io/docs/tracing)
- [Zipkin Documentation](https://zipkin.io/pages/documentation)
- [Spring Cloud Sleuth Migration Guide](https://docs.spring.io/spring-cloud-sleuth/docs/current-SNAPSHOT/reference/html/migration-guide.html) (for migrating from Spring Cloud Sleuth to Micrometer Tracing)

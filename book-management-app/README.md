# Book Management System with OpenTelemetry Observability

A Spring Boot application for managing book details with full CRUD operations, integrated with OpenTelemetry for complete observability. This application supports Java 8 to Java 21 and can be built with both Maven and Gradle.

## Technology Stack

- **Framework:** Spring Boot 3.5.16
- **Language:** Java 17
- **Database:** H2 (in-memory)
- **Build Tools:** Maven & Gradle
- **ORM:** Spring Data JPA
- **Validation:** Java Bean Validation
- **Containerization:** Docker
- **Orchestration:** Kubernetes
- **Observability:** OpenTelemetry (OTEL)
- **Metrics:** Micrometer + Prometheus
- **Logging:** Loki
- **Tracing:** Tempo
- **Visualization:** Grafana


**Access URLs (Docker):**
- Application: http://localhost:8080
- Grafana: http://localhost:3000 (admin/admin)
- Prometheus: http://localhost:9090
- OTEL Collector: http://localhost:4318 (HTTP), http://localhost:4317 (gRPC)
- H2 Console: http://localhost:8080/h2-console

## 📊 Observability Stack Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                  Spring Boot Application
                      book-management-app│
│                              │
│                                                              │
│  • Auto-instrumentation of HTTP requests                     │
│  • Database queries tracing                                  │
│  • JVM metrics collection                                    │
│  • Application logs with trace context                       │
└──────────────┬──────────────────────────────────────────────┘
               │ OTLP (HTTP/gRPC)
               ▼
┌─────────────────────────────────────────────────────────────┐
│              OpenTelemetry Collector                         │
│                                                              │
│  • Receives traces, metrics, and logs                        │
│  • Processes and batches telemetry data                      │
│  • Routes to appropriate backends                            │
└──────┬───────────────┬────────────────┬─────────────────────┘
       │               │                │
       │ Traces        │ Metrics        │ Logs
       ▼               ▼                ▼
┌───────────┐   ┌─────────────┐   ┌──────────┐
│   Tempo   │   │ Prometheus  │   │   Loki   │
│           │   │             │   │          │
│ Trace     │   │ Metrics     │   │ Logs     │
│ Storage   │   │ Storage     │   │ Storage  │
└───────────┘   └─────────────┘   └──────────┘
       │               │                │
       └───────────────┴────────────────┘
                       │
                       ▼
              ┌─────────────────┐
              │    Grafana      │
              │                 │
              │  Unified        │
              │  Observability  │
              │  Dashboard      │
              └─────────────────┘
```

## API Endpoints

The application runs on `http://localhost:8080`

### Book Operations

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/books` | Create a new book |
| GET | `/api/books` | Get all books |
| GET | `/api/books/{id}` | Get book by ID |
| PUT | `/api/books/{id}` | Update book by ID |
| DELETE | `/api/books/{id}` | Delete book by ID |
| GET | `/api/books/search?title={title}` | Search books by title |
| GET | `/api/books/author/{author}` | Get books by author |
| GET | `/api/books/category/{category}` | Get books by category |
| GET | `/api/books/isbn/{isbn}` | Get book by ISBN |

### Sample Request Body (POST/PUT)

#### Command to insert Sample data 
```cmd
.\create-books.bat
```

```json
{
  "title": "Clean Code",
  "author": "Robert C. Martin",
  "isbn": "978-0132350884",
  "description": "A Handbook of Agile Software Craftsmanship",
  "price": 45.99,
  "publicationYear": 2008,
  "availableQuantity": 100,
  "publisher": "Prentice Hall",
  "category": "Software Engineering"
}
```

### Sample Response

```json
{
  "id": 1,
  "title": "Clean Code",
  "author": "Robert C. Martin",
  "isbn": "978-0132350884",
  "description": "A Handbook of Agile Software Craftsmanship",
  "price": 45.99,
  "publicationYear": 2008,
  "availableQuantity": 100,
  "publisher": "Prentice Hall",
  "category": "Software Engineering"
}
```


## 🔍 What Gets Monitored



### Traces (via Tempo)
- HTTP request/response spans
- Database query execution
- Service-to-service calls
- Error tracking and stack traces
- Request latency breakdown

### Metrics (via Prometheus)
- HTTP request rates and durations
- JVM metrics (memory, GC, threads)
- Database connection pool stats
- Custom application metrics
- System resource utilization

### Logs (via Loki)
- Application logs with trace correlation
- Error and exception logs
- Request/response logging
- Database query logs
- Performance logs


#### Creating Dashboards:

**1. Application Metrics Dashboard:**
- Click "+" → "Dashboard" → "Add visualization"
- Select "Prometheus" datasource
- Use queries like:
  - `rate(http_server_requests_seconds_count[5m])` - Request rate
  - `http_server_requests_seconds_bucket` - Request duration histogram
  - `jvm_memory_used_bytes` - JVM memory usage
  - `jvm_gc_pause_seconds_sum` - GC pause time

**2. Logs Dashboard:**
- Click "Explore" → Select "Loki"
- Use queries like:
  - `{service_name="book-management-app"}` - All logs
  - `{service_name="book-management-app"} |= "ERROR"` - Error logs
  - `{service_name="book-management-app"} |= "BookController"` - Controller logs

- or 
  -`{instance="default/book-management-app-57b54ff55-hsqjj:book-management-app"}` - All logs
  - `{instance="default/book-management-app-57b54ff55-hsqjj:book-management-app"} |= "ERROR"` - Error logs
  - `{instance="default/book-management-app-57b54ff55-hsqjj:book-management-app"} |= "BookController"` - Controller logs



**3. Traces Dashboard:**
- Click "Explore" → Select "Tempo"
- Search traces by:
  - Service name: `book-management-app`
  - Span name: `GET /api/books/{id}` or
  - TraceQL : {resource.service.name="book-management-app" && name="GET /api/books/{id}"}


### Prometheus (Metrics)
- **URL:** http://localhost:9090
- Query examples:
  - `up` - Check service availability
  - `http_server_requests_seconds_count` - Total HTTP requests
  - `rate(http_server_requests_seconds_count[1m])` - Request rate per minute
  - `jvm_threads_live_threads` - Active threads


## 📚 References & Documentation

### Spring Boot & Java
- [Spring Boot 3.5 Documentation](https://docs.spring.io/spring-boot/3.5/)
- [Spring Boot 3.5 System Requirements](https://docs.spring.io/spring-boot/3.5/system-requirements.html)
- [Spring Boot Observability](https://docs.spring.io/spring-boot/3.5/reference/actuator/observability.html)
- [Spring Boot Metrics](https://docs.spring.io/spring-boot/3.5/reference/actuator/metrics.html)

### Containerization & Orchestration
- [Docker Documentation](https://docs.docker.com/)
- [Spring Boot with Docker](https://spring.io/guides/gs/spring-boot-docker/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Kubernetes Concepts](https://kubernetes.io/docs/concepts/)

### OpenTelemetry & Observability
- [OpenTelemetry Documentation](https://opentelemetry.io/docs/)
- [OpenTelemetry Java](https://opentelemetry.io/docs/languages/java/)
- [OpenTelemetry Spring Boot Starter](https://opentelemetry.io/docs/zero-code/java/spring-boot-starter/)
- [Micrometer Documentation](https://docs.micrometer.io/)
- [Micrometer Prometheus](https://docs.micrometer.io/micrometer/reference/implementations/prometheus.html)

### Monitoring & Visualization
- [Prometheus Documentation](https://prometheus.io/docs/)
- [Grafana Documentation](https://grafana.com/docs/)
- [Grafana Loki Documentation](https://grafana.com/docs/loki/)
- [Grafana Tempo Documentation](https://grafana.com/docs/tempo/)
- [Grafana Alloy Documentation](https://grafana.com/docs/alloy/)

### Spring Data & Validation
- [Spring Data JPA Documentation](https://docs.spring.io/spring-data/jpa/reference/)
- [Jakarta Bean Validation](https://beanvalidation.org/)

## 📄 License

This project is open source and available for educational purposes.

---

**Built with ❤️ for demonstrating OpenTelemetry observability in Spring Boot application With Kubernetes**
**Please feel free to pull, modify, and contribute improvements to this repository**
---




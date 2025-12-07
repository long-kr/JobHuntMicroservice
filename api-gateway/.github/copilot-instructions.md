# API Gateway - Copilot Instructions

## Project Overview
This is a Spring Cloud Gateway API Gateway for the JobHunt microservices platform. It routes requests to backend services (`user-service`, `application-service`) via Eureka service discovery.

**Tech Stack:** Spring Boot 3.5.8, Spring Cloud Gateway Server MVC 2025.0.0, Netflix Eureka Client, Java 17, Maven

## Architecture

### Service Discovery Integration
- Gateway registers with Eureka server at `http://localhost:8761/eureka/`
- Uses load-balanced URIs (`lb://service-name`) to route to discovered services
- `@EnableDiscoveryClient` annotation in `ApiGatewayApplication.java` enables Eureka registration

### Route Configuration Pattern
Routes are defined declaratively in `application.properties` using indexed notation:
```properties
spring.cloud.gateway.server.webflux.routes[N].id=service-name
spring.cloud.gateway.server.webflux.routes[N].uri=lb://service-name
spring.cloud.gateway.server.webflux.routes[N].predicates[0]=Path=/api/path/**
```

**Current Routes:**
- `/api/users/**` → `user-service`
- `/api/applications/**` → `application-service`

When adding routes, increment the index `[N]` and follow the pattern above.

## Package Structure Note
**Critical:** The package name is `com.jobhunt.apigateway` (NOT `com.jobhunt.api-gateway` or `com.jobhunt.api_gateway`). The test package incorrectly uses `api_gateway` - this is a known inconsistency from project generation.

## Development Workflow

### Build & Run
```bash
# Build with Maven wrapper
./mvnw clean package

# Run the application
./mvnw spring-boot:run
```

### Testing
```bash
# Run all tests
./mvnw test
```

## Configuration Conventions

### Adding New Routes
1. Add route definition in `application.properties` following the indexed pattern
2. Use `lb://` prefix for load-balanced service URIs discovered via Eureka
3. Path predicates should match the backend service's API path structure
4. Service ID should match the `spring.application.name` of the target microservice

### Logging
Gateway-specific trace logging is enabled for route debugging:
- `org.springframework.cloud.gateway.route.RouteDefinitionRouteLocator=INFO`
- `org.springframework.cloud.gateway.route.gateway=TRACE`

Adjust these levels in `application.properties` when debugging routing issues.

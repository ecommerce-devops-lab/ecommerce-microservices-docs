# Design Patterns in Ecommerce Architecture

## Overview

This documentation describes the design patterns implemented in the microservices architecture of the ecommerce system, organized by category and with practical code examples.

## Architectural Patterns

### 1. Microservices Pattern

**Purpose**: Decomposes the application into independent and deployable services.

**Implementation**:

* `user-service`: Manages users and credentials
* `order-service`: Handles order processing
* `product-service`: Manages product catalog
* `payment-service`: Processes payments
* `shipping-service`: Manages shipping
* `favourite-service`: Handles favorites list

**Benefits**: Independent scalability, technology diversity, separate deployment.

### 2. API Gateway Pattern

**Purpose**: Single entry point for all client requests.

**Implementation**:

```yaml
# api-gateway/src/main/resources/application.yml
server:
  port: 8080
```

**Benefits**: Centralized routing, security, rate limiting.

### 3. Service Discovery Pattern

**Purpose**: Enables automatic service registration and discovery.

**Implementation**:

```xml
<!-- service-discovery/pom.xml -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```

## Structural Patterns

### 4. Data Transfer Object (DTO)

**Purpose**: Transfers data between processes while reducing the number of calls.

**Implementation**:

```java
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class OrderDto implements Serializable {
    private Integer orderId;
    private LocalDateTime orderDate;
    private String orderDesc;
    private Double orderFee;
    private CartDto cartDto;
}
```

### 5. Builder Pattern

**Purpose**: Builds complex objects step by step.

**Implementation**:

```java
OrderDto order = OrderDto.builder()
    .orderDesc("Integration Test Order")
    .orderFee(299.99)
    .cartDto(testCartDto)
    .build();
```

### 6. Repository Pattern

**Purpose**: Encapsulates data access logic.

**Implementation**:

```java
@Repository
public interface OrderItemRepository extends JpaRepository<OrderItem, OrderItemId> {
    // Custom query methods
}
```

## Behavioral Patterns

### 7. Template Method Pattern

**Purpose**: Defines the skeleton of algorithms in the superclass.

**Implementation**:

```java
@Configuration
public class TemplateConfig {
    @LoadBalanced
    @Bean
    public RestTemplate restTemplateBean() {
        return new RestTemplate();
    }
}
```

### 8. Strategy Pattern

**Purpose**: Defines a family of interchangeable algorithms.

**Implementation**: Each service implements specific strategies:

* `PaymentService`: Payment processing strategies
* `ShippingService`: Shipping strategies
* `OrderService`: Order processing strategies

### 9. Proxy Pattern

**Purpose**: Provides a substitute to control access to another object.

**Implementation**:

```java
@FeignClient(name = "ORDER-SERVICE", path = "/order-service/api/orders")
public interface OrderClientService {
    @GetMapping("/{orderId}")
    ResponseEntity<OrderDto> findById(@PathVariable String orderId);
}
```

## Integration Patterns

### 10. Service Layer Pattern

**Purpose**: Defines application boundaries with a set of available services.

**Implementation**:

```java
@Service
@RequiredArgsConstructor
public class OrderItemServiceImpl implements OrderItemService {
    private final OrderItemRepository repository;
    private final RestTemplate restTemplate;
    // Business logic
}
```

### 11. Facade Pattern

**Purpose**: Provides a unified interface to a complex subsystem.

**Implementation**: REST controllers act as facades:

```java
@RestController
@RequestMapping("/api/orders")
public class OrderController {
    // Simplifies access to complex services
}
```

### 12. Dependency Injection Pattern

**Purpose**: Inverts control of dependencies.

**Implementation**:

```java
@Service
@RequiredArgsConstructor  // Constructor injection
public class FavouriteServiceImpl {
    private final FavouriteRepository repository;
    private final RestTemplate restTemplate;
}
```

## Architecture Advantages

* **Maintainability**: Clear separation of responsibilities
* **Scalability**: Independently scalable services
* **Testability**: Unit and integration testing per service
* **Flexibility**: Technology-specific per service
* **Resilience**: Service-specific failure isolation

## Considerations

* **Complexity**: Increased operational complexity
* **Communication**: Latency in inter-service calls
* **Consistency**: Eventual consistency between services
* **Monitoring**: Requires distributed observability
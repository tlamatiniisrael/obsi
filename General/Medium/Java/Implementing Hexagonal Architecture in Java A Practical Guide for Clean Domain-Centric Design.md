
# Implementing Hexagonal Architecture in Java: A Practical Guide for Clean Domain-Centric Design

## Introduction: The Problem with Traditional Layered Architecture

Traditional layered architecture often leads to tightly coupled systems where business logic becomes entangled with infrastructure concerns, making the codebase difficult to maintain and test.

Traditional layered architectures typically organize code into horizontal layers such as presentation, business logic, and data access. While this approach seems logical at first glance, it often results in an architecture where the domain model becomes dependent on infrastructure concerns, reversing the natural dependency flow.

Consider a typical three-layer application where business logic directly depends on database-specific code. Changes to database technology or external APIs force modifications to business logic, even when the underlying business rules remain unchanged.

This tight coupling makes testing difficult as domain logic tests require database setup or mocking of external dependencies. It also complicates maintenance as developers need to understand multiple layers to make simple changes to business functionality.

The problem becomes evident in codebases where service classes are littered with framework-specific annotations, SQL queries, and HTTP client calls, mixing essential business behavior with technical implementation details.

```java
// Traditional layered architecture approach  
public class CustomerService {  
    @Autowired  
    private CustomerRepository customerRepository; // Direct dependency on Spring and database  
      
    @Autowired  
    private EmailClient emailClient; // Direct dependency on external system  
      
    @Transactional // Framework-specific annotation  
    public void registerCustomer(CustomerDTO customerDTO) {  
        // Business logic mixed with infrastructure concerns  
        Customer customer = new Customer();  
        customer.setName(customerDTO.getName());  
        customer.setEmail(customerDTO.getEmail());  
          
        customerRepository.save(customer); // Direct database interaction  
        emailClient.sendWelcomeEmail(customer.getEmail()); // Direct external service call  
    }  
}
```

## Tips

- Watch for classes that import both domain model and infrastructure packages — this often signals problematic coupling
- Count the number of framework-specific annotations in your domain classes — more annotations usually means tighter coupling
- Consider how difficult it would be to swap your database or messaging system without changing business logic

## Understanding Hexagonal Architecture: Core Concepts and Principles

Hexagonal Architecture, also known as Ports and Adapters pattern, isolates business logic from external concerns by organizing code around a central domain model with clear boundaries.

Hexagonal Architecture was introduced by Alistair Cockburn as a way to create applications where business rules can be tested in isolation from external elements like databases, UIs, and third-party services. The core idea is to place the domain model at the center of the application, with all dependencies pointing inward toward this domain.

The architecture is visualized as a hexagon (hence the name) where each side represents a ‘port’ through which the application communicates with the outside world. Ports are simply interfaces defined by the domain that specify how external components can interact with the core business logic.

Adapters implement these ports to connect external technologies to the domain. There are two types: primary (driving) adapters that drive the application (like REST controllers or CLI interfaces) and secondary (driven) adapters that are driven by the application (like database repositories or messaging systems).

This separation enables the domain model to remain pure and focused on business rules without contamination from technical concerns. It also facilitates testing and makes it easier to swap out infrastructure components without affecting business logic.

``` java
// Domain - Core business logic  
public interface ProductService {  
    Product findById(String productId);  
    void saveProduct(Product product);  
}
```

``` java
// Port - Interface defined by the domain  
public interface ProductRepository {  
    Optional<Product> findById(String id);  
    void save(Product product);  
}  
// Adapter - Implementation connecting to external system  
public class JpaProductRepository implements ProductRepository {  
    private final ProductJpaEntity productJpaEntity;  
      
    @Override  
    public Optional<Product> findById(String id) {  
        // Database-specific code to fetch a product  
        return productJpaEntity.findById(id)  
               .map(this::mapToDomainEntity);  
    }  
      
    @Override  
    public void save(Product product) {  
        // Database-specific code to save a product  
        productJpaEntity.save(mapToJpaEntity(product));  
    }  
      
    // Mapping methods...  
}
```

## Tips

- Keep domain logic pure by pushing all infrastructure and framework concerns to the adapters
- Use dependency inversion to ensure the domain defines the contracts that adapters must implement
- Create clear boundaries between the application layers using well-defined interfaces
- Start with domain modeling rather than focusing on technical details

## Structuring Your Application: Ports and Adapters in Practice

This section demonstrates how to structure a Java application following the Hexagonal Architecture pattern, with practical examples of ports and adapters implementation.

The hexagonal architecture pattern organizes code by separating the business logic (domain) from external dependencies through ports and adapters. Ports define interfaces that the domain requires or exposes, while adapters implement these interfaces to connect with external systems.

In Java, ports are typically defined as interfaces in the domain layer. Primary (driving) ports represent use cases that can be triggered from outside the application, while secondary (driven) ports define what the domain needs from external systems.

Adapters are concrete implementations of these ports. Primary adapters convert external requests into calls to domain services, while secondary adapters translate domain requests into interactions with external resources like databases or web services.

A well-structured Java hexagonal application typically follows a package structure that clearly separates domain, ports, and adapters. The domain remains completely isolated, with no dependencies on frameworks or external systems.

```java
// Domain model - free of external dependencies  
public class Customer {  
    private CustomerId id;  
    private String name;  
    private EmailAddress email;  
      
    // Domain behavior...  
}
```

```java
// Primary port (use case interface)  
public interface CustomerService {  
    Customer findById(CustomerId id);  
    void register(Customer customer);  
}  
  
// Secondary port (repository interface)  
public interface CustomerRepository {  
    Optional<Customer> findById(CustomerId id);  
    void save(Customer customer);  
}
```

```java
// Primary adapter (REST controller)  
@RestController  
public class CustomerController {  
    private final CustomerService customerService;  
      
    public CustomerController(CustomerService customerService) {  
        this.customerService = customerService;  
    }  
      
    @GetMapping("/customers/{id}")  
    public CustomerResponse getCustomer(@PathVariable String id) {  
        Customer customer = customerService.findById(new CustomerId(id));  
        return new CustomerResponse(customer);  
    }  
}  
  
// Secondary adapter (repository implementation)  
@Repository  
public class JpaCustomerRepository implements CustomerRepository {  
    private final CustomerJpaRepository repository;  
      
    public JpaCustomerRepository(CustomerJpaRepository repository) {  
        this.repository = repository;  
    }  
      
    @Override  
    public Optional<Customer> findById(CustomerId id) {  
        return repository.findById(id.value())  
            .map(this::toDomain);  
    }  
}
```

## Tips

- Keep domain models free from annotations or dependencies related to frameworks like JPA or Spring.
- Use dependency injection to wire adapters to ports, allowing for easy swapping of implementations.
- Name your packages to clearly indicate domain, ports (primary/secondary), and adapters (primary/secondary).
- Consider implementing the Command pattern for primary ports to encapsulate use cases more cleanly.

## Implementing the Domain Layer: Business Logic Isolation

The domain layer is the core of hexagonal architecture, containing business logic isolated from external concerns and technical implementations.

In hexagonal architecture, the domain layer represents the heart of your application, encapsulating the business rules and logic that define your system’s purpose. This layer should be completely isolated from external concerns like databases, user interfaces, or third-party services, allowing it to focus solely on solving business problems.

Domain objects should be rich in behavior rather than being mere data carriers. They should express business rules as methods, validations, and relationships that accurately model the business domain. A well-designed domain model makes the code more readable, maintainable, and aligned with business requirements.

Let’s implement a simple domain model for an e-commerce application to demonstrate these principles. We’ll create an Order entity that contains business logic for adding items, calculating totals, and validating order state transitions.

Notice how our domain model remains free of technical concerns like persistence annotations or framework dependencies. It deals only with business concepts and rules, making it testable in isolation and resistant to changes in external systems or frameworks.

```java
package com.ecommerce.domain;  
  
import java.math.BigDecimal;  
import java.time.LocalDateTime;  
import java.util.ArrayList;  
import java.util.Collections;  
import java.util.List;  
import java.util.UUID;  
  
public class Order {  
    private final UUID id;  
    private final List<OrderItem> items;  
    private OrderStatus status;  
    private final LocalDateTime createdAt;  
    private LocalDateTime updatedAt;  
      
    public Order() {  
        this.id = UUID.randomUUID();  
        this.items = new ArrayList<>();  
        this.status = OrderStatus.CREATED;  
        this.createdAt = LocalDateTime.now();  
        this.updatedAt = this.createdAt;  
    }  
      
    public void addItem(Product product, int quantity) {  
        if (product == null) {  
            throw new IllegalArgumentException("Product cannot be null");  
        }  
          
        if (quantity <= 0) {  
            throw new IllegalArgumentException("Quantity must be positive");  
        }  
          
        if (status != OrderStatus.CREATED) {  
            throw new IllegalStateException("Cannot modify items in " + status + " state");  
        }  
          
        // Check if product already exists in order  
        for (OrderItem item : items) {  
            if (item.getProductId().equals(product.getId())) {  
                item.increaseQuantity(quantity);  
                this.updatedAt = LocalDateTime.now();  
                return;  
            }  
        }  
          
        // Add new item  
        items.add(new OrderItem(product.getId(), product.getName(),   
                product.getPrice(), quantity));  
        this.updatedAt = LocalDateTime.now();  
    }  
      
    public BigDecimal calculateTotal() {  
        return items.stream()  
                .map(OrderItem::calculateSubtotal)  
                .reduce(BigDecimal.ZERO, BigDecimal::add);  
    }  
      
    public void submit() {  
        if (items.isEmpty()) {  
            throw new IllegalStateException("Cannot submit empty order");  
        }  
          
        if (status != OrderStatus.CREATED) {  
            throw new IllegalStateException("Cannot submit order in " + status + " state");  
        }  
          
        status = OrderStatus.SUBMITTED;  
        updatedAt = LocalDateTime.now();  
    }  
      
    // Getters  
    public UUID getId() { return id; }  
    public List<OrderItem> getItems() { return Collections.unmodifiableList(items); }  
    public OrderStatus getStatus() { return status; }  
    public LocalDateTime getCreatedAt() { return createdAt; }  
    public LocalDateTime getUpdatedAt() { return updatedAt; }  
      
    public enum OrderStatus {  
        CREATED, SUBMITTED, PAID, SHIPPED, DELIVERED, CANCELLED  
    }  
}
```

## Tips

- Separate business logic validation from technical validation to maintain clean domain boundaries.
- Use value objects for immutable concepts that don’t have identity, like Money or Address.
- Apply Domain-Driven Design principles to create a ubiquitous language shared between developers and domain experts.
- Write unit tests for your domain models without mocking any infrastructure components.

## Building Ports: Defining Your Application’s Interfaces

Ports are the interfaces that define how the domain interacts with the outside world, acting as contracts between the core business logic and external adapters.

In Hexagonal Architecture, ports represent the boundary of your application’s core domain. They define a set of operations that must be implemented by adapters, creating a clear separation between what your application needs and how those needs are satisfied.

Primary (or driving) ports are interfaces that expose the functionality of your application to the outside world. These are typically service interfaces that define use cases and are implemented by the application’s core logic.

Secondary (or driven) ports are interfaces that your application core requires to fulfill its responsibilities. These ports define what your application needs from external systems, such as databases, message queues, or third-party services.

When designing ports, focus on domain language rather than technical implementation details. A well-designed port should reflect business operations and hide the complexity of how those operations are actually performed by adapters.

```java
// Primary (driving) port - used by UI/API to interact with the application  
public interface OrderService {  
    OrderDTO createOrder(CreateOrderCommand command);  
    Collection<OrderDTO> getOrdersForCustomer(CustomerId customerId);  
    void cancelOrder(OrderId orderId);  
}  
  
// Secondary (driven) port - needed by the application to persist orders  
public interface OrderRepository {  
    void save(Order order);  
    Optional<Order> findById(OrderId id);  
    Collection<Order> findByCustomerId(CustomerId customerId);  
    void delete(OrderId id);  
}
```

## Tips

- Keep ports focused on domain concepts rather than technical details
- Name your ports according to their role in the domain, not their technical implementation
- Design ports with testability in mind — they make it easy to mock dependencies
- Consider using Java’s sealed interfaces (Java 17+) to restrict which classes can implement your ports

## Creating Adapters: Connecting to External Systems

Adapters form the bridge between your domain core and external systems, implementing ports to translate between domain objects and external data representations.

In Hexagonal Architecture, adapters are responsible for implementing the port interfaces defined by the domain. They handle the translation between the domain model’s pure business concepts and the specific requirements of external technologies.

Primary (or driving) adapters accept input from outside the application and translate it into domain method calls. Common examples include REST controllers, GraphQL resolvers, or CLI interfaces that convert external requests into domain operations.

Secondary (or driven) adapters implement the repository interfaces defined by the domain to connect with external systems like databases, message queues, or third-party APIs. They translate domain objects into the format required by the external system and vice versa.

By isolating these integration concerns in adapter classes, you maintain a clean domain model that remains free of infrastructure details. This separation allows you to easily swap out one technology for another without affecting your core business logic.

```java
// Primary Adapter: REST Controller  
public class ProductRestController {  
    private final ProductService productService;  
      
    public ProductRestController(ProductService productService) {  
        this.productService = productService;  
    }  
      
    @PostMapping("/products")  
    public ResponseEntity<ProductDto> createProduct(@RequestBody ProductDto productDto) {  
        // Convert DTO to domain object  
        Product product = new Product(productDto.getName(), productDto.getPrice());  
          
        // Call domain service  
        Product savedProduct = productService.createProduct(product);  
          
        // Convert domain object back to DTO  
        ProductDto responseDto = new ProductDto(savedProduct.getId(),   
                                               savedProduct.getName(),   
                                               savedProduct.getPrice());  
        return ResponseEntity.ok(responseDto);  
    }  
}
```

```java
// Secondary Adapter: Repository Implementation  
public class JpaProductRepository implements ProductRepository {  
    private final SpringDataProductRepository repository;  
      
    public JpaProductRepository(SpringDataProductRepository repository) {  
        this.repository = repository;  
    }  
      
    @Override  
    public Product save(Product product) {  
        // Convert domain object to entity  
        ProductEntity entity = new ProductEntity();  
        entity.setName(product.getName());  
        entity.setPrice(product.getPrice());  
          
        // Save using Spring Data JPA  
        ProductEntity savedEntity = repository.save(entity);  
          
        // Convert entity back to domain object  
        return new Product(savedEntity.getId(),  
                          savedEntity.getName(),  
                          savedEntity.getPrice());  
    }  
      
    @Override  
    public Optional<Product> findById(String id) {  
        return repository.findById(id)  
                .map(entity -> new Product(  
                    entity.getId(),  
                    entity.getName(),  
                    entity.getPrice()  
                ));  
    }  
}
```

## Tips

- Keep adapter classes focused on translation logic between the domain and external systems, avoiding any business logic.
- Use Data Transfer Objects (DTOs) in primary adapters to isolate your domain model from API-specific concerns.
- Consider using mapper libraries like MapStruct for complex object transformations between domain objects and external representations.
- Test adapters independently from your domain to verify they correctly handle the translation between formats.

## Testing Strategies in Hexagonal Architecture

Testing in Hexagonal Architecture becomes more straightforward as components are naturally decoupled, allowing for focused and comprehensive test coverage across all layers.

The modular nature of Hexagonal Architecture makes testing significantly more manageable compared to traditional architectures. With clearly defined boundaries between the domain, ports, and adapters, each component can be tested in isolation with appropriate mocks or stubs.

Domain testing focuses on business rules without dependencies on external systems. Since the domain is completely isolated from infrastructure concerns, unit tests can validate business logic with high confidence and without complex setup.

Port testing verifies that the interfaces between the domain and the outside world are properly defined. Mock implementations of ports allow testing how the domain interacts with external systems without actually connecting to them.

Adapter testing ensures that implementations correctly transform between external formats and domain models. These tests verify that adapters properly conform to their respective port interfaces and correctly handle external system interactions.

Integration testing validates that the real implementations of adapters work correctly with external systems. These tests confirm that the application can properly communicate with databases, message queues, or external services.

```java
// Domain test example  
@Test  
class ProductServiceTest {  
    private ProductRepository mockRepository;  
    private ProductService service;  
      
    @BeforeEach  
    void setup() {  
        mockRepository = Mockito.mock(ProductRepository.class);  
        service = new ProductServiceImpl(mockRepository);  
    }  
      
    @Test  
    void shouldCalculateDiscountForPremiumCustomers() {  
        // Given  
        Product product = new Product("1", "Test Product", new BigDecimal("100.00"));  
        Customer premiumCustomer = new PremiumCustomer("C1", "John Doe");  
        Mockito.when(mockRepository.findById("1")).thenReturn(Optional.of(product));  
          
        // When  
        BigDecimal price = service.calculatePriceFor("1", premiumCustomer);  
          
        // Then  
        assertEquals(new BigDecimal("90.00"), price);  
    }  
}
```

```java
// Adapter test example  
@ExtendWith(SpringExtension.class)  
class JpaProductRepositoryAdapterTest {  
      
    @MockBean  
    private ProductJpaRepository jpaRepository;  
      
    @Autowired  
    private ProductRepositoryAdapter adapter;  
      
    @Test  
    void shouldMapJpaEntityToDomainModel() {  
        // Given  
        ProductEntity entity = new ProductEntity();  
        entity.setId("1");  
        entity.setName("Test Product");  
        entity.setPrice(100.00);  
        Mockito.when(jpaRepository.findById("1")).thenReturn(Optional.of(entity));  
          
        // When  
        Optional<Product> result = adapter.findById("1");  
          
        // Then  
        assertTrue(result.isPresent());  
        Product product = result.get();  
        assertEquals("1", product.getId());  
        assertEquals("Test Product", product.getName());  
        assertEquals(new BigDecimal("100.00"), product.getPrice());  
    }  
}
```

## Tips

- Use dependency injection to easily swap real adapters for test doubles during unit tests
- Create test-specific adapter implementations to simulate various scenarios and edge cases
- Test domain logic and adapters separately before combining them in integration tests
- Use behavior-driven testing frameworks to align tests with business requirements

## Real-world Example: Building a Spring Boot Application with Hexagonal Architecture

This section demonstrates a practical implementation of hexagonal architecture in a Spring Boot application, showing how to structure code for maintainability and testability.

Let’s build a sample Spring Boot application that follows hexagonal architecture principles. We’ll create a simple product management system with core domain logic isolated from external concerns.

First, we’ll establish our domain layer with business entities and ports. The domain will define what our application does without coupling to specific technologies.

Next, we’ll implement the primary and secondary adapters. Primary adapters like REST controllers will drive our application, while secondary adapters will handle persistence using Spring Data JPA.

Finally, we’ll wire everything together using Spring’s dependency injection capabilities, showcasing how hexagonal architecture allows for clean separation of concerns while leveraging Spring Boot’s convenience.

```java
// Domain Layer - Entity  
public class Product {  
    private String id;  
    private String name;  
    private BigDecimal price;  
      
    // Constructor, getters, setters  
}  
  
// Domain Layer - Port (Primary/Driving Port)  
public interface ProductService {  
    List<Product> getAllProducts();  
    Product getProductById(String id);  
    Product createProduct(Product product);  
    void deleteProduct(String id);  
}  
  
// Domain Layer - Port (Secondary/Driven Port)  
public interface ProductRepository {  
    List<Product> findAll();  
    Optional<Product> findById(String id);  
    Product save(Product product);  
    void deleteById(String id);  
}
```

```java
// Application Layer - Service Implementation  
@Service  
public class ProductServiceImpl implements ProductService {  
    private final ProductRepository productRepository;  
      
    public ProductServiceImpl(ProductRepository productRepository) {  
        this.productRepository = productRepository;  
    }  
      
    @Override  
    public List<Product> getAllProducts() {  
        return productRepository.findAll();  
    }  
      
    // Other methods implementation  
}  
  
// Infrastructure Layer - JPA Adapter  
@Repository  
public class JpaProductRepositoryAdapter implements ProductRepository {  
    private final SpringDataJpaProductRepository repository;  
      
    // Implementation using Spring Data JPA  
}  
  
// Infrastructure Layer - REST Adapter  
@RestController  
@RequestMapping("/api/products")  
public class ProductController {  
    private final ProductService productService;  
      
    public ProductController(ProductService productService) {  
        this.productService = productService;  
    }  
      
    @GetMapping  
    public ResponseEntity<List<Product>> getAllProducts() {  
        return ResponseEntity.ok(productService.getAllProducts());  
    }  
      
    // Other endpoints  
}
```

## Tips

- Keep your domain logic completely free of frameworks and external dependencies
- Use interfaces (ports) to define the boundaries between your domain and the outside world
- Implement proper dependency injection to ensure your domain doesn’t depend on adapters
- Write tests at different levels: unit tests for domain logic, integration tests for adapters

## Conclusion: Benefits and Considerations for Adoption

Hexagonal Architecture provides significant benefits for building maintainable Java applications by clearly separating domain logic from technical concerns, though it requires thoughtful implementation.

Throughout this guide, we’ve explored how Hexagonal Architecture enables the creation of clean, maintainable, and testable Java applications. By isolating the domain core from external dependencies through ports and adapters, we’ve seen how this architectural pattern promotes a truly domain-centric design that remains stable even as infrastructure components evolve.

The key advantage of Hexagonal Architecture lies in its ability to defer technical decisions. As demonstrated in our examples, changes to databases, UI frameworks, or integration points require minimal impact on the domain logic, allowing teams to focus on business problems rather than technical challenges.

Testing becomes substantially easier with this architecture. Domain logic can be tested in isolation with mock adapters, while integration tests can verify the correct functioning of the adapters without touching domain logic. This separation streamlines the testing process and improves test reliability.

However, adopting Hexagonal Architecture isn’t without challenges. The increased number of interfaces and classes introduces additional complexity, especially for simpler applications. Teams new to the pattern may struggle with determining appropriate boundaries between the domain and adapters.

Despite these challenges, the long-term benefits of maintainability, adaptability, and clear separation of concerns typically outweigh the initial investment for medium to large applications with complex business logic or those expected to evolve significantly over time.

```java
// Before Hexagonal Architecture: Domain logic mixed with infrastructure  
public class OrderService {  
    private final JpaOrderRepository repository; // Direct dependency on infrastructure  
    private final EmailSender emailSender;      // Direct dependency on infrastructure  
      
    public void placeOrder(Order order) {  
        repository.save(order);       // Domain logic tightly coupled with JPA  
        emailSender.send("Order " + order.getId() + " has been placed");  
    }  
}  
  
// After Hexagonal Architecture: Clean domain model  
public class OrderService {  
    private final OrderRepository repository;       // Interface (port)  
    private final NotificationService notifier;     // Interface (port)  
      
    public void placeOrder(Order order) {  
        // Pure domain logic with no infrastructure concerns  
        if (order.isValid()) {  
            order.markAsPlaced();  
            repository.save(order);  
            notifier.orderPlaced(order);  
        } else {  
            throw new InvalidOrderException("Cannot place invalid order");  
        }  
    }  
}
```

## Tips

- Start small by identifying a bounded context in your application where clean architecture would provide the most value.
- Use dependency injection frameworks like Spring to simplify wiring of adapters to ports.
- Consider using packaging structures that reflect the hexagonal layers to reinforce the architecture.
- Create documentation that explicitly identifies ports, adapters, and the domain model to help team members navigate the architecture.

If you are looking for a more advanced implementation of the Hexagonal Architecture with clear guidelines and design solutions to decouple by design, then check my book:

[

## Decoupling by Design: A Practitioner's Guide to Hexagonal Architecture

### Battle-Tested Strategies for Building Flexible, Maintainable Software Systems This book is 100% complete You pay You…

leanpub.com



](https://leanpub.com/decouplingbydesignapractitionersguidetohexagonalarchitecture?source=post_page-----37c7f8ca3e80---------------------------------------)

[  
](https://medium.com/tag/java?source=post_page-----37c7f8ca3e80---------------------------------------)
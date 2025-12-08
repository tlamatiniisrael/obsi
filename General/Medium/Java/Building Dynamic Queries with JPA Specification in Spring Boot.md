
![Building Dynamic Queries with JPA Specification in Spring Boot](https://miro.medium.com/v2/resize:fit:940/1*-xDMCBFoMvDz-ZZAZH9TSg.png)

Learn how to create flexible, reusable, and type-safe database queries using the JPA Specification API.

When building search functionality in your applications, users often want to filter results using multiple criteria — like name, department, or salary range. Writing one method for every combination of filters is hard to read, hard to maintain, and not scalable.

That’s where JPA Specification comes in — it helps you write dynamic, flexible, and reusable queries easily.

## Step 1: The Problem with Static Queries

Using Spring Data JPA, you might start with methods like:

```java
List<Employee> findByName(String name);
List<Employee> findByDepartment(String department);
List<Employee> findBySalaryBetween(Double min, Double max);
List<Employee> findByNameAndDepartment(String name, String department);
```

But as soon as you add more filters — like job title or hire date — the number of methods explodes. Maintaining these becomes a nightmare.

You need a cleaner, reusable solution.

## Step 2: Understanding JPA Specification

JPA Specification builds queries dynamically using Java code instead of hardcoded strings.  
It relies on this simple interface:

```java
public interface Specification<T> {
   Predicate toPredicate(Root<T> root, CriteriaQuery<?> query, CriteriaBuilder builder);
}
```

- **`Root<T>`**: The entity (like Employee)
- **`CriteriaQuery<?>`**: The query itself
- **`CriteriaBuilder`**: Helps you build conditions such as equal, like, and between

## Step 3: Create the Entity

Let’s use an Employee example.

```java
@Entity
@Table
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Employee {
   @Id
   @GeneratedValue(strategy = GenerationType.IDENTITY)
   private Long id;

   private String name;
   private String department;
   private Double salary;
}
```

## Step 4: Set Up the Repository

We extend **`JpaSpecificationExecutor`** along with **`JpaRepository`** to use Specifications.

```java
public interface EmployeeRepository 
       extends JpaRepository<Employee, Long>, JpaSpecificationExecutor<Employee> {
}
```

## Step 5: Build Specification Helpers

Here’s where we define reusable conditions.

```java
public class EmployeeSpecifications {

   public static Specification<Employee> hasNameLike(String name) {
       return (root, query, cb) ->
               cb.like(cb.lower(root.get(\name\)), \%\+ name.toLowerCase() + \%\);
   }

   public static Specification<Employee> inDepartment(String department) {
       return (root, query, cb) ->
               cb.equal(root.get(\department\), department);
   }

   public static Specification<Employee> hasSalaryBetween(Double min, Double max) {
       return (root, query, cb) ->
               cb.between(root.get(\salary\), min, max);
   }
}
```

Each method returns a Specification that can be combined dynamically at runtime.

## Step 6: Combine Specifications in the Service Layer

You can now combine filters depending on which search parameters are provided.

```java
@Service
@RequiredArgsConstructor
public class EmployeeService {

   private final EmployeeRepository employeeRepository;

   public List<Employee> searchEmployees(String name, String department, Double minSalary, Double maxSalary) {
       Specification<Employee> spec = Specification.where(null);

       if (name != null && !name.isEmpty()) {
           spec = spec.and(EmployeeSpecifications.hasNameLike(name));
       }

       if (department != null && !department.isEmpty()) {
           spec = spec.and(EmployeeSpecifications.inDepartment(department));
       }

       if (minSalary != null && maxSalary != null) {
           spec = spec.and(EmployeeSpecifications.hasSalaryBetween(minSalary, maxSalary));
       }

       return employeeRepository.findAll(spec);
   }
}
```

Now your code only includes filters the user actually provides — no more endless repository methods.

## Step 7: Create the Controller

This controller accepts optional query parameters and automatically filters results.

```java
@RestController
@RequestMapping(\/employees\)
@RequiredArgsConstructor
public class EmployeeController {

   private final EmployeeService employeeService;

   @GetMapping
   public ResponseEntity<List<Employee>> getEmployees(
           @RequestParam(required = false) String name,
           @RequestParam(required = false) String department,
           @RequestParam(required = false) Double minSalary,
           @RequestParam(required = false) Double maxSalary) {

       return ResponseEntity.ok(employeeService.searchEmployees(name, department, minSalary, maxSalary));
   }
}
```

## Step 8: Test the API

Once your application is running, try these example calls:

- All employees: `GET /employees`
- Filter by name: `GET /employees?name=sunil`
- Filter by department: `GET /employees?department=HR`
- Filter by salary range: `GET /employees?minSalary=30000&maxSalary=60000`

Each endpoint applies only the filters you provide.

## Step 9: Key Advantages

- **Maintainable:** Add new filters without changing repository methods.
- **Reusable:** Reuse specifications across services.
- **Type-safe:** Compile-time validation avoids common query errors.
- **Efficient:** Combine filters cleanly at runtime.
- **Testable:** Each specification can be tested independently.

JPA Specification takes the pain out of complex filtering.  
Instead of writing multiple query methods, you create small, reusable specifications that can be combined dynamically.  
Once you understand it, you’ll never want to go back to static queries. It’s cleaner, more scalable, and perfect for building professional-grade search features in Spring Boot.

**Thanks for reading — see you in the next guide! 💗**

**As always, feel free to drop a comment if I’ve made any mistakes or if there’s any way I can help!**
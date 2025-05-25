# Chapter 10 - Unit Test

## Summary

This chapter focuses on class design principles and organization in object-oriented programming. The key takeaways are:

1. **Class Organization**

   - Follow a consistent structure: constants → static variables → instance variables → public methods → private methods
   - Keep related code together for better readability

2. **Class Size**

   - Classes should be small
   - Even smaller than you think
   - Measure by responsibilities, not lines of code

3. **Single Responsibility Principle (SRP)**

   - Each class should have only one reason to change
   - Split large classes into smaller, focused ones
   - If a class name is ambiguous, it's likely doing too much

4. **Cohesion**

   - Methods should work with the class's instance variables
   - High cohesion means methods and variables work together as a logical whole
   - Low cohesion often indicates need for class splitting

5. **Change Management**

   - Design for change using the Open-Closed Principle
   - Classes should be open for extension but closed for modification
   - Use abstractions (interfaces) to isolate from concrete implementations

6. **Testing Considerations**
   - Make testing easier by keeping classes small and focused
   - Use protected access when necessary for testing
   - Depend on abstractions to make testing simpler

The chapter emphasizes that clean class design leads to maintainable, testable, and flexible code. Small, focused classes with clear responsibilities are easier to understand, modify, and test.

## Details

### Class Organization

- Variables should be listed first in a class:
  1. Public static constants
  2. Private static variables
  3. Private instance variables
- Public functions should follow variables
- Private utility functions should be placed right after the public function that calls them

- Example:

```java
public class UserService {
    // 1. Public static constants
    public static final int MAX_RETRY_ATTEMPTS = 3;

    // 2. Private static variables
    private static Logger logger = LoggerFactory.getLogger(UserService.class);

    // 3. Private instance variables
    private UserRepository userRepository;
    private EmailService emailService;

    // 4. Public functions
    public User createUser(UserDTO userDTO) {
        validateUser(userDTO);
        return saveUser(userDTO);
    }

    // 5. Private utility functions (placed right after the public function that uses them)
    private void validateUser(UserDTO userDTO) {
        // validation logic
    }

    private User saveUser(UserDTO userDTO) {
        // save logic
    }
}
```

### Encapsulation

- Keep variables and utility functions private by default
- Make them protected only when necessary for testing
- Example:

```java
public class PaymentProcessor {
    private double amount;
    private String currency;

    // Private by default
    private void validateAmount() {
        if (amount <= 0) {
            throw new IllegalArgumentException("Amount must be positive");
        }
    }

    // Protected for testing
    protected void setAmount(double amount) {
        this.amount = amount;
    }
}
```

### Classes Should Be Small

- First Rule: Classes should be small
- Second Rule: Classes should be smaller than the first rule
- Measure class size by responsibilities, not lines of code
- Example of a class with too many responsibilities:

```java
// BAD: Too many responsibilities
public class SuperDashboard {
    public void showObject(MetaObject object) { /* ... */ }
    public void showProgress(String s) { /* ... */ }
    public void updateStatus(int dotPos, int markPos) { /* ... */ }
    public void setMode(int mode) { /* ... */ }
    public void runProject() { /* ... */ }
    // ... many more methods
}

// GOOD: Split into focused classes
public class DashboardUI {
    public void showObject(MetaObject object) { /* ... */ }
    public void showProgress(String s) { /* ... */ }
}

public class ProjectManager {
    public void updateStatus(int dotPos, int markPos) { /* ... */ }
    public void runProject() { /* ... */ }
}
```

### The Single Responsibility Principle (SRP)

- A class should have only one reason to change
- Example:

```java
// BAD: Multiple responsibilities
public class UserManager {
    public void createUser() { /* ... */ }
    public void sendEmail() { /* ... */ }
    public void generateReport() { /* ... */ }
}

// GOOD: Single responsibility
public class UserService {
    public void createUser() { /* ... */ }
}

public class EmailService {
    public void sendEmail() { /* ... */ }
}

public class ReportGenerator {
    public void generateReport() { /* ... */ }
}
```

### Cohesion

- Classes should have a small number of instance variables
- Methods should manipulate one or more of those variables
- Example of a cohesive class:

```java
public class Stack {
    private int topOfStack = 0;
    private List<Integer> elements = new LinkedList<>();

    public int size() {
        return topOfStack;
    }

    public void push(int element) {
        topOfStack++;
        elements.add(element);
    }

    public int pop() {
        if (topOfStack == 0) throw new EmptyStackException();
        int element = elements.get(--topOfStack);
        elements.remove(topOfStack);
        return element;
    }
}
```

### Organizing for Change

- Design classes to minimize the impact of changes
- Use the Open-Closed Principle (OCP)
- Example:

```java
// BAD: Class needs modification for new features
public class Sql {
    public String create() { /* ... */ }
    public String select() { /* ... */ }
    public String insert() { /* ... */ }
}

// GOOD: Open for extension, closed for modification
public abstract class Sql {
    protected String table;
    protected Column[] columns;
    abstract public String generate();
}

public class CreateSql extends Sql {
    @Override
    public String generate() { /* ... */ }
}

public class SelectSql extends Sql {
    @Override
    public String generate() { /* ... */ }
}
```

### Isolating from Change

- Depend on abstractions, not concrete implementations
- Example:

```java
// BAD: Direct dependency on concrete class
public class Portfolio {
    private TokyoStockExchange exchange;

    public Portfolio() {
        this.exchange = new TokyoStockExchange();
    }
}

// GOOD: Dependency on abstraction
public interface StockExchange {
    Money currentPrice(String symbol);
}

public class Portfolio {
    private StockExchange exchange;

    public Portfolio(StockExchange exchange) {
        this.exchange = exchange;
    }
}
```

## Key Principles

1. Keep classes small and focused
2. Follow Single Responsibility Principle
3. Maintain high cohesion
4. Design for change
5. Depend on abstractions
6. Organize code for readability and maintainability

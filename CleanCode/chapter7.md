# Chapter 7 - Exception

## Chapter Summary

This chapter focuses on best practices for error handling in software development. Here are the key points:

1. **Exception Handling Fundamentals**

   - Use exceptions rather than return codes for error handling
   - Write try-catch-finally statements first when dealing with potential exceptions
   - Provide meaningful context with exceptions
   - Avoid using exceptions for control flow

2. **Checked vs Unchecked Exceptions**

   - Checked exceptions can violate the Open/Closed Principle
   - They create a cascade of changes through the call stack
   - Modern languages (C#, Python, Ruby) don't use checked exceptions
   - Consider using unchecked exceptions for better encapsulation

3. **Null Handling**

   - Never return null from methods
   - Never pass null to methods
   - Use special case objects (like empty collections) instead of null
   - Consider using language features that prevent null (like Optional)

4. **Exception Class Design**

   - Define exceptions based on how they'll be caught, not their source
   - Wrap third-party APIs to simplify exception handling
   - Use a single exception type when the handling is similar
   - Create meaningful exception hierarchies

5. **Special Case Pattern**

   - Use special case objects instead of exceptions for control flow
   - Encapsulate exceptional behavior in special case classes
   - Makes code cleaner and more maintainable
   - Reduces cognitive load on developers

6. **Best Practices**
   - Keep error handling code separate from business logic
   - Use meaningful error messages
   - Log appropriate context with exceptions
   - Consider the caller's needs when designing exceptions
   - Wrap third-party APIs to minimize dependencies

The chapter emphasizes writing clean, maintainable code by properly handling errors and exceptions while keeping the business logic clear and uncluttered.

## Examples

### Define Exception Classes in Terms of a Caller's Needs

When defining exception classes, focus on how they will be caught rather than their source or type. This leads to cleaner, more maintainable code.

Bad Example (Too Many Specific Exceptions):

```java
try {
    port.open();
} catch (DeviceResponseException e) {
    reportPortError(e);
    logger.log("Device response exception", e);
} catch (ATM1212UnlockedException e) {
    reportPortError(e);
    logger.log("Unlock exception", e);
} catch (GMXError e) {
    reportPortError(e);
    logger.log("Device response exception");
}
```

Better Approach (Wrapper with Common Exception):

```java
// Wrapper class
public class LocalPort {
    private ACMEPort innerPort;

    public LocalPort(int portNumber) {
        innerPort = new ACMEPort(portNumber);
    }

    public void open() {
        try {
            innerPort.open();
        } catch (DeviceResponseException | ATM1212UnlockedException | GMXError e) {
            throw new PortDeviceFailure(e);
        }
    }
}

// Usage
try {
    port.open();
} catch (PortDeviceFailure e) {
    reportError(e);
    logger.log(e.getMessage(), e);
}
```

Benefits of Wrapping Third-Party APIs:

1. Minimizes dependencies on third-party code
2. Makes it easier to switch libraries
3. Simplifies testing through mocking
4. Allows you to define your own API design
5. Reduces exception handling complexity

### Special Case Pattern

Instead of using exceptions for control flow, use the Special Case Pattern to handle exceptional cases gracefully.

Bad Example (Exception for Control Flow):

```java
try {
    MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
    m_total += expenses.getTotal();
} catch(MealExpensesNotFound e) {
    m_total += getMealPerDiem();
}
```

Better Approach (Special Case Pattern):

```java
// Special case class
public class PerDiemMealExpenses implements MealExpenses {
    public int getTotal() {
        return getMealPerDiem(); // Return default per diem amount
    }
}

// Usage becomes clean and simple
MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
m_total += expenses.getTotal();
```

Benefits of Special Case Pattern:

1. Eliminates exception handling for control flow
2. Makes code more readable and maintainable
3. Encapsulates special case behavior
4. Follows the principle of least surprise
5. Reduces cognitive load on developers

### Checked Exceptions vs Unchecked Exceptions

Checked exceptions were introduced in Java as a way to force developers to handle potential errors by making them part of the method signature. While this seemed like a good idea initially, it has several drawbacks:

1. **Violation of Open/Closed Principle**: Changes in low-level exceptions force changes in higher-level method signatures
2. **Cascade of Changes**: Adding a checked exception requires modifying all methods in the call stack
3. **Broken Encapsulation**: All functions in the exception path must know about low-level exception details

Many modern languages (C#, Python, Ruby) don't use checked exceptions and still produce robust software. Checked exceptions might be useful for critical libraries but often create more problems than they solve in general application development.

Example of the cascade problem:

```java
// Low level function
public void lowLevelFunction() throws DatabaseException {
    // Database operation that might fail
}

// Middle level function must now declare the exception
public void middleLevelFunction() throws DatabaseException {
    lowLevelFunction();
}

// Top level function must also declare the exception
public void topLevelFunction() throws DatabaseException {
    middleLevelFunction();
}
```

Better approach using unchecked exceptions:

```java
// Low level function
public void lowLevelFunction() {
    try {
        // Database operation
    } catch (SQLException e) {
        throw new DatabaseException("Failed to access database", e);
    }
}

// Middle and top level functions don't need to know about the exception
public void middleLevelFunction() {
    lowLevelFunction();
}

public void topLevelFunction() {
    middleLevelFunction();
}
```

Each exception that you throw should provide enough context to determine the source and location of an error.

Create informative error messages and pass them along with your exceptions. Mention the operation that failed and the type of failure. If you are logging in your application, pass along enough information to be able to log the error in your catch.

### Some Guidelines

#### Use Exceptions Rather Than Return Codes

In those languages the techniques for handling and reporting errors were limited. You either set an error flag or returned an error code that the caller could check

#### Write Your Try-Catch-Finally Statement First

It is good practice to start with a try-catch-finally statement when you are writing code that could throw exceptions. This helps you define what the user of that code should expect, no matter what goes wrong with the code that is executed in the try.

### Null (Don't Pass Null & Don't Pass Null)

Returning null from methods creates several problems:

1. Forces callers to add null checks throughout their code
2. Makes code harder to read with nested null checks
3. One missing null check can crash the application
4. Creates unnecessary complexity

Bad Example:

```java
public void registerItem(Item item) {
    if (item != null) {
        ItemRegistry registry = persistentStore.getItemRegistry();
        if (registry != null) {
            Item existing = registry.getItem(item.getID());
            if (existing.getBillingPeriod().hasRetailOwner()) {
                existing.register(item);
            }
        }
    }
}
```

Better Approaches:

1. Return Special Case Objects:

```java
// Instead of returning null
public List<Employee> getEmployees() {
    if (/* no employees */) {
        return Collections.emptyList(); // Special case object
    }
    return employeeList;
}

// Usage becomes cleaner
List<Employee> employees = getEmployees();
for (Employee e : employees) {
    totalPay += e.getPay();
}
```

2. Throw Exceptions:

```java
public Item getItem(String id) {
    Item item = itemRepository.findById(id);
    if (item == null) {
        throw new ItemNotFoundException("Item not found with id: " + id);
    }
    return item;
}
```

Passing null to methods is even worse than returning null because:

1. It's harder to track where nulls come from
2. It violates the principle of least surprise
3. Most languages don't have good ways to handle null parameters
4. It leads to defensive programming and complex null checks

Bad Example:

```java
public class MetricsCalculator {
    public double xProjection(Point p1, Point p2) {
        return (p2.x - p1.x) * 1.5;  // Will throw NullPointerException if p1 or p2 is null
    }
}
```

Better Approaches:

1. Use Assertions (for development):

```java
public class MetricsCalculator {
    public double xProjection(Point p1, Point p2) {
        assert p1 != null : "p1 should not be null";
        assert p2 != null : "p2 should not be null";
        return (p2.x - p1.x) * 1.5;
    }
}
```

2. Throw Custom Exceptions:

```java
public class MetricsCalculator {
    public double xProjection(Point p1, Point p2) {
        if (p1 == null || p2 == null) {
            throw new IllegalArgumentException("Points cannot be null");
        }
        return (p2.x - p1.x) * 1.5;
    }
}
```

3. Use Optional Parameters (in languages that support it):

```java
public class MetricsCalculator {
    public double xProjection(Optional<Point> p1, Optional<Point> p2) {
        return p1.flatMap(point1 ->
            p2.map(point2 -> (point2.x - point1.x) * 1.5)
        ).orElseThrow(() -> new IllegalArgumentException("Points cannot be null"));
    }
}
```

Key Principles:

1. Never return null from methods
2. Use special case objects (like empty collections) instead of null
3. Throw exceptions when a value is required but not found
4. Never pass null to methods
5. Use clear parameter validation
6. Consider using language features that help prevent null (like Optional in Java)

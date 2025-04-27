# Chapter 8 - Boundary

## Chapter Summary

This chapter discusses how to handle code boundaries in software systems, particularly when working with third-party code and external dependencies. Key points include:

1. **Interface Tension**: There's a natural conflict between interface providers (who want broad applicability) and users (who want focused, specific interfaces).

2. **Boundary Management**:

   - Encapsulate third-party code within your own interfaces
   - Create wrapper classes to control and constrain external APIs
   - Hide implementation details and type management

3. **Learning and Testing**:

   - Write "learning tests" to understand third-party code
   - These tests serve as documentation and help catch API changes
   - They provide a safety net for future updates

4. **Working with Unfinished Code**:

   - Create interfaces for modules that don't exist yet
   - This allows for parallel development and testing
   - Maintains control over your codebase

5. **Clean Boundaries**:
   - Good design accommodates change without major rework
   - Protect your investment in external code
   - Make future changes less costly

The chapter emphasizes the importance of proper boundary management to maintain clean, maintainable code when working with external dependencies.

## Examples

We seldom control all the software in our systems. Sometimes we buy third-party pack- ages or use open source. Other times we depend on teams in our own company to produce components or subsystems for us. Somehow we must cleanly integrate this foreign code with our own.

### Using Third-Party Code

There is a natural tension between the provider of an interface and the user of an interface. Providers of third-party packages and frameworks strive for broad applicability so they can work in many environments and appeal to a wide audience. Users, on the other hand, want an interface that is focused on their particular needs. This tension can cause problems at the boundaries of our systems. Example:

```java
Map sensors = new HashMap();
Sensor s = (Sensor)sensors.get(sensorId);
```

VS

```java
public class Sensors {
  private Map sensors = new HashMap();

  public Sensor getById(String id) {
    return (Sensor) sensors.get(id);
  }
  //snip
}
```

The first code exposes the casting in the Map, while the second is able to evolve with very little impact on the rest of the application. The casting and type management is handled inside the Sensors class.

The interface is also tailored and constrained to meet the needs of the application. It results in code that is easier to understand and harder to misuse. The Sensors class can enforce design and business rules.

### Exploring and Learning Boundaries

Third-party code helps us get more functionality delivered in less time. Where do we start when we want to utilize some third-party package? It's not our job to test the third-party code, but it may be in our best interest to write tests for the third-party code we use.

Solution: It's a good idea write some test for learn and understand how to use a third-party code. Newkirk calls such tests learning tests.

### Learning Tests Are Better Than Free

The learning tests end up costing nothing. We had to learn the API anyway, and writing those tests was an easy and isolated way to get that knowledge. The learning tests were precise experiments that helped increase our understanding.

Not only are learning tests free, they have a positive return on investment. When there are new releases of the third-party package, we run the learning tests to see whether there are behavioral differences.

### Using Code That Does Not Yet Exist

There is another kind of boundary that separates the known from the unknown. Sometimes what lies beyond the boundary is unknowable (at least for now), and sometimes we choose not to look beyond it. This situation often arises when working with subsystems that are still under development.

- Benefits of This Approach:

1. **Control**: The interface remains under our control
2. **Clean Code**: Client code stays readable and focused
3. **Testability**: Creates a convenient seam for testing
4. **Adaptability**: Easy to adapt when the real API is defined

- Implementation Strategy:

1. Define the interface you wish you had
2. Create adapter classes when the real API is available
3. Use the adapter pattern to bridge the gap
4. Implement boundary tests to ensure correct API usage

This approach allows teams to:

1. Continue development without blocking
2. Maintain clean, expressive code
3. Easily adapt to API changes
4. Test their code effectively

### Clean Boundaries

Interesting things happen at boundaries. Change is one of those things. Good software designs accommodate change without huge investments and rework. When we use code that is out of our control, special care must be taken to protect our investment and make sure future change is not too costly.

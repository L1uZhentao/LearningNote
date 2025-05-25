# Chapter 11 - Systems

## Quick Summary

This chapter focuses on system-level design principles and architecture. The key takeaways are:

1. **System Construction vs Usage**

   - Separate the construction of a system from its usage
   - Use dependency injection and factories to manage object creation
   - Keep the startup process modular and separate from runtime logic

2. **Architecture Evolution**

   - Systems should grow incrementally like cities
   - Start simple and add complexity as needed
   - Avoid Big Design Up Front (BDUF)
   - Maintain ability to change course based on evolving needs

3. **Cross-Cutting Concerns**

   - Use aspects and aspect-like mechanisms to handle cross-cutting concerns
   - Keep domain logic clean and separate from infrastructure
   - Utilize frameworks like Spring for dependency injection

4. **Modularity and Standards**

   - Build systems with modular domains of concern
   - Use standards wisely when they add demonstrable value
   - Implement domain-specific languages for better communication

5. **Decision Making**
   - Postpone decisions until the last possible moment
   - Make decisions based on current knowledge and requirements
   - Keep systems flexible for future changes

Key Principles:

1. Separate construction from use
2. Build systems incrementally
3. Handle cross-cutting concerns cleanly
4. Use domain-specific languages
5. Make decisions at the last responsible moment
6. Keep systems modular and flexible
7. Use standards wisely
8. Maintain clean architecture at all levels

## Details

### System Construction and Usage

- Separate construction from use:

  - Move construction to `main()` or modules called by `main`
  - Design system assuming objects are already constructed
  - Use dependency injection for object creation

- Example of poor construction:

```java
public Service getService() {
    if (service == null)
        service = new MyServiceImpl(...);
    return service;
}
```

- Better approach using dependency injection:

```java
public class Bank {
    private final Service service;

    public Bank(Service service) {
        this.service = service;
    }
}
```

## Separate Constructing a System from using It

_Software Systems should separate the startup process, when the application objects are constructed and the dependencies are "wired" together, from the runtime logic that takes over after startup_

### Separation from main

One way to separate construction from use is simply to move all aspects of construction to `main`, or modules called by `main`, and to design the rest of the system assuming that all objects have been created constructed and wired up appropriately.

The Abstract Factory Pattern is an option for this kind of approach.

### Dependency Injection

A powerful mechanism for separating construction from use is Dependency Injection (DI), the application of Inversion of control (IoC) to dependency management. Inversion of control moves secondary responsibilities from an object to other objects that are dedicated to the purpose, thereby supporting the Single Responsibility Principle. In context of dependency management, an object should not take responsibility for instantiating dependencies itself. Instead, it, should pass this responsibility to another "authoritative" mechanism, thereby inverting the control. Because setup is a global concern, this authoritative mechanism will usually be either the "main"
routine or a special-purpose _container_.

### Architecture Evolution

- Systems should grow like cities:

  - Start small and simple
  - Add infrastructure as needed
  - Maintain clean separation of concerns
  - Avoid over-engineering

- Key principles:
  - Test-driven architecture
  - Incremental growth
  - Flexible design
  - Minimal coupling

### Cross-Cutting Concerns

- Handle cross-cutting concerns through:

  - Aspect-Oriented Programming (AOP)
  - Java proxies
  - Pure Java AOP frameworks (Spring, JBoss)
  - AspectJ

- Example of clean separation:

```java
@Entity
@Table(name = "BANKS")
public class Bank implements java.io.Serializable {
    @Id
    private int id;

    @Embedded
    private Address address;

    @OneToMany
    private Collection<Account> accounts;
}
```

### Domain-Specific Languages (DSLs)

- Benefits:

  - Minimize communication gap
  - Express domain concepts clearly
  - Raise abstraction level
  - Improve code readability

- Implementation approaches:
  - Small scripting languages
  - APIs in standard languages
  - Structured prose-like syntax

### Decision Making and Standards

- Principles:

  - Postpone decisions until necessary
  - Make decisions based on current knowledge
  - Use standards when they add value
  - Keep systems flexible

- Guidelines:
  - Avoid premature decisions
  - Consider customer feedback
  - Evaluate implementation choices
  - Maintain agility

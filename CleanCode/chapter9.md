# Chapter 9 - Unit Test

## Chapter Summary

This chapter discusses the importance of clean, readable unit tests and provides guidelines for writing effective tests. Key points include:

1. **Test Readability**: The most important aspect of clean tests is readability. Tests should be clear, simple, and express their intent with minimal code.

2. **Test Structure**:

   - Follow the BUILD-OPERATE-CHECK pattern
   - Each test should focus on a single concept
   - Minimize the number of assertions per test

3. **Domain-Specific Testing Language**:

   - Create helper functions and utilities for tests
   - Build a specialized API for testing
   - Evolve the testing language through refactoring

4. **Dual Standard**:

   - Test code can have different standards than production code
   - Focus on readability over efficiency in tests
   - Maintain cleanliness while allowing for less optimal implementations

5. **Best Practices**:
   - One assertion per test when possible
   - Test one concept per test function
   - Use descriptive test names
   - Avoid test code duplication

The chapter emphasizes that clean tests are essential for maintaining a healthy codebase and ensuring long-term project success.

## Details

**T**est
**D**riven
**D**evelopment

### The Three Laws of TDD

- **First Law** You may not write production code until you have written a failing unit test.
- **Second Law** You may not write more of a unit tests than is sufficient to fail, and not comipling is failing.
- **Third Law** You may not write more production code than is sufficient to pass the currently failing tests.

### Some guidelines

#### Clean Tests

If you don't keep your tests clean, you will lose them.

The readability is very important to keep clean your tests.

#### One Assert per test

It's recommendable to maintain only one assert per test, because this helps to maintain each test easy to understand.

#### Single concept per Test

This rule will help you to keep short functions.

- **Write one test per each concept that you need to verify**

### F.I.R.S.T

- **Fast** Test should be fast.
- **Independient** Test should not depend on each other.
- **Repeatable** Test Should be repeatable in any environment.
- **Self-Validating** Test should have a boolean output. either they pass or fail.
- **Timely** Unit tests should be written just before the production code that makes them pass. If you write tests after the production code, then you may find the production code to be hard to test.

## Some more Examples

### Clean Tests

What makes a clean test? Three things: readability, readability, and readability. Readability is perhaps even more important in unit tests than it is in production code. What makes tests readable? The same things that make all code readable: clarity, simplicity, and density of expression. In a test, you want to say a lot with as few expressions as possible.

#### The BUILD-OPERATE-CHECK Pattern

Clean tests typically follow the BUILD-OPERATE-CHECK pattern:

1. **Build**: Set up the test data and preconditions
2. **Operate**: Perform the action being tested
3. **Check**: Verify the results

Example of a clean test following this pattern:

```java
@Test
public void testGetPageHierarchyAsXml() throws Exception {
    // BUILD
    makePages("PageOne", "PageOne.ChildOne", "PageTwo");

    // OPERATE
    submitRequest("root", "type:pages");

    // CHECK
    assertResponseIsXML();
    assertResponseContains(
        "<name>PageOne</name>",
        "<name>PageTwo</name>",
        "<name>ChildOne</name>"
    );
}
```

#### Domain-Specific Testing Language

Rather than using the APIs that programmers use to manipulate the system, build up a set of functions and utilities that make the tests more convenient to write and easier to read. These functions become a specialized API used by the tests.

Example of using a domain-specific language:

```java
@Test
public void turnOnLoTempAlarmAtThreshold() throws Exception {
    wayTooCold();
    assertEquals("HBchL", hw.getState());
}
```

Where "HBchL" represents the state of various components (Heater on, Blower on, cooler off, hi-temp-alarm off, lo-temp-alarm on).

#### One Assert per Test

There is a school of thought that says every test function should have one and only one assert statement. While this rule may seem draconian, it helps ensure tests are focused and easy to understand.

Example of splitting a test with multiple assertions:

```java
@Test
public void testGetPageHierarchyAsXml() throws Exception {
    givenPages("PageOne", "PageOne.ChildOne", "PageTwo");
    whenRequestIsIssued("root", "type:pages");
    thenResponseShouldBeXML();
}

@Test
public void testGetPageHierarchyHasRightTags() throws Exception {
    givenPages("PageOne", "PageOne.ChildOne", "PageTwo");
    whenRequestIsIssued("root", "type:pages");
    thenResponseShouldContain(
        "<name>PageOne</name>",
        "<name>PageTwo</name>",
        "<name>ChildOne</name>"
    );
}
```

#### Single Concept per Test

A better rule than "one assert per test" is to test a single concept in each test function. Avoid long test functions that test multiple miscellaneous things.

Example of a test that should be split:

```java
@Test
public void testAddMonths() {
    // Test adding one month to a date with 31 days
    SerialDate d1 = SerialDate.createInstance(31, 5, 2004);
    SerialDate d2 = SerialDate.addMonths(1, d1);
    assertEquals(30, d2.getDayOfMonth());
    assertEquals(6, d2.getMonth());
    assertEquals(2004, d2.getYYYY());

    // Test adding two months to a date with 31 days
    SerialDate d3 = SerialDate.addMonths(2, d1);
    assertEquals(31, d3.getDayOfMonth());
    assertEquals(7, d3.getMonth());
    assertEquals(2004, d3.getYYYY());

    // Test adding one month twice
    SerialDate d4 = SerialDate.addMonths(1, SerialDate.addMonths(1, d1));
    assertEquals(30, d4.getDayOfMonth());
    assertEquals(7, d4.getMonth());
    assertEquals(2004, d4.getYYYY());
}
```

This test should be split into three separate tests, each focusing on a single concept:

1. Adding one month to a date with 31 days
2. Adding two months to a date with 31 days
3. Adding one month twice

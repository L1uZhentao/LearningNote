# Chapter 3 - Functions

## Summary

- Functions should be small and do only one thing - blocks within control statements should be one line long and nesting should be minimal
- Functions should maintain one level of abstraction and follow the "Stepdown Rule" for readability
- Switch statements should be used carefully - ideally buried in low-level classes and used for polymorphism
- Use descriptive names for functions - longer descriptive names are better than short cryptic ones
- Function arguments should be limited to less than 3 - fewer arguments make functions easier to understand and test
- Avoid problematic monadic functions - be clear about whether a function is asking a question, transforming data, or handling an event
- Avoid flag arguments as they indicate the function does more than one thing
- Dyadic functions are harder to understand than monadic ones - use them carefully and consider refactoring when possible
- Triads should be used very carefully as they are significantly more complex than dyads
- Output arguments should be avoided - prefer changing state of the owning object
- Functions should either do something or answer something, but not both
- Prefer exceptions to returning error codes for better readability and maintainability
- Don't Repeat Yourself (DRY) - duplication is a root cause of problems in software

## Examples

### Functions should be small

- The first rule of functions is that they should be small. The second rule of functions is that they should be smaller than that.

- This implies that the blocks within `if` statements, `else` statements, `while` statements, and so on should be one line long.

- Functions should not be large enough to hold nested structures. Therefore, the indent level of a function should not be greater than one or two. This, of course, makes the functions easy to read and understand.

### Functions should do one thing

**FUNCTIONS SHOULD DO ONE THING. THEY SHOULD DO IT WELL. THEY SHOULD DO IT ONLY.**

Functions that do one thing cannot be reasonably divided into sections.

### One Level of Abstraction per Function

In order to make sure our functions are doing "one thing", we need to make sure that the statements within our function are all at the same level of abstraction.

#### Reading Code from Top to Bottom: _The Stepdown Rule_

We want the code to read like a top-down narrative. We want every function to be followed by those at the next level of abstraction so that we can read the program, descending one level of abstraction at a time as we read down the list of functions.

To say this differently, we want to be able to read the program as though it were a set of TO paragraphs, each of which is describing the current level of abstraction and referencing subsequent TO paragraphs at the next level down.

Making the code read like a top-down set of TO paragraphs is an effective technique for keeping the abstraction level consistent.

E.g:

```
- To include the setups and teardowns, we include setups, then we include the test page content, and then we include the teardowns.
  - To include the setups, we include the suite setup if this is a suite, then we include the regular setup.
  - To include the suite setup, we search the parent hierarchy for the "SuiteSetUp" page and add an include statement with the path of that page.
  - To search the parent...
```

### Be Careful with Switch Statements

- It's hard to make a small switch statement.

- We should make sure that each switch statement is buried in a low-level class and is never repeated. We do this, of course, with polymorphism.

- Switch Statement can be tolerated if they appear only once, are used to create polymorphic objects, and are hidden behind an inheritance

### Use Descriptive Names for Functions

- Half the battle to achieving that principle is choosing good names for small functions that do one thing. The smaller and more focused a function is, the easier it is to choose a descriptive name.

- Don't be afraid to make a name long. A long descriptive name is better than a short enigmatic name. A long descriptive name is better than a long descriptive comment. Use a naming convention that allows multiple words to be easily read in the function names, and then make use of those multiple words to give the function a name that says what it does.

- Choosing descriptive names will clarify the design of the module in your mind and help you to improve it. It is not at all uncommon that hunting for a good name results in a favorable restructuring of the code.

- Be consistent in your names. Use the same phrases, nouns, and verbs in the function names you choose for your modules.

### Limit Function Arguments to less than 3

The number of arguments for a function is important:

- The ideal number of arguments for a function is zero (niladic).
- Next comes one (monadic), followed closely by two (dyadic). - Three arguments (triadic) should be avoided where possible. - More than three (polyadic) requires very special justification—and then shouldn't be used anyway.

Arguments are even harder from a testing point of view:

- If there are no arguments, this is trivial.
- If there's one argument, it's not too hard.
- With two arguments the problem gets a bit more challenging.
- With more than two arguments, testing every combination of appropriate values can be daunting.

Output arguments are harder to understand than input arguments:

- When we read a function, we are used to the idea of information going in to the function through arguments and out through the return value.
- We don't usually expect information to be going out through the arguments. So output arguments often cause us to do a double-take.

### Avoid Problematic monadic function

Common forms of monadic functions:

- Question: `boolean fileExists("MyFile")`, which asks if the file exists and returns a boolean value.
- Transform: `InputStream fileOpen("MyFile")` transforms a file name `String` into an `InputStream` return value.
- Event: `void fileClosed(InputStream)`, which is an event that occurs when the file is closed.

Avoid these forms of monadic functions:

```java
// Confusing - uses output argument
void transform(StringBuffer out)

// Clearer - uses return value
StringBuffer transform(StringBuffer in)
```

Best practice:

- Make it obvious from the function name which form you're using
- Be consistent with the form throughout your codebase
- Prefer return values over output arguments
- If you must modify the input, make it clear in the function name
- Consider creating new types to make the transformation more explicit

### Avoid Flag Arguments

Flag arguments are ugly. Passing a boolean into a function is a truly terrible practice. It immediately complicates the signature of the method, loudly proclaiming that this function does more than one thing. It does one thing if the flag is `true` and another if the flag is `false`!

### Dyadic Functions are harder to Understand than Monadic Functions

- A function with two arguments is harder to understand than a monadic function. For example, `writeField(name)` is easier to understand than `writeField(output-Stream, name)`

- There are times, of course, where two arguments are appropriate. For example, `Point p = new Point(0,0);` is perfectly reasonable. Cartesian points naturally take two arguments.

- Dyads aren't evil, and you will certainly have to write them. However, you should be aware that they come at a cost and should take advantage of what mechanims may be available to you to convert them into monads. For example:
  - you might make the writeField method a member of outputStream so that you can say outputStream. writeField(name) .
  - Or you might make the outputStream a member variable of the current class so that you don't have to pass it.
  - Or you might extract a new class like FieldWriter that takes the outputStream in its constructor and has a write method.

### Triads: very carefuly before creating a triad

Functions that take three arguments are significantly harder to understand than dyads. The issues of ordering, pausing, and ignoring are more than doubled. I suggest you think very carefully before creating a triad.

| Before Refactoring                                      | After Refactoring                                 |
| ------------------------------------------------------- | ------------------------------------------------- |
| `Circle makeCircle(double x, double y, double radius);` | `Circle makeCircle(Point center, double radius);` |
| `getThem()`                                             | `getFlaggedCells()`                               |

#### Enncode the names of the arguments into the function name

- In the case of a monad, the function and argument should form a very nice verb/noun pair.

  - For example, `write(name)` is very evocative. Whatever this "name" thing is, it is being "written." An even better name might be `writeField(name)` , which tells us that the "name" thing is a "field".

- Another advantage of encoding the names of the arguments into the function name is that it strongly mitigates the problem of having to remember the ordering of the arguments.

  - For example, `assertEquals` might be better written as `assertExpectedEqualsActual(expected, actual)`.

### Output Arguments should be avoided

In general output arguments should be avoided. If your function must change the state of something, have it change the state of its owning object.

| Before Refactoring                               | After Refactoring        |
| ------------------------------------------------ | ------------------------ |
| `public void appendFooter(StringBuffer report);` | `report.appendFooter();` |

### Functions should either do something or answer something, but not both.

Functions should either do something or answer something, but not both. Either your function should change the state of an object, or it should return some information about that object. Doing both often leads to confusion.

### Prefer Exceptions to Returning Error Codes

Returning error codes from command functions is a subtle violation of command query separation.

Example:

Before:

```java
if (deletePage(page) == E_OK) {
  if (registry.deleteReference(page.name) == E_OK) {
    if (configKeys.deleteKey(page.name.makeKey()) == E_OK) {
      logger.log("page deleted");
    } else {
      logger.log("configKey not deleted");
    }
  } else {
    logger.log("deleteReference from registry failed");
      } else {
    logger.log("delete failed");
    return E_ERROR;
  }
}
```

After:

```java
try {
  deletePage(page);
  registry.deleteReference(page.name);
  configKeys.deleteKey(page.name.makeKey());
} catch (Exception e) {
  logger.log(e.getMessage());
}
```

### Don't Repeat Yourself

Duplication may be the root of all evil in software. Many principles and practices have been created for the purpose of controlling or eliminating it.

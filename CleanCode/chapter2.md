# Chapter 2 - Meaningful Names

## Summary

- Use Intention-Revealing Names: Names should clearly communicate purpose and usage
- Avoid Disinformation: Don't leave misleading clues about meaning or implementation
- Make Meaningful Distinctions: Names that differ should convey meaningful differences
- Use Pronounceable Names: Choose names that can be discussed in conversation
- Use Searchable Names: Longer, descriptive names are better for searching than single letters
- Avoid Encoding: Don't prefix or encode type information into names
- Avoid Mental Mapping: Readers shouldn't need to mentally translate names
- Class Names: Should be noun phrases describing the object
- Method Names: Should be verb phrases describing the action
- Don't Be Cute: Choose clarity over humor or cultural references
- Pick One Word Per Concept: Be consistent in terminology across codebase
- Don't Pun: Avoid using same word for different purposes
- Use Solution/Problem Domain Names: Draw from both technical and business contexts
- Add Meaningful Context: Names should be placed in well-named classes, functions, or namespaces
- Don't Add Gratuitous Context: Only include context that adds value

## Examples

### Use Intention-Revealing Names

The name of a variable, function or class, should answer all the big questions. It should tell you why it exists, what it does, and how is used. **If a name requires a comment, then the name does not reveals its intent**.

E.g.:

| Does not reveals intention       | Reveals intention       |
| -------------------------------- | ----------------------- |
| `int d; // elapsed time in days` | `int elapsedTimeInDays` |
| `getThem()`                      | `getFlaggedCells()`     |

### Avoid Disinformation

Programmers must avoid leaving false clues that obscure the meaning of code. We should avoid words whose entrenched meaning vary from our intended meaning.

E.g.:

| Disinformation | Avoid Disinformation                        |
| -------------- | ------------------------------------------- |
| `accountList`  | `accountGroup` `bunchOfAccounts` `accounts` |

### Make Meaningful Distinctions

It is not sufficient to add number series or noise words, even though the compiler is satisfied. If names must be different, then they should also mean something different.

Eg:

Bad code:

```java
public static void copyChars(char a1[], char a2[]) {
  for (int i = 0; i < a1.length; i++) {
    a2[i] = a1[i];
  }
}
```

Clean code:

```java
public static void copyChars(char source[], char destination[]) {
  for (int i = 0; i < source.length; i++) {
    destination[i] = source[i];
  }
}
```

### Use Pronounceable Names

Bad code:

```java
class DtaRcrd102 {
  private Date genymdhms;
  private Date modymdhms;
  private final String pszqint = "102";
  /* ... */
};
```

Clean code:

```java
class Customer {
  private Date generationTimestamp;
  private Date modificationTimestamp;;
  private final String recordId = "102";
  /* ... */
};
```

### Use Searchable Names

Single-letter names and numeric constants have a particular problem in that they are not easy to locate across a body of text.

### Avoid Encoding

We have enough encodings to deal with without adding more to our burden. Encoding type or scope information into names simply adds an extra burden of deciphering. Encoded names are seldom pronounceable and are easy to mis-type. An example of this, is the use of the [Hungarian Notation](https://en.wikipedia.org/wiki/Hungarian_notation) or the use of member prefixes.

### Avoid Mental Mapping

Readers shouldn't have to mentally translate your names into other names they already know. One difference between a smart programmer and a professional programmer is that the professional understands that clarity is king. Professionals use their powers for good and write code that others can understand.

Example: single-letter variable names in loop

### Class Names

Classes and objects should have noun or noun phrase names like `Customer`, `WikiPage`, `Account`, and `AddressParser`. Avoid words like `Manager`,`Processor`, `Data`, or `Info` in the name of a class. A class name should not be a verb.

### Method Names

- Methods should have verb or verb phrase names like `postPayment`, `deletePage` or `save`. Accessors, mutators, and predicates should be named for their value and prefixed with get, set, and is according to the javabean standard.

- When constructors are overloaded, use static factory methods with names that describe the arguments. For example:

```java
Complex fulcrumPoint = Complex.FromRealNumber(23.0);
```

Is generally better than

```java
Complex fulcrumPoint = new Complex(23.0);
```

### Don't Be Cute

Example:
| Cute name | Clean name |
| ----------------- | ------------- |
| `holyHandGranade` | `deleteItems` |
| `whack` | `kill` |
| `eatMyShorts` | `abort` |

### Pick one word per concept

Pick one word for one abstract concept and stick with it. For instance, it’s confusing to have fetch, retrieve, and get as equivalent methods of different classes.

### Don’t Pun

Avoid using the same word for two purposes. Using the same term for two different ideas is essentially a pun.

Example: in a class use `add` for create a new value by adding or concatenating two existing values and in another class use `add` for put a simple parameter in a collection, it's a better options use a name like `insert` or `append` instead.

### Solution Domain Names and Problem Domain Names

- When to use Solution Domain Names: Remember that the people who read your code will be programmers. So go ahead and use computer science (CS) terms, algorithm names, pattern names, math terms, and so forth.

- When to use Problem Domain Names: When there is no “programmer-eese” for what you’re doing, use the name from the problem domain. At least the programmer who maintains your code can ask a domain expert what it means.

### Add Meaningful context

Most names need context to be meaningful. Add context by placing names in well-named classes, functions, or namespaces. Only use prefixes as a last resort.

For example, variables like `state` are unclear alone, but make sense as part of an `Address` class containing `firstName`, `lastName`, `street`, `city`, and `state`. Creating a proper class is better than using prefixes like `addrState`.

### Don’t Add Gratuitous Context

In an imaginary application called “Gas Station Deluxe,” it is a bad idea to prefix every class with GSD. Example: `GSDAccountAddress`

Shorter names are generally better than longer ones, so long as they are clear. Add no more context to a name than is necessary.

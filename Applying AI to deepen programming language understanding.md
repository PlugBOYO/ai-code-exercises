

## **Activity 1: Idiomatic Code Transformation**

### **Step 1: Original Code (15–30 lines)**

Java method for calculating factorial:

```java
public class MathUtils {

    public static int factorial(int n) {
        int result = 1;
        for (int i = 1; i <= n; i++) {
            result = result * i;
        }
        return result;
    }
}
```

---

### **Step 2: Use AI Prompt**

```

public static int factorial(int n) {
    int result = 1;
    for (int i = 1; i <= n; i++) {
        result = result * i;
    }
    return result;
}


```

---

### **Step 3: Suggested Improvements**

1. Use **Java Streams** for a more declarative approach.
2. Take advantage of `IntStream.rangeClosed()` instead of a manual loop.
3. Improves readability and expresses intent (multiplying all numbers in a range).

---

### **Step 4: Side-by-Side Comparison**

| Original Version                     | Idiomatic Version                                      |
| ------------------------------------ | ------------------------------------------------------ |
| ```java                              | ```java                                                |
| public static int factorial(int n) { | public static int factorial(int n) {                   |
|    int result = 1;                   |    return java.util.stream.IntStream.rangeClosed(1, n) |
|    for (int i = 1; i <= n; i++) {    |       .reduce(1, (a, b) -> a * b);                     |
|       result = result * i;           | }                                                      |
|    }                                 |                                                        |
|    return result;                    |                                                        |
| }                                    |                                                        |

**Why it’s better:**

* More declarative → clearly shows “multiply numbers 1..n”.
* Uses modern Java features (`Streams`) for concise, readable code.
* Easier to extend (e.g., parallel processing with `parallel()` if needed).

---

### **Step 5: Key Learnings**

1. Using Streams in Java can reduce boilerplate loops.
2. Declarative code improves readability and expresses intent better than manual iteration.
3. Leveraging language features (like `IntStream`) is safer and more idiomatic.

---

## **Activity 2: Code Quality Detective**

### **Step 1: Old Code Example**

```java
public static boolean isPrime(int n) {
    if (n <= 1) return false;
    for (int i = 2; i < n; i++) {
        if (n % i == 0) return false;
    }
    return true;
}
```

---

### **Step 2: AI Prompt**

```
I'm a junior developer working with Java. Could you review this code for quality improvements:

public static boolean isPrime(int n) {
    if (n <= 1) return false;
    for (int i = 2; i < n; i++) {
        if (n % i == 0) return false;
    }
    return true;
}

Please:
1. Identify any code smells or quality issues
2. Suggest specific improvements
3. Explain why these improvements matter in Java
4. Rate the code's readability, performance, and maintainability
```

---

### **Step 3: Improvements**

1. **Performance:** Looping to `n-1` is inefficient → only need to loop to `√n`.
2. **Readability:** Single-line `if` is fine, but adding braces improves clarity.
3. **Maintainability:** Extracting a helper method for validation can improve reuse.

---

### **Step 4: Refactored Version**

```java
public static boolean isPrime(int n) {
    if (n <= 1) return false;
    int sqrt = (int) Math.sqrt(n);
    for (int i = 2; i <= sqrt; i++) {
        if (n % i == 0) return false;
    }
    return true;
}
```

**Checklist for Future Code Reviews:**

* Avoid unnecessary loops → optimize performance.
* Use braces for clarity even in single-line blocks.
* Extract reusable helper methods when logic grows.
* Consider algorithmic improvements (`√n` instead of `n-1`).
* Keep code readable and expressive.

---

### **Step 5: Key Learnings**

1. Small performance improvements can have a big impact on frequently used methods.
2. Clarity is just as important as correctness.
3. Creating reusable patterns improves maintainability.

---

## **Activity 3: Understanding a Language Feature (Java Streams)**

### **Step 1: Feature Prompt**

```
I want to improve my understanding of Java Streams.

1. Could you explain this feature with simple examples?
2. Show me 3 practical use cases where this would be valuable
3. Provide a small project idea that would help me practice this feature
4. What common mistakes should I avoid when using this feature?
```

---

### **Step 2: Explanation and Examples**

**Java Streams:**

* Allow processing collections in a functional style (map, filter, reduce).
* Work well for declarative, readable data operations.

**Example:**

```java
List<String> names = List.of("Alice", "Bob", "Charlie");

// Filter names starting with 'A' and convert to uppercase
List<String> filtered = names.stream()
    .filter(name -> name.startsWith("A"))
    .map(String::toUpperCase)
    .collect(Collectors.toList());
```

**Use Cases:**

1. Filtering large datasets (e.g., users over 18).
2. Aggregating numeric data (sum, average, max).
3. Transforming data structures (e.g., List → Map).

**Small Project Idea:**

* Create a mini “library system” where you filter books by genre, author, or rating using Streams.

**Common Mistakes:**

* Not closing streams in some IO operations (though collections streams are safe).
* Overusing `parallelStream()` without understanding concurrency.
* Using mutable state inside `map()` or `forEach()` → breaks functional style.

---

### **Step 3: Small Implementation**

```java
List<Book> books = List.of(
    new Book("1984", "George Orwell", 1949),
    new Book("Brave New World", "Aldous Huxley", 1932)
);

// Filter books published before 1940
List<Book> classicBooks = books.stream()
    .filter(book -> book.getYear() < 1940)
    .collect(Collectors.toList());
```

---

### **Step 4: Key Learnings**

1. Streams allow functional-style data processing, reducing loops and mutable state.
2. Filtering, mapping, and collecting are the core operations to master.
3. Always think about performance when using streams on large datasets; prefer `parallelStream()` only when safe.

---



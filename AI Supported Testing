Below is a **complete structured solution document** covering all Parts 1–4, including planning, improved tests, TDD practice, integration testing, and reflection.

In Java
---

# ✅ PART 1 – Understanding What to Test

---

# Exercise 1.1 – Behavior Analysis (calculateTaskScore)

Function under test:

```java
public static double calculateTaskScore(double marksObtained,
                                        double totalMarks,
                                        double taskWeight) {

    if (totalMarks == 0) {
        throw new IllegalArgumentException("Total marks cannot be zero.");
    }

    double percentage = (marksObtained / totalMarks) * 100;
    double weightedScore = (percentage * taskWeight) / 100;

    return weightedScore;
}
```

---

## 🧠 Behavioral Understanding

The function:

1. Validates `totalMarks != 0`
2. Calculates percentage
3. Applies weight
4. Returns weighted score

Formula simplifies to:

```
weightedScore = (marksObtained / totalMarks) * taskWeight
```

---

## ✅ Test Cases Identified (Minimum 5 Required)

### 1️⃣ Normal Case

* marksObtained = 80
* totalMarks = 100
* taskWeight = 20
* Expected = 16.0

---

### 2️⃣ Zero Total Marks (Exception)

* totalMarks = 0
* Expect IllegalArgumentException

---

### 3️⃣ Full Marks

* 100/100, weight 50
* Expected = 50

---

### 4️⃣ Zero Marks

* 0/100, weight 30
* Expected = 0

---

### 5️⃣ Decimal Precision

* 75/120, weight 25
* Expected ≈ 15.625

---

### 6️⃣ Edge Case: marks > totalMarks

* 110/100, weight 20
* Expected = 22
  (Should we allow this? This reveals validation gap.)

---

# Exercise 1.2 – Structured Test Plan

---

# 📋 TEST PLAN DOCUMENT

---

## 🎯 Scope

Test:

* calculateTaskScore
* sortTasksByImportance
* getTopPriorityTasks

---

## 🔹 Test Types Needed

| Function              | Unit Test | Integration Test |
| --------------------- | --------- | ---------------- |
| calculateTaskScore    | ✅         | ❌                |
| sortTasksByImportance | ✅         | ❌                |
| getTopPriorityTasks   | ✅         | ❌                |
| Combined Workflow     | ❌         | ✅                |

---

## 🔹 Test Priorities

### 🔴 High Priority

* Correct score calculation
* Exception handling
* Sorting correctness
* Top priority selection

### 🟡 Medium

* Edge values
* Decimal rounding

### 🟢 Low

* Performance (large datasets)

---

## 🔹 Test Dependencies

* No database needed
* Pure logic functions
* Deterministic outputs

---

## 🔹 Expected Outcomes

| Test         | Expected                     |
| ------------ | ---------------------------- |
| Valid score  | Correct weighted value       |
| totalMarks=0 | Exception thrown             |
| Sorting      | Correct descending order     |
| Top N tasks  | Only highest scores returned |

---

# ✅ PART 2 – Improving a Single Test

---

# Exercise 2.1 – Improved Unit Test

Original test was unrelated to function.

Here is a proper robust test:

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class CalculateTaskScoreTest {

    @Test
    void shouldCalculateWeightedScoreCorrectly() {
        double result = TaskUtils.calculateTaskScore(80, 100, 20);
        assertEquals(16.0, result, 0.0001);
    }

    @Test
    void shouldThrowExceptionWhenTotalMarksZero() {
        assertThrows(IllegalArgumentException.class,
                () -> TaskUtils.calculateTaskScore(50, 0, 20));
    }

    @Test
    void shouldReturnZeroWhenMarksAreZero() {
        double result = TaskUtils.calculateTaskScore(0, 100, 25);
        assertEquals(0.0, result, 0.0001);
    }

    @Test
    void shouldHandleDecimalResultsCorrectly() {
        double result = TaskUtils.calculateTaskScore(75, 120, 25);
        assertEquals(15.625, result, 0.0001);
    }
}
```

---

# Exercise 2.2 – Comprehensive Test

Even though this function has no due date logic, we improve coverage via boundary tests:

```java
@Test
void shouldHandleMarksGreaterThanTotal() {
    double result = TaskUtils.calculateTaskScore(110, 100, 20);
    assertEquals(22.0, result, 0.0001);
}
```

This reveals a design question:
Should we allow marks > total?

Testing helps clarify business rules.

---

# ✅ PART 3 – TDD Practice

---

# Exercise 3.1 – New Feature: +12 Boost

---

## Step 1: Write Failing Test

```java
@Test
void shouldAddBoostWhenAssignedToCurrentUser() {
    double baseScore = TaskUtils.calculateTaskScore(80, 100, 20);

    double boosted = TaskUtils.calculateTaskScoreWithUserBoost(
            80, 100, 20, true);

    assertEquals(baseScore + 12, boosted, 0.0001);
}
```

---

## Step 2: Minimal Implementation

```java
public static double calculateTaskScoreWithUserBoost(
        double marksObtained,
        double totalMarks,
        double taskWeight,
        boolean assignedToCurrentUser) {

    double baseScore = calculateTaskScore(marksObtained, totalMarks, taskWeight);

    if (assignedToCurrentUser) {
        return baseScore + 12;
    }

    return baseScore;
}
```

---

## Step 3: Add Next Test

```java
@Test
void shouldNotAddBoostIfNotAssigned() {
    double result = TaskUtils.calculateTaskScoreWithUserBoost(
            80, 100, 20, false);

    assertEquals(16.0, result, 0.0001);
}
```

---

# Exercise 3.2 – Bug Fix (Days Calculation)

---

## Write Failing Test

```java
@Test
void shouldCalculateDaysSinceUpdateCorrectly() {
    LocalDateTime twoDaysAgo = LocalDateTime.now().minusDays(2);

    long days = TaskUtils.calculateDaysSinceUpdate(twoDaysAgo);

    assertEquals(2, days);
}
```

---

## Buggy Code (Example)

```java
// WRONG
return Duration.between(lastUpdated, LocalDateTime.now()).toMillis() / (1000 * 60 * 60 * 24);
```

---

## Fix Using ChronoUnit

```java
return ChronoUnit.DAYS.between(lastUpdated, LocalDateTime.now());
```

Test now passes.

---

# ✅ PART 4 – Integration Test

---

# Integration Test: Full Workflow

```java
@Test
void shouldCalculateSortAndReturnTopTasksCorrectly() {

    Task t1 = new Task("Task1", 80, 100, 20);
    Task t2 = new Task("Task2", 90, 100, 30);
    Task t3 = new Task("Task3", 50, 100, 10);

    List<Task> tasks = List.of(t1, t2, t3);

    // Calculate scores
    tasks.forEach(task ->
            task.setScore(TaskUtils.calculateTaskScore(
                    task.getMarks(),
                    task.getTotal(),
                    task.getWeight()
            ))
    );

    // Sort
    List<Task> sorted = TaskUtils.sortTasksByImportance(tasks);

    // Get top task
    List<Task> top = TaskUtils.getTopPriorityTasks(sorted, 1);

    assertEquals("Task2", top.get(0).getTitle());
}
```

---

# 📘 FINAL DOCUMENT SUMMARY

---

## 1️⃣ Test Plan

* Unit tests for each function
* Integration test for workflow
* Edge case validation
* Exception testing

---

## 2️⃣ Improved Unit Tests

* Precise assertions
* Delta for floating point
* Exception verification
* Boundary testing

---

## 3️⃣ TDD Implementation

* Wrote failing tests first
* Implemented minimal code
* Refactored safely
* Added regression tests

---

## 4️⃣ Integration Test

* End-to-end workflow verified
* Score calculation → sorting → filtering

---

# 🎓 REFLECTION

---

### What I Learned

1. Testing clarifies business rules.
2. Edge cases reveal hidden design flaws.
3. TDD forces simpler design.
4. Integration tests validate real behavior.
5. Floating-point precision must be handled carefully.

---

### Biggest Insight

Good tests:

* Test behavior, not implementation.
* Cover happy path + edge cases.
* Fail clearly.
* Are readable and precise.

---


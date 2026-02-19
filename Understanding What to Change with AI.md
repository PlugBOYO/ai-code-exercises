
---

# Mogau’s AI Refactoring Checklist

Use this **before, during, and after** asking AI to refactor your code.

---

# 🔍 PHASE 1 — Before Asking AI

## 1️⃣ Clarify the Goal

Ask yourself:

* What problem am I solving?

  * Readability?
  * Performance?
  * Security?
  * Maintainability?
  * Concurrency safety?
* What must NOT change?

  * Output format?
  * API contract?
  * Time complexity?
  * Thread behavior?

✅ Write this clearly in your prompt.

Example:

> Refactor this function to improve readability without changing time complexity or external behavior.

---

## 2️⃣ Ensure Safety Nets

Before refactoring:

* ✅ Do I have tests?
* ✅ Do I understand the current behavior?
* ✅ Do I know the edge cases?
* ✅ Is this production code?

If no tests exist → write basic ones first.

---

#  PHASE 2 — When Using AI

Use structured prompts like:

> Refactor this code while preserving:
>
> * Thread safety
> * Time complexity
> * Memory constraints
> * Current public API

---

## 3️⃣ Naming & Clarity Check

Ask AI:

* Are variable names meaningful?
* Are method names intention-revealing?
* Are abbreviations removed?
* Does naming follow language conventions?

For example:

* Java → `camelCase` methods, `PascalCase` classes
* Python → `snake_case`
* JavaScript → `camelCase`

---

## 4️⃣ Responsibility Check (SRP Test)

Ask:

* Does this function do more than one thing?
* Can it be split into:

  * Validation
  * Business logic
  * Data access
  * Formatting

If yes → extract functions.

Especially important for:

* Backend logic
* OS simulations
* API endpoints

---

## 5️⃣ Complexity Check

Ask AI to analyze:

* Cyclomatic complexity
* Deep nesting
* Duplicate loops
* Repeated conditions

Red flags:

* Nested `if` blocks 3+ levels deep
* 50+ line functions
* Repeated code patterns

---

## 6️⃣ Performance Review (Important for You)

Since you're studying OS and systems:

Ask:

* Does this change time complexity?
* Does this add extra loops?
* Does this increase memory usage?
* Is this thread-safe?
* Are shared resources protected?

For Java:

* Is synchronization preserved?
* Any race conditions introduced?

For Python:

* Any unnecessary copies of large objects?

---

## 7️⃣ Security Check

Always ask:

* Any SQL injection risk?
* Input validation missing?
* Sensitive data exposed?
* Password handling secure?
* Are exceptions leaking internal info?

This is something many students forget.

---

#  PHASE 3 — Code Structure Review

After AI gives refactored code:

## 8️⃣ Compare Side-by-Side

Ask:

* Is it actually clearer?
* Or just “more abstract”?

Sometimes shorter ≠ clearer.

---

## 9️⃣ Read It Out Loud Test

If you can read the method name and instantly understand what it does — good design.

Bad:

```java
processData()
```

Better:

```java
calculateMonthlyRevenue()
```

---

## 🔁 10️⃣ Duplication Audit

Ask:

* Are similar loops repeated?
* Are validation blocks duplicated?
* Can a helper function remove repetition?

BUT avoid:
Over-generalizing too early.

---

#  PHASE 4 — Validation Before Merge

## 11️⃣ Behavior Verification

* Run tests
* Check edge cases
* Compare outputs

---

## 12️⃣ Performance Verification

Especially for:

* Large datasets
* Scheduling algorithms
* Memory simulations
* Banking algorithms

Check:

* Execution time
* Memory usage
* CPU behavior

---

## 13️⃣ Concurrency & State Check (Advanced)

For system-level code:

* Are shared variables protected?
* Is state mutation controlled?
* Are objects immutable where possible?
* Any deadlock potential introduced?

---

# 🧩 Personal AI Prompt Template (Reusable)

---

### 📌 Mogau’s Refactoring Prompt

> Refactor this code to improve readability and maintainability while:
>
> * Preserving behavior
> * Preserving time complexity
> * Avoiding additional memory overhead
> * Maintaining thread safety (if applicable)
> * Following [language] conventions
>
> Also:
>
> * Identify naming issues
> * Identify responsibility violations
> * Point out security concerns
> * Suggest improvements step-by-step

---

# 🚨 When NOT to Accept AI Suggestions

Reject changes if:

* It adds unnecessary abstraction
* It makes debugging harder
* It changes algorithm complexity
* It removes clarity for junior devs
* It introduces hidden side effects

---

# 📈 How This Helps You Long-Term

This checklist builds:

* Clean code mindset
* System design thinking
* Interview readiness
* Safer refactoring habits
* Stronger technical communication

---

---

# ✅ Exercise 1: Code Readability Improvement (Java)

**Prompt Used:** Code Readability Improvement
**Language:** Java
**Standards:** Standard Java naming conventions (PascalCase for classes, camelCase for variables/methods, meaningful names)

---

## 1️⃣ Parts That Are Difficult to Understand

### ❌ Unclear Class and Method Names

* `UserMgr` → abbreviated, not standard Java naming
* `U` → extremely unclear
* `a()` → meaningless method name
* `f()` → meaningless method name

These names hide intent and make the code hard to maintain.

---

### ❌ Poor Variable Naming

* `u_list`
* `db`
* `un`, `pw`, `em`
* `nu`
* `res`

They’re cryptic and don’t follow standard Java naming style.

---

### ❌ Mixed Responsibilities in Method `a()`

The method:

```java
public boolean a(String un, String pw, String em)
```

It:

1. Validates input
2. Checks for duplicate username
3. Creates user object
4. Adds to in-memory list
5. Inserts into database

That’s too many responsibilities for one method.

---

### ❌ SQL Injection Risk

```java
db.execute("INSERT INTO users VALUES ('" + un + "', '" + pw + "', '" + em + "')");
```

This is unsafe string concatenation.

---

## 2️⃣ Suggested Better Names

### Class Names

| Current   | Improved      |
| --------- | ------------- |
| `UserMgr` | `UserManager` |
| `U`       | `User`        |

---

### Variables

| Current  | Improved             |
| -------- | -------------------- |
| `u_list` | `users`              |
| `db`     | `databaseConnection` |
| `un`     | `username`           |
| `pw`     | `password`           |
| `em`     | `email`              |
| `nu`     | `newUser`            |
| `res`    | `insertSuccessful`   |

---

### Method Names

| Current | Improved               |
| ------- | ---------------------- |
| `a()`   | `registerUser()`       |
| `f()`   | `findUserByUsername()` |

---

## 3️⃣ Breaking Down Complex Sections

### 🔹 Extract Validation Logic

```java
private boolean isValidUserData(String username, String password, String email) {
    return username.length() >= 3 &&
           password.length() >= 8 &&
           email.contains("@");
}
```

---

### 🔹 Extract Duplicate Check

```java
private boolean usernameExists(String username) {
    for (User user : users) {
        if (user.getUsername().equals(username)) {
            return true;
        }
    }
    return false;
}
```

---

### 🔹 Refactored `registerUser()`

```java
public boolean registerUser(String username, String password, String email) {
    if (!isValidUserData(username, password, email)) {
        return false;
    }

    if (usernameExists(username)) {
        return false;
    }

    User newUser = new User(username, password, email);
    users.add(newUser);

    return databaseConnection.execute(
        "INSERT INTO users VALUES (?, ?, ?)"
    );
}
```

Even better: use prepared statements.

---

## 4️⃣ Style & Formatting Issues Identified

* Inconsistent naming conventions
* Abbreviations reduce clarity
* SQL concatenation is dangerous
* No separation of concerns
* No comments explaining intent

---

### Issues You Might Have Missed

* Security vulnerability (SQL injection)
* Too many responsibilities in one method
* Domain class `U` violates Java naming standards
* Lack of encapsulation (e.g., password getter exposed)

---

# ✅ Exercise 2: Function Refactoring (Python)

**Purpose:** Process orders, update inventory, track revenue.

---

## 1️⃣ Responsibilities in `process_orders()`

This function currently:

1. Validates inventory
2. Validates quantity
3. Validates customer existence
4. Calculates price
5. Applies discount
6. Updates inventory
7. Calculates shipping
8. Calculates tax
9. Updates revenue
10. Builds result object

That’s too many responsibilities.

---

## 2️⃣ Suggested Smaller Functions

### 🔹 Order Validation

```python
def validate_order(order, inventory, customer_data):
    item_id = order['item_id']
    quantity = order['quantity']
    customer_id = order['customer_id']

    if item_id not in inventory:
        return False, 'Item not in inventory'

    if inventory[item_id]['quantity'] < quantity:
        return False, 'Insufficient quantity'

    if customer_id not in customer_data:
        return False, 'Customer not found'

    return True, None
```

---

### 🔹 Price Calculation

```python
def calculate_price(item, quantity, customer):
    base_price = item['price'] * quantity
    if customer['premium']:
        base_price *= 0.9
    return base_price
```

---

### 🔹 Shipping Calculation

```python
def calculate_shipping(price, customer):
    if customer['location'] == 'domestic':
        return 5.99 if price < 50 else 0
    return 15.99
```

---

### 🔹 Refactored Main Function

```python
def process_orders(orders, inventory, customer_data):
    results = []
    error_orders = []
    total_revenue = 0

    for order in orders:
        is_valid, error = validate_order(order, inventory, customer_data)

        if not is_valid:
            error_orders.append({
                'order_id': order['order_id'],
                'error': error
            })
            continue

        item = inventory[order['item_id']]
        customer = customer_data[order['customer_id']]
        quantity = order['quantity']

        price = calculate_price(item, quantity, customer)
        shipping = calculate_shipping(price, customer)
        tax = price * 0.08
        final_price = price + shipping + tax

        inventory[order['item_id']]['quantity'] -= quantity
        total_revenue += final_price

        results.append({
            'order_id': order['order_id'],
            'final_price': final_price
        })

    return {
        'processed_orders': results,
        'error_orders': error_orders,
        'total_revenue': total_revenue
    }
```

---

## Improvements Made

* Clear separation of responsibilities
* Easier testing (can test functions independently)
* Better readability
* More maintainable

---

# ✅ Exercise 3: Code Duplication Detection (JavaScript)

---

## 1️⃣ Repeated Patterns Identified

### Pattern A – Calculating Average

Repeated 3 times:

```javascript
let totalX = 0;
for (...) {
  totalX += ...
}
const averageX = totalX / userData.length;
```

---

### Pattern B – Finding Maximum

Repeated 3 times:

```javascript
let highestX = userData[0].x;
for (...) {
  if (...) {
    highestX = ...
  }
}
```

---

## 2️⃣ Refactoring Strategy

Use helper functions.

---

### 🔹 Generic Average Function

```javascript
function calculateAverage(data, key) {
  let total = 0;
  for (let i = 0; i < data.length; i++) {
    total += data[i][key];
  }
  return total / data.length;
}
```

---

### 🔹 Generic Max Function

```javascript
function findHighest(data, key) {
  let highest = data[0][key];
  for (let i = 1; i < data.length; i++) {
    if (data[i][key] > highest) {
      highest = data[i][key];
    }
  }
  return highest;
}
```

---

### 🔹 Refactored Main Function

```javascript
function calculateUserStatistics(userData) {
  return {
    age: {
      average: calculateAverage(userData, 'age'),
      highest: findHighest(userData, 'age')
    },
    income: {
      average: calculateAverage(userData, 'income'),
      highest: findHighest(userData, 'income')
    },
    score: {
      average: calculateAverage(userData, 'score'),
      highest: findHighest(userData, 'score')
    }
  };
}
```

---

## Benefits

* Eliminates duplication
* Easier to extend
* Easier to maintain
* Fewer bugs

---

For junior teams:

✔ Helper functions (like above)
❌ Avoid overly clever functional programming (like heavy `.reduce()` chains)


---

# Reflection Answers

---

### Which prompting strategy was most useful?

**Function Refactoring** was most powerful.

Why?
Because it forces thinking in terms of:

* Single Responsibility Principle
* Testability
* Modularity

---

### What improvements might you not have thought of?

* Extracting validation logic
* Security issues (SQL injection)
* Separating business logic from data access
* Converting repetitive logic into generic functions

---

### Any suggestions you might disagree with?

Sometimes AI:

* Over-abstracts too early
* Suggests too many tiny functions
* Removes clarity in the name of DRY

Balance is important.

---

### How to adapt prompts to your codebase?

Since you're studying OS and backend systems, you could modify prompts like:

> "Ensure thread safety"
> "Consider memory usage"
> "Consider concurrency implications"
> "Follow clean architecture principles"

For example:

> Refactor while ensuring the function remains thread-safe.

---

### Safeguards Before Applying AI Refactoring

Always:

1. ✅ Have automated tests
2. ✅ Run performance benchmarks
3. ✅ Review for security issues
4. ✅ Do peer code review
5. ✅ Apply incrementally

Never blindly merge AI refactoring.

---

---



## **Step 1: Pattern Opportunity Identification (Prompt 1)**

### **Problems / Pain Points in Current Code**

1. **Complex conditional logic** – multiple `if`/`else if` branches for `shippingMethod` and `destinationCountry`.
2. **Hard to extend** – adding a new shipping method or destination requires modifying the main function.
3. **Violation of Open/Closed Principle** – code isn’t closed for modification.
4. **Dimensional weight rules are scattered** – logic repeated for multiple shipping types.

### **Candidate Pattern**

**Strategy Pattern** – encapsulate each shipping method’s calculation into a separate “strategy” object/class.

### **Benefits**

* Makes each shipping method self-contained.
* Easy to add new shipping methods (just create a new strategy).
* Reduces conditional complexity.
* Supports unit testing of each shipping strategy independently.

### **Potential Drawbacks**

* Slight increase in number of classes / files.
* Need to define a clear interface for all strategies.

---

## **Step 2: Strategy Pattern Implementation (Prompt 2)**

### **Refactoring Plan**

1. Define a `ShippingStrategy` interface with a `calculateCost(packageDetails, destinationCountry)` method.
2. Implement separate strategies for `StandardShipping`, `ExpressShipping`, `OvernightShipping`.
3. Create a `ShippingContext` class to select and execute the strategy.

---

### **Step 3: Refactored JavaScript Code**

```javascript
// Strategy interface
class ShippingStrategy {
    calculateCost(packageDetails, destinationCountry) {
        throw new Error("This method should be implemented by subclasses");
    }
}

// Standard Shipping
class StandardShipping extends ShippingStrategy {
    calculateCost({ weight, length, width, height }, destinationCountry) {
        let cost = 0;
        if (destinationCountry === "USA") cost = weight * 2.5;
        else if (destinationCountry === "Canada") cost = weight * 3.5;
        else if (destinationCountry === "Mexico") cost = weight * 4.0;
        else cost = weight * 4.5;

        // Dimensional weight adjustment
        if (weight < 2 && (length * width * height) > 1000) cost += 5.0;

        return cost;
    }
}

// Express Shipping
class ExpressShipping extends ShippingStrategy {
    calculateCost({ weight, length, width, height }, destinationCountry) {
        let cost = 0;
        if (destinationCountry === "USA") cost = weight * 4.5;
        else if (destinationCountry === "Canada") cost = weight * 5.5;
        else if (destinationCountry === "Mexico") cost = weight * 6.0;
        else cost = weight * 7.5;

        if ((length * width * height) > 5000) cost += 15.0;

        return cost;
    }
}

// Overnight Shipping
class OvernightShipping extends ShippingStrategy {
    calculateCost({ weight }, destinationCountry) {
        if (destinationCountry === "USA") return weight * 9.5;
        else if (destinationCountry === "Canada") return weight * 12.5;
        else throw new Error("Overnight shipping not available for this destination");
    }
}

// Context class
class ShippingContext {
    constructor(strategy) {
        this.strategy = strategy;
    }

    setStrategy(strategy) {
        this.strategy = strategy;
    }

    calculateCost(packageDetails, destinationCountry) {
        return this.strategy.calculateCost(packageDetails, destinationCountry).toFixed(2);
    }
}

// Example usage
const packageDetails = { weight: 5, length: 10, width: 10, height: 10 };

const context = new ShippingContext(new StandardShipping());
console.log(context.calculateCost(packageDetails, "USA")); // "12.50"

context.setStrategy(new ExpressShipping());
console.log(context.calculateCost(packageDetails, "Canada")); // "27.50"

context.setStrategy(new OvernightShipping());
console.log(context.calculateCost(packageDetails, "USA")); // "47.50"
```

---

### **Step 4: How to Verify Behavior**

1. Compare outputs of the original function vs. Strategy Pattern version using the same inputs.
2. Write unit tests for each strategy independently.

Example (using Jest):

```javascript
test("StandardShipping USA", () => {
    const strategy = new StandardShipping();
    expect(strategy.calculateCost({ weight: 5, length: 10, width: 10, height: 10 }, "USA")).toBe(12.5);
});

test("ExpressShipping Canada", () => {
    const strategy = new ExpressShipping();
    expect(strategy.calculateCost({ weight: 5, length: 10, width: 10, height: 10 }, "Canada")).toBe(27.5);
});

test("OvernightShipping unavailable", () => {
    const strategy = new OvernightShipping();
    expect(() => strategy.calculateCost({ weight: 5 }, "Mexico")).toThrow("Overnight shipping not available for this destination");
});
```

---

### **Step 5: Benefits of Strategy Pattern**

| Improvement         | How it helps                                                                                          |
| ------------------- | ----------------------------------------------------------------------------------------------------- |
| **Maintainability** | Each shipping method is isolated; modifying one doesn’t affect others.                                |
| **Extensibility**   | Adding a new shipping method is as simple as creating a new class that implements `ShippingStrategy`. |
| **Readability**     | Removes nested `if`/`else` blocks; main calculation logic is clear.                                   |
| **Testability**     | Each strategy can be tested independently with unit tests.                                            |

**Future changes easier:**

* Add new shipping methods, new dimensional rules, or country-specific logic without touching other methods.

**Challenges:**

* Initial setup requires defining multiple classes.
* Need to enforce strategy interface consistency.

---

✅ **Step 6: Reflection Questions**

1. **Maintainability Improvement:** The code is cleaner and modular; you can now change shipping rules without touching the context or other strategies.
2. **Future Changes:** Adding a new shipping method is just a new class implementing the strategy interface.
3. **Unexpected Challenges:** Managing the error handling for unsupported destinations (Overnight) required adding exceptions, but this is cleaner than returning strings in the original function.

---


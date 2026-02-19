
---

## **Prompt 1: Function Responsibility Analysis**

We’ll analyze the `processData` method from **`DataProcessor.java`**, which currently looks like this:

```java
public void processData(List<DataRecord> records) throws PipelineException {
    for (DataRecord record : records) {
        try {
            // Example processing: convert value to integer and double it
            int value = Integer.parseInt(record.getValue());
            value *= 2;
            record.setValue(String.valueOf(value));
        } catch (NumberFormatException e) {
            // Handle individual record error, log and continue
            System.err.println("Failed to process record " + record.getId() + ": " + e.getMessage());
            record.setValue("ERROR");
        } catch (Exception e) {
            throw new PipelineException("Unexpected error processing data", e);
        }
    }
}
```

### **Step 1: Identify distinct responsibilities**

1. **Iterate through records** – looping over the dataset.
2. **Convert string to integer** – parsing the value.
3. **Perform calculation** – doubling the value.
4. **Update the record** – setting the new value.
5. **Error handling per record** – catching `NumberFormatException`.
6. **Pipeline-level error handling** – catching other unexpected exceptions.

---

### **Step 2: Decomposition Strategy**

Break this into three smaller functions:

1. `parseAndTransformValue(String value)` → converts and transforms value.
2. `updateRecord(DataRecord record, int newValue)` → updates the record.
3. `processSingleRecord(DataRecord record)` → handles a single record including record-level error handling.

The main `processData` will now simply iterate over records and delegate.

---

### **Step 3: Refactored `DataProcessor.java`**

```java
package com.example.datapipeline;

import com.example.datapipeline.models.DataRecord;

import java.util.List;

public class DataProcessor {

    // Main function: delegates responsibilities
    public void processData(List<DataRecord> records) throws PipelineException {
        for (DataRecord record : records) {
            processSingleRecord(record);
        }
    }

    // Handles a single record with error handling
    private void processSingleRecord(DataRecord record) {
        try {
            int newValue = parseAndTransformValue(record.getValue());
            updateRecord(record, newValue);
        } catch (NumberFormatException e) {
            System.err.println("Failed to process record " + record.getId() + ": " + e.getMessage());
            record.setValue("ERROR");
        } catch (Exception e) {
            throw new RuntimeException("Unexpected error processing record " + record.getId(), e);
        }
    }

    // Parses and transforms the value
    private int parseAndTransformValue(String value) throws NumberFormatException {
        int intValue = Integer.parseInt(value);
        return intValue * 2; // Transformation logic
    }

    // Updates the record with the new value
    private void updateRecord(DataRecord record, int newValue) {
        record.setValue(String.valueOf(newValue));
    }
}
```

✅ **Benefits gained:**

* Clear separation of responsibilities.
* Easier to test each function individually.
* Main loop is much cleaner.

---

## **Prompt 2: Single-Responsibility Extraction (Error Handling)**

We want to **extract record-level error handling** into its own function.

### **Step 1: Identify error handling code**

Inside `processSingleRecord`:

```java
try {
    int newValue = parseAndTransformValue(record.getValue());
    updateRecord(record, newValue);
} catch (NumberFormatException e) {
    System.err.println("Failed to process record " + record.getId() + ": " + e.getMessage());
    record.setValue("ERROR");
}
```

---

### **Step 2: Create a new function**

```java
private void handleRecordProcessing(DataRecord record) {
    try {
        int newValue = parseAndTransformValue(record.getValue());
        updateRecord(record, newValue);
    } catch (NumberFormatException e) {
        record.setValue("ERROR");
        logRecordError(record, e);
    }
}

// Logging separated
private void logRecordError(DataRecord record, Exception e) {
    System.err.println("Failed to process record " + record.getId() + ": " + e.getMessage());
}
```

---

### **Step 3: Update `processData`**

```java
public void processData(List<DataRecord> records) {
    for (DataRecord record : records) {
        handleRecordProcessing(record);
    }
}
```

**Benefits:**

* Error handling is encapsulated and easier to change.
* Main processing code is now focused purely on logic, not errors.

---

## **Prompt 3: Conditional Logic Simplification**

Imagine we **add conditional processing**:

* If value < 0 → mark as INVALID
* If value > 100 → cap at 100
* Else → double the value

Original logic might look like:

```java
int val = Integer.parseInt(record.getValue());
if(val < 0) {
    record.setValue("INVALID");
} else if(val > 100) {
    record.setValue("100");
} else {
    record.setValue(String.valueOf(val * 2));
}
```

### **Step 1: Identify logical paths**

1. Negative → mark invalid.
2. Too large → cap at 100.
3. Normal → double value.

---

### **Step 2: Extract into named functions**

```java
private void handleNegativeValue(DataRecord record, int value) {
    record.setValue("INVALID");
}

private void handleLargeValue(DataRecord record, int value) {
    record.setValue("100");
}

private void handleNormalValue(DataRecord record, int value) {
    record.setValue(String.valueOf(value * 2));
}
```

---

### **Step 3: Apply Strategy Pattern**

```java
private void processByValue(DataRecord record, int value) {
    if(value < 0) handleNegativeValue(record, value);
    else if(value > 100) handleLargeValue(record, value);
    else handleNormalValue(record, value);
}
```

---

### **Step 4: Refactored `handleRecordProcessing`**

```java
private void handleRecordProcessing(DataRecord record) {
    try {
        int value = Integer.parseInt(record.getValue());
        processByValue(record, value);
    } catch (NumberFormatException e) {
        record.setValue("ERROR");
        logRecordError(record, e);
    }
}
```

✅ **Benefits:**

* Each branch has a clear purpose.
* Easy to add more rules without bloating main logic.
* Supports testing each branch independently.

---

## **Summary of Refactoring Approach**

1. **Function Responsibility Analysis** → broke `processData` into smaller, testable units.
2. **Single-Responsibility Extraction** → isolated error handling and logging.
3. **Conditional Logic Simplification** → moved branching logic into separate functions (Strategy-like approach).

**Result:** The pipeline is **modular, readable, and maintainable**.

---



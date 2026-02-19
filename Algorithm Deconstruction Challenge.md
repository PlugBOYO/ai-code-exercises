\\

---

## **Algorithm 1: Task Priority Sorting Algorithm**

**AI Prompt Used:** Prompt 1 – Understand an Algorithm Through Step-by-Step Analysis

### Step-by-Step Analysis

**Your Understanding Attempt:**

1. This function calculates a numerical priority score for a task based on several factors.
2. Inputs: `Task` objects; Returns: an `int` score for a task, used to sort tasks by importance.
3. Confusing part: How multiple factors combine and why some weights are chosen.

**Breakdown of the Algorithm:**

**1️⃣ Base Priority Score**

* Each `TaskPriority` has a base weight: LOW=1, MEDIUM=2, HIGH=3, URGENT=4
* Multiply by 10 → score range from 10 to 40

**2️⃣ Due Date Factor**

* Calculate `daysUntilDue = dueDate - now`
* Adjust score:

  * Overdue → +30
  * Due today → +20
  * Due in 2 days → +15
  * Due in a week → +10

**3️⃣ Status Adjustment**

* DONE → -50
* REVIEW → -15

**4️⃣ Tags Boost**

* Tasks with tags `"blocker"`, `"critical"`, `"urgent"` → +8

**5️⃣ Recent Update Boost**

* If last updated < 1 day ago → +5

**6️⃣ Sorting**

* Sort tasks by descending `calculateTaskScore(task)`

**7️⃣ Top N Tasks**

* Use `sortTasksByImportance(tasks).limit(N)`

---

**Example Execution:**

| Task   | Priority | Due Date  | Status  | Tags        | Score Calculation                 | Final Score |
| ------ | -------- | --------- | ------- | ----------- | --------------------------------- | ----------- |
| Task A | HIGH     | Tomorrow  | PENDING | ["work"]    | Base=30, due in 1 day +15 → 45    | 45          |
| Task B | MEDIUM   | Overdue   | PENDING | ["blocker"] | Base=20 + overdue 30 + tag 8 → 58 | 58          |
| Task C | LOW      | Next week | DONE    | []          | Base=10 - DONE50 + due 10 → -30   | -30         |

**Core Technique/Pattern:**

* **Weighted scoring system** combining multiple priority factors.
* Uses conditional logic to boost/reduce scores.
* Sorts based on computed metric → **ranking algorithm**.

**Non-obvious Tricks:**

* Recent updates (+5) subtly prioritize “active” tasks.
* Status overrides due date/priority to prevent completed tasks from surfacing.

---

**Targeted Questions:**

1. How would the score change if a task is both overdue and tagged "urgent"?
2. What happens if `dueDate` is null? How does it affect sorting?
3. How does the algorithm handle multiple tasks with the same score?

**Insights & Learning Points:**

* Weighted scoring gives flexibility to adjust task importance.
* Combining static (priority) and dynamic (due date/status/update) factors is effective for task ranking.
* Completed tasks are penalized heavily to push them down the list.

---

## **Algorithm 2: Task Text Parser**

**AI Prompt Used:** Prompt 2 – Decipher Code with Unclear Intent or Poor Documentation

### Understanding Attempt

**Your Understanding:**

* Code appears in a task input system.
* It parses free-form text into structured task objects.
* Variables manipulate title, priority, dueDate, tags.
* Likely purpose: Allow users to quickly add tasks using short text commands.

**Better Names:**

| Current            | Suggested                        |
| ------------------ | -------------------------------- |
| `title`            | `taskTitle`                      |
| `priorityPattern`  | `priorityRegex`                  |
| `tagPattern`       | `tagRegex`                       |
| `getNextWeekday()` | `computeNextWeekdayOccurrence()` |

**Programming Patterns/Techniques:**

* Regex parsing for text extraction
* Switch-case mapping for priority conversion
* Loop over parsed dates for validation
* Builder pattern for Task creation

**Pseudocode (Simplified):**

```
function parseTask(text):
    title = text
    priority = MEDIUM
    tags = []
    dueDate = null

    if text contains !priority:
        extract and remove from title
        convert to enum

    if text contains @tag:
        extract tags and remove from title

    if text contains #date:
        extract date(s) and remove from title
        parse date keywords or YYYY-MM-DD

    return new Task(title, priority, dueDate, tags)
```

**Documentation Example:**

```java
/**
 * Converts a free-form string into a Task object.
 * Supports inline tags (@), priority (!), and due dates (#).
 * Example: "Finish report @work !urgent #tomorrow"
 */
```

**Validation Questions:**

1. What happens if the text contains multiple priorities? Which one is picked?
2. How are ambiguous date strings handled (e.g., "fri" when today is Friday)?
3. Does it handle invalid date formats gracefully?
4. Are tags case-sensitive?

**Safe Experiments:**

* Input a task string with multiple `!` markers and observe which priority is set.
* Try `#today`, `#tomorrow`, and a specific date.
* Include tags and see if they are parsed correctly.

**Insights & Learning Points:**

* Allows rapid task creation without UI interaction.
* Regex + keyword mapping is an elegant lightweight parser.
* Date handling is robust but limited to simple keywords.

---

## **Algorithm 3: Task List Merging (Two-Way Sync)**

**AI Prompt Used:** Prompt 3 – Understand Complex Logic and Control Flow

### Understanding Attempt

**Your Understanding:**

* Merges local and remote task lists.
* Resolves conflicts using “most recent update wins” + “completed wins” rule.
* Produces maps for tasks that need creation/update locally or remotely.

**Control Flow Breakdown:**

1️⃣ Collect all unique task IDs.
2️⃣ For each task ID:

* Only local → create remote
* Only remote → create local
* Both → resolve conflicts

3️⃣ Conflict Resolution:

* Most recent update overwrites fields
* Completed status overrides non-completed
* Tags are merged as union
* Update timestamps to latest

**Refactoring/Segmenting:**

* Step 1: Identify new tasks
* Step 2: Identify updates
* Step 3: Resolve conflicts
* Step 4: Compile merge result

**Visual Representation:**

```
All Task IDs
 ├─ Only Local ─> merged + toCreateRemote
 ├─ Only Remote ─> merged + toCreateLocal
 └─ Both ─> resolveConflict
         ├─ Merge fields
         ├─ Check completion status
         ├─ Merge tags
         └─ Decide updates to local/remote
```

**Input Scenarios & Prediction Exercise:**

| Local Task                 | Remote Task | Expected Merge                  |
| -------------------------- | ----------- | ------------------------------- |
| Only local                 | None        | Add to remote                   |
| None                       | Only remote | Add to local                    |
| Both, remote newer         | Both        | Remote fields overwrite local   |
| Local DONE, remote PENDING | Both        | Status DONE wins, update remote |

**Insights & Learning Points:**

* Handles two-way synchronization safely.
* Separates “create” vs “update” for precise API actions.
* Conflict resolution rules are explicit and predictable.

---

## **Reflection Questions**

1. **How did AI’s explanation change your understanding?**

   * I better see how weights and conditional boosts combine in Task Priority.
   * I clearly understood TaskTextParser’s regex-driven parsing logic.
   * Merge algorithm flow is now visual and segmented in my mind.

2. **What aspects were still difficult?**

   * Edge cases in date parsing (like multiple dates in text).
   * How tags updates are propagated in merge if only one source changes.

3. **How would you explain to a junior developer?**

   * Task Priority → “Compute a score based on importance, due date, tags, status.”
   * Text Parser → “Extract priority, tags, and due date from a single string.”
   * Merge → “Combine two task lists, resolve conflicts, and track what to update/create.”

4. **Did you test understanding against AI?**

   * Yes, used examples with sample tasks to check scoring and merging predictions.

5. **How might you improve the algorithm?**

   * Allow Task Priority to be configurable via a settings file.
   * Enhance text parser to handle multiple priorities or complex natural language dates.
   * Optimize merge by reducing repeated set operations for tags.

---

If you want, I can create a **single diagram that visualizes all three algorithms together** in a workflow from text input → scoring → top tasks → merging. This would give a full mental model of the task system.

Do you want me to make that diagram?

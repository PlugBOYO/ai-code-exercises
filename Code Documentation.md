
## **1. Original Code**

package za.co.wethinkcode.taskmanager.app;

import za.co.wethinkcode.taskmanager.model.Task;
import za.co.wethinkcode.taskmanager.model.TaskPriority;
import za.co.wethinkcode.taskmanager.model.TaskStatus;
import za.co.wethinkcode.taskmanager.storage.TaskStorage;

import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.LocalTime;
import java.time.format.DateTimeFormatter;
import java.time.format.DateTimeParseException;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class TaskManager {
    private final TaskStorage storage;

    public TaskManager(String storagePath) {
        this.storage = new TaskStorage(storagePath);
    }

    TaskStorage getStorage() {
        return storage;
    }

    public String createTask(String title, String description, int priorityValue,
                             String dueDateStr, List<String> tags) {
        TaskPriority priority = TaskPriority.fromValue(priorityValue);
        LocalDateTime dueDate = null;

        if (dueDateStr != null && !dueDateStr.isEmpty()) {
            try {
                LocalDate localDate = LocalDate.parse(dueDateStr, DateTimeFormatter.ISO_DATE);
                dueDate = LocalDateTime.of(localDate, LocalTime.MAX);
            } catch (DateTimeParseException e) {
                System.err.println("Invalid date format. Use YYYY-MM-DD");
                return null;
            }
        }

        Task task = new Task(title, description, priority, dueDate, tags);
        return getStorage().addTask(task);
    }

    public List<Task> listTasks(String statusFilter, Integer priorityFilter, boolean showOverdue) {
        if (showOverdue) {
            return getStorage().getOverdueTasks();
        }

        if (statusFilter != null) {
            TaskStatus status = TaskStatus.fromValue(statusFilter);
            return getStorage().getTasksByStatus(status);
        }

        if (priorityFilter != null) {
            TaskPriority priority = TaskPriority.fromValue(priorityFilter);
            return getStorage().getTasksByPriority(priority);
        }

        return getStorage().getAllTasks();
    }

    public boolean updateTaskStatus(String taskId, String newStatusValue) {
        TaskStatus newStatus = TaskStatus.fromValue(newStatusValue);
        Task task = getStorage().getTask(taskId);
        if (task != null) {
            task.setStatus(newStatus);
            if (newStatus == TaskStatus.DONE) {
                task.markAsDone();
            }
            getStorage().save();
            return true;
        }
        return false;
    }

    public boolean updateTaskPriority(String taskId, int newPriorityValue) {

        TaskPriority newPriority = null;
        try {
            newPriority = TaskPriority.fromValue(newPriorityValue);
        } catch (IllegalArgumentException e) {
            System.err.println(e.getMessage());
            return false;
        }

        Task updates = new Task("tempTitle");
        updates.setPriority(newPriority);
        return getStorage().updateTask(taskId, updates);
    }

    public boolean updateTaskDueDate(String taskId, String dueDateStr) {
        try {
            LocalDate localDate = LocalDate.parse(dueDateStr, DateTimeFormatter.ISO_DATE);
            LocalDateTime dueDate = LocalDateTime.of(localDate, LocalTime.MAX);

            Task updates = new Task("tempTitle");
            updates.setDueDate(dueDate);
            return getStorage().updateTask(taskId, updates);
        } catch (DateTimeParseException e) {
            System.err.println("Invalid date format. Use YYYY-MM-DD");
            return false;
        }
    }

    public boolean deleteTask(String taskId) {
        return getStorage().deleteTask(taskId);
    }

    public Task getTaskDetails(String taskId) {
        return getStorage().getTask(taskId);
    }

    public boolean addTagToTask(String taskId, String tag) {
        Task task = getStorage().getTask(taskId);
        if (task != null) {
            task.addTag(tag);
            getStorage().save();
            return true;
        }
        return false;
    }

    public boolean removeTagFromTask(String taskId, String tag) {
        Task task = getStorage().getTask(taskId);
        if (task != null && task.removeTag(tag)) {
            getStorage().save();
            return true;
        }
        return false;
    }

    public Map<String, Object> getStatistics() {
        List<Task> tasks = getStorage().getAllTasks();
        int total = tasks.size();

        // Count by status
        Map<String, Integer> statusCounts = new HashMap<>();
        for (TaskStatus status : TaskStatus.values()) {
            statusCounts.put(status.getValue(), 0);
        }

        for (Task task : tasks) {
            String statusValue = task.getStatus().getValue();
            statusCounts.put(statusValue, statusCounts.get(statusValue) + 1);
        }

        // Count by priority
        Map<Integer, Integer> priorityCounts = new HashMap<>();
        for (TaskPriority priority : TaskPriority.values()) {
            priorityCounts.put(priority.getValue(), 0);
        }

        for (Task task : tasks) {
            int priorityValue = task.getPriority().getValue();
            priorityCounts.put(priorityValue, priorityCounts.get(priorityValue) + 1);
        }

        // Count overdue
        int overdueCount = (int) tasks.stream().filter(Task::isOverdue).count();

        // Count completed in last 7 days
        LocalDateTime sevenDaysAgo = LocalDateTime.now().minusDays(7);
        int completedRecently = (int) tasks.stream()
                .filter(task -> task.getCompletedAt() != null && task.getCompletedAt().isAfter(sevenDaysAgo))
                .count();

        Map<String, Object> stats = new HashMap<>();
        stats.put("total", total);
        stats.put("byStatus", statusCounts);
        stats.put("byPriority", priorityCounts);
        stats.put("overdue", overdueCount);
        stats.put("completedLastWeek", completedRecently);

        return stats;
    }
}

---

## **2. Documentation Using Prompt 1 – Comprehensive Function Documentation**

Here’s a **Javadoc-style documentation** for `TaskManager`:

```java
package za.co.wethinkcode.taskmanager.app;

import za.co.wethinkcode.taskmanager.model.Task;
import za.co.wethinkcode.taskmanager.model.TaskPriority;
import za.co.wethinkcode.taskmanager.model.TaskStatus;
import za.co.wethinkcode.taskmanager.storage.TaskStorage;

import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.LocalTime;
import java.time.format.DateTimeFormatter;
import java.time.format.DateTimeParseException;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

/**
 * TaskManager provides methods to manage tasks including creating, listing, updating, 
 * deleting, and retrieving task statistics.
 * 
 * It acts as a service layer between the user and the TaskStorage.
 */
public class TaskManager {
    private final TaskStorage storage;

    /**
     * Initializes a TaskManager with a given storage path.
     * 
     * @param storagePath the file path for storing tasks
     */
    public TaskManager(String storagePath) {
        this.storage = new TaskStorage(storagePath);
    }

    /**
     * Returns the internal TaskStorage instance.
     * 
     * @return the storage instance
     */
    TaskStorage getStorage() {
        return storage;
    }

    /**
     * Creates a new task with the given details and stores it.
     * 
     * @param title       the task title
     * @param description the task description
     * @param priorityValue the numeric value representing the task priority
     * @param dueDateStr  the task due date in YYYY-MM-DD format (optional)
     * @param tags        a list of tags associated with the task
     * @return the unique ID of the created task, or null if due date format is invalid
     */
    public String createTask(String title, String description, int priorityValue,
                             String dueDateStr, List<String> tags) {
        ...
    }

    /**
     * Lists tasks filtered by status, priority, or overdue flag.
     * 
     * @param statusFilter   status filter as a string (optional)
     * @param priorityFilter numeric priority filter (optional)
     * @param showOverdue    if true, returns only overdue tasks
     * @return list of tasks matching the given filters
     */
    public List<Task> listTasks(String statusFilter, Integer priorityFilter, boolean showOverdue) {
        ...
    }

    /**
     * Updates the status of a task.
     * 
     * @param taskId        the ID of the task to update
     * @param newStatusValue the new status as a string
     * @return true if the task was found and updated, false otherwise
     */
    public boolean updateTaskStatus(String taskId, String newStatusValue) {
        ...
    }

    /**
     * Updates the priority of a task.
     * 
     * @param taskId           the ID of the task to update
     * @param newPriorityValue the new priority value
     * @return true if updated successfully, false otherwise
     */
    public boolean updateTaskPriority(String taskId, int newPriorityValue) {
        ...
    }

    /**
     * Updates the due date of a task.
     * 
     * @param taskId     the ID of the task to update
     * @param dueDateStr new due date in YYYY-MM-DD format
     * @return true if updated successfully, false otherwise
     */
    public boolean updateTaskDueDate(String taskId, String dueDateStr) {
        ...
    }

    /**
     * Deletes a task by ID.
     * 
     * @param taskId the ID of the task to delete
     * @return true if deletion was successful, false otherwise
     */
    public boolean deleteTask(String taskId) {
        ...
    }

    /**
     * Retrieves details of a specific task.
     * 
     * @param taskId the ID of the task
     * @return the Task object, or null if not found
     */
    public Task getTaskDetails(String taskId) {
        ...
    }

    /**
     * Adds a tag to a task.
     * 
     * @param taskId the ID of the task
     * @param tag    the tag to add
     * @return true if added successfully, false otherwise
     */
    public boolean addTagToTask(String taskId, String tag) {
        ...
    }

    /**
     * Removes a tag from a task.
     * 
     * @param taskId the ID of the task
     * @param tag    the tag to remove
     * @return true if removed successfully, false otherwise
     */
    public boolean removeTagFromTask(String taskId, String tag) {
        ...
    }

    /**
     * Returns statistics about tasks including counts by status, priority,
     * overdue tasks, and tasks completed in the last 7 days.
     * 
     * @return a map containing statistics with keys:
     *         - "total": total number of tasks
     *         - "byStatus": map of status to count
     *         - "byPriority": map of priority to count
     *         - "overdue": count of overdue tasks
     *         - "completedLastWeek": count of tasks completed in last 7 days
     */
    public Map<String, Object> getStatistics() {
        ...
    }
}
```

✅ **Prompt 1 Notes**:

* Covers all public methods and parameters.
* Notes optional arguments (`dueDateStr`, `tags`).
* Specifies return values and error cases (like invalid date parsing).

---

## **3. Prompt 2 – Intent and Logic Explanation**

**High-Level Intent:**
`TaskManager` is a service class that provides all core task management operations: create, update, delete, query, and collect statistics. It abstracts the storage layer (`TaskStorage`) so other parts of the app don’t need to deal with storage directly.

**Step-by-Step Logic Highlights:**

1. **Creating Tasks:**

   * Converts integer priority to `TaskPriority` enum.
   * Parses optional due date string.
   * Returns `null` if date parsing fails.
2. **Listing Tasks:**

   * Filters by overdue first.
   * Then by status or priority if specified.
   * Defaults to returning all tasks.
3. **Updating Tasks:**

   * Status: Updates task and marks as done if status is `DONE`.
   * Priority & Due Date: Create a temporary `Task` object with only the field to update, then call `storage.updateTask`.
4. **Tags Management:**

   * Checks task exists, then modifies tags and saves storage.
5. **Statistics:**

   * Iterates all tasks to count by status, priority, overdue, and recently completed.

**Assumptions and Edge Cases:**

* Dates must be in `YYYY-MM-DD` format.
* Updating priority or status requires valid enum values.
* Temporary `Task` objects used for updates do not affect other fields.

**Suggested Inline Comments for Complex Parts:**

```java
// Convert string due date to LocalDateTime with max time for the day
// If parsing fails, return null
```

**Potential Improvements:**

* Throw exceptions instead of printing errors for invalid input, for better error handling.
* Merge repetitive code for updating tasks into a helper method.
* Consider using Optional for methods that may return null.

---

## **4. Final Combined Documentation Version (Enhanced)**

```java
/**
 * TaskManager is a service class for creating, updating, querying, deleting tasks,
 * managing tags, and generating task statistics. It abstracts the storage layer
 * (TaskStorage) for higher-level task operations.
 *
 * Usage Example:
 * <pre>
 *     TaskManager manager = new TaskManager("tasks.json");
 *     String taskId = manager.createTask("Study", "Read Java docs", 2, "2026-02-20", List.of("study", "java"));
 *     manager.updateTaskStatus(taskId, "DONE");
 *     List<Task> overdue = manager.listTasks(null, null, true);
 *     Map<String, Object> stats = manager.getStatistics();
 * </pre>
 *
 * Notes:
 * - All date strings must be in "YYYY-MM-DD" format.
 * - Enum-based fields (status, priority) must have valid values.
 * - Task updates use temporary Task objects to modify individual fields.
 */
public class TaskManager {
    ...
}
```

---

### ✅ **Key Learnings from This Exercise**

1. **Most challenging parts for AI:**

   * Explaining nuanced logic like the temporary `Task` objects for updates.
   * Capturing edge cases like invalid date formats or overdue filtering order.

2. **Additional info needed in prompts:**

   * Code context (like enums and storage methods).
   * Expected error handling behavior.

3. **Using this approach in projects:**

   * Helps standardize documentation across methods.
   * Combines API usage instructions, logic explanation, and edge-case warnings.
   * Reduces onboarding time for new developers.

---



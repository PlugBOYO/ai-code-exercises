TASK MANAGER FEATURE ANALYSIS
Using AI Prompts to Understand Code Behavior
Exercise Part 1: Understanding Task Creation & Status Updates

(Using Prompt 1 – Understand how a specific feature works)

Feature: Task Creation and Status Updates
Files Identified

From exploring the code, the following files are involved:

Task.java – Defines the Task entity, its properties (title, status, priority, due date), and methods.

TaskManager.java – Handles creation, updating, and management of tasks.

TaskStatus.java – Enum representing possible task states (e.g., PENDING, COMPLETED).

My Initial Understanding

I believed:

Task creation happens inside TaskManager, likely through an addTask() method.

Status updates are handled via a setter method (setStatus()).

TaskManager stores tasks in a collection (probably a List).

Status changes modify the task object directly in memory.

What I Learned from Prompt 1
1. What the Component Actually Does

Task.java represents the data model.

TaskManager acts as the coordinator.

TaskStatus defines allowed lifecycle states.

Together, they implement a simple domain-driven structure.

2. Execution Flow When a Task is Created

User triggers task creation (via CLI/UI).

TaskManager creates a new Task object.

Task is added to a List collection.

Task defaults to a specific status (e.g., PENDING).

3. Execution Flow When Status is Updated

User selects task ID.

TaskManager locates task in List.

Calls setStatus() on the Task object.

State is updated in memory.

4. Data Storage

Tasks are stored in a List inside TaskManager.

There is no persistent database.

State lives in memory during runtime.

5. Design Pattern Observed

Encapsulation: Task manages its own fields.

Enum-based State Management: TaskStatus limits allowed states.

Manager Pattern: TaskManager centralizes business logic.

Journal Reflection – Part 1
Main Components

Task (Domain Model)

TaskStatus (State Enum)

TaskManager (Business Logic + State Holder)

Key Insight

State is directly modified in-memory. There is no persistence layer, which simplifies flow but limits scalability.

Exercise Part 2: Task Prioritization System

(Using Prompt 2 – Guided Questioning)

My Initial Understanding

The prioritization system:

Uses an enum TaskPriority.

Each Task has a priority field.

Priority influences sorting or business decisions.

Priority does not automatically enforce rules (unless coded).

I was unclear about:

Whether priority affects status changes.

Whether there is validation logic.

Whether priority influences overdue logic.

AI-Guided Questions That Helped Me

Instead of direct answers, the guided questions made me investigate:

Where is priority assigned?

Is priority immutable or changeable?

Does TaskManager treat HIGH priority differently?

What happens if priority is null?

Are tasks sorted by priority anywhere?

What I Discovered

Priority is a simple attribute, not behavior-driven.

There is no automatic enforcement based on priority.

It acts as metadata rather than logic.

No built-in validation prevents invalid states.

Misconceptions Corrected

❌ I assumed priority influenced task lifecycle automatically.
✅ It only becomes meaningful when business rules explicitly reference it.

Key Insight

Priority is passive data until business logic activates it.

This clarified how I should implement the “overdue abandoned” rule later.

Exercise Part 3: Mapping Data Flow When Marking a Task Complete

(Using Prompt 3 – Mapping Data Flow & State Management)

Entry Point

User action → “Mark task as complete”

Components Involved

TaskManager

Task

TaskStatus enum

Data Flow Path
Step 1: Input

User selects task ID.

Step 2: Retrieval

TaskManager searches List<Task>.

Step 3: State Change

Calls task.setStatus(TaskStatus.COMPLETED).

Step 4: Updated State Stored

Object in List reflects new status.

State Management

State is stored inside Task object.

The List maintains object references.

No copying occurs; updates modify the same instance.

Data Transformations

Only enum value changes.

No structural changes to object.

Potential Failure Points

Task ID not found.

Null pointer issues.

Invalid status transitions.

No validation on repeated completion.

Persistence

There is no database. State persists only during runtime.

Mental Model

Think of the system as:

User Action
     ↓
TaskManager
     ↓
Modify Task Object
     ↓
Stored in List


This is a simple in-memory state management system.

Exercise Part 4: Reflection & Presentation Summary
High-Level Architecture Overview

Java application

Object-oriented design

In-memory state management

Central manager class controlling operations

How Key Features Work
1. Task Creation

Creates new Task object → stores in List → default status assigned.

2. Prioritization

Enum value attached to Task → used only when referenced by business rules.

3. Task Completion

Manager retrieves task → updates status → state changes in memory.

Interesting Design Pattern Discovered

Manager Pattern + Enum State Control

TaskManager centralizes behavior.

Enum restricts allowed states.

Simple but effective design.

Most Challenging Aspect

Understanding how state was managed without a database.

The prompts helped by:

Breaking flow into steps.

Encouraging diagramming.

Forcing me to track data movement.

Helping distinguish data vs behavior.

Final Outcomes Achieved

By the end of this exercise, I can:

✔ Explain how the Task Manager works
✔ Describe data flow for task creation and completion
✔ Understand how state is stored and modified
✔ Identify where business rules should be implemented
✔ Use AI prompts strategically to explore unfamiliar systems

Prompt Strategy Effectiveness
Prompt	Best For
Prompt 1	Understanding how a feature works
Prompt 2	Deep conceptual understanding
Prompt 3	Mapping data flow & state
Final Reflection

This exercise changed my approach from:

“Reading random code and hoping it makes sense”

To:

“Systematically mapping structure, flow, and domain meaning.”

The most powerful insight was learning that:

Understanding state and data flow is more important than understanding syntax.

These prompt strategies are now tools I can use for any unfamiliar codebase.

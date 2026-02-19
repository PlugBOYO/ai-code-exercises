1. Initial vs. Final Understanding of the Task Manager Codebase
Initial Understanding

When I first examined the Task Manager project, my understanding was primarily based on reviewing the directory structure, file names, and skimming key Java files. I initially assumed:

The application is a Java-based Task Management system.

The architecture follows a basic layered structure.

TaskManager.java likely serves as the central coordinating class.

Core entities include Task, TaskStatus, and TaskPriority.

Business logic may be embedded directly inside the manager class rather than separated into service layers.

At this stage, I was unsure about:

Where business rules were enforced.

How responsibilities were distributed across classes.

Whether the system had clear architectural boundaries.

Final Understanding

After applying structured AI prompts and systematically analyzing the codebase, my understanding became significantly clearer.

The Task Manager project:

Is built using Java and object-oriented design principles.

Contains core domain entities such as:

Task

TaskStatus

TaskPriority

Uses a central coordinating class (TaskManager) to manage operations and enforce business rules.

Follows a logical layered design:

Domain Layer → Entities and enums

Business Logic Layer → TaskManager

Interface Layer → Application entry point / UI (if applicable)

I now understand that:

Domain models represent real-world business concepts.

Business rules should be implemented in the manager/service layer.

Features often affect multiple layers of the system.

My understanding evolved from structural assumptions to recognizing architectural responsibilities and business relationships.

2. Most Valuable Insights from Each Prompt
Prompt 1: Understanding Project Structure

This prompt helped me:

Validate assumptions about architecture.

Identify application entry points.

Recognize how configuration files reveal technologies used.

Understand folder and file responsibilities.

Most valuable insight:
Always analyze structure before diving into implementation details.

Prompt 2: Finding Feature Implementation

When exploring how to implement “Task Export to CSV,” I learned to:

Use targeted search strategies (e.g., file operations, data transformation).

Trace data flow from model to output.

Break features into smaller components:

Data retrieval

Data formatting

File writing

Most valuable insight:
Features span multiple components. Understanding flow is more important than finding a single method.

Prompt 3: Understanding Domain Model

This prompt helped me:

Think in business terms instead of code syntax.

Clarify relationships between entities.

Connect domain concepts to user-facing functionality.

Most valuable insight:
Strong understanding of the domain model is essential before modifying business logic.

3. Approach to Implementing the New Business Rule
Business Rule

“Tasks that are overdue for more than 7 days should automatically be marked as abandoned unless they are marked as high priority.”

Affected Components

The following components would need modification:

TaskStatus → Add new status: ABANDONED

Task → Ensure it supports due dates and priority checks

TaskManager → Implement the business rule logic

Implementation Plan

Add ABANDONED to the TaskStatus enum.

Ensure Task contains:

Due date

Priority attribute

Implement logic in TaskManager:

Check if task is overdue by more than 7 days.

Exclude tasks with HIGH priority.

Update status to ABANDONED.

Determine when this rule should execute:

On system startup?

On scheduled interval?

On task retrieval?

Questions for the Team

Should abandoned tasks be reversible?

Should users be notified?

Does “7 days overdue” refer to calendar days or business days?

Should high-priority overdue tasks trigger alerts instead?

4. Strategies Developed for Approaching Unfamiliar Code

Through this exercise, I developed structured strategies:

1. Analyze Structure First

Review directories and configuration files before reading detailed code.

2. Form Early Hypotheses

Write down assumptions and validate them.

3. Think in Terms of Responsibilities

Identify what each class is responsible for rather than focusing only on methods.

4. Trace Features End-to-End

Follow how data flows through the system.

5. Understand the Domain Before Changing Logic

Business rules must align with domain meaning, not just technical implementation.

Final Reflection

This exercise transformed my approach from passive code reading to structured architectural reasoning.

Initially, my understanding was based on assumptions and surface-level exploration. After applying AI prompts:

I identified architectural patterns.

I understood the domain model clearly.

I confidently planned a business rule implementation.

I developed a repeatable strategy for analyzing unfamiliar systems.

The most important takeaway is that understanding system structure and domain logic first significantly improves implementation accuracy and confidence.

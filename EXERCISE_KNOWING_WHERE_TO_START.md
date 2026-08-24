# Exercise: Knowing Where to Start - Complete Workbook

**Repository:** Rev1994-bot/ai-code-exercises  
**Language:** Python  
**Focus:** Understanding the Task Manager Application  
**Date Started:** 2026-08-24

---

## Overview

This exercise teaches how to navigate an unfamiliar codebase using three AI-powered approaches:
1. **Understanding Project Structure and Technology Stack**
2. **Finding Feature Implementation Locations**
3. **Understanding Domain Models and Business Concepts**

Each section includes a prompt template, practical application, and specific exercises to validate your understanding.

---

# PART 1: Understanding Project Structure and Technology Stack

## Initial Assessment (Before Using AI)

### My Current Understanding:
- **What the application does:** A command-line task management system where users can create, track, and manage tasks with different statuses and priorities
- **Technologies used:** Python 3.11+, JSON for storage, standard library only (no external dependencies)
- **Folder structure pattern:** Layered architecture - presentation layer (CLI), business logic, data/persistence layer
- **Areas of confusion:** How exactly the JSON serialization/deserialization works with custom objects; why certain design patterns were chosen

---

## Directory Structure

```
use-cases/code-comprehension-001/python/TaskManager/
├── models.py              # Domain model classes (Task, TaskPriority, TaskStatus)
├── task_manager.py        # Core business logic orchestrator
├── storage.py             # JSON persistence layer with custom encoder/decoder
├── cli.py                 # Command-line interface with argparse
├── tests/                 # Unit tests directory
├── README.md              # Usage documentation
└── .gitignore             # Git ignore file
```

---

## Key Configuration & Dependency Files

**No external dependencies file needed** - This project uses only Python standard library.

**Python Version:** 3.11+

**Modules used from standard library:**
```python
# From models.py
from datetime import datetime
from enum import Enum
import uuid

# From storage.py
import json
import os
from datetime import datetime

# From cli.py
import argparse
from datetime import datetime

# From task_manager.py
import argparse
from datetime import datetime, timedelta
```

---

## Prompt 1 Application: Understanding Project Structure

### Using the Provided Prompt Template

**My Question to AI:**

---

I'm a junior developer who just joined this project. I've read the README but still need help understanding the project structure and technology stack.

Here's my current understanding of the project:
- It seems to be a **command-line task management system** where users can create tasks, mark them as done, add tags, and filter by various criteria
- It appears to use **Python 3.11+ with only standard library** (no external package dependencies)
- The folder structure seems to follow a **layered architecture pattern** with separation between CLI, business logic, and data persistence

**Project structure:**
```
TaskManager/
├── models.py          # Domain model (Task, TaskPriority, TaskStatus enums)
├── task_manager.py    # Business logic orchestrator
├── storage.py         # JSON file persistence layer
├── cli.py             # Command-line interface using argparse
└── tests/             # Unit tests
```

**Key configuration files:**
- **No configuration files** - Pure Python project
- **No dependencies file** - Uses only Python standard library (datetime, enum, uuid, json, os, argparse)
- **Storage backend:** JSON file (`tasks.json`)

Could you:
1. Validate my understanding and correct any misconceptions
2. Identify additional key technologies, frameworks, and libraries used
3. Explain what each main folder likely contains and its purpose
4. Point out where the application entry points are located
5. Suggest 3-5 specific questions I should ask my team to deepen my understanding

I'm particularly confused about:
- How the custom JSON encoder/decoder (TaskEncoder, TaskDecoder) works to serialize Python objects
- Why datetime objects are stored as ISO format strings in JSON
- Whether there are any hidden dependencies or patterns I'm missing

After your explanation, could you suggest a small exploration exercise I could do to verify my understanding of the project structure?

---

### Expected AI Response Analysis

**What we should learn:**
- ✅ The 3-tier layered architecture is correct
- ✅ No external dependencies is correct (benefit: simple deployment)
- ✅ JSON persistence is appropriate for this application size
- ✅ Datetime serialization to ISO format is standard practice
- ✅ UUID for task IDs enables distributed generation

**Key insights gained:**
1. **Entry point:** `cli.py`'s `main()` function is where execution starts
2. **Data flow:** User input → CLI parsing → TaskManager orchestration → Storage → JSON file
3. **Architecture benefits:** Each layer can be tested independently; storage could be swapped for database
4. **Design patterns identified:** 
   - Layered architecture
   - Repository pattern (TaskStorage)
   - Domain model pattern
   - Enum for type safety

---

## AI's Validation Questions for You

(After receiving AI response, these are typical validation questions)

1. **Folder Purpose:** Why do you think storage and models are separate files rather than combined into one?
2. **Entry Point:** If a user runs `python cli.py create "My Task"`, which file executes first?
3. **Abstraction:** What would happen if we needed to switch from JSON to a database? Which files would need to change?
4. **Dependencies:** Why might the developer choose NOT to use external libraries like SQLAlchemy or Click?
5. **Domain vs Technical:** What's the difference between a Task object in Python memory and its representation in the JSON file?

---

## Validation Exercise

### Task 1: Trace Code Execution
**Requirement:** Without running the code, trace what happens when you run:
```bash
python cli.py create "Buy groceries" -p 3 -u 2024-12-25
```

**What should happen:**
1. `cli.py` → `main()` function called
2. Argparse creates a command object with args
3. Creates TaskManager instance
4. Calls `create_task()` method
5. TaskManager validates priority (3 → TaskPriority.HIGH)
6. Parses date string "2024-12-25" → datetime object
7. Creates Task object with all attributes
8. Calls `storage.add_task(task)`
9. TaskStorage converts Task to JSON via TaskEncoder
10. Writes to `tasks.json`
11. Returns task ID to CLI
12. CLI prints success message

---

### Task 2: Identify Layer Boundaries
**Requirement:** For each file, write 1-2 sentences about its responsibility and what it depends on.

| File | Responsibility | Dependencies |
|------|-----------------|--------------|
| `models.py` | Define domain entities and enums | datetime, enum, uuid (stdlib) |
| `storage.py` | Handle JSON persistence and CRUD | models.py, json, os, datetime |
| `task_manager.py` | Orchestrate business logic | models.py, storage.py |
| `cli.py` | Parse user commands and format output | task_manager.py, models.py, argparse |

**Key finding:** There's a clear dependency direction: CLI depends on TaskManager, which depends on Storage, which depends on Models. This is a proper layered architecture!

---

### Task 3: Find the Entry Point
**Requirement:** Identify and explain the entry point of the application.

**Answer:**
```python
# cli.py, line 165-166
if __name__ == "__main__":
    main()
```

**Entry point:** `cli.py` → `main()` function
**What happens:** 
1. Creates ArgumentParser with subcommands (create, list, status, etc.)
2. Parses command-line arguments
3. Creates TaskManager instance with default storage path
4. Routes to appropriate command handler
5. Calls corresponding TaskManager method
6. Prints result to user

---

## Technology Stack Summary

| Category | Technology | Purpose |
|----------|-----------|---------|
| **Language** | Python 3.11+ | Application implementation |
| **CLI Framework** | argparse (stdlib) | Command parsing and help text |
| **Data Structure** | Enums | Type-safe priority and status values |
| **Storage** | JSON files | Persistent task storage |
| **ID Generation** | UUID (stdlib) | Unique task identifiers |
| **Date/Time** | datetime (stdlib) | Task timestamps and due dates |
| **Serialization** | Custom JSON encoder/decoder | Python object ↔ JSON conversion |

---

## Questions to Ask Your Team

1. **Architectural:** "This codebase uses a layered architecture. Were there discussions about using a framework like Django or Flask instead of building from scratch?"

2. **Scalability:** "The current design loads all tasks into memory. At what point would we consider switching to a database like SQLite or PostgreSQL?"

3. **Domain Knowledge:** "I see TaskStatus has 4 values (TODO, IN_PROGRESS, REVIEW, DONE). Are there any planned status changes or additional statuses we should support?"

4. **Testing Strategy:** "How should the tests be organized? Are there integration tests beyond unit tests for the CLI?"

5. **Extensibility:** "If we needed to add a REST API or web UI in the future, which components would we reuse?"

---

## Key Architectural Insights

### 1. Separation of Concerns
- **CLI layer** knows nothing about JSON or storage
- **Business logic** knows nothing about how tasks are persisted
- **Storage layer** is interchangeable (could swap JSON for database)
- **Models** are pure domain objects with no infrastructure concerns

### 2. No External Dependencies
**Benefit:** Simple deployment, fewer version conflicts  
**Trade-off:** More code to write for common patterns (custom JSON encoder)

### 3. In-Memory + File-Based Storage
**Pattern:** Load all tasks into memory dictionary, persist to JSON on every change  
**Benefit:** Fast lookups, simple implementation  
**Limitation:** Doesn't scale well beyond thousands of tasks

### 4. Type Safety with Enums
```python
# Good: Type-safe
task.priority = TaskPriority.HIGH  # ✓ Valid

# Bad: Would fail
task.priority = "high"  # ✗ TypeError at enum construction
```

### 5. UUID for Task IDs
```python
# Benefits:
- Can generate ID before saving (no database needed)
- Unique across entire system, not just database
- Hard to guess (security benefit)
```

---

# PART 2: Finding Feature Implementation Locations

## Scenario: Implementing CSV Export Feature

### Initial Search Approach

**Goal:** Add feature to export tasks to CSV format

**Search terms I tried:**
- "export" — No results
- "file" — Found storage.py file operations
- "format" — Found format_task() in cli.py for display
- "json" — Found storage.py JSON serialization
- "writer" — No results
- "csv" — No results (feature doesn't exist yet)

**Directories I checked:**
- `models.py` — Defined data structures, no export logic
- `storage.py` — Has TaskStorage for persistence, but only JSON support
- `task_manager.py` — Business logic methods, no export methods
- `cli.py` — Has format_task() that transforms Task for display

---

## Prompt 2 Application: Finding Feature Implementation

### Using the Provided Prompt Template

**My Question to AI:**

---

I need to work on the **CSV export feature** in this codebase, but I'm not sure where the code for this feature lives.

My approach so far:
- I've searched for keywords like "export", "file", "writer", "csv" but found no existing export functionality
- I looked in `storage.py` (where data persistence happens) and `cli.py` (where output formatting happens) which seemed most relevant
- I think the feature might relate to **data serialization and formatting**, similar to how `format_task()` in `cli.py` transforms a Task for terminal display

**Project structure:**
```
TaskManager/
├── models.py          # Task, TaskPriority, TaskStatus
├── task_manager.py    # Business logic: create, list, filter tasks
├── storage.py         # JSON persistence (TaskStorage class)
├── cli.py             # CLI commands and formatting
└── tests/             # Unit tests
```

**Based on my search, these files might be relevant:**
1. `storage.py` — Has `get_all_tasks()` and filtering methods
2. `cli.py` — Has `format_task()` that transforms data for display
3. `task_manager.py` — Has `get_statistics()` that shows data extraction patterns

**Can you help me:**
1. **Evaluate my search approach** and suggest improvements
2. **Identify which files and directories** most likely contain the implementation for this feature
3. **Suggest specific search terms or patterns** that would be more effective
4. **Explain what parts of the feature** might be located in different areas of the codebase
5. **Recommend a step-by-step investigation process** to understand the complete feature flow

Also, what questions could I ask myself as I'm exploring the code to ensure I'm on the right track? What specific patterns should I look for to confirm I've found all the relevant parts?

After your guidance, could you give me a small challenge to test my understanding of how to navigate this feature's code?

---

### Expected Implementation Plan

**Where should CSV export go?**

1. **Business Logic Layer** (`task_manager.py`)
   - Add method: `export_tasks_to_csv(filename, task_ids=None)`
   - Reuse existing `get_all_tasks()` or filtered retrieval methods
   - Handle date formatting for CSV

2. **CLI Layer** (`cli.py`)
   - Add new subcommand: `export`
   - Arguments: `--format` (csv, json, default csv), `--output` (filename)
   - Handle file writing and error messages

3. **No changes needed** (`storage.py`, `models.py`)
   - These layers provide data access, format-agnostic
   - TaskStorage continues to handle only JSON persistence

**Implementation pattern to follow:**
```python
# In task_manager.py
def export_tasks_to_csv(self, filename, filter_status=None):
    """Export tasks to CSV file"""
    tasks = self.list_tasks(status_filter=filter_status)
    # Write to CSV using csv module from stdlib
    # Return success/failure

# In cli.py
# Add "export" subcommand handler
elif args.command == "export":
    success = task_manager.export_tasks_to_csv(args.output, args.status)
    print(f"Exported {len(tasks)} tasks to {args.output}")
```

---

## Self-Assessment Questions While Exploring

**Ask yourself these questions to validate your exploration:**

1. **Data Source:** "Where in the code are all the tasks retrieved? Can I reuse that?"
   - Answer: `storage.get_all_tasks()` in storage.py, wrapped by `task_manager.list_tasks()`

2. **Data Format:** "What information about each task do I need to include in the CSV?"
   - Answer: id, title, description, priority, status, due_date, tags (from Task attributes)

3. **Dependencies:** "What modules do I need to import? Which are already imported?"
   - Answer: Need to import `csv` module (stdlib), other imports already done

4. **Similar Patterns:** "Is there existing code that does similar data transformation?"
   - Answer: `format_task()` in cli.py, `get_statistics()` in task_manager.py

5. **Integration Points:** "Where in the CLI should this command be defined?"
   - Answer: In main() function's subparsers, pattern: create_parser, list_parser, etc.

6. **Error Handling:** "What could go wrong? How do other methods handle errors?"
   - Answer: File I/O errors in storage.py (wrapped in try/except)

---

## Patterns to Look For

**Pattern 1: Data Retrieval**
```python
# Already exists in task_manager.py
def get_all_tasks(self):
    return self.storage.get_all_tasks()
```
✅ Can reuse for CSV export

**Pattern 2: Data Transformation**
```python
# Already exists in cli.py
def format_task(task):
    # Transforms Task object into formatted string
```
✅ Similar pattern for CSV serialization

**Pattern 3: Error Handling**
```python
# Already exists in storage.py
try:
    with open(self.storage_path, 'w') as f:
        json.dump(...)
except Exception as e:
    print(f"Error saving tasks: {e}")
```
✅ Follow this pattern for file I/O

**Pattern 4: CLI Command Definition**
```python
# Already exists in cli.py
create_parser = subparsers.add_parser("create", help="Create a new task")
create_parser.add_argument("title", help="Task title")
```
✅ Follow this pattern for export command

---

## Feature Flow Diagram

```
User Command
    ↓
cli.py: parse "export" command with --output and --status args
    ↓
task_manager.py: export_tasks_to_csv(filename, filter_status)
    ↓
task_manager.py: call self.list_tasks(status_filter=filter_status)
    ↓
storage.py: get_tasks_by_status(status) or get_all_tasks()
    ↓
[List of Task objects]
    ↓
task_manager.py: transform to CSV format using csv.DictWriter
    ↓
task_manager.py: write to file with error handling
    ↓
cli.py: print success message with task count and filename
```

---

## Validation Challenge

**Challenge:** Map out the complete CSV export feature flow

**Requirements:**
1. Start with CLI entry point (which function in cli.py handles "export" command?)
2. Trace to TaskManager (which method would you call?)
3. Identify data source (which Storage method provides tasks?)
4. Show CSV transformation (what data from Task goes into CSV?)
5. Handle errors (where could failures occur?)

**Answer structure:**
```
Entry: cli.py → main() → "export" subcommand handler
  ↓
Call: task_manager.export_tasks_to_csv(args.output, args.status)
  ↓
Retrieve: task_manager.list_tasks(status_filter=args.status)
  ↓
Access: storage.get_tasks_by_status(status) [or get_all_tasks()]
  ↓
Transform: Convert Task objects → CSV rows
  Fields: id, title, description, priority, status, due_date, tags
  ↓
Write: Open file, write CSV header, write task rows
  ↓
Error Handle: Catch IOError, permission errors
  ↓
Return: success/failure status
  ↓
Output: cli.py prints results to user
```

---

# PART 3: Understanding Domain Models and Business Concepts

## Domain Model Exploration

### My Current Understanding of Entities

```python
Task
├── Core Identification
│   └── id: UUID (unique, generated at creation)
├── Task Metadata
│   ├── title: str (required, what the task is)
│   ├── description: str (optional, more details)
│   └── tags: list[str] (labels for categorization)
├── Lifecycle Management
│   ├── status: TaskStatus enum (TODO → IN_PROGRESS → REVIEW → DONE)
│   └── priority: TaskPriority enum (LOW, MEDIUM, HIGH, URGENT)
├── Time Tracking
│   ├── created_at: datetime (when task created)
│   ├── updated_at: datetime (when last modified)
│   ├── due_date: datetime (optional deadline)
│   └── completed_at: datetime (when marked DONE)
└── Methods
    ├── update() - modify attributes
    ├── mark_as_done() - complete the task
    └── is_overdue() - check if past deadline
```

### Entity Relationships

```
Task ──┬── has_one ──→ TaskPriority (1-4)
       ├── has_one ──→ TaskStatus (4 states)
       ├── has_many ──→ tags (0+ strings)
       └── has_one ──→ due_date (optional date)
```

### Business Logic Rules I've Identified

1. **Task Creation**
   - Every task starts as TODO status
   - Default priority is MEDIUM (2)
   - created_at and updated_at set to current time
   - Tags are optional

2. **Status Workflow**
   - Tasks progress through states: TODO → IN_PROGRESS → REVIEW → DONE
   - When marked DONE, completed_at timestamp is set
   - DONE status means task is finished (no further changes typically)

3. **Overdue Calculation**
   - A task is overdue if: `due_date < now AND status != DONE`
   - Completed tasks are never overdue (business logic: finished = no longer overdue)

4. **Priority Semantics**
   - 1=LOW, 2=MEDIUM, 3=HIGH, 4=URGENT
   - Higher number = more urgent
   - Used for filtering and sorting

---

## Prompt 3 Application: Understanding Domain Models

### Using the Provided Prompt Template

**My Question to AI:**

---

I'd like you to act as a senior developer who deeply understands our codebase's domain model. I'm a junior developer trying to make sense of the business logic and domain concepts in this application.

Here's what I've found in the codebase:

```python
# From models.py

class TaskPriority(Enum):
    LOW = 1
    MEDIUM = 2
    HIGH = 3
    URGENT = 4

class TaskStatus(Enum):
    TODO = "todo"
    IN_PROGRESS = "in_progress"
    REVIEW = "review"
    DONE = "done"

class Task:
    def __init__(self, title, description="", priority=TaskPriority.MEDIUM,
                 due_date=None, tags=None):
        self.id = str(uuid.uuid4())
        self.title = title
        self.description = description
        self.priority = priority
        self.status = TaskStatus.TODO
        self.created_at = datetime.now()
        self.updated_at = self.created_at
        self.due_date = due_date
        self.completed_at = None
        self.tags = tags or []

    def update(self, **kwargs):
        for key, value in kwargs.items():
            if hasattr(self, key):
                setattr(self, key, value)
        self.updated_at = datetime.now()

    def mark_as_done(self):
        self.status = TaskStatus.DONE
        self.completed_at = datetime.now()
        self.updated_at = self.completed_at

    def is_overdue(self):
        if not self.due_date:
            return False
        return self.due_date < datetime.now() and self.status != TaskStatus.DONE
```

Based on this code, my current understanding is:
- The system seems to be modeling a **workflow-based task management system** where tasks move through states
- I think **TaskStatus** and **TaskPriority** are related because status represents "where in the workflow" while priority represents "how urgent"
- The **is_overdue()** method appears to represent a business rule: tasks with past due dates that aren't finished are considered overdue
- I'm confused about **why completed_at is separate from updated_at** — why do we need both?

Could you, as a senior developer:
1. **Validate my current understanding**, correcting any misconceptions
2. **Help me recognize the core domain concepts** represented in this code
3. **Explain the relationships** between these entities in business terms, not just technical relationships
4. **Clarify any domain-specific terminology** or patterns I might be missing
5. **Help me connect these models** to actual user-facing features or business processes

Then, please ask me 3-5 questions that would test my understanding of this domain model. These should be questions that make me think about the business logic, not just the code syntax.

Finally, suggest a simple diagram I could sketch to visualize these relationships that would help solidify my understanding.

---

### Expected AI Response & Analysis

**Validation of Your Understanding:**
- ✅ Correct: It is workflow-based task management
- ✅ Correct: Status is workflow position, Priority is urgency
- ✅ Correct: is_overdue() implements a specific business rule
- ✅ Important: completed_at is specifically for analytics ("tasks completed in last 7 days")

**Core Domain Concepts:**

1. **Task Lifecycle**
   - Every task has a journey from creation to completion
   - Status tracks position in that journey
   - Timestamps enable historical analysis

2. **Priority as Urgency Signal**
   - Priority is independent of status (you can have low-priority task in progress, high-priority task not started)
   - Allows for separate filtering/sorting decisions

3. **Overdue as Business Rule**
   - Not just "past due date" but specifically "past due AND not finished"
   - Captures the intent: only incomplete tasks can be overdue

4. **Tags for Flexible Categorization**
   - Independent of status/priority system
   - Allows cross-cutting organization (e.g., "work", "urgent", "project-X")

---

## Domain Model Testing Questions

### Question 1: Workflow Understanding
**Q:** "If I have a task that's been in REVIEW status for 3 weeks with a due date of yesterday, and I mark it as DONE, does it become 'not overdue' anymore? Why or why not?"

**Expected Answer:** Yes, because `is_overdue()` specifically checks `status != TaskStatus.DONE`. Once completed, even if it was late, it's no longer overdue. This makes sense business-wise: the task is done, so urgency doesn't matter anymore.

**Business Logic:** Overdue is a signal for action needed, not a permanent state.

---

### Question 2: Priority-Status Relationship
**Q:** "Can you have a LOW priority task in IN_PROGRESS status? What does that mean in real-world terms?"

**Expected Answer:** Yes, Priority and Status are independent. It could mean:
- You're working on a non-urgent task (maybe you had time, or it's maintenance work)
- Or you're context-switching and got interrupted by urgent work

**Business Logic:** Priority and Status represent different dimensions of task management.

---

### Question 3: Tag vs. Status vs. Priority
**Q:** "Why would we need tags when we already have status and priority? What information does each provide that's different?"

**Expected Answer:**
- **Status**: Fixed workflow steps (4 values defined by system)
- **Priority**: Urgency level (4 predefined levels)
- **Tags**: Custom, flexible labels (unlimited combinations)

Example: Same task could be:
- Status: IN_PROGRESS (where it is in workflow)
- Priority: HIGH (how urgent)
- Tags: ["project-deployment", "customer-critical", "blocked-on-reviews"] (contextual info)

---

### Question 4: Timestamp Strategy
**Q:** "We track created_at, updated_at, AND completed_at. What business questions can each one answer?"

**Expected Answer:**
- **created_at**: "How long has this task existed?" (age of task)
- **updated_at**: "When did the most recent change happen?" (last activity)
- **completed_at**: "How long did it take to complete?" (created_at → completed_at = duration)

Real example: Analytics might ask "How many tasks were completed in the last week?" → requires completed_at

---

### Question 5: The Overdue Logic Edge Case
**Q:** "A task with no due_date can never be overdue according to the code. What does this imply about the domain?"

**Expected Answer:** Tasks without deadlines are not time-sensitive. They might be:
- Ongoing maintenance tasks
- Nice-to-have features
- Tasks waiting for more information

This is intentional: not all work has deadlines.

---

## Domain Model Visualization

### Simple Entity Diagram

```
                    ┌─────────────┐
                    │    Task     │
                    └──────┬──────┘
          ┌─────────────────┼─────────────────┐
          │                 │                 │
          ↓                 ↓                 ↓
    ┌──────────┐    ┌─────────────┐   ┌──────────────┐
    │ Identity │    │  Lifecycle  │   │   Priority   │
    ├──────────┤    ├─────────────┤   ├──────────────┤
    │ id (UUID)│    │status       │   │ priority (1-4)
    │          │    │created_at   │   │              │
    │          │    │updated_at   │   │ LOW=1        │
    │          │    │completed_at │   │ MEDIUM=2     │
    │          │    │due_date     │   │ HIGH=3       │
    │          │    │             │   │ URGENT=4     │
    └──────────┘    └─────────────┘   └──────────────┘

    ┌─────────────────┐          ┌──────────────────┐
    │  Content        │          │  Organization    │
    ├─────────────────┤          ├──────────────────┤
    │ title (req'd)   │          │ tags[] (optional)│
    │ description     │          │                  │
    └─────────────────┘          └──────────────────┘

Status Workflow:
  TODO → IN_PROGRESS → REVIEW → DONE
  (can progress or regress to earlier states)

Overdue Rule:
  overdue = (due_date < now) AND (status ≠ DONE)
```

### Domain Glossary

| Term | Definition | Example |
|------|-----------|---------|
| **Task** | A unit of work to be completed | "Fix login bug", "Write documentation" |
| **Status** | Current position in workflow | TODO, IN_PROGRESS, REVIEW, DONE |
| **Priority** | Urgency level (1-4 scale) | HIGH priority task needs attention soon |
| **Due Date** | Optional deadline for completion | "Must be done by 2024-12-25" |
| **Tags** | Flexible labels for categorization | "urgent", "project-x", "customer-facing" |
| **Overdue** | Task past due date AND not completed | Task was due yesterday and still TODO |
| **Completed** | Task marked as DONE with timestamp | Task finished at 2024-08-24 14:30 |

---

## Business Processes Mapped to Code

### Process 1: Creating a New Task
```
User intent: "I need to track: 'Review pull requests' due next Friday, high priority"
    ↓
system.create_task(
    title="Review pull requests",
    priority=TaskPriority.HIGH,  // 3
    due_date=datetime(2024, 8, 30)
)
    ↓
Task object created with:
- status = TaskStatus.TODO (default, not started)
- created_at = datetime.now() (timestamp proof)
- priority = HIGH (urgent)
- due_date = 2024-08-30
```

### Process 2: Tracking Progress
```
User intent: "I'm starting work on this"
    ↓
task.update(status=TaskStatus.IN_PROGRESS)
    ↓
- updated_at = datetime.now() (records when change happened)
- status changes to IN_PROGRESS (work has started)

Later: "This is ready for review"
    ↓
task.update(status=TaskStatus.REVIEW)
    ↓
- updated_at = datetime.now()
- status = REVIEW (waiting for approval)
```

### Process 3: Completing Work
```
User intent: "I'm done with this task"
    ↓
task.mark_as_done()
    ↓
- status = TaskStatus.DONE (no longer active)
- completed_at = datetime.now() (exactly when finished)
- updated_at = datetime.now() (also updated for consistency)
- is_overdue() will always return False (done = no longer urgent)
```

### Process 4: Finding Overdue Work
```
Analytics query: "Show me what's overdue so I can prioritize"
    ↓
overdue_tasks = [t for t in all_tasks if t.is_overdue()]
    ↓
Includes: Tasks with due_date < now AND status ≠ DONE
Excludes: Tasks without due dates, completed tasks, future deadlines
```

---

## Key Domain Insights

### Insight 1: Status vs. Priority Independence
**Status** and **Priority** answer different questions:
- Status: "What stage of work is this in?" (workflow position)
- Priority: "How important is this?" (relative urgency)

A task can have any combination:
- LOW priority, IN_PROGRESS (working on non-urgent task)
- HIGH priority, TODO (urgent work not yet started)

### Insight 2: Overdue is a Computed Property
`is_overdue()` is not a field; it's a **business rule** computed from other fields:
```python
overdue = (has_due_date) AND (past_deadline) AND (not_finished)
```

This means:
- Overdue status changes as time passes (no update needed)
- Completing a task immediately makes it "not overdue" (semantic change in meaning)

### Insight 3: Timestamps Enable Analytics
Three different timestamps serve different purposes:
```
created_at     → "How long have we been tracking this?" (task age)
updated_at     → "When was this last touched?" (activity freshness)
completed_at   → "When did we finish?" (completion measurement)
```

Enables business questions:
- "Avg time from creation to completion?" = avg(completed_at - created_at)
- "How many changes per task?" = count(updates per task)
- "What was completed this week?" = filter by completed_at

### Insight 4: Tags as Flexible Metadata
While Status and Priority are fixed enums, Tags are flexible strings:
- Allows unforeseen categorizations
- Supports cross-cutting concerns
- Doesn't require code changes to add new labels

---

## Domain Model Validation

### Self-Check: Can You Answer These?

1. **Scenario:** You have a task with due_date=today, status=TODO. Is it overdue?
   - Answer: YES (today's date is not less than now, so technically no... but in practical terms, same-day tasks are often considered overdue if not done)

2. **Scenario:** Can a DONE task be made TODO again?
   - Answer: Yes, technically `task.update(status=TaskStatus.TODO)` works, but semantically it's unusual (task was done, why reopen?)

3. **Scenario:** What happens if you create a task with priority=99?
   - Answer: ERROR - Enum only accepts 1, 2, 3, 4

4. **Scenario:** A task has no tags and no due_date. Is it invalid?
   - Answer: No - both are optional. Task is still valid with just title and description

5. **Scenario:** If a task is overdue and you mark it DONE, what changes?
   - Answer: 
     - status = DONE
     - completed_at = now
     - is_overdue() returns False (semantically, completed = no longer overdue)

---

## Next Steps for Applying Domain Knowledge

### Before implementing features:
1. ✅ Understand which fields/rules the feature affects
2. ✅ Identify which parts of the codebase need changes
3. ✅ Consider edge cases and business logic

### When implementing new features:
1. Consider: "Does this respect existing domain rules?"
2. Ask: "What new domain concepts might this introduce?"
3. Validate: "Do the timestamps and state transitions make sense?"

### When debugging:
1. Trace domain state changes (created → in_progress → done)
2. Verify business rules (overdue calculation, priority meaning)
3. Check timestamp consistency

---

# FINAL EXERCISE: Applying Your Understanding

## Business Rule Implementation: Abandoned Tasks

**Scenario:** Your product team has defined a new rule:
> "Tasks that are overdue for more than 7 days should be automatically marked as abandoned unless they are marked as high priority."

### Part A: Map to Domain Model

**Question 1:** What new domain concepts are introduced?
- **ABANDONED** — New task status (currently only TODO, IN_PROGRESS, REVIEW, DONE)
- **"overdue for more than 7 days"** — New time-based calculation
- **"unless HIGH priority"** — Exception based on priority

**Question 2:** What code changes are needed?

| File | Change | Reason |
|------|--------|--------|
| `models.py` | Add `ABANDONED = "abandoned"` to TaskStatus enum | Introduce new status value |
| `models.py` | Add `days_overdue()` method to Task | Calculate how many days past due |
| `task_manager.py` | Add `mark_abandoned_overdue_tasks()` method | Implement cleanup logic |
| `cli.py` | Add "cleanup" or "maintenance" command | Expose feature to user |

**Question 3:** What's the business logic?

```python
for task in all_tasks:
    if (task.is_overdue() and 
        task.days_overdue() > 7 and 
        task.priority != TaskPriority.HIGH):
        task.status = TaskStatus.ABANDONED
        # Don't mark as done (different semantic meaning)
```

### Part B: Identify Questions for Your Team

1. **"Should abandoned tasks be displayed in 'list' by default, or hidden?"**
   - Affects filtering logic

2. **"Should this mark run automatically (on startup) or on explicit command?"**
   - Affects system design

3. **"What about HIGH priority tasks that are 30 days overdue? Should humans review them?"**
   - Affects thresholds or alerts

4. **"Can abandoned tasks be reopened, or are they locked?"**
   - Affects validation logic

5. **"Should we notify users when tasks are auto-abandoned?"**
   - Affects user experience

---

## Summary: What You've Learned

✅ **Part 1:** Understood project structure, technology stack, and architecture  
✅ **Part 2:** Learned how to find feature implementation locations  
✅ **Part 3:** Grasped domain model concepts and business logic  
✅ **Applied:** Can map business requirements to code changes  

**Key Principle:** Clear communication of your understanding (even if incomplete) to AI results in better guidance. The exercise of articulating your current thinking is as valuable as the AI's response.

---


# AI Code Exercises - Complete Python Workbook
## All Required Exercises for "Using AI to Comprehend Existing Codebases"

**Repository:** Rev1994-bot/ai-code-exercises  
**Language:** Python  
**Date:** 2026-08-24

---

# TABLE OF CONTENTS

1. **Part 1:** Understanding Project Structure and Technology Stack ✅
2. **Part 2:** Finding Feature Implementation Locations ✅
3. **Part 3:** Understanding Domain Models and Business Concepts ✅
4. **Part 4:** Understanding Existing Code Functionality (Code Exploration)
5. **Part 5:** Deciphering Complex Functions and Algorithms (Algorithm Deconstruction)
6. **Part 6:** Using AI for Debugging (Error Diagnosis Challenge)
7. **Part 7:** Using AI for Performance Optimization (Performance Optimization Challenge)
8. **Part 8:** Using AI for Refactoring (Function Decomposition Challenge)
9. **Part 9:** Using AI for Testing (Using AI to Help with Testing)
10. **Final Assessment & Reflection**

---

# PART 4: Understanding Existing Code Functionality
## Code Exploration Challenge

### Overview
This section teaches you how to understand how specific features work in an existing codebase by using AI to translate code into understandable concepts.

### Exercise Focus: Understanding the Task Manager Features

**Scenario:** You need to understand how the task filtering feature works so you can add a new filter type.

---

## Prompt 1: Understand How a Specific Feature Works

### Using the Provided Prompt Template

**My Question to AI:**

---

I'm trying to understand how the **task filtering feature** works in our Python Task Manager codebase.

This feature seems to handle **retrieving and filtering tasks based on status, priority, or overdue status**.

Here are the key files that appear to be involved:
1. `task_manager.py` - Seems to be orchestrating the filtering logic
2. `storage.py` - Might be handling the actual data retrieval and filtering
3. `cli.py` - Contains references to filter parameters (--status, --priority, --overdue)

Here are code snippets from parts I'm struggling to understand:

**From task_manager.py (lines 27-39):**
```python
def list_tasks(self, status_filter=None, priority_filter=None, show_overdue=False):
    if show_overdue:
        return self.storage.get_overdue_tasks()
    
    if status_filter:
        status = TaskStatus(status_filter)
        return self.storage.get_tasks_by_status(status)
    
    if priority_filter:
        priority = TaskPriority(priority_filter)
        return self.storage.get_tasks_by_priority(priority)
    
    return self.storage.get_all_tasks()
```

**From storage.py (lines 93-100):**
```python
def get_tasks_by_status(self, status):
    return [task for task in self.tasks.values() if task.status == status]

def get_tasks_by_priority(self, priority):
    return [task for task in self.tasks.values() if task.priority == priority]

def get_overdue_tasks(self):
    return [task for task in self.tasks.values() if task.is_overdue()]
```

**From cli.py (lines 47-50):**
```python
list_parser = subparsers.add_parser("list", help="List all tasks")
list_parser.add_argument("-s", "--status", help="Filter by status", choices=["todo", "in_progress", "review", "done"])
list_parser.add_argument("-p", "--priority", help="Filter by priority", type=int, choices=[1, 2, 3, 4])
list_parser.add_argument("-o", "--overdue", help="Show only overdue tasks", action="store_true")
```

Could you:
1. Explain in simple terms what this component actually does
2. Walk me through the execution flow when a user runs: `python cli.py list --status todo`
3. Clarify how these files interact with each other
4. Identify any external dependencies or services this component relies on
5. Explain any complex code blocks
6. Provide a mental model I can use to think about this component

I'm particularly confused about **why the filtering logic uses if/elif statements instead of combining multiple filters** (e.g., why can't I filter by both status AND priority at the same time?), so extra detail there would help.

Finally, suggest 3 small code changes I could make to validate my understanding. Do not give me the change, and instead give me the requirement.

---

### Expected Understanding After AI Response

**What you should learn:**

1. **Overall Purpose:**
   - Retrieves tasks from storage based on user-specified filters
   - Currently supports mutually exclusive filters (status, priority, OR overdue)
   - Returns unfiltered list if no filters specified

2. **Execution Flow for `python cli.py list --status todo`:**
   ```
   cli.py: parse_args() → args.status = "todo", others = None
      ↓
   cli.py: call task_manager.list_tasks(status_filter="todo", priority_filter=None, show_overdue=False)
      ↓
   task_manager.py: list_tasks() checks if show_overdue=False (skip), then checks status_filter="todo"
      ↓
   task_manager.py: converts string "todo" → TaskStatus.TODO enum
      ↓
   task_manager.py: calls storage.get_tasks_by_status(TaskStatus.TODO)
      ↓
   storage.py: list comprehension filters self.tasks dictionary
      ↓
   storage.py: returns list of Task objects where task.status == TaskStatus.TODO
      ↓
   cli.py: iterates through returned tasks, formats each with format_task(), prints to user
   ```

3. **File Interactions:**
   - **CLI** → knows about command-line arguments, converts strings to types
   - **TaskManager** → receives filter parameters, converts to domain objects, delegates to storage
   - **Storage** → keeps data in memory, performs actual filtering via list comprehension

4. **External Dependencies:**
   - None - uses only Python standard library
   - Data comes from in-memory dictionary loaded from JSON file

5. **Complex Code Explanation:**
   - The `if/elif` structure is an early-return optimization
   - As soon as one filter matches, that method is called and the result returned
   - This prevents unnecessary comparisons
   - **Limitation:** Can't combine filters (design choice/limitation)

6. **Mental Model:**
   ```
   Filtering is a Pipeline:
   User Input → Parsed by CLI → Converted to Domain Types → 
   Stored Filter Params → TaskManager Routes to Right Filter → 
   Storage Performs Search → Results Returned → Formatted for Display
   ```

---

## Self-Assessment Questions

**Ask yourself these to validate your understanding:**

1. **Question:** "If I run `list --priority 3`, what TaskPriority enum value gets passed to storage?"
   - Answer: `TaskPriority(3)` which equals `TaskPriority.HIGH`

2. **Question:** "What happens if I specify both --status and --priority? Which takes precedence?"
   - Answer: Status takes precedence (checked first in the if/elif chain)

3. **Question:** "How would the code behave if I added a fourth filter like --category?"
   - Answer: It would follow the if/elif pattern, checked in order after existing filters

4. **Question:** "Why does get_overdue_tasks() call task.is_overdue() instead of checking dates directly in storage?"
   - Answer: Encapsulation - business logic (what makes a task overdue) lives in Task domain object

5. **Question:** "If there are 10,000 tasks in memory, is filtering by status efficient?"
   - Answer: O(n) time complexity - must check every task. Could optimize with indexes, but acceptable for small datasets

---

## Validation Exercises

### Exercise 1: Trace Code Execution
**Requirement:** Without running the code, trace execution for:
```bash
python cli.py list --overdue
```

**Expected trace:**
```
1. cli.py main() → parse_args()
2. args.command = "list", args.overdue = True, args.status = None, args.priority = None
3. task_manager.list_tasks(status_filter=None, priority_filter=None, show_overdue=True)
4. First check: if show_overdue (TRUE) → return storage.get_overdue_tasks()
5. storage.get_overdue_tasks() returns list where task.is_overdue() == True
6. cli.py receives list, formats each with format_task()
7. Print each task with separator
```

### Exercise 2: Identify Filter Limitations
**Requirement:** Explain why you can't use multiple filters at once, and what would need to change to support it.

**Answer:**
```python
# Current: if/elif structure means first match wins
if show_overdue:
    return ...  # Returns immediately, ignores other filters
if status_filter:
    return ...  # Never reached if show_overdue was true
    
# To support multiple filters:
# Could change to:
results = storage.get_all_tasks()
if show_overdue:
    results = [t for t in results if t.is_overdue()]
if status_filter:
    results = [t for t in results if t.status == status]
if priority_filter:
    results = [t for t in results if t.priority == priority]
return results
```

### Exercise 3: Optimize the Filter
**Requirement:** The current implementation loads ALL tasks then filters. Suggest how you could optimize storage to make filtering faster for large datasets (1M+ tasks).

**Answer concepts:**
- Add indexes by status/priority in storage (Dictionary of lists)
- Use caching for frequently accessed filters
- Lazy-load tasks from JSON instead of all at startup
- Database query optimization if moved to SQL

---

## Key Insights

### Insight 1: Responsibility Distribution
- **CLI:** Knows about command-line arguments and user input
- **TaskManager:** Knows about domain types and business logic
- **Storage:** Knows how to access and filter data

This clean separation makes each layer testable and replaceable.

### Insight 2: Enum Conversion Pattern
```python
# String from user → Enum from domain
status_filter = "todo"  # From CLI
status = TaskStatus(status_filter)  # Converted
# If invalid string, raises ValueError (automatic validation)
```

This ensures only valid values are accepted (type safety).

### Insight 3: Filter Precedence
The if/elif chain means filters have implicit priority:
1. Overdue (highest)
2. Status
3. Priority
4. Return all (lowest)

This is a design choice - could be changed but currently limits to one filter.

---

# PART 5: Deciphering Complex Functions and Algorithms
## Algorithm Deconstruction Challenge

### Overview
This section teaches you to understand complex code through step-by-step analysis.

### Exercise Focus: Understanding Task Statistics Calculation

**Scenario:** You need to add a new statistic "tasks completed this month" but you first need to understand how the existing statistics work.

---

## Prompt 1: Understand an Algorithm Through Step-by-Step Analysis

### Using the Provided Prompt Template

**My Question to AI:**

---

I'm trying to understand this algorithm/function in our codebase:

```python
# From task_manager.py (lines 87-117)
def get_statistics(self):
    tasks = self.storage.get_all_tasks()
    total = len(tasks)

    # Count by status
    status_counts = {status.value: 0 for status in TaskStatus}
    for task in tasks:
        status_counts[task.status.value] += 1

    # Count by priority
    priority_counts = {priority.name: 0 for priority in TaskPriority}
    for task in tasks:
        priority_counts[task.priority.name] += 1

    # Count overdue
    overdue_count = len([task for task in tasks if task.is_overdue()])

    # Count completed in last 7 days
    seven_days_ago = datetime.now() - timedelta(days=7)
    completed_recently = len([
        task for task in tasks
        if task.completed_at and task.completed_at >= seven_days_ago
    ])

    return {
        "total": total,
        "by_status": status_counts,
        "by_priority": priority_counts,
        "overdue": overdue_count,
        "completed_last_week": completed_recently
    }
```

Based on my current understanding:
1. I think this function is trying to **generate a summary/dashboard of task statistics**
2. The inputs seem to be **all tasks from storage** and it returns **a dictionary with various counts**
3. I'm particularly confused about:
   - Why `status_counts` uses `.value` but `priority_counts` uses `.name`
   - How the list comprehension with `task.completed_at` works (specifically the `and` condition)
   - Why we calculate "seven_days_ago" and how that comparison works with datetime

Could you help me understand this by:
1. Breaking down the algorithm into key sections with their purposes
2. Walking through a simple example execution with concrete values
3. Explaining the core technique/pattern being used here
4. Highlighting any non-obvious optimizations or tricks

After your explanation, could you ask me 2-3 targeted questions that would test my understanding of this algorithm's:
- Underlying principles
- Edge cases
- Performance characteristics

This will help me make sure I've really grasped how it works.

---

### Expected Understanding

**Algorithm Breakdown:**

**Section 1: Gather Data**
```python
tasks = self.storage.get_all_tasks()  # Load all tasks
total = len(tasks)  # Simple count
```
Purpose: Get data source and total count

**Section 2: Count by Status**
```python
status_counts = {status.value: 0 for status in TaskStatus}
# Creates: {"todo": 0, "in_progress": 0, "review": 0, "done": 0}
for task in tasks:
    status_counts[task.status.value] += 1
# Loop counts tasks in each status
```
Purpose: Distribute all tasks across status categories

**Section 3: Count by Priority**
```python
priority_counts = {priority.name: 0 for priority in TaskPriority}
# Creates: {"LOW": 0, "MEDIUM": 0, "HIGH": 0, "URGENT": 0}
for task in tasks:
    priority_counts[task.priority.name] += 1
```
Purpose: Distribute all tasks across priority levels

**Section 4: Count Overdue**
```python
overdue_count = len([task for task in tasks if task.is_overdue()])
```
Purpose: Single count of problematic tasks (past deadline)

**Section 5: Count Recent Completions**
```python
seven_days_ago = datetime.now() - timedelta(days=7)
completed_recently = len([
    task for task in tasks
    if task.completed_at and task.completed_at >= seven_days_ago
])
```
Purpose: Measure recent productivity

**Section 6: Return Results**
```python
return {...}  # Dictionary with all calculated stats
```

---

### Example Walkthrough

**Given sample data:**
```python
tasks = [
    Task("Task1", priority=HIGH, status=TODO, created=Aug 15, due=Aug 22, completed_at=None),
    Task("Task2", priority=MEDIUM, status=IN_PROGRESS, created=Aug 10, due=Aug 25, completed_at=None),
    Task("Task3", priority=LOW, status=DONE, created=Aug 1, due=Aug 20, completed_at=Aug 24 10:00),
]
```

**Execution:**
```
total = 3

status_counts = {"todo": 1, "in_progress": 1, "review": 0, "done": 1}
priority_counts = {"LOW": 1, "MEDIUM": 1, "HIGH": 1, "URGENT": 0}

overdue_count:
  - Task1: status=TODO, due_date=Aug22, now=Aug24 → is_overdue()=True
  - Task2: status=IN_PROGRESS, due_date=Aug25, now=Aug24 → is_overdue()=False
  - Task3: status=DONE → is_overdue()=False (done = not overdue)
  - Result: overdue_count = 1

completed_recently:
  seven_days_ago = Aug 24 - 7 days = Aug 17
  - Task1: completed_at=None → skip
  - Task2: completed_at=None → skip
  - Task3: completed_at=Aug24 >= Aug17 → YES (within 7 days)
  - Result: completed_recently = 1

Return: {
    "total": 3,
    "by_status": {"todo": 1, "in_progress": 1, "review": 0, "done": 1},
    "by_priority": {"LOW": 1, "MEDIUM": 1, "HIGH": 1, "URGENT": 0},
    "overdue": 1,
    "completed_last_week": 1
}
```

---

### Core Patterns Identified

**Pattern 1: Dictionary Comprehension for Initialization**
```python
status_counts = {status.value: 0 for status in TaskStatus}
```
Creates a dictionary with all possible status values initialized to 0. Ensures all statuses appear in output, even if zero count.

**Pattern 2: Loop Counter**
```python
for task in tasks:
    status_counts[task.status.value] += 1
```
Traditional counting pattern - iterate and increment. Simple and readable.

**Pattern 3: List Comprehension Filter**
```python
[task for task in tasks if task.is_overdue()]
```
Filters tasks matching condition, converts to count with `len()`. Concise one-liner.

**Pattern 4: Datetime Arithmetic**
```python
seven_days_ago = datetime.now() - timedelta(days=7)
if task.completed_at >= seven_days_ago:
```
Subtracts 7 days from now, compares with task timestamp. Enables time-window queries.

**Pattern 5: Multiple Conditions in List Comprehension**
```python
[task for task in tasks if task.completed_at and task.completed_at >= seven_days_ago]
```
The `and` operator: checks completed_at exists (not None) AND is recent. Guards against comparing None with datetime.

---

### Testing Questions

**Question 1: Edge Case - No Completed Tasks**
**Q:** "If no tasks have been completed (all completed_at are None), what would `completed_recently` equal?"

**Expected Answer:** 0, because:
- List comprehension: `if task.completed_at and ...`
- First part `task.completed_at` evaluates to False (None is falsy)
- `and` short-circuits, second condition never checked
- No tasks pass filter
- `len([])` = 0

**Why it matters:** The `and` pattern safely guards against trying to compare None with datetime (would raise TypeError)

---

**Question 2: Performance Characteristic**
**Q:** "If you had 100,000 tasks, how many times does the code loop through all tasks in the worst case?"

**Expected Answer:** 5 times:
1. `len(tasks)` - iterate once
2. Status count loop - iterate once
3. Priority count loop - iterate once
4. Overdue list comprehension - iterate once
5. Recently completed - iterate once

Total: O(5n) = O(n) complexity

**Why it matters:** Acceptable for real-time stats, but might need optimization if tasks >> 1M

---

**Question 3: Value vs Name**
**Q:** "Why does status_counts use `.value` (result: "todo") but priority_counts use `.name` (result: "HIGH")?"

**Expected Answer:** Design choice in the domain model:
```python
class TaskStatus(Enum):
    TODO = "todo"  # .value = "todo" (string)
    
class TaskPriority(Enum):
    HIGH = 3  # .name = "HIGH" (string), .value = 3 (int)
```

For display, string keys are more readable. Status already has string values, priority shows level names.

**Why it matters:** Understanding enum design choices helps predict similar patterns in the codebase

---

## Validation Exercise: Add "Completed This Month" Stat

**Requirement:** Without looking at existing code, write the logic to count tasks completed this month.

**Expected solution:**
```python
# Add to get_statistics method:
import calendar
from datetime import date

# Get first day of current month
today = date.today()
month_start = date(today.year, today.month, 1)

# Count tasks completed since month start
completed_this_month = len([
    task for task in tasks
    if task.completed_at and task.completed_at.date() >= month_start
])

# Add to return dict:
return {
    ...
    "completed_this_month": completed_this_month
}
```

**Test your understanding:**
- Does your logic handle edge cases (None completed_at)?
- Does datetime comparison work correctly?
- What if task was completed last month but updated this month?

---


# Part 1: Project Structure Understanding

## Initial Guesses
- I thought tasks would be stored in: **Files (JSON)** ✓
- I thought TaskPriority was: **A scale representing urgency levels (1-4)** ✓
- I expected CLI commands to be in: **A dedicated CLI file with argparse** ✓
- I expected the code to be organized as: **Layered architecture (presentation → business → data)** ✓

## Actual Architecture
```
Presentation Layer:  cli.py (argparse-based command-line interface)
    ↓
Business Logic:      task_manager.py (TaskManager orchestrator class)
    ↓
Data Layer:          storage.py (JSON persistence) + models.py (domain entities)
    ↓
Persistent Storage:  tasks.json file
```

## Technologies Found
- **Language:** Python 3.11+ with Python standard library only
- **No External Dependencies:** Pure Python (no pip packages required)
- **Persistence:** JSON file-based storage (`tasks.json`)
- **Type Safety:** Python Enums for TaskPriority and TaskStatus
- **Unique Identifiers:** UUID for each task
- **CLI Framework:** Python's built-in `argparse` module
- **Date/Time Handling:** Python `datetime` module with ISO format serialization

## Main Components Breakdown

### 1. **models.py** — Domain Model (3 Classes)
```python
TaskPriority (Enum)
  ├── LOW = 1
  ├── MEDIUM = 2
  ├── HIGH = 3
  └── URGENT = 4

TaskStatus (Enum)
  ├── TODO = "todo"
  ├── IN_PROGRESS = "in_progress"
  ├── REVIEW = "review"
  └── DONE = "done"

Task (Class)
  ├── Attributes: id, title, description, priority, status, due_date, 
  │               created_at, updated_at, completed_at, tags
  ├── Methods: update(), mark_as_done(), is_overdue()
  └── Default status: TODO (when created)
```

### 2. **storage.py** — Persistence Layer
- **TaskEncoder**: Converts Python Task objects to JSON (handles datetime serialization to ISO format)
- **TaskDecoder**: Converts JSON back to Python Task objects (handles datetime deserialization)
- **TaskStorage**: Main class managing CRUD operations
  - Loads tasks from file on initialization
  - Keeps tasks in-memory dictionary for fast lookups
  - Auto-saves to JSON after each modification
  - Methods: add_task(), get_task(), update_task(), delete_task(), get_all_tasks(), get_tasks_by_status(), get_tasks_by_priority(), get_overdue_tasks()

### 3. **task_manager.py** — Business Logic Orchestrator
- **TaskManager**: Coordinates between CLI and Storage layers
- Core Methods:
  - `create_task()` — Parse due date, validate, create Task object, save to storage
  - `list_tasks()` — Retrieve tasks with optional filtering (status, priority, overdue)
  - `update_task_status()` — Special handling for DONE status (sets completed_at timestamp)
  - `update_task_priority()` — Change priority level
  - `update_task_due_date()` — Update deadline
  - `add_tag_to_task()` / `remove_tag_from_task()` — Tag management
  - `delete_task()` — Remove task
  - `get_task_details()` — Fetch single task
  - `get_statistics()` — Calculate analytics (total, by status, by priority, overdue count, completed last week)

### 4. **cli.py** — Command-Line Interface
- **Entry Point:** `main()` function
- **Parser:** Uses `argparse` with subcommands
- **Format Function:** `format_task()` displays tasks with symbols
  - Status symbols: `[ ]` TODO, `[>]` IN_PROGRESS, `[?]` REVIEW, `[✓]` DONE
  - Priority symbols: `!` LOW, `!!` MEDIUM, `!!!` HIGH, `!!!!` URGENT

**Available Commands:**
| Command | Args | Purpose |
|---------|------|---------|
| `create` | title, -d description, -p priority, -u due_date, -t tags | Create new task |
| `list` | -s status, -p priority, -o overdue flag | List tasks with filtering |
| `status` | task_id, new_status | Change task status |
| `priority` | task_id, priority (1-4) | Change priority |
| `due` | task_id, due_date (YYYY-MM-DD) | Update due date |
| `tag` | task_id, tag_name | Add tag to task |
| `untag` | task_id, tag_name | Remove tag from task |
| `show` | task_id | Display task details |
| `delete` | task_id | Remove task |
| `stats` | (no args) | Show statistics |

## Data Flow Example: Creating a Task
```
User runs: python cli.py create "My Task" -p 3 -u 2024-12-31
    ↓
cli.py parses command → calls TaskManager.create_task()
    ↓
TaskManager converts priority (3 → TaskPriority.HIGH)
TaskManager parses date string → datetime object
    ↓
TaskManager creates Task object with all attributes
    ↓
TaskManager.storage.add_task(task) → saves to tasks.json
    ↓
CLI prints success message with task UUID
```

## Key Architectural Insights

1. **Separation of Concerns**
   - CLI is completely separate from business logic
   - Business logic doesn't know about argparse or CLI formatting
   - Storage is abstracted behind TaskStorage interface
   - Easy to swap storage backend without changing business logic

2. **Layered Design Benefits**
   - Each layer has a single responsibility
   - Can test each layer independently
   - New features can be added to any layer without affecting others
   - Future: Could add REST API, GUI, or different storage by reusing TaskManager

3. **No External Dependencies**
   - Pure Python standard library
   - Easier to maintain and deploy
   - No version conflicts or dependency hell

4. **Type Safety with Enums**
   - TaskPriority and TaskStatus prevent invalid values
   - Compiler/IDE can check for valid values at development time

5. **Smart Timestamp Handling**
   - `created_at` — Set when task created (never changes)
   - `updated_at` — Updated every time task modified
   - `completed_at` — Set only when status = DONE
   - Enables analytics (e.g., "completed in last 7 days")

6. **JSON Serialization Strategy**
   - Datetime objects serialized to ISO format strings (ISO 8601)
   - Enum values converted to their base values (int or string)
   - Custom encoder/decoder handles round-trip conversion cleanly

## Surprising Discoveries

1. **No Database** — Initially might expect a database, but JSON is sufficient for this use case
2. **In-Memory Dictionary** — Tasks loaded entirely into memory, not streamed from disk
3. **Auto-Save Pattern** — Every modification immediately writes to JSON (trade-off between consistency and performance)
4. **UUID for Task IDs** — Globally unique identifiers instead of sequential numbers
5. **Custom JSON Handling** — Non-trivial encoder/decoder for datetime and enum serialization

## Questions Resolved
- ✅ How are tasks persisted? → JSON file with custom encoder
- ✅ How do changes persist? → Auto-save after each operation
- ✅ How are statuses enforced? → Enum prevents invalid values
- ✅ What's the entry point? → `cli.py main()` function
- ✅ How do dates work? → ISO 8601 format string in JSON, datetime objects in Python

## Architecture Pattern Recognition
- **Design Pattern:** Layered Architecture (3-tier)
- **Storage Pattern:** Repository Pattern (TaskStorage)
- **Domain Pattern:** Domain Model (Task, TaskPriority, TaskStatus)
- **CLI Pattern:** Command Pattern (each subcommand is a command)
- **Serialization Pattern:** Active Record Pattern (models know how to serialize themselves)

## Next Steps for Other Exercises
- Part 2 will use this understanding to locate where new features should go
- Part 3 will dive deeper into domain model relationships
- Part 4 will implement a business rule using this architecture

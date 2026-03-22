# Todo List Application — Java

A console-based task manager built in Java. Users can add, delete, and display tasks with deadlines, all within a looping menu-driven interface.

---

## Project Structure

```
TodoList/
└── Codechef.java       ← single file, one public class, all methods static
```

This is a single-file project typical of competitive/educational Java exercises. Everything lives inside `public class Codechef` — no packages, no separate files.

### Data Storage

Two parallel `ArrayList`s hold all task data:

| ArrayList | Type | Holds |
|---|---|---|
| `taskNames` | `ArrayList<String>` | Task name entered by user |
| `taskDeadlines` | `ArrayList<Date>` | Parsed deadline as a `Date` object |

Items stay in sync by index — index `0` in `taskNames` always matches index `0` in `taskDeadlines`. Deleting a task removes from both at the same position.

---

## Application Flow

<img width="665" height="479" alt="image" src="https://github.com/user-attachments/assets/db482cf8-e892-467d-b4d9-3eee0b4ea1ec" />


```
Start
  └── Main menu loop (while true)
        ├── 1. Add Task
        │     ├── Accept task name
        │     ├── Accept deadline (DD-MM-YYYY)
        │     ├── validateDate() → parse or reject
        │     └── Save to both ArrayLists
        │
        ├── 2. Delete Task
        │     ├── displayTasks() → show numbered list
        │     ├── Accept task number
        │     └── deleteTask() → remove by index from both lists
        │
        ├── 3. Display Tasks
        │     └── displayTasks() → print each task with formatted deadline
        │
        └── 4. Exit
              └── Return "Exiting application. Goodbye!" → break loop → End
```

The loop is driven by `while(true)` in `main()`. The only exit is option 4, which returns a sentinel string that `main()` checks to `break`.

---

## Method Reference

### `main()`
Entry point. Initialises the two `ArrayList`s, the `Scanner`, and the menu loop. After each `userChoice()` call it checks the return value — if it equals the exit string, the loop breaks and the program ends.

### `userChoice(int choice, ArrayList<String> taskNames, ArrayList<Date> taskDeadlines, Scanner scanner)`
The central router. Receives the integer menu choice and delegates:

| Choice | Action |
|---|---|
| `1` | Reads name + deadline from console, calls `addTask()` |
| `2` | Calls `displayTasks()` first, reads task number, calls `deleteTask()` |
| `3` | Calls `displayTasks()` |
| `4` | Returns `"Exiting application. Goodbye!"` |
| other | Prints `"Invalid choice!"` |

Returns `null` for choices 1–3, returns the exit string for choice 4. `main()` uses this return value to decide whether to break.

### `addTask(ArrayList<String> taskNames, ArrayList<Date> taskDeadlines, String taskName, String deadline)`
Calls `validateDate()` on the deadline string. If it returns a valid `Date`, appends the task name and date to their respective lists. If `validateDate()` returns `null` (invalid format), nothing is added.

### `deleteTask(ArrayList<String> taskNames, ArrayList<Date> taskDeadlines, int taskNumber)`
Converts the 1-based task number to a 0-based index. Validates the index is within bounds, then removes the entry from both `taskNames` and `taskDeadlines` at that index.

### `displayTasks(ArrayList<String> taskNames, ArrayList<Date> taskDeadlines)`
Iterates over both lists together. Uses `SimpleDateFormat("dd-MM-yyyy")` to format each `Date` back to a human-readable string. Prints each task as:
```
1. Buy groceries - Deadline: 25-12-2025
```
If the list is empty, prints `"No tasks available."`.

### `validateDate(String deadline)`
Uses `SimpleDateFormat("dd-MM-yyyy")` with `setLenient(false)` to strictly parse the input string. Returns the parsed `Date` on success, or `null` and an error message on failure. `setLenient(false)` prevents Java from silently accepting invalid dates like `32-01-2025`.

---

## Method Call Graph

<img width="548" height="310" alt="image" src="https://github.com/user-attachments/assets/30ebefa3-1a74-421b-8414-ab5a7364fc80" />


```
main()
  └── userChoice()
        ├── addTask()
        │     └── validateDate()
        ├── deleteTask()   (also calls displayTasks first, via userChoice)
        ├── displayTasks()
        └── return "Goodbye!" ──→ break
```

`deleteTask()` is always preceded by a `displayTasks()` call (handled inside `userChoice`) so the user can see the numbered list before choosing what to remove.

---

## Key Code Patterns

### The exit sentinel pattern
```java
String value = userChoice(choice, taskNames, taskDeadlines, scanner);
if ("Exiting application. Goodbye!".equals(value)) {
    System.out.println(value);
    break;
}
```
`userChoice()` returns `null` for all normal operations and a specific string only for exit. `main()` uses `.equals()` (not `==`) to safely compare strings.

### Parallel ArrayList sync
```java
taskNames.remove(taskIndex);
taskDeadlines.remove(taskIndex);
```
Both removals use the same index in the same method, keeping the two lists in sync.

### Strict date validation
```java
SimpleDateFormat dateFormat = new SimpleDateFormat("dd-MM-yyyy");
dateFormat.setLenient(false);   // rejects "99-99-9999" type inputs
Date date = dateFormat.parse(deadline);
```
Without `setLenient(false)`, Java would attempt to interpret out-of-range values (e.g. day 40 rolls into the next month). Setting it to `false` throws a `ParseException` instead.

---

## Diagrams

### 1. Application flow diagram
Shows the `while(true)` loop, the four menu branches, and how the exit sentinel breaks the loop. The dashed lines represent the loop-back to the main menu after each operation completes.

### 2. Project structure diagram
Shows the single `Codechef.java` file as the outer container, with all six methods and the two `ArrayList`s laid out as inner regions by category — entry/routing methods in teal/purple, data methods in blue, destructive operations in coral, and validation in amber.

### 3. Method call graph
Shows the caller/callee relationships between all methods. `main()` calls `userChoice()`, which fans out to `addTask()`, `deleteTask()`, and `displayTasks()`. `addTask()` is the only method that delegates further — to `validateDate()`. The dashed line shows that `deleteTask()` is always preceded by a `displayTasks()` call within `userChoice()`.

---

## Possible Enhancements

- **Use a `Task` class** instead of two parallel ArrayLists — wraps `name` and `deadline` in one object, making the code more robust and readable.
- **Sort tasks by deadline** — `taskDeadlines` is a `Date`, so sorting with a `Comparator` is straightforward.
- **Mark tasks as complete** — add a third `ArrayList<Boolean>` or a `boolean` field on a `Task` class.
- **Persist tasks to a file** — use `FileWriter`/`BufferedReader` with `ObjectOutputStream` or a simple CSV format so tasks survive between runs.
- **Input error handling** — wrap `Integer.parseInt(scanner.nextLine())` in a `try/catch` to prevent a crash on non-numeric menu input.

---

## Running the Project

```bash
# Compile
javac Codechef.java

# Run
java Codechef
```

Requires Java 8 or later (uses `ArrayList`, `Date`, `SimpleDateFormat` — all from `java.util`).

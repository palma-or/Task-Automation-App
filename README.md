# Task-Automation-App
A rule-based desktop automation application built in Java with JavaFX, following a trigger-action paradigm and the SCRUM development methodology.

## 📚 Overview
AutomationApp is a desktop automation application developed in Java with a JavaFX graphical interface. The application allows users to define automation rules based on a trigger-action paradigm: each rule associates one or more triggering conditions with one or more actions to be executed automatically when those conditions are met.

Users can create, activate, deactivate, and delete rules through an intuitive GUI. Rules are persisted to disk so they survive application restarts, and a background evaluation engine continuously monitors active rules and fires the corresponding actions when their trigger conditions are satisfied.

## ✨ Features

### Action Types
The system supports a wide variety of automated tasks, each encapsulated in its own dedicated class:
- **DialogBoxAction** — Displays a pop-up information message to the user.
- **PlayFileAction** — Plays an audio file in a loop and provides a dedicated UI window with a "STOP" button to halt playback.
- **WriteOnFileAction** — Appends a specified string to a target text file.
- **CopyFileAction** — Copies a selected file to a target destination directory.
- **MoveFileAction** — Moves a selected file to a target destination directory.
- **DeleteFileAction** — Deletes a specified file from the filesystem.
- **ExecuteProgramAction** — Executes an external program, with or without custom command-line parameters.

### Trigger Types (Conditions)
The application evaluates specific conditions to determine when an action should be triggered:
- **TimeOfDayCondition** — Evaluates to true when the system clock matches a specified exact time (HH:mm).
- **DayOfWeekCondition** — Evaluates to true if the current day matches a specified day of the week (e.g., "Monday").
- **DayOfMonthCondition** — Evaluates to true if the current day matches a specified day of the month.
- **DayOfYearCondition** — Evaluates to true on a specific month and day.
- **FileExistenceCondition** — Evaluates to true if a specified file exists in a target folder. It also supports logical negation (firing when the file does *not* exist).
- **FileSizeCondition** — Evaluates to true when a specified file exceeds a defined size threshold in kilobytes.
- **ExitStatusCondition** — Executes a specified external program via a bash process and evaluates to true if the program returns a specific exit code.

### Additional Features
- **Rule Persistence** — Rules are saved to disk upon application exit and automatically reloaded when the application is restarted using a dedicated `RuleFileHandler`.
- **Execution Modes** — Rules can be configured to fire only once (`executeOnce`) or periodically after a specified `sleepingPeriod`.
- **Logical AND Combinations** — A single `Trigger` object holds an `ArrayList` of multiple `Condition` objects. The trigger fires only if *all* associated conditions evaluate to true simultaneously.
- **Sequential Actions** — A single `Rule` can hold an `ArrayList` of multiple `Action` objects, executing them in sequence when the trigger is met.

## 🏗️ Architecture & Design Patterns

The application is built on the **MVC (Model-View-Controller)** pattern and structured into well-defined packages. The design leverages several Gang-of-Four patterns to keep the codebase modular, extensible, and testable.

### Design Patterns Used

| Pattern | Usage |
| :--- | :--- |
| **Strategy / Command** | The execution logic is fully decoupled using the `Action` interface and the `Condition` interface. Every concrete action implements `executeAction()`, allowing the rule engine to execute tasks without knowing their underlying implementation details. |
| **Chain of Responsibility** | Used within the `FXMLController` to parse user input and instantiate the correct Actions and Conditions. Handlers (e.g., `AudioActionHandler`, `DialogBoxActionHandler`) are linked sequentially to process the UI strings into executable objects. |
| **Composite** | The `Trigger` class acts as a composite object containing a list of `Condition` objects. Its `checkTrigger()` method delegates evaluation to its children, returning true only if all conditions are met. |

### Concurrency and Background Engine

The background evaluation engine runs on a separate thread utilizing a JavaFX `Task<Void>`. This engine operates as follows:
- **Monitoring Thread:** An infinite loop checks all active rules every 5 seconds without blocking the main GUI thread.
- **Thread Safety:** When a rule's trigger condition evaluates to true, the `executeAction()` calls and UI updates (like refreshing the `TableView`) are safely dispatched back to the main thread using `Platform.runLater()`.

### Core Components

```text
src/
├── Action/                  # Action interfaces and concrete implementations
├── ActionHandlers/          # Chain of Responsibility handlers for actions
├── Condition/               # Condition interfaces, concrete implementations, and Trigger
├── ConditionHandlers/       # Chain of Responsibility handlers for conditions
├── Rule/                    # Rule and RulesSet models
└── ifttt/                   # Main application entry point and JavaFX FXMLController
```
## 📈 Agile Methodology & Task Tracking

The development lifecycle was strictly managed using the **SCRUM framework**. We utilized a Trello Kanban board to track our progress, assign Story Points, and manage our Product and Sprint Backlogs. 

As shown in our board, tasks flow through standard Agile states ("Da fare", "Sprint backlog", "In esecuzione", "Fatto") to ensure continuous delivery and clear visibility of the team's workload. We actively tracked feature implementation (such as OR logic in triggers and inactive rule modes) alongside technical debt management like refactoring.

<img width="1417" height="482" alt="Screenshot 2026-09-30 155858" src="https://github.com/user-attachments/assets/073ed069-22b0-4271-9134-c1f72871c4b0" />


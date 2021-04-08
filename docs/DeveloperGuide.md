---
layout: page
title: Developer Guide
---
## Table of Contents
1. [Preface](#1-preface)<br>
1. [Setting up, getting started](#2-setting-up-getting-started)<br>
1. [Design](#3-design)<br>
   3.1  [Architecture: High Level View](#31-architecture)<br>
   3.2  [UI Component](#32-ui-component)<br>
   3.3  [Logic Component](#33-logic-component)<br>
   3.4  [Model Component](#34-model-component)<br>
   3.5  [Storage Component](#35-storage-component)<br>
   3.6  [Common Classes](#36-common-classes)<br>
1. [Implementation](#4-implementation)<br>
   4.1 [SOChedule](#41-sochedule)<br>
       4.1.1 [Overview](#411-overview)<br>
       4.1.2 [Implementation of SOChedule-level Commands](#412-implementation)<br>
   4.2 [Task](#42-task)<br>
       4.1.1 [Overview](#421-overview)<br>
       4.1.2 [Implementation of Task-level Commands](#422-implementation)<br>
   4.3 [Event](#43-event)<br>
       4.1.1 [Overview](#431-overview)<br>
       4.1.2 [Implementation of Event-level Commands](#432-implementation)<br>
1. [Planned Features](#5-documentation-logging-testing-configuration-dev-ops)<br>
1. [Appendix](#appendix)<br>
   A1. [Product Scope](#a1-product-scope)<br>
   A2. [User Stories](#a2-user-stories)<br>
   A3. [Use Cases](#a3-use-cases)<br>
   A4. [Non-Functional Requirements](#a4-non-functional-requirements)<br>
   A5. [Glossary](#a5-glossary)<br>
   A6. [Instructions for Manual Testing](#a6-instructions-for-manual-testing)<br>
   A7. [Launch and Shutdown](#a7-launch-and-shutdown)<br>
   A8. [Saving Data](#a8-saving-data)<br>
   
--------------------------------------------------------------------------------------------------------------------
## 1. Preface
SOChedule is a one-stop solution for managing tasks and events, optimized for use via a Command Line Interface (CLI) while still having the benefits of a Graphical User Interface (GUI).  

The Developer Guide for Sochedule is designed to showcase the high level architecture systems used to design and implement Sochedule.

The link to the repository can be found [here](https://github.com/AY2021S2-CS2103-W16-1/tp).

--------------------------------------------------------------------------------------------------------------------

## 2. Setting up, getting started

Refer to the guide [_Setting up and getting started_](SettingUp.md).

--------------------------------------------------------------------------------------------------------------------

## 3. Design

### 3.1 Architecture

![Architecture Diagram](images/ArchitectureDiagram.png)

The ***Architecture Diagram*** given above explains the high-level design of the App. Given below is a quick overview of each component.

<div markdown="span" class="alert alert-primary">

:bulb: **Tip:** The `.puml` files used to create diagrams in this document can be found in the [diagrams](https://github.com/se-edu/addressbook-level3/tree/master/docs/diagrams/) folder. Refer to the [_PlantUML Tutorial_ at se-edu/guides](https://se-education.org/guides/tutorials/plantUml.html) to learn how to create and edit diagrams.
 
</div>

**`Main`** has two classes called [`Main`](https://github.com/se-edu/addressbook-level3/tree/master/src/main/java/seedu/address/Main.java) and [`MainApp`](https://github.com/se-edu/addressbook-level3/tree/master/src/main/java/seedu/address/MainApp.java). It is responsible for,
* At app launch: Initializes the components in the correct sequence, and connects them up with each other.
* At shut down: Shuts down the components and invokes cleanup methods where necessary.

[**`Commons`**](#common-classes) represents a collection of classes used by multiple other components.

The rest of the App consists of four components.

* [**`UI`**](#ui-component): The UI of the App.
* [**`Logic`**](#logic-component): The command executor.
* [**`Model`**](#model-component): Holds the data of the App in memory.
* [**`Storage`**](#storage-component): Reads data from, and writes data to, the hard disk.

Each of the four components,

* defines its *API* in an `interface` with the same name as the Component.
* exposes its functionality using a concrete `{Component Name}Manager` class (which implements the corresponding API `interface` mentioned in the previous point.

For example, the `Logic` component (see the class diagram given below) defines its API in the `Logic.java` interface and exposes its functionality using the `LogicManager.java` class which implements the `Logic` interface.

![Class Diagram of the Logic Component](images/LogicClassDiagram.png)

**How the architecture components interact with each other**

The *Sequence Diagram* below shows how the components interact with each other for the scenario where the user issues the command `delete_task 1`.

![Architecture Sequence Diagram](images/ArchitectureSequenceDiagram.png)

The sections below give more details of each component.

### 3.2 UI component

![Structure of the UI Component](images/UiClassDiagram.png)

**API** :
[`Ui.java`](https://github.com/se-edu/addressbook-level3/tree/master/src/main/java/seedu/address/ui/Ui.java)

The UI consists of a `MainWindow` that is made up of parts e.g.`CommandBox`, `ResultDisplay`, `PersonListPanel`, `StatusBarFooter` etc. All these, including the `MainWindow`, inherit from the abstract `UiPart` class.

The `UI` component uses JavaFx UI framework. The layout of these UI parts are defined in matching `.fxml` files that are in the `src/main/resources/view` folder. For example, the layout of the [`MainWindow`](https://github.com/se-edu/addressbook-level3/tree/master/src/main/java/seedu/address/ui/MainWindow.java) is specified in [`MainWindow.fxml`](https://github.com/se-edu/addressbook-level3/tree/master/src/main/resources/view/MainWindow.fxml)

The `UI` component,

* Executes user commands using the `Logic` component.
* Listens for changes to `Model` data so that the UI can be updated with the modified data.

### 3.3 Logic component

![Structure of the Logic Component](images/LogicClassDiagram.png)

**API** :
[`Logic.java`](https://github.com/se-edu/addressbook-level3/tree/master/src/main/java/seedu/address/logic/Logic.java)

1. `Logic` uses the `SocheduleParser` class to parse the user command.
1. This results in a `Command` object which is executed by the `LogicManager`.
1. The command execution can affect the `Model` (e.g. adding a task or an event).
1. The result of the command execution is encapsulated as a `CommandResult` object which is passed back to the `Ui`.
1. In addition, the `CommandResult` object can also instruct the `Ui` to perform certain actions, such as displaying help to the user.

Given below is the Sequence Diagram for interactions within the `Logic` component for the `execute("delete_task 1")` API call.

![Interactions Inside the Logic Component for the `delete_task 1` Command](images/DeleteTaskCommandSequenceDiagram.png)

<div markdown="span" class="alert alert-info">:information_source: **Note:** The lifeline for `DeleteTaskCommandParser`should end at the destroy marker (X) but due to a limitation of PlantUML, the lifeline reaches the end of diagram.
</div>

[Return to Table of Contents](#table-of-contents)  

### 3.4 Model component

![Structure of the Model Component](images/ModelClassDiagram.png)

**API** : [`Model.java`](https://github.com/AY2021S2-CS2103-W16-1/tp/blob/master/src/main/java/seedu/address/model/Model.java)

The `Model`,

* stores a `UserPref` object that represents the user’s preferences.
* stores the Sochedule data.
* exposes an unmodifiable `ObservableList<Task>` and an unmodifiable `ObservableList<Event>`that can be 'observed' e.g. the UI can be bound to this list so that the UI automatically updates when the data in the list change.
* does not depend on any of the other three components.


<div markdown="span" class="alert alert-info">:information_source: 
**Note:** An (arguably) more OOP model can store a `Tag` list and a `Category` list in the `Sochedule`, which `Task` and `Event` can refer.
This allows `Sochedule` to only require one `Tag` object per unique `Tag`, and one `Category` object per unique `Object`, 
instead of each `Task` and `Event` needing their own `Tag` and `Category` object.<br>

</div>

#### 3.4.1 Design considerations for Task-related Models
When implementing Task-related models, it is important to avoid adding duplicate tasks. 
For example, it is undesirable for users to add the same task, completing CS2103 Quiz before 2021-06-01, twice into the task list. 
Because these two tasks contain the same information and can potentially cause confusion for users 
when he completes one and finds another task with the same description still left uncompleted.

In our application, we require users to minimally provide the name, deadline and priority when creating a task.
To ensure duplicates are handled, our team went through several alternatives and here are our considerations.

* Alternative 1 (Chosen Implementation): `equals(Task task)` method should check if the name, priority, deadline, 
  tags (if any) and categories (if any) are equal.
  * Pros:  
    * Tasks with same name but different deadline, priority and/or any other fields are allowed.
  * Cons:
    * Harder to implement.
* Alternative 2 : `equals(Task task)` method should check for the equality of task name only.
    * Pros:
        * Easier to implement.
        * Ensure that the task names are always distinct.
    * Cons:
        * Less flexibility and may not meet some users' need because task with same name but other different fields are not allowed.

We chose Alternative 1 because it is more flexible and suitable for users' need. 
There can be multiple tasks with same name but other different fields, like deadlines. 
For example, a user may need to create two tasks with the same name '2103 quiz',
but one is due on this Monday and the other is due the next Monday. Both of these 2 tasks should be allowed in our task list.

#### 3.4.2 Design considerations for Event-Related Models
Similar to Task-related Models, we face the same challenge when choosing between checking for the equality of name only and 
checking for all fields entered by the user. We chose to check for all fields for the same reasons as mentioned above.

### 3.5 Storage component

![Structure of the Storage Component](images/StorageClassDiagram.png)

**API** : [`Storage.java`](https://github.com/AY2021S2-CS2103-W16-1/tp/blob/master/src/main/java/seedu/address/storage/Storage.java)

The `Storage` component,
* can save `UserPref` objects in json format and read it back.
* can save the Sochedule data in json format and read it back.

### 3.6 Common classes

Classes used by multiple components are in the `seedu.addressbook.commons` package.

[Return to Table of Contents](#table-of-contents)  

--------------------------------------------------------------------------------------------------------------------

## 4 Implementation

This section describes some noteworthy details on how [Sochedule](#41-sochedule), [Task](#42-task) and [Event](#43-event) are implemented.

### 4.1 Sochedule

#### 4.1.1 Overview

#### 4.1.2 Implementation

**Implementation of ClearCommand**  
The following is a detailed explanation on how ClearCommand is implemented.

**Step 1**: User executes `clear` command to clear task and event lists.
An `ClearCommandParser` object is created, and the `ClearCommandParser#parse(String args)` method is called.
A `ClearCommand` object is returned.

**Step 2**: On `ClearCommand#execute()`, `Model#setSochedule(new Sochedule())` is called.
This will replace Sochedule data with a new empty Sochedule.
For brevity, lower level implementation is omitted.

**Step 3**: On execution completion a `CommandResult` is created.
A success message will be appended with `CommandResult#MESSAGE_SUCCESS`.

The sequence diagram for `ClearCommand` can be found below.

![Sequence Diagram of Clear Command](images/ClearCommandSequenceDiagram.png)

[Return to Table of Contents](#table-of-contents)  

**Implementation of SummaryCommand**  
The following is a detailed explanation on how SummaryCommand is implemented.

**Step 1**: User executes `summary` command to get a summary of the current completion status of tasks and events.
An `SummaryCommandParser` object is created, and the `SummaryCommandParser#parse(String args)` method is called.
A `SummaryCommand` object is returned.

**Step 2**: On `SummaryCommand#execute()`, `Model#getNumCompletedTask()`,
`Model#getFilteredTaskList()`,
`Model#getNumOverdueTask()`,
`Model#getNumIncompleteTask()`,
`Model#getNumIncomingEvents()` are called.
This will get different statistics on task and event completion.
For brevity, lower level implementation is omitted.

**Step 3**: On execution completion a `CommandResult` is created.
A success message will be appended with `CommandResult#MESSAGE_SUCCESS`.

The sequence diagram for `SummaryCommand` can be found below.

![Sequence Diagram of Summary Command](images/SummaryCommandSequenceDiagram.png)

[Return to Table of Contents](#table-of-contents)  

**Implementation of FindScheduleCommand**  
The following is a detailed explanation on how SummaryCommand is implemented.

**Step 1**: User executes `find_schedule DATE` command to find the ongoing tasks and events before or on the given date.
An `FindScheduleCommandParser` object is created, and the `FindScheduleCommandParser#parse(String args)` method is called.
The method conducts parses the `DATE` and conducts validation checks to ensure that it complies with the specification.
Two predicates, `TaskFindSchedulePredicate(Date date)` and `EventFindSchedulePredicate(Date date)` are created based on the given date.
Then, a `FindScheduleCommand` object is created given the two predicates and returned.

**Step 2**: On `FindScheduleCommand#execute()`, 
`Model#updateFilteredTaskList(TaskFindSchedulePredicate taskPredicate)` 
and `Model#updateFilteredEventList(EventFindSchedulePredicate eventPredicate)` are called.
This will update the task list to only show the uncompleted tasks with deadline before or on the given date.
Similary, the event list will be updated to only show the events with start date before or on the given date and end date after or on the given date.
For brevity, lower level implementation of `Model#updateFilteredTaskList(TaskFindSchedulePredicate taskPredicate)` 
and `Model#updateFilteredEventList(EventFindSchedulePredicate eventPredicate)` are omitted.

**Step 3**: On execution completion a `CommandResult` is created.
A success message will be appended with `CommandResult#MESSAGE_FIND_SCHEDULE_SUCCESS`.

The sequence diagram for `FindScheduleCommand` can be found below.

![Sequence Diagram of FindSchedule Command](images/FindScheduleCommandSequenceDiagram.png)

<div markdown="span" class="alert alert-info">:information_source: 
**Note:** Due to the size constraint, the argument `taskPredicate` is not shown in the sequence diagram 
when calling the method `Model#updateFilteredTaskList(TaskFindSchedulePredicate taskPredicate)`.
Same for the method `Model#updateFilteredEventList(EventFindSchedulePredicate eventPredicate)`.
</div>

[Return to Table of Contents](#table-of-contents)  

### 4.2 Task

#### 4.2.1 Overview

#### 4.2.2 Implementation

**Implementation of AddTaskCommand**
The following is a detailed explanation on how AddTaskCommand is implemented.

**Step1**: User executes `add_task n/TASKNAME d/DEADLINE p/PRIORITY [c/CATEGORY]... [t/TAG]...` command to add the 
specific task with given arguments. An `AddTaskCommandParser` object is created, and the 
`AddTaskParser#parse(String args)` method is called. The method conducts parses the `args` and conducts validation
checks to ensure that it compiles with the specification. An `AddTaskCommand` object is returned.

**Step 2**: On `AddTaskCommand#execute()`, `Model#addTasks(Task taskToAdd)` is called.
This will add the task specified into the task list.
For brevity, lower level implementation of `Model#addTasks(Task taskToAdd)` is omitted.

**Step 3**: On execution completion a `CommandResult` is created.
A success message will be appended with `CommandResult#MESSAGE_ADD_TASK_SUCCESS`.
The UI will also update as the underlying task list has been modified.

The sequence diagram for `AddTaskCommand` can be found below.

![Sequence Diagram of AddTask Command](images/AddTaskCommandSequenceDiagram.png)

[Return to Table of Contents](#table-of-contents)  

**Implementation of DeleteTaskCommand**  
The following is a detailed explanation on how DeleteTaskCommand is implemented.

**Step 1**: User executes `delete_task Index` command to delete the task at the given index.
A `DeleteTaskParser` object is created, and the `DeleteTaskParser#parse(String args)` method is called.
The method conducts parses the `args` and conducts validation checks to ensure that it complies with the specification.
A `DeleteTaskCommand` object is returned.

**Step 2**: On `DeleteTaskCommand#execute()`, `Model#deleteTasks(Task taskToDelete)` is called.
This will delete the task at the specified index.
For brevity, lower level implementation of `Model#deleteTasks(Task taskToDelete)` is omitted.

**Step 3**: On execution completion a `CommandResult` is created.
A success message will be appended with `CommandResult#MESSAGE_DELETE_TASK_SUCCESS`.
The UI will also update as the underlying task list has been modified.

The sequence diagram for `DeleteTaskCommand` can be found below.

![Sequence Diagram of DeleteTask Command](images/DeleteTaskCommandSequenceDiagram.png)

[Return to Table of Contents](#table-of-contents)  

**Implementation of UndoneTaskCommand**  
The following is a detailed explanation on how UndoneTaskCommand is implemented.

**Step 1**: User executes `undone_task Index` command to mark the completed task at the given index as uncompleted. 
Let us call this task the target task.
A `UndoneTaskCommandParser` object is created, and the `UndoneTaskCommandParser#parse(String args)` method is called.
The method conducts parses the `args` and conducts validation checks to ensure that the given index is a valid unsigned non-zero integer.
A `UndoneTaskCommand` object is returned.

**Step 2**: On `UndoneTaskCommand#execute()`, the index is further checked to ensure it is not out of range (i.e. larger than the size of task list)
and the target task is indeed a completed task.
Afterwards, `UndoneTaskCommand#createUncompletedTask()` method is called.
This method copies the information of the target task and creates an uncompleted task with exactly the same information as the target task.
Finally, `Model#setTask(Task taskToUndone, Task uncompletedTask)` and `Model#updateFilteredTaskList(PREDICATE_SHOW_ALL_TASKS)` method are called.
These two methods updates the target task in the task list and refresh the UI to show the update.
For brevity, lower level implementation of `UndoneTaskCommand#createUncompletedTask()`, 
`Model#setTask(Task taskToUndone, Task uncompletedTask)`, `Model#updateFilteredTaskList(PREDICATE_SHOW_ALL_TASKS)` are omitted.

**Step 3**: On execution completion a `CommandResult` is created.
A success message will be appended with `CommandResult#MESSAGE_UNDONE_TASK_SUCCESS`.

The sequence diagram for `UndoneTaskCommand` can be found below.

![Sequence Diagram of UndoneTask Command](images/UndoneTaskCommandSequenceDiagram.png)

<div markdown="span" class="alert alert-info">:information_source: 
**Note:** Due to the size constraint, the argument `PREDICATE_SHOW_ALL_TASKS` is not shown in the sequence diagram 
when calling the method `Model#updateFilteredTaskList(PREDICATE_SHOW_ALL_TASKS)`.
</div>

**Implementation of EditTaskCommand**  
The following is a detailed explanation on how EditTaskCommand is implemented.

**Step 1**: User executes `Edit_task Index` command to Edit the task at the given index.
An `EditTaskCommandParser` object is created, and the `EditTaskCommandParser#parse(String args)` method is called.
The method conducts parses the `args` and conducts validation checks to ensure that it complies with the specification.
An `EditTaskDescriptor` object is created, and it contains all the field an Task needed. 
If the field is edited, then store the edited one; otherwise, store the original value.
An `EditTaskCommand` object (with the `EditTaskDescriptor` as a parameter) is returned.

**Step 2**: On `EditTaskCommand#execute()`, `Model#getFilteredTaskList()` and 
`Model#createEditedTask(Task taskToEdit, EditTaskDescriptor editTaskDescriptor)` are called.
These will create the edited Task. Then, `Model#setTask(Task taskToEdit, Task editedTask)` and 
`Model#updateFilteredTaskList()` are called. These will update the edited Task into the task list.

**Step 3**: On execution completion a `CommandResult` is created.
A success message `EditTaskCommand#MESSAGE_EDIT_TASK_SUCCESS` will be displayed.

The UI will also update as the underlying task list has been modified.

The sequence diagram for `EditTaskCommand` can be found below.

![Sequence Diagram of EditTask Command](images/EditTaskCommandSequenceDiagram.png)

[Return to Table of Contents](#table-of-contents)  

**Implementation of SortTaskCommand**  
The following is a detailed explanation on how SortTaskCommand is implemented in the Logic component.

**Step 1**: User executes `sort SORT_VAR` command to sort the tasks based on the `SORT_VAR` provided.
An `SortTaskCommandParser` object is created, and the `SortTaskCommandParser#parse(String args)` method is called. 
The method conducts parses the `SORT_VAR` and conducts validation checks to ensure that it complies with the specification.
A `SortTaskCommand` object is returned.

**Step 2**: On `SortTaskCommand#execute()`, `Model#sortTasks(String comparingVar)` is called.
This will cause the task list to sort itself, based on the provided `comparingVar`.
For brevity, lower level implementation of `Model#sortTasks(String comparingVar)` is omitted.

**Step 3**: On execution completion a `CommandResult` is created.
A success message will be appended with `CommandResult#MESSAGE_SORT_TASK_SUCCESS`.
The UI will also update as the underlying task list has been modified.

The sequence diagram for `sortTaskCommand` can be found below.

![Sequence Diagram of SortTask Command](images/SortTaskSequenceDiagram.png)

***Lower Level implementation***  
The following is a brief explanation , as shown in a sequence diagram, of how sorting is implemented inside the Model component.
![Sequence Diagram of SortTaskCommand in Model Component](images/SortTaskModelSequenceDiagram.png)

***Design Considerations for `SortTaskCommand`***
<table>
    <tr>
        <th> Alternative 1 (Chosen Implementation) </th>
        <th> Alternative 2 </th>
    </tr>
    <tr>
        <td> 
            <ul>
                <li>Persistent Sorting using a Comparator</li>
                <li> Pros:
                    <ul>
                        <li>UX considerations when users expect sorting to be persistent over multiple commands</li>
                    </ul>
                </li>
                <li> Cons:
                    <ul>
                        <li>Additional Testing of Comparator needed</li>
                        <li>Additional component added might add to complexity</li>
                    </ul>
                </li>
            </ul>
        </td>
        <td> 
            <ul>
                <li>Transient Sorting by sorting the UniqueTaskList directly</li>
                <li> Pros:
                    <ul>
                        <li>Straightforward implementation</li>
                        <li>Less testing required due to less components</li>
                    </ul>
                </li>
                <li> Cons:
                    <ul>
                        <li>UX might be compromised as order is not maintained over command executions</li>
                    </ul>
                </li>
            </ul>
        </td>
    </tr>
</table>

Our group decided to go with alternative 1 and implemented an additional sort comparator known as `TaskComparator`.
This is to ensure smoother UX, as well as better integration with other commands.

By implementing it in this way, our group is able to ensure that the sort order remains consistent over commands that could either:
* Alter the number of tasks shown at any one time (See <code>find_task</code>)</li>
* Change the order of task appearance independent of <code>sort_task</code> (See <code>pin_task</code>)</li>

This would enable SOChedule to better serve the needs of its user base. 

[Return to Table of Contents](#table-of-contents)  

**Implementation of PinTaskCommand/UnpinTaskCommand**  
The following is a detailed explanation on how PinTaskCommand is implemented.
UnpinTaskCommand is largely similar in implementation to PinTaskCommand and will be omitted for brevity.

**Step 1**: User executes `pin_task INDEX` command to pin the task at the given index.
An `PinTaskCommandParser` object is created, and the `PinTaskCommandParser#parse(String args)` method is called.
The method conducts parses the `args` and conducts validation checks to ensure that it complies with the specification.
A `PinTaskCommand` object is returned.

**Step 2**: On `PinTaskCommand#execute()`, `Model#pinTask(Task task)` is called.
This will pin the task at the specified index.
Subsequently, the underlying task list will be sorted by calling `Model#sortTasksDefault()`, with pinned tasks being first in priority, followed by the last sorted variable 
(if `sort_task` was not called before, task list will be sorted by name).
For brevity, lower level implementation of `Model#pinTask(Task task)` is omitted.

**Step 3**: On execution completion a `CommandResult` is created.
A success message will be appended with `CommandResult#MESSAGE_PIN_TASK_SUCCESS`.
The UI will also update as the underlying task list has been modified.

The sequence diagram for `PinTaskCommand` can be found below.
It is largely similar to `SortTaskCommand`, with a some minor differences:
* Instead of `SortTask`-related parsers and commands, `PinTask`-related parsers and commands are created and activated.
* Additional call to `Model#sortTaskDefault()` after `Model#pinTask(Task)`

![Sequence Diagram of PinTaskCommand](images/PinTaskSequenceDiagram.png)

The below activity diagram summarises what happens when `pin_task` is called.
It can also be similarly extrapolated to apply to `unpin_task`.

![Activity Diagram of PinTaskCommand](images/PinTaskActivityDiagram.png)

[Return to Table of Contents](#table-of-contents)  

**Implementation of ClearCompletedTaskCommand**  
The following is a detailed explanation on how ClearCompletedTaskCommand is implemented.

**Step 1**: User executes `clear_completed_task` command to clear completed tasks in task list.
A `ClearCompletedTaskCommand` object is created and returned.

**Step 2**: On `ClearCompletedTaskCommand#execute()`, `Model#clearCompletedTasks()` is called.
This will delete all ticked tasks (all completed tasks).
For brevity, lower level implementation of `Model#clearCompletedTasks()` is omitted.

**Step 3**: On execution completion a `CommandResult` is created.
A success message `ClearCompletedTaskCommand#MESSAGE_CLEAR_COMPLETED_TASK_SUCCESS` will be displayed.

The sequence diagram for `ClearCompletedTaskCommand` can be found below.

![Sequence Diagram of Clear Command](images/ClearCompletedTaskCommandSequenceDiagram.png)

[Return to Table of Contents](#table-of-contents)  

**Implementation of ClearExpiredTaskCommand**  
The following is a detailed explanation on how ClearExpiredTaskCommand is implemented.

**Step 1**: User executes `clear_completed_task` command to clear completed tasks in task list.
A `ClearExpiredTaskCommand` object is created and returned.

**Step 2**: On `ClearExpiredTaskCommand#execute()`, `Model#clearExpiredTasks()` is called.
This will delete all expired tasks whose deadline have already past.
For brevity, lower level implementation of `Model#clearExpiredTasks()` is omitted.

**Step 3**: On execution completion a `CommandResult` is created.
A success message `ClearExpiredTaskCommand#MESSAGE_CLEAR_EXPIRED_TASK_SUCCESS` will be displayed.

The sequence diagram for `ClearExpiredTaskCommand` can be found below.

![Sequence Diagram of Clear Command](images/ClearExpiredTaskCommandSequenceDiagram.png)

[Return to Table of Contents](#table-of-contents)  

### 4.3 Event

#### 4.3.1 Overview

#### 4.3.2 Implementation

**Implementation of AddEventCommand**
The following is a detailed explanation on how AddEventCommand is implemented.

**Step1**: User executes `add_event n/TASKNAME sd/STARTDATE st/STARTTIME ed/ENDDATE et/ENDTIME [c/CATEGORY]... [t/TAG]...` 
command to add the specific event with given arguments. An `AddEventCommandParser` object is created, and the 
`AddEventParser#parse(String args)` method is called. The method conducts parses the `args` and conducts validation
checks to ensure that it compiles with the specification. An `AddEventCommand` object is returned.

**Step 2**: On `AddEventCommand#execute()`, `Model#addEvents(Event eventToAdd)` is called.
This will add the event specified into the event list.
For brevity, lower level implementation of `Model#addEvents(Event eventToAdd)` is omitted.

**Step 3**: On execution completion a `CommandResult` is created.
A success message `AddEventCommand#MESSAGE_ADD_EVENT_SUCCES` will be displayed.
The UI will also update as the underlying event list has been modified.

The sequence diagram for `AddEventCommand` can be found below.

![Sequence Diagram of AddEvent Command](images/AddEventCommandSequenceDiagram.png)

[Return to Table of Contents](#table-of-contents)  

**Implementation of DeleteEventCommand**  
The following is a detailed explanation on how DeleteEventCommand is implemented.

**Step 1**: User executes `delete_event Index` command to delete the event at the given index.
A `DeleteEventParser` object is created, and the `DeleteEventParser#parse(String args)` method is called.
The method conducts parses the `args` and conducts validation checks to ensure that it complies with the specification.
A `DeleteEventCommand` object is returned.

**Step 2**: On `DeleteEventCommand#execute()`, `Model#deleteEvents(Event eventToDelete)` is called.
This will delete the event at the specified index.
For brevity, lower level implementation of `Model#deleteEvents(Event eventToDelete)` is omitted.

**Step 3**: On execution completion a `CommandResult` is created.
A success message `DeleteEventCommand#MESSAGE_DELETE_EVENT_SUCCESS` will be displayed.
The UI will also update as the underlying event list has been modified.

The sequence diagram for `DeleteEventCommand` can be found below.

![Sequence Diagram of DeleteEvent Command](images/DeleteEventCommandSequenceDiagram.png)

[Return to Table of Contents](#table-of-contents)  

**Implementation of EditEventCommand**  
The following is a detailed explanation on how EditEventCommand is implemented.

**Step 1**: User executes `Edit_event Index` command to Edit the event at the given index.
An `EditEventParser` object is created, and the `EditEventParser#parse(String args)` method is called.
The method conducts parses the `args` and conducts validation checks to ensure that it complies with the specification.
An `EditEventDescriptor` object is created, and it contains all the field an Event needed. 
If the field is edited, then store the edited one; otherwise, store the original value.
An `EditEventCommand` object (with the `EditEventDescriptor` as a parameter) is returned.

**Step 2**: On `EditEventCommand#execute()`, `Model#getFilteredEventList()` and 
`Model#createEditedEvent(Event eventToEdit, EditEventDescriptor editEventDescriptor)` are called.
These will create the edited Event. Then, `Model#setEvent(Event eventToEdit, Event editedEvent)` and 
`Model#updateFilteredEventList()` are called. These will update the edited Event into the event list.

**Step 3**: On execution completion a `CommandResult` is created.
A success message `EditEventCommand#MESSAGE_EDIT_TASK_SUCCESS` will be displayed.
The UI will also update as the underlying event list has been modified.

The sequence diagram for `EditEventCommand` can be found below.

![Sequence Diagram of EditEvent Command](images/EditEventCommandSequenceDiagram.png)

[Return to Table of Contents](#table-of-contents)  

**Implementation of FindFreeTimeCommand**  
The following is a detailed explanation on how FindFreeTaskCommand is implemented.

**Step 1**: User executes `free_time DATE` command to find free time slots in the given day.
An `FindFreeTimeCommandParser` object is created, and the `FindFreeTimeCommandParser#parse(String args)` method is called.
The method conducts parses the `args` and conducts validation checks to ensure that it complies with the specification.
A `FindFreeTimeCommand` object is returned.

**Step 2**: On `FindFreeTimeCommand#execute()`, `Model#getFreeTimeSlots(Date date)` is called.
This will get free time slots and store in a string arraylist.
For brevity, lower level implementation of `Model#getFreeTimeSlots(Date date)` is omitted.

**Step 3**: On execution completion a `CommandResult` is created.
A success message will be appended with `CommandResult#MESSAGE_FIND_FREE_TIME_SUCCESS`.

The sequence diagram for `FindFreeTimeCommand` can be found below.

![Sequence Diagram of FindFreeTimeCommand](images/FindFreeTimeCommandSequenceDiagram.png)

The below activity diagram summarises what happens when `free_time` is called.

![Activity Diagram of FindFreeTimeCommand](images/FindFreeTimeActivityDiagram.png)

***Design Considerations for `FindFreeTimeCommand`***
<table>
    <tr>
        <th> Alternative 1 (Chosen Implementation) </th>
        <th> Alternative 2 </th>
    </tr>
    <tr>
        <td> 
            <ul>
                <li>Several helper functions were implemented in UniqueEventList class</li>
                <li> Pros:
                    <ul>
                        <li>Each function hanles a small part of logic</li>
                        <li>Easier to detect bugs</li>
                        <li>Better readability of code</li>
                    </ul>
                </li>
                <li> Cons:
                    <ul>
                        <li>Longer lines of code</li>
                        <li>Affects other classes</li>
                    </ul>
                </li>
            </ul>
        </td>
        <td> 
            <ul>
                <li>Write methods in FindFreeTimeCommand class directly</li>
                <li> Pros:
                    <ul>
                        <li>Straightforward implementation</li>
                        <li>Less testing required due to less components</li>
                    </ul>
                </li>
                <li> Cons:
                    <ul>
                        <li>Strong dependency on Event class and UniqueEventList class</li>
                    </ul>
                </li>
            </ul>
        </td>
    </tr>
</table>
<div markdown="block">

We choose alternative 1 because it presents code in a clearer way. Breaking up long methods into shorter methods will improve
readability of the code. Implementing codes under UniqueEventList also reduces dependency.

[Return to Table of Contents](#table-of-contents)  

**Implementation of ClearExpiredEventCommand**  
The following is a detailed explanation on how ClearExpiredEventCommand is implemented.

**Step 1**: User executes `clear_completed_event` command to clear completed events in event list.
A `ClearExpiredEventCommand` object is created and returned.

**Step 2**: On `ClearExpiredEventCommand#execute()`, `Model#clearExpiredEvents()` is called.
This will delete all expired events whose end date time have already past.
For brevity, lower level implementation of `Model#clearExpiredEvents()` is omitted.

**Step 3**: On execution completion a `CommandResult` is created.
A success message `ClearExpiredEventCommand#MESSAGE_CLEAR_COMPLETED_TASK_SUCCESS` will be displayed.

The sequence diagram for `ClearExpiredEventCommand` can be found below.

![Sequence Diagram of Clear Command](images/ClearExpiredEventCommandSequenceDiagram.png)


[Return to Table of Contents](#table-of-contents)  

--------------------------------------------------------------------------------------------------------------------

## 5. Documentation, logging, testing, configuration, dev-ops

* [Documentation guide](Documentation.md)
* [Testing guide](Testing.md)
* [Logging guide](Logging.md)
* [Configuration guide](Configuration.md)
* [DevOps guide](DevOps.md)

--------------------------------------------------------------------------------------------------------------------

## Appendix

### A1. Product scope

**Target user profile**:

* NUS SOC Student
* Prefer using CLI rather than GUI
* Struggle to manage their schedule
* Have a lot of commitments (6 modules, TA and RA roles while balancing a part time internship)
* Can type fast, hate mouse

**Value proposition**:

* Provide a simple platform for target users to manage their schedule
* CLI commands to manage meetings or schedule with some contacts
* CLI commands to add tasks and reminders
* GUI to display the schedule of user

### A2. User stories

Priorities: High (must have) - `* * *`, Medium (nice to have) - `* *`, Low (unlikely to have) - `*`

#### Task Related

| Priority | As a …​                            | I want to …​                                                        | So that …​                                                                |
| -------- | ------------------------------------- | ---------------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| `* * *`  | SOC Student                           | Add a task to my schedule                                              | I can track my task better                                                   |
| `* * *`  | SOC Student                           | Delete a task from my schedule from my schedule                        | I can have flexibility in my schedule                                        |
| `* * *`  | SOC Student                           | Edit tasks in my schedule                                              | I can have flexibility in my schedule                                        |
| `* * *`  | SOC Student                           | View tasks in my schedule                                              | I can have a better sense of what will happen in the following days or weeks |
| `* *`    | SOC Student                           | Mark a task complete in my schedule                                    | I can track which tasks I have completed                                     |
| `* *`    | SOC Student                           | Undo a completed task in my schedule                                   | I undo the marking if it is done by mistake                                  |
| `* *`    | SOC Student                           | Sort my tasks in my schedule in various orderings                      | I can prioritise my time better                                              |
| `* *`    | SOC Student                           | Pin certain tasks in my schedule                                       | I can prioritise my time better                                              |
| `* *`    | SOC Student                           | Unpin certain tasks in my schedule                                     | I can prioritise my time better                                              |
| `*`      | SOC Student                           | View tasks with the deadline on current date                           | I can have a better sense of what tasks are due today                        |
| `*`      | SOC Student                           | Search tasks by certain keywords                                       | I can view the details of the task that I want to find                       |
| `*`      | SOC Student                           | Clear completed tasks                                                  | I can make my schedule cleaner                                               |
| `*`      | SOC Student                           | Clear expired tasks                                                    | I can make my schedule cleaner                                               |
| `*`      | SOC Student                           | Allocate a priority score to a task                                    | I can prioritise my time better                                              |
| `*`      | SOC Student                           | Allocate a category to a task                                          | I can know what category my tasks belong to                                  |

#### Event Related

| Priority | As a …​                            | I want to …​                                                        | So that …​                                                                |
| -------- | ------------------------------------- | ---------------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| `* * *`  | SOC Student                           | Add an event to my schedule                                            | I can track my events better                                                 |
| `* * *`  | SOC Student                           | Delete an event from my schedule from my schedule                      | I can have flexibility in my schedule                                        |
| `* * *`  | SOC Student                           | Edit events in my schedule                                             | I can have flexibility in my schedule                                        |
| `* * *`  | SOC Student                           | View events in my schedule                                             | I can have a better sense of what will happen in the following days or weeks |
| `*`      | SOC Student                           | View events that are happening today                                   | I can have a better sense of what events are happening today                 |
| `*`      | SOC Student                           | Search events by certain keywords                                      | I can view the details of the event that I want to find                      |
| `*`      | SOC Student                           | Clear expired events                                                   | I can make my schedule cleaner                                               |
| `*`      | SOC Student                           | Allocate a priority score to an event                                  | I can prioritise my time better                                              |
| `*`      | SOC Student                           | Allocate a category to an event                                        | I can know what category my events belong to                                 |

#### Both Task and Event Related

| Priority | As a …​                            | I want to …​                                                        | So that …​                                                                |
| -------- | ------------------------------------- | ---------------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| `* *`    | SOC Student                           | Find tasks and events before or on a given date                        | I can keep track of my tasks and events better                               |
| `* *`    | SOC Student                           | Find free time slots on a specific date                                | I can allocate my time better                                                |
| `* *`    | SOC Student                           | Have a summary of task completion status and events in next 7 days     | I can keep track of my progress and plan ahead for my schedule               |
| `*`      | SOC Student                           | Clear the entire schedule                                              | I can start adding tasks and events from fresh                               |

*{More to be added}*

### A3. Use cases

(For all use cases below, the **System** is the `SOChedule` and the **Actor** is the `User`, unless specified otherwise)

**Use case: UC01 - Adding a task**

**MSS**

1. User wishes to add a new task.
2. User enters the required parameters.
3. SOChedule displays a success message for adding the task.
<br><br>
Use case ends.

**Extensions**

* 2a. Some required information about the event is missing in the command.

    * 1a1. SOChedule displays an error message suggesting that information provided when creating
      the event is incomplete.
      Use case ends.


* 2b. The date provided for the event is invalid

    * 1b1. SOChedule displays an error message suggesting that date provied for the event
      is invalid, or not following the `YYYY-MM-DD` format.
      Use case ends.

**Use case: UC02 - Deleting a task**

**MSS**

1. User requests to <u> list tasks (UC02)</u>.
2. SOChedule shows a list of tasks.
3. User chooses to delete a task.
4. User enters the index of the task to be deleted.
5. SOChedule displays a success message for deleting the task.
   <br><br>
   Use case ends.

**Extensions**

* 2a. The task list is empty.

  Use case ends.

  <br>

* 3a. The given index is invalid.

    * 3a1. SOChedule shows an error message.

      Use case resumes at step 2.

**Use case: UC03 - Editing a task**

`<pending>`

**Use case: UC04 - List tasks**

**MSS**

1. User wishes to add a new task.
2. User enters the corresponding command.
3. SOChedule displays all tasks.
<br><br>
Use case ends.


**Use case: UC05 - Marking tasks complete**
*TO BE EDITED*
**MSS**

1. User requests to <u> list tasks (UC02)</u>.
2. SOChedule shows a list of tasks.
3. User chooses to mark a task as completed.
4. User enters the index of the task to be marked.
5. SOChedule displays a success message for marking the task as completed.
   <br><br>
   Use case ends.

**Extensions**

* 2a. The task list is empty.

  Use case ends.


* 3a. The given index is invalid.

    * 3a1. SOChedule shows an error message indicating the invalidity of the index.

      Use case resumes at step 2.

**Use case: UC06 - Undoing a task completion**

`<pending>`

**Use case: UC07 - Getting tasks today**

`<pending>`

**Use case: UC08 - Sorting all tasks**

**MSS**

1. User requests to <u> list tasks (UC02)</u>.
2. SOChedule shows a list of tasks.
3. User chooses to sort task.
4. User enters the sort parameter.
5. SOChedule sorts the task list, and displays a success message.
   <br><br>
   Use case ends.

**Extensions**

* 2a. The task list is empty.

  Use case ends.


* 4a. The given sort argument is invalid.

    * 4a1. SOChedule shows an error message indicating the invalidity of the sort argument.

      Use case resumes at step 2.

**Use case: UC09 - Pinning a task**

**MSS**

1. User requests to <u> list tasks (UC02)</u>.
2. SOChedule shows a list of tasks.
3. User chooses to pin a task.
4. User enters the index of the task to be pinned.
5. SOChedule pins the task, <u> sorts the task list (UC08)</u>, and displays a success message for pinning the task.
   <br><br>
   Use case ends.

**Extensions**

* 2a. The task list is empty.

  Use case ends.


* 3a. The given index is invalid.

    * 3a1. SOChedule shows an error message indicating the invalidity of the index.

      Use case resumes at step 2.
        
* 3b. The task specified by index is already pinned

    * 3b1. SOChedule shows an error message indicating that task is already pinned.

      Use Case resumes at step 2.

**Use case: UC10 - Unpinning a task**

**MSS**

1. User requests to <u> list tasks (UC02)</u>.
2. SOChedule shows a list of tasks.
3. User chooses to unpin a task.
4. User enters the index of the task to be unpinned.
5. SOChedule unpins the task, <u> sorts the task list (UC08)</u>, and displays a success message for unpinning the task.
   <br><br>
   Use case ends.

**Extensions**

* 2a. The task list is empty.

  Use case ends.


* 3a. The given index is invalid (negative or out of range).

    * 3a1. SOChedule shows an error message indicating the invalidity of the index.

      Use case resumes at step 2.
    
* 3b. The task specified by index is not pinned

    * 3b1. SOChedule shows an error message indicating that task is not pinned.
    
      Use Case resumes at step 2.

**Use case: UC11 - Clearing all completed tasks**

`<pending>`

**Use case: UC12 - Clearing all expired tasks**

`<pending>`

**Use case: UC13 - Add an event**

**MSS**

1. User requests to add a new event.
1. SOChedule displays a success message for adding the event.
<br><br>
Use case ends.

**Extensions**

* 1a. Some required information about the event is missing in the command.

    * 1a1. SOChedule displays an error message suggesting that information provided when creating
      the event is incomplete.
      Use case ends.


* 1b. The date provided for the event is invalid

    * 1b1. SOChedule displays an error message suggesting that date provided for the event
      is invalid, or not following the `YYYY-MM-DD` format.
      Use case ends.

**Use case: UC14 - Delete an event**

**MSS**

1. User requests to <u> list events (UC06)</u>.
2. SOChedule shows a list of events.
3. User chooses to delete an event.
4. User enters the index of the event to be deleted.
5. SOChedule displays a success message for deleting the event.
<br><br>
Use case ends.

**Extensions**

* 2a. The event scheduler is empty.

  Use case ends.


* 3a. The given index is invalid.

    * 3a1. SOChedule shows an error message.

      Use case resumes at step 2.

**Use case: UC15 - Editing an event**

`<pending>`

**Use case: UC16 - List events**

**MSS**

1. User requests to list all events in the SOChedule.
1. SOChedule displays a list of all events added.
   <br><br>
   Use case ends.

**Extensions**

* 1a. No events have been added.
    * 1a1. SOChedule displays an empty list.
      Use case ends.

**Use case: UC17 - Getting today's events**

`<pending>`

**Use case: UC18 - Find an event**

`<pending>`

**Use case: UC19 - Clearing expired events**

`<pending>`

**Use case: UC20 - Finding tasks and events before or on given date**

`<pending>`

**Use case: UC21 - Finding free time slots**

**MSS**

1. User requests to find free time slots on a given date.
2. SOChedule shows a list of free time slots on that date.
   Use case ends.

**Extensions**

* 2a. SOChedule notifies that there is no free time slots.

  Use case ends.


* 3a. The given date is invalid.

    * 3a1. SOChedule shows an error message indicating the invalidity of the date.

      Use case ends.

**Use case: UC22 - Getting a summary of SOChedule**

**MSS**

1. User requests to have a summary.
2. SOChedule shows a list of different types of tasks and events happening in the next 7 days.
   Use case ends.

**Use case: UC23 - Clearing SOChedule**

**MSS**

1. User requests to clear the whole SOChedule.
2. SOChedule clears all tasks and events stored.
3. SOChedule shows an empty list of tasks and events.

*{More to be added}*

### A4. Non-Functional Requirements

1.  Should work on any _mainstream OS_ as long as it has Java `11` or above installed.
1.  Should be able to hold up to 1000 tasks without a noticeable sluggishness in performance for typical usage.
1.  Should be able to hold up to 500 events without a noticeable sluggishness in performance for typical usage.
1.  A user with above average typing speed for regular English text (i.e. not code, not system admin commands) should be able to accomplish most of the tasks faster using commands than using the mouse.
1.  Should give a response to user's input within 5 seconds.
1.  The user interface should be intuitive to a SoC freshman with little knowledge about programming.
1.  The source code should be open source.

*{More to be added}*

### A5. Glossary

* **Mainstream OS**: Windows, Linux, Unix, OS-X
* **Event**: Activities that start at a specific time and ends at a specific time.
* **Task**: Activities to be undertaken that can be marked complete/incomplete. Optionally it has a date field to indicate deadlines.

--------------------------------------------------------------------------------------------------------------------

### A6. Instructions for manual testing

Given below are instructions to test the app manually.

<div markdown="span" class="alert alert-info">:information_source: **Note:** These instructions only provide a starting point for testers to work on;
testers are expected to do more *exploratory* testing.

</div>

### A7. Launch and shutdown

1. Initial launch

   1. Download the jar file and copy into an empty folder

   1. Double-click the jar file Expected: Shows the GUI with a set of sample contacts. The window size may not be optimum.

1. Saving window preferences

   1. Resize the window to an optimum size. Move the window to a different location. Close the window.

   1. Re-launch the app by double-clicking the jar file.<br>
       Expected: The most recent window size and location is retained.

1. _{ more test cases …​ }_

### Deleting a task (Not in use yet)

1. Deleting a task while all tasks are being shown

   1. Prerequisites: List all tasks using the `list_task` command. Multiple tasks in the list.

   1. Test case: `delete_task 1`<br>
      Expected: First task is deleted from the list. Details of the deleted task shown in the status message.

   1. Test case: `delete_task 0`<br>
      Expected: No task is deleted. Error details shown in the status message. Status bar remains the same.

   1. Other incorrect delete commands to try: `delete_task`, `delete_task x`, `...` (where x is larger than the list size)<br>
      Expected: Similar to previous.

1. _{ more test cases …​ }_

### A8. Saving data

1. Dealing with missing/corrupted data files

   1. _{explain how to simulate a missing/corrupted file, and the expected behavior}_

1. _{ more test cases …​ }_

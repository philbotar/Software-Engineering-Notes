These notes are in the form of flashcards.

What System Design pattern should you think of when you have to handle alot of state and failures?
Think Multi-Step Processes. They are built to handle complex state and can restart in the case of failures.

What can Event Sourcing help handle in System Design?
Event Sourcing can handle highly stateful systems where you can store a log of actions rather than the end state of a value. It gives you fault tolerance, scalability, observability and flexibility.

What are Workflows? What are they good for in System Design?
They are long running processes that can survive failures and pick up where they left off. They are good for Multi-Step processes in distributed systems.

What are the Two Types of Workflows we should know for System Design? 1. Durable Execution Engines. Its a way to write long running code that can move between machines and survive system failures and restarts. When a system crashes, DEE's will just continue where it left off on a new machine. These usually use CODE and Temporal is the most popular version of this. 2.

What is a Durable Execution Engine?
Its a way to write long running code that can move between machines and survive system failures and restarts. When a system crashes, DEE's will just continue where it left off on a new machine. These usually use CODE and Temporal is the most popular version of this.

What is Temporal
Temporal is a durable execution engine. It centers around workflows and activities. Workflows are the high level flow of your system, and activities are the individual steps in that flow. - Workflows are deterministic, same inputs and history, they give the same output. - Activities are Idempotent: they can be called multiple times with the same inputs and get the same result, but temporal guarantees that they are not retried once successful.
![[assets/Screenshot 2025-12-08 at 5.12.01 pm.png]]
The way this works is each activity run is recorded into a history database. If a workflow runner crashes, another runner can replay the workflow and utilize the history database to remember what happened with each activity invocation: eliminating the need to run the activity again. Activity executions can be spread across many worker machines, and the workflow engine will automatically balance the work across them.

What is a Managed Workflow System?
Example: AWS Step Functions. Its almost the same as Durable Execution Engines, where it can orchestrate a workflow, call activities and record progress in the same way, but its declarative. You can make visual workflows with this and use YML instead of code. Not usually a point of debate in interviews.

What are some common interview systems where you would use Durable Execution Engines? 1. Payment Systems. Theres alot of state and a strong need to be able to handle failures gracefully. 2. Human in the loop workflows: In products like Uber, where theres a wait for a human to complete a task.

How would you handle updates to a workflow in Durable Execution Engines? 1. Workflow versioning: Give workflows versions and for all non-running workflows use the new version. 2. Workflow Migrations: alot like alembic, switch over when possible in the code. You'll use a "patch" which will help the system deterministically decide whether to take the new path or not.

How do we deal with external events in Workflows?
Workflows excel in not using resources when they shouldn't be. Workflows can wait indefinitely without using resources for the response, and go live when they get one or time out.

How can we ensure X step only runs once in Durable Execution Engines ?
Most workflow systems provide a way to ensure an activity runs _exactly once_ ... for a very specific definition of "run". If the activity finishes successfully, but fails to "ack" to the workflow engine, the workflow engine will retry the activity. This might be a bad thing if the activity is a refund or an email send.
The solution is to make the activity **idempotent**. This means that the activity can be called multiple times with the same inputs and get the same result. Storing off a key to a database (e.g. the idempotency key of the email) and then checking if it exists before performing the irreversible action is a common pattern to accomplish this.

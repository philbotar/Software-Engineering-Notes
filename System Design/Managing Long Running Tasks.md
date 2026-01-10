
What is the long running tasks pattern? 
	It splits API requests into two phases: Immediate acknowledgement and background processing. When users submit heavy tasks, the web server instantly validates the request, pushes the job to the queue, and returns a job ID. Meanwhile, separate worker processes continuously poll the queue, grab pending jobs, execute the actual work and update the status in a database. 

What do you gain from using the long running tasks pattern?
	1. Faster user response time: API immediately returns.
	2. Indenpendent Scaling: Web servers and web workers scale separately
	3. Fault Isolation: A worker crash processing one video doesn't bring down your entire API. Failed jobs can be retried without affecting user-facing services
	4. Better resource utilisation: CPU-intensive workers run on compute-optimized instances. Memory-heavy tasks get high-memory machines.

What do you lose from using the long running tasks pattern? 
	1. System Complexity increases.
	2. Eventual Consistency (Wait for task to finish)
	3. Job Status Tracking
	4. Monitoring Overhead: Queue depth, worker health, job failure rates, processing latency - you're monitoring a distributed system instead of simple request/response cycles.

How do you implement the long running tasks pattern? 
	1. Web server validates the request and creates a job record in your database with status "pending"
	2. Web server pushes a message to the queue containing the job ID (not the full job data)
	3. Web server returns the job ID to the client immediately
	4. Worker pulls message from queue, fetches job details from database
	5. Worker updates job status to "processing"
	6. Worker performs the actual work
	7. Worker stores results (in S3 for files, database for metadata, etc.)
	8. Worker updates job status to "completed" or "failed"

What is Redis With Bull?
	Redis is a cache, and Bull is a queue. Redis provides the storage, while Bull adds job queue semantics on top: automatic retries, delayed jobs, priority queues. It's simple to set up and "just works" for 90% of use cases. While Redis offers persistence options, it's still memory-first, so you can lose jobs in a hard crash. For true durability, you'd want SQS or RabbitMQ.

What should you think of when you hear "video transcoding", "image processing", "PDF generation", "sending bulk emails", or "data exports"?
	Think of the Long Running Tasks pattern, where you decouple the task and execution in your system. 

What should you think of when a problem involves both simple API requests and GPU heavy Work?
	Think of the Long Running Tasks pattern, where you decouple the task and execution in your system, being able to run them on different machines.

What would you do if a worker crashes during a job in the long running task pattern? 
	The answer is that the job will be retried by another worker. Typically you would have a heartbeat mechanism that runs to check worker status. You need to be careful on how often it runs, because too often and its overhead, too little and youll have a dead worker. You can do this in kafka by setting a session timeout. 

How do you handle repeated task failures in the long running task pattern?
	What you do is set up a dead letter queue. If a task fails more than 3 times, you put it in the dead letter queue for investigation. The key is monitoring your DLQ since a growing dead letter queue usually signals a bug in your system that needs immediate attention.

How do you prevent duplicate work in Long Running Tasks ? 
	Idempotency Keys. When accepting a job, require a unique identifier that represents the operation. For user-initiated actions, combine user ID + action + timestamp (likely rounded to the duration you want to prevent duplicate work on). For system-generated jobs, use deterministic IDs based on the input data. Before starting work, check if a job with this key already exists. If it does, return the existing job ID instead of creating a new one.

How do you manage queue backpressure? 
	The solution is called backpressure. It slows down job acceptance when workers are overwhelmed. You can set queue depth limits and reject new jobs when the queue is too deep and return a "system busy" response immediately rather than accepting work you can't handle.
	You should also autoscale workers based on queue depth. When the queue grows beyond a threshold, spin up more workers. When it shrinks, scale down. CloudWatch alarms + Auto Scaling groups make this straightforward on AWS. The key metric is queue depth, not CPU usage. By the time CPU is high, your queue is already backed up.

How do you handle mixed size workloads in long running tasks ?
	You do this by having multiple queues, and each queue has a "size" of which they accept for the tasks. If there are "large" tasks, the queue takes it and sends it to more equipped workers, same with small tasks. You can also split out your tasks into smaller tasks to be fanned out. 

What if generating a report requires three steps: fetch data, generate PDF, then email it. How do you handle jobs that depend on other jobs? (Long Running Processes)
	Without proper orchestration, you end up with spaghetti code. Workers complete step 1 then directly queue step 2, making the flow hard to understand and modify. Error handling becomes a nightmare - if step 2 fails, should you retry just that step or start over? Monitoring which step failed and why requires digging through logs.
	For simple chains, have each worker queue the next job before marking itself complete. Include the full context in each job so steps can be retried independently. For complex workflows with branching or parallel steps, use a workflow orchestrator like AWS Step Functions, Temporal, or Airflow.

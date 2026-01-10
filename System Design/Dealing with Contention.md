## The Problem

When multiple users attempt to perform the same action on a piece of data simultaneously, there is the issue of contention. Which change do you prioritise? This is common issue for things such as concert websites. If both perform different things at the same time, one will recieve stale data, which in the case of a concert may be a seat they actually dont have.

## The Solution

---

### Single Node Solutions

#### Atomicity

- When dealing with ACID compliant databases, you know that the transaction will never get committed in an incorrect way.
- The problem is, if there are conditionals that are read at point A, and by the time the change is committed at point B, the value that was read is stale.
- This boils down to the fact that transactions provide atomicity within themselves, but don't prevent other transactions from reading the same data concurrently.

#### Pessimistic Locking

- Means to get the lock, before making the change.
- FOR UPDATE in SQL is a form of pessimistic locking
- A lock is a mechanism that prevents other database connections from accessing the same data until the lock is released.
  - You want to lock things for the shortest possible time, as you create bottlenecks or kill concurrency the longer it stays locked.

#### Isolation Levels

- This is letting the database automatically handle conflicts by raising the isolation level.
- There are 4 Isolation levels
  1.  READ UNCOMMITTED - Can see uncommitted changes from other transactions (rarely used)
  2.  READ COMMITTED - Can only see committed changes (default in PostgreSQL)
  3.  REPEATABLE READ - Same data read multiple times within a transaction stays consistent (default in MySQL)
  4.  SERIALIZABLE - Strongest isolation, transactions appear to run one after another. Database automatically detects conflicts and aborts one transaction if they interfere with each other.

```
-- Set isolation level for this transaction
BEGIN TRANSACTION ISOLATION LEVEL SERIALIZABLE;

UPDATE concerts
SET available_seats = available_seats - 1
WHERE concert_id = 'weeknd_tour'

-- Create the ticket record
INSERT INTO tickets (user_id, concert_id, seat_number, purchase_time)
VALUES ('user123', 'weeknd_tour', 'A15', NOW());

COMMIT;
```

- Serializable Isolation is much more EXPENSIVE than explicit locks. It requires the database to track all reads and writes to detect potential conflicts, and transaction aborts waste work that must be redone. Explicit locks give you precise control over what gets locked and when, making them more efficient for scenarios where you know exactly which resources need coordination.

#### Optimistic Concurrency Control

- Makes assumption that conflicts are rare, and detects them AFTER they occur.
- This is better for performance, letting all transacations attempt to complete and only retrying those that fail.
- The pattern is just having a Version Number with your data, Every time you update the record you update the version. When updating, specify both the new value and the expected current version.
  - This is good for things that arent highly contentious, such as buying the same item at the same time on a commerce site.

```
-- Alice reads: 1 seat available
-- Bob reads: 1 seat available

-- Alice tries to update first:
BEGIN TRANSACTION;
UPDATE concerts
SET available_seats = available_seats - 1
WHERE concert_id = 'weeknd_tour'
  AND available_seats = 1;  -- Expected current value

INSERT INTO tickets (user_id, concert_id, seat_number, purchase_time)
VALUES ('alice', 'weeknd_tour', 'A15', NOW());
COMMIT;

-- Alice's update succeeds, seats now = 0

-- Bob tries to update:
BEGIN TRANSACTION;
UPDATE concerts
SET available_seats = available_seats - 1
WHERE concert_id = 'weeknd_tour'
  AND available_seats = 1;  -- Stale value!

-- Bob's update affects 0 rows - conflict detected, transaction rolls back
```

## Multiple Nodes

- What happens when you need to coordinate updates across multiple databases?
- _Note_: If you identify that your system needs strong consistency during high contention scenarios, do everything you can to keep the relevant data in a single database.
- Consider a situation where Alice and Bob have accounts in different databases, Database A needs to credit a row in database B, so a row in both DB's must complete or fail. Below are some ways to ensure that both rows don't end up in malformed states.

### Two Phase Commit

- Where your transfer service acts as the co-ordinator, managing the transaction across all the nodes.
  1.  The coordinator asks all participants to prepare the transaction for the first phase
  2.  Then tells them to commit or Abort in the transaction based on whether the first phase was successful or not.
- The coordinator MUST write to a persistent log before sending any commit or abort decisions, logging the participants, the changes and the current state. We need this in case the coordinator crashes mid transaction.
- Be careful of keeping transactions open across network calls, as they keep the accounts locked. If the coordinator crashes before unlocking, those accounts could be locked forever. Usually a TTL is applied but if the TTL is too short, transacations get cancelled when they shouldn't.
- Both databases in the prepare phase will check. If the transaction can be done and draft the update, keeping the transaction open. When the coordinator decides that its time to change the values / complete the transaction, it will notify both DBs to complete it. - Either both prepare successfully, and the transaction goes through, or neither do and the transaction fails. - Guarantees Atomicity, but is expensive and fragile. If a DB is slow, or theres a network partition, this becomes painful to maintain.
  ![[assets/assets/Screenshot 2025-12-05 at 6.56.29 am.png]]

```
-- Database A during prepare phase
BEGIN TRANSACTION;
SELECT balance FROM accounts WHERE user_id = 'alice' FOR UPDATE;
-- Check if balance >= 100
UPDATE accounts SET balance = balance - 100 WHERE user_id = 'alice';
-- Transaction stays open, waiting for coordinator's decision

-- Database B during prepare phase
BEGIN TRANSACTION;
SELECT * FROM accounts WHERE user_id = 'bob' FOR UPDATE;
-- Verify account exists and is active
UPDATE accounts SET balance = balance + 100 WHERE user_id = 'bob';
-- Transaction stays open, waiting for coordinator's decision
```

### Distributed Locks

- This is for simpler coordination needs.
- Guarantees only a single process can work on a particular resource at a time.
- 3 Ways to Implement this.

### 1. Redis with TTL

- Maintain a Redis cache. Because Redis provides atomic operations with automatic expiration, its ideal for distributed locks.
- Distributed because all services can see redis and the lock
- When the lock expires or is explicitly deleted, the resource becomes available again.
- Advantage: Speed and Simplicity
- Disadvantage: Single Point of failure, and handling scenarios where Redis is unavailable.2

### 2 . Database Columns

- You can implement distributed locks using your existing database by adding status and expiration columns to track which resources are locked.
- A cleanup cronjob periodically goes through all the values and cleans the expired lockes, but you need to handle race conditions between the cleanup job and users trying to extend their locks
- Advantage
  - consistency with your existing data and no additional infra needed.
- Disadvantage
  - Database operations are slower than cache operations and you need to implement cleanup logic

#### 3. Zookeeper / etcd

- Purpose built coordination services designed specifically for distributed systems.
- provide strong consistency guarantees even during network partitions and leader failures.
- Zookeeper uses "Ephermal nodes" that auto disapper when the client session ends.
  - Both Zookeeper and Etcd use consensus algorithms (Raft, ZAB) to maintain consistency across nodes.
- Advantage
  - Robustness
- Disadvantage
  - Operational complexity, as you need to run and maintain a seperate coordination cluster.

#### Saga Pattern

- Breaks operation into a sequence of independent steps, that can be reverted if anything goes wrong.
- instead of doing each all at once, you do one after the other, and if they fail you do the inverse.
- Each step is a complete, committed transaction. There are no long-running open transactions and no coordinator crashes leaving things in limbo.
- Trade-off: The system is temporarily inconsistent.

## Choosing the Right Approach

| Approach                     | Use When                                                         | Avoid When                                               | Typical Latency                      | Complexity |
| ---------------------------- | ---------------------------------------------------------------- | -------------------------------------------------------- | ------------------------------------ | ---------- |
| **Pessimistic Locking**      | High contention, critical consistency, single database           | Low contention, high throughput needs                    | Low (single DB query)                | Low        |
| **SERIALIZABLE Isolation**   | Need automatic conflict detection, can't identify specific locks | Performance critical, high contention                    | Medium (conflict detection overhead) | Low        |
| **Optimistic Concurrency**   | Low contention, high read/write ratio, performance critical      | High contention, can't tolerate retries                  | Low (when no conflicts)              | Medium     |
| **Distributed Transactions** | Must have atomicity across systems, can tolerate complexity      | High availability requirements, performance critical     | High (network coordination)          | Very High  |
| **Distributed Locks**        | User-facing flows, need reservations, simpler than 2PC           | No alternatives available, purely technical coordination | Low (simple status updates)          | Medium     |

![[assets/Screenshot 2025-12-05 at 2.22.54 pm.png]]

When in doubt, start with pessimistic locking in a single database. It's simple, predictable and you can always improve it later.

## When to use in Interviews

- **Multiple Users competing for limited resources**: Such as concert tickets, auction bidding, seat reservations. etc.
- **Prevent double booking or double charging**: payment processing, seat reservations
- **Ensure data consistency under high consistency**: Account Balance updates, inventory management, collaborative editing.
- **Handle race conditions in distributed systems**: In any scenario where the same thing may happen simultaneously across multiple servers and where the outcome is sensitive to order of operations.

## Common Interview Scenarios

- Online Auction Systems
- Ticketmaster / Event Booking
- Banking / Payment Systems
- Ride Sharing Dispatch
- Yelp / Review Systems

## When not to overcomplicate

- **Low contention scenarios** where conflicts are rare (updating product descriptions where only admins can edit)\, you can use basic optimistic concurrency with retry logic. Don't implement elaborate locking schemes when simple retry logic handles the occasional conflict.
- **Single User Operations**: personal todo lists, private docs, or user prefences have no contention
- **Read Heavy Workloads** where most operations are occasional writes.

## Common Deep Dives

### **_How do you prevent deadlocks with pessimistic Locking ?_**

Consider a bank transfer between two accounts. Alice wants to transfer $100 to Bob, while Bob simultaneously wants to transfer $50 to Alice. Transaction A needs to debit Alice's account and credit Bob's account. Transaction B needs to debit Bob's account and credit Alice's account. Transaction A locks Alice's account first, then tries to lock Bob's account. Transaction B locks Bob's account first, then tries to lock Alice's account. Both transactions wait forever for the other to release their lock.
![[assets/Screenshot 2025-12-05 at 2.30.18 pm.png]]

_Solution_
The standard solution is ordered locking, which means you always get the locks in a consistent order, regardless of the business logic flow. This means that you sort the database rows the same way each time and lock the first that you find, ensuring that you can never lock the second without the first, regardless of whos doing what.

The fallback to this is having database timeout configurations that automatically release the lock after x time. Set these to be a reasonable time so that transactions can get killed before unlocking. Most modern DB's have automatic deadlock detection that kills one transaction when cycles are detected, but this should be a last resort.

#### What if your coordinator service crashes during a distributed transaction

Databases are sitting with prepared transactions, waiting for commit or abort instructions that never come. Those transactions hold locks on resources, potentially blocking other operations indefinitely.
![[assets/Screenshot 2025-12-05 at 2.46.54 pm.png]]
Production systems handle this with coordinator failover and transaction recovery. When a new coordinator starts up, it reads persistent logs to determine which transactions were in-flight and completes them.

Sagas are more resilient here (as discussed earlier) because they don't hold locks across network calls. Coordinator failure just pauses progress rather than leaving participants in limbo.

### How do you handle the ABA problem with optimistic concurrency control?

The ABA problem happens when a value changes from A → B → A between a read and a write. An optimistic concurrency check that only looks at the final value A will falsely assume nothing changed, even though important state transitions occurred. In something like a Yelp-style review system, a restaurant might have a 4.0 rating with 100 reviews. Two new reviews (5 stars and 3 stars) arrive at the same time, both see the current average as 4.0, compute a new average, and update. Because of the math, the final average might still be 4.0 with 102 reviews, but if you only use the average rating as your concurrency token, both operations appear valid and you can accidentally drop one of the updates.

To avoid this, use a field that you know will always change as your version. In the Yelp example, the review count is ideal because every new review increments it. The update becomes: “set the new average and increment the count to 101, but only if the current count is 100.” If no naturally changing field exists, add an explicit version column that increments on every update, even if the business data (like the average rating) stays the same. Some databases, such as DynamoDB, handle this for you with internal versioning that advances on every write, ensuring you reliably detect concurrent updates.

### What about performance when everyone wants the same resource?

The hot partition / celebrity problem. The fundamental issue is that normal scaling strategies break down when demand concentrates on a single point. Sharding doesn't help because you can't spilt the taylor swift concert into mulitiple databases when everyone wants a specific resource.

Your first strategy should be questioning whether you can change the problem itself rather than the infrastructure. Maybe instead of one auction item, you have 10 identicals and can run seperate auctions for each.

For situations where you truly need strong consistency on a hot resource, implement queue based serialization. Put all requests for that specific resource into a dedicated queue that gets processed by a single worker thread. This acts as a buffer that can absorb traffic spikes while the worker processes requests at a sustainable rate.

The tradeoff is latency. Users may wait longer for their requests to be processed, but this is often better than the alternative of having your entire system grind to a halt under the contention.

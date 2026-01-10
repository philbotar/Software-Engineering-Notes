## Functional Requirements

- Talk a little bit about windowing at the beginning. When we talk about the last hour, do we mean from our current time or from when the hour began?
  - Sliding Window (start moves with our time), or
  - Tumbling Window: The window starts at the beginning of our hour
  - Propose to the interviewer that we should use tumbling windows and give them a moment to object.
- Clarify the time periods we're going to support.
- Put some constraint on the size of hte top k. Since it can be unbounded, we should have a limit to what we would like to precompute up to.

**Core Requirements**

1. Clients should be able to query the top K videos for all-time (up to a max of 1k results).
2. Clients should be able to query *tumbling windows* of 1 {hour, day, month} and all-time (up to a max of 1k results).

**Below the line (out of scope):**

- Arbitrary time periods.
- Arbitrary starting/ending points (we'll assume all queries are looking back from the current moment).

## Non-functional Requirements

- Key Question: Whats the buffer we give ourselves between when a view happens and when it needs to be tabulated and included in our top k calculation.
- Key Question: Will we tolerate approximation in our results? If so think of Count-Min Sketch to approximate how many.
- Key Question: Whats the latency on our system? We should aim to be within 10's of milliseconds on our retrieves.

**Core Requirements**

1. We'll tolerate at most 1 min delay between when a view occurs and when it should be tabulated.
2. Our results must be precise, so we should not approximate.
3. We should return results within 10's of milliseconds.
4. Our system should be able to handle a massive number (TBD - cover this later) of views per second.
5. We should support a massive number (TBD - cover this later) of videos.

![[../assets/Screenshot 2025-12-21 at 2.52.13 pm.png]]

## Core Entities

1. Video: Video Being Viewed
2. View: The view event that happened
3. Time Window: The time window associated with the query "all time", "last hour", etc.

## API or System Interface

GET /views/top-k?window={WINDOW}&k={K} -> { videoId: string, views: number }[]

Especially for more senior candidates, it's important to focus your efforts on the "interesting" aspects of the interview. Spending too much time on obvious elements both deprives you of time for the more interesting parts of the interview but also signals to the interviewer that you may not be able to distinguish more complex pieces from trivial ones: a critical skill for senior engineers.

## High Level Design

It's important that we're noting to our interviewer the bottlenecks that are appearing as we go about our design. You don't want your interviewer to think you've missed something obvious even as you're deliberately carving a simpler path to begin with.

### 1) Clients should be able to query the top K videos for all-time (up to a max of 1k results).

- Make the assumption that we are getting our events from a kafka stream so that we can bypass alot of the setup
- Start Simple
  1. The view consumer retrieves a view event from the Kafka stream.
  2. The view consumer updates the counter for the video ID in the Postgres database.
- Next, we need a way to query for the topK videos, so we can start with a simple index on a table and query from it.
  ![[../assets/Screenshot 2025-12-21 at 3.02.27 pm.png]]
- Note: Updating the index takes the write from an O(1) append to a O(log n) update to the index.

### 2) Clients should be able to query *tumbling windows* of 1 {hour, day, month} and all-time (up to a max of 1k results).

Next, we need to extend our system to support time window queries like last hour, last day, last month, etc. Again, we're going to go with a simple (even naive) solution first and then use it as a springboard to guide us to a more optimal solution.

Add to our table schema a Timestamp column. We can set this column to be the hour of the view event, having one row for each video that has been viewed at least once in a given hour. This will blow up the number of rows we use, so we need to improve this later.

![[../assets/Screenshot 2025-12-21 at 3.05.24 pm.png]]

Then we will also need to update the query to do window reads like so

```sql
SELECT "videoId", SUM("views") as "views",
FROM VideoViews
WHERE "timestamp" >= {windowStart} AND "timestamp" <= {windowEnd}
GROUP BY "videoId"
ORDER BY SUM("views") DESC LIMIT {k};
```

## Potential Deep Dives

### 1) How can we cut down on the number of queries to the database?

Pre-compute the Tok K for each time window

Another approach we can utilize is to add a cron to our system which, on fixed intervals, will precompute the top K for each time window and warms our cache in the same way. Then, requests that come to our top-K service are only reading from the cache, never querying the database.
![[../assets/Screenshot 2025-12-21 at 3.07.31 pm.png]]
This solves the problem of (b) in our "good" solution where we break our SLA around window boundaries when our cache expires. Because our cron job is running at fixed intervals, it has time to "get ahead" of the expirations that would have happened.

###### Challenges

The biggest problem with this approach is that it adds some operational complexity. What happens if the cron job fails? How do we monitor to ensure we're not very far behind? In the grander scheme of things, these are not the most important aspects of this particular design (we have a lot more to cover) so most interviewers aren't going to ask for additional details here.

We can retain the cache entries for a couple hours to solve for the case where our precomputation job is late. Then the top-k service can serve stale data for a short period of time rather than nothing.

### 2) How can we handle the massive number of writes to the database?

In most interviews, we can assume "big" for a lot of quantities and avoid a back-of-the-envelope estimation. As general guidance, the deeper the infra challenge, the more likely you are to encounter an interviewer who wants you to do some estimation. Regardless, the same rule applies for _any_ problem: **estimate when you need it and when it might influence your design**. And here we really do need it!

Throughput Calc
`70B views/day / (100k seconds/day) = 700k tps

Storage Calc
`Videos/Day = 1 hour content/second / (6 minutes content/video) \* (100k seconds/day) = 1M videos/day

`Total Videos = 1M videos/day * 365 days/year * 10 years = 3.6B videos`

Estimate of a naive table of ID's and counts
`Naive Storage = 4B videos \* (8 bytes/ID + 8 bytes/count) = 64 GB

#### Sharding Ingestion

We can scale our view consumers horizontally by spinning up multiple instances to read from each partition of the ViewEvent topic. We'll shard our database by the same scheme, so that each shard has a partial view of only a subset of videos. Each View consumer will be writing to its own shard of the database.![[../assets/Screenshot 2025-12-21 at 3.12.39 pm.png]]

1. When a view comes in, it is assigned to a shard based on the video ID.
2. The view consumer for that partition reads the view event from the Kafka topic and fires off a write to the database for that shard.

By sharding the database, we no longer get the benefit of our single SQL query to get the top K. Instead, we need to query each shard and merge the results (either manually or by using something like Citus).

Fortunately, this is an easy enough operation. By grabbing the top K from _each shard_, we're mathematically guaranteed to have in our final list the "global" top K. So our top K cron is updated to make one call for each shard and merge the results.

#### Batching Ingestion

videos, in any given minute a lot of those views are going to be on a small number of popular Mr. Beast and Taylor Swift videos. Instead of making a write to the database for each view, we can batch up the writes for each video and flush these batches periodically to the database.

A great option for doing this is [Flink](https://www.hellointerview.com/learn/system-design/deep-dives/flink). Flink is a stream processing framework that gives us a bunch of convenient tools for handling batching and aggregation in streaming applications. Flink handles checkpoint and recovery for us, so we don't have to worry about losing data or struggling with itchy problems like event delays.

For this Flink application, we'll use BoundedOutOfOrdernessWatermarkStrategy to handle late events: basically we'll tell Flink that we're ok waiting up to some time (probably 30 seconds here, < 1 minute) for late events to arrive. We'll also use a tumbling window of 1 hour to aggregate the views for each video.

Since Flink is reading from Kafka, if a given host fails or goes down, Flink can rewind to the checkpoint offset in the Kafka topic and resume processing from there.
![[../assets/Screenshot 2025-12-21 at 3.13.36 pm.png]]
Because we're batching, instead of a steady stream of writes we now have a big lump of writes every hour. As long as these are spread across shards, this is acceptable and it can even be more optimal, as databases handle bulk data much more efficiently than individual writes.

We should also expect that the number of writes will be smaller than before (by anything from 2-100x) because we're summing many views that could have happened in one hour for a singular video.

All told, by batching, we can probably bring the number of shards down in the 5-10 range. Still not great, but manageable. Let's keep going.

### 3) How do we optimize our top K queries?

Great Solution: Maintain Aggregates for Each Window in the Database

Instead of maintaining aggregates for arbitrary partitions of time, we can maintain aggregates for each window. As we discussed earlier, if we have a VideoViewsLastHour table with an index on the views column, our queries are very fast! In this case, all we need to do is have our top K cron read the top K videos directly from our index.

To make this work, we'll need a table which has the current aggregate for each video for each window.
![[../assets/Screenshot 2025-12-21 at 3.15.36 pm.png]]

To tie this all together, for the video views in the last month:

1. Our Flink jobs will continue to aggregate, at hour grain, the views for each video.
2. Whenever a new hour has passed and we're ready to write to our database, we'll make updates to our window tables (e.g. VideoViewsLastHour) rather than writing to the hour-grain VideoViews table.

###### Challenges

This approach moves some complexity from the top-K reads to the writes. We now have to write to 4 tables instead of just 1.

### 4) What if we need to support sliding windows?

To keep track of the last hour of views for a video, when we have a new batch of views for the most recent minute we can **increase** by the views that have come in during that minute and **decrease** by the views that happened 60 minutes ago.

To tie this all together, for the video views in the last hour:

1. Our Flink jobs aggregate, at a minute grain, the views for each video.
2. Whenever a new minute has passed and we're ready to write to our database, we'll... 1. Read all of the views that happened in the minute exactly T-60 minutes ago. Call this our decrement. 2. Write all of the views that happened in the last minute (our increment) to VideoViews. 3. Update the VideoViewsLastHour table with difference between the increment and decrement.
   ![[../assets/Screenshot 2025-12-21 at 3.17.22 pm.png]]

One mature path is to propose an alternative to our interviewer: how about we support sliding windows for the "last hour" and tumbling windows for the "last day" and "last month"? This matches the product experience: we don't expect the top videos for the month to change quickly, but we do for the last hour.

Another option here is to use a different consumer group to process Kafka events on a lag. One consumer group can increment our counters, and the other consumer group is responsible for decrementing them after they've expired. This means we need to have 1 month+ of retention on our Kafka topic, but we don't need to keep minute-grained data around anymore and we can take advantage of the log-based nature and performance of Kafka.

Some might suggest we could use Flink's native sliding windows. Unfortunately, sliding windows with a 1 minute slide multiplies the memory by 60 _ 24 _ 30 = 43,200x! This is impractical.

### 5) Can we make use of approximations to improve performance?

To do this, we'll use a data structure/approach called Count-Min Sketch (CMS). CMS allows us to estimate the number of times an items has been added to the "sketch", the underlying data structure, with substantially less memory required than the hundred-gigabyte full hash table we'd need to maintain otherwise (think: hundreds of megabytes). To do this, CMS uses a set of hash functions to map items to a 2d array of counters. It _forgets_ the actual items, but remembers how many times they've been seen by virtue of their hash.

1. When a view comes in, we'll add it to our CMS. This updates our sketch so we can remember this view.
2. Immediately after we add, we'll estimate the number of times we've seen it. This gives us an upper bound on the number of views this item has received and a decent approximation.
3. We'll add this view count to a sorted list or heap. We'll truncate this list periodically so we're not using excessive memory. Since our users can never query values higher than the top 1000, for all time we can keep the sorted list to 1000 entries.
4. When we want to retrieve the top K items, we'll grab the top K items from the sorted list!

Great Solution: Flink
If we're using a Flink-based approach, the CMS and the sorted list can both be maintained in Flink's state management in memory (checkpointed by Flink to avoid losing state). This is just custom code we're writing into our job rather than additional infra we're deploying, so our diagram largely looks the same!
![[../assets/Screenshot 2025-12-21 at 3.19.30 pm.png]]

If a node goes down, Flink allows us to restore from a checkpoint and replay our state from the events in the Kafka stream.
This approach is more resilient to failures than the Redis approach and more efficient because the data structure is kept in memory.

###### Challenges

Like earlier Flink-based solutions, this approach will require both that (a) your interviewer understands enough about Flink to understand the proposal, and (b) you're able to go into detail about how each of the pieces works at a lower level. Expect to spend some time talking about the job structure, how state is managed, etc.

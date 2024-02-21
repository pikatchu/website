---
title: Concurrency in SKDB
author: Julien Verlaguet
category: Reactive programming
layout: post
---

Concurrency is a foundational concept in database systems, dictating
how multiple transactions occur simultaneously. Today, I'll delve into
how concurrency functions in reactive systems in general and in SKDB
in particular.

### Reactive Databases: What Sets Them Apart?

Reactive databases come with a unique feature: the capability to
incrementally update any query or transaction. Suppose you initiate a
'select' query and, while it's in progress, the underlying data
changes. In a reactive database, this 'select' will recognize the
change and incrementally update accordingly. This dynamic nature is
true not only for 'select' but also for any other SQL transactions.

### Concurrency in Traditional Databases

![alt text](../img/small_lock.png)

#### Global Lock

One simple way is to have a global lock. Here, multiple users can read
simultaneously, but only one can write at a time. While this method
sidesteps concurrency issues, it's inefficient for multiple users
accessing a single database.

#### Locking Mechanisms

A more intricate method involves placing locks at different levels,
like rows or tables. When executing a transaction, data gets locked at
these levels. For instances where deadlocks might occur or data is
being accessed by another user, a journal system keeps track, ensuring
transaction integrity.

### SKDB’s Unique Approach

SKDB deviates from these traditional methods. Here's how:

When a transaction starts, SKDB hands over a version of the database
to the user. This isn't a physical copy, but rather a pointer to your
version of the data. Users can then execute their transaction
concurrently on their thread, separate from other transactions.

Once the transaction finishes and it's time to commit the changes,
this is where the uniqueness of SKDB shines. Recognizing that the
database may have undergone changes between the start and commit of
the transaction, it **incrementally updates the transaction**. As
transactions are reactive, they can quickly adjust to these changes,
most of which may not affect the current transaction.

### There's a Catch!

In traditional database systems, when a 'select' query is executed,
results are immediately displayed or returned to the user, reflecting
the current state of the database at the time of the query. However,
SKDB introduces a unique operational paradigm wherein no data—be it
query results or other outputs—leaves the database until the entire
transaction is completed and the "commit" command is invoked.

Why is that? Because of how commits work. Remember that we
incrementally update transactions at commit time, right? Well, letting
data leave the database before the commit would leave the user with
potentially inconsistent data.

### Wrapping Up

By allowing most of the transactional work to happen outside the
global lock and leveraging the reactive nature of transactions, SKDB
offers a highly efficient, concurrent database system. In essence, it
minimizes transactional bottlenecks, making it a compelling choice for
database operations that require high levels of concurrent user
access.

![alt text](../img/until_next_time.png){:width="100%"}{:.centered}

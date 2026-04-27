### 8 Layer Stack - Distributed Systems Architecture
1. DNS Routing: Route users to nearest region
2. API Gateway: Authenticate & route reqs
3. Microservices: Parallel fan-out to services
4. Data & Caching: Cache hits & Persistent storage
5. CDN (Petflix Box): Video served from user's ISP
6. Pods & Replication: Leader election & consensus
7. VMs & Containers: Isolation & Orchestration
8. Cores & Threads: Thread Scheduling & Parallelism

**(WRITE NOTES FOR THE ABOVE TOPIC)**
## Transactions and OLTP Databases
- OLTP - Online Transaction Processing Database
- OLAP - Online Analytical Processing Database

| Property            | OLTP                                                                          | OLAP                                                      |
| ------------------- | ----------------------------------------------------------------------------- | --------------------------------------------------------- |
| Supported Functions | Supportds realtime reading and writing/updating data                          | Read only, no-updates. Data re-written in batches offline |
| Query Size          | Smaller sized queries                                                         | Usually larger queries (scans and aggregates)             |
| Type of DB          | Sometimes relational, sometimes not                                           | Sometimes relational, sometimes not                       |
| Use cases           | Financial Transactions, upto date game progress and social media, Google Docs | Interactive Data Analysis, building stat model            |
| Examples            | MySQL, Postgres, Redis, Casandra, DynamoDB, Aurora/RDS                        | BigQuery, Snowflake, Databricks, Redshift                 |
OLAP also provides transactions abstraction

#### Transactions (TXNs)
> A **database transaction** is a single, logical unit of work that contains one or more operations (reads or writes), which are all treated as an "all-or-nothing" operation and reflects a single real-world transition

Transactions are the core of payments, stock market, banks, gmail, google docs, gaming and healthcare systems

- Each Statement = one transactions (in **ad-hoc** SQL)
- In a program, multiple statements can be grouped together as a transaction

**Motivation for Transactions:**
- Recovery and Durability
	- Keeps the DB data consistent and durable in face of crashes, aborts, system shutdowns
	- Essential for for any system
	- Idea: make sure TXNs are either durably stored in full or not at all. Keep logs of transactions to be able to rollback 
	- One way is to add few lines in our SQL query
	```sql
	START TRANSACTION 
	INSERT INTO SmallProduct(name, price) 
			SELECT pname, price 
			FROM Product 
			WHERE price <= 0.99 
	DELETE Product 
		WHERE price <=0.99 
	COMMIT OR ROLLBACK
	```
- Concurrency
	- Creating parallel transactions without creating anomalies. 
	- Essential for better performance
	- Have DB handle running several user TXNs concurrently, in order to keep throughput high
```sql
%% Client 1: %% 
START TRANSACTION 
UPDATE Product 
SET Price = Price – 1.99 
WHERE pname = ‘Gizmo’ 
COMMIT 

%% Client 2:  %%
START TRANSACTION 
UPDATE Product 
SET Price = Price*0.5 
WHERE pname=‘Gizmo’ 
COMMIT
```
### Transaction Properties - ACID
1. Atomic 
	- State shows all effects of TXN or none of them. All or nothing
	- TXN would happen completely or not at all
	- Two outcomes
		- It commits - all changes are made
		- It aborts - no changes are made
2. Consistency
	- TXN moves from a state where integrity holds to another where integrity still holds (integrity - accuracy and consistency of data stored in databases)
	- Examples of integrity - Unique account number, sum of debits and credits is 0
	- How it's achieved:
		- Programmer writes TXN to go from one consistent state to another consistent state
		- System makes sure TXN is atomic. (User's responsibility to make sure it's atomic)
3. Isolated 
	- Effect of TXNs is the same as TXNs running one after another
	- Transactions executes concurrently with other transactions while also in isolation
	- TXN A must not observe changes from another concurrent TXN B
4. Durable 
	- Once a TXN has committed it's effects remain in the database despite termination of program, power failures and crashes
	- In data center settings: replicate data, backup

**Challenges for ACID Properties:**
- Power failures and loss of memory
- Users needing to rollback and abort (logging)
- Users executing concurrently
- ACID is controversial, many SQL DBs do not provide ACID by default
- NoSQL DBs relax ACID even more
- NewSQL reintroduces ACID compliance to NoSQL-style DBs
- To ensure **Atomicity** and **Durability** 
	- TXNs should either happen completely or not at all. No effects should be seen in aborts/crash
	- If DB stops running, changes due to completed TXNs should all persist (store on stable disk)

## Logging
- Ordered list of update records for ongoing transactions
- Contains `UNDO` information for every update
- `<TransactionID, location, old_data, new_data>` - format
- Example: Update Record - `<Tid, &A, 0,1>`
- The database
	- Owns the logging service for all applications/transactions (separate from actual data)
	- Sequential writes to log, can flush (force writes to disk)
- We need to write partial results of TXNs to disk because of memory (enough space?) and time (duration of txn) constraints

### Write-Ahead Logging (WAL) Commit Protocol
- Before any change is written to the actual database (on disk), the log record describing that change must first be written to a stable log (on disk). This is the "write-ahead" rule.
- Principle:
	- Before a data page is flushed to disk, all log records pertaining to that page must be written to the log on stable storage. 
	- All log records for a transaction must be written to stable storage before the transaction is considered committed.
- Flush rules:
	- Flush update record into log before corresponding data page goes tostorage
	- Before Transaction commits,
		- Flush all update records to log
		- Flush commit record to log (commit record - marks successful completion of a TXN)
Following WAL rules, we get 2/4 of the ACID properties

Consistency can be ignored since databases usually don't implement many constraints (3/4 done)

### Concurrency:
> *Intuition: Lock each record for shortest time possible*

DB is responsible for concurrency so that Isolation (execute each TXN as they were the only user) and Consistency is maintained (TXN must leave DB in consistent state)
- DB doesn't care which TXN executes first, dev can put T1 and T2 inside 1 TXN
- Goal: Scheduling Transactions:
	- Interleave transactions ( TXN 2 starts before the TXN 1 could finish) to boost performance
	- Data stays in good state after commits and/or aborts
- DB must pick interleaving/schedule such that isolation and consistency are maintained such that it must be as if these TXNs have executed serially
	1. Serial Schedule - one that does not interleave the actions of different transactions
	2. Equivalent Schedules - For any db state, effect of TXN A is identical to effect of TXN B
	3. Serializable Schedule - Schedule equivalent to some serial execution of the transactions
	4. Not Serializable Schedule - A schedule is different than any serial order
	
### Conflicts & Anomalies
- Two actions conflict is they are a part of different TXNs, involve the same variable and atleast one of them is a write
- Three types of conflicts:
	- Read-Write conflicts (RW)
	- Write-Read conflicts (WR)
	- Write-Write conflicts (WW)
- Conflicts are in both good and bad in transactions
- Goal: Avoid anomalies (bad schedules where isolation and consistency is broken) when interleaving transactions with conflicts

### Conflict Serializability
- When concurrent TXNs executed such that results are same as if they run one after another
- **Conflict serializable** is a specific type of serializability based on the concept of "conflicts." A conflict occurs when two transactions access the same data item and at least one of them writes to it.
- 2 Schedules are conflict equivalent if:
	- each txn order of operations is the asme
	- every pair of conflicting actions of two txns are ordered in the same way
- A schedule S is conflict serializable if S is conflict equivalent to some serial schedule 
- Bad schedule creates anomalies

**Conflict Graph:**
- Nodes are TXNs, edge from 2 nodes if actions in T<sub>i</sub> -> T<sub>j</sub> precede and conflict with any actions in T<sub>j</sub> 
- If graph is acyclic, it's conflict serializable. Cyclic graphs are not conflict serializable

**DAGs and Topological Orderings:**
- Topological ordering is a linear ordering of it's a directed graph that respects all directed edges
- DAGs have one or more topological orderings

Database techniques and algorithms:
- Write-ahead logging
- Serializability
- 2-Phase locking
- Indexing
- Bloom filters
- Caching

### Anomaly Terminologies:
Not allowed by serializability
- Lost Updates (write-write conflict)
	- Two users update same row at the same time and one of their commits is lost forever
- Dirty Reads (write-read conflict)
	- User reads an uncommitted update. TXN 1 updates and aborts but TXN 2 reads update though it's uncommitted
- Unrepeatable Reads (read-write conflict)
	- User reads two different values for same row within same transaction
	- TXN 1 reads A = 0, TXN 2 updates A = 1 and commits, TXN 1 reads A=1
- Phantom Reads (read-write conflict)
	- Users read same set of rows twice, but new rows are inserted/removed to/from by another committed TXN in between

### 2PL: A Simple Locking Algorithm
It's a way to deal with concurrency that guarantees conflict serializability and straightforward to implement and transparent to the user
- Strict 2PL Protocol:
	- An exclusive lock `X` on the object before **writing**
		- If a TXN holds this, no other TXN can get any lock on that object
	- A shared lock `S` on the object before **reading** 
		- If a TXN holds this, no other TXN can get an `X` lock on that object
	- All locks held by a TXN are released when it completes
![[Pasted image 20260302153920.png]]
2PL: A transaction cannot req additional locks once it releases any locks. There is a **growing** phase, followed by a **shrinking** phase
Strict 2PL: Release locks right after COMMIT (COMMIT Record flushed) or ABORT
- If schedule follows strict 2PL, it's conflict serializable 
- It produces a subset of all conflict serializable schedules

**Waits-for Graph:**
- Track which transactions are waiting. Different from conflict graphs

**Deadlocks:** Cycle of transactions waiting for locks to be released by each other. Can be dealt with by preventing and detecting it

**Conservative 2 Phase Locking (C2PL):**
- Deadlock prevention principle
- Obtains all locks before the TXN begins. If all cannot be obtained, release and retry
- Ensure no deadlocks occurs but it can degrade performance

**Wait-Die/ Wound-Wait:**
- Deadlock prevention principle
- Working:
	- Transactions assigned an ID based on start time that determines priority
	- **T<sub>x</sub>  wants lock that T<sub>y</sub> holds**, we have two deadlock avoidance algorithms
	1. Wait-Die (non-preemptive)
		- If T<sub>x</sub> has higher priority, T<sub>x</sub> waits for T<sub>y</sub>. Else T<sub>x</sub>  aborts
	2. Wound-wait (preemptive)
		- If T<sub>x</sub> has higher priority, T<sub>y</sub> aborts. Else T<sub>x</sub>  waits
	- In both cases, TXNs start again later with similar priority

**Deadlock Detection:**
- Create waits for graph
- Nodes are transactions
- Edges from T<sub>x</sub>  to T<sub>y</sub> if T<sub>x</sub> is waiting for T<sub>y</sub> to release a lock
- Check for and break cycles in the waits-for graph periodically
- (To look at: Waits-for graph)

Locking allows only conflict serializable schedules. If the schedule completes, it may deadlock
Serializability is unfortunately rare

**Read Committed:** prevents "dirty reads" (reading uncommitted, intermediate data from other transactions)
**Repeated Reads:** Stronger than read committed but still allows lost updates and phantom reads

Serializability Limitations:
- No constraints on equivalent transaction orders
- No relationship between final serial order and when transactions were submitted
- 2 DBs given identical set of transactions in same order at same time may produce different results

**Strict Serializability:**
- Even stronger than conflict serializability
- If T<sub>y</sub> starts after T<sub>x</sub> commits (X and Y are not concurrent)
	- Final state is equivalent to serial order
	- X must have been executed before Y in that serial order
## Indexing
- Used to speed up access to desired data
- **Search Key:** Attribute used to look up records in a file
- **Index:** Consists of records (index entries) of the form `search-key | pointer` 
- Index is typically smaller than original data
- To figure the size of pointer, take the size of data and figure out how many bits need to represent it
- Index usually stored in memory. Index has to be small since memory capacity is limited

**Index Evaluation Metrics:** Access time, Insertion time, Update time, Deletion time, Space overhead

**Ordered Indices:**
- In ordered index, index entries sorted on the search key
- Primary Index:
	- Sequentially ordered file, index who's search key specifies sequential order of the file
	- Search key of primary index is usually (but not necessarily) the primary key
- Secondary Index:
	- **Secondary Index:** An index on a field that is not the primary key. Allows fast searches on non-key attributes. 
	- Can be dense or sparse, but typically requires more storage since multiple records may share the same value.

**Dense Index:** An index entry for every record in the data file. Provides direct lookup but uses more storage space.

**Sparse Index:** Index entries for only some records (e.g., first record of each block). Uses less storage but requires a secondary search within the block to find the exact record.

**Multilevel Index:**  
- An index on the index itself, creating a hierarchy of index levels. 
- The first level indexes the data file, the second level indexes the first-level index, and so on. 
- Reduces search time by narrowing down the search space at each level, similar to how a book's table of contents works

**Index Update: Insertion**
**Single-Level Index Insertion:**
1. **Dense Index:**
    - Perform lookup on search-key value of new record
    - If search-key doesn't exist in index, insert it
    - Indices maintained as sequential files
    - Must create space for new entry (may require overflow blocks)
2. **Sparse Index:**
    - Index stores one entry per data block
    - No index change needed unless a new block is created
    - If new block created, insert the first search-key value from that block into index
**Multilevel Index Insertion:**
- Uses simple extensions of single-level insertion algorithms
- Insertion cascades through multiple levels as needed


## Bloom Filter
> *Way to approximately determine if an object exists*

Does Object exists $\rightarrow$ Bloom Filter $\rightarrow$ Yes (maybe) / No (I'm certain)
**Parameters:** Array of `m` bits (array initialized to 0), `k` independent functions (h<sub>1</sub>, h<sub>2</sub>,..., h<sub>k</sub>) that return a number between 1 to m 
It can have false positives but no false negatives
**Hash Function:**  $h(x) = y$ 
Where x is an input and y is a uniformly distributed random number
- If $h(x_1) = h(x_2)$, then maybe $x_1 = x_2$
- If $h(x_1) != h(x_2)$, then we know for sure that $x_1 !=x_2$ 
![[Pasted image 20260303135555.png]]
The size of k is a trade off

Optimal k:
$$
k = \frac{m}{n}*\ln{2}
$$
m - number of bits
n - total number of unique objects

Large Bloom Filter:
- Reduces false positives -> fewer reads to disk -> lower latency and high throughput
- **However** 
- More space in memory -> less space for caching database index entries in memory -> higher chance of going to disk -> higher latency and lower throughput

**Issues with Bloom Filter:**
- Can get depleted over time
- Number of unique entries need to be estimated in advance
- Doesn't support deletes

Improvements: counting bloom filters, cuckoo filters, learned bloom filters, elastic bloom filters


## Caching
- Used for reducing work/transport duplication
- Big data systems even use dedicated cache servers (key-value caches)
- Data stored in memoery (doesn't use disk)
- Checks key-value cache before going to database

Important Caching Questions:
- Assignment - where you put the data? how to optimize speed
- Consistency - what happens when data is updated? 
	- passive: cache entry expires
	- active cache entry invalidated
- Eviction - who do you kick out? (cache space is expensive)

**Standard Eviction Policy Model:**
- Cache has fixed size (C) (each item has same size and a unique key)
- Cache is filled
- Requests come in. If
	- Req of item in cache, cache read
	- Req of item not in cahce, fetched from storage and another item is evicted to make room for it
- Eviction policy decides what to remove when new item is inserted

Success Metric:
- Usually latency, sometimes throughput

### Eviction Policies
1. FIFO (First in First Out)
	- Evict the item last inserted into the cache (last inserted is the oldest so least relevant)
	- Easy to implement and can be implemented as a **list**
	- Might work because the cache last is old and not read anymore
	- Might not work because an old item is popular and is not read anymore
	- Usually suffers from a low hit rate
2. LRU (Least Recently Used)
	- Evict the object that was last accessed from cache
	- Uses list
	- Idea: recently accessed item is most likely accessed again in the future
	- Eviction works reasonably well for many workloads
	- Can be expensive to implement as we need to also track the last access time of each item and find item with oldest access time
	- In practice, many caches use approximations of LRU
		- Sampled LRU:
			- Instead of maintaining full ranking of all items, everytime a new item is accessed simply update it's access time
			- At eviction time, sample N items and pick the one with oldest access time
			- With large enough N, it approximates LRU very well
3. CLOCK (approximates LRU)
	- Maintain 1 bit per item in cache (0 or 1 as bit value)
	- When item is accessed set bit to 1
	- Use clock hand that decrements bits to 0 one by one
	- At eviction time, evict next item that has bit 0
	- Originally used to cache memory pages in OS
4. LFU (Least frequently used)
	- Evict object that was accessed the fewest total time from cache
	- Idea: Items that were accessed many times more likely to be accessed again in the future
	- Works well for workloads that have relatively static number of popular items
	- Doesn't work well if new items become popular as it can be difficult to implement.
	- Need to track item frequency for all keys and fine one with lowest frequency
5. LRU/LFU Combinations:
	- LRU prioritizes recent access, LFU frequently accessed items
	- Recency and freq are both important features, each works better for different workloads
	- Segemented LRU: (hybrid)
		- 1st time object inserted into cach, insert into the middle of cache (not head)
		- At every access insert it to the top of cache
		- Behaves like LRU but penalizes objects that might be accessed recently but only once
6. OPT (Belady's algorithm)
	- Assumes it knows the future
	- Evicts pages that won't be accessed for the longest time in the future
	- Can't be used in practice as it needs to know the future
	- Can be used to evaluate how far LRU/LFU etc are from the optimal for a given workload.
	- Recetnly been used to train machine learning eviction policies

Eviction policies in the real world:
- All are used by real systems (Except OPT)
- LRU and approximations most common
- Complications:
	- Assumes items are all the same size
	- Things are more complicated when items have different sizes
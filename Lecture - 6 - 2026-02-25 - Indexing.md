- Networking Layers
- Partitioning
- Replication
- Distributed file systems
- Distributed databases and distributed transactions
- Two phase commits (2PC)


### TCP/IP 5-Layer Model
1. Physical (Layer 1 / L1) 
	- Converts bits to physical signals, medium determines speed, range and latency
	- Types of cables - Twisted Pair (4 pairs and 8 conductors), Coaxial Cable (Single Copper Core + Shielding), Fiber Optic (Light Pulses - km scale)
	- Wireless Spectrum -(longer range, lower freq) 2.4 GHz, 5 GHz, 5G sub-6, 5G mmWave (short range, higher freq)
	- Ethernet Spans L1 (signaling) + L2 (framing). Layer 1 is protocol-agnostic. Only moves signals. Broken cable/Weak signal kills everything above it regardless of software 
2. Data Link (Layer 2 / L2)
	- Organizes bits into frames. Handles local network delivery. MAC addresses identify hardware
	- MAC Address: 48-bit hardware identifier. Local only. Resolves local delivery
	- Switches uses MAC table, routers do not. MAC table used to identify devices
	- ARP - Address Resolution Protocol. Bridges MAC (L2) and IP (L3)
	- DHCP - Dynamic Host Configuration. Discover, Offer, Request, Acknowledge. It bootstraps new devices automatically
	- ARP and DHCP are critical infrastructure and both have well-known attacks
3. Network
	- Routes packets across multiple networks using IP addresses. Global internet is possible because of this
	- IP structure `xxxx.xxxx.xxxx.xxxx` - 32 bit address. Logical and hierarchical. Encode location. No router has complete map. Each makes a local best-hop decision
	- BGP - Autonomous Systems & Routing. Routes ISP to the webpage? It is trust based. Mistakes redirect global traffic. The trust based mosel is the greatest vulnerability
4. Transport
	- End to end delivery between applications. Ports multiplex streams
	- QUIC (RFC 9000 / HTTP/3) Runs over UDP reimplements reliability. Integrates TLS 1.3 (0-RTT vs TCP+TLS's 2 round trips). NO head-of-line blocking. Survives WiFi -> 5G handoff without dropping connections. Used by big tech in a major fraction of traffic
	- TCP - 3 way handshake
	- TCP (HTTP, SSH, SMTP, FTP) vs UDP (DNS, Zoom, Games)
	- QUIC is the modern attempt to get TCP-level guarantees with UDP-level latency
5. Application
	- Where devs work. Protocols define the language two speak over a network
	- HTTPS = HTTP + TLS Encryption
	- SMTP (Port 25/587)
	- SSH (Port 22)
	- FTP (Port 21/22)
	- DNS (Port 53 UDP)
	- Application protocol define message format, verbs and session rules

## Distributed Systems

### Storage Parallelism
Partitions relations on multiple disk on multiple nodes (PCs) to reduce data retrieval time.
**Horizontal Partitioning:**
- Tuples of relation are divided among many nodes
- r1(A,B,C,D) on node 1. r2(A,B,C,D) on node 2
**Vertical Partitioning:**
- Columns divide among nodes into tables
- table(A,B,C,D) is split into table1(A,B) on node 1, table2(A,C,D) on node 2

Follow horizontal paritioning by default

**Partitioning Techniques:** (number of nodes = n)
- Round Robin:
	- Send the ith tuple inserted in the relation to node i in mod n
- Hash partitioning 
	- Choose one or more attribute as the partitioning attributes
	- Choose hash function h with range 0,...n-1
	- i -> result of hash function h applied to partitioning atttribute value of a tuple. 
	- Send tuple to node i
- Range Partitioning
	- Choose an attribute as the partitioning attribute
	- Vector = `[v0, v1,,,,vn-1`] is chosen
	- Let v be partitioning attribute value of a tuple
	- v<sub>i</sub> <= v<sub>i+1</sub> go to node i+1
	- v < v<sub>0</sub> go to node 0 and tuples with v >= v<sub>n-2</sub> go to node n-1

- **Full scan**: read every tuple in the entire relation
- **Point query**: find all tuples where an attribute equals an exact value (e.g., `r.A = 25`)
- **Range query**: find all tuples where an attribute falls between two values (e.g., `10 ≤ r.A < 25`)

**Skew**

Two types:

- **Data-distribution skew**: some nodes get far more tuples
    - Attribute-value skew: many tuples share the same partitioning value, all land on one node (affects range and hash)
    - Partition skew: bad range vector design (affects range partitioning mainly)
- **Execution skew**: some tuples are accessed far more often regardless of how data is distributed (affects all three methods)

Fix for data skew in range partitioning: periodically rebalance the partition vector (can be expensive).

**Round Robin**
- Tuples distributed by insertion order: tuple i goes to node i mod n
- Full scan: excellent, all nodes have roughly equal tuples, work is perfectly balanced
- Point queries: must query ALL nodes (no idea which node holds a given key)
- Range queries: must query ALL nodes (same reason)
- Pros: simplest to implement, minimal metadata (just a counter), most even distribution
- Cons: useless for targeted lookups

**Hash Partitioning**

- A hash function on a chosen attribute determines which node gets the tuple
- Full scan: good, distribution is roughly even (slightly less even than round robin)
- Point queries: excellent — hash the key, go directly to the right node
- Range queries: must query ALL nodes (hashing destroys ordering)
- Pros: fast point lookups, no insertion counter needed
- Cons: still bad for range queries

**Range Partitioning**

- A partitioning vector divides attribute values into ranges, each range assigned to a node
- Full scan: works, but load balance depends on how well the vector was chosen
- Point queries: good — consult partition table, go to one node
- Range queries: excellent — only 1 to a few nodes need to be accessed; others are free for other queries
- Pros: great for range queries and transactions that touch similar key ranges (popular in ACID distributed DBs)
- Cons:
    - Needs a centralized master/proxy to store the partition table (single point of failure, extra hop)
    - Prone to skew if the partition vector is poorly chosen or data is unevenly distributed

**Replication**

- Goal: availability despite failures
- Data replicated at 2-3 nodes (3 is common for fault tolerance)
- Unit of replication is typically a partition (tablet)
- Failed node requests are automatically routed to a replica
- Replicas can be within a data center (handles machine/rack failures) or across data centers (handles power outages, natural disasters, network partitions)

**Keeping Replicas Consistent**

- Master replica scheme: all updates go to master, then propagate to others; reads from master
- If master fails, another node must take over (requires a protocol)
- Two approaches: Two-Phase Commit (2PC) or Consensus protocols

**Distributed File Systems (HDFS/GFS)**

- Architecture: one master (NameNode) + many chunk/data servers
- NameNode maps filenames to Block IDs, and Block IDs to DataNodes
- DataNodes store the actual data and return it to clients
- Blocks are typically 64 MB, replicated on 3 DataNodes
- NameNode is NOT on the critical path of reads/writes (client talks directly to DataNode)
- HDFS is write-once, append-only (not ACID)

**HDFS Limitations**

- NameNode is a single point of failure and a bottleneck
- Keeps directory info in memory, so memory limits number of files
- No in-place updates; suited for append-only workloads

**Distributed Databases and Transactions**

- Approach 1 (Shared-nothing): partition tables across many single-server DBs; application routes requests; scales well but not transparent (app must be shard-aware)
- Approach 2 (Distributed Transactions): support global transactions spanning multiple servers

**Global vs. Local Transactions**

- Local: access data at one server only
- Global: access/update data at more than one server
- Each server has a local transaction manager and a transaction coordinator
- Key rule: a transaction must commit at ALL servers or abort at ALL servers

**Two-Phase Commit (2PC)**
- Used to ensure atomicity across servers
- Assumes fail-stop model (failed nodes stop, don't send bad messages)

**Phase 1 - Prepare:**
- Coordinator sends "prepare T" to all participants
- Each participant tries to acquire locks; if it can commit, it writes `<ready T>` to log, flushes all records, and replies "ready"
- If it cannot commit, it writes `<no T>` and sends "abort T"

**Phase 2 - Decision:**

- If all participants replied "ready," coordinator writes `<commit T>` to log and notifies all
- If any replied "abort," coordinator writes `<abort T>` and notifies all
- Participants then commit or undo locally based on the decision

**Handling Failures in 2PC**

**Server failure:** On recovery, check the log:

- `<commit T>` found: redo the transaction
- `<abort T>` found: undo the transaction
- `<ready T>` found: ask the coordinator what was decided
- No record: coordinator must abort T; server undoes T

**Coordinator failure:** Participants check their own logs:

- Someone has `<commit T>`: commit
- Someone has `<abort T>`: abort
- Someone has no `<ready T>`: safe to abort
- All have `<ready T>` but no decision: must wait for coordinator to recover -- this is the **blocking problem**

**Network partition:** Servers in coordinator's partition follow normal protocol; others treat coordinator as failed and wait

**The Blocking Problem**

- If the coordinator fails after all participants are "ready" but before recording a decision, everyone is stuck
- Fix: use a **consensus protocol** (Paxos, Raft) to replicate the coordinator's state across multiple nodes
- As long as a majority of consensus nodes are alive, a decision can be reached without waiting for recovery
- The **three-phase commit (3PC)** is an extension of 2PC that avoids blocking under certain assumptions
## Network Topology
- Bus - used for utility, gas connections
- Ring - few cables
- Mesh - lot of redundancy
- Star - lot of centralization
- <span style="background:#fff88f">Tree</span> - generally used in data centers (hybridization of tree)
- Hybrid - combination of multiple models

Multipath routing:
internet -> core layer -> aggregation - > access layer (bottom most)

Switch Locations:
- Top of Rack
	- first router after compute node
	- all connected to end of row router
- End of Rack
- Core Rack

**Power Usage Effectiveness (PUE):**
$$
\text{PUE Ratio} = \frac{\text{Total facility power}}{\text{Server or Network Power}}
$$
- Early data centers - 1.7 - 2.0 ratio = Inefficient 
- Now in 2024 - 1.15 (only 15% from optimal)
- Some can be 1.04. 
- Power is usually 25% of monthly operating cost, it is also one of the limiting factor in how large the data center can be
- Better power distribution - fewer transformers
- Liquid cooling for cooling

**Backup Power:**
- Massive amount of batteries to tolerate short glitches in power (until backup generators start back up)
- Glitches could occur due to natural disastrs
- Fuel replenishment 

**Energy Sources:**
- Data centers are being powered by renewable energy

**Fault tolerance:**
- Redundancy is solution to tolerate faults incase things break
- Make independent copies of all data
- Multiple independant network connections
- Copies of every service -> releases, update cycles, resource use

**Potential failures for a new data center:**
- hard drives failures, individual machine failures, blips for dns
- router failures, reloads
- network maintenances
- racks go wonky, failures
- network rewiring
- rack-move
- PDU (power delivery unit) failure, overheating

**Comparing AI datacenters to traditional ones:**
- **Similarity**
	- Same rack/row topology
	- Cooling still a big problem (GPU immersion cooling)
- Differences:
	- Compute: AI centers need thousands of GPUs, small ratio of CPUs/GPUs
	- Memory: Don't need as much traditional CPU memory, reauires lots of on-GPU high bandwidth networking both within a server and aceoss servers
	- Network: needs more demanding network requirements. Requires high bandwidth both within and across servers

**Where to build:**
- Plentiful, inexpensive electricity
- Good network connections
- Inexpensive land
- Geographically near users
- Available labor pool
- Politics

**General application guidance**
- organizer jobs and tasks into tiers
- localizers and colocates
- minimizes bandwidth usage
- uses right models (pubsub, RPCs, batch workflows)
- respects quotas
- provides telemetry for obeservation and adjustment
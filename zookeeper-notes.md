# Notes on Zookeeper

### Overview
- Zookeeper aims to provide coordination services to distributed systems. 
- ZooKeeper provides simple kernel (a filesystem API!) for empowering the clients to build more complex coordination primitives.
- A user of Zookeeper is a "client" and "server" denotes a service providing the Zookeeper service

### Terminology
  - <b>znodes:</b> denotes an in-memory data node (similar to the filesystem inode) in the ZooKeeper. znodes are organized in a  hierarchical namespace referred to as the data tree.
  - Two types of znodes
      - Regular:  clients create and deletes these nodes explicitly
      - Ephemeral: clients creates these nodes but either deletes explicitly OR lets the system delete them automatically when the client's session terminates. 


### Using ZooKeeper to implement coordination primitives
1. <b>Configuration Management:</b> The configuration is stored in a znode, zc. Processes start up with the full pathname of zc. 
Starting processes obtain their configuration by reading zc with the watch flag set to true. If the configuration in zc is ever updated, 
the processes are notified and read the new configuration, again setting the watch flag to true.

2. <b>Rendezvous:</b> When the master starts it fills in zr with information about addresses and ports it is using. When workers start, they read
zr with watch set to true. If zr has not been filled in yet, the worker waits to be notified when zr is updated.

3. <b>Group Membership:</b> A znode, zg, is created to represent the group. When a process member of the group starts, it creates an ephemeral 
child znode under zg. If the process fails or ends, the znode that represents it under zg is automatically removed. Processes may put 
process information in the data of the child znode, e.g., addresses and ports used by the process. Processes may obtain group information 
by simply listing the children of zg. If a process wants to monitor changes in group membership, the process can set the watch flag to 
true and refresh the group information (always setting the watch flag to true) when change notifications are received.

4. <b>Simple locks:</b> To acquire a lock, a client tries to create the designated znode with the EPHEMERAL flag. If the create succeeds, the 
client holds the lock. Otherwise, the client can read the znode with the watch flag set. A client releases the lock explicitly or it is 
removed by timeout if it dies. Other clients that are waiting for a lock try again to acquire a lock once they observe the znode being 
deleted.

### [Client API](https://zookeeper.apache.org/doc/r3.4.6/api/org/apache/zookeeper/ZooKeeper.html)
- Main class of ZooKeeper client library. To use a ZooKeeper service, an application must first instantiate an object of ZooKeeper class. All the iterations will be done by calling the methods of ZooKeeper class. The methods of this class are thread-safe unless otherwise noted.
- Once a connection to a server is established, a session ID is assigned to the client. The client will send heart beats to the server periodically to keep the session valid.
- All methods in the API have both a synchronous and an asynchronous version.

  - create(path, data, flags)
  - delete(path, version)  // operation is conditional on version (if provided)
  - exists(path, watch)
  - getData(path, watch)
  - setData(path, data, version) // operation is conditional on version (if provided)
  - getChildren(path, watch)
  - sync(path)

### Architecture and Internals


### Drawbacks
1. How data are partitioned across multiple machines? Complete replication in memory. (yes this is limiting)
2. How does update happen (interaction across machines)? All updates flow through the master and are considered complete when a
quorum confirms the update.
3. How does read happen (is getting a stale copy possible) ? Reads go to any member of the cluster. Yes, stale copies can be
returned. Typically, these are very fresh, however.
4. What is the responsibility of a leader? To assign serial id's to all updates and confirm that a quorum has received the update.
  There are several limitations that stand out in this architecture:
    - complete replication limits the total size of data that can be managed using Zookeeper. This is acceptable in some applications,    not acceptable in others. In the original domain of Zookeeper (management of configuration and status), it isn't an issue, but zookeeper is good enough to encourage creative misuse where this can become a problem.
    - serializing all updates through a single leader can be a performance bottleneck. On the other hand, it is possible to push 50K updates per second through a leader and the associated quorum so this limit is pretty high.
    - the data storage model is completely non relational.
5. Zookeeper scales well with increase in read operations, but does not with increase in write operations. 
6. Zookeeper also does not scale with more Zookeeper replicas added. To alleviate this observer replicas are used, but they are limited in operation, and do not allow/benefit write operations. 
7. Finally, due to very large latencies involved ZooKeeper cannot handle across the WAN deployment of ZooKeeper servers.


### References
1. [](http://www.corejavaguru.com/bigdata/zookeeper/internals)
2. [](http://highscalability.com/zookeeper-reliable-scalable-distributed-coordination-system)

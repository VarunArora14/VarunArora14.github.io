---
title: "MongoDB notes"
date: 2024-07-08T22:26:12+05:30
draft: false
tags: ["MongoDB", "NoSQL"]
---

### WiredTiger

WiredTiger is a **high-performance**, open-source storage engine used by MongoDB to **store and manage data**.

WiredTiger is designed to support **high concurrency** and **high throughput workloads** while maintaining **low latency and high reliability**. It is optimized for modern hardware architectures, including solid-state drives (SSDs), multi-core processors, and large memory configurations.

Key features of WiredTiger:

- **Document-level concurrency control**: Allows **multiple operations on the same document** to be processed concurrently, improving performance and reducing lock contention.

- **Compression**: WiredTiger supports both snappy and zlib compression, reducing the storage footprint of data on disk.

- **Memory management**: WiredTiger manages memory usage dynamically, optimizing memory allocation based on workload and available resources.

- **Transactional support**: WiredTiger provides support for **ACID** (Atomicity, Consistency, Isolation, Durability) transactions, enabling safe concurrent access to data.

- **Checkpointing**: WiredTiger periodically writes data to disk in the background, reducing the time required for **recovery** after a crash.

---

### MongoDB config file explained

Example Config -

```yml
systemLog:
  destination: file
  path: "/var/log/mongodb/mongod.log"
  logAppend: true
storage:
  journal:
    enabled: true
processManagement:
  fork: true
net:
  bindIp: 127.0.0.1
  port: 27017
setParameter:
  enableLocalhostAuthBypass: false
```

- `systemLog` contains configuration info such as **path** to log file, should logs be **appended** or added each time.
  `destination: file` means logs must be added to `file` rather than `syslog` or `console` options.

- `processManagement` configuration option in MongoDB controls how the mongod or mongos process is managed. One of suboptions is `fork` which controls whether process should run in **background** or **foreground**.
- `storage` is another option which has suboption like `dbpath` where we specify the db path specifically. `journal` configuration controls whether MongoDB uses a write-ahead log, or `journal`, to ensure data consistency in the event of a crash or other failure.
  `journal` - A sequential, binary **transaction log used to bring the database into a valid state in the event of a hard shutdown**. Journaling **writes data first to the journal and then to the core data files**.
  When journal is set to **true**, MongoDB will write all changes to the database to the journal file before they are written to the data files. **This ensures that if a crash or other failure occurs, MongoDB can recover the most recent consistent state of the database by replaying the changes in the journal.**
  If set to **false** it does not use a write-ahead log, and changes to the database may be lost in the event of a crash or other failure. It can give better performance which comes at a cost.
- `security` is another option which allows to enable `authorization` and we can insert `keyfile` for `SCRAM` authentication where all nodes have same copy of keyfile for authentication.
- `net` is used for network-related settings for the database instance, including the network interfaces and ports that the instance should use. `bindIp` **specifies the network interfaces that MongoDB should listen on**. if you set bindIp to 127.0.0.1,192.168.0.100, MongoDB will bind to the loopback interface (127.0.0.1) as well as the interface with IP address 192.168.0.100.

---

## MongoDB Leader election

## Reason for Odd Number of nodes

Having an odd number of nodes in a replica set ensures that there is always a **majority** of **nodes** available to **elect** a new primary node. In a replica set with an even number of nodes, a **split-brain scenario** can occur if the nodes are equally divided, where **each set of nodes considers itself to be the primary and continues to accept write operations independently**. This can result in **data inconsistency** and **corruption**.

## Split Brain Scenario

It occurs when nodes are equally divided into 2 or more groups and each group **thinks** it is **primary**. This can happen when there is a **network partition**, where the nodes in **one group cannot communicate with the nodes in another group**.

In a split-brain scenario, both groups of nodes can independently process write operations, leading to data inconsistencies and conflicts when the network partition is resolved. Each group of nodes can continue to write data and make changes to the database, which can result in data being lost or overwritten.

To prevent this, **voting mechanisms** are used to elect primary node in replica set.Each node in the replica set has one vote, and the node with the majority of votes becomes the primary node.

## Arbiter Node

Arbiter node is a **special** type of node in a replica set that **does not store any data but participates in elections to determine the primary node**.
An arbiter node is a **lightweight member of the replica set** that participates in elections to ensure that there is always a **majority of nodes available** to elect a new primary node.
**Note -** Arbiter does not provide additional redundancy or fault tolerance for replica set.

Max number of arbiters can be **7** but it is not recommended more than **at max 1 or 2**. Adding many arbiters can **reduce the fault tolerance**.

**Fault tolerance** is helpful as if **primary fails, a secondary can takeover**. Adding **non-data bearer** nodes is not helpful for failovers.

## Criteria of Voting

The **arbiter** or any **replica set member** will vote for the node that it believes has the most recent data, and whose state is "up" or "recovering" .
The primary node will also vote for itself.

## Need of Voting

Voting in MongoDB is used to **elect a primary node** in a replica set when the current primary node fails or becomes unavailable.
Voting is needed in MongoDB to ensure that there is always a majority of nodes available to elect a new primary node. This is important to avoid **split-brain** scenarios, where the nodes in a replica set are equally divided, and each set of nodes considers itself to be the primary and continues to accept write operations independently. This can result in data inconsistency and corruption.

Voting process -

- A node detects that the primary node is unavailable or has failed. This can be due to network issues, hardware failures, or other issues.

- The node starts a new election by sending a message to all other nodes in the replica set.

- Each node checks if it is eligible to become a primary node. To be eligible, **a node must be able to communicate with a majority of the other nodes in the replica set**.

- If a node is eligible, it casts a vote for itself and sends a message to all other nodes in the replica set indicating that it has voted for itself.

- Once a node receives a majority of votes, it becomes the new primary node. The other nodes in the replica set then acknowledge the new primary node and update their configuration accordingly.

It's important to note that MongoDB uses a variant of the Raft consensus algorithm for elections, which ensures that only **one node becomes the primary node** and that all nodes in the replica set eventually agree on the current primary node.
Refer here for voting => https://stackoverflow.com/questions/15597372/voting-in-mongodb

---

## MongoDB on Kubernetes

## Resource

https://www.linkedin.com/pulse/run-mongodb-amazon-elastic-kubernetes-service-eks-using-kubedb-/?trk=pulse-article_more-articles_related-content-card

All scaling, replica sets in kubernetes using this => https://github.com/mongodb/mongodb-kubernetes-operator

## Blog

https://www.opcito.com/blogs/ways-to-host-a-mongodb-cluster-on-kubernetes

---

## Mongosh Commands

- `show dbs` - Shows all the databases
- `use db_name` - Creates a new database **db_name** which will be viewed only with `show dbs` when it has data in it.
- `show collections` - It shows the collections of current database.

- `db.collection_name.insertOne({data})` - It is used to insert `data` which is a json object is collection named `collection_name` and this is a single document by use of `insertOne()`

- `db.collection_name.insertOne([{first_document}, {second_document}])` - is used to insert multiple documents.

- `db.users.find()` - list all the documents

- `db.users.find({name: "username"})` - Finds all documents with name as **username**

- `db.users.find.limit(count)` - in `users` collection print all the documents of max count `count`

- `db.users.find().sort({column1: 1, column2: -1})` - Column1 sorts in ascending and column2 in descending

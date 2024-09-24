---
title: "MongoDB vs DocumentDB"
date: 2024-09-13T22:26:12+05:30
draft: false
tags: ["MongoDB", "NoSQL", "DocumentDB", "AWS"]
---

**MongoDB** is also known as **DocumentDB** or **NoSQL** database.
The reason it is called **documentDB** is it stores and retrieves data in the form of semi-structured or unstructured documents

# DocumentDB

**DocumentDB** is AWS offering for **NoSQL** database which stores data in **semi-structured data as documents** than traditional relational data with fixed schema.
It provides **high scalability, high availability and security and works well for applications that require LOW latency and HIGH throughput** for read/write operations.

**DocumentDB** uses **JSON** whereas **MongoDB** uses **BSON** format which is **binary format**.
**JSON** is **Javascript Object Notation** and **BSON** is **Binary Javascript Object Notation**.
JSON is **slower** than BSON whereas JSON uses **lesser space** than BSON.
BSON supports more data types like **data, timestamp etc** which are not in JSON.

# MongoDB vs DocumentDB

- **MongoDB uses BSON whereas DocumentDB uses JSON**.
- DocumentDB is AWS offering and cloud based which has it's infrastructure, scalability and security been provided by the service provider whereas infra, scalability and security has to be setup for MongoDB
- MongoDB is **more flexible** in terms of **schema** and supports **complex querying and indexing**, while DocumentDB provides **automatic indexing** and **simpler data model with fewer querying capabilities**.

## Document

Document is **basic unit of data storage and retrieval**. It is similar to row of relational DB.
A document is a JSON-like data structure that consists of a set of key-value pairs, where the keys are strings and the values can be of any **BSON** data type, including other documents or arrays. Example

```json
{
  "_id": ObjectId("6155f5ee0e0d5d5e5cda8a2b"),
  "name": "John Smith",
  "age": 30,
  "email": "john.smith@example.com",
  "address": {
    "street": "123 Main St",
    "city": "New York",
    "state": "NY",
    "zip": "10001"
  }
}
```

## No ACID in MongoDB

MongoDB does not provide full ACID compliance.
The updates to data are not **immediately consistent** which means that updates to the database are not guaranteed to be immediately consistent across all nodes in the cluster.

In MongoDB **updates to single document are atomic**, meaning that they are either completely executed or not executed at all. But **updates to multiple documents in a transaction are not guaranteed to be atomic or consistent**.
This means that in a **distributed system with multiple nodes, there can be a delay between updates on different nodes, resulting in eventual consistency rather than immediate consistency**.

The focus of MongoDB is of **replication** and **sharding** which enables **horizontal and vertical scaling** making **availability as priority over consistency**.

MongoDB's approach to **replication** and **sharding**, which enables horizontal scalability, is focused on **availability and partition tolerance rather than consistency**. This means that MongoDB prioritizes availability of the system over strict consistency, which is a trade-off made to **achieve greater scalability and performance**.

## ACID in DocumentDB

DocumentDB follows **MVCC(Multi-version Concurrency Control)** and **quorum-based replication model**.

With MVCC we ensure **consistency** as each document has it's **unique version identifier** which is updated each time a transaction modifies the document. DocumentDB creates new version of document and assigns it a new version identifier.
This new version is made **visible to other transactions** while previous versions are stored as snapshots for **read only mode**.

For **Isolation**, DocumentDB uses **Locking** mechanisms that prevent multiple transactions from modifying same document at same time. When a transaction attempts to modify a document currently locked, it puts it in wait state till released.

For **durability**, DocumentDB uses **multiple replicas** of data across multiple availability zones. When transaction commits, it writes to **primary replica** which then replicates to secondary replicas. If primary fails, one of secondary promoted.

**Qorum based** replication model ensures **changes to documents are replicated to multiple nodes in cluster before transactions commited**. It provides high availability and durability as even if one node fails, other can take over to make it consistent.

Additionally, DocumentDB provides strong consistency guarantees for operations that involve multiple documents or indexes through the use of **distributed transactions**. This allows **complex transactions involving multiple documents or indexes to be treated as a single atomic operation** with ACID guarantees.

## Locking

Locking is a mechnaism to ensure **data consistency** and **prevent conflicts between concurrent transactions accessing the same data**. Locking is necessary because **multiple transactions can access the same data simultaneously, and if one transaction changes the data while another transaction is reading or writing it, the result can be inconsistent or incorrect**.

Locking can occur at different levels -

- database level
- table level
- row level
- page level

### Locking Problems

- May not free from recoverability.
- Not free from deadlock.
- Not free from starvation.
- Not free from cascading rollback.

### Pages in Database

**Database pages are the internal basic structure to organize the data in the database files**
Their size are in power **2^i** where **i** is any whole number.

A page is a fixed-size block of contiguous data that is used as the unit of data storage and retrieval. Pages are typically organized in a hierarchical structure, with higher-level structures such as tables, indexes, and partitions consisting of multiple pages. **Document** is same as a page in **MongoDB** and max size can be **16mb**.
We design to data model and document structure in a way that minimizes the size of individual documents and optimizes them for efficient querying and indexing.

In addition to document size, MongoDB also has a concept of "**chunks**," which are **units of data used for sharding and distribution of data across multiple nodes in a MongoDB cluster**. The size of a chunk is configurable and depends on the storage engine and other factors, but typically ranges from 64 MB to 1 GB. **Chunks are continuous range of data that is distributed across multiple nodes in a sharded cluster**.

Chunks in MongoDB are determined by the **shard key**, which is a unique identifier that is used to **partition data into logical ranges**.

**When a new piece of data is added to a sharded collection, MongoDB determines which chunk the data belongs to based on the shard key value. If the data falls outside the range of existing chunks, MongoDB automatically splits the affected chunk into two or more smaller chunks, each containing a subset of the data**.
By default, MongoDB uses a chunk size of 64 megabytes (MB), but this can be customized based on the specific requirements of the application and the hardware configuration of the cluster.
The number of chunks in a sharded collection can grow or shrink dynamically over time as data is added, modified, or deleted.

**Chunks in MongoDB are contiguous ranges of data that are distributed across multiple nodes in a sharded cluster. They are determined by the shard key, and their size is controlled by the balancer process.**

# Collections

**A Collection is a group of Documents**.
A collection is similar to table in relational DB but **far more flexible. Collections do not enforce a schema, and documents in the same collection can have different fields.**.

Each collection is associated with one MongoDB database. To show which collections are in a particular database, use the command `listCollections`.

## SCRAM

**Salted Challenge Response Authentication Mechanism** is a password-based mutual authentication protocol designed to make an eavesdropping attack (i.e. man-in-the-middle) more difficult.
Using cryptographic hashing techniques, a client can prove to a server that the user knows a secret derived from the user’s password without sending the password itself.

In MongoDB we use **OpenSSL** to generate 741 bit hash with using base64 encoding. This keyfile must be in all nodes to communicate with authentication enabled.

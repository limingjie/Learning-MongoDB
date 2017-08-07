# Learning MongoDB

## Chapter 1. Introduction

### Ease of Use

- MongoDB is a **document-oriented database**, not a relational one. The primary reason for moving away from the relational model is to make scaling out easier, but there are some other advantages as well.
- A document-oriented database replaces the concept of a “row” with a more flexible model, the “document.” By allowing **embedded documents and arrays**, the document-oriented approach makes it possible to **represent complex hierarchical relationships with a single record**. This fits naturally into the way developers in modern **object-oriented languages** think about their data.
- There are also **no predefined schemas**: a document’s keys and values are not of fixed types or sizes. Without a fixed schema, adding or removing fields as needed becomes easier. Generally, this makes development faster as developers can quickly iterate. It is also easier to experiment. Developers can try dozens of models for the data and then choose the best one to pursue.

### Designed to Scale

- Data set sizes for applications are growing at an incredible pace. Increases in available bandwidth and cheap storage have created an environment where even small-scale applications need to store more data than many databases were meant to handle. A terabyte of data, once an unheard-of amount of information, is now commonplace.
- As the amount of data that developers need to store grows, developers face a difficult decision: how should they scale their databases? Scaling a database comes down to the choice between **scaling up (getting a bigger machine) or scaling out (partitioning data across more machines)**. Scaling up is often the path of least resistance, but it has drawbacks: large machines are often very expensive and, eventually, a physical limit is reached where a more powerful machine cannot be purchased at any cost. The alternative is to scale out: to add storage space or increase throughput for read and write operations, buy additional servers and add them to your cluster. This is both cheaper and more scalable; however, it is more difficult to administer a thousand machines than it is to care for one.
- **MongoDB was designed to scale out**. The document-oriented data model makes it easier to split data across multiple servers. **MongoDB automatically takes care of balancing data and load across a cluster, redistributing documents automatically and routing reads and writes to the correct machines**. [Link to Come] The topology of a MongoDB cluster or whether, in fact, there is a cluster versus a single node at the other end of a database connection is transparent to the application. This allows developers to focus on programming the application, not scaling it. Likewise if the topology of an existing deployment needs to change in order to, for example, scale to support greater load, the application logic can remain the same.

### Rich with Features...

- MongoDB is a general-purpose database, so aside from **creating, reading, updating, and deleting data**, it provides most of the features you would expect from a DBMS and many others that set it apart:
- **Indexing**
  - MongoDB supports generic secondary indexes and provides unique, compound, geospatial, and full-text indexing capabilities as well. Secondary indexes on hierarchical structures such as nested documents and arrays are also supported and enable developers to take full advantage of the ability to model in ways that best suit their applications.
- **Aggregation**
  - MongoDB provides an aggregation framework based on the concept of data processing pipelines. Aggregation pipelines allow you to build complex analytics engines by processing data through a series of relatively simple stages on the server side and with the full advantage of database optimizations.
- **Special collection and index types**
  - MongoDB supports time-to-live (TTL) collections for data that should expire at a certain time, such as sessions and fixed-size (capped) collections, for holding recent data, such as logs. MongoDB also supports partial indexes limited to only those documents matching a criteria filter in order to increase efficiency and reduce the amount of storage space required.
- **File storage**
  - MongoDB supports an easy-to-use protocol for storing large files and file metadata.
- Some features common to relational databases are not present in MongoDB, notably complex multirow transactions. MongoDB also only supports joins in a very limited way through use of the $lookup aggregation operator introduced the 3.2 release. MongoDB’s treatment of multirow transactions and joins were architectural decisions to allow for greater scalability, because both of those features are difficult to provide efficiently in a distributed system.

## Chapter 2. Getting Started

MongoDB is powerful but easy to get started with. In this chapter we’ll introduce some of the basic concepts of MongoDB:

- A **document** is the basic unit of data for MongoDB and is roughly equivalent to **a row in a relational database management system** (but much more expressive).
- Similarly, a **collection** can be thought of as **a table with a dynamic schema**.
- **A single instance of MongoDB can host multiple independent databases, each of which can have its own collections**.
- **Every document has a special key, "_id", that is unique within a collection**.
- MongoDB is distributed with a simple but powerful tool called the **mongo shell**. The mongo shell provides built-in support for **administering MongoDB instances** and **manipulating data using the MongoDB query language**. It is also **a fully-functional JavaScript interpreter** which enables users to create and load their own scripts for a variety of purposes.

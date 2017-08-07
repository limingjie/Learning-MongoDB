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

### Documents

Document: an ordered set of keys with associated values.

- The keys in a document are strings. **Any UTF-8 character is allowed** in a key, with a few notable **exceptions**:
  - Keys must not contain the **character \0 (the null character)**. This character is used to signify the end of a key.
  - **The . and $ characters** have some special properties and should be used only in certain circumstances, as described in later chapters. In general, they should be considered reserved, and drivers will complain if they are used inappropriately.
- MongoDB is **type-sensitive** and **case-sensitive**, for **both key and values**.
- A final important thing to note is that documents in MongoDB **cannot contain duplicate keys**.
- TODO ? - Key/value pairs in documents are ordered: {"x" : 1, "y" : 2} is not the same as {"y" : 2, "x" : 1}. Field order does not usually matter and you should not design your schema to depend on a certain ordering of fields (MongoDB may reorder them). This text will note the special cases where field order is important.
- TODO ? - In some programming languages the default representation of a document does not even maintain ordering (e.g., dictionaries in Python and hashes in Perl or Ruby 1.8). Drivers for those languages usually have some mechanism for specifying documents with ordering, when necessary.

### Collections

A collection is a group of documents. If a document is the MongoDB analog of a row in a relational database, then a collection can be thought of as the analog to a table.

#### Dynamic Schemas

Collections have dynamic schemas. This means that the documents within a single collection can have any number of different “shapes.” For example, both of the following documents could be stored in a single collection:

```json
{"greeting" : "Hello, world!", "views": 3}
{"signoff": "Good night, and good luck"}
```

#### Naming

A collection is identified by its name. Collection names can be any UTF-8 string, with a few restrictions:

- The **empty string ("")** is not a valid collection name.
- Collection names may not contain **the character \0 (the null character)** because this delineates the end of a collection name.
- You should not create any collections that **start with system.**, a prefix reserved for internal collections. For example, the system.users collection contains the database’s users, and the system.namespaces collection contains information about all of the database’s collections.
- User-created collections should not contain the **reserved character $** in the name. The various drivers available for the database do support using $ in collection names because some system-generated collections contain it. You should not use $ in a name unless you are accessing one of these collections.

##### Subcollections

One convention for organizing collections is to use namespaced subcollections separated by the . character. For example, an application containing a blog might have a collection named blog.posts and a separate collection named blog.authors. This is for organizational purposes only—there is no relationship between the blog collection (it doesn’t even have to exist) and its “children.”

### Databases

In addition to grouping documents by collection, MongoDB groups collections into databases. A single instance of MongoDB can host several databases, each grouping together zero or more collections. A database has its own permissions, and each database is stored in separate files on disk. A good rule of thumb is to store all data for a single application in the same database. Separate databases are useful when storing data for several application or users on the same MongoDB server.

Like collections, databases are identified by name. Database names can be any UTF-8 string, with the following restrictions:

- The empty string ("") is not a valid database name.
- A database name cannot contain any of these characters: /, \, ., ", *, <, >, :, |, ?, $, (a single space), or \0 (the null character). Basically, stick with alphanumeric ASCII.
- Database names are case-sensitive, even on non-case-sensitive filesystems. To keep things simple, try to just use lowercase characters.
- Database names are limited to a maximum of 64 bytes.

One thing to remember about database names is that they will actually end up as files on your filesystem. This explains why many of the previous restrictions exist in the first place.

There are also several reserved database names, which you can access but which have special semantics. These are as follows:

- *admin*

  The admin database plays a role in authentication and authorization. In addition, access to this database is required for some administrative operations. See [Link to Come] for more information about the admin database.

- *local*

  This database stores data specific to a single server. In replica sets, local stores data used in the replication process. The local database itself is never replicated. See [Link to Come] for more information about replication and the local database).

- *config*

  Sharded MongoDB clusters (see [Link to Come]), use the config database to store information about each shard.

By concatenating a database name with a collection in that database you can get a fully qualified collection name called a namespace. For instance, if you are using the blog.posts collection in the cms database, the namespace of that collection would be cms.blog.posts. Namespaces are limited to 121 bytes in length and, in practice, should be fewer than 100 bytes long. For more on namespaces and the internal representation of collections in MongoDB, see [Link to Come].

# Learning MongoDB

<!-- TOC depthFrom:2 -->

- [Chapter 1. Introduction](#chapter-1-introduction)
    - [Ease of Use](#ease-of-use)
    - [Designed to Scale](#designed-to-scale)
    - [Rich with Features...](#rich-with-features)
- [Chapter 2. Getting Started](#chapter-2-getting-started)
    - [Documents](#documents)
    - [Collections](#collections)
        - [Dynamic Schemas](#dynamic-schemas)
        - [Naming](#naming)
            - [Subcollections](#subcollections)
    - [Databases](#databases)
    - [Getting and Starting MongoDB](#getting-and-starting-mongodb)
    - [Introduction to the MongoDB Shell](#introduction-to-the-mongodb-shell)
        - [Running the Shell](#running-the-shell)
        - [A MongoDB Client](#a-mongodb-client)
        - [Basic Operations with the Shell](#basic-operations-with-the-shell)
            - [Create](#create)
            - [Read](#read)
            - [Update](#update)
            - [Delete](#delete)
    - [Data Types](#data-types)
        - [Basic Data Types](#basic-data-types)
        - [Dates](#dates)
        - [Arrays](#arrays)
        - [Embedded Documents](#embedded-documents)
        - [_id and ObjectIds](#_id-and-objectids)
            - [ObjectIds](#objectids)
            - [Autogeneration of _id](#autogeneration-of-_id)
    - [Using the MongoDB Shell](#using-the-mongodb-shell)
        - [Tips for Using the Shell](#tips-for-using-the-shell)
        - [Running Scripts with the Shell](#running-scripts-with-the-shell)
        - [Creating a .mongorc.js](#creating-a-mongorcjs)
        - [Customizing Your Prompt](#customizing-your-prompt)
        - [Editing Complex Variables](#editing-complex-variables)
        - [Inconvenient Collection Names](#inconvenient-collection-names)
- [Chapter 3. Creating, Updating, and Deleting Documents](#chapter-3-creating-updating-and-deleting-documents)
    - [Inserting Documents](#inserting-documents)
        - [insertMany()](#insertmany)
        - [Insert Validation](#insert-validation)
        - [insert()](#insert)

<!-- /TOC -->

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

### Getting and Starting MongoDB

On Windows

- When run with no arguments, mongod will use the default data directory, /data/db/ (or \data\db\ on the current volume on Windows). If the data directory does not already exist or is not writable, the server will fail to start. It is important to create the data directory (e.g., mkdir -p /data/db/) and to make sure your user has permission to write to the directory before starting MongoDB.

- You can safely stop mongod by typing Ctrl-C in the shell that is running the server.

```ShellSession
# If the data directory does not already exist or is not writable, the server will fail to start.
C:\Users\mingjli>mkdir c:\data\db
C:\Users\mingjli>mongod
2017-08-07T19:18:16.728+0800 I CONTROL  [initandlisten] MongoDB starting : pid=11144 port=27017 dbpath=C:\data\db\ 64-bit host=MINGJLI-CN
2017-08-07T19:18:16.730+0800 I CONTROL  [initandlisten] targetMinOS: Windows 7/Windows Server 2008 R2
2017-08-07T19:18:16.731+0800 I CONTROL  [initandlisten] db version v3.4.6
2017-08-07T19:18:16.731+0800 I CONTROL  [initandlisten] git version: c55eb86ef46ee7aede3b1e2a5d184a7df4bfb5b5
2017-08-07T19:18:16.731+0800 I CONTROL  [initandlisten] OpenSSL version: OpenSSL 1.0.1u-fips  22 Sep 2016
2017-08-07T19:18:16.731+0800 I CONTROL  [initandlisten] allocator: tcmalloc
2017-08-07T19:18:16.731+0800 I CONTROL  [initandlisten] modules: none
2017-08-07T19:18:16.731+0800 I CONTROL  [initandlisten] build environment:
2017-08-07T19:18:16.731+0800 I CONTROL  [initandlisten]     distmod: 2008plus-ssl
2017-08-07T19:18:16.732+0800 I CONTROL  [initandlisten]     distarch: x86_64
2017-08-07T19:18:16.732+0800 I CONTROL  [initandlisten]     target_arch: x86_64
2017-08-07T19:18:16.733+0800 I CONTROL  [initandlisten] options: {}
2017-08-07T19:18:16.738+0800 I STORAGE  [initandlisten] wiredtiger_open config: create,cache_size=7627M,session_max=20000,eviction=(threads_min=4,threads_max=4),config_base=false,statistics=(fast),log=(enabled=true,archive=true,path=journal,compressor=snappy),file_manager=(close_idle_time=100000),checkpoint=(wait=60,log_size=2GB),statistics_log=(wait=0),
2017-08-07T19:18:16.820+0800 I CONTROL  [initandlisten]
2017-08-07T19:18:16.821+0800 I CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
2017-08-07T19:18:16.821+0800 I CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
2017-08-07T19:18:16.822+0800 I CONTROL  [initandlisten]
2017-08-07T19:18:21.163+0800 I FTDC     [initandlisten] Initializing full-time diagnostic data capture with directory 'C:/data/db/diagnostic.data'
2017-08-07T19:18:21.192+0800 I INDEX    [initandlisten] build index on: admin.system.version properties: { v: 2, key: {version: 1 }, name: "incompatible_with_version_32", ns: "admin.system.version" }2017-08-07T19:18:21.192+0800 I INDEX    [initandlisten]          building index using bulk method; build may temporarily use up to 500 megabytes of RAM
2017-08-07T19:18:21.199+0800 I INDEX    [initandlisten] build index done.  scanned 0 total records. 0 secs
2017-08-07T19:18:21.204+0800 I COMMAND  [initandlisten] setting featureCompatibilityVersion to 3.4
2017-08-07T19:18:21.206+0800 I NETWORK  [thread1] waiting for connections on port 27017
```

### Introduction to the MongoDB Shell

MongoDB comes with a JavaScript shell that allows interaction with a MongoDB instance from the command line. The shell is useful for performing administrative functions, inspecting a running instance, or just exploring MongoDB. The mongo shell is a crucial tool for using MongoDB. We use the mongo shell extensively throughout the rest of the text.

#### Running the Shell

```mongoshell
$ mongo
MongoDB shell version v3.4.6
connecting to: mongodb://127.0.0.1:27017
MongoDB server version: 3.4.6
Welcome to the MongoDB shell.
For interactive help, type "help".
For more comprehensive documentation, see
        http://docs.mongodb.org/
Questions? Try the support group
        http://groups.google.com/group/mongodb-user
Server has startup warnings:
2017-08-07T19:18:16.820+0800 I CONTROL  [initandlisten]
2017-08-07T19:18:16.821+0800 I CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
2017-08-07T19:18:16.821+0800 I CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
2017-08-07T19:18:16.822+0800 I CONTROL  [initandlisten]
> x=200
200
> x/5;
40
> x/5
40
> Math.sin(Math.PI/2)
1
> new Date("2010/1/1")
ISODate("2009-12-31T16:00:00Z")
> "Hello world!".replace("world", "whatever")
Hello whatever!
> function factorial(n) {
... if (n <= 1) return 1;
... return n * factorial(n-1);
... }
> factorial(5)
120
>
```

#### A MongoDB Client

```mongoshell
> db
test
> use video
switched to db video
> db
video
>
```

#### Basic Operations with the Shell

We can use the four basic operations, create, read, update, and delete (CRUD) to manipulate and view data in the shell.

##### Create

The **insertOne** function adds a document to a collection.

```mongoshell
> movie = {"title" : "Star Wars: Episode IV - A New Hope",
... "director" : "George Lucas",
... "year" : 1977}
{
        "title" : "Star Wars: Episode IV - A New Hope",
        "director" : "George Lucas",
        "year" : 1977
}
> db.movies.insertOne(movie)
{
        "acknowledged" : true,
        "insertedId" : ObjectId("5988534267642c6d7d3f5358")
}
> db.movies.find()
{ "_id" : ObjectId("5988534267642c6d7d3f5358"), "title" : "Star Wars: Episode IV - A New Hope", "director" : "George Lucas", "year" : 1977 }
> show dbs
admin  0.000GB
local  0.000GB
video  0.000GB
> show collections
movies
>
```

##### Read

**find** and **findOne** can be used to query a collection.

```mongoshell
> db.movies.findOne()
{
        "_id" : ObjectId("5988534267642c6d7d3f5358"),
        "title" : "Star Wars: Episode IV - A New Hope",
        "director" : "George Lucas",
        "year" : 1977
}
> db.movies.findOne({'title': /^Star/})
{
        "_id" : ObjectId("5988534267642c6d7d3f5358"),
        "title" : "Star Wars: Episode IV - A New Hope",
        "director" : "George Lucas",
        "year" : 1977
}
> db.movies.findOne({'title': /^Avatar/})
null
>
```

##### Update

If we would like to modify our post, we can use **updateOne**. updateOne takes (at least) two parameters: the first is the criteria to find which document to update, and the second is the new document.

```mongoshell
> db.movies.update({title : "Star Wars: Episode IV - A New Hope"},
... {$set : {reviews: []}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.movies.find()
{ "_id" : ObjectId("5988534267642c6d7d3f5358"), "title" : "Star Wars: Episode IV - A New Hope", "director" : "George Lucas", "year" : 1977, "reviews" : [ ] }
> db.movies.findOne()
{
        "_id" : ObjectId("5988534267642c6d7d3f5358"),
        "title" : "Star Wars: Episode IV - A New Hope",
        "director" : "George Lucas",
        "year" : 1977,
        "reviews" : [ ]
}
>
```

##### Delete

**deleteOne** and **deleteMany** permanently delete documents from the database. Both methods take filter document specifying criteria for removal. For example, this would remove the movie we just created:

Use **deleteMany** to delete all documents matching a filter.

```mongoshell
> db.movies.deleteOne({title : "Star Wars"})
{ "acknowledged" : true, "deletedCount" : 0 }
> db.movies.deleteOne({title : "Star Wars: Episode IV - A New Hope"})
{ "acknowledged" : true, "deletedCount" : 1 }
> db.movies.count()
0
>
```

### Data Types

#### Basic Data Types

JSON types: null, boolean, numeric, string, array, and object.

- JSON has no date type.
- There is no way to differentiate floats and integers, never mind any distinction between 32-bit and 64-bit numbers.
- No regular expressions.
- No function.

MongoDB adds support for a number of additional data types while keeping JSON’s essential key/value pair nature.

The most common types are:

- null, `{"x" : null}`
- boolean, `{"x" : true}`
- number
  - The shell defaults to using 64-bit floating point numbers.
    - `{"x" : 3.14}`
    - `{"x" : 3}`
  - For integers, use the NumberInt or NumberLong classes, which represent 4-byte or 8-byte signed integers, respectively.
    - `{"x" : NumberInt("3")}`
    - `{"x" : NumberLong("3")}`
- string, any string of UTF-8 characters, `{"x" : "foobar"}`
- date, MongoDB stores dates as 64-bit integers representing milliseconds since the Unix epoch (January 1, 1970). The time zone is not stored: `{"x" : new Date()}`
- regular expression, JavaScript regular expression, `{"x" : /foobar/i}`
- array, `{"x" : ["a", "b", "c"]}`
- embedded document, `{"x" : {"foo" : "bar"}}`
- object id, 12-byte ID for documents, `{"x" : ObjectId()}`

There are also a few less common types that you may need, including:

- binary data, binary data is a string of arbitrary bytes. It cannot be manipulated from the shell. Binary data is the only way to save non-UTF-8 strings to the database.
- code, `{"x" : function() { /* ... */ }}`

#### Dates

When creating a new Date object, **always call new Date(...)**, not just Date(...). Calling the constructor as a function (that is, not including new) returns a string representation of the date, not an actual Date object.

Dates in the shell are displayed using local time zone settings. However, dates in the database are just stored as milliseconds since the epoch, so they have no time zone information associated with them. (Time zone information could, of course, be stored as the value for another key.)

#### Arrays

```json
{"things" : ["pie", 3.14]}
```

As we can see from the example, arrays can contain different data types as values (in this case, a string and a floating-point number). In fact, array values can be any of the supported values for normal key/value pairs, even nested arrays.

#### Embedded Documents

Documents can be used as the value for a key.

```json
{
    "name" : "John Doe",
    "address" : {
        "street" : "123 Park Street",
        "city" : "Anytown",
        "state" : "NY"
    }
}
```

#### _id and ObjectIds

- Every document stored in MongoDB must have an "_id" key.
- The "_id" key’s value can be any type, but it defaults to an ObjectId.
- In a single collection, every document must have a unique value for "_id", which ensures that every document in a collection can be uniquely identified.

##### ObjectIds

The ObjectId class is designed to be lightweight, while still being easy to generate in a globally unique way across different machines. MongoDB’s distributed nature is the main reason why it uses ObjectIds as opposed to something more traditional, like an autoincrementing primary key: it is difficult and time-consuming to synchronize autoincrementing primary keys across multiple servers. Because MongoDB was designed to be a distributed database, it was important to be able to generate unique identifiers in a sharded environment.

- a 4-byte value representing the seconds since the Unix epoch,
- a 3-byte machine identifier,
- a 2-byte process id, and
- a 3-byte counter, starting with a random value.

These first nine bytes of an ObjectId guarantee its uniqueness across machines and processes for a single second. The last three bytes are simply an incrementing counter that is responsible for uniqueness within a second in a single process. This allows for up to 2563 (16,777,216) unique ObjectIds to be generated per process in a single second.

##### Autogeneration of _id

As stated previously, if there is no "_id" key present when a document is inserted, one will be automatically added to the inserted document. This can be handled by the MongoDB server but will generally be done by the driver on the client side.

### Using the MongoDB Shell

Connect to MongoDB on a different machine or port.

```ShellSession
$ mongo some-host:30000/myDB
MongoDB shell version: 3.3.5
connecting to: some-host:30000/myDB
>
```

Start without DB and then connect.

```mongoshell
$ mongo --nodb
MongoDB shell version: 3.3.5
> conn = new Mongo("some-host:30000")
connection to some-host:30000
> db = conn.getDB("myDB")
myDB
```

#### Tips for Using the Shell

- DB level help `db.help()`
- Collection level help `db.movies.help()`
- Print the JavaScript source code for the function, enter without parentheses. `db.movies.updateOne`

#### Running Scripts with the Shell

```ShellSession
$ mongo script1.js script2.js script3.js
$ mongo --quiet server:port/database script1.js script2.js script3.js
> load("script1.js")
```

Scripts have access to the db variable (as well as any other globals). However, shell helpers such as "use db" or "show collections" do not work from files. There are valid JavaScript equivalents to each of these, as shown in Table 2-1.

|      Helper      |       Equivalent        |
| ---------------- | ----------------------- |
| use video        | db.getSisterDB("video") |
| show dbs         | db.getMongo().getDBs()  |
| show collections | db.getCollectionNames() |

#### Creating a .mongorc.js

`~/.mongorc.js` file runs whenever you start up the shell. More practically, you can use this script to set up any global variables you’d like to use, alias long names to shorter ones, and override built-in functions. One of the most common uses for `.mongorc.js` is remove some of the more “dangerous” shell helpers. You can override functions like dropDatabase or deleteIndexes with no-ops or undefine them altogether:

```JavaScript
var no = function() {
    print("Not on my watch.");
};

// Prevent dropping databases
db.dropDatabase = DB.prototype.dropDatabase = no;

// Prevent dropping collections
DBCollection.prototype.drop = no;

// Prevent dropping indexes
DBCollection.prototype.dropIndex = no;
```

You can disable loading your .mongorc.js by using the --norc option when starting the shell.

#### Customizing Your Prompt

Read later... Not necessary :)

#### Editing Complex Variables

Read later... Better use script for many commands than shell.

#### Inconvenient Collection Names

Read later... Looks like something that should be avoid.

## Chapter 3. Creating, Updating, and Deleting Documents

### Inserting Documents

#### insertMany()

```mongoshell
> use video
switched to db video
> show collections
movies
> db.movies.find()
> db.movies.insertOne({"title" : "3 idiots"})
{
        "acknowledged" : true,
        "insertedId" : ObjectId("598acf7d704a221947c7b8be")
}
> db.movies.find()
{ "_id" : ObjectId("598acf7d704a221947c7b8be"), "title" : "3 idiots" }
> db.movies.insertMany([{"title" : "Avator"},
...                     {"title" : "Martian"},
...                     {"title" : "Up in the Air"}]);
{
        "acknowledged" : true,
        "insertedIds" : [
                ObjectId("598acfd4704a221947c7b8bf"),
                ObjectId("598acfd4704a221947c7b8c0"),
                ObjectId("598acfd4704a221947c7b8c1")
        ]
}
> db.movies.find()
{ "_id" : ObjectId("598acf7d704a221947c7b8be"), "title" : "3 idiots" }
{ "_id" : ObjectId("598acfd4704a221947c7b8bf"), "title" : "Avatar" }
{ "_id" : ObjectId("598acfd4704a221947c7b8c0"), "title" : "Martian" }
{ "_id" : ObjectId("598acfd4704a221947c7b8c1"), "title" : "Up in the Air" }
>
```

Sending dozens, hundreds, or even thousands of documents at a time can make inserts significantly faster.

Use `insertMany` for bulk/batch insert, currently, MongoDB do not accept message longer than 48MB. Munge data before saving it to MongoDB.

Handling insert error:

- ordered (default), documents inserted by insertion order, insertion stops on error, documents beyond the point will not be inserted.
- unordered, MongoDB will attempt to insert as many as possible.

  ```mongodb
  > db.movies.insertMany([
  ... {"_id" : 3, "title" : "Sixteen Candles"},
  ... {"_id" : 4, "title" : "The Terminator"},
  ... {"_id" : 4, "title" : "The Princess Bride"},
  ... {"_id" : 5, "title" : "Scarface"}],
  ... {"ordered" : false})
  2017-08-09T17:16:48.776+0800 E QUERY    [thread1] BulkWriteError: write error at   item 2 in bulk operation :
  BulkWriteError({
          "writeErrors" : [
                  {
                          "index" : 2,
                          "code" : 11000,
                          "errmsg" : "E11000 duplicate key error collection:   video.movies index: _id_ dup key: { : 4.0 }",
                          "op" : {
                                  "_id" : 4,
                                  "title" : "The Princess Bride"
                          }
                  }
          ],
          "writeConcernErrors" : [ ],
          "nInserted" : 3,
          "nUpserted" : 0,
          "nMatched" : 0,
          "nModified" : 0,
          "nRemoved" : 0,
          "upserted" : [ ]
  })
  BulkWriteError@src/mongo/shell/bulk_api.js:372:48
  BulkWriteResult/this.toError@src/mongo/shell/bulk_api.js:336:24
  Bulk/this.execute@src/mongo/shell/bulk_api.js:1173:1
  DBCollection.prototype.insertMany@src/mongo/shell/crud_api.js:302:5
  @(shell):1:1
  >
  ```

#### Insert Validation

One of the basic structure checks is size: all documents must be smaller than 16 MB.

```mongodb
> db.movies.findOne({"title" : "Avatar"})
{ "_id" : ObjectId("598acfd4704a221947c7b8bf"), "title" : "Avatar" }
> Object.bsonsize(db.movies.findOne({"title" : "Avatar"}))
40
>
```

#### insert()

Backward compatibility for MongoDB prior to 3.0. For consistency CRUD API after 3.0, `insertOne` and `insertMany` are prefered.

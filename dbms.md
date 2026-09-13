# DBMS Chapter 1: Keys & ER Modeling
(Engineering Summary Notes)
Learning Philosophy: Model the business first. The database schema should naturally emerge from the business requirements—not the other way around.
1. Keys
Super Key

A Super Key is any set of attributes that can uniquely identify a row.

Examples:

{id}
{email}
{username}
{id, email}
{id, username}

A Super Key may contain unnecessary attributes.

Candidate Key

A Candidate Key is the smallest possible Super Key.

Removing any attribute should make it unable to uniquely identify a row.

Examples:

id
email
username

if each is individually unique.

Primary Key

A Primary Key is the Candidate Key chosen to identify every row.

A good Primary Key should be:

Unique
Non-null
Stable (doesn't change)
Simple
URL Shortener
Users

id ← Primary Key
username
email
password

We selected id instead of email or username because usernames/emails may change while id remains constant.

Foreign Key

A Foreign Key represents a relationship between two tables.

It stores the Primary Key of another table.

Example
Users

id

↓

Links

created_by

created_by stores Users.id.

It does not refer to Links.id.

The attribute name doesn't matter.

Only the referenced Primary Key matters.

2. Foreign Key Management

If the referenced row is deleted, different business rules are possible.

Examples:

CASCADE
SET NULL
RESTRICT
URL Shortener Decision

Deleting a user should not delete shortened URLs.

Reason:

Shortened URLs may still be shared publicly and continue serving traffic even after the account is deleted.

Business requirements decide the deletion behavior.

3. ER Modeling

ER Modeling is used to understand the business before designing tables.

Never start by thinking about SQL.

Always start by understanding the product.

4. Entity

An Entity is a real-world object that exists independently.

URL Shortener Entities
Users
Links
QR Codes
Clicks
Teams

Rule:

Ask:

"What things exist in my product?"

5. Attributes

Attributes describe an Entity.

Example:

Users

id
username
email
password

Ask:

"What information do I know about this entity?"

6. Relationship

A Relationship explains how Entities are connected.

Examples:

User

creates

Link
Link

has

QR Code

Never think about tables here.

Only think about business.

7. Cardinality

Cardinality answers:

How many?

Always ask both questions.

One A

↓

How many B?

and

One B

↓

How many A?
Types
One-to-One

Example:

Link

↓

QR Code

One Link → One QR Code

One QR Code → One Link

One-to-Many

Example:

User

↓

Links

One User

↓

Many Links

One Link

↓

One User

Many-to-Many

Example:

Users

↓

Teams

One User

↓

Many Teams

One Team

↓

Many Users

8. Participation

Participation answers:

Can this entity exist without the relationship?

Never ask:

"Will users do this?"

Instead ask:

"Can this row exist independently?"

Partial Participation

Entity can exist without the relationship.

Example:

A User can register without creating any Links.

User

Partial Participation
Total Participation

Entity cannot exist without the relationship.

Example:

A QR Code cannot exist without a Link.

QR Code

Total Participation
9. Strong Entity

A Strong Entity has its own identity.

It can be uniquely identified independently.

Examples:

Users

user_id
Links

link_id

Rule:

Ask:

Can this entity identify itself?

If YES → Strong Entity.

10. Weak Entity

A Weak Entity cannot identify itself.

It depends on another Entity.

Example:

Link

↓

Versions

Version Number:

1
2
3

Question:

Version 3 of which Link?

Identity becomes:

Link ID

+

Version Number

Therefore Version is a Weak Entity.

11. Converting ER Model to Tables
Rule 1

Every Entity

↓

becomes

↓

One Table

Rule 2

Primary Keys come from the Entity's identity.

Rule 3

Relationships create Foreign Keys.

One-to-Many

Foreign Key goes on the Many side.

Example:

Users

↓

Links

↓

Links

created_by

Reason:

The "many" side grows indefinitely.

Storing millions of Link IDs inside one User row is not scalable.

One-to-One

Foreign Key can exist on either side.

Business requirements determine the better choice.

Many-to-Many

Cannot be represented directly.

Create a Junction Table.

Example:

Users

↓

TeamMembers

↓

Teams
TeamMembers

user_id

team_id

Relationship attributes like

role

Owner

Editor

Viewer

belong inside the Junction Table.

Engineering Thinking (Most Important)

Never design databases by thinking:

Tables

↓

Foreign Keys

Instead think:

Product Requirements

↓

Entities

↓

Attributes

↓

Relationships

↓

Cardinality

↓

Participation

↓

Strong / Weak Entities

↓

ER Diagram

↓

Tables

↓

Primary Keys

↓

Foreign Keys

## Important definitions: 
1) A file system stores and manages data as files and directories, while a DBMS provides a structured way to store, retrieve, update, and manage related data using concepts such as schemas, constraints, transactions, concurrency control, and querying.

2) An ER model is a conceptual representation of a database that identifies entities, their attributes, the relationships between entities, and constraints such as cardinality and participation before the database is implemented.

3) Cardinality specifies the number of instances of one entity that can be associated with instances of another entity through a relationship.
4) A strong entity is an entity that has its own key attribute and can be uniquely identified independently of another entity.
5) A weak entity is an entity that cannot be uniquely identified by its own attributes alone and depends on another entity, called the owner or strong entity, for its identification.
6) A candidate key is a minimal set of attributes that uniquely identifies each row in a relation.
7) A super key is any set of one or more attributes that uniquely identifies a row.
8) A primary key is the candidate key selected by the database designer to uniquely identify rows in a table. It must be unique and cannot contain NULL values. The choice among candidate keys can depend on the application's requirements and design considerations.

9) Normalization is the process of structuring a database to reduce unnecessary redundancy and prevent insertion, deletion, and update anomalies while maintaining data integrity.
10) A relation is in 1NF if every attribute contains atomic values, meaning each cell contains a single indivisible value and there are no repeating groups or multi-valued attributes.
11) A relation is in 2NF if it is already in 1NF and every non-prime attribute is fully functionally dependent on the whole candidate key, meaning there is no partial dependency on a proper subset of a composite candidate key.

12) A relation is in 3NF if it is in 2NF and there is no transitive dependency of a non-prime attribute on a candidate key.
13) For every non-trivial functional dependency X → Y, X must be a super key.
14) A join combines rows from two or more tables based on a related condition, allowing us to retrieve data that is distributed across those tables.
15) A transaction is a sequence of one or more database operations that together represent a single logical unit of work.
16) ACID is a set of properties that ensures database transactions are processed reliably and maintain the correctness of data, especially in the presence of failures and concurrent transactions.
17) Atomicity means a transaction is treated as an indivisible unit: either all of its operations are successfully completed, or none of its changes are applied.
18) Consistency means that a transaction takes the database from one valid state to another valid state while preserving all defined data constraints and rules.
19) Isolation means that concurrently executing transactions should not interfere with each other in a way that produces an incorrect result; their effect should be consistent with an appropriate serial execution.
20) Durability means that once a transaction is successfully committed, its changes are permanently preserved and will not be lost even if a system failure occurs afterward.
21) An index is a data structure built on one or more columns that provides an efficient way for the database to locate rows matching a query, avoiding a full table scan when the index is useful.

## Database Indexes — 
1. What is an Index?
An index is an additional data structure created on one or more columns (or expressions).
Its purpose is to help the database locate required rows efficiently instead of scanning the entire table.
The table stores the actual data; the index provides an efficient access path to that data.
An index is not part of the row itself and is separate from the table's actual data storage.
Interview definition

An index is a data structure built on one or more columns that allows the database to locate matching rows efficiently, reducing the need for a full table scan.

2. Why are Indexes Needed?

Without an index:

Query → scan table → examine rows → find matching rows

With a useful index:

Query → search index → locate relevant rows → fetch rows

Therefore, indexes are primarily used to:

Speed up read/query operations
Efficiently locate rows for frequently used search conditions
Support efficient equality and range queries, depending on index type
3. Advantages of Indexes
✅ Faster reads

Queries can avoid scanning the entire table.

✅ Efficient searching

Particularly useful for frequently queried columns.

✅ Efficient sorting/range operations

Certain index types, especially B-Trees, can efficiently support ordered and range-based queries.

✅ Can improve query performance significantly

Especially when the table is large and the query is selective.

4. Disadvantages of Indexes
❌ Extra storage

Indexes require additional disk/storage space.

❌ Slower writes

INSERT, UPDATE, and DELETE may need to update the corresponding indexes.

❌ Maintenance overhead

The database must maintain indexes as table data changes.

❌ Not every index is useful

The database optimizer may decide that using an index is more expensive than scanning the table.

Key trade-off

Indexes improve read performance at the cost of additional storage and write/maintenance overhead.

5. Important: Index ≠ B-Tree

This distinction is very important.

Index
  │
  ├── B-Tree
  ├── Hash
  └── Other index structures/types
Index = the mechanism/access path used to efficiently locate data.
B-Tree = one data structure commonly used to implement an index.
Hash = another possible structure for certain types of lookups.

So don't say:

"An index is a B-Tree."

Say:

"A B-Tree is a common data structure used to implement an index."

## B-Trees — 
6. What is a B-Tree?

A B-Tree is a balanced, multi-way search tree.

Important properties:

Keys are maintained in sorted order
Each node can contain multiple keys
Each node can have many children
The tree remains balanced
Therefore, it is wide and shallow
7. Why B-Trees for Database Indexes?

The biggest reason is storage/page access.

Database data is managed in pages/blocks rather than treating storage like ordinary RAM.

Storage access is much more expensive than operations performed in memory.

Therefore:

The database wants to minimize the number of pages it needs to access.

B-Tree helps because:

Many children per node
        ↓
High branching factor
        ↓
Fewer levels
        ↓
Shallow tree
        ↓
Fewer page accesses
        ↓
Faster search
Core interview answer

B-Trees are well suited for database indexes because they are balanced and have a high branching factor, making them shallow. This reduces the number of storage-page accesses required to locate data.

8. Advantages of B-Trees
✅ Balanced

Search paths remain roughly similar in length.

✅ High branching factor

Nodes can contain many keys/children.

✅ Shallow

High branching factor means fewer levels.

✅ Fewer page accesses

This is the major database-specific advantage.

✅ Keys are sorted

Makes ordered searching possible.

✅ Efficient equality searches

Can efficiently find a specific key.

✅ Efficient range queries

Because keys are ordered, B-Trees are excellent for conditions such as:

<, <=, =, >=, >
BETWEEN
✅ Ordered retrieval

Can efficiently support queries that need data in key order.

9. Disadvantages of B-Trees
❌ Index consumes additional storage

The B-Tree itself requires space.

❌ Write overhead

Insertions, deletions, and updates may require modifying/rebalancing the tree.

❌ More complex than simpler structures

Maintaining balance and structure adds implementation complexity.

❌ Not always the best index type

Different workloads may benefit from different index structures.

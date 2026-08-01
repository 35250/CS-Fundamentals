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

---
title: "Database Systems"
date: 2026-10-06
summary: "Database Systems"
tags: ["Databases"]
---

Entities: objects in the real world.
Entity set: a set of related entities.
Attributes: variables describing an entity; an entity set contains the same set of attributes.
Key: an attribute that uniquely identifies an entity in an entity set.
Relationship: an association between two or more entities.
Relationship set: a set of relationships between entity sets.
Relationship attribute: similar to how entities have attributes, we may also assign relationships attributes.
Instance: a set of relationships.
Key constraint: a restriction on the possible set of relationships in an instance.
SQL and the relational model: SQL attempts to do an engineering's approximation to implement relational databases. In SQL tables correspond to relations, but do not adhere to all the requirements of a relation; a table may contain duplicate values, is explicitly ordered, etc.

Relation: consists of a heading and body; a heading is a set of attributes with a name and data type, a body is a set of tuples where each value in the tuple corresponds to an attribute in the heading.
Constraints: a constraint is a general boolean expression; if this expression is true the database is consistent, otherwise it is inconsistent. We can thus restrict our database by ensuring the database is always consistent.

In general entities are divided into entity sets (for example, a person is part of an employee entity set), and relationships are described using relationship sets between associated entity sets (for example, an employee in an entity set works in a department entity set). We may assign this relationship a relationship attribute by introducing a 'since' variable representing the date which the employee has worked in a given department.
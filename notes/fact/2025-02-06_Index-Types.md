---
date: 2025-02-06
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Index Types

Generally, the index we create in Postgres using regular syntax is a B-tree index. We have introduced its principles in a [[2025-01-27_How-an-Index-Works|previous topic]].

However, Postgres supports several types of indexes, but they are all used for very specific situations, and these situations are also very rare, so in this course we will not introduce how to use them, just knowing their existence is enough.

| Index Type | Description |
|---|---|
| **B-Tree** | General purpose index. 99% of the time you want this. |
| **Hash** | Speeds up simple equality checks. |
| **GiST** | Geometry, full-text search. |
| **SP-GiST** | Clustered data, such as dates - many rows might have the same year. |
| **GIN** | For columns that contain arrays or JSON data. |
| **BRIN** | Specialized for really large datasets. |

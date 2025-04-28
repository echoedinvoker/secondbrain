---
date: 2025-03-26
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 5: CRUD by sqlite3

```sh
 sqlite3 todos.db # enter sqlite3 shell
SQLite version 3.49.1 2025-02-18 13:38:58
Enter ".help" for usage hints.
sqlite> .schema todos # check the schema of the todos table
```
```sql
-- schema of the todos table
CREATE TABLE todos (
        id INTEGER NOT NULL, 
        title VARCHAR, 
        description VARCHAR, 
        priority INTEGER, 
        completed BOOLEAN, 
        PRIMARY KEY (id)
);
CREATE INDEX ix_todos_id ON todos (id);
```
```sql
-- insert some data into the todos table
INSERT INTO todos (title, description, priority, completed) VALUES 
('Buy groceries', 'Get milk, eggs, and bread', 1, 0),
('Finish project report', 'Complete the quarterly project summary', 2, 0),
('Call dentist', 'Schedule annual checkup', 3, 0),
('Plan vacation', 'Research destinations and book flights', 4, 0),
('Clean garage', 'Organize tools and dispose of old items', 5, 0);

SELECT * FROM todos;
```
```sh
1|Buy groceries|Get milk, eggs, and bread|1|0
2|Finish project report|Complete the quarterly project summary|2|0
3|Call dentist|Schedule annual checkup|3|0
4|Plan vacation|Research destinations and book flights|4|0
5|Clean garage|Organize tools and dispose of old items|5|0
```

```sql
-- delete a row with id=5
DELETE FROM todos WHERE id=5;
SELECT * FROM todos;
```
```sh
1|Buy groceries|Get milk, eggs, and bread|1|0
2|Finish project report|Complete the quarterly project summary|2|0
3|Call dentist|Schedule annual checkup|3|0
# row with id=5 is deleted
4|Plan vacation|Research destinations and book flights|4|0
```
```sql
-- insert a new row into the todos table
INSERT INTO todos (title, description, priority, completed) VALUES 
('Clean garage', 'Organize tools and dispose of old items', 5, 0);
SELECT * FROM todos;
```
```sh
1|Buy groceries|Get milk, eggs, and bread|1|0
2|Finish project report|Complete the quarterly project summary|2|0
3|Call dentist|Schedule annual checkup|3|0
4|Plan vacation|Research destinations and book flights|4|0
5|Clean garage|Organize tools and dispose of old items|5|0 # new row, and id=5 is reused
```
```sql
-- delete a row with id=4
DELETE FROM todos WHERE id=4;
SELECT * FROM todos;
```
```sh
1|Buy groceries|Get milk, eggs, and bread|1|0
2|Finish project report|Complete the quarterly project summary|2|0
3|Call dentist|Schedule annual checkup|3|0
# row with id=4 is deleted
5|Clean garage|Organize tools and dispose of old items|5|0
```
```sql
-- insert a new row into the todos table
INSERT INTO todos (title, description, priority, completed) VALUES 
('Plan vacation', 'Research destinations and book flights', 4, 0);
SELECT * FROM todos;
```
```sh
1|Buy groceries|Get milk, eggs, and bread|1|0
2|Finish project report|Complete the quarterly project summary|2|0
3|Call dentist|Schedule annual checkup|3|0
5|Clean garage|Organize tools and dispose of old items|5|0
6|Plan vacation|Research destinations and book flights|4|0 # new row, and id=4 is not reused, it uses the next available id
```
```sql
-- update the priority of a row with id=3
UPDATE todos SET priority = 1 WHERE id = 3;
SELECT * FROM todos;
```
```sh
1|Buy groceries|Get milk, eggs, and bread|1|0
2|Finish project report|Complete the quarterly project summary|2|0
3|Call dentist|Schedule annual checkup|1|0
#                                      ^ updated priority
5|Clean garage|Organize tools and dispose of old items|5|0
6|Plan vacation|Research destinations and book flights|4|0
```


---
date: 2025-03-26
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 6: sqlite3 display modes

SQLite3 provides multiple display modes. We can use the command `.mode` to switch display modes.

1. `ascii`: Default mode, output in simple ASCII table format.
2. `csv`: Comma-separated values format.
3. `column`: Left-aligned column format.
4. `html`: HTML table format.
5. `insert`: Generate SQL insert statements.
6. `line`: One value per line, with column names.
7. `list`: Values on each line separated by a .separator character.
8. `quote`: Similar to `list`, but each value enclosed in quotes.
9. `tabs`: Values separated by tabs.
10. `tcl`: TCL list elements.
11. `box`: Box-shaped output drawn using Unicode characters.
12. `json`: JSON format output.
13. `markdown`: Markdown table format.
14. `table`: Similar to `box`, but using ASCII characters.

For example:

```sh
sqlite> SELECT * FROM todos;
# this will display the table in ASCII format (default mode)
1|Buy groceries|Get milk, eggs, and bread|1|0
2|Finish project report|Complete the quarterly project summary|2|0
3|Call dentist|Schedule annual checkup|1|0
5|Clean garage|Organize tools and dispose of old items|5|0
6|Plan vacation|Research destinations and book flights|4|0
sqlite> .mode json # switch to JSON mode
sqlite> SELECT * FROM todos;
# this will display the table in JSON format
[{"id":1,"title":"Buy groceries","description":"Get milk, eggs, and bread","priority":1,"completed":0},
{"id":2,"title":"Finish project report","description":"Complete the quarterly project summary","priority":2,"completed":0},
{"id":3,"title":"Call dentist","description":"Schedule annual checkup","priority":1,"completed":0},
{"id":5,"title":"Clean garage","description":"Organize tools and dispose of old items","priority":5,"completed":0},
{"id":6,"title":"Plan vacation","description":"Research destinations and book flights","priority":4,"completed":0}]

```


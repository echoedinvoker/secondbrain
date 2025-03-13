---
date: 2025-02-20
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Grouping by Week

Below are three related tables, where `likes` can like both `posts` and `comments`.


![likes-post-n-comment.png](../assets/imgs/likes-post-n-comment.png)

We need to query the above three tables above to answer the following questions:

> For each week, show the number of likes that posts an comments recieved. Use the posts and comment create_at date, not when the like was recieved.

1. We group with week from the very first post and comment.

![group-by-week-from-very-first-date.png](../assets/imgs/group-by-week-from-very-first-date.png)

2. split ids to different groups by their create_at date.

![split-ids-to-week-by-date.png](../assets/imgs/split-ids-to-week-by-date.png)


3. Add the like to ids according to table `likes`.

![add-like-to-ids.png](../assets/imgs/add-like-to-ids.png)


4. convert diagram result to table.

![convert-diagram-to-table.png](../assets/imgs/convert-diagram-to-table.png)




---
date: 2025-01-01
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Where with join

Assuming in our app, users can comment on their own photos, I want to query the photo URL and comment contents for all such occurrences.

![comment-own-photo-diagram.png](../assets/imgs/comment-own-photo-diagram.png)

From the diagram above, it can be seen that the operations in the blue and green parts are simply inner joins, but the operation in the red part is a typical where clause, and it acts on an imaginary table.

```sql
SELECT 
  url, contents
FROM
  photos
JOIN
  comments ON comments.photo_id = photos.id  -- inner join to create a imaginary table
WHERE -- MUST be after the join clause
  comments.user_id = photos.user_id; -- filter rows based on the condition on the imaginary table
                                     -- (which also means this clause is applied after the join)


--- OUTPUT ---
          url           |                                       contents                                       
------------------------+--------------------------------------------------------------------------------------
 http://chet.net        | Non est totam.
 https://kailyn.name    | Fuga et iste beatae.
 http://chet.net        | Nihil quo voluptatem placeat.
 https://alayna.net     | Rerum dolor sunt sint.
 http://marjolaine.name | Culpa perferendis qui perferendis eligendi officia neque ex.
 http://marjolaine.name | Enim doloremque delectus.
 https://santina.net    | Quia dolorem officia explicabo quae.
 https://santina.net    | Tempora quas facere.
 http://chet.net        | Rem autem corporis earum necessitatibus dolores explicabo iste quo.
 https://kailyn.name    | Porro illum perferendis nemo libero voluptatibus vel.
 https://santina.net    | Eum aut magni sint.
 https://santina.net    | Vitae voluptatem voluptates vel nam.
 http://marjolaine.name | Aperiam sunt aliquam illum impedit.
 http://marjolaine.name | Voluptates nihil voluptatum quia maiores dolorum molestias occaecati.
 http://marjolaine.name | Fugit quia perferendis magni doloremque dicta officia dignissimos ut necessitatibus.
 https://kailyn.name    | Ad dignissimos quia aut commodi vel ut nisi.
 https://alayna.net     | Ut nemo voluptas voluptatem voluptas.
 http://chet.net        | Esse voluptatem sed deserunt ipsum eaque maxime rerum qui.
 https://kailyn.name    | Illo atque nihil et quod consequatur neque pariatur delectus.
 https://kailyn.name    | Debitis repellendus inventore omnis est facere aliquam.
 https://kailyn.name    | Minima dolorem reiciendis excepturi culpa sapiente eos deserunt ut.
(21 rows)

```

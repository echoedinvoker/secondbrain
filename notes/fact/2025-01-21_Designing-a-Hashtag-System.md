---
date: 2025-01-21
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Designing a Hashtag System

There are many places using hashtags in our app:

![hashtag-in-many-places.png](../assets/imgs/hashtag-in-many-places.png)

According to previous learning, we can design a hashtag system as follows:

![hashtag-system.png](../assets/imgs/hashtag-system.png)

This is [[2025-01-18_Polymorphic-Associations|polymorphic Associations]] problem, so you can also only use one table to store all the hashtags.

> But the key point is, do we really need to query these hashtags? If we never need to query hashtags, then we don't need to store them in the database at all.

Let's check our app to resolve this problem:

![search-for-hashtag.png](../assets/imgs/search-for-hashtag.png)

As above, we found that in the search function, you can search for hashtags, and clicking on them will display all related posts. However, it is obvious that these hashtags are only found in the captions of posts, and no functionality has been found for hashtags in user profiles or comments.

![usefull-useless-hashtags.png](../assets/imgs/usefull-useless-hashtags.png)

So the hashtag_comments and hashtag_users in the bottom left of the image will never be queried, so they do not need to be stored in the database.

![only-one-hashtag.png](../assets/imgs/only-one-hashtag.png)



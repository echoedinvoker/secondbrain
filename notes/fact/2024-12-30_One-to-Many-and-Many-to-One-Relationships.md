---
date: 2024-12-30
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# One to Many and Many to One Relationships

In [[2024-12-30_Design-a-database|this topic]], we draw resources and their relationships but not to tell what kind of relationship they are.

In fact, there are four types of relationships and we'll discuss two of them in this topic.

![user-own-photos.png](../assets/imgs/user-own-photos.png)

In above figure, we can find that a user posts many photos, which means a user **has many** photos. This is called a **one-to-many** relationship in termnology.

![one-to-many.png](../assets/imgs/one-to-many.png)

But if we change the perspective, we can say a photo belongs to a user, which means a photo **has one** user (it can only has **ONE**). This is called a **many-to-one** relationship.

![many-to-one.png](../assets/imgs/many-to-one.png)

They are describing the same relationship but from different perspectives.

Let's check another example: the photo and its comments page:

![photo-own-comments.png](../assets/imgs/photo-own-comments.png)

Now, you should be able to tell that a photo **has many** comments and a comment **has one** photo. They are **one-to-many** and **many-to-one** relationships respectively.





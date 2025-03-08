---
date: 2025-01-20
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Photo Mentions vs Caption Mentions


## Tagging People in Photos

This topic, we want to talk about the hard feature of [[2025-01-20_Additional-Features-Around-Posts|additional Features Around Posts]] - tagging people in photos. 

We design a new table `tags` as below to implement this feature:

![add-tags-table-first.png](../assets/imgs/add-tags-table-first.png)

However, the above structure does not provide information on which location in the photo the person is tagged, so we need to add additional information:

![location-photo-system.png](../assets/imgs/location-photo-system.png)

![add-extra-info-to-tag.png](../assets/imgs/add-extra-info-to-tag.png)



## Tagging People in Captions

Now, we basically have a system to tag people in photos. But we also need to think about how to tag people in the caption.

![tag-user-in-caption.png](../assets/imgs/tag-user-in-caption.png)

Do we also need to save the tags in the caption in the database? Not necessarily, if these tags are simply for highlighting purposes, they can be implemented from the application side and do not need to be saved in the database.

The key point of whether to save the tags in the caption to the database is whether our app needs these tags for other functions. For example:

**Need to show a list of posts a user was mentioned in?**

**Need to show a list of the most-often mentioned users?**

**Need to notify a user when they are mentioned in a caption?**

When there are these requirements, we need to also store the tags in the caption in the database.



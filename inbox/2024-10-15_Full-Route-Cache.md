---
date: 2024-10-15
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Full Route Cache

$ npm run build
        |
        v
Next finds all the different routes in your app
        |
        |                                       Database
        |                                       ---------
        |                                       snippet 1
        |                                       snippet 2
        |                                       snippet 3
        |        Static or Dynamic?                 ^
        |        if static, Full Route Cache        |
        |        starts its work.                   |
        |        |                                  |
        v        v                                  |
        /  -->  static  -->  Render the 'Home' page |
                                                    |
                               This occurs at *build time*.
                               We use the current data in
                               the database
                                                    |
                                                    v
                                                HTML File
                                                ---------
                                                snippet 1
                                                snippet 2     This exact file will be served to
                                                snippet 3     everyone who navigates to '/'


>ISSUE: Even if we delete or add snippets, this HTML file will not change, but will remain the same as it was when it was originally built.

So when routes are determined to be static, special attention should be paid to the operation of the Full Route Cache. We may need to set it to dynamic or actively update the cache.

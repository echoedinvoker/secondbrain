---
date: 2025-01-15
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# So Where Are We Applying Validation

![where-validation.png](../assets/imgs/where-validation.png)

We need to analyze the benefits of both sides in this issue.


**Validation in Web Server**

- Easier to express more complex validation

Some very complex validations, such as determining whether a stock can be purchased, may require calling other APIs or querying other databases. These validations are easier to implement on a web server. Although PostgreSQL can also achieve this type of validation, it may be more difficult.

- Far easier to apply new validation rules
- Many libraries to handle validation automatically

There are many libraries that can help us verify whether a string is an email or a phone number, which is very convenient.


**Validation in Database**

- Validation still applied even if you connect with a different client

- Guaranteed that validation is always applied

No matter which client connects to the database, validation will be applied. It's very important to ensure data integrity.

- Can only apply new validation rules if all existing rows satisfy it

Although this is troublesome, it is not a disadvantage. It prevents situations where some of your data is valid and some is invalid, which would make your data unreliable and very dangerous.


**Conclusion**

The conclusion is that I will set most of the validation on the web server, only a few critical validations will be placed in the database, especially those that would compromise data integrity.

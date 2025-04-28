---
date: 2025-04-07
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Properly Running Data and Schema Migrations

To resolve the problem of [[2025-04-07_Dangers-Around-Data-Migrations|Dangers Around Data Migrations]], we want to split the steps to different migrations and include some codes updates as below:

1. **Migration#4** - add column `loc

| id | url | lat | lng | loc |
|----|-----|-----|-----|-----|
| 1  | ... | 25  | 121 | null |
| 2  | ... | 22  | 120 | null |
                       ^^^^^^^^ new column `loc` and values `null`
The column `loc` must allow null values in this migration but we'll set it to `NOT NULL` in the future migration.

2. **Code Update**
Update the API to create the new post with both `lat/lng` and `loc` values.
 
| id | url | lat | lng | loc |
|----|-----|-----|-----|-----|
| 1  | ... | 25  | 121 | null |
| 2  | ... | 22  | 120 | null |
| 3  | ... | 32  | 22  | 32, 22 | <-- new post by updated API

3. **Program**
In this step, we need to copy the values of `lat/lng` into `loc`. Data migration doesn't have to be a migration, we can write a small program to complete it.

| id | url | lat | lng | loc |
|----|-----|-----|-----|-----|
| 1  | ... | 25  | 121 | 25, 121 |
                         ^^^^^^^ copy values from `lat/lng`
| 2  | ... | 22  | 120 | 22, 120 |
                         ^^^^^^^ copy values from `lat/lng`
| 3  | ... | 32  | 22  | 32, 22 |
| 4  | ... | 42  | 12  | 42, 12 | <-- new post

4. Code Update
Update the API to create the new post with only `loc` values.

| id | url | lat | lng | loc |
|----|-----|-----|-----|-----|
| 1  | ... | 25  | 121 | 25, 121 |
| 2  | ... | 22  | 120 | 22, 120 |
| 3  | ... | 32  | 22  | 32, 22 |
| 4  | ... | 42  | 12  | 42, 12 |
| 5  | ... | null | null | 52, 2 | <-- new post by updated API


5. **Migration#5** - drop column `lat` and `lng`, set `loc` to `NOT NULL`

| id | url | loc |
|----|-----|-----|
| 1  | ... | 25, 121 |
| 2  | ... | 22, 120 |
| 3  | ... | 32, 22 |
| 4  | ... | 42, 12 |
| 5  | ... | 52, 2 |
            ^^^^^^^ all records have correct `loc` values

In this process, even if there is a long time gap between each adjacent step, it will not cause any issues because each step does not affect the execution of other steps. This design ensures the safety and reliability of data migration.

This design will not have any problems in the vast majority of cases, but there are still a very small number of exceptional cases where this process cannot be used.


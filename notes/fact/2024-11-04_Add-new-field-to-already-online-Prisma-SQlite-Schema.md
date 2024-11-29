---
date: 2024-11-04
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Add new field to already online Prisma SQlite Schema

```prisma
<!--prisma/schema.prisma-->

...

model Notodo {
  id        String @id @default(cuid())
  title     String
  content   String
  userId    String
  activeAt  DateTime?

  weight    Float?   // New field, it should be nullable or have a default value
                     // otherwise it will throw an error when running the migration
                     // because old records will not have a value for this

  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  user      User      @relation(fields: [userId], references: [id], onDelete: Cascade)
  thresholds Threshold[]
  challenges Challenge[]
}

...

}

```

Create a new migration for the new field by running the following command:

```bash
~/D/g/p/notodo > npx prisma migrate dev --name add_nullable_weight_to_notodo
```

It will create a new migration file (.sql) in the `prisma/migrations` directory, you can double check the file content to make sure it's correct.

```sql
-- prisma/migrations/20241104114837_add_nullable_weight_to_notodo/migration.sql 

-- AlterTable
ALTER TABLE "Notodo" ADD COLUMN "weight" REAL;

```

Following command will apply the migration to the database, it will sync the database schema with the Prisma schema:

>This step will actually modify the database schema, so make sure to backup the database before running this command!

```bash
~/D/g/p/notodo > npx prisma migrate deploy
```

Update the Prisma client to reflect the changes in the schema, it will effect TypeScript types and Prisma client methods:

```bash
~/D/g/p/notodo > npx prisma generate
```

---
date: 2025-03-29
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 33: Create revision, upgrade and downgrade the database by it

```sh
$ alembic revision -m "Create phone number for user column" # Create a new revision
  # Generating /home/matt/Projects/fastapi/TodoApp/alembic/versions/aeb6876f4654_c 
  # reate_phone_number_for_user_column.py ...  done
```

Then, we can see a new file in the `alembic/versions` directory.

```sh
 tree
.
├── alembic
│   ├── env.py
│   ├── README
│   ├── script.py.mako
│   └── versions
│       └── aeb6876f4654_create_phone_number_for_user_column.py # New file
│       #   ^^^^^^^^^^^^ id of the revision       
...

```

It'll generate some codes in the file.

```py
# alembic/versions/aeb6876f4654_create_phone_number_for_user_column.py

"""Create phone number for user column

Revision ID: aeb6876f4654
Revises: 
Create Date: 2025-03-29 04:19:05.102614

"""
from typing import Sequence, Union

from alembic import op
#                   ^^ we can use this to run the operations for the database
import sqlalchemy as sa


# revision identifiers, used by Alembic.
revision: str = 'aeb6876f4654'
down_revision: Union[str, None] = None
branch_labels: Union[str, Sequence[str], None] = None
depends_on: Union[str, Sequence[str], None] = None


def upgrade() -> None:
    """Upgrade schema."""
    pass # we can add the code to upgrade the schema here


def downgrade() -> None:
    """Downgrade schema."""
    pass # we can add the code to downgrade the schema here

```

Above codes are the template for the revision. We can add the code to upgrade and downgrade the schema in the `upgrade` and `downgrade` functions.

Now, assume that we want to add a new column `phone_number` to the `users` table.

```py
...

def upgrade() -> None:
    """Upgrade schema."""
    # Add a new column `phone_number` to the `users` table
    op.execute("""ALTER TABLE users
                  ADD COLUMN phone_number VARCHAR(20);""")


def downgrade() -> None:
    """Downgrade schema."""
    # Drop the `phone_number` column from the `users` table
    op.execute("""ALTER TABLE users
                  DROP COLUMN phone_number;""")
```

It's recommended to use the `op.execute` method to run the SQL queries because it'll handle the SQL scripts for different databases.

Now, we can upgrade the database by running the following command.

```sh
$ alembic upgrade aeb6876f4654
#                 ^^^^^^^^^^^^^ id of the revision

# INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
# INFO  [alembic.runtime.migration] Will assume transactional DDL.
# INFO  [alembic.runtime.migration] Running upgrade  -> aeb6876f4654, Create phone number for user column
```

And our application should update the model to include the `phone_number` field as well.

```py
from sqlalchemy import Boolean, Column, Integer, String
from database import Base


class Users(Base):
    __tablename__ = 'users'

    id = Column(Integer, primary_key=True, index=True)
    email = Column(String, unique=True)
    username = Column(String, unique=True)
    first_name = Column(String)
    last_name = Column(String)
    hashed_password = Column(String)
    is_active = Column(Boolean, default=True)
    role = Column(String)
    phone_number = Column(String) # New field to fit the database upgrade

...

```

Then, you can run the application and try to query the `users` table to see if the `phone_number` field is added.

Next, we can downgrade the database by running the following command.

```sh
$ alembic downgrade aeb6876f4654
# INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
# INFO  [alembic.runtime.migration] Will assume transactional DDL.
```

Same, you should update the model to remove the `phone_number` field as well.

```py
...

class Users(Base):
    __tablename__ = 'users'

    id = Column(Integer, primary_key=True, index=True)
    email = Column(String, unique=True)
    username = Column(String, unique=True)
    first_name = Column(String)
    last_name = Column(String)
    hashed_password = Column(String)
    is_active = Column(Boolean, default=True)
    role = Column(String)
    # phone_number = Column(String)  # Remove the field to fit the database downgrade

...

```


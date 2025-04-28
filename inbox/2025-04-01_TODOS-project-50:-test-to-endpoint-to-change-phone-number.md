---
date: 2025-04-01
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 50: test to endpoint to change phone number

Before we write the test, let's check the endpoint to change the phone number.

```py
# TodoApp/routers/users.py

...

@router.put("/phonenumber/{phone_number}", status_code=status.HTTP_204_NO_CONTENT)
async def change_phonenumber(user: user_dependency, db: db_dependency,
                          phone_number: str):
    if user is None:
        raise HTTPException(status_code=401, detail='Authentication Failed')
    user_model = db.query(Users).filter(Users.id == user.get('id')).first()
    user_model.phone_number = phone_number
    db.add(user_model)
    db.commit()
```

Now, we can write the test for it.

```py
# TodoApp/tests/test_users.py

...

def test_change_phonenumber(test_user):
    response = client.put("/user/phonenumber/9876543210")
    assert response.status_code == status.HTTP_204_NO_CONTENT

    # further check if the phone number is updated
    db = TestingSessionLocal()
    user = db.query(Users).first() # because there is only one user in the database, we can simply use .first() to get the user
    assert user.phone_number == '9876543210' # type: ignore

```

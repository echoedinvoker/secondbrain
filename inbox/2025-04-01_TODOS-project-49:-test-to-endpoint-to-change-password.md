---
date: 2025-04-01
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 49: test to endpoint to change password

Before we start, we need to check the endpoint to change the password.

```py
# TodoApp/routers/users.py
...

class UserVerification(BaseModel):
    password: str
    new_password: str = Field(min_length=6)

...

@router.put("/password", status_code=status.HTTP_204_NO_CONTENT)
async def change_password(user: user_dependency, db: db_dependency,
                          user_verification: UserVerification):
    if user is None:
        raise HTTPException(status_code=401, detail='Authentication Failed')
    user_model = db.query(Users).filter(Users.id == user.get('id')).first()

    if not bcrypt_context.verify(user_verification.password, user_model.hashed_password):
        raise HTTPException(status_code=401, detail='Error on password change')
    user_model.hashed_password = bcrypt_context.hash(user_verification.new_password)
    db.add(user_model)
    db.commit()

...

```

Now we can write the test for the endpoint.

```py
# TodoApp/tests/test_users.py

...


def test_change_password(test_user):
    response = client.put("/user/password", json={
        "password": "password",
        "new_password": "new_password"
    })
    assert response.status_code == status.HTTP_204_NO_CONTENT


```

We need to test the edge case that the password is wrong.

```py
def test_change_password_wrong_password(test_user):
    response = client.put("/user/password", json={
        "password": "wrong_password",
        #           ^^^^^^^^^^^^^^^^ replace with wrong password
        "new_password": "new_password"
    })
    assert response.status_code == status.HTTP_401_UNAUTHORIZED
    assert response.json() == {'detail': 'Error on password change'}
```

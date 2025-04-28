---
date: 2025-04-02
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 59: Register page

Like [[2025-04-02_TODOS-project-58:-login-page|TODOS project 58: login page]], now we want to create a register page.

First we create a new file `register.html` in `templates` folder.

```html
<!DOCTYPE >
<html lang="en">
  <head>
    <link
      rel="stylesheet"
      type="text/css"
      href="{{ url_for('static', path='/css/base.css') }}"
    />
    <link
      rel="stylesheet"
      type="text/css"
      href="{{ url_for('static', path='/css/bootstrap.css') }}"
    />
    <meta charset="UTF-8" />
    <title>TodoApp</title>
  </head>
  <body>
    <div class="container">
      <div class="card">
        <div class="card-header">Register</div>
        <div class="card-body">
          <form id="registerForm">
            <div class="form-row">
              <div class="form-group col-md-6">
                <label>Email</label>
                <input type="text" class="form-control" name="email" required />
              </div>
              <div class="form-group col-md-6">
                <label>Username</label>
                <input
                  type="text"
                  class="form-control"
                  name="username"
                  required
                />
              </div>
            </div>
            <div class="form-row">
              <div class="form-group col-md-6">
                <label>First Name</label>
                <input
                  type="text"
                  class="form-control"
                  name="firstname"
                  required
                />
              </div>
              <div class="form-group col-md-6">
                <label>Last Name</label>
                <input
                  type="text"
                  class="form-control"
                  name="lastname"
                  required
                />
              </div>
            </div>
            <div class="form-row">
              <div class="form-group col-md-6">
                <label>Role</label>
                <input type="text" class="form-control" name="role" required />
              </div>
              <div class="form-group col-md-6">
                <lable>Phone Number</lable>
                <input
                  type="text"
                  class="form-control"
                  name="phone_number"
                  autocomplete="phone_number"
                  required
                />
              </div>
            </div>
            <div class="form-group">
              <label>Password</label>
              <input
                type="password"
                class="form-control"
                name="password"
                autocomplete="new-password"
                required
              />
            </div>
            <div class="form-group">
              <label>Verify Password</label>
              <input type="password" class="form-control" name="password2" autocomplete="new-password" required />
            </div>
            <button type="submit" class="btn btn-primary">Sign in</button>
          </form>
        </div>
        <div class="card-footer text-muted">
          <a href="/auth/login-page">Already have an account?</a>
        </div>
      </div>
    </div>
  </body>
</html>
```

```py
# TodoApp/routers/auth.py
...
from fastapi import APIRouter, Depends, HTTPException, Request
from fastapi.templating import Jinja2Templates

...

templates = Jinja2Templates(directory="TodoApp/templates")


### Pages ###

@router.get("/login-page") ...

# create a new route for register page
@router.get("/register-page")
#            ^^^^^^^^^^^^^^ new route for register page
def render_register_page(request: Request):
    return templates.TemplateResponse("register.html", {"request": request})
    #                                  ^^^^^^^^^^^^^ render register page this time


### Endpoints ###

...

```

Now we can access the register page by going to `http://localhost:8000/auth/register-page`. But sumit button does not work yet. We will implement it in the next topic.


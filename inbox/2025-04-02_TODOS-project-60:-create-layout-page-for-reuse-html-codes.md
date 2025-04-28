---
date: 2025-04-02
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 60: create layout page for reuse html codes

In [[2025-04-02_TODOS-project-58:-login-page|TODOS project 58: login page]] and [[2025-04-02_TODOS-project-59:-Register-page|TODOS project 59: Register page]], we've created the login and register pages but we've repeated the same code in both pages.

```html
<!DOCTYPE>
<html lang="en">
  <head>
    <link rel="stylesheet" type="text/css"
      href="{{ url_for('static', path='/css/base.css') }}">
    <link rel="stylesheet" type="text/css"
      href="{{ url_for('static', path='/css/bootstrap.css') }}">
    <meta charset="UTF-8">
    <title>TodoApp</title>
  </head>
  <body>
    <!--only codes inside body tag is different-->
  </body>
</html>
```

The code above is the common part of the login and register pages, only the comments are different. We can extract the common part and put it in another HTML file.

```html
<!--layout.html-->
<!DOCTYPE>
<html lang="en">
  <head>
    <link rel="stylesheet" type="text/css"
      href="{{ url_for('static', path='/css/base.css') }}">
    <link rel="stylesheet" type="text/css"
      href="{{ url_for('static', path='/css/bootstrap.css') }}">
    <meta charset="UTF-8">
    <title>TodoApp</title>
  </head>
  <body>
    {% block content %}{% endblock %}
  </body>
</html>
```

`{% block content %}{% endblock %}` is a block that will be replaced by the content of the child page. We can use this layout in the login and register pages.

```html
<!--login.html-->
{% include 'layout.html' %}

<div class="container">
  <div class="card">
    <div class="card-header">Login</div>
    <div class="card-body">
      <form id="loginForm">
        <div class="form-group">
          <label>Username</label>
          <input
            type="text"
            class="form-control"
            name="username"
            autocomplete="username"
            required
          />
        </div>
        <div class="form-group">
          <label>Password</label>
          <input
            type="password"
            class="form-control"
            name="password"
            autocomplete="current-password"
            required
          />
        </div>
        <button type="submit" class="btn btn-primary">Login</button>
      </form>
    </div>
    <div class="card-footer text-muted">
      <a href="/auth/register-page">Register?</a>
    </div>
  </div>
</div>
```

```html
<!--register.html-->
{% include 'layout.html' %}

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
            <input type="text" class="form-control" name="username" required />
          </div>
        </div>
        <div class="form-row">
          <div class="form-group col-md-6">
            <label>First Name</label>
            <input type="text" class="form-control" name="firstname" required />
          </div>
          <div class="form-group col-md-6">
            <label>Last Name</label>
            <input type="text" class="form-control" name="lastname" required />
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
          <input
            type="password"
            class="form-control"
            name="password2"
            autocomplete="new-password"
            required
          />
        </div>
        <button type="submit" class="btn btn-primary">Sign in</button>
      </form>
    </div>
    <div class="card-footer text-muted">
      <a href="/auth/login-page">Already have an account?</a>
    </div>
  </div>
</div>
```
Above, we deleted the common parts in login.html and register.html, leaving only the different parts, and used `{ include 'layout.html' }` at the beginning to include the layout.html file. This way, we can achieve the effect of sharing.



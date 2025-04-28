---
date: 2025-04-02
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 61: load JS files to frontend

In [[2025-04-02_TODOS-project-58:-login-page|TODOS project 58: login page]] and [[2025-04-02_TODOS-project-59:-register-page|TODOS project 59: register page]] we created the frontend pages for the login and register pages but they are not functional yet because we have not loaded the JS files to the frontend.

We've already prepared the JS files in [[2025-04-02_TODOS-project-57:-download-static-CSS-and-JS-files-from-the-course|TODOS project 57: download static CSS and JS files from the course]], now we just need to include them in the `layout.html` file.

```html
<!--layout.html-->
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
    {% block content %}{% endblock %}

    <!--include the JS files here (it must be at the end of the body tag)-->
    <script src="{{ url_for('static', path='/js/jquery-slim.js') }}"></script>
    <script src="{{ url_for('static', path='/js/propper.js') }}"></script>
    <script src="{{ url_for('static', path='/js/bootstrap.js') }}"></script>
    <script src="{{ url_for('static', path='/js/base.js') }}" defer></script>
    <!--^^^^^ this means no matter where the script is, it will be executed after the page is loaded-->
    <!--^^^^^ this is important because base.js uses ids of elements that are not loaded yet-->
  </body>
</html>
```

Now you can test the register and login pages and they should work as expected. (redirect to todos page after login, show 404 error because we have not created the todos page yet, but you should still get the token in the cookies after login)

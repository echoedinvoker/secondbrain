---
date: 2025-04-02
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Jinja Templating overview

## What is Jinja?

- Fast, expressive and extensible templating language.
- Able to write code similar to Python in the DOM.
- The template is passed data to render within the final document.

## What are Jinja templating tags and scripts?

- Jinja tags allows developers to be confident while working with backend data, using tags that are similar to HTML.

for example:
```html
<link
  rel="stylesheet" type="text/css"
  href="{{ url_for('static', path='/todo/css/base.css') }}">
</link>
```
- Now image we have a list of todos that we retrieved from the database. We can pass the entire list of todos into the front-end and loop through each todo with this simple `for loo` on the template.

for example, on our fastapi side we have a context:
```py
context: {
  "todos": todo_list
}
```
on the frontend we can loop through the todos like this:
```html
{% for todo in todos %}
  <li>{{ todo }}</li>
{% endfor %}
```

- We can also use Jinja templating language with `if else` statements. One thing that may stand out is the double brackets with `todos|length`.

for example:
```html
{% if todos %}
  Displaying {{ todos|length }} todos
{% else %}
  You don't have any todos!
{% endif %}
```

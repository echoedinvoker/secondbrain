---
date: 2025-05-07
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# POST Request by fetch

```js
const data = {
  name: "John Doe",
  age: 30,
};

fetch("https://example.com/api", {
  method: "POST",
  headers: {
    "Content-Type": "application/json", // specify body content type
  },
  body: JSON.stringify(data), // convert data to JSON string because content type is JSON
})
  .then((response) => {
    if (!response.ok) {
    //   ^^^^^^^^^^^ response.ok is a boolean indicating if the response was successful
      throw new Error("Network response was not ok");
    }
    return response.json(); // parse the response body as JSON, this returns a promise
  })
  .then((data) => {
    console.log("Success:", data);
  })
  .catch((error) => {
    console.error("Error:", error);
  });
```


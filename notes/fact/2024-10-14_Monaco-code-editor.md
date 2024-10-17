---
date: 2024-10-14
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Monaco code editor

This library provides a React component for the Monaco editor, which is a code editor that powers Visual Studio Code.

**Installation**
```bash
npm install @monaco-editor/react

```

**Official Documentation**

[https://www.npmjs.com/package/@monaco-editor/react]


**Example**

```tsx
'use client';

import { Editor } from "@monaco-editor/react";
import { Snippet } from "@prisma/client";
import { useState } from "react";

interface SnippetEditFormProps {
  snippet: Snippet;
}

export default function SnippetEditForm(props: SnippetEditFormProps) {
  const [code, setCode] = useState(props.snippet.code);

  function handleCodeChange(value: string = '') {   // setting default value to empty string to avoid undefined error
    setCode(value);  // sync the code of the editor with the state above
  }
    
  return (
    <div>
      <Editor
        height="40vh"
        theme="vs-dark"
        language="javascript"
        defaultValue={props.snippet.code}
        options={{ minimap: { enabled: false } }}  {/* check documents for more options */}
        onChange={(value) => handleCodeChange(value)}
      />
    </div>
  )
}

```

**It should be inside a client component**

Above, we can see that we are using the event handler `onChange` to listen for changes in the editor, and using `useState` to store the content of the editor. Therefore, we must use a Client Component to wrap this editor.

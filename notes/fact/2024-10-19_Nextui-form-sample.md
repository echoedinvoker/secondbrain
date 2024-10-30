---
date: 2024-10-19
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Nextui form sample

Nextui only support input and textarea for now. Here is a sample form using Nextui components:

```tsx
import { Button, Input, Textarea } from "@nextui-org/react";
import * as actions from "@/actions"

export default function TopicCreateForm() {
  return (
    <form action={actions.createTopic}>  {/* Nextui doesn't support form element */}
      <div className="flex flex-col gap-4 p4 w-80">
        <h3 className="text-lg font-bold">Create a Topic</h3>  {/* Nextui doesn't support heading */}
        <Input label="Name" labelPlacement="outside" placeholder="Enter the topic name" />                    {/* Nextui supports input */}
        <Textarea label="Description" labelPlacement="outside" placeholder="Enter the topic description" />   {/* Nextui supports textarea */}
        <Button type="submit">submit</Button>                                                                 {/* Nextui supports button */}
      </div>
    </form>
  );
}
```
```

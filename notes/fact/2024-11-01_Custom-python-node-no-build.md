---
date: 2024-11-01
type: fact
aliases:
  -
hubs:
  - "[[Omniverse]]"
---

# Custom python node no build

```bash
~/D/g/liftup_agv_control > tree
.
├── control-extension
│   ├── exts
│   │   └── agv
│   │       ├── agv
│   │       │   ├── extension.py
│   │       │   └── nodes               # folder name must be `nodes`
│   │       │       ├── OgnPrimAdd.ogn  # one node is built by a .ogn and a .py files
│   │       │       └── OgnPrimAdd.py   # their names must start with `Ogn` and same
│   │       ├── config
│   │       │   └── extension.toml      # need to add some dependencies

```

```toml control-extension/exts/agv/config/extension.toml 

[dependencies]             # these two dependencies are required for building custom nodes
"omni.graph" = {}          # Or omniverse will just ignore nodes folder completely
"omni.graph.tools" = {}

```

```json control-extension/exts/agv/agv/nodes/OgnPrimAdd.ogn 
{
    "PrimAdd": {   <-- Same with file name but no `Ogn` start
                     it combines ext name to get node's name
                     in this case, our node complete name is `agv.PrimAdd`
        "description": "Moving path of the prim",
        "version": 1,
        "language": "python",  <-- we use python file to wright logic, so here must set `python`
        "inputs": {              another option is cpp
            "execIn": {
                "type": "execution",
                "description": "Trigger to execute the node"
            },
            "prim_from": {
                "type": "string",
                "description": "Path to the prim to move"
            },
            "prim_name": {
                "type": "string",
                "description": "Name of the prim to move"
            },
            "prim_to": {
                "type": "string",
                "description": "Where to move the prim"
            }
        },
        "outputs": {
            "execOut": {
                "type": "execution",
                "description": "Triggered when the node is done"
            }
        }
    }
}
```

```py control-extension/exts/agv/agv/nodes/OgnPrimAdd.py
import omni.kit.commands  # We need to import omni things manually, not like script node 

class OgnPrimAdd:  # same name with file (has `Ogn` start)
    class State:
        def __init__(self):
            pass                    # If we need to persist some states
                                    # between triggers, we can define here
    @staticmethod
    def internal_state():           # then, we can use db.per_instance_state
        return OgnPrimAdd.State()   # to get states we defined in the constructor

    @staticmethod
    def compute(db) -> bool:  # we write our main business logic inside compute method
        path = f"{db.inputs.prim_from}/{db.inputs.prim_name}"  # we can use inputs and outputs,
        pathTo = f"{db.inputs.prim_to}/{db.inputs.prim_name}"  # which we defined in .ogn file
        omni.kit.commands.execute('MovePrim', path_from=path, path_to=pathTo)

        return True  # compute method always return True
```

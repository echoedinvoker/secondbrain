---
date: 2025-05-29
type: fact
aliases:
  -
hubs:
  - "[[mcp]]"
---

# Clone and Compile a pre build MCP Server from Github to Local

There are many open source, pre-built MCP server projects on Github that can be cloned directly to our local machine to be used locally. Below is an example of a nodejs pre-built MCP server.

```sh
# clone the repository
 git clone https://github.com/modelcontextprotocol/quickstart-resources.git

 cd quickstart-resources/

 ls
LICENSE  mcp-client-python/  mcp-client-typescript/  README.md  weather-server-python/  weather-server-typescript/

 cd weather-server-typescript

 ls
package.json  package-lock.json  README.md  src/  tsconfig.json

# install dependencies
 npm install

# compile the server
 npm run build

 ls
build/  node_modules/  package.json  package-lock.json  README.md  src/  tsconfig.json

 cd build

 ls
index.js* # this is the compiled server, which can be run with claude, cursor, or any other MCP client
```



---
date: 2025-03-07
type: fact
aliases:
  -
hubs:
  - "[[langgraph]]"
---

# Create containers with langgraph up

`langgraph up` will use the `pyproject.toml` of poetry to generate an image and create containers, so we must use poetry to manage our python package.

```sh
# in your project directory
poetry init
poetry add [your-dependencies]
```

Your `pyproject.toml` should look like this:

```toml
[tool.poetry]
name = "langgraph_deploy"
version = "0.1.0"
description = ""
authors = ["echoedinvoker <echoedinvoker@gmail.com>"]
# readme = "README.md" # remove this line, or you need to create a README.md
packages = [{include = "graph"}]

[tool.poetry.dependencies]
python = ">=3.9,<4.0"
python-dotenv = ">=1.0.1,<2.0.0"
black = ">=25.1.0,<26.0.0"
isort = ">=6.0.1,<7.0.0"
langchain = ">=0.3.20,<0.4.0"
langchain-openai = ">=0.3.7,<0.4.0"
langgraph = ">=0.3.5,<0.4.0"
langchain-community = ">=0.1.0"
# even ingestion.py's dependencies need to be added here, or the error will occur
langchain-chroma = ">=0.1.0"
beautifulsoup4 = ">=4.12.2"

[build-system]
requires = ["poetry-core>=2.0.0,<3.0.0"]
build-backend = "poetry.core.masonry.api"

```

Because poetry don't create a shell for us, so we should create a shell in other ways such as `python -m venv venv`, we need to install `langgraph-cli` in the shell for useing `langgraph up`.

```sh
python -m venv venv
source venv/bin/activate
pip install "langgraph-cli[inmem]" 

```


Then, you are ready to launch the containers with `langgraph up`:

```sh
langgraph up

Starting LangGraph API server...
For local dev, requires env var LANGSMITH_API_KEY with access to LangGraph Cloud closed beta.
For production use, requires a license key in env var LANGGRAPH_CLOUD_LICENSE_KEY.
Ready!       
- API: http://localhost:8123
- Docs: http://localhost:8123/docs
- LangGraph Studio: https://smith.langchain.com/studio/?baseUrl=http://127.0.0.1:8123

```
`langgraph up` will build the image and create the containers to run the langgraph API server. You can use `docker ps` to check the containers.

```sh
 docker ps
CONTAINER ID   IMAGE                            COMMAND                  CREATED          STATUS                    PORTS                                         NAMES
6ab3046fc6ca   langgraph_deploy-langgraph-api   "/storage/entrypoint…"   9 minutes ago    Up 11 seconds (healthy)   0.0.0.0:8123->8000/tcp, [::]:8123->8000/tcp   langgraph_deploy-langgraph-api-1
a91a4f4f310d   pgvector/pgvector:pg16           "docker-entrypoint.s…"   25 minutes ago   Up 17 seconds (healthy)   0.0.0.0:5433->5432/tcp, [::]:5433->5432/tcp   langgraph_deploy-langgraph-postgres-1

```

In addition to the langgraph API server, it will also start a postgres database to store the data of the langgraph API server.

We can use this method to deploy the langgraph API server frontend service anywhere.






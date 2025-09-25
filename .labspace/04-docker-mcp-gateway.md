## Using the Docker MCP Gateway with Docker Compose


The [Docker MCP Gateway](https://docs.docker.com/ai/mcp-gateway/) is a CLI tool and gateway service that implements the Model Context Protocol (MCP) - an open standard that allows AI applications to securely connect to external data sources and tools. It essentially acts as a universal translator and proxy between AI applications and the various tools/services they need to access, making it much easier to build AI-powered applications with rich external integrations.

### Features

- Available out of the box in the latest release of Docker Desktop.
- Orchestrate and manages MCP servers securely across Dev and Prod 
- Secure handling of API keys and credentials via Docker Desktop
- One gateway endpoint centralizes configuration, credentials, and access control for all MCP servers
- Built-in logging and call tracing capabilities
- Run in isolated Docker containers with restricted privileges, network access, and resource usage

<img width="1000" height="341" alt="image" src="https://github.com/user-attachments/assets/59ccb984-c868-46d5-9e21-7324746d5dbe" />



## Getting Started

### Clone the repository

```bash
git clone https://github.com/docker/mcp-gateway
```

### Change directory to mcp-gateway

```bash
cd mcp-gateway/examples/client
```

You'll find two key files:

- Client Code (main.py):
   - Connects to the MCP Gateway via HTTP streaming
   - Calls a `search` tool with query `Docker`
   - The gateway routes this to the appropriate MCP server

- Docker Compose:
  - Client Service: Your Python application connecting to the gateway
  - Gateway Service: The MCP Gateway that manages MCP servers
  - Gateway is configured with `--servers=duckduckgo` (a search MCP server)

### Run the application containers

```bash
docker compose up --build
```

### How it works?

1. Your Python client calls session.call_tool("search", {"query": "Docker"})
2. The MCP Gateway receives the HTTP request and routes it to the DuckDuckGo MCP server
3. The DuckDuckGo server performs the search via its API
4. Results flow back through the server → gateway → client
5. Your client prints result.content[0].text

This example shows how to call the MCP Gateway from a python client:

- Doesn't rely on the MCP Toolkit UI. Can run anywhere, even if Docker Desktop is not available.
- Defines the list of enabled servers from the gateway's command line, with --server
- Uses the online Docker MCP Catalog hosted on http://desktop.docker.com/mcp/catalog/v2/catalog.yaml.
- Uses the latest http streaming transport.

### Next Steps

We're all set to build DevDuck Multi-Agent App using Cerebras, ADK and Agentic Compose. Let's get started.


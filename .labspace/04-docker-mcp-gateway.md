## Demonstrating the Docker MCP Gateway with Docker Compose


The Docker MCP Gateway is a CLI tool and gateway service that implements the Model Context Protocol (MCP) - an open standard that allows AI applications to securely connect to external data sources and tools.


```mermaid
graph TB
    subgraph "AI Clients"
        A1[Claude Desktop]
        A2[VS Code]
        A3[Cursor]
        A4[Custom Python Client]
    end
    
    subgraph "Docker MCP Gateway"
        G[MCP Gateway<br/>:9011]
        GM[Gateway Manager]
        GR[Request Router]
        GA[Auth Handler]
        GS[Secrets Manager]
    end
    
    subgraph "MCP Servers (Docker Containers)"
        S1[DuckDuckGo Server<br/>Search Tools]
        S2[GitHub Server<br/>Repository Tools]
        S3[PostgreSQL Server<br/>Database Tools]
        S4[Slack Server<br/>Communication Tools]
    end
    
    subgraph "External Services"
        E1[DuckDuckGo API]
        E2[GitHub API]
        E3[PostgreSQL DB]
        E4[Slack API]
    end
    
    subgraph "Configuration"
        C1[docker-mcp.yaml<br/>Server Catalog]
        C2[registry.yaml<br/>Enabled Servers]
        C3[config.yaml<br/>Gateway Config]
    end
    
    %% Client connections
    A1 -->|HTTP/Streaming| G
    A2 -->|HTTP/Streaming| G
    A3 -->|HTTP/Streaming| G
    A4 -->|HTTP/Streaming| G
    
    %% Gateway internal flow
    G --> GM
    GM --> GR
    GR --> GA
    GA --> GS
    
    %% Gateway to servers
    GM -->|MCP Protocol| S1
    GM -->|MCP Protocol| S2
    GM -->|MCP Protocol| S3
    GM -->|MCP Protocol| S4
    
    %% Servers to external services
    S1 --> E1
    S2 --> E2
    S3 --> E3
    S4 --> E4
    
    %% Configuration
    C1 --> GM
    C2 --> GM
    C3 --> GM
    
    %% Docker Host
    subgraph "Docker Host"
        D[Docker Engine]
        DS[Docker Socket<br/>/var/run/docker.sock]
    end
    
    GM -->|Container Management| DS
    DS --> D
    
    %% Example flow annotation
    A4 -.->|1. call_tool("search", {"query": "Docker"})| G
    G -.->|2. Route to DuckDuckGo server| S1
    S1 -.->|3. Search via API| E1
    E1 -.->|4. Return results| S1
    S1 -.->|5. MCP response| G
    G -.->|6. HTTP response| A4
    
    classDef client fill:#e1f5fe
    classDef gateway fill:#f3e5f5
    classDef server fill:#e8f5e8
    classDef external fill:#fff3e0
    classDef config fill:#fce4ec
    
    class A1,A2,A3,A4 client
    class G,GM,GR,GA,GS gateway
    class S1,S2,S3,S4 server
    class E1,E2,E3,E4 external
    class C1,C2,C3 config
```

### Key Files:

1. Client Code (main.py):

- Connects to the MCP Gateway via HTTP streaming
- Calls a "search" tool with query "Docker"
- The gateway routes this to the appropriate MCP server

2. Docker Compose:

- Client Service: Your Python application connecting to the gateway
- Gateway Service: The MCP Gateway that manages MCP servers
- Gateway is configured with --servers=duckduckgo (a search MCP server)


### Message Flow in Your Example

1. Client Request: Your Python client calls session.call_tool("search", {"query": "Docker"})
2. Gateway Routing: The MCP Gateway receives the HTTP request and routes it to the DuckDuckGo MCP server
3. Server Execution: The DuckDuckGo server performs the search via its API
4. Response Chain: Results flow back through the server → gateway → client
5. Client Output: Your client prints result.content[0].text


This example shows how to call the MCP Gateway from a python client:

- Doesn't rely on the MCP Toolkit UI. Can run anywhere, even if Docker Desktop is not available.
- Defines the list of enabled servers from the gateway's command line, with --server
- Uses the online Docker MCP Catalog hosted on http://desktop.docker.com/mcp/catalog/v2/catalog.yaml.
- Uses the latest http streaming transport.

## Getting Started

### Clone the repository

```
git clone https://github.com/docker/mcp-gateway
```

### Change directory to mcp-gateway

```
cd mcp-gateway/examples/client
```

### Running the app

```
docker compose up --build
```

### Key Benefits

1. For Developers:

- Container Isolation: Each MCP server runs in its own Docker container
- Unified API: One interface to access multiple tools and services
- Security: Secrets are managed securely, not in environment variables
- Scalability: Can serve multiple AI clients simultaneously

The Docker MCP Gateway essentially acts as a universal translator and proxy between AI applications and the various tools/services they need to access, making it much easier to build AI-powered applications with rich external integrations.

### Next Steps

We're all set to build DevDuck Multi-Agent App using Cerebras, ADK and Agentic Compose. Let's get started.


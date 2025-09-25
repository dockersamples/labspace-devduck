# Getting Started with DevDuck Agents

Now that you've understood how Docker Model Runner and MCP Gateway works, let's try building DevDuck Multi-Agent application! This section will guide you through the complete setup process, from cloning the repository to accessing your first multi-agent system

DevDuck is a multi-agent system for Node.js programming assistance built with Google Agent Development Kit (ADK). This project features a coordinating agent (DevDuck) that manages two specialized sub-agents (Local Agent and Cerebras) for different programming tasks.

## Architecture

The system consists of three main agents orchestrated by Docker Compose, which plays a **primordial role** in launching and coordinating all agent services:

### 🐙 Docker Compose Orchestration

- **Central Role**: Docker Compose serves as the foundation for the entire multi-agent system
- **Service Orchestration**: Manages the lifecycle of all three agents (DevDuck, Local Agent, and Cerebras)
- **Configuration Management**: Defines agent prompts, model configurations, and service dependencies
  directly in the compose file
- **Network Coordination**: Establishes secure inter-agent communication channels
- **Environment Management**: Handles API keys, model parameters, and runtime configurations

### Agent Components

### 🦆 DevDuck (Main Agent)

- **Role**: Main development assistant and project coordinator
- **Model**: Mistral (`ai/mistral:7B-Q4_0`)
- **Capabilities**: Routes requests to appropriate sub-agents based on user needs

### 👨‍💻 Local Agent Agent

- **Role**: General development tasks and project coordination
- **Model**:  llama3.2 (`ai/llama3.2:1B-Q8_0`)
- **Specialization**: Node.js programming expert for understanding code, explaining concepts, and generating code snippets

### 🧠 Cerebras Agent

- **Role**: Advanced computational tasks and complex problem-solving
- **Model**: Llama-4 Scout (`llama-4-scout-17b-16e-instruct`)
- **Provider**: Cerebras API
- **Specialization**: Node.js programming expert for complex problem-solving scenarios

## Step 1: Repository Setup

First, let's clone the repository and explore its structure:

### Clone the Repository

```bash
# Clone the Docker Cerebras demo repository
git clone https://github.com/dockersamples/docker-cerebras-demo.git

# Navigate to the project directory
cd docker-cerebras-demo

# List the contents to familiarize yourself
ls -la
```


### Component Breakdown

#### 🎼 **DevDuck Orchestrator**
- **Role**: Central coordinator and decision maker
- **Responsibilities**: 
  - Route user requests to appropriate agents
  - Manage conversation context and history
  - Handle multi-agent workflows
  - Provide unified response interface
- **Technology**: Python, FastAPI
- **Port**: 8000

#### 🤖 **Local Agent**
- **Role**: Fast, local processing for common tasks
- **Responsibilities**:
  - Handle simple queries and code completion
  - Provide quick responses for development tasks
  - Cache frequently used information
  - Fallback processing when cloud services are unavailable
- **Technology**: Python with local ML models
- **Models**: Lightweight language models (1-7B parameters)

#### 🧠 **Cerebras Agent**
- **Role**: Advanced AI processing for complex tasks
- **Responsibilities**:
  - Complex code analysis and architecture suggestions
  - Advanced problem-solving and reasoning
  - Large-scale text processing and generation
  - Research and documentation tasks
- **Technology**: Python client for Cerebras API
- **Models**: High-performance cloud models (70B+ parameters)

#### 🌐 **MCP Gateway**
- **Role**: Enhanced protocol support and tool integration
- **Responsibilities**:
  - Extend agent capabilities with tools and plugins
  - Handle file system operations
  - Provide additional context and memory
  - Enable advanced agent interactions
- **Technology**: Docker's MCP Gateway service

### Communication Flow

#### 📡 **Request Processing Flow**

1. **User Input**: User sends request via web interface
2. **Orchestration**: DevDuck analyzes request and determines routing
3. **Agent Selection**: Based on complexity and requirements:
   - Simple queries → Local Agent
   - Complex analysis → Cerebras Agent  
   - Multi-step tasks → Both agents in sequence
4. **Processing**: Selected agent(s) process the request
5. **Response**: DevDuck aggregates and returns unified response


### Container Architecture

Each component runs in its own Docker container for isolation and scalability:

#### 📦 **Container Specifications**

| Service | Image | CPU | Memory | Ports |
|---------|-------|-----|---------|-------|
| DevDuck Orchestrator | Custom Python | 0.5-1.0 | 1-2 GB | 8000 |
| Local Agent | Custom Python | 1.0-2.0 | 2-4 GB | Internal |
| Cerebras Agent | Custom Python | 0.2-0.5 | 512 MB | Internal |
| MCP Gateway | docker/mcp-gateway | 0.2-0.5 | 256 MB | Internal |


## Environment Configuration

### Required Environment Variables

You'll need to configure these environment variables:

```bash
# Cerebras Configuration
CEREBRAS_API_KEY=your_cerebras_api_key
CEREBRAS_BASE_URL=https://api.cerebras.ai/v1
CEREBRAS_CHAT_MODEL=llama-4-scout-17b-16e-instruct
```

### Docker Compose Structure

The system uses Docker Compose for orchestration:

```yaml
services:
  devduck-agent:
    build: ./agents
    ports:
      - "8000:8000"
    environment:
      - CEREBRAS_API_KEY=${CEREBRAS_API_KEY}
    depends_on:
      - mcp-gateway
      
  mcp-gateway:
    image: docker/mcp-gateway:latest
    command: /docker-mcp gateway serve mcp-gateway-catalog.yaml
```

### 🔍 Examine Key Files

Let's look at the main configuration files:

```bash
# View the Docker Compose configuration
cat compose.yml

# Examine the main agent entry point
cat agents/main.py

# Check the agent requirements
cat agents/requirements.txt
```


## Docker Image Preparation

Before deploying, let's understand what Docker will build:

### 🐳 Examine the Dockerfile

```bash
# View the agent Dockerfile
cat agents/Dockerfile
```

**Understanding the Build Process:**
- Base image with Python runtime
- Agent dependencies installation
- Application code copying
- Container startup configuration


## Step 2. System Deployment

Now let's deploy the complete multi-agent system:

### 🚀 Initial Deployment

```bash
# Deploy all services
docker compose up
```

**What to expect during first deployment:**

1. **Image Building** (2-3 minutes)
   - Base image download
   - Python dependencies installation
   - Application code integration

2. **Service Initialization** (1-2 minutes)
   - Container startup
   - Model downloads (if using local models)
   - Service health checks

3. **Network Setup**
   - Internal network creation
   - Port mapping configuration
   - Inter-service communication setup

### 🔄 Development Mode Deployment

For active development with code changes:

```bash
# Run in detached mode (background)
docker compose up -d

# Build and restart when code changes
docker compose up --build

# Force recreation of containers
docker compose up --build --force-recreate
```

## Step 3: Deployment Verification

### ✅ Check Service Status

```bash
# Verify all containers are running
docker compose ps
```

**Expected Output:**
```
NAME                                   IMAGE                               COMMAND                  SERVICE         CREATED         STATUS         PORTS
docker-cerebras-demo-devduck-agent-1   docker-cerebras-demo-devduck-agent  "sh -c 'uvicorn main…"   devduck-agent   2 minutes ago   Up 2 minutes   0.0.0.0:8000->8000/tcp
docker-cerebras-demo-mcp-gateway-1     docker/mcp-gateway:latest           "/docker-mcp gateway…"   mcp-gateway     2 minutes ago   Up 2 minutes   
```

### 🩺 Health Check

Perform a comprehensive health check:

```bash
# Check container resource usage
docker stats --no-stream

# Test network connectivity
docker compose exec devduck-agent curl -f http://localhost:8000/health

# Verify agent endpoints
curl -s http://localhost:8000/health | jq .
```


## Step 4: First Access

### 🌐 Access the Web Interface

Open your web browser and navigate to:

**Primary URL**: [http://localhost:8000/dev-ui/?app=devduck](http://localhost:8000/dev-ui/?app=devduck)

**Alternative URLs** (if the primary doesn't work):
- [http://0.0.0.0:8000/dev-ui/?app=devduck](http://0.0.0.0:8000/dev-ui/?app=devduck)
- [http://127.0.0.1:8000/dev-ui/?app=devduck](http://127.0.0.1:8000/dev-ui/?app=devduck)

### 🧪 Initial Testing

Test the system with a simple interaction:

1. **Load the Interface**: Ensure the web page loads completely
2. **Send Test Message**: Type "Hello DevDuck" in the chat interface
3. **Verify Response**: Confirm you receive a response from the system
4. **Check Logs**: Monitor the logs for any errors

```bash
# Watch logs during your first interaction
docker compose logs -f devduck-agent
```

## Step 5: System Validation

### 🔍 Comprehensive Testing

Run through this validation checklist:

#### Basic Functionality
- [ ] Web interface loads without errors
- [ ] Can send and receive messages
- [ ] DevDuck orchestrator responds appropriately
- [ ] No error messages in container logs

#### Agent Communication
```bash
# Test direct agent communication (advanced)
docker compose exec devduck-agent python -c "
import requests
response = requests.get('http://localhost:8000/health')
print(f'Status: {response.status_code}')
print(f'Response: {response.json()}')
"
```

#### Resource Health
- [ ] System resources are stable (not maxed out)
- [ ] All containers remain running
- [ ] Network connectivity is working



## Success Confirmation

If everything is working correctly, you should see:

✅ **Web Interface**: DevDuck interface loads at http://localhost:8000/dev-ui/?app=devduck  
✅ **Agent Response**: Messages sent receive appropriate responses  
✅ **Container Status**: All containers show "Up" status  
✅ **Resource Usage**: System resources are stable  
✅ **Logs**: No critical errors in application logs  

## Next Steps

Excellent! You now have a fully functional multi-agent system running. In the next section, you'll dive deeper into environment configuration and learn how to customize your deployment for different scenarios.

Ready to explore the environment setup and deployment options? Let's continue! 🚀

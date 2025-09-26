# Labspace - DevDuck Multi-Agent Systems

This Labspace provides an overview of the [DevDuck Multi-Agent System](https://github.com/dockersamples/docker-cerebras-demo), a secure coding agent for Node.js programming assistance built with Google Agent Development Kit (ADK). The system features a coordinating agent (DevDuck) that manages two specialized sub-agents (Local Agent and Cerebras) for different programming tasks.

## Learning objectives

This Labspace will teach you:
- Multi-agent coordination and intelligent routing between specialized AI agents
- Google Agent Development Kit (ADK) architecture and implementation
- Agent specialization patterns (coordination vs execution vs analysis)
- Docker Compose orchestration for multi-agent systems
- Agent-to-agent communication and task delegation
- Debugging agent decision-making through trace visualization

## System Architecture

**Three-Agent System:**
- **DevDuck Agent**: Main coordinator using Qwen3 model
- **Local Agent**: General development tasks using Qwen2.5 model  
- **Cerebras Agent**: Complex problem-solving using Llama-4 Scout via Cerebras API


## Launch the Labspace

To launch the Labspace, run the following command:

```bash
docker compose -f oci://dockersamples/labspace-devduck up -d
```

And then open your browser to http://localhost:3030.

### Using the Docker Desktop extension

If you have the Labspace extension installed (`docker extension install dockersamples/labspace-extension` if not), you can also [click this link](https://open.docker.com/dashboard/extension-tab?extensionId=dockersamples/labspace-extension&location=dockersamples/labspace-mcp-gateway&title=Docker%20MCP%20Gateway) to launch the Labspace.

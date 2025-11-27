# Local LLM Setup with Ollama and Open WebUI

A complete Docker-based solution for running large language models (LLMs) locally with a ChatGPT-like interface.

## What's Inside

### Architecture
```
┌─────────────────┐
│   Open WebUI    │ ← Web chat interface (what you see)
│   (port 3000)   │    - Svelte/SvelteKit frontend
└────────┬────────┘    - Python/FastAPI backend
         │              - SQLite database
         ↓
┌─────────────────┐
│     Ollama      │ ← LLM runtime engine (the engine)
│   (port 11434)  │    - Model management
└────────┬────────┘    - Inference optimization
         │              - API server
         ↓
┌─────────────────┐
│  qwen2.5:14b    │ ← AI model (the brain)
│   (~9GB RAM)    │    - Made by Alibaba Cloud
└─────────────────┘    - Excellent reasoning & multilingual
```

### Components

**Ollama**
- Platform for running LLMs locally
- Manages model downloads and execution
- Optimizes performance for your hardware
- Made by Ollama Inc. (Docker founders)

**Open WebUI**
- Self-hosted ChatGPT-like interface
- Multi-user support with accounts
- Chat history and organization
- Model switching capability
- Completely private and offline-capable

**Qwen 2.5 14B**
- Current model: `qwen2.5:14b`
- Publisher: Alibaba Cloud (DAMO Academy)
- RAM usage: ~8-9GB
- Runs fully offline (no internet needed after download)
- Strong at: reasoning, coding, multilingual tasks

## Quick Start

### Prerequisites
- Docker and Docker Compose installed
- 32GB RAM recommended (minimum 16GB)
- ~10GB free disk space

### Start the Stack

```powershell
# Start all services
docker compose -f "./docker-compose ollama open-webui.yml"  up -d --build

# Check status
docker ps

# View logs
docker logs ollama
docker logs openwebui
```

### Access the Interface

Open your browser and navigate to:
```
http://localhost:3000
```

First time:
1. Create an account (stored locally)
2. Select `qwen2.5:14b` from the model dropdown
3. Start chatting!

### Stop the Stack

```powershell
# Stop services (keeps data)
docker compose -f .\docker-compose-deepseek.yml down

# Stop and remove all data (fresh start)
docker compose -f .\docker-compose-deepseek.yml down -v
```

## Privacy & Offline Usage

✅ **Completely Private**
- All data stays on your machine
- No telemetry or external connections
- Conversations never leave your computer

✅ **Works Offline**
- Internet only needed for initial model download
- Once downloaded, works without connectivity
- All processing happens locally

## Changing Models

### Edit the Dockerfile

Modify `Dockerfile.ollama` and change the model:

```dockerfile
RUN nohup bash -c "ollama serve &" && \
    sleep 5 && \
    ollama pull <model-name> && \
    pkill ollama
```

### Recommended Models (32GB RAM)

| Model | RAM | Publisher | Best For |
|-------|-----|-----------|----------|
| `qwen2.5:14b` | ~9GB | Alibaba | General, reasoning, multilingual |
| `qwen2.5:7b` | ~5GB | Alibaba | Faster, still high quality |
| `codellama:13b` | ~8GB | Meta | Code generation |
| `mistral:7b` | ~5GB | Mistral AI | General purpose |
| `llama3.1:8b` | ~5GB | Meta | General purpose |
| `phi4:14b` | ~9GB | Microsoft | Reasoning |

### Rebuild with New Model

```powershell
docker compose -f .\docker-compose-deepseek.yml down
docker compose -f .\docker-compose-deepseek.yml up -d --build
```

## Adding More Models

You can add multiple models without rebuilding:

```powershell
# Pull additional model
docker exec ollama ollama pull mistral:7b

# List all models
docker exec ollama ollama list

# Remove a model
docker exec ollama ollama rm deepseek-r1:1.5b
```

Then switch between models in the Open WebUI interface.

## Data Persistence

Data is stored in Docker volumes:
- `llm_ollama` - Downloaded models
- `llm_open-webui` - Chat history, users, settings

Your chats persist even when containers are recreated (unless you use `down -v`).

## Troubleshooting

### Container won't start
```powershell
docker logs ollama
docker logs openwebui
```

### Clear cache and restart
```powershell
docker compose -f .\docker-compose-deepseek.yml down
docker system prune -f
docker compose -f .\docker-compose-deepseek.yml up -d --build
```

### Reset everything
```powershell
docker compose -f .\docker-compose-deepseek.yml down -v
docker compose -f .\docker-compose-deepseek.yml up -d --build
```

## Resources

- [Ollama Models Library](https://ollama.com/library)
- [Open WebUI Documentation](https://docs.openwebui.com)
- [Qwen GitHub](https://github.com/QwenLM/Qwen2.5)

## License

This setup uses:
- Ollama: MIT License
- Open WebUI: MIT License
- Qwen 2.5: Apache 2.0 License

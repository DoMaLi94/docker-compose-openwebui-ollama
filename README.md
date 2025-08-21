# OpenWebUI with Ollama

This repository contains a Docker Compose setup for running OpenWebUI with Ollama, providing a complete local AI chat interface with GPU acceleration support.

## Overview

This setup includes:
- **OpenWebUI**: A user-friendly web interface for interacting with AI models
- **Ollama**: A local AI model server for running large language models
- **GPU Support**: NVIDIA GPU acceleration for faster inference

## Prerequisites

- Docker and Docker Compose installed
- NVIDIA GPU (optional, for GPU acceleration)
- NVIDIA Container Toolkit (if using GPU)

## Quick Start

1. Clone or download this repository
2. Navigate to the project directory
3. Start the services:
   ```bash
   docker compose up -d
   ```
4. Access OpenWebUI at: http://localhost:3000

## Configuration

### Default Settings
- OpenWebUI runs on port 3000
- Ollama API runs on port 11434
- GPU acceleration is enabled by default (NVIDIA)

### Environment Variables
- `OLLAMA_BASE_URL`: Points to the Ollama service (default: http://ollama:11434)

### Alternative Ollama Setup
If you're running Ollama on your host machine instead of in a container:
1. Uncomment the host.docker.internal line in the docker-compose.yaml
2. Comment out or remove the ollama service section
3. Remove the ollama dependency from the open-webui service

## Usage

### Starting the Services
```bash
# Start in detached mode
docker compose up -d

# Start with logs visible
docker compose up
```

### Stopping the Services
```bash
docker compose down
```

### Viewing Logs
```bash
# View all logs
docker compose logs

# View logs for specific service
docker compose logs open-webui
docker compose logs ollama
```

### Installing Models
Once the services are running, you can install models through the OpenWebUI interface or directly via Ollama:

```bash
# Install a model via Ollama container
docker exec -it ollama ollama pull llama2

# List installed models
docker exec -it ollama ollama list
```

## Volumes and Data Persistence

The setup uses named volumes to persist all important data across container restarts and updates:

### Volume Details
- **`ollama`**: Stores downloaded AI models, model configurations, and Ollama settings
  - Location: `/root/.ollama` inside the container
  - Contains: All pulled models (e.g., llama2, codellama, etc.), model metadata, and Ollama configuration
  
- **`open-webui`**: Stores all OpenWebUI application data
  - Location: `/app/backend/data` inside the container
  - Contains: User accounts, user settings, chat histories, custom prompts, knowledge bases, and application configuration

### What Gets Persisted
- User accounts and authentication data
- All chat conversations and history  
- User preferences and interface settings
- Downloaded Ollama models (can be large, 4GB+ per model)
- Custom prompts and templates
- Knowledge bases and uploaded documents
- Application configuration and settings

### Important Notes
- Data persists even when containers are stopped, updated, or recreated
- Volumes are only deleted when explicitly removed with `docker compose down -v`
- To backup your data, you can backup these Docker volumes
- Models downloaded through OpenWebUI interface are automatically stored in the ollama volume

## GPU Support

The configuration includes NVIDIA GPU support with the following settings:
- Driver: nvidia
- Device IDs: all available GPUs
- Capabilities: gpu

If you don't have an NVIDIA GPU, you can remove the `deploy` section from the ollama service.

## Troubleshooting

### Common Issues

1. **Port conflicts**: If port 3000 or 11434 is already in use, modify the port mappings in docker-compose.yaml

2. **GPU not working**: Ensure you have the NVIDIA Container Toolkit installed:
   ```bash
   # Ubuntu/Debian
   sudo apt-get update
   sudo apt-get install -y nvidia-container-toolkit
   sudo systemctl restart docker
   ```

3. **OpenWebUI can't connect to Ollama**: Check that both services are running and the OLLAMA_BASE_URL is correct

### Health Checks
The Ollama service includes a health check. You can verify the status:
```bash
docker compose ps
```

## Accessing the Services

- **OpenWebUI**: http://localhost:3000
- **Ollama API**: http://localhost:11434

## Security Notes

This setup is intended for local private use only and is not production ready.

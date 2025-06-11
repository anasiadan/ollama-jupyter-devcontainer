# Local LLM Testing with Dev Containers

A containerized development environment for testing Large Language Models (LLMs) locally using Ollama and Jupyter notebooks. This setup allows you to run and experiment with LLMs without installing anything directly on your host machine.

## Overview

This project provides a complete Docker-based development environment that includes:
- **Ollama**: Local LLM server for running models like Llama 3.1
- **Jupyter Notebook**: Interactive environment for testing and experimenting with LLMs
- **Dev Container**: VS Code integration for seamless development experience

## Prerequisites

- [Docker](https://www.docker.com/get-started) installed on your system
- [VS Code](https://code.visualstudio.com/) with the [Dev Containers extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)

## Quick Start

1. **Clone the repository**
   ```bash
   gh repo clone anasiadan/ollama-jupyter-devcontainer
   cd ollama-jupyter-devcontainer

   ```

2. **Open in VS Code**
   ```bash
   code .
   ```

3. **Launch Dev Container**
   - Press `Ctrl+Shift+P` (or `Cmd+Shift+P` on Mac)
   - Type "Dev Containers: Reopen in Container"
   - Select the option and wait for the container to build

4. **Access Jupyter Notebook**
   - Once the container is running, Jupyter will be available at `http://localhost:8888`
   - Navigate to the `ollama-workspace` folder to find the example notebook
   - Open the notebook to start experimenting with LLMs

## Architecture

The setup consists of two main services:

### Ollama Service
- **Image**: `ollama/ollama`
- **Port**: 11434 (API endpoint)
- **Purpose**: Hosts and serves LLM models locally
- **Storage**: Persistent volume for model data

### Jupyter Service  
- **Base**: Microsoft Dev Containers Python 3.12
- **Port**: 8888 (Notebook interface)
- **Purpose**: Interactive development environment
- **Dependencies**: Basic HTTP client libraries for API communication

Both services communicate through a dedicated Docker network (`ollama-network`).

## Usage Examples

The included Jupyter notebook demonstrates three main interaction patterns:

### 1. Model Download
```python
# Choose the model as a parameter
model_name = "your_model"

# Pull a model (first time only)
requests.post('http://ollama:11434/api/pull', json={"name": "model_name"})
```
### 2. Basic Generation
```python
# Generate text
response = requests.post('http://ollama:11434/api/generate', json={
    "model": "model_name",
    "prompt": "Your question here",
    "stream": False
})
```

### 3. Chat-based Interaction
```python
messages = [
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "Your question here"}
]

response = requests.post('http://ollama:11434/api/chat', json={
    "model": "llama3.1", 
    "messages": messages,
    "stream": False
})
```
For more apis and how to use them refer on the ollama [api site](https://github.com/ollama/ollama/blob/main/docs/api.md)

## Available Models

You can use any model supported by Ollama. Popular options include:
- `llama3.1` (default in examples)
- `llama2`
- `deepseek-r1`
- `codellama`
- `mistral`
- `neural-chat`

To use a different model, simply change the `model_name` variable in your requests.
See all the available models [here](https://ollama.com/library).

## File Structure

```
.
├── .devcontainer/
│   ├── devcontainer.json          # Dev container configuration
│   ├── docker-compose.yml         # Multi-service container setup
│   ├── Dockerfile                 # Jupyter service image
│   └── requirements.txt           # Python dependencies
├── ollama-workspace/
│   └── ollama_jupyter_notebook.ipynb  # Example notebook
└── README.md                      # This file
```

## Configuration

### Customizing Python Dependencies
Edit `requirements.txt` to add additional Python packages:
```txt
requests==2.32.4
jupyter>=1.0.0
pandas>=1.5.0
# Add your packages here
```

### Changing Ports
Modify the port mappings in `docker-compose.yml`:
```yaml
ports:
  - "8888:8888"  # Jupyter (host:container)
  - "11434:11434"  # Ollama API (host:container)
```

### Workspace Mounting
The workspace is mounted to `/workspaces` in the container. Adjust the volume mapping in `docker-compose.yml` if needed:
```yaml
volumes:
  - ..:/workspaces:cached  # Mounts parent directory
```

## Troubleshooting

### Container Won't Start
- Ensure Docker is running
- Check that ports 8888 and 11434 are not in use
- Try rebuilding: `Ctrl+Shift+P` → "Dev Containers: Rebuild Container"

### Model Download Issues
- Models are large (several GB) - ensure sufficient disk space
- Download times vary based on internet connection
- Models are cached in the `ollama` Docker volume

### API Connection Problems
- Verify Ollama service is running: `docker compose ps`
- Check network connectivity between services
- Ensure you're using `http://ollama:11434` (not localhost) in API calls

## Performance Notes

- **First Run**: Initial model download can take several minutes
- **Memory**: LLMs require significant RAM (4GB+ recommended)
- **Storage**: Models are cached persistently in Docker volumes
- **CPU**: Better performance on multi-core systems

## Contributing

Feel free to submit issues and enhancement requests! This project is designed to be a starting point for local LLM experimentation.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

MIT License allows you to:
- ✅ Use the code commercially
- ✅ Modify and distribute
- ✅ Use in private projects
- ✅ No warranty or liability

## Resources

- [Ollama Documentation](https://ollama.ai/)
- [Ollama API Reference](https://github.com/ollama/ollama/blob/main/docs/api.md)
- [Dev Containers Documentation](https://containers.dev/)
- [Jupyter Documentation](https://jupyter.org/documentation)
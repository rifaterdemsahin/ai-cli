# 🐳 Container Environments

## Docker Configuration

### Dockerfile
```dockerfile
FROM ubuntu:22.04

# Install dependencies
RUN apt-get update && \
    apt-get install -y jq curl bash && \
    rm -rf /var/lib/apt/lists/*

# Create app directory
WORKDIR /app

# Copy AI CLI scripts
COPY 6_🔣_Symbols/ ./scripts/

# Make scripts executable
RUN chmod +x ./scripts/*.sh

# Set environment variables (override at runtime)
ENV OPENROUTER_API_KEY=""
ENV AI_MODEL="anthropic/claude-3.5-sonnet"

# Create alias for easy access
RUN echo 'alias ai="/app/scripts/ai_linux.sh"' >> ~/.bashrc

ENTRYPOINT ["/bin/bash"]
```

### Docker Compose Configuration
**File**: `docker-compose.yml`
```yaml
version: '3.8'
services:
  ai-cli:
    build: .
    container_name: ai-cli-tool
    environment:
      - OPENROUTER_API_KEY=${OPENROUTER_API_KEY}
      - AI_MODEL=${AI_MODEL:-anthropic/claude-3.5-sonnet}
    volumes:
      - ~/.ai_cli:/root/.ai_cli
      - ~/secondbrain:/root/secondbrain
    stdin_open: true
    tty: true
```

## Docker Usage

### Build and Run
```bash
# Build the container
docker build -t ai-cli .

# Run interactively
docker run -it --rm \
  -e OPENROUTER_API_KEY="your-api-key" \
  -v ~/.ai_cli:/root/.ai_cli \
  -v ~/secondbrain:/root/secondbrain \
  ai-cli

# Or use docker-compose
echo "OPENROUTER_API_KEY=your-api-key" > .env
docker-compose run ai-cli
```

### Environment File
**File**: `.env`
```bash
OPENROUTER_API_KEY=your-openrouter-api-key-here
AI_MODEL=anthropic/claude-3.5-sonnet
```

## Container Best Practices

### Volume Mounts
- Mount `~/.ai_cli` to persist conversation history
- Mount `~/secondbrain` to save conversations
- Use bind mounts for development

### Security
- Don't hardcode API keys in Dockerfile
- Use environment variables or secrets
- Run as non-root user in production

### Networking
- Ensure container has internet access for API calls
- Consider proxy settings for corporate environments
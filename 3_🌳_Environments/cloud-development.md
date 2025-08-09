# ☁️ Cloud Development Environments

## GitHub Codespaces

### DevContainer Configuration
**File**: `.devcontainer/devcontainer.json`
```json
{
    "name": "AI CLI Development",
    "image": "mcr.microsoft.com/devcontainers/base:ubuntu",
    "features": {
        "ghcr.io/devcontainers/features/common-utils:2": {},
        "ghcr.io/devcontainers/features/node:1": {}
    },
    "postCreateCommand": "bash 3_🌳_Environments/codespaces-setup.sh",
    "customizations": {
        "vscode": {
            "extensions": [
                "ms-vscode.powershell",
                "timonwong.shellcheck",
                "foxundermoon.shell-format"
            ]
        }
    },
    "remoteUser": "vscode",
    "portsAttributes": {
        "3000": {
            "label": "Preview",
            "onAutoForward": "openPreview"
        }
    }
}
```

### Post-Create Setup Script
**File**: `codespaces-setup.sh`
```bash
#!/bin/bash
# GitHub Codespaces Post-Create Setup

echo "Setting up AI CLI development environment..."

# Install dependencies
sudo apt update
sudo apt install -y jq curl dos2unix

# Fix line endings
find 6_🔣_Symbols -name "*.sh" -exec dos2unix {} \;

# Make scripts executable
chmod +x 6_🔣_Symbols/*.sh

# Create sample environment file
cat > .env.example << EOF
# Copy this to .env and add your actual API key
OPENROUTER_API_KEY=your-openrouter-api-key-here
AI_MODEL=anthropic/claude-3.5-sonnet
EOF

echo "Setup complete! Remember to:"
echo "1. Copy .env.example to .env"
echo "2. Add your OpenRouter API key"
echo "3. Source the environment: source .env"
```

## Other Cloud Platforms

### Gitpod Configuration
**File**: `.gitpod.yml`
```yaml
tasks:
  - init: |
      sudo apt update
      sudo apt install -y jq curl dos2unix
      chmod +x 6_🔣_Symbols/*.sh
    command: |
      echo "AI CLI development environment ready!"
      echo "Set your OPENROUTER_API_KEY environment variable to get started."

vscode:
  extensions:
    - timonwong.shellcheck
    - foxundermoon.shell-format
```

### Cloud IDE Setup Tips
- Always set API keys as environment variables
- Use post-create scripts for consistent setup
- Include necessary VS Code extensions
- Provide clear setup instructions for users
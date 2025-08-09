# 3_🌳_Environments - Development and Runtime Configurations

## Purpose
This folder contains environment configurations for different development and deployment scenarios. Following the Lacan triad methodology, this represents the structured environments where learning and development occur.

## 🌱 Environment Overview

### Supported Environments
1. **Local Development** - macOS, Linux, Windows
2. **GitHub Codespaces** - Cloud-based development environment
3. **Docker Containers** - Containerized deployment
4. **CI/CD Pipelines** - Automated testing and deployment
5. **WSL (Windows Subsystem for Linux)** - Windows-Linux hybrid environment

## 📁 Environment Configuration Files

### Local Development Environments

#### macOS Configuration
**File**: `macos-setup.sh`
```bash
#!/bin/bash
# macOS Environment Setup for AI CLI

# Install Homebrew if not present
if ! command -v brew &> /dev/null; then
    echo "Installing Homebrew..."
    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
fi

# Install dependencies
echo "Installing dependencies..."
brew install jq curl

# Set up environment variables
echo "Setting up environment..."
echo 'export OPENROUTER_API_KEY="your-api-key-here"' >> ~/.zshrc
echo 'export AI_MODEL="anthropic/claude-3.5-sonnet"' >> ~/.zshrc
echo 'export PATH="$PATH:$(pwd)/6_🔣_Symbols"' >> ~/.zshrc

echo "Setup complete! Please restart your terminal or run: source ~/.zshrc"
```

#### Linux/Ubuntu Configuration
**File**: `linux-setup.sh`
```bash
#!/bin/bash
# Linux Environment Setup for AI CLI

# Update package list
sudo apt update

# Install dependencies
echo "Installing dependencies..."
sudo apt install -y jq curl dos2unix

# Set up environment variables
echo "Setting up environment..."
echo 'export OPENROUTER_API_KEY="your-api-key-here"' >> ~/.bashrc
echo 'export AI_MODEL="anthropic/claude-3.5-sonnet"' >> ~/.bashrc
echo 'export PATH="$PATH:$(pwd)/6_🔣_Symbols"' >> ~/.bashrc

# Fix line endings for WSL compatibility
find ../6_🔣_Symbols -name "*.sh" -exec dos2unix {} \;

echo "Setup complete! Please restart your terminal or run: source ~/.bashrc"
```

#### Windows PowerShell Configuration
**File**: `windows-setup.ps1`
```powershell
# Windows PowerShell Environment Setup for AI CLI

# Check PowerShell version
if ($PSVersionTable.PSVersion.Major -lt 7) {
    Write-Host "PowerShell 7+ required. Please install from: https://github.com/PowerShell/PowerShell/releases"
    exit 1
}

# Set environment variables for current session
$env:OPENROUTER_API_KEY = "your-api-key-here"
$env:AI_MODEL = "anthropic/claude-3.5-sonnet"

# Create PowerShell profile if it doesn't exist
if (!(Test-Path $PROFILE)) {
    New-Item -ItemType File -Path $PROFILE -Force
}

# Add environment variables to profile
Add-Content $PROFILE '$env:OPENROUTER_API_KEY = "your-api-key-here"'
Add-Content $PROFILE '$env:AI_MODEL = "anthropic/claude-3.5-sonnet"'

Write-Host "Setup complete! Environment variables added to PowerShell profile."
Write-Host "Please restart PowerShell or run: . $PROFILE"
```

### Container Environments

#### Docker Configuration
**File**: `Dockerfile`
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

#### Docker Usage Instructions
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

### Cloud Development Environments

#### GitHub Codespaces Configuration
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

### CI/CD Environments

#### GitHub Actions Configuration
**File**: `.github/workflows/test.yml`
```yaml
name: Test AI CLI

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test-bash:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    
    - name: Install dependencies
      run: |
        sudo apt update
        sudo apt install -y jq curl shellcheck
    
    - name: Lint bash scripts
      run: |
        shellcheck 6_🔣_Symbols/ai.sh
        shellcheck 6_🔣_Symbols/ai_linux.sh
    
    - name: Test script syntax
      run: |
        bash -n 6_🔣_Symbols/ai.sh
        bash -n 6_🔣_Symbols/ai_linux.sh

  test-powershell:
    runs-on: windows-latest
    steps:
    - uses: actions/checkout@v3
    
    - name: Test PowerShell script syntax
      run: |
        Get-Command pwsh
        pwsh -NoProfile -Command "& { . ./6_🔣_Symbols/ai.ps1 --help }"
```

## 🔧 Environment-Specific Configurations

### Development Tools Configuration

#### VSCode Settings
**File**: `.vscode/settings.json`
```json
{
    "shellformat.useEditorConfig": true,
    "shellformat.flag": "-i=4 -sr -kp -fn",
    "shellcheck.enable": true,
    "shellcheck.run": "onType",
    "files.associations": {
        "*.sh": "shellscript"
    },
    "terminal.integrated.defaultProfile.windows": "PowerShell",
    "terminal.integrated.profiles.windows": {
        "PowerShell": {
            "source": "PowerShell",
            "args": ["-NoLogo"]
        }
    }
}
```

#### EditorConfig
**File**: `.editorconfig`
```ini
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true
indent_style = space
indent_size = 4

[*.{sh,bash}]
indent_size = 4

[*.ps1]
indent_size = 4

[*.{yml,yaml}]
indent_size = 2

[*.md]
trim_trailing_whitespace = false
```

## 🌍 Platform-Specific Considerations

### macOS
- **Shell**: zsh (default in macOS 10.15+)
- **Package Manager**: Homebrew
- **Path Considerations**: `/usr/local/bin` vs `/opt/homebrew/bin` (Intel vs Apple Silicon)
- **Security**: Gatekeeper may require script approval

### Linux/Ubuntu
- **Shell**: bash
- **Package Manager**: apt
- **Permissions**: May require sudo for installations
- **Line Endings**: Ensure Unix line endings (LF)

### Windows
- **Shell**: PowerShell 7+
- **Execution Policy**: May need `Set-ExecutionPolicy RemoteSigned`
- **Path Separators**: Use backslashes in paths
- **Environment Variables**: Persist through registry or profile

### WSL (Windows Subsystem for Linux)
- **Hybrid Environment**: Windows filesystem accessible via `/mnt/c/`
- **Line Endings**: Convert CRLF to LF using `dos2unix`
- **Performance**: File operations slower on Windows filesystem
- **Integration**: Can call Windows executables from WSL

## 📋 Environment Validation Checklist

### Quick Validation Script
**File**: `validate-environment.sh`
```bash
#!/bin/bash
# Environment Validation Script

echo "🔍 Validating AI CLI Environment..."

# Check dependencies
echo "Checking dependencies..."
command -v jq >/dev/null 2>&1 || { echo "❌ jq not found"; exit 1; }
command -v curl >/dev/null 2>&1 || { echo "❌ curl not found"; exit 1; }
echo "✅ Dependencies OK"

# Check environment variables
if [ -z "$OPENROUTER_API_KEY" ]; then
    echo "⚠️  OPENROUTER_API_KEY not set"
else
    echo "✅ API key configured"
fi

# Check scripts
if [ -x "../6_🔣_Symbols/ai.sh" ]; then
    echo "✅ ai.sh executable"
else
    echo "❌ ai.sh not executable"
fi

# Test API connection (if key is set)
if [ ! -z "$OPENROUTER_API_KEY" ]; then
    echo "Testing API connection..."
    ../6_🔣_Symbols/ai.sh "Hello" >/dev/null 2>&1
    if [ $? -eq 0 ]; then
        echo "✅ API connection successful"
    else
        echo "❌ API connection failed"
    fi
fi

echo "🎉 Environment validation complete!"
```

### Validation Steps
1. **Dependencies**: Verify all required tools are installed
2. **Environment Variables**: Check API keys and configuration
3. **Permissions**: Ensure scripts are executable
4. **Connectivity**: Test API connection
5. **Platform-Specific**: Validate platform-specific requirements

## 🚀 Quick Environment Setup Commands

### One-Line Setup Commands

**macOS/Linux:**
```bash
curl -sSL https://raw.githubusercontent.com/yourusername/ai-cli/main/3_🌳_Environments/setup.sh | bash
```

**Windows PowerShell:**
```powershell
iwr -useb https://raw.githubusercontent.com/yourusername/ai-cli/main/3_🌳_Environments/setup.ps1 | iex
```

**Docker:**
```bash
docker run -it --rm -e OPENROUTER_API_KEY="your-key" yourusername/ai-cli
```

## 📖 Environment Troubleshooting

Common environment issues and solutions are documented in:
- `../7_🌀_Semblance/environment-errors.md`
- Platform-specific troubleshooting guides
- Dependency conflict resolution

## 🔗 Related Resources

- Setup guides: `../2_✈️_Journey/README.md`
- Implementation code: `../6_🔣_Symbols/README.md`
- Error solutions: `../7_🌀_Semblance/README.md`
- Best practices: `../5_📐_Formulas/README.md`
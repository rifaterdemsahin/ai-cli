# 🖥️ Local Development Environments

## macOS Setup

### Quick Setup Script
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

### Platform Considerations
- **Shell**: zsh (default in macOS 10.15+)
- **Package Manager**: Homebrew
- **Path Considerations**: `/usr/local/bin` vs `/opt/homebrew/bin` (Intel vs Apple Silicon)
- **Security**: Gatekeeper may require script approval

## Linux/Ubuntu Setup

### Quick Setup Script
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

### Platform Considerations
- **Shell**: bash
- **Package Manager**: apt
- **Permissions**: May require sudo for installations
- **Line Endings**: Ensure Unix line endings (LF)

## Windows PowerShell Setup

### Quick Setup Script
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

### Platform Considerations
- **Shell**: PowerShell 7+
- **Execution Policy**: May need `Set-ExecutionPolicy RemoteSigned`
- **Path Separators**: Use backslashes in paths
- **Environment Variables**: Persist through registry or profile

## WSL (Windows Subsystem for Linux)

### Special Considerations
- **Hybrid Environment**: Windows filesystem accessible via `/mnt/c/`
- **Line Endings**: Convert CRLF to LF using `dos2unix`
- **Performance**: File operations slower on Windows filesystem
- **Integration**: Can call Windows executables from WSL

### WSL Setup Tips
```bash
# Copy files to WSL filesystem for better performance
cp -r /mnt/c/path/to/ai-cli ~/ai-cli
cd ~/ai-cli

# Fix line endings
find . -name "*.sh" -exec dos2unix {} \;

# Set permissions
chmod +x 6_🔣_Symbols/*.sh
```
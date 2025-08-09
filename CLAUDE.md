# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is an AI CLI tool project that provides a simple command-line interface for interacting with AI models through the OpenRouter API. The project implements cross-platform solutions with context-aware conversation management.

## Project Structure

The repository follows the 7-folder learning methodology:

- **1_🌍_Real/** - Objectives and Key Results (OKRs) - measurable goals and outcomes
- **2_✈️_Journey/** - Setup guides, commands, and learning progression documentation
  - `linux_version.md` - WSL adaptation guide
  - `powershell_version.md` - PowerShell implementation documentation
- **3_🌳_Environments/** - Environment configurations (local, Codespaces, cloud, containers)
- **4_🌌_Imaginary/** - Screenshots, visual documentation, and concept captures
- **5_📐_Formulas/** - Implementation guides, best practices, and reference materials
- **6_🔣_Symbols/** - Executable code, scripts, and practical implementations
  - `ai.sh` - macOS/Unix shell script (primary implementation)
  - `ai_linux.sh` - Linux/WSL optimized version with simplified output
  - `ai.ps1` - Windows PowerShell implementation
- **7_🌀_Semblance/** - Error documentation, troubleshooting, and debugging solutions
- **README.md** - Project documentation and overview
- **CLAUDE.md** - Guidance for Claude Code instances

## Core Architecture

### Context Management System
All implementations share a common context management architecture:

- **Context Storage**: `~/.ai_cli/conversation.json` - Stores conversation history in JSON format
- **Message Structure**: Each message contains `role` (user/assistant) and `content` fields
- **Persistence**: Conversations persist across sessions until explicitly cleared

### API Integration
- **Provider**: OpenRouter API (https://openrouter.ai/api/v1/chat/completions)
- **Default Model**: `anthropic/claude-3.5-sonnet` (configurable via `AI_MODEL` environment variable)
- **Authentication**: Requires `OPENROUTER_API_KEY` environment variable
- **Request Format**: OpenAI-compatible chat completions API

### Command Interface
Common commands across all implementations:
- `ai "question"` - Ask a question with conversation context
- `ai --new "question"` - Start a new conversation
- `ai --context` - Show current conversation history
- `ai --clear` - Clear conversation history
- `ai --save` - Save conversation to second brain (`~/secondbrain/ai_conversations/`)

## Common Development Commands

### Setup and Dependencies

**macOS/Linux:**
```bash
# Install dependencies
brew install jq  # JSON processing
curl --version   # HTTP client (usually pre-installed)

# Set API key
export OPENROUTER_API_KEY='your-api-key'
export AI_MODEL='anthropic/claude-3.5-sonnet'  # optional
```

**Linux/WSL specific:**
```bash
sudo apt update
sudo apt install jq curl

# Convert line endings if edited on Windows
dos2unix ai_linux.sh
chmod +x ai_linux.sh
```

**PowerShell:**
```powershell
# Set API key
$env:OPENROUTER_API_KEY='your-api-key'
$env:AI_MODEL='anthropic/claude-3.5-sonnet'  # optional

# PowerShell 7+ required
# No additional dependencies needed (uses built-in JSON cmdlets)
```

### Running the CLI

**macOS:**
```bash
chmod +x 6_🔣_Symbols/ai.sh
./6_🔣_Symbols/ai.sh "What is the weather like?"
```

**Linux/WSL:**
```bash
chmod +x 6_🔣_Symbols/ai_linux.sh
./6_🔣_Symbols/ai_linux.sh "Explain quantum physics"
```

**Windows PowerShell:**
```powershell
.\6_🔣_Symbols\ai.ps1 "Write a haiku about coding"
```

### Testing and Validation

No automated test suite exists. Manual testing involves:
1. Verifying API connectivity with a simple query
2. Testing context persistence across multiple queries
3. Validating special commands (--new, --context, --clear, --save)
4. Checking error handling for missing API keys or network issues

## Key Implementation Differences

### Shell Script Variants (6_🔣_Symbols/ai.sh vs 6_🔣_Symbols/ai_linux.sh)
- **ai.sh**: Enhanced output with colored visual separators and status messages
- **ai_linux.sh**: Simplified output optimized for WSL environments
- Both support identical functionality and command-line interface

### PowerShell Implementation (6_🔣_Symbols/ai.ps1)
- Uses native PowerShell JSON cmdlets instead of `jq`
- Leverages `Invoke-RestMethod` for HTTP requests instead of `curl`
- Parameter handling uses PowerShell's parameter binding system
- File path handling uses `Join-Path` for cross-platform compatibility

### Error Handling Patterns
- **API Key Validation**: All implementations check for required environment variable
- **Dependency Checking**: Shell scripts verify `jq` availability; PowerShell uses built-in cmdlets
- **Network Error Handling**: Graceful degradation with informative error messages
- **JSON Parsing**: Robust handling of malformed API responses

## Environment Variables

- `OPENROUTER_API_KEY` (required) - API authentication token
- `AI_MODEL` (optional) - Override default model (default: anthropic/claude-3.5-sonnet)

## File Locations

- Context file: `~/.ai_cli/conversation.json`
- Second brain saves: `~/secondbrain/ai_conversations/conversation_YYYYMMDDHHMMSS.md`

## Self-Learning Framework Integration

This project follows the 7-folder learning methodology referenced in README.md:
1. **Real** (🌍) - OKRs and measurable goals for AI CLI development
2. **Journey** (✈️) - Visual story with setup steps and learning progression  
3. **Environments** (🌳) - Cross-platform implementation (macOS, Linux, Windows)
4. **Imaginary** (🌌) - Concept capture through documentation and screenshots
5. **Formulas** (📐) - Implementation guides and best practices
6. **Symbols** (🔣) - Executable code in multiple shell environments
7. **Semblance** (🌀) - Error documentation and troubleshooting solutions
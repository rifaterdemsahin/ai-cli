# 6_🔣_Symbols - Executable Code and Implementations

## Purpose
This folder contains the executable code, scripts, and practical implementations that make the AI CLI tool functional. Following the Lacan triad methodology, this represents the "Symbolic" - the actual code that implements the concepts and patterns.

## 💻 Code Files Overview

### Core Implementations
- **`ai.sh`** - macOS/Unix shell script implementation
- **`ai_linux.sh`** - Linux/WSL optimized version
- **`ai.ps1`** - Windows PowerShell implementation

### File Descriptions

#### ai.sh - macOS/Unix Implementation
**Purpose**: Primary implementation for macOS and Unix-like systems  
**Features**: Enhanced visual output with colored separators and status messages  
**Dependencies**: bash, jq, curl  
**Platform**: macOS, Unix, some Linux distributions

#### ai_linux.sh - Linux/WSL Implementation  
**Purpose**: Optimized for Linux and Windows Subsystem for Linux environments  
**Features**: Simplified output, WSL-compatible formatting  
**Dependencies**: bash, jq, curl  
**Platform**: Linux distributions, WSL

#### ai.ps1 - PowerShell Implementation
**Purpose**: Native Windows PowerShell implementation  
**Features**: PowerShell-native JSON handling, cross-platform PowerShell support  
**Dependencies**: PowerShell 7+  
**Platform**: Windows, PowerShell Core on Linux/macOS

## 🔍 Code Analysis

### Common Architecture Across All Implementations

#### 1. Configuration Management
All implementations follow this pattern:
```bash
# Environment variable with fallback default
MODEL="${AI_MODEL:-anthropic/claude-3.5-sonnet}"
CONTEXT_DIR="${HOME}/.ai_cli"
CONTEXT_FILE="${CONTEXT_DIR}/conversation.json"
```

#### 2. Dependency Validation
```bash
# Check for required tools
if ! command -v jq &> /dev/null; then
    echo "Error: jq is required"
    exit 1
fi
```

#### 3. Context Management Functions
- `init_context()` - Initialize conversation storage
- `add_to_context()` - Add messages to conversation history
- `show_context()` - Display current conversation
- `clear_context()` - Reset conversation history

#### 4. Command-Line Interface
- `--new` - Start new conversation
- `--context` - Show conversation history  
- `--clear` - Clear conversation history
- `--save` - Save conversation to second brain
- `--help` - Show usage information

#### 5. API Integration
- Bearer token authentication
- JSON payload construction
- HTTP request handling
- Response parsing and validation

### Detailed Code Breakdown

#### Shell Script Structure (ai.sh & ai_linux.sh)

**Script Header and Configuration**
```bash
#!/bin/bash

# API Configuration
MODEL="${AI_MODEL:-anthropic/claude-3.5-sonnet}"
CONTEXT_DIR="${HOME}/.ai_cli"
CONTEXT_FILE="${CONTEXT_DIR}/conversation.json"

# Visual styling (ai.sh only)
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
NC='\033[0m'
```

**Dependency Validation**
```bash
# API Key Validation
if [[ -z "$OPENROUTER_API_KEY" ]]; then
    echo -e "${RED}❌ Error: OPENROUTER_API_KEY not set${NC}"
    exit 1
fi

# Tool Availability Check
if ! command -v jq &> /dev/null; then
    echo -e "${RED}❌ Error: jq is required${NC}"
    exit 1
fi
```

**Context Management Implementation**
```bash
init_context() {
    if [[ ! -d "$CONTEXT_DIR" ]]; then
        mkdir -p "$CONTEXT_DIR"
    fi
    
    if [[ ! -f "$CONTEXT_FILE" || "$1" == "new" ]]; then
        echo '{"messages":[]}' > "$CONTEXT_FILE"
    fi
}

add_to_context() {
    local role="$1"
    local content="$2"
    
    jq --arg role "$role" --arg content "$content" \
       '.messages += [{"role": $role, "content": $content}]' \
       "$CONTEXT_FILE" > "${CONTEXT_FILE}.tmp"
    mv "${CONTEXT_FILE}.tmp" "$CONTEXT_FILE"
}
```

**API Request Handling**
```bash
# Create JSON payload with conversation history
JSON_PAYLOAD=$(jq --arg model "$MODEL" \
    '{model: $model, messages: .messages, max_tokens: 4000}' \
    "$CONTEXT_FILE")

# Make API call with proper headers
RESPONSE=$(curl -s https://openrouter.ai/api/v1/chat/completions \
    -H "Authorization: Bearer $OPENROUTER_API_KEY" \
    -H "Content-Type: application/json" \
    -d "$JSON_PAYLOAD")

# Extract and validate response
CONTENT=$(echo "$RESPONSE" | jq -r '.choices[0].message.content // empty')
```

#### PowerShell Structure (ai.ps1)

**Parameter Definition**
```powershell
param(
    [Parameter(ValueFromRemainingArguments = $true)]
    [string[]]$Args
)
```

**Configuration Management**
```powershell
$Model = $env:AI_MODEL ?? "anthropic/claude-3.5-sonnet"
$ContextDir = Join-Path $HOME ".ai_cli"
$ContextFile = Join-Path $ContextDir "conversation.json"
```

**Context Management Functions**
```powershell
function Add-To-Context($role, $content) {
    $context = Get-Content $ContextFile | ConvertFrom-Json
    $context.messages += @{ role = $role; content = $content }
    $context | ConvertTo-Json -Depth 10 | Out-File -Encoding UTF8 $ContextFile
}

function Show-Context {
    $messages = Get-Content $ContextFile | ConvertFrom-Json
    foreach ($msg in $messages.messages) {
        if ($msg.role -eq "user") {
            Write-Host "You: $($msg.content)" -ForegroundColor Yellow
        } else {
            Write-Host "AI: $($msg.content)" -ForegroundColor Green
        }
    }
}
```

**API Integration**
```powershell
$payload = @{
    model = $Model
    messages = $context.messages
    max_tokens = 4000
}

$response = Invoke-RestMethod -Uri "https://openrouter.ai/api/v1/chat/completions" `
    -Headers @{ Authorization = "Bearer $($env:OPENROUTER_API_KEY)"; "Content-Type" = "application/json" } `
    -Method Post `
    -Body ($payload | ConvertTo-Json -Depth 10)
```

## 🚀 Usage Examples

### Basic Usage
```bash
# macOS/Linux
./ai.sh "What is artificial intelligence?"

# Linux/WSL  
./ai_linux.sh "Explain machine learning"

# Windows PowerShell
.\ai.ps1 "What is deep learning?"
```

### Context Management
```bash
# Start new conversation
./ai.sh --new "Hello, I want to learn about AI"

# Continue conversation
./ai.sh "Tell me more about neural networks"

# View conversation history
./ai.sh --context

# Clear conversation
./ai.sh --clear
```

### Advanced Features
```bash
# Save conversation to second brain
./ai.sh --save

# Use different model
export AI_MODEL="openai/gpt-4"
./ai.sh "Compare different AI models"

# Pipe input
echo "Explain quantum computing" | ./ai.sh

# Input from file
./ai.sh < questions.txt
```

## 🔧 Customization and Extension

### Environment Variables
```bash
# Core Configuration
export OPENROUTER_API_KEY="your-api-key"
export AI_MODEL="anthropic/claude-3.5-sonnet"

# Advanced Configuration
export AI_MAX_TOKENS="4000"
export AI_TEMPERATURE="0.7"
export AI_CONTEXT_DIR="$HOME/.config/ai-cli"
```

### Script Modifications

#### Adding New Commands
```bash
# Add to command parsing section
case "$1" in
    "--new") NEW_CONVERSATION=true ;;
    "--context") SHOW_CONTEXT=true ;;
    "--clear") CLEAR_CONTEXT=true ;;
    "--save") SAVE_TO_SECONDBRAIN=true ;;
    "--custom") CUSTOM_COMMAND=true ;;  # New command
    *) INPUT="$*" ;;
esac
```

#### Custom Output Formatting
```bash
# Modify response display
format_response() {
    local content="$1"
    
    # Custom formatting logic here
    echo -e "${GREEN}🤖 AI Response:${NC}"
    echo "$content" | fold -w 80 -s  # Wrap text at 80 characters
    echo ""
}
```

#### Adding Model Presets
```bash
# Model preset function
set_model_preset() {
    case "$1" in
        "creative") MODEL="openai/gpt-4" ;;
        "analytical") MODEL="anthropic/claude-3.5-sonnet" ;;
        "fast") MODEL="openai/gpt-3.5-turbo" ;;
        *) echo "Unknown preset: $1" ;;
    esac
}
```

## 🧪 Testing and Validation

### Unit Testing Approach
```bash
# Test script: test_ai_cli.sh
#!/bin/bash

# Test dependency validation
test_dependency_check() {
    # Temporarily rename jq to test error handling
    if PATH="/tmp:$PATH" ./ai.sh "test" 2>&1 | grep -q "jq is required"; then
        echo "✅ Dependency check works"
    else
        echo "❌ Dependency check failed"
    fi
}

# Test context management
test_context_management() {
    # Clean test environment
    rm -f ~/.ai_cli/conversation.json
    
    # Test context initialization
    if ./ai.sh --new "test" && [[ -f ~/.ai_cli/conversation.json ]]; then
        echo "✅ Context initialization works"
    else
        echo "❌ Context initialization failed"
    fi
}

# Test API key validation
test_api_validation() {
    # Test without API key
    if OPENROUTER_API_KEY="" ./ai.sh "test" 2>&1 | grep -q "API key"; then
        echo "✅ API key validation works"
    else
        echo "❌ API key validation failed"
    fi
}

# Run all tests
run_tests() {
    echo "Running AI CLI tests..."
    test_dependency_check
    test_context_management
    test_api_validation
    echo "Testing complete"
}

run_tests
```

### Manual Testing Checklist
- [ ] **Basic functionality**: Simple query and response
- [ ] **Context management**: --new, --context, --clear commands work
- [ ] **Error handling**: Graceful failure with helpful messages
- [ ] **Cross-platform**: Same functionality on different platforms
- [ ] **API integration**: Proper authentication and response handling
- [ ] **File operations**: Context file creation and management
- [ ] **Input handling**: Command line args, piped input, file input

### Performance Testing
```bash
# Performance test script
#!/bin/bash

# Test response time
time_test() {
    echo "Testing response time..."
    time ./ai.sh "Hello"
}

# Test with long input
long_input_test() {
    echo "Testing with long input..."
    long_text=$(head -c 2000 /dev/urandom | base64)
    ./ai.sh "$long_text"
}

# Test context file size growth
context_growth_test() {
    echo "Testing context file growth..."
    for i in {1..10}; do
        ./ai.sh "Message $i"
        echo "Context file size: $(wc -c < ~/.ai_cli/conversation.json) bytes"
    done
}
```

## 🔒 Security Considerations

### API Key Handling
```bash
# Secure API key validation
validate_api_key() {
    local key="$OPENROUTER_API_KEY"
    
    # Check presence
    [[ -n "$key" ]] || {
        echo "❌ API key not configured" >&2
        return 1
    }
    
    # Basic format validation (don't expose actual key)
    if [[ ${#key} -lt 20 ]]; then
        echo "⚠️  API key appears too short" >&2
        return 1
    fi
    
    # Never log the actual key value
    echo "✅ API key configured (${#key} characters)" >&2
}
```

### Input Sanitization
```bash
# Sanitize user input
sanitize_input() {
    local input="$1"
    
    # Remove potential command injection characters
    input=$(echo "$input" | tr -d '`$(){}[];|&<>')
    
    # Limit input length
    if [[ ${#input} -gt 4000 ]]; then
        echo "❌ Input too long (max 4000 characters)" >&2
        return 1
    fi
    
    echo "$input"
}
```

### File Security
```bash
# Secure file operations
secure_file_write() {
    local file="$1"
    local content="$2"
    local temp_file="${file}.tmp.$$"
    
    # Create with restricted permissions
    (umask 077; echo "$content" > "$temp_file")
    
    # Atomic move
    mv "$temp_file" "$file"
}
```

## 📊 Code Metrics

### Lines of Code
- **ai.sh**: ~260 lines
- **ai_linux.sh**: ~260 lines  
- **ai.ps1**: ~140 lines
- **Total**: ~660 lines

### Function Count
- **Shell scripts**: 8 functions each
- **PowerShell script**: 5 functions
- **Common patterns**: Context management, API calls, error handling

### Complexity Analysis
- **Cyclomatic Complexity**: Low to medium (2-6 per function)
- **Maintainability Index**: High (consistent patterns, clear separation)
- **Code Duplication**: Minimal (platform-specific variations only)

## 🚀 Deployment Considerations

### Distribution Methods
1. **Direct Download**: Individual script files
2. **Package Managers**: Homebrew formula, apt package
3. **Container Images**: Docker images with scripts pre-installed
4. **Installer Scripts**: Automated setup and configuration

### Version Management
```bash
# Version information in scripts
readonly VERSION="1.0.0"
readonly BUILD_DATE="2024-12-01"

show_version() {
    echo "AI CLI Tool v$VERSION (built $BUILD_DATE)"
    echo "Platform: $(uname -s)"
    echo "Shell: $SHELL"
}
```

### Update Mechanism
```bash
# Self-update functionality
update_script() {
    local script_url="https://raw.githubusercontent.com/user/ai-cli/main/6_🔣_Symbols/ai.sh"
    local current_script="$0"
    local temp_script="${current_script}.new"
    
    echo "Downloading latest version..."
    if curl -sSL "$script_url" -o "$temp_script"; then
        chmod +x "$temp_script"
        mv "$temp_script" "$current_script"
        echo "✅ Update completed"
    else
        echo "❌ Update failed"
        rm -f "$temp_script"
    fi
}
```

## 🔗 Integration Points

### With Other Folders
- **Real Folder**: Code implements the objectives and key results
- **Journey Folder**: Scripts support the learning progression  
- **Environments Folder**: Code runs in configured environments
- **Imaginary Folder**: Screenshots show code execution
- **Formulas Folder**: Code follows established patterns and practices
- **Semblance Folder**: Code generates errors documented in troubleshooting

### External Integrations
- **Second Brain**: Conversation export to markdown
- **OpenRouter API**: AI model access and management
- **Shell Environment**: Environment variable configuration
- **File System**: Context storage and management

## 📖 Development Guidelines

### Contributing to Code
1. **Follow Patterns**: Use established architecture patterns from Formulas folder
2. **Test Thoroughly**: Ensure changes work across all platforms  
3. **Document Changes**: Update relevant README files
4. **Maintain Compatibility**: Keep consistent CLI interface
5. **Security First**: Never expose API keys or sensitive data

### Code Review Checklist
- [ ] Follows established patterns and conventions
- [ ] Includes proper error handling
- [ ] Works on all target platforms
- [ ] Maintains backward compatibility
- [ ] Includes appropriate documentation
- [ ] Handles edge cases gracefully
- [ ] Follows security best practices
- [ ] Performance considerations addressed
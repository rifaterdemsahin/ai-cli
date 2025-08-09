# 5_📐_Formulas - Implementation Guides and Best Practices

## Purpose
This folder contains implementation guides, best practices, architectural patterns, and reference materials that serve as "formulas" for understanding and extending the AI CLI project. Following the Lacan triad methodology, this represents the structured knowledge and patterns that govern the system.

## 📚 Formula Categories

### 1. **Architecture Patterns** - Core system design principles
### 2. **Implementation Guides** - Step-by-step technical implementations
### 3. **Best Practices** - Proven approaches and standards
### 4. **API Integration Patterns** - Reusable API interaction patterns
### 5. **Cross-Platform Strategies** - Multi-platform development approaches
### 6. **Security Guidelines** - Security implementation standards
### 7. **Performance Optimization** - Efficiency and optimization techniques

## 📁 Folder Structure

```
5_📐_Formulas/
├── README.md
├── architecture-patterns/
│   ├── context-management-pattern.md
│   ├── api-integration-pattern.md
│   └── cross-platform-pattern.md
├── implementation-guides/
│   ├── script-analysis.md
│   ├── json-processing.md
│   └── error-handling.md
├── best-practices/
│   ├── shell-scripting-standards.md
│   ├── powershell-conventions.md
│   └── api-security.md
├── patterns/
│   ├── command-line-interface.md
│   ├── configuration-management.md
│   └── logging-strategies.md
└── reference/
    ├── api-documentation.md
    ├── tool-comparisons.md
    └── extension-guidelines.md
```

## 🏗️ Architecture Patterns

### Context Management Pattern
**File**: `architecture-patterns/context-management-pattern.md`

#### Pattern Overview
The AI CLI implements a persistent context management system that maintains conversation history across sessions using JSON-based storage.

#### Core Components
1. **Context Storage**: `~/.ai_cli/conversation.json`
2. **Context Manager Functions**: Initialize, read, write, clear
3. **Message Structure**: Role-based message format (user/assistant)
4. **Persistence Layer**: File-based JSON storage

#### Implementation Formula
```bash
# Context Management Pattern Structure
init_context() {
    # 1. Check directory existence
    # 2. Create if missing
    # 3. Initialize empty context if needed
}

add_to_context() {
    # 1. Load existing context
    # 2. Append new message
    # 3. Save updated context
}

show_context() {
    # 1. Read context file
    # 2. Parse JSON structure
    # 3. Display formatted output
}
```

#### Benefits
- **Persistence**: Conversations survive session restarts
- **Flexibility**: Easy to extend with additional metadata
- **Portability**: JSON format works across all platforms
- **Debuggability**: Human-readable conversation history

#### Usage Examples
```bash
# Initialize new conversation
ai --new "Start fresh conversation"

# View conversation history
ai --context

# Clear conversation
ai --clear
```

### API Integration Pattern
**File**: `architecture-patterns/api-integration-pattern.md`

#### Pattern Overview
Standardized approach to integrating with OpenRouter API using REST principles and proper error handling.

#### Core Components
1. **Authentication**: Bearer token authentication
2. **Request Formation**: JSON payload construction
3. **Response Handling**: JSON parsing and validation
4. **Error Management**: Graceful degradation and user feedback

#### Implementation Formula
```bash
# API Integration Pattern
make_api_call() {
    # 1. Validate prerequisites (API key, network)
    # 2. Construct request payload
    # 3. Make HTTP request with proper headers
    # 4. Parse and validate response
    # 5. Handle errors gracefully
    # 6. Return processed result
}
```

### Cross-Platform Pattern
**File**: `architecture-patterns/cross-platform-pattern.md`

#### Pattern Overview
Strategy for maintaining consistent functionality across different operating systems and shell environments.

#### Core Components
1. **Platform Detection**: Automatic platform identification
2. **Conditional Logic**: Platform-specific implementations
3. **Common Interface**: Uniform command-line interface
4. **Shared Core Logic**: Platform-agnostic business logic

#### Implementation Formula
```bash
# Cross-Platform Pattern Structure
detect_platform() {
    # 1. Check OS type
    # 2. Check shell environment
    # 3. Set platform-specific variables
}

execute_platform_specific() {
    case $PLATFORM in
        "macos") macos_implementation ;;
        "linux") linux_implementation ;;
        "windows") windows_implementation ;;
    esac
}
```

## 🛠️ Implementation Guides

### Script Analysis Guide
**File**: `implementation-guides/script-analysis.md`

#### Shell Script Structure Analysis

##### Core Structure Pattern
```bash
#!/bin/bash

# 1. CONFIGURATION SECTION
MODEL="${AI_MODEL:-default-model}"
CONTEXT_DIR="${HOME}/.ai_cli"
CONTEXT_FILE="${CONTEXT_DIR}/conversation.json"

# 2. DEPENDENCY VALIDATION
check_dependencies() {
    command -v jq >/dev/null 2>&1 || { echo "Error: jq required"; exit 1; }
    command -v curl >/dev/null 2>&1 || { echo "Error: curl required"; exit 1; }
}

# 3. UTILITY FUNCTIONS
show_help() { ... }
init_context() { ... }
add_to_context() { ... }

# 4. MAIN LOGIC
parse_arguments() { ... }
validate_environment() { ... }
execute_main_function() { ... }

# 5. EXECUTION FLOW
main() {
    check_dependencies
    parse_arguments "$@"
    validate_environment
    execute_main_function
}

# 6. SCRIPT ENTRY POINT
main "$@"
```

##### Key Implementation Patterns

**Configuration Management**
```bash
# Pattern: Environment Variable with Defaults
MODEL="${AI_MODEL:-anthropic/claude-3.5-sonnet}"
TIMEOUT="${API_TIMEOUT:-30}"
MAX_TOKENS="${MAX_TOKENS:-4000}"

# Pattern: XDG Base Directory Specification
CONFIG_DIR="${XDG_CONFIG_HOME:-$HOME/.config}/ai_cli"
DATA_DIR="${XDG_DATA_HOME:-$HOME/.local/share}/ai_cli"
```

**Error Handling Pattern**
```bash
# Pattern: Early Exit with Clear Messages
check_api_key() {
    if [[ -z "$OPENROUTER_API_KEY" ]]; then
        echo "❌ Error: OPENROUTER_API_KEY not set" >&2
        echo "💡 Set it with: export OPENROUTER_API_KEY='your-key'" >&2
        exit 1
    fi
}

# Pattern: Command Validation
validate_command() {
    if ! command -v "$1" &> /dev/null; then
        echo "❌ Error: $1 is required but not installed" >&2
        echo "💡 Install with: brew install $1" >&2
        return 1
    fi
}
```

### JSON Processing Guide
**File**: `implementation-guides/json-processing.md`

#### jq Processing Patterns

**Reading Context File**
```bash
# Pattern: Safe JSON Reading with Fallback
read_context() {
    if [[ -f "$CONTEXT_FILE" ]]; then
        jq -r '.messages[]' "$CONTEXT_FILE" 2>/dev/null || {
            echo '{"messages":[]}' > "$CONTEXT_FILE"
        }
    else
        echo '{"messages":[]}'
    fi
}
```

**Adding Messages to Context**
```bash
# Pattern: Atomic JSON Updates
add_message() {
    local role="$1"
    local content="$2"
    local temp_file="${CONTEXT_FILE}.tmp"
    
    jq --arg role "$role" --arg content "$content" \
       '.messages += [{"role": $role, "content": $content}]' \
       "$CONTEXT_FILE" > "$temp_file" && mv "$temp_file" "$CONTEXT_FILE"
}
```

**API Payload Construction**
```bash
# Pattern: Dynamic Payload Building
build_payload() {
    jq -n \
        --arg model "$MODEL" \
        --argjson messages "$(jq '.messages' "$CONTEXT_FILE")" \
        --arg max_tokens "$MAX_TOKENS" \
        '{
            model: $model,
            messages: $messages,
            max_tokens: ($max_tokens | tonumber)
        }'
}
```

### Error Handling Guide
**File**: `implementation-guides/error-handling.md`

#### Error Handling Hierarchy

**Level 1: Validation Errors**
- Missing dependencies
- Invalid configuration
- Missing required files

**Level 2: Runtime Errors**
- Network connectivity issues
- API authentication failures
- File permission problems

**Level 3: Response Errors**
- API rate limiting
- Invalid API responses
- JSON parsing failures

#### Error Handling Patterns

**Graceful Degradation Pattern**
```bash
handle_api_error() {
    local response="$1"
    local error_message
    
    # Try to extract error message from API response
    error_message=$(echo "$response" | jq -r '.error.message // "Unknown API error"')
    
    # Provide helpful context
    case "$error_message" in
        *"authentication"*)
            echo "❌ Authentication failed. Please check your API key."
            echo "💡 Verify: echo \$OPENROUTER_API_KEY"
            ;;
        *"rate limit"*)
            echo "⏳ Rate limit exceeded. Please wait and try again."
            echo "💡 Consider upgrading your API plan for higher limits."
            ;;
        *"network"*)
            echo "🌐 Network error. Please check your connection."
            echo "💡 Try: curl -I https://openrouter.ai/"
            ;;
        *)
            echo "❌ API Error: $error_message"
            echo "💡 Check the API documentation for details."
            ;;
    esac
}
```

**Retry Logic Pattern**
```bash
retry_with_backoff() {
    local max_attempts=3
    local delay=1
    local attempt=1
    
    while [[ $attempt -le $max_attempts ]]; do
        if execute_api_call; then
            return 0
        fi
        
        echo "⏳ Attempt $attempt failed, retrying in ${delay}s..."
        sleep $delay
        delay=$((delay * 2))
        attempt=$((attempt + 1))
    done
    
    echo "❌ All attempts failed"
    return 1
}
```

## 📖 Best Practices

### Shell Scripting Standards
**File**: `best-practices/shell-scripting-standards.md`

#### Code Organization Standards
```bash
# Pattern: Clear Section Separation
#############################################
# CONFIGURATION AND CONSTANTS
#############################################
readonly SCRIPT_NAME="$(basename "$0")"
readonly VERSION="1.0.0"

#############################################
# UTILITY FUNCTIONS
#############################################
log_info() { echo "ℹ️  $*" >&2; }
log_error() { echo "❌ $*" >&2; }

#############################################
# MAIN BUSINESS LOGIC
#############################################
process_user_input() { ... }
```

#### Variable Naming Conventions
```bash
# Constants (readonly)
readonly API_BASE_URL="https://openrouter.ai/api/v1"
readonly DEFAULT_MODEL="anthropic/claude-3.5-sonnet"

# Environment variables
MODEL="${AI_MODEL:-$DEFAULT_MODEL}"
API_KEY="${OPENROUTER_API_KEY}"

# Local variables (lowercase)
local user_input="$1"
local temp_file="/tmp/ai_cli_$$.tmp"

# Function names (verb_noun pattern)
validate_api_key() { ... }
format_response() { ... }
cleanup_temp_files() { ... }
```

#### Error Handling Standards
```bash
# Pattern: Consistent Error Messaging
error_exit() {
    local message="$1"
    local exit_code="${2:-1}"
    echo "❌ Error: $message" >&2
    echo "💡 Run '$SCRIPT_NAME --help' for usage information" >&2
    exit "$exit_code"
}

# Pattern: Input Validation
validate_input() {
    [[ -n "$1" ]] || error_exit "Input cannot be empty"
    [[ ${#1} -le 4000 ]] || error_exit "Input too long (max 4000 characters)"
}
```

### PowerShell Conventions
**File**: `best-practices/powershell-conventions.md`

#### PowerShell Best Practices

**Parameter Handling**
```powershell
[CmdletBinding()]
param(
    [Parameter(Mandatory=$true, ValueFromPipeline=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$InputText,
    
    [Parameter(Mandatory=$false)]
    [ValidateSet("new", "context", "clear", "save")]
    [string]$Mode = "query",
    
    [Parameter(Mandatory=$false)]
    [ValidateNotNullOrEmpty()]
    [string]$Model = $env:AI_MODEL ?? "anthropic/claude-3.5-sonnet"
)
```

**Error Handling Pattern**
```powershell
function Invoke-ApiCall {
    [CmdletBinding()]
    param([string]$Payload)
    
    try {
        $response = Invoke-RestMethod -Uri $ApiUrl -Method Post -Body $Payload -Headers $headers
        return $response
    }
    catch [System.Net.WebException] {
        Write-Error "Network error: $($_.Exception.Message)"
        throw
    }
    catch {
        Write-Error "Unexpected error: $($_.Exception.Message)"
        throw
    }
}
```

### API Security Best Practices
**File**: `best-practices/api-security.md`

#### Security Implementation Patterns

**API Key Management**
```bash
# Pattern: Secure API Key Validation
validate_api_key() {
    local key="$OPENROUTER_API_KEY"
    
    # Check if key is set
    [[ -n "$key" ]] || {
        echo "❌ API key not configured" >&2
        return 1
    }
    
    # Check key format (basic validation)
    [[ "$key" =~ ^sk-[a-zA-Z0-9_-]+$ ]] || {
        echo "⚠️  API key format appears invalid" >&2
        return 1
    }
    
    # Never log or expose the actual key
    log_info "API key configured (${#key} characters)"
}
```

**Request Security**
```bash
# Pattern: Secure HTTP Requests
make_secure_request() {
    local payload="$1"
    
    # Use secure headers
    local headers=(
        "Authorization: Bearer $OPENROUTER_API_KEY"
        "Content-Type: application/json"
        "User-Agent: AI-CLI/1.0.0"
        "X-Title: AI CLI Tool"
    )
    
    # Make request with timeout
    curl -s --max-time 30 \
         --fail-with-body \
         "${headers[@]/#/-H }" \
         -d "$payload" \
         "$API_URL"
}
```

## 🔧 Extension Patterns

### Plugin Architecture Pattern
**File**: `patterns/plugin-architecture.md`

#### Extensible Command System
```bash
# Pattern: Plugin Discovery and Loading
load_plugins() {
    local plugin_dir="${CONFIG_DIR}/plugins"
    
    if [[ -d "$plugin_dir" ]]; then
        for plugin in "$plugin_dir"/*.sh; do
            if [[ -f "$plugin" && -x "$plugin" ]]; then
                source "$plugin"
                log_info "Loaded plugin: $(basename "$plugin")"
            fi
        done
    fi
}

# Pattern: Plugin Interface
# Each plugin should implement these functions:
plugin_name() { echo "plugin-name"; }
plugin_description() { echo "Plugin description"; }
plugin_commands() { echo "command1 command2"; }
plugin_execute() { ... }
```

### Configuration Management Pattern
**File**: `patterns/configuration-management.md`

#### Hierarchical Configuration
```bash
# Pattern: Configuration Precedence
# 1. Command line arguments
# 2. Environment variables  
# 3. User config file
# 4. System config file
# 5. Built-in defaults

load_config() {
    # Built-in defaults
    MODEL="anthropic/claude-3.5-sonnet"
    MAX_TOKENS=4000
    TIMEOUT=30
    
    # System config
    [[ -f "/etc/ai-cli/config" ]] && source "/etc/ai-cli/config"
    
    # User config
    [[ -f "$HOME/.config/ai-cli/config" ]] && source "$HOME/.config/ai-cli/config"
    
    # Environment variables
    MODEL="${AI_MODEL:-$MODEL}"
    MAX_TOKENS="${AI_MAX_TOKENS:-$MAX_TOKENS}"
    
    # Command line arguments override everything
    while [[ $# -gt 0 ]]; do
        case $1 in
            --model) MODEL="$2"; shift 2 ;;
            --max-tokens) MAX_TOKENS="$2"; shift 2 ;;
            *) shift ;;
        esac
    done
}
```

## 📊 Performance Optimization Formulas

### Caching Strategy
**File**: `patterns/caching-strategy.md`

#### Response Caching Pattern
```bash
# Pattern: Simple Response Cache
cache_response() {
    local query_hash
    local cache_file
    
    query_hash=$(echo "$1" | sha256sum | cut -d' ' -f1)
    cache_file="${CACHE_DIR}/${query_hash}.json"
    
    if [[ -f "$cache_file" && $(find "$cache_file" -mmin -60) ]]; then
        # Cache hit (less than 1 hour old)
        cat "$cache_file"
        return 0
    fi
    
    # Cache miss - make API call and cache result
    local response
    response=$(make_api_call "$1")
    echo "$response" > "$cache_file"
    echo "$response"
}
```

### Optimization Techniques
```bash
# Pattern: Batch Processing
process_multiple_queries() {
    local queries=("$@")
    local batch_size=5
    
    for ((i=0; i<${#queries[@]}; i+=batch_size)); do
        local batch=("${queries[@]:i:batch_size}")
        process_batch "${batch[@]}" &
    done
    
    wait # Wait for all background processes
}

# Pattern: Resource Cleanup
cleanup_resources() {
    trap cleanup_resources EXIT
    
    # Clean up temporary files
    [[ -n "$TEMP_DIR" ]] && rm -rf "$TEMP_DIR"
    
    # Kill background processes
    jobs -p | xargs -r kill
}
```

## 📚 Reference Materials

### API Documentation Reference
**File**: `reference/api-documentation.md`

#### OpenRouter API Quick Reference
```bash
# Endpoint: https://openrouter.ai/api/v1/chat/completions
# Method: POST
# Headers:
#   Authorization: Bearer $OPENROUTER_API_KEY
#   Content-Type: application/json

# Request Format:
{
    "model": "anthropic/claude-3.5-sonnet",
    "messages": [
        {"role": "user", "content": "Hello"},
        {"role": "assistant", "content": "Hi there!"},
        {"role": "user", "content": "How are you?"}
    ],
    "max_tokens": 4000,
    "temperature": 0.7,
    "stream": false
}

# Response Format:
{
    "choices": [
        {
            "message": {
                "role": "assistant", 
                "content": "Response text here"
            }
        }
    ]
}
```

### Tool Comparisons
**File**: `reference/tool-comparisons.md`

#### Alternative Implementations
| Feature | Our Implementation | Alternative A | Alternative B |
|---------|-------------------|---------------|---------------|
| Context Management | JSON file | SQLite database | Memory only |
| Platform Support | Bash + PowerShell | Python | Node.js |
| API Integration | Direct HTTP | SDK wrapper | GraphQL |
| Configuration | Environment vars | Config file | CLI params |

## 🎯 Formula Application Checklist

### Implementation Validation
- [ ] **Architecture Patterns Applied**
  - [ ] Context management follows standard pattern
  - [ ] API integration uses established pattern
  - [ ] Cross-platform pattern implemented consistently

- [ ] **Best Practices Followed**
  - [ ] Shell scripting standards applied
  - [ ] Error handling implemented properly
  - [ ] Security guidelines followed
  - [ ] Performance optimizations applied

- [ ] **Code Quality Standards**
  - [ ] Consistent naming conventions
  - [ ] Proper documentation
  - [ ] Error messages are helpful
  - [ ] Code is maintainable

### Extension Readiness
- [ ] **Plugin Architecture**
  - [ ] Plugin loading system implemented
  - [ ] Standard plugin interface defined
  - [ ] Example plugins available

- [ ] **Configuration Management**
  - [ ] Hierarchical config system
  - [ ] Environment variable support
  - [ ] User customization options

## 🔗 Integration Points

### Cross-Folder References
- **Real Folder**: Formulas support objective achievement
- **Journey Folder**: Implementation guides support learning path
- **Environments Folder**: Best practices guide environment setup
- **Symbols Folder**: Patterns are implemented in actual code
- **Semblance Folder**: Error patterns help with troubleshooting

### Usage in Development
1. **Planning Phase**: Reference architecture patterns
2. **Implementation Phase**: Follow implementation guides
3. **Quality Assurance**: Apply best practices checklist
4. **Extension Phase**: Use extension patterns
5. **Maintenance Phase**: Follow optimization formulas
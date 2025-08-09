# 7_🌀_Semblance - Error Documentation and Troubleshooting Solutions

## Purpose
This folder contains comprehensive error documentation, troubleshooting guides, and debugging solutions. Following the Lacan triad methodology, this represents the "Semblance" - where errors and their resolutions create learning opportunities and system improvements.

## 🚨 Error Categories

### 1. **Environment Setup Errors** - Issues during initial setup and configuration
### 2. **Dependency Errors** - Missing or incompatible dependencies
### 3. **API Integration Errors** - Problems with OpenRouter API connectivity
### 4. **Authentication Errors** - API key and authorization issues
### 5. **Runtime Errors** - Execution-time failures and unexpected behaviors
### 6. **Platform-Specific Errors** - Operating system and shell-specific issues
### 7. **Context Management Errors** - Problems with conversation persistence
### 8. **Network Errors** - Connectivity and timeout issues

## 📁 Folder Structure

```
7_🌀_Semblance/
├── README.md
├── common-errors/
│   ├── setup-errors.md
│   ├── dependency-errors.md
│   ├── api-errors.md
│   └── runtime-errors.md
├── platform-specific/
│   ├── macos-issues.md
│   ├── linux-issues.md
│   ├── windows-issues.md
│   └── wsl-issues.md
├── troubleshooting-guides/
│   ├── diagnostic-steps.md
│   ├── error-patterns.md
│   └── recovery-procedures.md
└── solutions/
    ├── quick-fixes.md
    ├── advanced-solutions.md
    └── prevention-strategies.md
```

## 🔧 Common Error Scenarios and Solutions

### Environment Setup Errors

#### Error: "OPENROUTER_API_KEY not set"
**Symptom**: Script exits immediately with API key error message
**Cause**: Environment variable not configured
**Solution**:
```bash
# macOS/Linux
export OPENROUTER_API_KEY="your-api-key-here"
echo 'export OPENROUTER_API_KEY="your-api-key-here"' >> ~/.bashrc

# Windows PowerShell
$env:OPENROUTER_API_KEY="your-api-key-here"
# For persistence, add to PowerShell profile
```

**Prevention**: Always verify environment setup with validation script
**Related Files**: `../3_🌳_Environments/validate-environment.sh`

#### Error: "Permission denied"
**Symptom**: Script cannot execute, shows permission error
**Cause**: Script files are not executable
**Solution**:
```bash
# Make scripts executable
chmod +x 6_🔣_Symbols/ai.sh
chmod +x 6_🔣_Symbols/ai_linux.sh

# For all shell scripts in project
find . -name "*.sh" -exec chmod +x {} \;
```

**Prevention**: Include permission setup in installation scripts
**Platform Notes**: Windows doesn't require chmod, but may need execution policy changes

### Dependency Errors

#### Error: "jq: command not found"
**Symptom**: Script fails when trying to process JSON
**Cause**: jq JSON processor not installed
**Solution**:
```bash
# macOS
brew install jq

# Linux/Ubuntu
sudo apt update && sudo apt install jq

# Windows (using Chocolatey)
choco install jq

# Windows (using Scoop)
scoop install jq
```

**Verification**:
```bash
jq --version
```

**Alternative Solution**: Use Python as fallback JSON processor
```bash
# Fallback function if jq is not available
parse_json_python() {
    python3 -c "import json,sys; print(json.loads(sys.stdin.read())['$1'])"
}
```

#### Error: "curl: command not found"
**Symptom**: HTTP requests fail, curl not found
**Cause**: curl HTTP client not installed
**Solution**:
```bash
# macOS (usually pre-installed)
brew install curl

# Linux/Ubuntu
sudo apt update && sudo apt install curl

# Windows (PowerShell uses Invoke-RestMethod, curl not needed)
```

**Alternative**: Use platform-native HTTP tools
```bash
# Use wget as curl alternative
wget -qO- --header="Content-Type: application/json" \
     --post-data="$JSON_PAYLOAD" \
     "$API_URL"
```

### API Integration Errors

#### Error: "API Error: Invalid API key"
**Symptom**: Authentication fails with 401 Unauthorized
**Cause**: Invalid, expired, or malformed API key
**Troubleshooting Steps**:
1. **Verify Key Format**:
   ```bash
   echo $OPENROUTER_API_KEY | head -c 20
   # Should start with 'sk-' for OpenRouter
   ```

2. **Test Key Manually**:
   ```bash
   curl -H "Authorization: Bearer $OPENROUTER_API_KEY" \
        https://openrouter.ai/api/v1/models
   ```

3. **Check Key Status**: Log into OpenRouter dashboard to verify key is active

**Solution**: Generate new API key from OpenRouter dashboard
**Prevention**: Regularly rotate API keys and monitor usage

#### Error: "API Error: Rate limit exceeded"
**Symptom**: HTTP 429 status code, rate limiting message
**Cause**: Too many requests in short time period
**Solution**:
```bash
# Add retry logic with exponential backoff
retry_with_backoff() {
    local max_attempts=3
    local delay=1
    
    for attempt in $(seq 1 $max_attempts); do
        if make_api_call; then
            return 0
        fi
        
        echo "Rate limited, waiting ${delay}s..."
        sleep $delay
        delay=$((delay * 2))
    done
    
    echo "Max retries exceeded"
    return 1
}
```

**Prevention**: Implement request throttling and caching

#### Error: "Network timeout"
**Symptom**: Long delays followed by timeout errors
**Cause**: Network connectivity issues or API slowness
**Diagnostic Commands**:
```bash
# Test basic connectivity
ping -c 3 openrouter.ai

# Test HTTPS connectivity
curl -I https://openrouter.ai/

# Check DNS resolution
nslookup openrouter.ai
```

**Solution**:
```bash
# Increase timeout in curl command
curl --max-time 60 --connect-timeout 30 \
     -H "Authorization: Bearer $OPENROUTER_API_KEY" \
     https://openrouter.ai/api/v1/chat/completions
```

### Platform-Specific Errors

#### macOS: "Operation not permitted"
**Symptom**: Script fails with permission errors on macOS Catalina+
**Cause**: macOS Gatekeeper security restrictions
**Solution**:
1. **Allow script execution**:
   ```bash
   # Remove quarantine attribute
   xattr -d com.apple.quarantine 6_🔣_Symbols/ai.sh
   
   # Or grant full disk access to Terminal in Privacy settings
   ```

2. **Alternative**: Move script to user directory
   ```bash
   cp 6_🔣_Symbols/ai.sh ~/bin/ai
   chmod +x ~/bin/ai
   ```

#### Linux: "bad interpreter: No such file or directory"
**Symptom**: Script fails to execute with interpreter error
**Cause**: Incorrect line endings (Windows CRLF vs Unix LF)
**Solution**:
```bash
# Convert line endings
dos2unix 6_🔣_Symbols/ai.sh
dos2unix 6_🔣_Symbols/ai_linux.sh

# Verify shebang line
head -1 6_🔣_Symbols/ai.sh
# Should show: #!/bin/bash
```

**Prevention**: Use Unix line endings when editing on Windows

#### Windows: "Execution of scripts is disabled on this system"
**Symptom**: PowerShell script cannot execute due to execution policy
**Cause**: PowerShell execution policy restrictions
**Solution**:
```powershell
# Check current policy
Get-ExecutionPolicy

# Set policy for current user
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser

# Or bypass for specific script
powershell -ExecutionPolicy Bypass -File .\6_🔣_Symbols\ai.ps1
```

**Alternative**: Sign the script or run in unrestricted context

#### WSL: "Input/output error"
**Symptom**: File operations fail in WSL environment
**Cause**: Windows filesystem permissions or antivirus interference
**Solution**:
1. **Move files to Linux filesystem**:
   ```bash
   # Copy to WSL home directory
   cp /mnt/c/path/to/ai-cli ~/ai-cli
   cd ~/ai-cli
   ```

2. **Fix permissions**:
   ```bash
   # Reset file permissions
   find . -type f -name "*.sh" -exec chmod 755 {} \;
   find . -type f -name "*.json" -exec chmod 644 {} \;
   ```

### Runtime Errors

#### Error: "JSON parsing error"
**Symptom**: jq fails to parse API response or context file
**Cause**: Malformed JSON data or corrupted context file
**Diagnostic**:
```bash
# Validate context file JSON
jq . ~/.ai_cli/conversation.json

# Check API response format
echo "$RESPONSE" | jq .
```

**Solution**:
```bash
# Backup and reset corrupted context
mv ~/.ai_cli/conversation.json ~/.ai_cli/conversation.json.backup
echo '{"messages":[]}' > ~/.ai_cli/conversation.json
```

**Recovery**:
```bash
# Attempt to recover valid messages from backup
jq -r '.messages[]? // empty' ~/.ai_cli/conversation.json.backup | \
jq -s '{"messages": .}' > ~/.ai_cli/conversation.json
```

#### Error: "Context file too large"
**Symptom**: Slow performance or API errors with large context files
**Cause**: Conversation history exceeds practical limits
**Solution**:
```bash
# Implement context trimming
trim_context() {
    local max_messages=20
    jq ".messages |= if length > $max_messages then .[(-$max_messages):] else . end" \
       "$CONTEXT_FILE" > "${CONTEXT_FILE}.tmp"
    mv "${CONTEXT_FILE}.tmp" "$CONTEXT_FILE"
}
```

**Prevention**: Implement automatic context management

### Network and Connectivity Errors

#### Error: "Could not resolve host"
**Symptom**: DNS resolution fails for openrouter.ai
**Diagnostic**:
```bash
# Test DNS resolution
nslookup openrouter.ai
dig openrouter.ai

# Test with alternative DNS
nslookup openrouter.ai 8.8.8.8
```

**Solution**:
```bash
# Use IP address directly (temporary)
API_URL="https://104.26.10.233/api/v1/chat/completions"

# Or configure DNS servers
echo "nameserver 8.8.8.8" | sudo tee -a /etc/resolv.conf
```

#### Error: "SSL certificate problem"
**Symptom**: HTTPS requests fail with certificate errors
**Cause**: Outdated certificates or corporate proxy
**Solution**:
```bash
# Update certificate bundle
# macOS
brew install ca-certificates

# Linux
sudo apt update && sudo apt install ca-certificates

# Temporary bypass (NOT RECOMMENDED for production)
curl -k -H "Authorization: Bearer $OPENROUTER_API_KEY" \
     https://openrouter.ai/api/v1/chat/completions
```

## 🔍 Diagnostic Tools and Procedures

### Automated Diagnostics Script
**File**: `troubleshooting-guides/diagnostic-steps.md`

```bash
#!/bin/bash
# AI CLI Diagnostic Script

echo "🔍 AI CLI Diagnostic Report"
echo "=========================="

# System Information
echo "📊 System Information:"
echo "OS: $(uname -s)"
echo "Shell: $SHELL"
echo "Date: $(date)"
echo ""

# Environment Check
echo "🌍 Environment Variables:"
echo "OPENROUTER_API_KEY: ${OPENROUTER_API_KEY:+SET (${#OPENROUTER_API_KEY} chars)}"
echo "AI_MODEL: ${AI_MODEL:-Not set}"
echo "HOME: $HOME"
echo ""

# Dependencies Check
echo "🔧 Dependencies:"
for cmd in bash jq curl; do
    if command -v $cmd >/dev/null 2>&1; then
        echo "✅ $cmd: $(command -v $cmd) ($(${cmd} --version 2>&1 | head -1))"
    else
        echo "❌ $cmd: Not found"
    fi
done
echo ""

# File Permissions
echo "📁 File Permissions:"
for script in ai.sh ai_linux.sh; do
    if [[ -f "6_🔣_Symbols/$script" ]]; then
        echo "📄 $script: $(ls -l 6_🔣_Symbols/$script)"
    else
        echo "❌ $script: Not found"
    fi
done
echo ""

# Network Connectivity
echo "🌐 Network Connectivity:"
if ping -c 1 openrouter.ai >/dev/null 2>&1; then
    echo "✅ Ping to openrouter.ai: Success"
else
    echo "❌ Ping to openrouter.ai: Failed"
fi

if curl -s -I https://openrouter.ai/ >/dev/null 2>&1; then
    echo "✅ HTTPS to openrouter.ai: Success"
else
    echo "❌ HTTPS to openrouter.ai: Failed"
fi
echo ""

# Context File Status
echo "💾 Context File Status:"
if [[ -f ~/.ai_cli/conversation.json ]]; then
    echo "✅ Context file exists"
    echo "Size: $(wc -c < ~/.ai_cli/conversation.json) bytes"
    echo "Messages: $(jq '.messages | length' ~/.ai_cli/conversation.json 2>/dev/null || echo 'Invalid JSON')"
else
    echo "ℹ️  No context file (will be created on first run)"
fi
echo ""

# API Test
echo "🔑 API Test:"
if [[ -n "$OPENROUTER_API_KEY" ]]; then
    echo "Testing API connection..."
    response=$(curl -s -w "%{http_code}" \
        -H "Authorization: Bearer $OPENROUTER_API_KEY" \
        -H "Content-Type: application/json" \
        -d '{"model":"anthropic/claude-3.5-sonnet","messages":[{"role":"user","content":"test"}],"max_tokens":1}' \
        https://openrouter.ai/api/v1/chat/completions)
    
    http_code="${response: -3}"
    if [[ "$http_code" == "200" ]]; then
        echo "✅ API connection: Success"
    else
        echo "❌ API connection: HTTP $http_code"
    fi
else
    echo "⚠️  Skipping API test (no API key set)"
fi

echo ""
echo "🎉 Diagnostic complete!"
```

### Error Pattern Recognition
**File**: `troubleshooting-guides/error-patterns.md`

#### Common Error Patterns and Solutions

**Pattern**: `curl: (6) Could not resolve host`
**Category**: Network/DNS
**Solution**: DNS configuration issue
```bash
# Quick fix
echo "nameserver 8.8.8.8" | sudo tee -a /etc/resolv.conf
```

**Pattern**: `jq: parse error: Invalid numeric literal`
**Category**: JSON Processing
**Solution**: Malformed JSON response or context file
```bash
# Validate and fix context
jq . ~/.ai_cli/conversation.json || echo '{"messages":[]}' > ~/.ai_cli/conversation.json
```

**Pattern**: `bash: ./ai.sh: Permission denied`
**Category**: File Permissions
**Solution**: Script not executable
```bash
chmod +x 6_🔣_Symbols/ai.sh
```

**Pattern**: `PowerShell: Execution of scripts is disabled`
**Category**: Platform Security
**Solution**: PowerShell execution policy
```powershell
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
```

## 🚀 Quick Fix Reference

### Emergency Recovery Commands
```bash
# Complete environment reset
reset_ai_cli() {
    echo "🔄 Resetting AI CLI environment..."
    
    # Backup current state
    timestamp=$(date +%Y%m%d_%H%M%S)
    [[ -d ~/.ai_cli ]] && mv ~/.ai_cli ~/.ai_cli_backup_$timestamp
    
    # Recreate clean environment
    mkdir -p ~/.ai_cli
    echo '{"messages":[]}' > ~/.ai_cli/conversation.json
    
    # Fix permissions
    find 6_🔣_Symbols -name "*.sh" -exec chmod +x {} \;
    
    echo "✅ Environment reset complete"
    echo "💾 Previous state backed up to ~/.ai_cli_backup_$timestamp"
}

# Dependency quick install
quick_install_deps() {
    echo "🔧 Installing dependencies..."
    
    case "$(uname -s)" in
        Darwin) brew install jq curl ;;
        Linux) sudo apt update && sudo apt install -y jq curl ;;
        *) echo "❌ Unsupported OS for auto-install" ;;
    esac
    
    echo "✅ Dependencies installed"
}

# Network connectivity test and fix
network_troubleshoot() {
    echo "🌐 Network troubleshooting..."
    
    # Test basic connectivity
    if ! ping -c 1 8.8.8.8 >/dev/null 2>&1; then
        echo "❌ No internet connectivity"
        return 1
    fi
    
    # Test DNS
    if ! nslookup openrouter.ai >/dev/null 2>&1; then
        echo "🔧 DNS issue detected, trying to fix..."
        echo "nameserver 8.8.8.8" | sudo tee -a /etc/resolv.conf
    fi
    
    # Test HTTPS
    if curl -s -I https://openrouter.ai/ >/dev/null 2>&1; then
        echo "✅ Network connectivity OK"
    else
        echo "❌ HTTPS connectivity failed"
    fi
}
```

### Context File Recovery
```bash
# Recover corrupted context file
recover_context() {
    local backup_file="$1"
    
    echo "🔄 Attempting context recovery..."
    
    if [[ ! -f "$backup_file" ]]; then
        echo "❌ Backup file not found: $backup_file"
        return 1
    fi
    
    # Try to extract valid messages
    if valid_messages=$(jq -r '.messages[]?' "$backup_file" 2>/dev/null); then
        echo '{"messages":[]}' > ~/.ai_cli/conversation.json
        echo "$valid_messages" | while read -r message; do
            echo "$message" | jq . >> ~/.ai_cli/conversation_temp.json
        done
        
        # Reconstruct context file
        jq -s '{"messages": .}' ~/.ai_cli/conversation_temp.json > ~/.ai_cli/conversation.json
        rm -f ~/.ai_cli/conversation_temp.json
        
        echo "✅ Context recovery successful"
    else
        echo "❌ Cannot recover context, creating fresh file"
        echo '{"messages":[]}' > ~/.ai_cli/conversation.json
    fi
}
```

## 📚 Learning from Errors

### Error Documentation Template
**File**: `solutions/error-template.md`
```markdown
# Error: [Error Message or Description]

## Symptoms
- What the user sees
- Error messages displayed
- Unexpected behavior

## Root Cause
Technical explanation of why this error occurs

## Immediate Solution
Step-by-step fix for this specific instance

## Prevention Strategy
How to avoid this error in the future

## Related Errors
- Similar errors that might occur
- Cascade effects

## Testing
How to verify the fix worked

## Additional Resources
- Links to relevant documentation
- Related troubleshooting guides
```

### Contributing Error Solutions
1. **Document New Errors**: When encountering new issues, document them
2. **Test Solutions**: Verify solutions work across platforms
3. **Update Guides**: Keep troubleshooting guides current
4. **Share Knowledge**: Contribute solutions back to the project
5. **Pattern Recognition**: Identify recurring error patterns

## 🔗 Cross-Reference Index

### Error-to-Solution Quick Index
- **API Key Issues** → `api-errors.md` + `../3_🌳_Environments/setup guides`
- **Permission Errors** → `platform-specific/` + `../2_✈️_Journey/setup guides`
- **Dependency Problems** → `dependency-errors.md` + `../3_🌳_Environments/`
- **JSON Issues** → `runtime-errors.md` + `../5_📐_Formulas/json-processing.md`
- **Network Problems** → `common-errors/network-errors.md`
- **Context File Issues** → `context-management-errors.md`

### Platform-Specific Quick Links
- **macOS Issues** → `platform-specific/macos-issues.md`
- **Linux Issues** → `platform-specific/linux-issues.md`
- **Windows Issues** → `platform-specific/windows-issues.md`
- **WSL Issues** → `platform-specific/wsl-issues.md`

## 💡 Advanced Troubleshooting Techniques

### Log Analysis
```bash
# Enable debug mode
DEBUG=1 ./6_🔣_Symbols/ai.sh "test query"

# Trace script execution
bash -x ./6_🔣_Symbols/ai.sh "test query"

# Monitor API calls
./6_🔣_Symbols/ai.sh "test" 2>&1 | tee debug.log
```

### Performance Profiling
```bash
# Time individual components
time_components() {
    echo "⏱️  Timing API call..."
    time curl -s -H "Authorization: Bearer $OPENROUTER_API_KEY" \
              https://openrouter.ai/api/v1/models >/dev/null
    
    echo "⏱️  Timing JSON processing..."
    echo '{"test": "data"}' | time jq .
    
    echo "⏱️  Timing context file operations..."
    time cat ~/.ai_cli/conversation.json >/dev/null
}
```

### System Health Monitoring
```bash
# Monitor system resources during execution
monitor_resources() {
    echo "📊 System resources:"
    echo "Memory: $(free -h | grep '^Mem:' | awk '{print $3 "/" $2}')"
    echo "Disk: $(df -h ~ | tail -1 | awk '{print $3 "/" $2 " (" $5 " used)"}')"
    echo "Load: $(uptime | awk -F'load average:' '{print $2}')"
}
```

This comprehensive error documentation and troubleshooting system ensures that learners can effectively diagnose and resolve issues while building their understanding of the AI CLI system through problem-solving experiences.
# ✅ Environment Validation

## Quick Validation Script
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

## Validation Checklist

### Dependencies
- [ ] **jq**: JSON processing tool installed
- [ ] **curl**: HTTP client available
- [ ] **dos2unix**: Line ending conversion (Linux/WSL)

### Environment Variables
- [ ] **OPENROUTER_API_KEY**: API key configured
- [ ] **AI_MODEL**: Model selection (optional)
- [ ] **PATH**: Scripts accessible from PATH

### File Permissions
- [ ] **Shell Scripts**: Executable permissions set
- [ ] **Directory Access**: Read/write permissions to context directory
- [ ] **Line Endings**: Unix line endings (LF) on Unix systems

### Connectivity
- [ ] **Internet Access**: Can reach openrouter.ai
- [ ] **DNS Resolution**: Domain name resolution working
- [ ] **API Authentication**: Valid API key authentication

### Platform-Specific
- [ ] **macOS**: Gatekeeper approval for scripts
- [ ] **Windows**: PowerShell execution policy configured
- [ ] **WSL**: File system permissions and line endings
- [ ] **Linux**: Package dependencies installed

## Automated Validation

### Run Validation
```bash
# Make validation script executable
chmod +x 3_🌳_Environments/validate-environment.sh

# Run validation
./3_🌳_Environments/validate-environment.sh
```

### Expected Output
```
🔍 Validating AI CLI Environment...
Checking dependencies...
✅ Dependencies OK
✅ API key configured
✅ ai.sh executable
Testing API connection...
✅ API connection successful
🎉 Environment validation complete!
```

## Troubleshooting

### Common Issues
- **Dependencies Missing**: Install jq and curl for your platform
- **Permission Denied**: Run `chmod +x` on script files
- **API Key Not Set**: Configure OPENROUTER_API_KEY environment variable
- **Network Issues**: Check firewall and proxy settings

### Quick Fixes
```bash
# Fix permissions
find 6_🔣_Symbols -name "*.sh" -exec chmod +x {} \;

# Fix line endings (Linux/WSL)
find 6_🔣_Symbols -name "*.sh" -exec dos2unix {} \;

# Test API key
echo $OPENROUTER_API_KEY | head -c 20
```
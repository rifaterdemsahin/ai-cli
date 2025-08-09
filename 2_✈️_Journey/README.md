# 2_✈️_Journey - Learning Progression and Setup Guides

## Purpose
This folder contains the step-by-step learning journey, setup guides, and progressive skill development documentation. Following the Lacan triad methodology, this represents the experiential path from beginner to skilled practitioner.

## 🛤️ Learning Path Overview

### Phase 1: Foundation Setup
**Duration**: 1-2 hours  
**Prerequisites**: Basic command line knowledge

1. **Environment Preparation**
   - Install required tools (jq, curl, PowerShell 7+)
   - Set up API keys and environment variables
   - Verify platform-specific requirements

2. **Basic Understanding**
   - Learn about AI CLI tools and their benefits
   - Understand REST APIs and JSON processing
   - Familiarize with shell scripting basics

### Phase 2: Implementation Deep Dive
**Duration**: 3-4 hours  
**Prerequisites**: Completed Phase 1

1. **Platform-Specific Learning**
   - Master Bash scripting techniques
   - Learn PowerShell fundamentals
   - Understand cross-platform considerations

2. **API Integration**
   - OpenRouter API authentication
   - Request/response handling
   - Error management strategies

### Phase 3: Advanced Features
**Duration**: 2-3 hours  
**Prerequisites**: Completed Phase 2

1. **Context Management**
   - JSON-based persistence
   - Conversation flow control
   - Second brain integration

2. **User Experience Enhancement**
   - Command-line interface design
   - Visual feedback systems
   - Error message optimization

## 📚 Step-by-Step Setup Guide

### Quick Start (5 minutes)
```bash
# 1. Clone or download the project
cd ai-cli

# 2. Set your API key
export OPENROUTER_API_KEY='your-api-key-here'

# 3. Run your first query
./6_🔣_Symbols/ai.sh "Hello, how are you?"
```

### Complete Setup

#### For macOS Users
1. **Install Dependencies**
   ```bash
   # Install Homebrew if not already installed
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   
   # Install jq for JSON processing
   brew install jq
   
   # Verify curl is available (usually pre-installed)
   curl --version
   ```

2. **Set Up Environment**
   ```bash
   # Add to your ~/.zshrc or ~/.bash_profile
   export OPENROUTER_API_KEY='your-openrouter-api-key'
   export AI_MODEL='anthropic/claude-3.5-sonnet'  # optional
   
   # Reload your shell
   source ~/.zshrc
   ```

3. **Test Installation**
   ```bash
   chmod +x 6_🔣_Symbols/ai.sh
   ./6_🔣_Symbols/ai.sh "Test connection"
   ```

#### For Linux/WSL Users
1. **Install Dependencies**
   ```bash
   # Update package list
   sudo apt update
   
   # Install required packages
   sudo apt install jq curl
   
   # For WSL: Convert line endings if needed
   sudo apt install dos2unix
   dos2unix 6_🔣_Symbols/ai_linux.sh
   ```

2. **Set Up Environment**
   ```bash
   # Add to your ~/.bashrc
   echo 'export OPENROUTER_API_KEY="your-openrouter-api-key"' >> ~/.bashrc
   echo 'export AI_MODEL="anthropic/claude-3.5-sonnet"' >> ~/.bashrc
   
   # Reload your shell
   source ~/.bashrc
   ```

3. **Test Installation**
   ```bash
   chmod +x 6_🔣_Symbols/ai_linux.sh
   ./6_🔣_Symbols/ai_linux.sh "Test connection"
   ```

#### For Windows PowerShell Users
1. **Install PowerShell 7+**
   ```powershell
   # Install from Microsoft Store or download from GitHub
   # Verify version
   $PSVersionTable.PSVersion
   ```

2. **Set Up Environment**
   ```powershell
   # Set environment variables (session-level)
   $env:OPENROUTER_API_KEY='your-openrouter-api-key'
   $env:AI_MODEL='anthropic/claude-3.5-sonnet'
   
   # For persistent environment variables, use System Properties
   # or add to PowerShell profile
   ```

3. **Test Installation**
   ```powershell
   .\6_🔣_Symbols\ai.ps1 "Test connection"
   ```

## 🎓 Learning Modules

### Module 1: Understanding AI CLI Tools
**Estimated Time**: 30 minutes

**Learning Objectives**:
- Understand the purpose and benefits of AI CLI tools
- Learn about different AI model providers and APIs
- Explore use cases for command-line AI interaction

**Activities**:
1. Read about OpenRouter API and supported models
2. Try different model configurations
3. Experiment with various query types

**Resources**:
- [OpenRouter Documentation](https://openrouter.ai/docs)
- Model comparison examples in `../5_📐_Formulas/model-comparison.md`

### Module 2: Shell Scripting Fundamentals
**Estimated Time**: 45 minutes

**Learning Objectives**:
- Master basic shell scripting concepts
- Understand variable handling and command substitution
- Learn JSON processing with jq

**Activities**:
1. Analyze the ai.sh script line by line
2. Modify script parameters and observe changes
3. Practice jq commands for JSON manipulation

**Resources**:
- Script breakdown in `../5_📐_Formulas/script-analysis.md`
- jq tutorial and examples

### Module 3: Cross-Platform Development
**Estimated Time**: 60 minutes

**Learning Objectives**:
- Compare shell vs PowerShell implementations
- Understand platform-specific considerations
- Learn adaptation strategies for different environments

**Activities**:
1. Compare ai.sh and ai.ps1 implementations
2. Test the same functionality across platforms
3. Identify and resolve platform-specific issues

**Resources**:
- Platform comparison in `../5_📐_Formulas/platform-differences.md`
- Troubleshooting guides in `../7_🌀_Semblance/`

### Module 4: Context Management Systems
**Estimated Time**: 45 minutes

**Learning Objectives**:
- Understand conversation context and persistence
- Learn JSON-based data storage patterns
- Implement context management features

**Activities**:
1. Examine the conversation.json structure
2. Test context commands (--new, --context, --clear)
3. Implement custom context features

**Resources**:
- Context system design in `../5_📐_Formulas/context-architecture.md`

## 🔄 Progress Tracking

### Beginner Level (0-2 hours)
- [ ] Completed environment setup
- [ ] Successfully ran first AI query
- [ ] Understood basic command options
- [ ] Read project documentation

### Intermediate Level (2-5 hours)
- [ ] Analyzed script implementations
- [ ] Tested cross-platform compatibility
- [ ] Customized environment variables
- [ ] Used all command-line options

### Advanced Level (5+ hours)
- [ ] Modified script functionality
- [ ] Implemented error handling improvements
- [ ] Created custom integrations
- [ ] Contributed to documentation

## 📖 Additional Resources

### Documentation Files in This Folder
- `linux_version.md` - WSL adaptation guide
- `powershell_version.md` - PowerShell implementation guide

### External Learning Resources
- [Bash Scripting Tutorial](https://www.gnu.org/software/bash/manual/)
- [PowerShell Documentation](https://docs.microsoft.com/en-us/powershell/)
- [jq Manual](https://stedolan.github.io/jq/manual/)
- [OpenRouter API Docs](https://openrouter.ai/docs)

## 🤝 Community and Support

### Getting Help
1. Check the error solutions in `../7_🌀_Semblance/`
2. Review troubleshooting guides
3. Consult the implementation formulas in `../5_📐_Formulas/`

### Contributing Back
1. Document new errors and solutions
2. Create usage examples
3. Improve setup instructions
4. Share advanced configurations

## ✅ Module Completion Checklist

### Setup Verification
- [ ] All dependencies installed
- [ ] API key configured correctly
- [ ] Scripts executable and working
- [ ] Test queries successful

### Understanding Check
- [ ] Can explain how the tool works
- [ ] Understands context persistence
- [ ] Knows how to troubleshoot common issues
- [ ] Can modify basic configurations

### Practical Skills
- [ ] Can set up on multiple platforms
- [ ] Comfortable with all command options
- [ ] Can customize for specific needs
- [ ] Ready to help others learn
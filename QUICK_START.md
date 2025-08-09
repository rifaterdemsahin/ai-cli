# 🚀 Quick Start Guide - Get Running in 10 Minutes

## 🎯 Goal
Get the AI CLI tool up and running on your system in under 10 minutes.

## 📋 Prerequisites
- Command line access (Terminal/PowerShell)
- Internet connection
- 10 minutes of your time

## ⚡ Platform-Specific Quick Setup

### 🍎 macOS (5 minutes)

1. **Install Dependencies** (2 minutes)
   ```bash
   # Install Homebrew if needed
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   
   # Install jq
   brew install jq
   ```

2. **Set API Key** (1 minute)
   ```bash
   export OPENROUTER_API_KEY="your-openrouter-api-key-here"
   ```

3. **Run the Tool** (2 minutes)
   ```bash
   chmod +x 6_🔣_Symbols/ai.sh
   ./6_🔣_Symbols/ai.sh "Hello, introduce yourself!"
   ```

### 🐧 Linux/Ubuntu (4 minutes)

1. **Install Dependencies** (2 minutes)
   ```bash
   sudo apt update && sudo apt install -y jq curl
   ```

2. **Set API Key** (1 minute)
   ```bash
   export OPENROUTER_API_KEY="your-openrouter-api-key-here"
   ```

3. **Run the Tool** (1 minute)
   ```bash
   chmod +x 6_🔣_Symbols/ai_linux.sh
   ./6_🔣_Symbols/ai_linux.sh "Hello, introduce yourself!"
   ```

### 🪟 Windows PowerShell (3 minutes)

1. **Check PowerShell Version** (30 seconds)
   ```powershell
   $PSVersionTable.PSVersion
   # Should be 7.0 or higher. If not, install from Microsoft Store
   ```

2. **Set API Key** (1 minute)
   ```powershell
   $env:OPENROUTER_API_KEY="your-openrouter-api-key-here"
   ```

3. **Run the Tool** (1.5 minutes)
   ```powershell
   .\6_🔣_Symbols\ai.ps1 "Hello, introduce yourself!"
   ```

## 🔑 Getting Your OpenRouter API Key

1. Visit [OpenRouter.ai](https://openrouter.ai)
2. Sign up for an account (free tier available)
3. Navigate to API Keys section
4. Generate a new API key
5. Copy the key (starts with `sk-`)

## ✅ Verification Steps

After setup, verify everything works:

```bash
# Test basic functionality
./6_🔣_Symbols/ai.sh "What is 2+2?"

# Test context management
./6_🔣_Symbols/ai.sh --new "My name is [Your Name]"
./6_🔣_Symbols/ai.sh "What did I just tell you?"
./6_🔣_Symbols/ai.sh --context

# Test help
./6_🔣_Symbols/ai.sh --help
```

## 🎉 Success Indicators

You've successfully set up the AI CLI when:
- ✅ No error messages about missing dependencies
- ✅ AI responds to your questions appropriately
- ✅ Context commands work (--new, --context, --clear)
- ✅ Help command shows usage information

## 🚨 Common Quick Fixes

### "Command not found: jq"
```bash
# macOS
brew install jq

# Linux
sudo apt install jq

# Windows (use PowerShell version instead)
```

### "Permission denied"
```bash
chmod +x 6_🔣_Symbols/ai.sh
chmod +x 6_🔣_Symbols/ai_linux.sh
```

### "API key not set"
```bash
echo $OPENROUTER_API_KEY  # Should show your key
export OPENROUTER_API_KEY="sk-your-key-here"
```

### PowerShell execution policy error
```powershell
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
```

## ⏭️ What's Next?

Once you have the basic tool working:

1. **Learn More**: Read `LEARNING_PATH.md` for comprehensive learning
2. **Explore Features**: Try all the command options
3. **Understand Structure**: Browse the 7-folder system
4. **Customize**: Modify settings and add personal touches

## 📞 Need Help?

- 📖 **Detailed Setup**: See `2_✈️_Journey/README.md`
- 🐛 **Troubleshooting**: Check `7_🌀_Semblance/README.md`
- 🔧 **Environment Issues**: Review `3_🌳_Environments/README.md`
- 📝 **Code Questions**: Study `6_🔣_Symbols/README.md`

**Time to Success**: Most users are up and running within 5-10 minutes following this guide!

---
*Remember to replace "your-openrouter-api-key-here" with your actual API key from OpenRouter.ai*
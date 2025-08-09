# 🛠️ Development Tools Configuration

## VS Code Settings

### Workspace Settings
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

### Recommended Extensions
**File**: `.vscode/extensions.json`
```json
{
    "recommendations": [
        "timonwong.shellcheck",
        "foxundermoon.shell-format",
        "ms-vscode.powershell",
        "redhat.vscode-yaml",
        "ms-vscode.vscode-json"
    ]
}
```

## Editor Configuration

### EditorConfig
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

## Git Configuration

### Git Attributes
**File**: `.gitattributes`
```
# Ensure shell scripts use LF line endings
*.sh text eol=lf
*.bash text eol=lf

# PowerShell scripts can use CRLF on Windows
*.ps1 text eol=crlf

# Documentation files
*.md text eol=lf
*.txt text eol=lf

# Binary files
*.png binary
*.jpg binary
*.gif binary
```

### Git Ignore
**File**: `.gitignore`
```
# Environment files
.env
.env.local

# OS generated files
.DS_Store
.DS_Store?
._*
.Spotlight-V100
.Trashes
ehthumbs.db
Thumbs.db

# IDE files
.vscode/settings.json
.idea/

# Temporary files
*.tmp
*.temp
*~

# Log files
*.log

# API keys and secrets
**/api-key*
**/secret*
```

## Linting and Formatting

### Shell Script Linting
```bash
# Install shellcheck
# macOS
brew install shellcheck

# Linux
sudo apt install shellcheck

# Usage
shellcheck 6_🔣_Symbols/ai.sh
shellcheck 6_🔣_Symbols/ai_linux.sh
```

### PowerShell Script Analysis
```powershell
# Install PSScriptAnalyzer
Install-Module -Name PSScriptAnalyzer -Force

# Usage
Invoke-ScriptAnalyzer -Path 6_🔣_Symbols/ai.ps1
```

## Pre-commit Hooks

### Setup Pre-commit
**File**: `.pre-commit-config.yaml`
```yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.4.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-added-large-files

  - repo: https://github.com/shellcheck-py/shellcheck-py
    rev: v0.9.0.2
    hooks:
      - id: shellcheck
        files: \.(sh|bash)$
```
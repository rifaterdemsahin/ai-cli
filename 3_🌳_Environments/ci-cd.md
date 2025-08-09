# 🔄 CI/CD Pipeline Environments

## GitHub Actions

### Test Workflow
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

## GitLab CI/CD

### Pipeline Configuration
**File**: `.gitlab-ci.yml`
```yaml
stages:
  - test
  - validate

test-scripts:
  stage: test
  image: ubuntu:latest
  before_script:
    - apt-get update -qq && apt-get install -y -qq jq curl shellcheck
  script:
    - shellcheck 6_🔣_Symbols/*.sh
    - bash -n 6_🔣_Symbols/ai.sh
    - bash -n 6_🔣_Symbols/ai_linux.sh

validate-structure:
  stage: validate
  image: ubuntu:latest
  script:
    - test -f 6_🔣_Symbols/ai.sh
    - test -f 6_🔣_Symbols/ai_linux.sh
    - test -f 6_🔣_Symbols/ai.ps1
    - test -d 1_🌍_Real
    - test -d 7_🌀_Semblance
```

## Jenkins Pipeline

### Jenkinsfile
```groovy
pipeline {
    agent any
    
    stages {
        stage('Setup') {
            steps {
                sh 'sudo apt-get update && sudo apt-get install -y jq curl shellcheck'
            }
        }
        
        stage('Lint') {
            steps {
                sh 'shellcheck 6_🔣_Symbols/ai.sh'
                sh 'shellcheck 6_🔣_Symbols/ai_linux.sh'
            }
        }
        
        stage('Test Syntax') {
            steps {
                sh 'bash -n 6_🔣_Symbols/ai.sh'
                sh 'bash -n 6_🔣_Symbols/ai_linux.sh'
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
    }
}
```

## CI/CD Best Practices

### Security
- Never commit API keys to repository
- Use secrets management for sensitive data
- Validate scripts without making API calls
- Run security scanning tools

### Testing Strategy
- Syntax validation for all scripts
- Linting with shellcheck
- Cross-platform compatibility testing
- Documentation completeness checks
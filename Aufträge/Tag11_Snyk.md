# Security Pipline erstellen

1. Snyk Account aufsetzten  https://app.snyk.io/login und Token rauskopieren (Account settings (unten links) -> Auth Token)
2. Token als Secret im Repo hinterlegen: SNYK_TOKEN
3. Piplien schreiben und im Projekt unter .githup/workflows hinterlegen

Pipline:

```bash
name: TechStyle CI Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  security-scan:
    runs-on: ubuntu-latest
    name: Security Analysis with Snyk
    permissions:
      contents: read
      security-events: write
      actions: read
    
    steps:
    - name: Checkout Code
      uses: actions/checkout@v4
      
    - name: Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.10'
        
    - name: Install Dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
        
    - name: Run Snyk to check for vulnerabilities
      uses: snyk/actions/python@master
      continue-on-error: true
      env:
        SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
      with:
        args: --severity-threshold=low --file=requirements.txt --json-file-output=snyk-deps.json --sarif-file-output=snyk-deps.sarif
    
    - name: Run Snyk Code Analysis
      uses: snyk/actions/python@master
      continue-on-error: true
      env:
        SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
      with:
        command: code test
        args: --severity-threshold=low --json-file-output=snyk-code.json --sarif-file-output=snyk-code.sarif

    - name: Upload code result to GitHub Code Scanning
      uses: github/codeql-action/upload-sarif@v3
      with:
        sarif_file: snyk-code.sarif

    - name: Upload deps result to GitHub Code Scanning
      uses: github/codeql-action/upload-sarif@v3
      with:
        sarif_file: snyk-deps.sarif


    # Optionaler Output -----------------------------


    - name: Process Snyk Results
      if: always()
      run: |
        echo "🔍 Snyk Security Scan Results" >> $GITHUB_STEP_SUMMARY
        echo "Static Code Analysis and SCA completed" >> $GITHUB_STEP_SUMMARY
        echo "" >> $GITHUB_STEP_SUMMARY
        
        # Process dependency scan results
        if [ -f "snyk-deps.json" ]; then
          echo "## 📦 Dependency Vulnerabilities" >> $GITHUB_STEP_SUMMARY
          DEPS_ISSUES=$(jq '.vulnerabilities | length' snyk-deps.json 2>/dev/null || echo "0")
          echo "Found $DEPS_ISSUES dependency vulnerabilities" >> $GITHUB_STEP_SUMMARY
          
          if [ "$DEPS_ISSUES" -gt 0 ]; then
            echo "### High/Critical Issues:" >> $GITHUB_STEP_SUMMARY
            jq -r '.vulnerabilities[] | select(.severity == "high" or .severity == "critical") | "- **\(.title)** in \(.packageName) (Severity: \(.severity))"' snyk-deps.json >> $GITHUB_STEP_SUMMARY 2>/dev/null || true
          fi
          echo "" >> $GITHUB_STEP_SUMMARY
        fi
        
        # Process code scan results
        if [ -f "snyk-code.json" ]; then
          echo "## 🔍 Code Analysis Issues" >> $GITHUB_STEP_SUMMARY
          CODE_ISSUES=$(jq '.runs[0].results | length' snyk-code.json 2>/dev/null || echo "0")
          echo "Found $CODE_ISSUES code security issues" >> $GITHUB_STEP_SUMMARY
          
          if [ "$CODE_ISSUES" -gt 0 ]; then
            echo "### Medium/High Issues:" >> $GITHUB_STEP_SUMMARY
            jq -r '.runs[0].results[] | select(.level == "warning" or .level == "error") | "- **\(.ruleId)**: \(.message.text)"' snyk-code.json >> $GITHUB_STEP_SUMMARY 2>/dev/null || true
          fi
          echo "" >> $GITHUB_STEP_SUMMARY
        fi
        
        echo "📊 **Security Scan Summary**" >> $GITHUB_STEP_SUMMARY
        echo "- Dependencies checked: ✅" >> $GITHUB_STEP_SUMMARY
        echo "- Code analysis completed: ✅" >> $GITHUB_STEP_SUMMARY
        echo "- Results available in job artifacts" >> $GITHUB_STEP_SUMMARY
        
    - name: Upload Snyk Results as Artifacts
      uses: actions/upload-artifact@v4
      if: always()
      with:
        name: snyk-security-results
        path: |
          snyk-*.json
        retention-days: 30
        
    - name: Check for Critical Issues
      if: always()
      run: |
        # Fail the job if critical vulnerabilities are found
        CRITICAL_DEPS=0
        CRITICAL_CODE=0
        
        if [ -f "snyk-deps.json" ]; then
          CRITICAL_DEPS=$(jq '.vulnerabilities[] | select(.severity == "critical") | length' snyk-deps.json 2>/dev/null | wc -l || echo "0")
        fi
        
        if [ -f "snyk-code.json" ]; then
          CRITICAL_CODE=$(jq '.runs[0].results[] | select(.level == "error") | length' snyk-code.json 2>/dev/null | wc -l || echo "0")
        fi
        
        if [ "$CRITICAL_DEPS" -gt 0 ] || [ "$CRITICAL_CODE" -gt 0 ]; then
          echo "❌ Critical security issues found!"
          echo "Dependencies: $CRITICAL_DEPS critical issues"
          echo "Code: $CRITICAL_CODE critical issues"
          exit 1
        else
          echo "✅ No critical security issues found"
        fi
```
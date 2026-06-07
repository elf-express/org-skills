# Recipe CICD Skill Validation

> Source: https://skillshare.runkids.cc/docs/how-to/recipes/ci-cd-skill-validation

---

# 配方：CI/CD 技能驗證



> 在 CI 管道中自動審核和同步技能。



## 場景



您擁有一個團隊技能庫，並希望確保每個 PR：



- 透過安全審核（無提示注入、憑證竊盜等）
- 驗證 SKILL.md 格式
- 同步無錯誤



## 解決方案



### GitHub Actions（使用 setup-skillshare）



[`setup-skillshare`](https://github.com/marketplace/actions/setup-skillshare) 操作一步處理安裝、初始化和可選的安全審核。



```
name: Skill Validationon:  pull_request:    paths:      - 'skills/**'jobs:  validate:    runs-on: ubuntu-latest    steps:      - uses: actions/checkout@v4      - uses: runkids/setup-skillshare@v1        with:          source: ./skills          audit: true          audit-threshold: high      - run: skillshare sync --dry-run
```



### 上傳有 SARIF 的 GitHub Actions



若要透過 [GitHub 程式碼掃描](https://docs.github.com/en/code-security/code-scanning) 取得內嵌 PR 註釋，請使用 SARIF 輸出：



```
name: Skill Security Scanon:  pull_request:    paths: ['skills/**']  push:    branches: [main]jobs:  validate:    runs-on: ubuntu-latest    permissions:      security-events: write    steps:      - uses: actions/checkout@v4      - uses: runkids/setup-skillshare@v1        with:          source: ./skills          audit: true          audit-threshold: high          audit-format: sarif          audit-output: results.sarif      - name: Upload SARIF to Code Scanning        if: always()        uses: github/codeql-action/upload-sarif@v3        with:          sarif_file: results.sarif          category: skillshare-audit      - run: skillshare sync --dry-run
```



### 沒有操作（手動設定）



如果您不想使用該操作，可以直接安裝 Skillshare：



```
jobs:  validate:    runs-on: ubuntu-latest    steps:      - uses: actions/checkout@v4      - run: curl -fsSL https://raw.githubusercontent.com/runkids/skillshare/main/install.sh | sh      - run: skillshare init --no-copy --all-targets --no-git --no-skill --source ./skills      - run: skillshare audit --threshold high --format json      - run: skillshare sync --dry-run
```



### 來自 GitLab



創建`.gitlab-ci.yml`：



```
skill-validation:  image: ghcr.io/runkids/skillshare-ci:latest  stage: test  script:    - skillshare init    - skillshare install . --into ci-check    - skillshare audit --threshold high --format json    - skillshare sync --dry-run  rules:    - changes:        - skills/**/*
```



### 使用 CI Docker 映像



為了更快地啟動管道，請使用預先建置的 CI 映像：



```
# GitHub Actionsjobs:  validate:    runs-on: ubuntu-latest    container:      image: ghcr.io/runkids/skillshare-ci:latest    steps:      - uses: actions/checkout@v4      - run: skillshare init && skillshare audit --format json
```



## 輸出格式



`audit`指令支援多種輸出格式，以滿足不同的 CI/CD 整合需求。



### 退出程式碼



```
# Block deployment if any skill has findings at or above threshold
skillshare audit --threshold high
echo $?  # 0 = clean, 1 = findings found
```



### SARIF 輸出



[SARIF（靜態分析結果交換格式）](https://docs.oasis-open.org/sarif/sarif/v2.1.0/sarif-v2.1.0.html) 是 GitHub Code Scanning、VS Code SARIF Viewer、Azure DevOps、SonarQube 和其他靜態分析工具使用的 OASIS 標準。



```
skillshare audit --format sarif              # Output to stdout
skillshare audit --format sarif > results.sarif  # Save to file
```



SARIF 輸出包含：



- **工具元資料** — 工具名稱 (`skillshare`)、版本與資訊 URI
- **規則** — 具有 `security-severity` 分數的去重規則描述符
- **結果** — 每個發現都對應到包含檔案位置和嚴重性等級的 SARIF 結果



嚴重性映射到 SARIF 等級：



|技能分享 嚴重性 | SARIF 等級 |安全嚴重性 |
| ---| ---| ---|
|關鍵 |錯誤 | 9.0 |
|高|錯誤 | 7.0 |
|中 |警告| 4.0 |
|低|注意| 2.0 |
|訊息|注意| 0.5 | 0.5



### Markdown 報告



產生適合貼上到 GitHub 問題、拉取請求或文件的獨立 Markdown 報表：



```
skillshare audit --format markdown               # Print to stdout
skillshare audit --format markdown > report.md   # Save to file
skillshare audit -p --format markdown > report.md  # Project mode
```



報告內容包括：



- **標題** — 掃描計數、模式和閾值
- **總表** — 通過/警告/失敗計數、嚴重性細分、風險評分、可分析性
- **發現** — 每個技能表，包含嚴重性、模式、訊息和位置；可折疊片段
- **清潔技能** — 以逗號分隔的技能列表，沒有發現任何結果



### 使用 jq 輸出 JSON



```
# List all skills with CRITICAL findings
skillshare audit --json | jq '[.skills[] | select(.findings[] | .severity == "CRITICAL")]'
# Extract risk scores for all skills
skillshare audit --json | jq '.skills[] | {name: .skillName, score: .riskScore, label: .riskLabel}'
# Count findings by severity
skillshare audit --json | jq '[.skills[].findings[].severity] | group_by(.) | map({(.[0]): length}) | add'
```



## 驗證



- PR 檢查通過：審核退出 0（沒有發現達到/高於閾值）
- 稽核 JSON 輸出可以由下游工具解析
- SARIF 上傳將發現結果顯示為 PR 差異的內嵌註釋
- 同步試運行顯示預期的符號連結操作



## 變化



- **阻止高嚴重性**：將 `--threshold HIGH`（或 `-T HIGH`）加到 `audit` — 任何 HIGH+ 結果均非零
- **用於程式碼掃描的 SARIF**：使用 `--format sarif` 和 `github/codeql-action/upload-sarif@v3` 進行內聯 PR 註釋
- **並行驗證**：在單獨的 CI 作業中執行審核和同步以獲得更快的回饋
- **定期審核**：每晚運行以捕獲現有技能中新檢測到的模式



## 相關



- [安全審核指南](https://skillshare.runkids.cc/docs/how-to/advanced/security)
- [`audit`指令參考](https://skillshare.runkids.cc/docs/reference/commands/audit)
- [`audit rules`參考](https://skillshare.runkids.cc/docs/reference/commands/audit-rules)
- [審核引擎](https://skillshare.runkids.cc/docs/understand/audit-engine) — 引擎如何運作
- [Docker沙盒指南](https://skillshare.runkids.cc/docs/how-to/advanced/docker-sandbox)
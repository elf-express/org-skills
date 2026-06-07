# Security

> Source: https://skillshare.runkids.cc/docs/understand/philosophy/security-first

---

# 安全第一的設計



> AI技能是可执行的指令。 Skillshare 將它們視為不受信任的輸入。



## 威脅模型



當您從 GitHub 安裝技能時，您將向 AI 工具發出指令，這些指令將影響程式碼產生、檔案修改以及潛在的命令執行。惡意技能可能：



- **注入提示**，超越人工智慧的安全準則
- **透過指示 AI 將文件內容傳送到外部 URL 來竊取資料**
- **透過AI的shell存取執行破壞性命令**
- **透過存取環境變數或設定檔竊取憑證**



這不是理論上的。即時注入是人工智慧工具中排名第一的安全問題。



## 審計引擎



Skillshare 包括一個內建安全掃描器 (`skillshare audit`)，可根據 5 個嚴重等級的 15 種以上偵測模式檢查每個已安裝的技能：



|嚴重性 |範例 |
| --- | --- |
|關鍵 |提示注入，系統提示覆蓋|
|高|資料外洩 URL、憑證存取模式 |
|中 |破壞性指令（rm -rf、DROP TABLE）、檔案系統寫入 |
|低|網路請求、外部工具呼叫 |
|資訊|檔案大小大、格式不尋常 |



### 它是如何運作的



審核引擎使用模式匹配和啟發式掃描 SKILL.md 內容：



```
# Scan all installed skills
skillshare audit
# JSON output for CI integration
skillshare audit --json
# Scan project skills only
skillshare audit -p
```



### 自動封鎖



在`skillshare install`期間，審核會自動運作。如果偵測到關鍵發現，安裝將被阻止：



```
CRITICAL: Prompt injection detected in "malicious-skill"  → Pattern: "ignore previous instructions"  → Installation blocked. Use --force to override (not recommended).
```



## 縱深防禦



審計引擎是一層。 Skillshare 的安全模型包括：



1. **安裝時審核** — 在威脅到達您的 AI 工具之前將其捕獲
2. **按需審核** - 新增新模式時重新掃描現有技能
3. **符號連結隔離** — 技能是符號連結的，而不是複製的，因此來源仍然是權威
4. **更改前備份** — `skillshare backup` 快照您的整個技能庫
5. **帶有 TTL 的垃圾箱** — 刪除的技能首先進入垃圾箱，而不是永久刪除
6. **操作日誌記錄** — 每個變異操作都會記錄到 `operations.log` (JSONL)



## 供應鏈考慮因素



人工智慧技能生態系統還很年輕。沒有帶有審查流程的套件註冊表，沒有程式碼簽名，沒有依賴關係解析。技能是 git 儲存庫中的 Markdown 檔案。



技能共享的方法：



- **掃描一切** - 甚至是來自可信來源的技能
- **預設阻止** — 重要發現阻止安裝
- **記錄一切** — 儲存審核結果以供法醫審查
- **更新模式** - 每個技能共享版本都會附帶新的檢測模式



## 設定審核行為



在`config.yaml`中設定阻止閾值以控制阻止安裝的嚴重性：



```
# config.yaml
audit:
  block_threshold: HIGH   # Block on HIGH and CRITICAL (default: CRITICAL)
```



對於每個規則的自訂，請使用單獨的 `audit-rules.yaml` 檔案（使用 `skillshare audit --init-rules` 初始化）：



```
# audit-rules.yaml
rules:
  - id: network-request-0
    enabled: false          # Disable this specific rule
  - id: my-custom-check
    severity: MEDIUM
    pattern: "TODO|FIXME"
    description: Policy violation — unresolved TODOs
```



## 相關



- [`audit`指令參考](https://skillshare.runkids.cc/docs/reference/commands/audit)
- [安全指南](https://skillshare.runkids.cc/docs/how-to/advanced/security)
- [CI/CD 驗證秘訣](https://skillshare.runkids.cc/docs/how-to/recipes/ci-cd-skill-validation)
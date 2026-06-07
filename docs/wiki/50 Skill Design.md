# Skill Design

> Source: https://skillshare.runkids.cc/docs/understand/philosophy/skill-design

---

# 技能設計



如何編寫可靠的技能——選擇正確的複雜性等級、最大化確定性以及使用漸進式揭露。

這什麼時候重要？

如果您的技能效果不一致，如果廉價模型無法滿足您的技能，或者您正在為團隊培養技能 - 本指南可幫助您編寫**可靠、安全且高效**的技能。



## 技能譜



並非所有技能都是天生平等的。了解您的技能在複雜性範圍內的位置有助於您做出正確的設計選擇：



|水平|風格|決定論|模型成本|最適合 |
| --- | --- | --- | --- | --- |
|被動|僅上下文 |不適用 |最低|背景知識、編碼標準 |
|教學 |規則+指南|中|低|程式碼審查、風格指南 |
| CLI 包裝器 |呼叫已編譯的二進位檔案 |高|低|自動化、整合、資料處理 |
|工作流程|多步驟驗證 |中|中等|部署管道、遷移 |
|生成 |要求代理程式編寫程式碼 |低|高|鷹架、程式碼生成 |



**關鍵見解：盡可能向左移動。 ** 更簡單的技能更可靠，執行成本更低，並且可以跨更多模型工作。



---



## 原則 1：決定論第一



精心設計的技能最重要的品質是**確定性**——相同的輸入每次都應該產生相同的輸出。



### 為什麼決定論很重要



- **便宜的模型可以運行確定性技能。 ** 表示「運行`eslint --fix`」的技能適用於任何模型。 「分析程式碼並提出改進建議」的技能需要昂貴的推理。
- **確定性技能不會被破壞。 ** CLI 命令要么成功，要么失敗並出現明顯錯誤。不明確的指令會默默地失敗或產生不一致的結果。
- **團隊需要可預測性。 ** 如果一項技能對不同的團隊成員產生不同的結果，就會造成混亂。



### 如何增加決定論



**優先使用命令而不是描述：**



```
# ✅ Deterministic — any model can run thisRun the formatter:`prettier --write "src/**/*.{ts,tsx}"`# ❌ Non-deterministic — model must reason about formatting rulesFormat the code following the project's style conventions.Ensure consistent indentation, trailing commas, and import ordering.
```



**優先選擇腳本而不是指令：**



```
# ✅ Deterministic — execute a scriptRun `./scripts/deploy.sh staging` to deploy.# ❌ Non-deterministic — model must reconstruct the deploy flowDeploy to staging:1. Build the project2. Run tests3. Push to the staging branch4. Wait for CI5. Verify the deployment
```



**優先考慮明確的價值觀而不是判斷：**



```
# ✅ DeterministicBlock any file larger than 100KB.# ❌ Non-deterministicBlock files that are too large.
```



---



## 原則 2：CLI 包裝模式



實現可靠技能的最強大技術：**將邏輯包裝在已編譯的 CLI 二進位檔案中，然後讓技能呼叫它。 **



### 模式



```
my-tool/                  # Compiled binary (Go, Rust, Swift, Bun)├── main.go└── ...my-skill/                 # Skill just calls the binary└── SKILL.md
```

SKILL.md

```
---name: my-tooldescription: Processes data files with my-tool CLI---# My ToolUse the `my-tool` CLI for data processing tasks.## Commands- `my-tool convert <input> <output>` — Convert between formats- `my-tool validate <file>` — Check file integrity- `my-tool analyze <file> --json` — Output analysis as JSON
```



### 為什麼這有效



1. **零運行時依賴。 ** Go 或 Rust 二進位檔案沒有 `node_modules`，沒有 `pip install`，沒有版本衝突。
2. **二進制行為是固定的。 ** 相同的二進位版本在每台機器上產生相同的結果。
3. **安全性。 ** 不存在傳遞依賴所帶來的供應鏈風險。二進位檔案是獨立的。
4. **適用於廉價型號。 ** 即使是最小的型號也可以執行`my-tool convert a.csv b.json`。



### 現實世界的例子



[Peter Steinberger](https://github.com/steipete)（PSPDFKit 創辦人）為其 AI 代理程式所需的一切建置編譯的 CLI：



|命令列 |語言 |目的|
| --- | --- | --- |
|戈克利|去 | Google 套件（Gmail、日曆、雲端硬碟）|
|躲貓貓 |斯威夫特 | AI 視覺的 macOS 截圖 |
|圖片 |斯威夫特 |傳送/接收 iMessages |
|麥克波特 |包子|將 MCP 伺服器轉換為 CLI 二進位檔案 |



他的方法：**SKILL.md 是一條單行指令，二進位檔案完成所有工作。 **



> 「代理真的非常擅長調用 CLI — 實際上比調用 MCP 好得多。您不必弄亂您的上下文，並且可以按需使用所有功能。」— [躲貓貓2.0](https://steipete.me/posts/2025/peekaboo-2-freeing-the-cli-from-its-mcp-shackles)



### 何時使用此模式



- 你有複雜的邏輯，不應該出現在提示中
- 您需要團隊成員之間可重複的行為
- 您正在與外部服務（API、資料庫、雲端）集成
- 安全性問題（無依賴供應鏈）



### 何時不使用此模式



- 簡單的知識或慣例（使用教學技能代替）
- 每次的邏輯都不同（使用生成技能）
- 您沒有時間建立 CLI（從說明開始，稍後重構）



---



## 原則 3：漸進式揭露



不要將所有內容轉儲到 SKILL.md 中。對您的內容進行分層，以便人工智慧僅加載它需要的內容。



### 三層



```
my-skill/├── SKILL.md           # Layer 1: Always loaded (~100 tokens in description)├── references/        # Layer 2: Loaded on demand│   ├── api-guide.md│   └── patterns.md├── scripts/           # Layer 3: Executed, not loaded into context│   └── validate.sh└── examples/          # Layer 3: Referenced by path    └── sample.json
```



**第 1 層 — 元資料**（始終在上下文中）：您的 `name` + `description` 在 frontmatter 中。保持在 200 個字元以內。這是AI用來決定是否啟動技能的依據。



**第 2 層 — 正文 + 引用**（技能啟動時載入）：SKILL.md 正文和任何引用的檔案。將 SKILL.md 保持在 500 行以內。詳細文檔放在`references/`。



**第 3 層 — 腳本 + 資產**（執行或路徑引用，從未載入）：腳本透過 Bash 運行，模板複製到輸出。这些不消耗上下文令牌。



### 上下文視窗是共享資源



您技能中的每個標記都會與使用者的程式碼、對話歷史記錄和其他技能競爭。問問自己：



> “這條線值得它花費的上下文令牌嗎？”



**前：**



```
## BackgroundPDF (Portable Document Format) was developed by Adobe in 1993. It's widely usedfor document exchange because it preserves formatting across platforms. PDFs cancontain text, images, forms, and multimedia. The PDF specification is maintainedby ISO as ISO 32000...## InstructionsUse pdfplumber to extract text from PDF files.
```



**後：**



```
Use `pdfplumber` for text extraction:    import pdfplumber    with pdfplumber.open("file.pdf") as pdf:        text = pdf.pages[0].extract_text()
```



AI 已經知道 PDF 是什麼。只添加它不知道的內容。



---



## 原則 4：將複雜度與風險相符



使用“窄橋與開放區域”啟發式：



|場景 |風險|自由|方法|
| --- | --- | --- | --- |
|資料庫遷移|高|低|確切的指令、驗證步驟、回滾計畫 |
|程式碼審查 |低|高|通用準則，讓AI使用判斷|
|部署到生產環境 |高|低|具有明確步驟和檢查的腳本 |
|編寫文檔 |低|高|風格指南+範例|



**高風險操作需要低自由度技能：**



```
## Database Migration⚠️ Follow these steps EXACTLY in order:1. Create backup: `pg_dump -Fc mydb > backup_$(date +%Y%m%d).dump`2. Run migration: `psql mydb < migrations/0042_add_index.sql`3. Verify: `psql mydb -c "SELECT count(*) FROM pg_indexes WHERE indexname = 'idx_users_email'"`4. If verification fails, rollback: `pg_restore -d mydb backup_*.dump`
```



**低風險操作可以高自由度：**



```
## Code Review GuidelinesWhen reviewing code, consider:- Are there obvious bugs or edge cases?- Is the code readable and well-structured?- Are there performance concerns?Adapt your review depth to the change size.
```



---



## 原則 5：先設計介面



在編寫技能之前，先定義它的契約──什麼觸發它，它做什麼，以及它產生什麼。



### 需要回答的五個問題



1. **何時應啟動此技能？ ** 編寫`description`字段，就像教導新團隊成員何時使用此工具一樣。
2. **需要什麼輸入？ ** 參數、檔案、環境狀態？
3. **成功是什麼樣的？ ** 具體的輸出格式、已建立的檔案、執行的命令？
4. **不應該做什麼？ ** 明確排除可以防止範圍蔓延。
5. **如何驗證它是否有效？ ** 包括驗證步驟。



### 模板



```
---name: {name}description: {what it does}. Use when {trigger condition}.---# {Name}{One sentence: what this does.}## When to Use{Specific trigger conditions — be precise}## Instructions{Steps — ordered, concrete, verifiable}## Verify{How to confirm it worked}## When NOT to Use{Explicit exclusions}
```



---



## 反模式



導致技能不可靠的常見錯誤：



### 1.廚房水槽



```
# ❌ Too many responsibilitiesThis skill handles code review, testing, deployment,documentation updates, and changelog generation.
```



**修正：** 一項技能 = 一個目的。分成單獨的技能。



### 2. 模糊指示



```
# ❌ Agent must guess what "properly" meansEnsure the code is properly formatted and follows best practices.
```



**修復：** 說出具體的工具和規則。



```
# ✅ Specific and actionableRun `prettier --write .` to format. Run `eslint --fix .` to lint.
```



### 3. 解釋人工智慧已經知道的內容



```
# ❌ Wasting context tokensReact is a JavaScript library for building user interfaces.Components are reusable pieces of UI. Props are passed fromparent to child components...
```



**修復：** 僅添加人工智慧不知道的內容 - 您專案的特定約定、內部 API、網域規則。



### 4. 太多選擇



```
# ❌ Choice paralysisYou can use pdfplumber, PyMuPDF, pdfminer, tabula-py, or camelotdepending on the use case...
```



**修復：** 給予一個預設值，僅在需要時提及替代方案。



```
# ✅ Clear defaultUse `pdfplumber` for text extraction. For scanned PDFs, fall back to `pytesseract`.
```



### 5. 無驗證步驟



```
# ❌ No way to confirm successDeploy the application to staging.
```



**修復：** 始終包含如何驗證。



```
# ✅ VerifiableDeploy to staging:1. Run `make deploy-staging`2. Verify: `curl -s https://staging.example.com/health | jq .status`   Expected: `"ok"`
```



### 6. 硬編碼路徑



```
# ❌ Breaks on other machinesEdit the file at /Users/john/projects/my-app/src/config.ts
```



**修復：** 使用相對路徑或環境變數。



---



## 測試你的技能



### 跨模型測試



在多個模型層上進行測試：



- **廉價型號**（例如俳句）：它可以按照說明操作嗎？如果沒有，請簡化。
- **中層模型**（例如 Sonnet）：它會產生一致的結果嗎？
- **頂級模型**（例如 Opus）：它是否尊重邊界，或者是否“改進”超出了範圍？



### 簡單性測試



> 如果廉價模型無法可靠地執行您的技能，則表示該技能太複雜。



這是您需要的最強訊號：



- 将逻辑提取到脚本或 CLI 二进制文件中
- 減少指令中的歧義
- 添加明確的命令而不是描述



### 迭代循環



```
Write skill → Sync → Test in AI CLI → Observe behavior → Edit → Repeat
```



使用 `skillshare sync` 部署更改，然後在 AI CLI 中進行測試。注意：



- AI是否在正確的時間啟動技能？
- 它是否依序執行步驟？
- 它會跳過或臨時設定步驟嗎？
- 驗證步驟是否發現失敗？



---



## 總結



|原理|單線|
| --- | --- |
|決定論第一 |命令重於描述，腳本重於指令 |
| CLI 包裝模式 |複雜邏輯→編譯二進制，技巧→薄包裝|
|漸進式揭露 |圖層內容：元資料→內文→引用→腳本|
|將複雜性與風險相匹配 |高風險=精確的步驟；低風險=指導方針|
|設計介面第一 |在編寫之前定義觸發器、輸入、輸出、排除 |



---



## 下一步：設計模式



了解上述原理後，請參閱[技能設計模式](https://skillshare.runkids.cc/docs/understand/philosophy/skill-design-patterns)，了解五個結構模板（工具包裝器、生成器、審查器、反轉、管道）​​，您可以將其用作技能的起點。



---



## 另請參閱



- [創作技巧](https://skillshare.runkids.cc/docs/how-to/daily-tasks/creating-skills) — 逐步創作指南
- [最佳實務](https://skillshare.runkids.cc/docs/how-to/daily-tasks/best-practices) — 命名、組織、版本控制
- [技能格式](https://skillshare.runkids.cc/docs/understand/skill-format) — SKILL.md 結構與元數據
- [保護你的技能](https://skillshare.runkids.cc/docs/how-to/advanced/security) — 安全掃描與審計
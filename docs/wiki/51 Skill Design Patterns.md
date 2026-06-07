# Skill Design Patterns

> Source: https://skillshare.runkids.cc/docs/understand/philosophy/skill-design-patterns

---

# 技能設計模式



五個結構模板可協助您快速啟動技能。選擇一個模式，使用 `skillshare new` 產生模板，然後從那裡進行自訂。

快速入門

使用`skillshare new my-skill -P <pattern>`產生任何模式的模板。運行 `skillshare new my-skill` 以獲得互動式選擇器。



---



## 第 1 部分：快速參考



### 模式概述



|圖案|它有什麼作用 |使用時間 |
| --- | --- | --- |
|工具包裝|教導代理如何使用庫/API |代理需要特定於域的約定 |
|發電機|從範本產生結構化輸出 |您需要一致的文件/程式碼格式 |
|審查者|根據清單進行評分/審核 |程式碼審查、安全審計、品質檢查 |
|反轉|代理人在行動前訪問使用者 |需求收集、專案規劃 |
|管道|帶有檢查點的多步驟工作流程 |需要驗證門的複雜任務 |



### 我應該使用哪一種模式？號



是的

不

是的

不

是的

不

是的

不

你的什麼
需要技巧嗎？

教如何
使用什麼東西？

工具包裝紙

生產
格式化輸出？

發電機

檢查品質
或合規性？

審稿人

需要資訊
從用戶優先？

反轉

管道



### 用例類別



建立技能時，您也可以使用**類別**對其進行標記以表示其網域：



|類別 |描述 |範例 |
| ---| ---| ---|
|圖書館 |庫與 API 參考 |計費庫、內部平台-cli |
|驗證|產品驗證 |註冊流程驅動程式、結帳驗證程序 |
|資料|資料取得與分析|漏斗查詢，grafana |
|自動化|業務流程和團隊自動化|站立帖子，每週回顧 |
|腳手架|程式碼腳手架和模板|新遷移，創建應用程式 |
|品質 |程式碼品質和審查|對抗性審查、測試實踐|
| CICD | CI/CD 與部署 |保母公關、部署服務 |
|運作手冊|操作手冊與事件回應| oncall-runner，對數相關器 |
|基礎設施 |基礎設施運作|孤兒清理，成本調查|



類別儲存在 SKILL.md frontmatter 中，並且獨立於模式 - 任何模式都可以與任何類別組合。



---



## 第 2 部分：詳細範例



### 工具包裝器



透過嵌入約定和使用範例，教導代理人如何使用特定的函式庫、框架或 API。



代理程式會載入一次約定，並在編寫或檢查涉及該程式庫的程式碼時套用它們。這可以讓特定領域的知識遠離你的頭腦，並轉化為可重複的技能。



**技能範例.md：**



```
---name: billing-libdescription: >-  Conventions for the billing-lib SDK. Use when writing or reviewing  code that imports billing-lib or handles payment flows.pattern: tool-wrappercategory: library---# Billing Lib## Core ConventionsLoad and follow the rules in `references/conventions.md` before writing any code.## When Reviewing Code- Check that all API calls follow the conventions- Verify error handling matches the library's patterns- Ensure imports and initialization are correct## When Writing Code- Follow the conventions from `references/conventions.md`- Use idiomatic patterns for this library/API- Include error handling for common failure modes
```



**目錄結構：**



```
billing-lib/├── SKILL.md└── references/    └── conventions.md      # API patterns, error codes, initialization
```



**變體：** 一些工具包裝技能包括帶有複製貼上片段的`references/examples.md`，或用於版本升級的`references/migration.md`。



---



### 發電機



透過根據樣式指南填入範本來產生結構化輸出（文件、設定檔、程式碼）。代理從使用者收集變量，然後每次產生一致的輸出。



**技能範例.md：**



```
---name: rfc-writerdescription: >-  Generates RFC documents following the team template. Use when user  says "write an RFC", "new proposal", or "design doc".pattern: generatorcategory: scaffold---# RFC Writer## Steps### Step 1: Load Style GuideRead `references/style-guide.md` for formatting and naming rules.### Step 2: Load TemplateRead `assets/template.md` as the base structure.### Step 3: Gather InputAsk the user what they need generated. Collect all required variables.### Step 4: GenerateFill in the template following the style guide. Ensure all placeholders are replaced.### Step 5: DeliverPresent the generated output. Ask if adjustments are needed.
```



**目錄結構：**



```
rfc-writer/├── SKILL.md├── assets/│   └── template.md         # RFC skeleton with placeholders└── references/    └── style-guide.md       # Formatting, section ordering, naming
```



**變體：** 一些生成器跳過樣式指南並將所有規則直接放入模板中。其他在`assets/`中包含針對不同文檔類型的多個範本。



---



### 審稿人



分數或審核根據定義的清單進行。代理讀取目標，應用每個標準，並產生包含嚴重性等級和通過/失敗分數的結構化報告。



**技能範例.md：**



```
---name: pr-reviewdescription: >-  Reviews pull requests against the team quality checklist. Use when  user says "review this PR", "check this code", or "audit quality".pattern: reviewercategory: quality---# PR Review## Steps### Step 1: Load ChecklistRead `references/review-checklist.md` for the complete list of review criteria.### Step 2: UnderstandRead the code/document under review. Identify its purpose and scope.### Step 3: Apply RulesEvaluate each checklist item. Classify findings by severity:- **Critical**: Must fix before proceeding- **Warning**: Should fix, may cause issues later- **Info**: Suggestion for improvement### Step 4: ReportProduce a review report with:1. Summary (pass/fail + one-line verdict)2. Findings (severity, location, description)3. Score (percentage of checklist items passed)4. Top 3 recommended fixes
```



**目錄結構：**



```
pr-review/├── SKILL.md└── references/    └── review-checklist.md  # Criteria with severity weights
```



**變體：** 一些審查者技能包括 `references/examples.md` 顯示每個規則的好代碼與壞代碼。其他人則添加 `references/scoring-rubric.md` 進行加權評分。



---



### 反轉



翻轉通常的互動：代理不是由使用者告訴代理要做什麼，而是在採取行動之前採訪使用者以收集需求。這可以防止“先構建，稍後詢問”問題。



**技能範例.md：**



```
---name: project-plannerdescription: >-  Plans a new project by interviewing the user about goals, constraints,  and success criteria. Use when user says "plan a project", "new feature  spec", or "help me think through this".pattern: inversioncategory: automation---# Project Planner**DO NOT start building until all phases are complete.**## Phase 1: DiscoveryAsk the user these questions before proceeding:- What is the goal?- Who is the audience?- What does success look like?## Phase 2: ConstraintsAsk the user about constraints:- What are the technical limitations?- What is the timeline?- Are there existing patterns to follow?## Phase 3: SynthesisBased on the answers, load `assets/template.md` and produce a plan.Present the plan for approval before executing.
```



**目錄結構：**



```
project-planner/├── SKILL.md└── assets/    └── template.md          # Plan document skeleton
```



**變化：** 一些反轉技能在`references/interview-questions.md`中有固定的問題列表，而不是內嵌的。其他人添加了階段 0，在提出問題之前讀取現有項目上下文。



---



### 管道



編排多步驟工作流程，其中每個階段都有一個驗證門。代理在繼續之前必須通過每個檢查點，這可以防止複雜操作中的級聯故障。



**技能範例.md：**



```
---name: deploy-stagingdescription: >-  Deploys to staging with pre-flight checks and rollback plan. Use when  user says "deploy to staging", "push to staging", or "staging release".pattern: pipelinecategory: cicd---# Deploy Staging## Steps### Step 1: PrepareGather inputs and validate prerequisites:- Confirm branch is clean (`git status`)- Run tests (`make test`)- Check CI status### Step 2: Gate CheckPresent the plan to the user.**Do NOT proceed until user confirms.**### Step 3: ExecuteRun the deployment pipeline. After each stage, verify output before continuing:1. Build: `make build`2. Push: `make push-staging`3. Health check: `curl -sf https://staging.example.com/health`### Step 4: Quality CheckReview results against `references/quality-checklist.md`.Report pass/fail status for each criterion.
```



**目錄結構：**



```
deploy-staging/├── SKILL.md├── references/│   └── quality-checklist.md  # Post-deploy verification criteria├── assets/│   └── rollback-plan.md      # Steps to undo if something fails└── scripts/    └── healthcheck.sh        # Automated health verification
```



**變體：** 某些管道包含一個 `scripts/` 目錄，其中代理執行自動化。其他人則直接在 SKILL.md 正文中嵌入回滾指令。



---



## 模式組成



這些圖案是構建塊，而不是剛性模具。將它們組合起來以滿足您的實際需求：



- **管道 + 審核者：** 部署管道以品質審核步驟結束，在將部署標記為完成之前根據清單對部署進行評分。
- **反轉+產生器：** RFC 技能，首先採訪使用者有關目標和限制的資訊（反轉），然後使用收集到的信息填充模板（生成器）。
- **工具包裝器 + 審查者：** 一種庫技能，既教授約定（工具包裝器），又可以審核現有程式碼的合規性（審查者）。



從單一模式開始，然後隨著您的技能範圍的增長而分層添加其他模式。



---



## 另請參閱



- [技能設計](https://skillshare.runkids.cc/docs/understand/philosophy/skill-design)－設計原則（決定論、漸進式揭露、複雜度匹配）
- [創作技巧](https://skillshare.runkids.cc/docs/how-to/daily-tasks/creating-skills) — 逐步創作指南
- [技能格式](https://skillshare.runkids.cc/docs/understand/skill-format) — SKILL.md 結構與元數據
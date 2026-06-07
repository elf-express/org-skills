# Skill Format

> Source: https://skillshare.runkids.cc/docs/understand/skill-format

---

# 技能格式



技能共享技能的結構和元資料。

這什麼時候重要？

SKILL.md 格式決定 AI CLI 如何發現和載入您的技能。 `description`字段尤其重要——人工智慧用它來決定何時啟動你的技能。



## 概述



一個技能是一個至少包含一個 `SKILL.md` 檔案的目錄：



```
my-skill/└── SKILL.md
```



`SKILL.md` 文件有兩個部分：



1. **YAML frontmatter** — 元數據
2. **Markdown 正文** — AI 說明



---



## 基本結構



```
---name: my-skilldescription: Brief description of what this skill does---# My SkillInstructions for the agent when this skill is activated.## When to UseDescribe when this skill should be used.## Instructions1. First step2. Second step3. Additional steps as needed
```



---



## 必填欄位



### `name`



技能標識符。用於：



- 呼叫技能（例如，`/skill:my-skill`）
- 碰撞偵測
- 在技能清單中顯示



```
name: my-skill
```



**規則：**



- 小寫字母、數字、連字號、底線
- 必須以字母或數字開頭
- 所有技能都應該是獨一無二的



**範例：**



```
name: code-reviewname: pdf-toolsname: acme-frontend-ui  # Namespaced for teams
```



---



## 可選字段



### `description`



技能清單和搜尋結果中顯示的簡要說明。



```
description: Reviews code for bugs, style issues, and improvements
```



---



## 可選字段



### `tags`



用於在中心索引中過濾和分組的分類標籤。當您執行 `skillshare hub index` 時，來自 SKILL.md frontmatter 的標籤將包含在產生的 `skillshare-hub.json` 中。



```
tags: git, workflow
```



標籤也可搜尋 - `skillshare search workflow --hub ...` 符合標示「工作流程」的技能。



### `targets`



限制該技能同步到的目標。省略時，技能會同步到**所有**目標。



支援兩種放置樣式 - 在`metadata:`下（建議）或在頂層：



```
# Recommended: under metadatametadata:  targets: [claude, cursor]# Legacy: top-level (still fully supported)targets: [claude, cursor]
```

優先規則

如果兩者都存在，`metadata.targets` 優先於頂級`targets`。這可以讓您逐步遷移 - 添加 `metadata:` 不會與剩餘的頂級字段發生衝突。



|價值|行為 |
| --- | --- |
| （省略） |同步到所有目標（預設）|
| [克勞德] |僅同步到符合「claude」的目標 |
| [克勞德，遊標] |同步到與任一名稱 | 匹配的目標



**跨模式匹配：** 宣告`targets: [claude]`的技能也符合專案目標`claude`，因為兩者都引用相同的AI CLI。配對使用[目標註冊表](https://skillshare.runkids.cc/docs/reference/targets/supported-targets)。



**與配置過濾器互動：**技能等級`targets`在**配置等級`include`/`exclude`之後套用。兩者都必須通過才能同步技能。參見【配置】(https://skillshare.runkids.cc/docs/reference/targets/configuration#skill-level-targets)。



**範例 - 克勞德專用技能：**



```
---name: claude-promptsdescription: Prompt patterns for Claude Codemetadata:  targets: [claude]---# Claude Prompts...
```



即使您配置了 Cursor、Codex 和其他目標，該技能也只會出現在 Claude Code 的技能目錄中。



### `pattern`



此技能使用的結構設計模式。由`skillshare new -P <pattern>`自動產生。



```
pattern: reviewer
```



可用圖案：`tool-wrapper`、`generator`、`reviewer`、`inversion`、`pipeline`。詳細內容請參考【技能設計模式】(https://skillshare.runkids.cc/docs/understand/philosophy/skill-design-patterns)。



### `category`



該技能的用例類別。在`skillshare new`期間互動式設定或完全省略。



```
category: quality
```



可用類別：`library`、`verification`、`data`、`automation`、`scaffold`、⟦、P53⟧、`cicd`、⟦P55⟦、P53⟧、`cicd`、⟦P55⟦、P53256⟦⧟。



### `license`



技能的許可證識別碼。在安裝過程中顯示以協助做出合規性決策。



```
license: MIT
```



如果存在，`skillshare install` 在技能選擇提示和確認畫面中顯示許可證：



- **單一技能**：在技能資訊框中顯示為`License: MIT`
- **多重技能儲存庫**：附加到選擇清單中的技能名稱（例如，`my-skill (MIT)`）



這純粹是提供資訊 - 它不會阻止安裝。常用值：`MIT`、`Apache-2.0`、`GPL-3.0`、`BSD-3-Clause`、`ISC`。



---



## `metadata` 區塊



`metadata:` 區塊是用於部署和行為領域的結構化 YAML 物件。這與 30 多個 AI CLI 工具中使用的[代理技能生態系統約定](https://developers.googleblog.com/en/5-agent-skill-design-patterns-every-adk-developer-should-know/) 一致。



```
---name: my-skilldescription: My custom skillmetadata:  targets: [claude]  pattern: reviewer  domain: python---
```



目前，`targets` 是 Skillshare 處理的唯一 `metadata` 字段。其他字段（如 `pattern`、`domain`、`interaction`）保留在 frontmatter 中，但不被 Skillshare 使用——它們可能會被生態系統中的其他工具使用。



為了向後相容，skillshare 還讀取頂級 `targets` 字段。如果兩者都存在，則`metadata.targets`優先。



## 自訂欄位



您可以新增任何自訂頂級欄位：



```
---name: my-skilldescription: My custom skillauthor: Your Nameversion: 1.0.0---
```



自訂頂級欄位儲存在 frontmatter 中，但技能共享本身不使用。



---



## Markdown 正文



正文包含人工智能的指令。就像您在指導人類助手一樣編寫它。



**良好做法：**



- 清晰、具體的說明
- 輸入和預期輸出的範例
- 邊緣情況與錯誤處理
- 何時使用（何時不使用）



**範例：**



```
# Code ReviewYou are a code reviewer. Analyze code for:- Bugs and potential issues- Style and consistency- Performance concerns- Security vulnerabilities## When to UseUse this skill when the user asks you to review code, find bugs, or improve code quality.## Instructions1. Read the provided code carefully2. Identify issues in order of severity3. Suggest specific improvements with code examples4. Be constructive and explain your reasoning## ExampleUser: "Review this function"```pythondef add(a, b): 回傳 a + b
```

 

Response: "The function looks correct but could benefit from type hints..."

 

```
---## 集中元資料當您安裝技能時，技能共享將其元資料記錄在`.metadata.json`（集中所有技能）：```json{  "skills": [    {      "name": "pdf",      "source": "anthropics/skills/skills/pdf",      "type": "github",      "installed_at": "2026-01-20T15:30:00Z",      "repo_url": "https://github.com/anthropics/skills.git",      "subdir": "skills/pdf",      "version": "abc1234"    }  ]}
```



每個技能條目包括：



|領域 |描述 |
| --- | --- |
|名稱 |技能目錄名稱 |
|來源 |原始安裝源輸入|
|類型 |來源類型（github、本地等）|
|安裝位置 |安裝時間戳記 |
|倉庫位址 | Git 克隆 URL（僅限 git 來源）|
|子目錄 |子目錄路徑（僅限 monorepo 來源）|
|版本 | Git 在安裝時提交哈希 |



`skillshare update` 和 `skillshare check` 使用它來了解從哪裡獲取更新。



**請勿手動編輯此文件。 **



---



## 創造技能



```
skillshare new my-skill
```



這將創建：



```
~/.config/skillshare/skills/my-skill/└── SKILL.md  (with template)
```



編輯產生的`SKILL.md`並運行`skillshare sync`進行部署。



---



## 驗證技能



```
skillshare doctor
```



檢查：



- 有效的 SKILL.md 格式
- 必填`name`字段
- 有效的 frontmatter YAML
- 名稱衝突



---



## 另請參閱



- [新](https://skillshare.runkids.cc/docs/reference/commands/new) — 使用正確的範本建立技能
- [創作技巧](https://skillshare.runkids.cc/docs/how-to/daily-tasks/creating-skills) — 寫作技巧完整指南
- [最佳實務](https://skillshare.runkids.cc/docs/how-to/daily-tasks/best-practices) — 命名與組織技巧
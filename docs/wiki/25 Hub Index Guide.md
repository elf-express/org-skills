# Hub Index Guide

> Source: https://skillshare.runkids.cc/docs/how-to/sharing/hub-index

---

# 中心索引指南



為您的組織建立集中技能目錄 - 無需 GitHub API 或令牌。



## 為什麼要使用中心索引？號



中心索引是一個 JSON 檔案 (`skillshare-hub.json`)，其中列出了技能及其名稱、描述和來源。在內部託管它，每個團隊成員都可以從中搜尋和安裝技能。



|使用案例| GitHub 搜尋 |中心索引 |
| ---| ---| ---|
|組織範圍的技能目錄 |沒有 |是的 |
|私人/內部技能|沒有 |是的 |
|氣隙/僅限 VPN 環境 |沒有 |是的 |
|精心策劃、認可的技能組合 |沒有 |是的 |
|不需要 GitHub 令牌 |沒有 |是的 |



有關實際範例，請參閱公共中心部分。



## 快速入門



### 1. 建立索引



```
# From your global skills
skillshare hub index
# From a project
skillshare hub index -p
# Output: <source>/skillshare-hub.json
```



### 2. 搜尋索引



```
# Local file
skillshare search react --hub ./skillshare-hub.json
# Remote URL
skillshare search react --hub https://internal.corp/skills/skillshare-hub.json
# Browse all skills (no query)
skillshare search --hub ./skillshare-hub.json --json
```



### 3. 從結果安裝



互動式搜尋流程與 GitHub 搜尋的工作方式相同 - 選擇一項技能即可安裝。



## 審計豐富



將安全風險評分添加到您的索引中，以便隊友可以一目了然地了解技能安全性：



```
# Build index with audit scores
skillshare hub index --audit
# Combine with full metadata
skillshare hub index --full --audit
```



使用`--audit`時，每個技能都會按照`skillshare audit`規則進行掃描，索引包括`riskScore`（0-100）、`riskLabel`（乾淨/低/中/高/嚴重）和`auditedAt`時間戳。掃描失敗的技能包含在沒有風險欄位的情況下。



經審計的索引的搜尋結果顯示風險徽章：



```
  1. safe-skill               owner/repo/safe-skill         [clean]  2. risky-skill              owner/repo/risky-skill        [high]
```



## 分享策略



### 檔案共享（最簡單）



將索引檔案複製到共用位置：



```
skillshare hub index -o /shared/team/skillshare-hub.json
```



隊友搜尋：



```
skillshare search --hub /shared/team/skillshare-hub.json
```



### HTTP 伺服器



在本地生成索引，然後將其上傳到您的託管：



```
# Step 1: Generate
skillshare hub index -o ./skillshare-hub.json
# Step 2: Upload (use your preferred method)
scp ./skillshare-hub.json server:/var/www/skills/
# or: aws s3 cp ./skillshare-hub.json s3://my-bucket/
# or: rsync, FTP, etc.
```



隊友搜尋：



```
skillshare search --hub https://skills.company.com/skillshare-hub.json
```



### Git 儲存庫



將索引提交到共用儲存庫，以便隊友可以拉取它：



```
skillshare hub index -o ./skillshare-hub.json
git add skillshare-hub.json && git commit -m "Update skill index"
git push
```



隊友可以透過原始 URL、SSH 或本地克隆進行搜尋：



```
# Via raw URL
skillshare search --hub https://raw.githubusercontent.com/team/skills/main/skillshare-hub.json
# Over SSH — clones the repo and reads the index (no manual clone needed)
skillshare search --hub git@github.com:team/skills.git
skillshare search --hub git@ghe.corp.com:team/skills.git//hubs/team.json
# Or clone and search locally
git pull
skillshare search --hub ./skillshare-hub.json
```

私有和 GitHub Enterprise 儲存庫

SSH 集線器來源是使用 SSH 代理/金鑰進行複製的，因此它們適用於原始 HTTPS URL 重定向到登入頁面的私人儲存庫和 GitHub Enterprise (GHE) 主機。儲存庫內的索引路徑來自 `//path` 後綴，預設為儲存庫根目錄下的 `skillshare-hub.json`。 scp 樣式 (`git@host:org/repo.git`) 和方案樣式 (`ssh://git@host/org/repo.git`) URL 皆有效。使用 [`hub add`](https://skillshare.runkids.cc/docs/reference/commands/hub#hub-add) 儲存一次即可按標籤搜尋。



## 網路儀表板



網頁儀表板（`skillshare ui`）支援中心搜尋：



1. 開啟**搜尋**頁面
2. 按一下「**中心**」標籤
3. 按一下**管理**新增中心來源（URL 或本機路徑）
4. 從下拉清單中選擇一個中心並蒐索
5. 直接從 UI 安裝



儲存的集線器保留在瀏覽器本機儲存中。



### 中心搜尋



![Hub search page](https://skillshare.runkids.cc/img/web-hub-search-demo.png)



從下拉清單中選擇中心來源並蒐索技能。



### 在集線器之間切換



![Hub dropdown selector](https://skillshare.runkids.cc/img/web-hub-dropdown-demo.png)



使用下拉式選單在多個集線器來源之間切換。



### 管理中心



![Manage hubs modal](https://skillshare.runkids.cc/img/web-hub-manage-demo.png)



按一下「**管理**」以新增、檢視或刪除中心來源。輸入 `skillshare-hub.json` 檔案的 URL 或本機檔案路徑。

儀表板中的 SSH 中心

儀表板僅克隆**已儲存的** SSH 集線器來源。在搜尋之前在此處新增 SSH 中心（或透過 [`hub add`](https://skillshare.runkids.cc/docs/reference/commands/hub#hub-add)）—伺服器不會複製臨時傳遞的任意 SSH URL。



### 刪除確認



![Hub delete confirmation](https://skillshare.runkids.cc/img/web-hub-delete-confirm-demo.png)



刪除集線器需要確認，以防止意外刪除。



## 索引架構



此索引遵循 Schema v1：



```
{  "schemaVersion": 1,  "generatedAt": "2026-02-12T10:00:00Z",  "sourcePath": "/home/user/.config/skillshare/skills",  "skills": [    {      "name": "my-skill",      "description": "Does something useful",      "source": "owner/repo/.claude/skills/my-skill",      "tags": ["workflow", "productivity"]    }  ]}
```



### 基本欄位（消費者合約）



|領域 |必填|描述 |
| ---| ---| ---|
|名稱 |是的 |技能顯示名稱|
|來源 |是的 |安裝來源（GitHub 簡寫、URL 或本機路徑）|
|描述 |推薦|搜尋符合的簡短描述 |
|技能 |沒有 |多技能儲存庫中的特定技能名稱（與 install -s 一起使用） |
|標籤 |沒有 |用於過濾和分組的分類標籤 |



### 文檔層級欄位



|領域 |描述 |
| ---| ---|
|架構版本 |總是 1 |
|產生於 | RFC 3339 時間戳記 |
|來源路徑 |解析相關來源的基本路徑|



### 來源路徑解析



當設定了 `sourcePath` 且技能的 `source` 是相對路徑時，搜尋使用者會加入它們：



```
sourcePath: /home/user/.config/skillshare/skillssource:     _team/frontend-skill→ resolved: /home/user/.config/skillshare/skills/_team/frontend-skill
```



這可以防止相對路徑被誤解為 GitHub 簡寫 (`owner/repo`)。



絕對路徑、URL 和網域前綴路徑永遠不會被連接：



|來源模式|加入了嗎？ |
| ---| ---|
| _團隊/我的技能 |是的 |
|子目錄/技能 |是的 |
| /絕對/路徑 |沒有 |
| github.com/owner/repo/skill | github.com/owner/repo/skill沒有 |
| https://... |沒有 |



## 手寫索引



您可以手動建立索引，而無需使用`hub index`。這對於託管在私人基礎設施上的內部技能特別有用 - GitHub 搜尋和公共工具永遠無法存取的來源：



```
{  "schemaVersion": 1,  "skills": [    {      "name": "company-style",      "description": "Company coding standards and review checklist",      "source": "ghe.internal.company.com/platform/ai-skills/company-style",      "tags": ["quality", "workflow"]    },    {      "name": "deploy-helper",      "description": "Internal deployment automation",      "source": "gitlab.internal.company.com/ops/skills/deploy-helper",      "tags": ["devops"]    },    {      "name": "onboarding",      "description": "New hire onboarding skill for AI assistants",      "source": "ghe.internal.company.com/hr/ai-skills/onboarding",      "tags": ["workflow"]    }  ]}
```

為什麼不直接使用 GitHub 搜尋呢？

`skillshare search` 僅在 github.com 上找到公共儲存庫。中心索引可以指向**任何**來源 - GitHub Enterprise、私人 GitLab、內部伺服器 - 只有 VPN 背後的員工才能存取的內容。這使得 Hub 成為組織範圍內技能分配的首選解決方案。



手寫索引的技巧：



- `sourcePath` 是可選的 — 如果所有來源都是絕對的則省略
- `tags` 是可選的 — 對於在網站或搜尋中進行過濾很有用
- `name`為空的技能會被跳過
- 結果按名稱字母順序排序



## 組織部署



在整個組織中推出中心的典型端到端工作流程：



```
# 1. A skill admin curates skills from internal repos
skillshare install ghe.internal.company.com/platform/ai-skills/coding-standards
skillshare install ghe.internal.company.com/platform/ai-skills/review-checklist
skillshare install ghe.internal.company.com/security/ai-skills/threat-model
# 2. Generate the hub index (with optional audit scores)
skillshare hub index --audit -o ./skillshare-hub.json
# 3. Host it (pick one)
#    - Internal Git repo: commit and push
#    - S3/CDN: aws s3 cp ./skillshare-hub.json s3://skills-bucket/
#    - Intranet server: scp to your hosting
# 4. Team members add the hub once
skillshare hub add https://skills.internal.company.com/skillshare-hub.json --label company
# 5. Search and install — only accessible behind VPN
skillshare search coding --hub company
```



為了保持索引最新，請將 `skillshare hub index` 新增至技能變更後運行的 CI 管道。



## 公共中心



[skillshare-hub](https://github.com/runkids/skillshare-hub) 是優質技能的精選目錄。它是 **預設中心** — 當您執行 `search --hub` 而不指定來源時，它會在此處搜尋：



```
skillshare search --hub              # Browse all skills in the public hub
skillshare search react --hub        # Search for "react" skills
```



它也可以作為建立您自己的組織中心的參考：



- **索引結構** — 如何使用名稱、描述、來源和標籤組織`skillshare-hub.json`
- **CI 驗證** — 對每個 PR 進行自動 JSON 格式檢查和 `skillshare audit` 安全掃描
- **貢獻工作流程** — 分叉 → 新增條目 → PR，帶有 CI 門



想為您的團隊建立內部中心嗎？分叉儲存庫，用組織的目錄取代技能，並自訂 CI 管道以符合您的安全策略。



## 提示



- **自動產生索引** — 在技能變更後將 `skillshare hub index` 新增至您的 CI 管道
- **使用`--full`進行審核** - 完整模式包括版本、安裝日期和類型信息
- **與專案模式結合** — `skillshare hub index -p` 僅索引專案級技能



---



## 另請參閱



- [搜尋](https://skillshare.runkids.cc/docs/reference/commands/search) — 從中心搜尋技能
- [hub](https://skillshare.runkids.cc/docs/reference/commands/hub) — 管理中心源
- [安裝](https://skillshare.runkids.cc/docs/reference/commands/install) — 安裝發現的技能
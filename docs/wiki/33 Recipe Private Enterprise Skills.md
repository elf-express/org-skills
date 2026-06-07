# Recipe Private Enterprise Skills

> Source: https://skillshare.runkids.cc/docs/how-to/recipes/private-enterprise-skills

---

#秘方：私人企業技能



> 使用令牌身份驗證從私有儲存庫安裝技能。



## 場景



您的組織在私人 GitHub/GitLab 儲存庫中託管內部技能。您需要安裝和更新這些技能而不暴露設定檔中的憑證。



## 解決方案



### 第 1 步：設定身份驗證



Skillshare 從環境變數中偵測令牌，特定於平台的變數優先於通用後備：



|平台|環境變數|
| ---| ---|
| GitHub / GitHub 企業版 | GITHUB_TOKEN |
| GitLab / 自架 GitLab | GITLAB_TOKEN |
|位元桶 | BITBUCKET_TOKEN（+ 可選 BITBUCKET_USERNAME）|
| Azure 開發營運 | AZURE_DEVOPS_TOKEN |
|任何平台（後備）|技能分享_GIT_TOKEN |



```
# Option A: Git credential helper (recommended for GitHub)
gh auth login   # sets up git credential helper for HTTPS
# Option B: Platform-specific environment variable
export GITHUB_TOKEN=ghp_xxxxxxxxxxxxx      # GitHub
export GITLAB_TOKEN=glpat-xxxxxxxxxxxxx    # GitLab
export AZURE_DEVOPS_TOKEN=your-pat-here    # Azure DevOps
# Option C: Generic fallback (works with any HTTPS host)
export SKILLSHARE_GIT_TOKEN=your-token-here
```



### 第 2 步：從私人儲存庫安裝



```
skillshare install your-org/internal-skills --track
```



Skillshare 會自動從上面列出的環境變數中偵測令牌。



### 第 3 步：驗證追蹤



```
skillshare list
```



安裝的儲存庫顯示帶有 `_` 前綴（追蹤儲存庫）：



```
_your-org-internal-skills/├── code-review/├── testing-standards/└── deployment-checklist/
```



### 第 4 步：更新周期



```
skillshare check    # Detect upstream changes
skillshare update   # Pull latest
skillshare sync     # Push to targets
```



## 驗證



- `skillshare list` 顯示追蹤的倉庫
- `skillshare check`可以到達遠程並比較哈希值
- `skillshare sync` 在所有目標中建立符號鏈接



## 變化



- **選擇性安裝**：`skillshare install your-org/internal-skills --track --skill code-review`僅安裝一項技能
- **CI/CD 令牌**：在管道中，從 CI 機密設定特定於平台的環境變數（例如，`GITHUB_TOKEN`）
- **自架 GitLab**：設定 `GITLAB_TOKEN` 並使用 HTTPS URL：`skillshare install https://gitlab.internal.com/team/skills.git --track`
- **Gitee / AtomGit**：透過帶有 `SKILLSHARE_GIT_TOKEN` 的 HTTPS URL 支持



## 相關



- [`install`指令參考](https://skillshare.runkids.cc/docs/reference/commands/install)
- [`update`指令參考](https://skillshare.runkids.cc/docs/reference/commands/update)
- [組織分享指南](https://skillshare.runkids.cc/docs/how-to/sharing/organization-sharing)
- [URL格式參考](https://skillshare.runkids.cc/docs/reference/appendix/url-formats)
# install

> Source: https://skillshare.runkids.cc/docs/reference/commands/install#json-output

---

＃ 安裝



從 GitHub 儲存庫、git URL 或本機路徑新增技能。



## 概述



安裝

來源

同步

目標

更新

解除安裝

同步

從目標中刪除



## 何時使用



- 從 GitHub、GitLab、Bitbucket、Azure DevOps 或本地路徑新增技能
- 安裝組織的共享技能儲存庫（使用`--track`）
- 重新安裝或更新現有技能（使用`--update`或`--force`）



---



## 簡單範例



```
# From GitHub (shorthand)
skillshare install anthropics/skills/skills/pdf
# Browse available skills in a repo
skillshare install anthropics/skills
# From local path
skillshare install ~/Downloads/my-skill
# As tracked repo (for team sharing)
skillshare install github.com/team/skills --track
# Install into a subdirectory (organize by category)
skillshare install ~/my-skill --into frontend
# Install all skills from config (no arguments)
skillshare install
```



## 來源格式



### GitHub 速記



使用 `owner/repo` 格式 — 自動擴充為 `github.com/owner/repo`：



```
skillshare install anthropics/skills                    # Browse mode
skillshare install anthropics/skills/skills/pdf         # Direct install
skillshare install ComposioHQ/awesome-claude-skills     # Another repo
```



### GitLab / Bitbucket / 其他主機



對於非 GitHub 主機使用 `domain/owner/repo` 格式：



```
skillshare install gitlab.com/user/repo                 # GitLab
skillshare install bitbucket.org/team/skills            # Bitbucket
skillshare install git.company.com/team/skills          # Self-hosted
```



完整 URL 和 SSH 也可以使用：



```
skillshare install https://gitlab.com/user/repo.git
skillshare install git@gitlab.com:user/repo.git
```

在自訂網域上自我管理的 GitLab

使用嵌套子組支援自動偵測名稱中包含 `gitlab` 或 `jihulab` 的主機。對於自訂網域上的其他自我管理的 GitLab 實例（例如，`git.company.com`），請將主機名稱新增至設定中的 [`gitlab_hosts`](https://skillshare.runkids.cc/docs/reference/targets/configuration#gitlab_hosts)，以便 Skillshare 將完整 URL 路徑視為儲存庫。如果沒有配置，您可以附加 `.git` 作為解決方法：`git.company.com/team/frontend/ui.git`。



### Azure DevOps



使用 `ado:` 簡寫或完整的 Azure DevOps URL：



```
# Shorthand (ado:org/project/repo)
skillshare install ado:myorg/myproject/myrepo
skillshare install ado:myorg/myproject/myrepo/skills/react    # With subdir
# Full HTTPS URL
skillshare install https://dev.azure.com/myorg/myproject/_git/myrepo
# Legacy format (auto-normalized)
skillshare install https://myorg.visualstudio.com/myproject/_git/myrepo
# SSH
skillshare install git@ssh.dev.azure.com:v3/myorg/myproject/myrepo
```



## 發現模式（瀏覽技巧）



當您不指定路徑時，skillshare 會複製儲存庫、掃描技能並顯示互動式選擇器：



```
skillshare install anthropics/skills
```



![install demo](https://skillshare.runkids.cc/img/install-demo.png)



Discovery 掃描所有目錄中的 `SKILL.md` 文件，僅跳過 `.git`。這意味著像`.curated/`或`.system/`這樣的隱藏目錄中的技能會被自動發現。當找到多個技能時，選擇提示會按目錄將它們分組，以便於瀏覽。



如果儲存庫的根目錄包含 `.skillignore` 文件，則匹配的技能將自動從發現中排除。請參閱下面的.skillignore。



如果技能的 `SKILL.md` 包含 `license:` frontmatter 字段，則許可證將顯示在選擇提示中（例如，`my-skill (MIT)`）以及單技能安裝的確認畫面中。



**提示**：使用`--dry-run`即可預覽，無需安裝：



```
skillshare install anthropics/skills --dry-run
```



## 選擇性安裝（非互動式）



從多技能儲存庫中選擇特定技能，無需提示。 `--skill` 標誌支援 **模糊匹配** 和 **glob 模式** - 如果找不到確切的名稱，它會嘗試 glob 匹配（`*`、`?`、`[...]`），然後回退到最接近的子字串匹配：



```
# Install specific skills by name (exact or fuzzy)
skillshare install anthropics/skills -s pdf,commit
# Install skills matching a glob pattern
skillshare install anthropics/skills -s "core-*"
# Install all discovered skills
skillshare install anthropics/skills --all
# Auto-accept (same as --all for multi-skill repos)
skillshare install anthropics/skills -y
# Combine with other flags
skillshare install anthropics/skills -s pdf --dry-run
skillshare install anthropics/skills --all -p
```



全域匹配不區分大小寫：`"Core-*"` 匹配 `core-auth`、`CORE-DB` 等。

外殼全域保護

始終引用 glob 模式 (`"core-*"`)，以防止 shell 將 `*` 擴展為目前目錄中的檔案名稱。



對於 CI/CD 管道和腳本化工作流程很有用。



## 直接安裝（具體路徑）



提供完整路徑以立即安裝：



```
# GitHub with subdirectory
skillshare install anthropics/skills/skills/pdf
skillshare install google-gemini/gemini-cli/packages/core/src/skills/builtin/skill-creator
# Fuzzy subdirectory — if exact path doesn't exist, matches by skill name
skillshare install runkids/my-skills/vue-best-practices
# Full URL
skillshare install github.com/user/repo/path/to/skill
# SSH URL
skillshare install git@github.com:user/repo.git
# SSH URL with subdirectory (use // separator)
skillshare install git@github.com:user/repo.git//path/to/skill
# Local path
skillshare install ~/Downloads/my-skill
skillshare install /absolute/path/to/skill
```

模糊子目錄解析

當指定像 `owner/repo/skill-name` 這樣的子目錄路徑時，如果儲存庫中不存在確切的路徑，skillshare 會掃描所有 `SKILL.md` 檔案並按目錄基本名稱進行比對。如果多個技能共用相同的名稱，則會顯示完整路徑的歧義錯誤，以便您可以指定確切的技能。



## 從組態安裝（無參數）



當不帶源參數運行時，`skillshare install`從`config.yaml`讀取`skills:`部分並安裝本地尚不存在的所有列出的遠端技能：



```
# Global — reads ~/.config/skillshare/config.yaml
skillshare install
# Project — reads .skillshare/config.yaml
skillshare install -p
```



這使得`config.yaml`成為**便攜式技能清單** - 共享它以在任何機器上重現相同的技能設定：



```
# New machine setup
skillshare install       # Installs all skills from config
skillshare sync          # Sync to targets
# New team member onboarding
git clone github.com/team/project && cd project
skillshare install -p    # Install all remote skills from project config
skillshare sync
```



`tracked: true` 的技能是透過完整的 git 歷史記錄克隆的（與 `--track` 相同），因此 `skillshare update` 可以正常工作。磁碟上已有的技能將被跳過。

推/拉與從配置安裝

`push`/`pull`透過git同步實際技能**檔案**。 `install` 從設定重新下載 **來源 URL**。它們是互補的 — 請參閱[跨機同步](https://skillshare.runkids.cc/docs/how-to/sharing/cross-machine-sync#alternative-install-from-config) 以了解何時使用哪一個。



使用無參數安裝時，不支援 `--name`、`--into`、`--track`、`--skill`、`--exclude`、`--all`、`--yes` 和 ⟦P95⧦（它們需要源參數）。 `--dry-run`、`--force`、`--skip-audit` 和閾值覆蓋 (`--audit-threshold` / `--threshold` / `-T`) 按預期工作。



## 專案模式



將技能安裝到專案的`.skillshare/skills/`目錄中：



```
# Install a skill into the project
skillshare install anthropics/skills/skills/pdf -p
# Install into a subdirectory within the project
skillshare install anthropics/skills -s pdf --into tools -p
# → .skillshare/skills/tools/pdf/
# Install all remote skills from config (for new team members)
skillshare install -p
```



### 有何不同



| |全球|計畫 (-p) |
| ---| ---| ---|
|目的地 | 〜/.config/skillshare/skills/| .skillshare/技能/ |
| --軌道|支援 |支援 |
|設定更新 |自動協調 config.yaml 技巧： |自動協調 .skillshare/config.yaml 技能： |
| 無參數安裝 |安裝 config | 中所列的所有技能安裝 config | 中所列的所有技能



**專案模式下追蹤的儲存庫**與全域儲存庫的工作方式相同 - 儲存庫被克隆，並保留`.git`並將其新增至`.skillshare/.gitignore`（預設也會忽略`.skillshare/logs/`和`.skillshare/trash/`）。 `tracked: true` 標誌自動記錄在 `.skillshare/config.yaml` 中：



```
skillshare install github.com/team/skills --track -p
skillshare sync
```



完整指南請參閱[項目設定](https://skillshare.runkids.cc/docs/how-to/sharing/project-setup)。



## 選項



|旗幟|短|描述 |
| ---| ---| ---|
| --name <姓名> | |僅安裝一項技能時覆蓋已安裝的名稱 |
| --進入<目錄> | |安裝到子目錄（例如 --into frontend 或 --into frontend/react）|
| --力| -f |涵蓋現有技能；覆蓋審核阻止和跨路徑重複檢查|
| --更新| -u |如果存在則更新（git pull 或重新安裝）|
| --branch <名稱> | -b |要複製的 Git 分支（預設：遠端預設分支）|
| --軌道| -t |保留 .git 以用於追蹤儲存庫 |
| --kind <技能\|代理> | |將安裝限制為一種資源類型 |
| --agent <姓名> | -a |從儲存庫中選擇特定代理（以逗號分隔） |
| --技能| -s |從多技能儲存庫中選擇特定技能（逗號分隔；支援 core-* 等全域模式）|
| --排除 | |在安裝過程中跳過特定技能（逗號分隔；支援 test-* 等 glob 模式）|
| --全部| |安裝所有發現的技能而不提示 |
| --是的| -y |自動接受所有提示（CI/CD 友善）|
| --跳過審計 | |跳過此安裝的安全審核 |
| --audit-threshold <t>, --threshold <t> | -T |覆寫此指令的審核區塊閾值（「關鍵 |
| --審計詳細 | |顯示每項技能的完整審核結果（預設：緊湊摘要）|
| --項目| -p |安裝到項目 .skillshare/skills/ |
| --全球| -g |安裝到全域 ~/.config/skillshare/skills/ |
| --空運行 | -n |僅預覽 |
| --json | |輸出為 JSON（意味著 --force；當沒有給出 --skill/--agent 過濾器時也意味著非互動式選擇）|



## JSON 輸出



```
skillshare install anthropics/skills --json
```



```
{  "source": "anthropics/skills",  "tracked": false,  "dry_run": false,  "skills": ["pdf", "commit", "review"],  "failed": [],  "duration": "2.345s"}
```



當使用`--into`時，包含`into`字段：



```
skillshare install anthropics/skills --json --into frontend
```



```
{  "source": "anthropics/skills",  "tracked": false,  "dry_run": false,  "into": "frontend",  "skills": ["pdf", "commit"],  "failed": [],  "duration": "1.890s"}
```



對於僅代理安裝，JSON 輸出仍使用 `skills` 陣列來報告已安裝的名稱：



```
skillshare install github.com/user/agents --kind agent --json
```



```
{  "source": "github.com/user/agents",  "tracked": false,  "dry_run": false,  "skills": ["reviewer", "tutor"],  "failed": [],  "duration": "1.234s"}
```



## 重複偵測



Skillshare 會自動偵測何時要安裝已存在的內容：



### 相同儲存庫重新安裝



如果技能已經存在並且是從**相同的儲存庫**安裝的，則 Skillshare 會跳過它並發出警告，而不是失敗：



```
skillshare install anthropics/skills/skills/pdf
# ✓ Installed pdf
skillshare install anthropics/skills/skills/pdf
# ⊘ pdf — already installed from same repo
```



使用`--update`刷新，或使用`--force`覆蓋。



### 交叉路徑重複



如果某個儲存庫已安裝在一個位置，並且您嘗試將其安裝在**不同的**位置，則 Skillshare 會封鎖該操作：



```
# First install (into a subdirectory)
skillshare install runkids/feature-radar --into feature-radar
# Later, forget about the first install
skillshare install runkids/feature-radar
# ✗ this repo is already installed at skills/feature-radar/scan (and 2 more)
#   Use 'skillshare update' to refresh, or reinstall with --force to allow duplicates
```



這可以防止不同路徑上的意外重複。使用`--force`有意允許它。



### 與不同倉庫的衝突



如果目標目錄存在但是從 **不同** 儲存庫安裝的，則錯誤訊息包括原始來源：



```
skillshare install owner/repo-b --name my-skill
# ✗ my-skill already exists (installed from https://github.com/owner/repo-a.git).
#   To overwrite: skillshare install owner/repo-b --name my-skill --force
```



`--force` 提示始終包含正確的標誌（包括 `--into` 如果適用）。



## 常見場景



**使用自訂名稱安裝：**



```
skillshare install google-gemini/gemini-cli/.../skill-creator --name my-creator
# Installed as: ~/.config/skillshare/skills/my-creator/
```



`--name` 僅在安裝解析為單一技能時才有效。在`--track`模式下，自訂名稱儲存為追蹤的儲存庫目錄（自動新增`_`前綴），且不得包含路徑分隔符號或`..`。



```
# ✅ Single skill (works)
skillshare install comeonzhj/Auto-Redbook-Skills --name haha
# ❌ Multiple discovered skills (errors)
skillshare install anthropics/skills --name my-skill
```



**強制覆蓋現有：**



```
skillshare install ~/my-skill --force
```



**更新現有技能：**



```
# By skill name (uses stored source)
skillshare install pdf --update
# By source URL
skillshare install anthropics/skills/skills/pdf --update
```



**安裝到子目錄：**



```
# Organize by category
skillshare install ~/my-skill --into frontend
# → ~/.config/skillshare/skills/frontend/my-skill/
# Multi-level nesting
skillshare install anthropics/skills -s pdf --into frontend/react
# → ~/.config/skillshare/skills/frontend/react/pdf/
# After sync, target shows flat name: frontend__my-skill, frontend__react__pdf
```



文件夾策略請參考【整理技巧】(https://skillshare.runkids.cc/docs/how-to/daily-tasks/organizing-skills)。



**從特定分支安裝：**



```
# Regular install from a branch
skillshare install github.com/team/skills --branch develop --all
# Track a specific branch
skillshare install github.com/team/skills --track --branch frontend
# Same repo, different branches (use --name to avoid collision)
skillshare install github.com/team/skills --track --branch frontend --name team-frontend
skillshare install github.com/team/skills --track --branch backend --name team-backend
```



**安裝團隊儲存庫（已追蹤）：**



```
skillshare install anthropics/skills --track
```



![tracked repo install demo](https://skillshare.runkids.cc/img/team-reack-demo.png)



## 私有儲存庫



### SSH（建議）



SSH 是最簡單的方法 - 如果您配置了 SSH 金鑰，它就可以工作：



```
skillshare install git@github.com:org/private-skills.git --track
skillshare install git@gitlab.com:org/skills.git --track
skillshare install git@bitbucket.org:team/skills.git --track
skillshare install git@ssh.dev.azure.com:v3/org/project/skills --track
# With subdirectory
skillshare install git@github.com:org/skills.git//frontend-react
```



### 帶令牌的 HTTPS



設定適當的環境變數並使用常規 HTTPS URL。 Skillshare 會自動偵測令牌並在克隆期間注入它：



```
export GITHUB_TOKEN=ghp_your_token
skillshare install https://github.com/org/private-skills.git --track
```



|平台|環境變數 |代幣類型 |
| ---| ---| ---|
| GitHub | GITHUB_TOKEN |個人存取權杖（回購範圍）|
| GitLab | GITLAB_TOKEN |個人存取或 CI 工作令牌 |
|位元桶 | BITBUCKET_TOKEN | BITBUCKET_TOKEN |儲存庫令牌或應用程式密碼（使用 BITBUCKET_USERNAME）|
| Azure 開發營運 | AZURE_DEVOPS_TOKEN |個人存取權杖（代碼：讀取範圍）|
|任意主機|技能分享_GIT_TOKEN |通用後備|



特定於平台的變數優先於`SKILLSHARE_GIT_TOKEN`。



官方代幣文檔：



- GitHub：[管理您的個人存取權杖](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens)
- GitLab：[代幣概述](https://docs.gitlab.com/security/tokens/)
- Bitbucket：[訪問令牌](https://support.atlassian.com/bitbucket-cloud/docs/access-tokens/)
- Azure DevOps：[使用個人存取權杖](https://learn.microsoft.com/en-us/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops)



對於 Bitbucket 應用程式密碼，也請設定您的使用者名稱：



```
export BITBUCKET_USERNAME=your_bitbucket_username
export BITBUCKET_TOKEN=your_app_password
skillshare install https://bitbucket.org/team/skills.git --track
```



### CI/CD 範例



**GitHub 操作：**



```
- name: Install shared skills  env:    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}  run: skillshare install https://github.com/org/skills.git --track
```



**GitLab 來自：**



```
install-skills:  script:    - skillshare install https://gitlab.com/org/skills.git --track  variables:    GITLAB_TOKEN: $CI_JOB_TOKEN
```



**位桶管道：**



```
- step:    name: Install shared skills    script:      - skillshare install https://bitbucket.org/team/skills.git --track    env:      BITBUCKET_USERNAME: $BITBUCKET_USERNAME   # for app passwords      BITBUCKET_TOKEN: $BITBUCKET_TOKEN
```



**Azure管道：**



```
- script: skillshare install https://dev.azure.com/org/project/_git/skills --track  env:    AZURE_DEVOPS_TOKEN: $(System.AccessToken)
```



## 安全掃描



安裝過程中會自動掃描每個技能的安全威脅：



- `audit.block_threshold` 或以上的結果 **阻止安裝**（預設值：`CRITICAL`）
- 較低的結果顯示為警告，並包括風險評分背景
- `audit.block_threshold`僅控制區塊層級；它**不**禁用掃描
- 沒有配置開關可以始終跳過審核；需要時在每個命令中使用 `--skip-audit`
- 您可以使用`--audit-threshold`、`--threshold`或`-T`覆寫每個指令的閾值



閾值配置範例：



```
audit:  block_threshold: HIGH
```



```
# Blocked — critical threat detected
skillshare install evil-skill
# → Installation blocked at active threshold. Use --force to override.
# Force install despite warnings
skillshare install suspicious-skill --force
# Skip scan entirely (use with caution)
skillshare install suspicious-skill --skip-audit
# Per-command threshold override (same meaning)
skillshare install suspicious-skill --audit-threshold high
skillshare install suspicious-skill --threshold high
skillshare install suspicious-skill -T h
```



使用`--force`覆蓋塊決策，或使用`--skip-audit`完全繞過掃描。掃描詳情請參閱【審核】(https://skillshare.runkids.cc/docs/reference/commands/audit)。



安裝決策使用**尋找嚴重性與閾值**。報告風險評分/標籤是為了了解上下文，本身不會阻止安裝。預設情況下，審核結果顯示為緊湊摘要（按嚴重性和訊息分組）。使用`--audit-verbose`查看完整清單。



### 追蹤回購審計門 (`--track`)



追蹤儲存庫使用相同的閾值模型，但掃描範圍和故障處理更嚴格：



- 全新的`--track`安裝會掃描**整個克隆存儲庫**（不僅僅是一個技能資料夾）
- 達到/高於閾值的結果阻止安裝，除非使用`--force`
- 在受阻的全新安裝中，skillshare 會自動從來源移除複製的儲存庫
- 如果自動清理失敗，安裝會傳回明確的錯誤並告訴您手動刪除路徑



透過安裝 (`skillshare install <repo> --track --update`) 追蹤的儲存庫更新在`git pull`之後進行審核：



- Skillshare 首先捕獲預拉提交哈希
- 如果哈希捕獲失敗，更新立即中止（失敗關閉）
- 如果偵測到達到/高於閾值的結果，更新將回滾到預拉提交
- 如果回滾失敗，命令退出並警告可能殘留惡意內容



### `--force` vs `--skip-audit`



兩者都可以解鎖安裝，但它們的作用不同：



|旗幟|審計執行|會發生什麼 |
| ---| ---| ---|
| --力|審計仍在進行中 |仍會產生/記錄結果；即使達到閾值，安裝也會繼續|
| --跳過審計 |審核被跳過 |此安裝未執行任何掃描 |



推薦用法：



- 當您仍希望了解調查結果時，首選`--force`。
- 僅當您有意需要繞過掃描時才使用`--skip-audit`。
- 如果兩者均設置，則實際中`--skip-audit`優先（跳過掃描）。



## 排除技能



### `--exclude` 旗幟



從多技能儲存庫安裝時跳過特定技能。支援精確名稱和**全域模式**：



```
# Install all except specific skills
skillshare install anthropics/skills --all --exclude cli-sentry,delayed-command
# Exclude by glob pattern
skillshare install anthropics/skills --all --exclude "test-*"
# Works with -y too
skillshare install org/skills -y --exclude internal-tool
# Combine with --skill for fine-grained control
skillshare install org/skills -s pdf,commit,docs --exclude docs
```



排除技能後，會顯示一則訊息，顯示已跳過的內容：`Excluded 2 skill(s): cli-sentry, delayed-command`。

需要多技能發現

`--exclude` 僅在從包含多種技能的 **git 儲存庫** 安裝時才有效。它適用於 `--all`、`--yes`、`--skill` 和互動式選擇模式。對於直接安裝（本機路徑或單技能 git URL），`--exclude` 不適用 - 如果指定，則會顯示警告。



### .skillignore



儲存庫維護人員可以在儲存庫根目錄中建立一個 `.skillignore` 文件，以隱藏技能以防止發現。從儲存庫安裝的使用者將永遠不會在選擇提示中看到這些技能。

.skillignore

```
# Internal tooling — not for public usevalidation-scriptsscaffold-template# Exclude all test/eval skillsprompt-eval-*# Exclude an entire group directoryinternal-tools
```



**真實範例** — [`runkids/my-skills`](https://github.com/runkids/my-skills) 使用 `.skillignore` 排除非技能目錄與內部工具：

.skillignore

```
skillsharefeature-radar
```



結合`--exclude`，用戶可以進一步縮小選擇範圍：



```
skillshare install runkids/my-skills --exclude seo
```



**格式** — 使用 [gitignore 語法](https://git-scm.com/docs/gitignore)：



|圖案|範例|行為 |
| ---| ---| ---|
|確切的名字 |驗證腳本 |符合該路徑的技能 |
|小組賽|特徵雷達 |匹配特徵雷達下的所有技能/ |
|精準路徑|特徵雷達/特徵雷達 |僅該特定技能 |
| * 通配符 |提示-評估-* |符合一個段落（不交叉/）|
| ** | **/溫度|符合任意目錄深度 |
| ？ | ?.md |符合單一字元 |
| [ABC] | [Tt] 預計 |角色類別|
| ！模式| !重要|否定 - 取消忽略先前配對的技能 |
| /圖案| /僅限 root |錨定到 .skillignore 位置 |
|圖案/ |構建/ |僅目錄匹配 |
| \#,\! | \#檔 |轉義文字字元 |



以 `#` 開頭的行是註解。空行將被忽略。



**推薦場景：**



- 發布多技能儲存庫，同時隱藏內部工具或正在進行的技能
- 使用帶有分組技能目錄的 monorepo 並排除整個群組（例如，`internal-tools`）
- 執行維護人員層級的可見性規則，以便所有安裝人員永遠不會發現某些技能



**不適合：**



- 直接本機路徑安裝（這些跳過發現）
- 單一技能直接安裝（類似`--exclude`，直接安裝路徑會被忽略）



`.skillignore` 在 git repo 發現期間應用，因此它會影響所有基於發現的安裝路徑：`--all`、`--skill`、`--yes` 和互動式選擇。它**不適用於**直接本地路徑安裝（完全跳過發現）。

.skill忽略範圍

**儲存庫層級** `.skillignore`（在儲存庫根目錄中）控制當使用者從儲存庫安裝時可以發現哪些技能。安裝後，追蹤的儲存庫保留其 `.skillignore` — 它也受到 `doctor`、`status`、`list`、`sync`、⟦P166⧦、⟧、⟦

**源根** `.skillignore` (`~/.config/skillshare/skills/.skillignore`) 全域適用於所有技能 - 追蹤和非追蹤。使用它可以暫時靜音技能或排除模式（例如`draft-*`）而無需卸載。



### `.skillignore` vs `--exclude`



| | .skillignore | --排除 |
| ---| ---| ---|
|誰控制它|回購維護者 |安裝用戶 |
|它住在哪裡|倉庫根目錄中的.skillignore | CLI 標誌 |
|何時適用 |發現期間（選擇之前）|發現後（提示前）|
|範圍 |所有從此儲存庫安裝的使用者 |僅此安裝 |
|需要 |具有多種技能的 Git 倉庫 |具有多種技能的 Git 倉庫 |



## 代理支援



安裝儲存庫時，skillshare 會自動偵測代理程式（獨立 `.md` 檔案）以及技能：



- 如果儲存庫包含 `agents/` 目錄，則其中的 `.md` 檔案會被發現為候選代理
- 如果儲存庫同時包含 `skills/` 和 `agents/`，則兩者均已安裝
- 如果倉庫根目錄下只有鬆散的`.md`檔案（沒有`SKILL.md`），它們將被視為代理



### 明確代理標誌



```
# Install only agents from a repo
skillshare install github.com/user/repo --kind agent
# Install specific agents by name (-a shorthand)
skillshare install github.com/user/repo -a tutor,reviewer
# Combine with project mode
skillshare install github.com/user/repo --kind agent -p
```



`-a <name>` 標誌是相當於技能中的 `-s <name>` 的代理。代理安裝到`~/.config/skillshare/agents/`（全域）或`.skillshare/agents/`（專案）。完整概念請參考[代理](https://skillshare.runkids.cc/docs/understand/agents)。



### 在混合儲存庫中確定技能與代理的範圍



當儲存庫同時包含技能和代理程式時，過濾器會精確控制安裝的內容：



|旗幟|安裝了什麼 |
| ---| ---|
| （無）|所有技能及所有代理人|
| --全部/--是|所有技能和所有代理人|
| -s <姓名> |只有指定的技能－沒有代理人|
| -s <姓名> -a <姓名> |指定的技能和指定的代理|
| -a <姓名> |僅指定代理 |



```
# Install just one skill from a mixed repo — agents are NOT pulled in
skillshare install github.com/user/repo -s pdf
# Install one skill and one agent together
skillshare install github.com/user/repo -s pdf -a tutor
```



在安裝任何技能之前，未知的 `-a` 名稱會導致整個命令失敗，因此自動化永遠不會看到半完成的安裝。



## 安裝後



始終同步以分發到目標：



```
skillshare install anthropics/skills/skills/pdf
skillshare sync  # ← Don't forget!
```



## 另請參閱



- [列表](https://skillshare.runkids.cc/docs/reference/commands/list) — 查看已安裝的技能
- [更新](https://skillshare.runkids.cc/docs/reference/commands/update) — 更新技能或追蹤的儲存庫
- [升級](https://skillshare.runkids.cc/docs/reference/commands/upgrade) — 升級CLI與內建技能
- [卸載](https://skillshare.runkids.cc/docs/reference/commands/uninstall) — 刪除技能
- [sync](https://skillshare.runkids.cc/docs/reference/commands/sync) — 將技能同步到目標
- [組織範圍的技能](https://skillshare.runkids.cc/docs/how-to/sharing/organization-sharing) — 與追蹤的儲存庫共用組織
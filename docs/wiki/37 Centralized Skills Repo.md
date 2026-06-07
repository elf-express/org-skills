# Centralized Skills Repo

> Source: https://skillshare.runkids.cc/docs/how-to/recipes/centralized-skills-repo

---

# 集中技能庫



> 使用一個項目作為共享技能庫；其他項目保持乾淨。



## 場景



您的團隊有多個專案（B、C、D），但希望在單一專用儲存庫中管理 AI 技能 (A)。每個開發人員克隆儲存庫 A 並將目標指向自己的本機專案。



## 解決方案



### 建立者：設定共用儲存庫



```
cd ~/DEV/skills-repo        # Project A
skillshare init -p --config local --targets claude
```



這會創建 `.skillshare/` 和 `config.yaml` gitignored，因此每個開發人員都可以獨立管理自己的目標。



```
# Add shared skills
skillshare install <skill-repo> -p
# Commit (config.yaml is excluded by .gitignore)
git add .skillshare/
git commit -m "add shared skills"
git push
```



### 隊友：複製並配置



```
git clone <A-repo> && cd skills-repo
skillshare init -p
```



Skillshare 自動偵測共用儲存庫（`.gitignore` 包含`config.yaml`）並建立一個空配置。不需要 `--config local` 標誌。



```
# Add targets pointing to your local projects
skillshare target add project-b ~/DEV/project-b/.cursor/skills -p
skillshare target add project-c ~/DEV/project-c/.claude/skills -p
# Sync shared skills to all your targets
skillshare sync -p
```



## 工作原理



隊友

創作者

推

Skillshare init -p --config local

安裝技巧+git推播

git 克隆 + Skillshare init -p

目標添加+同步-p



`--config local` 標誌將 `config.yaml` 加入 `.skillshare/.gitignore`。這意味著：



- **技能** (`.skillshare/skills/`) 透過 git 分享
- **配置** (`.skillshare/config.yaml`) 對於每個開發人員來說都是本地的
- 每個開發者選擇自己的目標而不影響其他人



## 驗證



創建者運行`init -p --config local`後：



```
cat .skillshare/.gitignore
# Should contain: config.yaml
```



隊友克隆並運行`init -p`後：



```
skillshare list -p     # Shows shared skills
skillshare status -p   # Shows your personal targets
```



## FAQ



**問：每個隊友都需要`--config local`嗎？ ** 答：不需要，只有創作者才需要`--config local`。隊友只要運行`skillshare init -p`，skillshare 就會自動偵測共用儲存庫模式。



**問：隊友可以安裝額外技能嗎？ ** 答：可以。 `skillshare install <repo> -p`工作正常。安裝的技能會落在`.skillshare/skills/`中，並由git跟踪，因此您可以將其推送給其他人使用。



**問：如果隊友想要不同的技能怎麼辦？ ** 答：`.skillshare/skills/`中的技能是共享的。對於真正的個人技能，請使用[全局模式](https://skillshare.runkids.cc/docs/understand/project-skills)（`skillshare install <repo>`沒有`-p`）。
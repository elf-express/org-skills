# Sync Modes Explained

> Source: https://skillshare.runkids.cc/docs/understand/philosophy/sync-modes-explained

---

# 同步模式解釋



> 深入探討三種同步模式——合併、複製和符號連結——何時使用每種模式以及權衡。



## 三種模式



Skillshare 提供三種同步模式，用於控制如何將技能從來源目錄傳送到 AI 工具目標目錄。



### 合併模式（預設）



```
Source: ~/.config/skillshare/skills/├── code-review/SKILL.md├── testing/SKILL.md└── debugging/SKILL.mdTarget: ~/.claude/skills/├── code-review → ~/.config/skillshare/skills/code-review  (symlink)├── testing → ~/.config/skillshare/skills/testing           (symlink)├── debugging → ~/.config/skillshare/skills/debugging       (symlink)└── my-local-skill/SKILL.md                                 (untouched)
```



**How it works**: Creates one symlink per skill.目標中的每個技能目錄都指向來源。



**關鍵屬性**：**非破壞性**。目標目錄中的本地技能（如上面的`my-local-skill`）被保留。 Skillshare 僅管理它創建的符號連結。



### 複製模式



```
Source: ~/.config/skillshare/skills/├── code-review/SKILL.md├── testing/SKILL.md└── debugging/SKILL.mdTarget: ~/.cursor/skills/├── code-review/SKILL.md                (physical copy)├── testing/SKILL.md                    (physical copy)├── debugging/SKILL.md                  (physical copy)├── .skillshare-manifest.json           (tracks managed files)└── my-local-skill/SKILL.md             (untouched)
```



**工作原理**：將每項技能物理複製到目標中。 `.skillshare-manifest.json` 檔案追蹤管理哪些技能及其 SHA-256 校驗和。在後續同步中，僅重新複製已變更的技能。



**關鍵屬性**：**最大相容性**。适用于任何地方 - 不需要符号链接支持。就像合併模式一樣保留本地技能。



### 符號連結模式



```
Source: ~/.config/skillshare/skills/├── code-review/SKILL.md├── testing/SKILL.md└── debugging/SKILL.mdTarget: ~/.claude/skills → ~/.config/skillshare/skills/  (single symlink)
```



**工作原理**：用指向來源的單一符號連結取代整個目標目錄。



**關鍵屬性**：**完全控制**。目標正是源。目標中不能存在本地技能。



## 何時使用每個



|因素 |合併|複製|符號連結 |
| ---| ---| ---| ---|
|保留本地技能 |是的 |是的 |沒有 |
|跨平台支援 |可能有問題 |隨處可用 |可能有問題 |
|反映來源變化|立即 |同步後 |立即 |
|處理巢狀路徑 |展平 (a/b/c → a__b__c) |展平|原生結構 |
|孤兒清理|自動|自動|不需要|
|磁碟使用情況|最小（符號連結）|完整副本 |最小（一個符號連結）|
|建議用於 |大多數使用者 | WSL、Docker、CI |單一來源設定 |



### 選擇合併時間



- 您的 AI 工具擁有本地技能，但您不希望透過技能共享來管理這些技能
- 您使用多個具有不同本地客製化的人工智慧工具
- 您正在逐步採用技能共享（有些技能得到管理，有些則沒有）



### 選擇複製時間



- 您的平台具有不可靠的符號連結支援（WSL、某些 Docker 設定）
- AI 工具無法正確遵循符號鏈接
- 您處於 CI/CD 管道或容器化環境中
- 您希望目標獨立於來源目錄工作



### 選擇符號連結



- 技能共享是目標的唯一技能來源
- 您希望目標內容零歧義
- 你正在建立一個新鮮的環境



## 巢狀路徑處理



在合併和複製模式下，巢狀來源路徑使用雙下劃線進行扁平化：



```
Source: skills/frontend/react-patterns/SKILL.mdTarget: ~/.claude/skills/frontend__react-patterns → skills/frontend/react-patterns
```



這可以避免在需要扁平技能結構的目標中建立目錄。在符號連結模式下，目錄結構按原樣保留。



## 孤兒清理



合併和複製模式會在`skillshare sync`期間自動刪除孤立條目。如果您從來源中卸載技能，則目標中相應的符號連結（或複製的目錄）將在下次同步時清除。



```
skillshare uninstall old-skill
skillshare sync
# → Pruned orphan: old-skill
```



## 按目標模式覆蓋



您可以為每個目標設定不同的模式。全域配置使用地圖格式：



```
targets:  claude:    path: ~/.claude/skills    mode: merge  cursor:    path: ~/.cursor/skills    mode: copy
```



項目配置使用清單格式：



```
targets:  - name: claude    mode: merge  - name: cursor    mode: copy
```



或透過 CLI 更改模式：



```
skillshare target claude --mode copy
```



## 相關



- [同步模式概念頁](https://skillshare.runkids.cc/docs/understand/sync-modes)
- [`sync`指令參考](https://skillshare.runkids.cc/docs/reference/commands/sync)
- [來源與目標](https://skillshare.runkids.cc/docs/understand/source-and-targets)
# Sync Modes

> Source: https://skillshare.runkids.cc/docs/understand/sync-modes

---

# 同步模式



Skillshare 如何將來源連結到目標。

這什麼時候重要？

當您需要每個技能的符號連結並保留目標中的本地技能時，請選擇合併模式（預設）。當您需要真實文件而不是符號連結（可移植性、CI 或個人偏好）時，請選擇複製模式。當您想要連結整個目錄並且不需要本地目標技能時，請選擇符號連結模式。



## 概述



|模式|行為 |使用案例|
| --- | ---| ---|
|合併|每個技能都單獨符號連結|預設。保留當地技能。 |
|複製|每個技能複製為真實文件 |可移植性、CI/沙盒環境，或當您喜歡真實文件而不是符號連結時。 |
|符號連結 |整個目錄是一個符號連結 |到處都是一模一樣的副本。 |



## 決策矩陣（中性）



使用此表根據您的限制進行選擇，而不是針對品牌名稱：



|決策軸|合併|複製|符號連結 |
| --- | --- | --- | --- |
|不同 AI CLI 之间的兼容性 |中等|高|低-中 |
|编辑一次立即反映|高|低（需要同步）|高|
|磁碟使用情況|低|高|低|
|防止意外从目标删除的安全性高|高|低|
|操作簡單 |中|中|高|
|按目标过滤（包含/排除）|是的 |是的 |没有 |



如果您不確定，請從`merge`開始，然後根據需要將特定目標切換到`copy`。



---



## 合併模式（預設）



每個技能都是單獨的符號連結。目標的本地技能被保留。



```
Source                          Target (claude)
─────────────────────────────────────────────────────────────
skills/                         ~/.claude/skills/
├── my-skill/        ────────►  ├── my-skill/ → (symlink)
├── another/         ────────►  ├── another/  → (symlink)
└── ...                         ├── local-only/  (preserved)
                                └── .skillshare-manifest.json
```



**優點：**



- 保留特定目標的技能（不同步）
- 混合安裝和本地技能
- 精細化控制
- 每個目標包括/排除過濾
- 基於清單的孤立清理（卸載後安全刪除非符號連結殘留）

項目模式下的相對符號鏈接

在專案模式（`-p`）中，符號連結被建立為**相對路徑**（例如，`../../.skillshare/skills/my-skill`）而不是絕對路徑。這使得項目可移植 - 移動或重命名目錄並且符號連結繼續工作。在全域模式下，由於來源和目標位於不同位置，因此使用絕對路徑。



**何時使用：**



- 您只需要一些特定 AI CLI 中的技能
- 您想在同步之前嘗試本地技能
- 您需要一個來源，但每個目標需要不同的技能子集



### 合併模式下的篩選策略



`include` 和 `exclude` 依照以下順序評估每個目標：



1. `include` 保持匹配的名字
2. `exclude` 從保留的集合中刪除



快速選擇：



- 當目標只應該獲得一小部分子集時使用`include`
- 當目標應該得到幾乎所有東西時使用`exclude`
- 當您需要具有明確剝離的廣泛子集時，請使用`include + exclude`



規則更改時的行為：



- 先前同步的來源連結條目被過濾掉，將在下一個`sync`刪除
- 保留目標中現有的本機非符號連結資料夾



完整範例請參考[目標配置](https://skillshare.runkids.cc/docs/reference/targets/configuration#include--exclude-target-filters)。



---



## 複製模式



每個技能都會作為真實檔案複製到目標目錄。 `.skillshare-manifest.json` 檔案追蹤管理哪些技能及其校驗和，因此保留本地技能。



```
Source                          Target (cursor)
─────────────────────────────────────────────────────────────
skills/                         ~/.cursor/skills/
├── my-skill/        ────copy►  ├── my-skill/    (real files)
├── another/         ────copy►  ├── another/     (real files)
└── ...                         ├── local-only/  (preserved)
                                └── .skillshare-manifest.json
```



### 為什麼選擇複製模式？號



即使您的 AI CLI 正確處理符號鏈接，複製模式也能提供價值：



- **防禦性設計** - 並非每個 AI CLI 都保證符號連結支持，尤其是在 Windows 上，其中符號連結行為因平台和權限等級而異
- **沙盒環境** - 嚴格的 CI 管道、容器和氣隙設定可能不會遵循跨檔案系統邊界的符號鏈接
- **用戶偏好** - 一些用戶和團隊只是為了透明度和可移植性而更喜歡真實文件而不是符號鏈接



**優點：**



- 隨處可用 — AI CLI 或作業系統無需符號連結支持
- 保留本地技能（與合併模式相同）
- 每個目標包括/排除過濾
- 基於校驗和的跳過：未更改的技能不會重新複製



**何時使用：**



- 您的 AI CLI 報告「未找到技能」或無法讀取符號連結的技能
- 您想要將技能提供給專案儲存庫 - 專案模式下的複製模式允許團隊將真正的技能檔案提交到 git，因此隊友不需要安裝 Skillshare
- 您需要獨立的技能目錄，無需中央來源即可運作（便攜式設定、CI 管道、氣隙環境）
- 您想要與合併模式相同的過濾行為，但使用真實文件
- `copy`的常見第一個候選：`cursor`、`antigravity`、`copilot`、`opencode`



### 更新如何運作



在每個`skillshare sync`上，每個來源技能的校驗和與清單中儲存的值進行比較：



- **相同的校驗和** → 跳過技能（快速）
- **不同的校驗和** → 技能被新版本覆蓋
- **`--force`** → 無論校驗和如何，所有託管技能都會被覆蓋



### 清單生命週期



合併和複製模式都會寫入 `.skillshare-manifest.json` 來追蹤託管技能：



- **合併模式**：記錄具有值`"symlink"`的技能名稱 - 用於在卸載後安全地刪除孤立的真實目錄（例如，複製模式殘留）
- **複製模式**：使用 SHA-256 校驗和記錄技能名稱 - 用於增量同步和孤立檢測
- 切換到符號連結模式時自動刪除
- 如果手動刪除，下一個`sync`會重建它



---



## 符號連結模式



整個目標目錄是到來源的單一符號連結。



```
Source                          Target (claude)
─────────────────────────────────────────────────────────────
skills/              ────────►  ~/.claude/skills → (symlink to source)
├── my-skill/
├── another/
└── ...
```



**優點：**



- 所有目標都是相同的
- 管理更簡單
- 沒有孤立的符號鏈接



**何時使用：**



- 您希望所有 AI CLI 都具有完全相同的技能
- 您不需要特定目標的技能



**警告：** 在符號連結模式下，透過目標刪除會刪除來源！



```
rm -rf ~/.claude/skills/my-skill  # ❌ Deletes from SOURCE
skillshare target remove claude   # ✅ Safe way to unlink
```



---



## 改變模式



### 每個目標



```
# Switch to copy mode (for AI CLIs that can't read symlinks)
skillshare target cursor --mode copy
skillshare sync
# Switch to symlink mode
skillshare target claude --mode symlink
skillshare sync
# Switch back to merge mode
skillshare target claude --mode merge
skillshare sync
```



### 按目標覆蓋（建議）



您不需要為每個目標使用一種全域模式。一個常見的模式是：



```
mode: merge
targets:
  claude:
    path: ~/.claude/skills    # inherits merge
  cursor:
    path: ~/.cursor/skills
    mode: copy
  codex:
    path: ~/.codex/skills
    mode: symlink
```



當一個目標需要相容性優先行為 (`copy`) 而其他目標保持即時反射 (`merge`/`symlink`) 時，請使用按目標覆寫。



### 預設模式



在配置中設定新目標：



```
# ~/.config/skillshare/config.yaml
mode: merge  # or symlink or copy
targets:
  claude:
    path: ~/.claude/skills    # inherits default mode
  cursor:
    path: ~/.cursor/skills
    mode: copy  # real files for Cursor
  codex:
    path: ~/.codex/skills
    mode: symlink  # override default
```



---



## 目標命名



控制使用合併或複製模式時如何在目標中命名技能目錄。



|命名|行為 |
| --- | --- |
|平（預設）|使用 __ 分隔符號扁平化嵌套技能：frontend/dev → frontend__dev |
|標準|使用 SKILL.md 名稱欄位：frontend/dev → dev |



全域或每個目標設定：



```
target_naming: standard    # global default
targets:
  claude:
    skills:
      target_naming: flat  # per-target override
```



或透過 CLI：



```
skillshare target claude --target-naming standard
skillshare sync
```



**標準模式**遵循【代理技能規格】(https://agentskills.io/specification)，要求SKILL.md `name`欄位與父目錄名稱相符。名稱無效或名稱衝突的技能會被警告並跳過。



**遷移**：從`flat`切換到`standard`會自動重新命名現有的託管條目。如果本地技能已經佔用了裸名稱，則保留舊的平面條目。



**符號連結模式**：忽略`target_naming` - 整個目錄原樣連結。



---



## 模式比較



|方面|合併|複製|符號連結 |
| --- | ---| --- | --- |
|保留當地技能 | ✅ 是的 | ✅ 是的 | ❌ 否 |
|符號連結相容 | ✅ 是的 | ❌ 真實檔案 | ✅ 是的 |
|所有目標相同 | ❌ 可以不同 | ❌ 可以不同 | ✅ 是的 |
|每個目標包含/排除 | ✅ 是的 | ✅ 是的 | ❌ 被忽略 |
|要清理孤兒| ✅ 是的 | ✅ 是的 | ❌ 否 |
|刪除安全性| ✅ 安全 | ✅ 安全 | ⚠️注意|
|磁碟使用情況|低（符號連結）|更高（份數）|低（符號連結）|



---



## 孤兒清理



在合併和複製模式下，`sync`都會自動修剪孤兒：



- 指向已刪除來源技能的**符號連結**始終被刪除
- **真實目錄**如果出現在 `.skillshare-manifest.json` 中則被刪除（以前由 Skillshare 管理）
- **未在清單中的未知目錄**保留並帶有警告（假定是使用者建立的）



這意味著在 `uninstall` + `sync` 之後，即使是非符號連結殘留（例如，先前的 `copy` 模式留下的目錄）也會被安全地清除。



```
$ skillshare sync
✓ claude: merged (5 linked, 2 local, 0 updated, 1 pruned)
✓ cursor: copied (3 new, 2 skipped, 0 updated, 1 pruned)
```

代理遵循相同的模式

所有三種模式（合併、複製、符號連結）也適用於代理同步。代理孤立清理、每個目標包含/排除過濾和模式轉換的行為與技能相同 - 唯一的區別是代理是單一 `.md` 檔案而不是目錄。支援代理的目標（Claude、Cursor、Augment、OpenCode）在其 `agents:` 子鍵上遵循相同的 `mode` 設定。詳情請參閱【代理】(https://skillshare.runkids.cc/docs/understand/agents)。



---



## 額外同步模式



額外內容（非技能資源，如規則、命令、提示）也使用合併和複製模式。每個額外目標都可以指定自己的模式：



```
extras:
  - name: rules
    targets:
      - path: ~/.claude/rules          # merge (default): per-file symlinks
      - path: ~/.cursor/rules
        mode: copy                     # copy: real file copies
```



其行為與技能同步模式相同 - 合併創建每個文件的符號鏈接，複製創建真實的文件副本。



---



## 另請參閱



- [sync](https://skillshare.runkids.cc/docs/reference/commands/sync) — 運行同步以應用模式更改
- [target](https://skillshare.runkids.cc/docs/reference/commands/target) — 更改目標的同步模式
- [來源與目標](https://skillshare.runkids.cc/docs/understand/source-and-targets) — 核心架構
- [配置](https://skillshare.runkids.cc/docs/reference/targets/configuration) — 每個目標設定
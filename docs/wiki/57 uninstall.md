# uninstall

> Source: https://skillshare.runkids.cc/docs/reference/commands/uninstall

---

# 解除安裝



從來源目錄中刪除一項或多項技能或追蹤儲存庫。技能會移至垃圾箱並保留 7 天，然後自動清理。



```
skillshare uninstall my-skill              # Remove a single skill
skillshare uninstall a b c --force         # Remove multiple skills at once
skillshare uninstall --all                 # Remove all skills
skillshare uninstall --group frontend      # Remove all skills in a group
skillshare uninstall team-repo             # Remove tracked repository (_ prefix optional)
```



## 何時使用



- 刪除您不再需要的技能（它們會移至垃圾箱 7 天）
- 清理您已停止使用的追蹤儲存庫
- 一次大量刪除整組技能
- 使用`--all`一次刪除**所有**技能



![uninstall demo](https://skillshare.runkids.cc/assets/images/uninstall-demo-2ff552150bb1042d135d975c8a6be019.png)



## 發生了什麼事



技能共享卸載

1. 解決目標2.飛行前檢查 3.確認並移至垃圾箱

## 選項



|旗幟|描述 |
| ---| ---|
| --全部|從源頭刪除所有技能（需要確認）|
| --group, -G <name> |刪除一組中的所有技能（前綴匹配，可重複）|
| --力，-f |跳過確認並忽略未提交的更改 |
| --dry-run，-n |預覽而不進行更改 |
| --項目，-p |在目前目錄中使用項目級配置 |
| --全域，-g |使用全域設定(~/.config/skillshare) |
| --json |輸出為 JSON（暗示 --force，跳過確認）|
| --幫助，-h |顯示幫助 |



## JSON 輸出



```
skillshare uninstall my-skill another-skill --json
```



```
{  "removed": ["my-skill", "another-skill"],  "failed": [],  "skipped": 0,  "dry_run": false,  "duration": "0.089s"}
```



結合`--dry-run`預覽：



```
skillshare uninstall --all --json --dry-run
```



## 多種技能



透過一個指令刪除多項技能：



```
skillshare uninstall alpha beta gamma --force
```



當找不到某些技能時，該命令**會跳過它們並發出警告**並繼續刪除其餘技能。只有當**所有**指定技能都無效時才會失敗。



### 全域模式



技能名稱支援全域模式（`*`、`?`、`[...]`）以進行批次刪除：



```
skillshare uninstall "core-*"              # Remove all skills matching core-*
skillshare uninstall "test-?" --force      # Single-character wildcard
skillshare uninstall "core-*" "util-*"     # Multiple patterns
```



全域匹配不區分大小寫：`"Core-*"` 匹配`core-auth`、`CORE-DB` 等。

僅頂級匹配

Glob 模式與來源資料夾中的**頂級目錄名稱**相符。嵌套技能（例如 `frontend/react-hooks`）與 `"react-*"` 不符 — 使用 `--group frontend` 來定位子目錄中的技能。



## 全部刪除



使用`--all`一次從來源目錄中刪除所有技能：



```
skillshare uninstall --all                 # Interactive confirmation
skillshare uninstall --all --force         # Skip confirmation
skillshare uninstall --all -n              # Preview what would be removed
```



`--all`不能與技能名稱或`--group`組合。

外殼全域保護

運行不帶引號的 `skillshare uninstall *` 會導致 shell 將 `*` 擴展為目前目錄中的檔案名稱。 Skillshare 偵測到這一點並建議改用 `--all`。始終引用通配符 (`"*"`) 或使用 `--all`。



## 群組移除



當您卸載包含子技能的目錄時，skillshare 會自動將其偵測為 **群組** 並在要求確認之前列出包含的技能：



```
Uninstalling group (5 skills)─────────────────────────────────────────  - feature-radar  - feature-radar-archive  - feature-radar-learn  - feature-radar-ref  - feature-radar-scan→ Name: feature-radar→ Path: ~/.config/skillshare/skills/feature-radarAre you sure you want to uninstall this group? [y/N]:
```



`--group` 標誌使用**前綴匹配**刪除目錄下的所有技能：



```
# Remove all skills under frontend/
skillshare uninstall --group frontend
# Also removes nested skills: frontend/react/hooks, frontend/vue/composables
skillshare uninstall --group frontend --force
# Preview what would be removed
skillshare uninstall --group frontend --dry-run
```



應用程式群組刪除（包含自動偵測的目錄群組）時，每個刪除的成員也會從配置中的託管`skills:`清單中刪除（專案模式下為`~/.config/skillshare/config.yaml`或`.skillshare/config.yaml`）。



您可以將位置名稱與`--group`組合，甚至多次使用`-G`：



```
# Mix names and groups
skillshare uninstall standalone-skill -G frontend -G backend --force
# Duplicates are automatically deduplicated
skillshare uninstall frontend/hooks -G frontend --force  # hooks removed once
```



## 追蹤儲存庫



對於追蹤的儲存庫（以 `_` 開頭的資料夾）：



- 檢查未提交的更改（使用`--force`覆蓋）
- 自動刪除`.gitignore`中的條目
- 卸載時`_`前綴是可選的



```
skillshare uninstall _team-skills        # With prefix
skillshare uninstall team-skills         # Without prefix (auto-detected)
skillshare uninstall _team-skills --force # Force remove with uncommitted changes
```



## 範例



```
# Remove a single skill
skillshare uninstall my-skill
# Remove multiple skills
skillshare uninstall skill-a skill-b skill-c --force
# Remove all skills
skillshare uninstall --all
skillshare uninstall --all --force
skillshare uninstall --all -n              # Preview
# Remove by group
skillshare uninstall --group frontend --force
# Preview removal
skillshare uninstall my-skill --dry-run
skillshare uninstall --group frontend -n
# Remove tracked repository
skillshare uninstall team-repo
# Mix names and groups
skillshare uninstall my-skill -G frontend --force
```



## 安全



卸載的技能**移至垃圾箱**，而不是永久刪除：



- **位置：** `~/.local/share/skillshare/trash/`（全球）或`.skillshare/trash/`（項目）
- **保留：** 7天，然後自動清理
- **重新安裝提示：** 如果技能是從遠端來源安裝的，則會顯示重新安裝命令
- **恢復：** 使用`skillshare trash restore <name>`從垃圾中恢復



單一技能（詳細）：



```
✓ Uninstalled skill: my-skillℹ Moved to trash (7 days): ~/.local/share/skillshare/trash/my-skill_2026-01-20_15-30-00ℹ Reinstall: skillshare install github.com/user/repo/my-skill
```



多項技能（大量）：



```
✓ Uninstalled 4 skill(s) (0.1s)── Removed ─────────────────────────────✓ pdf        skill✓ tdd        skill✓ security   group, 2 skills✗ bad-skill  failed to move to trash: ...── Next Steps ──────────────────────────ℹ Moved to trash (7 days).ℹ Run 'skillshare sync' to update all targets
```



大批量使用壓縮格式：



```
✓ Uninstalled 920, failed 2 (1.2s)── Failed ──────────────────────────────✗ bad-a  failed to move to trash: permission denied✗ bad-b  failed to move to trash: permission denied── Removed ─────────────────────────────✓ 920 uninstalled── Next Steps ──────────────────────────ℹ Moved to trash (7 days).ℹ Run 'skillshare sync' to update all targets
```



若要恢復意外卸載的技能：



```
skillshare trash list                  # See what's in trash
skillshare trash restore my-skill      # Restore to source
skillshare sync                        # Sync back to targets
```



## 卸載後



運行`skillshare sync`刪除所有目標的技能：



```
skillshare uninstall old-skill
skillshare sync  # Remove from Claude, Cursor, etc.
```



## 專案模式



從專案的`.skillshare/skills/`卸載技能或追蹤的儲存庫：



```
skillshare uninstall my-skill -p                  # Remove a skill
skillshare uninstall a b c -p -f                  # Remove multiple skills
skillshare uninstall --all -p -f                   # Remove all project skills
skillshare uninstall --group frontend -p -f        # Remove a group
skillshare uninstall team-skills -p                # Tracked repo (_ prefix optional)
```



在專案模式下，卸載：



- 將技能目錄移至`.skillshare/trash/`（保留7天）
- 從`.skillshare/config.yaml``skills:`清單中刪除技能條目（用於遠端技能）
- 刪除`.skillshare/.gitignore`中的條目（用於遠端/追蹤技能）
- 對於追蹤的儲存庫：檢查未提交的變更（使用`--force`覆蓋）
- `_` 前綴是可選的 — 自動偵測



```
skillshare uninstall pdf -p
skillshare sync
git add .skillshare/ && git commit -m "Remove pdf skill"
```



## 代理支援



使用`--kind agent`卸載代理程式而不是技能：



```
skillshare uninstall --kind agent tutor              # Remove an agent
skillshare uninstall --kind agent tutor reviewer -f   # Remove multiple agents
skillshare uninstall --kind agent --all               # Remove all agents
```



代理卸載遵循與技能相同的垃圾並保留行為（移至垃圾箱，保留 7 天）。背景請參閱【特工】(https://skillshare.runkids.cc/docs/understand/agents)。



## 另請參閱



- [安裝](https://skillshare.runkids.cc/docs/reference/commands/install) — 安裝技巧
- [list](https://skillshare.runkids.cc/docs/reference/commands/list) — 列出已安裝的技能
- [垃圾](https://skillshare.runkids.cc/docs/reference/commands/trash) — 管理廢棄的技能
- [專案技能](https://skillshare.runkids.cc/docs/understand/project-skills) — 專案模式概念
- [代理](https://skillshare.runkids.cc/docs/understand/agents) — 代理概念
# Backup & Restore

> Source: https://skillshare.runkids.cc/docs/how-to/daily-tasks/backup-restore

---

# 備份與還原



保護您的技能並從錯誤中恢復。



## 概述



Skillshare 會維護自動備份並提供手動備份/復原命令。



備份

恢復

目標

〜/.local/share/skillshare/backups/

目標



---



## 自動備份



備份會在以下情況之前自動建立：



- `skillshare sync`（技能目標與代理目標）
- `skillshare sync agents`（僅限代理目標）
- `skillshare target remove`



**位置：** `~/.local/share/skillshare/backups/<timestamp>/`（代理備份在常規 `<target>/` 目錄旁顯示為 `<target>-agents/`）。



---



## 手動備份



### 所有目標



```
skillshare backup
```



### 具體目標



```
skillshare backup claude
```



### 預覽



```
skillshare backup --dry-run
```



---



## 列出備份



```
skillshare backup --list
```



**輸出範例：**



```
Backups─────────────────────────────────────────  2026-01-20_15-30-00/    claude/    5 skills, 2.1 MB    cursor/    5 skills, 2.1 MB  2026-01-19_10-00-00/    claude/    4 skills, 1.8 MB
```



---



## 恢復



### 來自最新備份



```
skillshare restore claude
```



### 來自特定備份



```
skillshare restore claude --from 2026-01-19_10-00-00
```



### 預覽



```
skillshare restore claude --dry-run
```



---



## 恢復的作用是什麼



技能共享恢復克勞德

1. 找到最新的備份2.刪除目前目標3。將備份複製到目標

**注意：** 恢復後，目標包含真實檔案（不是符號連結）。運行 `skillshare sync` 重新建立符號連結。



---



## 清理舊備份



```
skillshare backup --cleanup
```



刪除早於配置的保留期的備份。



---



## 恢復場景



### 透過符號連結不小心刪除了技能



```
# If git is initialized (recommended)
cd ~/.config/skillshare/skills
git checkout -- deleted-skill/
# Or restore from backup
skillshare restore claude
skillshare sync
```



### 同步模式混亂



```
skillshare restore claude
skillshare target claude --mode merge
skillshare sync
```



### 想要撤銷最近的變更



```
skillshare backup --list
skillshare restore claude --from <earlier-timestamp>
```



### 恢復代理



代理有自己的備份條目（`<target>-agents`）並遵循與技能相同的流程：



```
# Manual agent backup
skillshare backup agents claude
# Restore from latest
skillshare restore agents claude
# Restore from a specific timestamp
skillshare restore agents claude --from 2026-01-19_10-00-00
```



在專案模式下，只能備份或還原代理程式 - `skillshare backup -p agents` 可以工作，但會出現簡單的 `skillshare backup -p` 錯誤。專案模式規則請參考[備份](https://skillshare.runkids.cc/docs/reference/commands/backup#agent-backup)。



---



## 最佳實踐



### 在危險操作之前



```
skillshare backup
```



### 重大變更後



```
skillshare push -m "Major update"  # Git backup
```



### 每週維護



```
skillshare backup --cleanup
```



---



## Git 作為備份



Git 提供了一個額外的備份層：



```
# Recover deleted skill
cd ~/.config/skillshare/skills
git checkout -- deleted-skill/
# See history
git log --oneline
# Restore to previous commit
git checkout <commit-hash> -- specific-skill/
```



---



## 另請參閱



- [備份](https://skillshare.runkids.cc/docs/reference/commands/backup) — 備份指令參考
- [恢復](https://skillshare.runkids.cc/docs/reference/commands/restore) — 恢復指令參考
- [垃圾](https://skillshare.runkids.cc/docs/reference/commands/trash) — 軟體刪除管理
- [故障排除](https://skillshare.runkids.cc/docs/troubleshooting) — 當出現問題時
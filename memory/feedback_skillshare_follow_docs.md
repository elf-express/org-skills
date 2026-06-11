---
name: skillshare 操作照官方文檔規範、別憑感覺試錯
description: 處理 skillshare(尤其 install/sync/UI)前先讀官方文檔吃透機制、按規範一次到位,別 UI 一個個點或憑感覺猜命令——那會連鎖產生孤兒 symlink、灌錯、雙層 source、清空 config 等問題
metadata:
  type: feedback
---
**回饋**:處理 skillshare 時要**先把官方文檔(skillshare.runkids.cc/docs)讀清楚、按規範一次到位**,不要 UI 一個個點、邊試邊探、或憑記憶猜命令。

**Why**:2026-06-11 一整段 session 因為憑感覺試錯而連鎖踩雷——UI 逐個安裝產生孤兒 symlink、誤用 `install --track` 訂閱聚合 repo(被 audit 擋)、雙層 `source`、嵌套 `.skillshare/.skillshare/`、global↔project 互相清空 config…用戶原話:「你按他規範建立就好,說明書不看清楚一點,我看了十次」。先讀文檔的成本遠低於試錯善後。

**How to apply**:
- 動手前先讀對應 doc(reference/targets/configuration、how-to/sharing、recipes/ci-cd 等),確認該情境的**正確命令與模式**再執行。
- 用 **CLI 命令**(可預期、可 `--dry-run`),少用 UI 逐個點。
- 破壞性/跨設定操作**前後**用只讀命令(`status`/`list`/`diff`/`grep` config)驗證,並先備份。
- 宣稱前先有 raw output 為證(對齊 [[feedback_git_push_verification]]);規範抉擇給定本、不丟選項給用戶自選(對齊 [[feedback_consistency_first]])。
- skillshare 具體機制與已知 bug(含跨 config 清空)見 [[project_skillshare_silent_skills_bug]]。

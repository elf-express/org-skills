---
name: skillshare <0.19.12 靜默刪 config skills 的 bug
description: skillshare project-mode 指令在 < v0.19.12 會靜默刪掉 .skillshare/config.yaml 的 skills 區塊,連帶清掉各 target 上千派發產物;升級 ≥0.19.12 修復,git restore 可救援
type: project
---
**事實**:skillshare < **v0.19.12** 有 bug:project-mode 指令(sync / update 等)在「internal migration」時會**靜默刪掉 `.skillshare/config.yaml` 的 `skills:` 區塊**,且不留可提交的記錄。因各 target 是 merge 模式逐個派發,清掉 skills 會連帶移除 `.adal/.claude/.codex/.agents` 上千個 symlink 產物。2026-06-10 在本機(當時 v0.19.7)觸發過一次:`git status` 一口氣冒出約 5300 筆刪除(同一根因 × 4 個 target × ~1323 筆),看起來像「資料全沒了」,實則沒有任何原始資料遺失。

**Why**:官方 changelog v0.19.12 已修:"Project `skills:` in config.yaml no longer stripped … Now `skills:` stays in `config.yaml` as the declarative source of truth." 根在工具版本,不在資料。

**How to apply**:
- **先別慌、別重新 clone**:那些是可重生的派發產物,config 在 commit 裡完好(本機 HEAD 通常 = `origin/main`,毋須拉遠端)。
- **救援一行**:`git restore .skillshare/config.yaml .adal .claude .codex .agents`,還原後只剩你真正的改動。
- **根治**:升級 skillshare 到 ≥ v0.19.12(2026-06 本機已升 **v0.20.10**);升級前別再跑 `skillshare sync` / `update` 以免復發。
- **遮蔽坑**:本機曾同時有 `/usr/local/bin/skillshare`(手動二進位,`which` 命中)與 `/opt/homebrew/bin/skillshare`(brew);PATH 中前者較前,`brew upgrade` 後 `which` 仍指舊的。升級時兩份都要顧,或 `sudo rm /usr/local/bin/skillshare` 統一交給 brew。
- **殘留物**:該次 migration 會在 `.skillshare/registry.yaml` 留下未追蹤的孤兒檔;新版正確模型 SSOT 仍是 `config.yaml` 的 `skills:`。相關背景見 [[org-skills 倉庫的目的與架構]]。

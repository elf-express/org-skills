---
name: 首次 push / 切換 remote 後用 git ls-remote 驗證
description: git push 顯示「new branch ... -> ...」不等於遠端真的收到,重要 push 後加一行 git ls-remote origin 驗證 refs 存在
type: feedback
originSessionId: e842c404-0ce9-4883-b7b9-0646bed64940
---
**重要的首次 push(新 repo)或切換 remote 後第一次 push 後,必須用 `git ls-remote origin` 驗證遠端 refs 真的存在。**

**Why**:2026-05-14 把 source 推到 `elf-express/skillshare-source` 時:
- `git push -u origin master` 輸出 `* [new branch] master -> master`(看似成功)
- 但 `gh api repos/elf-express/skillshare-source` 顯示 `size: 0`、`branches: []`
- `git ls-remote origin` 也回空
- 重 push 一次才真的到位

可能根因:GitHub 那邊在 push 後幾秒被 UI 動作(刪重建、reset)清空,但 git 客戶端看不到那個動作。

**How to apply**:
- **適用時機**:首次 push 到新 repo;`git remote set-url` 後第一次 push;切換組織 owner;**push 後感覺異常**。
- **不要每次 push 都做**(日常 push 老 repo 不需要)。只在「新狀況」或「異常感」時做。
- **驗證方式**:
  ```bash
  git ls-remote origin          # 直接問遠端有什麼 refs
  gh api repos/<owner>/<repo>/commits --jq '.[].sha[0:7]'  # GitHub 那邊看到的 commit
  ```
- **如果 ls-remote 回空**:不用驚慌,通常重 push 即可解決;確認本地 commits 仍在(`git log`),然後 `git push -u origin <branch>`。

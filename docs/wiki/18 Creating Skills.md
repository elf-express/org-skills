# Creating Skills

> Source: https://skillshare.runkids.cc/docs/how-to/daily-tasks/creating-skills

---

# 創造技能



從想法到發布的技能。

提示

想要控制哪些目標接受你的技能？參見【濾波技巧】(https://skillshare.runkids.cc/docs/how-to/daily-tasks/filtering-skills)。



## 概述



主意

創造

寫

測試

發布



---



## 第 1 步：創建技能



```
skillshare new my-skill
```



這將創建：



```
~/.config/skillshare/skills/my-skill/└── SKILL.md  (with template)
```



---



## 第 2 步：寫下技能



編輯生成的`SKILL.md`：



```
$EDITOR ~/.config/skillshare/skills/my-skill/SKILL.md
```



### 基本結構



```
---name: my-skilldescription: Brief description (shown in skill lists)---# My SkillWhat this skill does and when to use it.## Instructions1. Step one2. Step two3. Step three
```



### 好的寫作技巧



**具體一點：**



```
# GoodWhen the user asks to review code, analyze for:- Bugs and potential issues- Style consistency- Performance concerns# BadReview the code and make it better.
```



**包括範例：**



```
## ExampleUser: "Review this function"```pythondef add(a, b): 回傳 a + b
```

 

Response: Suggest adding type hints...

 

```
**指定何時不使用：**```markdown## When NOT to Use- Don't use for simple syntax questions- Don't use for explaining code (use explain-code skill instead)
```



---



## 步驟 3：部署與測試



### 部署到所有目標



```
skillshare sync
```



### 在您的 AI CLI 中進行測試



嘗試使用技能：



- 明確呼叫：`/skill:my-skill`
- 或描述任務，看看人工智慧是否接受它



### 迭代



編輯→同步→測試，直到運作良好。



---



## 步驟 4：發布（可選）



### 與您的團隊分享



推送到你的 git 遠端：



```
skillshare push -m "Add my-skill"
```



團隊成員可以拉：



```
skillshare pull
```



### 公開分享



1. 為您的技能建立 GitHub 儲存庫
2.推送你的技能目錄
3.其他可以安裝：

```
skillshare install github.com/you/my-skills/my-skill
```



---



## 技能模板



### 簡單技巧



```
---name: simple-skilldescription: Does one thing well---# Simple SkillWhen the user asks to do X, follow these steps:1. First, do Y2. Then, do Z3. Finally, confirm completion
```



### 任務導向技能



```
---name: code-reviewdescription: Reviews code for quality and issues---# Code ReviewYou are a code reviewer. Analyze code for quality issues.## What to Check- Bugs and edge cases- Performance issues- Security vulnerabilities- Code style and readability## Output FormatFor each issue found:1. **Location**: File and line2. **Severity**: High/Medium/Low3. **Issue**: What's wrong4. **Fix**: Suggested solution## Example[Include an example input and expected output]
```



### 針對特定目標的技能



```
---name: claude-promptsdescription: Prompt patterns specific to Claude Codetargets: [claude]---# Claude PromptsPatterns that work best with Claude Code's capabilities.## When to UseUse when crafting prompts for Claude Code specifically.
```



當設定`targets`時，該技能僅同步到匹配的目標－其他目標不會收到它。省略 `targets` 即可在各處同步。



### 流程技巧



```
---name: git-workflowdescription: Guides through git commit workflow---# Git WorkflowGuide the user through proper git commit practices.## Steps1. **Check status**: Run `git status`2. **Review changes**: Run `git diff`3. **Stage files**: Add specific files, not `git add .`4. **Write message**: Follow conventional commits5. **Commit**: Create the commit6. **Verify**: Run `git log -1`## Commit Message Format```texttype(範圍): 描述[可選正文]
```

 

Types: feat, fix, docs, style, refactor, test, chore

 

---

 

## Advanced Topics​

 

### Multiple files in a skill​

 

A skill can contain additional files:

 

```
my-skill/├── SKILL.md├── 例/│ └── 樣本.py└── 模板/ └── 組件.tsx
```

 

Reference them in your SKILL.md:

 

```
請參閱`examples/sample.py`中的範例以供參考。
```

 

### Namespacing for teams​

 

Avoid collisions with namespaced names:

 

```
名稱： acme 碼審查
```

 

### Version tracking​

 

Add version metadata:

 

```
---名稱：我的技能描述：我的技能版本：1.0.0作者：你的名字---
```

 

### License metadata​

 

Add a `license` field so users see license info before installing:

 

```
---名稱：我的技能描述：我的可重複使用的技能許可證：麻省理工學院---
```

 

When present, `skillshare install` displays the license in the selection prompt and confirmation screen. This helps corporate users with compliance decisions. See [Skill Format](https://skillshare.runkids.cc/docs/understand/skill-format#license) for details.

 

### Controlling discovery with .skillignore​

 

When publishing a multi-skill repository, you may have internal tools or work-in-progress skills you don't want users to discover. Create a `.skillignore` file at the repo root:

 .skillignore

```
# 內部工具validation-scriptsscaffold-template# 排除整個群組目錄internal-tools/# 正在進行中prompt-eval-*# 忽略任何深度的temp**/temp# 排除測試但保留test-ritictest-*!test-ritic
```

 

`.skillignore` uses [gitignore syntax](https://git-scm.com/docs/gitignore) — supports `*`, `**`, `?`, `[abc]`, `!negation`, `/anchored`, `pattern/` (dir-only), and `\#`/`\!` escapes. A group name like `internal-tools` excludes **all** skills under that directory. Use a precise path like `internal-tools/helper` to exclude only a specific skill within a group.

 

Skills matching these patterns won't appear in `skillshare install <repo>` discovery. This is applied server-side (in the repo), so all users benefit automatically. See [`runkids/my-skills`](https://github.com/runkids/my-skills) for a real-world example, and [install --exclude](https://skillshare.runkids.cc/docs/reference/commands/install#excluding-skills) for user-side exclusion.

 

### Source-root .skillignore (local)​

 

You can also place a `.skillignore` at your **source root** (`~/.config/skillshare/skills/.skillignore`) to globally hide skills from all commands — `doctor`, `status`, `list`, `sync`, `audit`, `diff`, and `check`:

 ~/.config/skillshare/skills/.skillignore

```
# 暫時靜音技能而不卸載 my-experimental-skill# 排除所有草案技能[Dd]raft*# 隱藏整個追蹤的 repo_archived-team-skills# 忽略任何深度的供應商 deps**/node_modules*.venv
```

 

Both layers apply: source-root patterns affect all skills (tracked and non-tracked), while repo-level patterns affect only that repo's skills. If either layer matches, the skill is excluded.

 

### .skillignore.local (personal override)​

 

If a shared repo's `.skillignore` blocks a skill you need locally, create a `.skillignore.local` in the same directory. Its patterns are appended after `.skillignore`, so `!pattern` negations override the base file:

 _team-skills/.skillignore.local

```
# 儲存庫忽略 private-*，但我需要我自己的！ private-mine
````



該文件不應該被提交－將其加到`.gitignore`。它適用於來源根和儲存庫層級。



---



## 清單



發布前：



- 清晰、具體的名稱
- 描述解釋目的
- 指示是可操作的
- 包括範例
- 在 AI CLI 中測試
- 與現有技能不衝突



---



## 另請參閱



- [新](https://skillshare.runkids.cc/docs/reference/commands/new) — 使用範本建立技能
- [技能格式](https://skillshare.runkids.cc/docs/understand/skill-format) — SKILL.md 結構
- [技能設計](https://skillshare.runkids.cc/docs/understand/philosophy/skill-design) — 複雜性等級、確定性、CLI 包裝模式
- [最佳實務](https://skillshare.runkids.cc/docs/how-to/daily-tasks/best-practices) — 命名與組織
- [整理技巧](https://skillshare.runkids.cc/docs/how-to/daily-tasks/organizing-skills) — 資料夾結構
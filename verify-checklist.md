# 《女巫J的时光机》技术事实核查清单

> 日期：2026-10-11｜方法：只读教程 + 实测（git 2.43.0）+ 官方文档（git-scm.com、GitHub Docs、Pro Git）
> 图例：❓核查问题 → ✅结论 → 📎出处｜⚠️= 待定（查不到实锤，不编）

---

## 第一夜：为什么需要时光机

1. ❓ "git"在英式英语里是"笨蛋、讨厌鬼"，出处可靠吗？
   → ✅可靠。Wikipedia Git 词条"命名"一节、git 源码 README 均有记载；man page 称 Git 为 "the stupid content tracker"。
   → 📎 en.wikipedia.org/wiki/Git_(software) "Naming" 节；github.com/git/git README

2. ❓ Linus 名言"我是个自大狂，我用自己的名字命名所有项目。先是 Linux，现在是 git。"真是他说的吗？
   → ✅是。2005 年 PC World 采访原话："I'm an egotistical bastard, and I name all my projects after myself. First 'Linux', now 'git'." 教程中文翻译准确。
   → 📎 Wikipedia 引 PC World《After controversy, Torvalds begins work on 'git'》(2005)；en.wikiquote.org Linus Torvalds

3. ❓ "版本控制 = 给文件拍时间快照"定义准确吗？
   → ✅准确，符合 Pro Git 对版本控制"记录文件内容变化、可回退"的定义。
   → 📎 Pro Git 第一章；git-scm.com

4. ❓ 开场"网站最终版2""真的最终"梗有事实问题吗？
   → ✅无，这是修辞手法，非事实主张。

---

## 第二夜：开机三件事

1. ❓ `git add.`（本夜代码块出现两次）命令正确吗？
   → ✅**错误，缺空格！** 正确是 `git add .`。`git add.` 会被当成名为 "add." 的路径，报错 `fatal: pathspec 'add.' did not match any files`。
   → 📎 git-add 文档（pathspec 参数）；常识验证

2. ❓ "`-m` 必填，不写时光机拒绝拍照"对吗？故事里"忘了喊名字就重来"对吗？
   → ✅**错误。** `-m` 可选；不带 `-m` 时 git 会打开编辑器让你写 message。实测：用脚本编辑器自动填写后提交成功。只有编辑器留空才会 abort。
   → 📎 实测（git 2.43.0）；git-commit 文档

3. ❓ "时光机每张照片都要署名。不署名的照片，它不存"对吗？
   → ✅基本正确。未配置 user.name / user.email 时，`git commit` 会报 "Author identity unknown" 并拒绝提交。
   → 📎 git-commit 文档

4. ❓ 懒人咒"`git commit -a -m` 取景快门一步到位"准确吗？
   → ✅部分准确，需补 nuance：`-a` 只对**已跟踪（tracked）**文件生效，新文件仍需先 `git add`。
   → 📎 git-commit 文档 `-a/--all` 说明

---

## 第三夜：翻相册

1. ❓ `git diff` 默认显示"还没取景的改动"（工作区 vs 暂存区）对吗？
   → ✅对。不加参数的 `git diff` 比较工作区与暂存区（index）。
   → 📎 git-diff 文档；Pro Git

2. ❓ `git log --oneline` 描述对吗？
   → ✅对，把每条提交压缩成一行显示。
   → 📎 git-log 文档

3. ❓ HEAD = "你现在站在哪张照片上"准确吗？
   → ✅准确。HEAD 是指向当前分支/提交的指针。
   → 📎 git-scm 术语；Pro Git "分支简介"

4. ❓ `git status` 输出"三件衣服换了还没拍照"这类描述准确吗？
   → ✅准确，status 确实报告已修改/已暂存/未跟踪三类状态。
   → 📎 git-status 文档

---

## 第四夜：后悔药，兑现

1. ❓ `git checkout -- <文件>` 还能用吗？现代 Git 推荐什么？
   → ✅能用（实测 git 2.43.0 成功还原，无 deprecation 报错）。但 git 2.23 起官方把"还原文件"的职责拆给了 **`git restore`**，建议教程备注现代写法 `git restore <文件>`。
   → 📎 实测；git 2.23 release notes；git-restore 文档

2. ❓ "`reset --hard` 倒回去的改动**找不回来**"对吗？
   → ✅**过于绝对。** 实测：`reset --hard HEAD~1` 后，用 `git reflog` 找到原提交再 `reset --hard HEAD@{1}`，完整恢复。真正找不回来的是**未提交**的工作区/暂存区改动。建议改成"没 commit 的东西真没了；commit 过的还能靠 reflog 抢救，但别依赖它"。
   → 📎 实测（git 2.43.0）；Pro Git "Data Recovery"（reflog）

3. ❓ "注意 `--` 后面有个空格，别念错咒"这个提醒对吗？
   → ✅对。`--` 用于分隔选项与路径；写成 `--index.html` 会被当成未知选项报错。
   → 📎 git-checkout 文档（`--` 分隔符约定）

4. ❓ reset 三档 `--soft`/`--mixed`/`--hard`"一档比一档狠"对吗？
   → ✅对，hard 会动工作区，最狠。
   → 📎 git-reset 文档

---

## 第五夜：平行宇宙

1. ❓ "branch 只有 41 字节"准确吗？
   → ✅准确。实测：`.git/refs/heads/<分支名>` 文件恰好 **41 字节**（40 位 SHA-1 + 1 个换行符）。
   → 📎 实测（git 2.43.0，`wc -c` = 41）

2. ❓ `git switch -c night-mode`（新建并跳入）对吗？
   → ✅对，git 2.23+ 引入的现代写法。
   → 📎 git-switch 文档

3. ❓ main 是默认主分支，对吗？
   → ✅对。Git ≥2.28 可配 `init.defaultBranch`，GitHub 新仓库默认 main。注：老项目可能是 master，可加一句备注。
   → 📎 git-init 文档；GitHub docs

4. ❓ `git branch` 不加名字"查户口"、`*` 标记当前分支，对吗？
   → ✅对。
   → 📎 git-branch 文档

---

## 第六夜：宇宙合并，与打架

1. ❓ 冲突标记归属（`<<<<<<< HEAD` 到 `=======` 是当前分支，`=======` 到 `>>>>>>>` 是对方）对吗？
   → ✅对。
   → 📎 git-merge 文档；Pro Git "基本的分支与合并"

2. ❓ "裁完之后 `git add` + `git commit` 再存一张快照"对吗？
   → ✅对。冲突解决后需 `add` 标记已解决，再 commit 完成合并。
   → 📎 Pro Git

3. ❓ 快进合并（fast-forward）"只是把书签往前挪"对吗？
   → ✅对。被合并分支无新提交时，Git 只移动指针，不产生合并提交。
   → 📎 git-merge 文档；Pro Git

4. ❓ 本夜代码块 `git add.` 对吗？
   → ✅**同第二夜，缺空格笔误**，应为 `git add .`。

---

## 第七夜：云端时间管理局

1. ❓ `git push -u origin main` 里 `-u` 的解释（"记住这个地址"）对吗？
   → ✅对。`-u` = `--set-upstream`，为当前分支设置上游，之后 `git push` / `git pull` 不用再写 remote 和分支名。
   → 📎 `git push -h`："-u, --set-upstream: set upstream for git pull/status"；git-push 文档

2. ❓ `git pull` = "把云端的新变化下载下来并合并到本地"对吗？
   → ✅对（默认行为 = fetch + merge）。
   → 📎 git-pull 文档

3. ❓ `git clone` "连历史照片一起搬走"对吗？
   → ✅对，clone 复制完整历史。
   → 📎 git-clone 文档；Pro Git

4. ❓ "女巫J第一次 push，手抖把密码输错三次"——2026 年 GitHub 还支持密码 push 吗？
   → ✅**已不支持。** GitHub 2021 年 8 月 13 日起移除 Git 操作的密码认证，必须用 personal access token。故事里的"密码"应改为"令牌"。
   → 📎 GitHub 官方公告（2020-12-15 预告 → 2021-08-13 生效）

5. ❓ Octocat 题外话"官方说法：因为它能同时干很多事"对吗？
   → ✅**错误归因。** 真实 origin：英国设计师 Simon Oxley 在 iStock 上卖的 stock 插画"Octopuss"，GitHub 创始人买下版权用作 404 页面，梗是 git 的 **"octopus merge"**（多分支合并）。"八只手同时干活"是后人附会，非官方说法。
   → 📎 Wikipedia "Simon Oxley"；GitHub 官方博客 / 创始人 Quora 回答

---

## 第八夜（终夜）：亲手造一台

1. ❓ init → add → commit → remote → push 一条龙顺序对吗？
   → ✅对，标准流程。
   → 📎 Pro Git；GitHub docs "创建仓库"

2. ❓ "去 GitHub 建一个仓库，叫 joyce-way，公开"——公开是必须的吗？
   → ✅本教程语境下合理：GitHub Pages 免费版要求仓库公开（私有仓库需 Pro 起），与第十一夜一致，前后闭环。
   → 📎 GitHub Pages 计费文档（见第十一夜第 3 点）

3. ❓ 流程有无遗漏关键前提？
   → ✅无。故事已注明"自我介绍那两行前面已念过"，config 前提交代完整。
   → 📎 教程自查

---

## 第九夜：不许拍照名单，与魔法钥匙

1. ❓ "名单只对'还没拍过照的'生效。已经 commit 进去的，得先删掉再加名单"对吗？
   → ✅对。实测：已 tracked 的文件加入 `.gitignore` 后，修改仍被 `git status` 检出，不受影响。
   → 📎 实测（git 2.43.0）；gitignore 文档（仅作用于 untracked 文件）

2. ❓ 教科书"必备三件套：`node_modules/`、`DS_Store`、`.env`"对吗？
   → ✅**笔误：缺前导点！** 应为 `.DS_Store`。故事正文里写的是对的（`.DS_Store`），教科书里写错了，前后不一致。
   → 📎 教程自查

3. ❓ `ssh-keygen -t ed25519 -C "邮箱"` 命令对吗？
   → ✅对，与 GitHub 官方文档推荐的 ed25519 密钥生成方式一致。
   → 📎 GitHub docs "生成新的 SSH 密钥并将其添加到 ssh-agent"

4. ❓ "HTTPS 每次都要验身份（token）；新手建议直接配 SSH，一劳永逸"对吗？
   → ✅**两处存疑：** ① HTTPS 配合系统凭据管理器（Git Credential Manager / macOS 钥匙串）可缓存 token，并非"每次"都输；② GitHub 官方文档历史上明确写 "Cloning with HTTPS URLs (recommended)"，直接建议新手用 SSH 与官方口径不一致。建议两种都介绍、说明 trade-off，让读者自己选。
   → 📎 GitHub docs "About remote repositories"；help.github.com "Which remote URL should I use?"

5. ❓ AWS 密钥被盗刷的题外话可信吗？
   → ⚠️**待定。** GitHub 确有 secret scanning / push protection 会拦截密钥（有官方文档），"密钥传公开仓库被盗刷"是真实常见的事故类型；但"几小时被盗刷几千美元"的具体个案无出处，建议表述为"圈内常见事故类型"而非具体事实。
   → 📎 GitHub docs "About secret scanning"

---

## 第十夜：请求合并

1. ❓ Fork vs clone 的区别（"fork 是云端复制，clone 是下载到本地"）对吗？
   → ✅对。
   → 📎 GitHub docs "Fork a repo"

2. ❓ PR 五步流程（Fork → Clone → 开分支改 → Push → 发 PR）对吗？
   → ✅对，标准流程。
   → 📎 GitHub docs "Creating a pull request"

3. ❓ "改完再 push，PR 自动更新，不用重发"对吗？
   → ✅对。同一分支的新 push 会自动追加到已 open 的 PR。
   → 📎 GitHub docs

4. ❓ 贡献小绿格（contribution graph）题外话对吗？
   → ✅对。GitHub profile 页有 contribution graph，按提交活跃度着色。
   → 📎 GitHub docs "查看个人资料上的贡献"

---

## 第十一夜（终夜）：展示橱窗

1. ❓ Pages 开启三步（Settings → Pages → Deploy from a branch → 选 main）对吗？
   → ✅对，2026 年仍是标准路径。
   → 📎 GitHub docs "Configuring a publishing source for your GitHub Pages site"

2. ❓ "每次 push……全世界下一秒看到新版"对吗？
   → ✅**"下一秒"错误。** 社区实测：Pages 部署通常 1–10 分钟，常见 1–2 分钟，且有 CDN 缓存。教程步骤里写的"等一分钟"是对的，但"下一秒"是夸张，应改为"一两分钟"。
   → 📎 GitHub 社区 Pages troubleshooting 文档（多份 2026 年指南：typical 1–10 min）

3. ❓ "免费额度：公开仓库免费"对吗？
   → ✅对。GitHub Free 的 Pages 仅支持**公开**仓库；私有仓库需 Pro（个人）/ Team（组织）起。教程第八夜要求建公开仓库，前后一致。
   → 📎 GitHub docs "About GitHub Pages" 计费说明

4. ❓ 用户站 `用户名.github.io` vs 项目站 `用户名.github.io/仓库名` 对吗？
   → ✅对。
   → 📎 GitHub docs "About GitHub Pages"

5. ❓ 故事 URL `https://女巫J.github.io/joyce-way/` 里中文用户名能用吗？
   → ✅故事修辞，无事实错误。真实 GitHub 用户名不支持中文字符，此处是拟人化写法；教科书已用"用户名"占位。保留无碍。
   → 📎 教程自查

---

## 汇总：疑似错误（8 个）+ 待定（1 个）

| # | 位置 | 问题 | 性质 |
|---|------|------|------|
| 1 | 第二夜代码块 ×2、第六夜代码块 ×1 | `git add.` 缺空格，应为 `git add .` | 命令笔误，会报错 |
| 2 | 第二夜故事 + 教科书 | "`-m` 必填，不写时光机拒绝拍照" | 事实错误（-m 可选，不带则开编辑器）|
| 3 | 第四夜教科书 | "`reset --hard` 倒回去的改动找不回来" | 过于绝对（reflog 可救已 commit 的） |
| 4 | 第九夜教科书 | `DS_Store` 缺前导点，应为 `.DS_Store` | 笔误（故事正文是对的） |
| 5 | 第七夜题外话 | Octocat "官方说法：八只手同时干活" | 错误归因（真实是买的 stock 图 + octopus merge 梗） |
| 6 | 第十一夜故事 | "全世界下一秒看到新版" | 夸张（实际 1–10 分钟，多为 1–2 分钟） |
| 7 | 第七夜故事 | push "把密码输错三次" | 过时（GitHub 2021-08 起不支持密码，须用 token） |
| 8 | 第九夜教科书 | "HTTPS 每次验身份；新手直接配 SSH" | 与 GitHub 官方推荐口径不一致 + "每次"夸张 |

**待定 1 个**：第九夜题外话 AWS 盗刷的具体个案（"几小时几千美元"）无出处；机制（secret scanning）成立，建议改述为事故类型。

**已实测验证（git 2.43.0）**：commit 不带 -m 可提交｜branch ref 恰好 41 字节｜reset --hard 后 reflog 可完整恢复｜.gitignore 对已 tracked 文件无效｜`git checkout --` 仍可用｜`git push -u` = --set-upstream。

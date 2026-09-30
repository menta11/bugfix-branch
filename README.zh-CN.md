# bugfix-branch

一个通用 Agent Skill：**安全地把 bugfix 分支开出来**——先看清仓库状态，跟你确认分支名，全程不动你正在开发的代码与分支。

[English](./README.md) · **简体中文**

## 它是什么

给一段 bug 描述，它建出 `fix/<模块>-<用户名>` 分支：干净且就在默认分支上走经典 `git checkout -b`，工作区脏或不在默认分支则在独立的 `git worktree` 里开。

只负责把分支开出来——修 bug、提交、push、开 PR、合并都不在范围内。

## 为什么需要它

`git checkout -b` 看着就一行，却会悄悄出事：未提交的改动被带进修复分支、`git stash` 不加 `-u` 会漏掉未跟踪文件、从当前 checkout 的分支开会让修复叠在无关特性分支之上，模型自行命名也往往不符合 `fix/<模块>-<用户名>` 规范。

所以技能把两件最难回头的事提前问清：**仓库现在是什么状态**、**分支将要叫什么名字**。

## 何时触发

仅在**显式要求**开修复分支时触发：

- "开个 bugfix 分支"
- "帮我拉个修复分支"
- "open a bugfix branch"
- "用 bugfix-branch"

**不会**因为对话里出现"bug"一词就触发——正常的调试会话不该悄悄给你的仓库开分支。

## 安装

`bugfix-branch` 是标准的 Agent Skill——一个 `SKILL.md` 放在与技能同名的目录里，不依赖任何特定 agent；只要是从目录加载技能的 agent 都能用。

用 `skills` CLI 安装，它会放进检测到的各个 agent 目录：

```bash
npx skills add https://github.com/menta11/bugfix-branch.git --skill bugfix-branch
```

也可以手动拷贝。下例用 Claude Code 的目录（`~/.claude/skills/`），换成你所用 agent 的目录即可：

```bash
git clone https://github.com/menta11/bugfix-branch.git
mkdir -p ~/.claude/skills/bugfix-branch
cp bugfix-branch/SKILL.md ~/.claude/skills/bugfix-branch/SKILL.md
```

Windows（PowerShell）：

```powershell
git clone https://github.com/menta11/bugfix-branch.git
New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills\bugfix-branch"
Copy-Item bugfix-branch\SKILL.md "$env:USERPROFILE\.claude\skills\bugfix-branch\SKILL.md"
```

## 使用示例

```
你:   开个 bugfix 分支，导出报告的按钮点了没反应

技能: Pre-flight（只读）——
         工作区  : 脏（2 个已修改，1 个未跟踪）
         当前分支: feature/report-v2
         默认分支: main

       模块名  : export-report
       用户名  : （回车用默认 lmt）
       分支全名: fix/export-report-lmt —— 确认？

你:   确认

技能: 工作区脏且不在默认分支 → 推荐 worktree。
       正在基于 origin/main 创建 ../fix-export-report-lmt …
       完成。cd ../fix-export-report-lmt 开始修。
       当前分支与未提交改动一步未动。
```

## 仓库结构

```
bugfix-branch/
  SKILL.md          # 技能本体 —— agent 唯一加载的文件
  README.md         # 英文说明
  README.zh-CN.md   # 本文件
```

完整流程、铁律与边界情况见 [SKILL.md](./SKILL.md)。

## 许可

在 [MIT 许可证](LICENSE) 下发布 — © 2026 刘孟涛

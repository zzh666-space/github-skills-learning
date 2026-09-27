# 学习笔记 01：Introduction to GitHub

> 课程来源：[GitHub Skills · Introduction to GitHub](https://github.com/skills/introduction-to-github)
> 完成日期：2026-09-27
> 练习仓库：`github-skills-learning`（本仓库）
> 分支：`feature/course-01-introduction-to-github` → 合并至 `main`

## 一、核心知识点

### 1. 什么是 GitHub 与 Git

- **Git**：分布式版本控制系统，负责本地文件的版本记录。
- **GitHub**：基于 Git 的协作平台，提供仓库托管、Issue、Pull Request、Actions 等协作能力。
- 二者关系：Git 是工具，GitHub 是平台。

### 2. Repository（仓库）

- 一个包含文件与文件夹的项目，并跟踪每个文件/文件夹的版本历史。
- 本课程练习仓库包含：`README.md`、`PROFILE.md`、`docs/` 等。

### 3. Branch（分支）

- 分支是仓库的一个**平行版本**，默认分支为 `main`。
- 在分支上修改不会影响 `main`，多人协作时互不干扰。
- 命名规范（本课程实践）：`feature/course-01-introduction-to-github`。
- 关键命令：

```bash
git branch                  # 查看本地分支
git checkout -b <分支名>     # 创建并切换分支
git branch --show-current   # 显示当前分支
```

### 4. Commit（提交）

- 提交是对项目文件/文件夹的一次变更记录，归属于某个分支。
- 每次提交包含：提交信息（message）、作者、时间戳、变更内容。
- 本课程遵循 **Conventional Commits** 规范：

```
<type>(<scope>): <description>
# 例如
docs(readme): add GitHub skills learning plan
feat(profile): add PROFILE.md welcome file
```

常用 type：`feat`（新功能）、`fix`（修复）、`docs`（文档）、`chore`（杂项）、`refactor`（重构）。

### 5. Pull Request（拉取请求）

- PR 是协作的核心：把某个分支的改动**提议合并**到另一个分支（如 `main`）。
- PR 提供：变更对比（diff）、讨论区、代码审查、自动检查（CI）。
- 流程：创建分支 → 提交 → push → 开 PR → 审查 → 合并。

### 6. Merge（合并）

- 合并把 PR 中的改动并入目标分支。
- 合并后分支通常删除，保持仓库整洁。
- 合并本身也是一种"提交"（merge commit）。

## 二、用到的命令（完整清单）

| 阶段 | 命令 | 说明 |
|------|------|------|
| 环境 | `winget install --id GitHub.cli` | 安装 GitHub CLI |
| 认证 | `gh auth login --web` | 设备码浏览器授权 |
| 认证 | `gh auth status` | 查看登录状态与 token 权限 |
| 认证 | `gh auth setup-git` | 配置 git 凭证助手 |
| 配置 | `git config --global user.name/email` | 配置提交者身份 |
| 仓库 | `gh repo create <name> --public --add-readme` | 创建远程仓库 |
| 分支 | `git checkout -b feature/course-01-introduction-to-github` | 创建并切换分支 |
| 提交 | `git add <file>` / `git commit -m "..."` | 暂存与提交 |
| 推送 | `git push -u origin <branch>` | 推送分支到远程 |
| PR | `gh pr create --title "..." --body "..."` | 创建 Pull Request |
| PR | `gh pr merge --delete-branch` | 合并 PR 并删除分支 |
| 同步 | `git pull` | 拉取最新 main |

## 三、踩坑记录

### 坑 1：gh CLI 未安装

- **现象**：`gh` 不是内部或外部命令。
- **解决**：`winget install --id GitHub.cli --scope user`，安装后需刷新 PATH 或重开 shell。

### 坑 2：GitHub 网络不通（需要代理）

- **现象**：`gh auth login` 轮询超时，报 `connection attempt failed`（连接 github.com:443 失败）；git clone 时部分仓库失败。
- **排查**：检查系统代理端口（本机 Clash for Windows 监听 `127.0.0.1:7890`）；`Invoke-WebRequest -Proxy` 验证连通性。
- **解决**：为 git/gh 命令设置代理环境变量：

```powershell
$env:HTTPS_PROXY="http://127.0.0.1:7890"
$env:HTTP_PROXY="http://127.0.0.1:7890"
```

> 注：代理端口因用户环境而异（Clash 默认 7890）。本机验证 `https://api.github.com/zen` 返回 `Non-blocking is better than blocking` 即通。

### 坑 3：设备码认证需要浏览器配合

- **现象**：`gh auth login --web` 生成一次性设备码，浏览器需打开 `https://github.com/login/device` 输入并授权。
- **注意**：设备码有效期约 15 分钟，网络超时会导致认证失败，需重新生成设备码。
- **经验**：认证流程涉及账号登录，必须由账号本人完成；登录与授权完成后 gh 自动完成配置。

### 坑 4：中文路径与 PowerShell 编码

- **现象**：git 输出中文路径正常，但 PowerShell 调用 git 时 stderr 被包装为 NativeCommandError（正常现象，非错误）。
- **解决**：git 的进度/警告信息走 stderr，PowerShell 会显示为红色错误样式，实际命令成功；以 `$LASTEXITCODE` 判断真实结果。

### 坑 5：Move-Item 移动 .git 目录权限失败

- **现象**：把仓库从聊天目录移动到 `E:\代码` 时，`.git` 目录报"没有足够的访问权限"。
- **解决**：目标目录实际已完整（.git + 文件均在），手动删除源目录残留的空 `.git` 即可。

## 四、实操截图描述

> 截图存放于 [docs/screenshots/](./screenshots/)，命名格式：`course-01-<步骤>.png`

| 截图 | 内容描述 |
|------|----------|
| `course-01-branch-created.png` | GitHub 仓库页面，显示 `feature/course-01-introduction-to-github` 分支已创建并推送 |
| `course-01-pull-request.png` | Pull Request 页面：标题、描述、base=`main`、compare=feature 分支 |
| `course-01-pr-merged.png` | PR 合并成功后的页面，显示 Merged 状态与删除分支按钮 |

## 五、课程收获

1. 完整走通了 **分支 → 提交 → PR → 合并 → 删分支** 的标准协作流程。
2. 掌握了 GitHub CLI（`gh`）替代网页操作的高效路径。
3. 实践了 Conventional Commits 规范，提交信息可读、可追溯。
4. 学会了代理环境下 git/gh 的网络配置方法。

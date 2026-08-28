# Git 与 GitHub 项目管理知识点

本文结合 `ruoyi-archive-management` 项目，整理 Git Bash 命令、前后端分离项目管理、`.gitignore` 和常见问题。

## 1. 基本概念

- Git：运行在本地电脑上的版本控制工具。
- GitHub：远程代码托管平台。
- 本地仓库：电脑上的项目目录，内部有 `.git`。
- 远程仓库：GitHub 上的仓库地址。
- commit：保存一个本地版本。
- push：上传本地提交。
- pull：下载远程更新并合并。

## 2. 推荐项目结构

```text
ruoyi-archive-management/
├─ .git/                 # Git 内部数据，只保留根目录这一份
├─ .gitignore            # 根目录忽略规则
├─ .gitattributes        # 统一文件属性（可选）
├─ README.md             # 项目总说明
├─ LICENSE               # 开源许可
├─ backend/              # 若依后端
├─ frontend/             # Vue 前端
├─ docs/                 # 项目文档
└─ sql/                  # 数据库脚本
```

一个根目录的 `.git` 可以管理所有子目录。检查是否有嵌套仓库：

```bash
find . -type d -name ".git"
```

正常只显示 `./.git`。

## 3. Git Bash 路径

Windows 路径：

```text
E:\WorkSpace\ruoyi-archive-management
```

Git Bash 路径：

```bash
/e/WorkSpace/ruoyi-archive-management
```

常用目录命令：

```bash
cd /e/WorkSpace/ruoyi-archive-management  # 进入目录
pwd                                       # 查看当前目录
ls                                        # 查看文件
```

## 4. 创建和下载仓库

### 克隆远程仓库

```bash
git clone https://github.com/用户名/仓库名.git
```

作用：下载远程仓库，并自动创建本地 `.git`。

指定本地目录名：

```bash
git clone https://github.com/用户名/仓库名.git ruoyi-archive-management
```

### 初始化本地仓库

```bash
git init
```

作用：在当前目录创建新的 Git 仓库。已经使用 `git clone` 时不需要再次执行。

## 5. 远程仓库

```bash
git remote -v
```

作用：查看远程地址。

推荐使用：

```text
origin    你的 GitHub 仓库
upstream  若依官方仓库
```

命令：

```bash
git remote rename origin upstream
```

把原远程仓库改名为 `upstream`。

```bash
git remote add origin https://github.com/LGH0519/ruoyi-archive-management.git
```

添加自己的远程仓库。

```bash
git remote set-url origin 新地址
```

修改远程地址。

## 6. 日常提交和推送

查看状态：

```bash
git status
```

作用：查看当前分支、修改文件、未跟踪文件和是否领先远程仓库。

查看修改：

```bash
git diff          # 查看未暂存的修改
git diff --cached  # 查看已暂存但未提交的修改
```

加入暂存区：

```bash
git add .                 # 添加全部修改
git add backend           # 只添加后端
git add frontend          # 只添加前端
git add README.md         # 添加指定文件
```

创建本地版本：

```bash
git commit -m "添加档案管理系统后端"
```

作用：保存暂存区内容。commit 只保存到本地，不会上传 GitHub。

第一次推送：

```bash
git branch -M main
git push -u origin main
```

`-u` 会建立本地 `main` 与远程 `origin/main` 的关联，以后可以直接：

```bash
git push
```

推荐的日常流程：

```bash
git status
git add backend frontend
git commit -m "更新档案管理系统"
git push
```

## 7. 分支管理

```bash
git branch                         # 查看本地分支
git branch -a                      # 查看所有分支
git switch -c feature/archive      # 创建并切换分支
git switch main                    # 切换分支
git branch -d feature/archive      # 删除已合并分支
```

旧版本 Git 可使用：

```bash
git checkout -b feature/archive
git checkout main
```

建议 `main` 保持可运行，具体功能在 `feature/*` 分支开发。

## 8. 同步若依官方代码

获取官方最新提交但不修改当前代码：

```bash
git fetch upstream
```

查看远程分支：

```bash
git branch -r
```

更新前先备份：

```bash
git switch -c backup-before-update
git push -u origin backup-before-update
git switch main
```

合并官方分支（以实际分支名为准）：

```bash
git merge upstream/master
# 或
git merge upstream/main
```

`git pull` 等于先获取再合并：

```bash
git pull
```

二次开发项目不要直接覆盖官方源码。合并后要测试数据库、登录、权限、菜单、档案功能和前后端接口。

## 9. 冲突处理

发生冲突后：

```bash
git status
```

冲突文件会有：

```text
<<<<<<< HEAD
你的代码
=======
官方代码
>>>>>>> upstream/master
```

手动保留正确内容并删除标记，然后：

```bash
git add 冲突文件名
git commit -m "解决官方更新合并冲突"
```

## 10. .gitignore

`.gitignore` 是普通文本文件，用来忽略不应提交的文件。根目录建议内容：

```gitignore
# Java / Maven
target/
*.class
*.jar
*.war

# IntelliJ IDEA
.idea/
*.iml
out/

# Eclipse
.classpath
.project
.settings/

# Node / Vue
node_modules/
dist/
.vite/
.npm/

# 日志
*.log
logs/

# 本地环境配置
.env
.env.local
.env.*.local

# 操作系统
.DS_Store
Thumbs.db

# 临时文件
*.tmp
*.temp
```

规则示例：

```gitignore
target/                 # 忽略所有 target 目录
*.log                   # 忽略所有日志文件
/frontend/node_modules/ # 忽略前端依赖
!important.log          # 取消忽略指定文件
```

查看忽略原因：

```bash
git check-ignore -v 文件名
```

注意：`.gitignore` 对已经被 Git 跟踪的文件无效。停止跟踪但保留本地文件：

```bash
git rm --cached 文件名
git commit -m "停止跟踪本地配置"
git push
```

不要提交数据库密码、JWT 密钥、API 密钥和真实生产配置。

## 11. 相关文件

| 文件 | 作用 | 建议 |
|---|---|---|
| `.git/` | Git 历史和配置 | 根目录保留，不手动修改 |
| `.gitignore` | 忽略规则 | 提交到仓库 |
| `.gitattributes` | 统一换行符 | 建议提交 |
| `README.md` | 项目说明 | 提交 |
| `LICENSE` | 开源许可 | 保留并提交 |

可选的 `.gitattributes`：

```gitattributes
* text=auto
*.java text eol=lf
*.xml text eol=lf
*.yml text eol=lf
*.js text eol=lf
*.vue text eol=lf
*.bat text eol=crlf
```

## 12. 查看历史

```bash
git log                              # 完整历史
git log --oneline --graph --all      # 简洁图形历史
git show 提交编号                    # 查看某次提交
```

## 13. 撤销和临时保存

取消暂存但保留本地修改：

```bash
git restore --staged 文件名
```

放弃未提交的文件修改：

```bash
git restore 文件名
```

临时保存修改：

```bash
git stash
git stash pop
```

修改最近一次提交说明（尚未推送时）：

```bash
git commit --amend -m "新的说明"
```

以下命令可能造成数据丢失，使用前先备份：

```bash
git reset --hard
git branch -D 分支名
```

## 14. Git 用户信息

```bash
git config --global user.name
git config --global user.email
```

设置作者信息：

```bash
git config --global user.name "LGH0519"
git config --global user.email "你的邮箱"
```

## 15. IDEA 和 VS Code

- IDEA：打开 `backend`，负责 Java、Maven、后端调试和 Git 提交。
- VS Code：打开 `frontend` 或项目根目录，负责 Vue、npm、前端调试和 Git 提交。
- 两个软件操作的是同一个根目录 `.git`。
- 不要让两个软件同时提交同一批修改。

## 16. 常见问题

### 推送出现 Empty reply from server

先检查：

```bash
git remote -v
git status
```

确认地址正确后重试：

```bash
git push -u origin main
```

仍然失败时，检查网络、代理、VPN 或换网络。

### 前端依赖是否提交

不要提交：

```text
frontend/node_modules/
frontend/dist/
```

应提交：

```text
package.json
package-lock.json 或其他锁定文件
```

### 最常用五条命令

```bash
git status
git add .
git commit -m "说明"
git pull
git push
```

记忆顺序：

```text
修改代码 → 查看状态 → 暂存 → 提交 → 推送
```


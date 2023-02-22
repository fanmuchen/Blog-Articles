# Git 命令速查表

绝大部分人经常使用的 Git 命令只有极少的一部分，不常使用的命令容易遗忘，故制作此速查表。

### 仓库管理

- `git init`: 初始化仓库。
- `git clone <url>`: 克隆仓库。
- `git remote add <name> <url>`: 添加远程仓库。
- `git remote -v`: 查看远程仓库的详细信息。
- `git pull`: 拉取远程仓库的更新。
- `git push`: 推送本地提交到远程仓库。
- `git fetch`: 从远程仓库获取最新更新，但不合并到本地仓库。

### 分支管理

- `git branch`: 查看分支信息。
- `git checkout <branch>`: 切换到指定分支。
- `git merge <branch>`: 将指定分支合并到当前分支。
- `git tag`: 标记某个提交点。

### 提交管理

- `git add <file>`: 将文件添加到暂存区。
- `git commit -m "<message>"`: 提交暂存区中的文件，并添加提交信息。
- `git reset`: 恢复文件到上一个提交状态。
- `git rm <file>`: 删除文件并将其添加到暂存区。
- `git mv <old-file> <new-file>`: 重命名或移动文件。
- `git stash`: 将当前工作区的修改保存到一个栈中。

### 状态查询

- `git status`: 查看当前仓库的状态。
  `git status --short`: F*MC 喜欢的格式*
- `git log`: 查看提交历史记录。
  `git log --all --graph --oneline`: _FMC 喜欢的格式_
- `git diff`: 查看当前工作区与暂存区的差异。

### 初始配置

```bash
git config --global user.name "名字"
git config --global user.email "邮箱地址"
git config --global init.defaultBranch main #使用 main 作为默认分支
```

**如果和我一样喜欢 vscode**，先安装 code 命令工具。打开 vscode，然后按 F1 或 Ctrl+Shift+P，在命令面板中搜索 "Shell Command: Install 'code' command in PATH"。选择该命令并回车，即可安装 code 命令。然后：

```bash
git config --global core.editor "code --wait"
git config --global diff.tool vscode
git config --global difftool.vscode.cmd 'code --wait --diff $LOCAL $REMOTE'
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'
```

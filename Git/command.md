### Git 常用命令笔记

#### 一、仓库设置与初始化

1.  **`git config`：配置 Git**
    *   首次使用 Git 时，需要设置你的用户名和邮箱，这些信息会记录在你的每一次提交中。
    *   `git config --global user.name "Your Name"`
    *   `git config --global user.email "youremail@example.com"`

2.  **`git init`：初始化仓库**
    *   在你项目的根目录中执行此命令，可以创建一个新的本地 Git 仓库（会生成一个 `.git` 隐藏目录）。
    *   `git init`：**创建一个本地仓库**

3.  **`git clone`：克隆远程仓库**
    *   从远程服务器（如 GitHub）上拷贝一个完整的 Git 仓库到本地。
    *   `git clone [url]`

#### 二、核心工作流程（添加与提交）

Git 的核心工作区有三个：工作区（你实际修改文件的目录）、暂存区（Staging Area）、和本地仓库（Repository）。

1.  **`git status`：查看仓库状态**
    *   这是最常用的命令之一，用于显示工作区和暂存区中文件的状态（是否有修改、是否已暂存等）。

2.  **`git add`：将文件添加到暂存区**
    *   将工作区的修改内容添加到暂存区，为下一次提交做准备。
    *   `git add <file>`：添加指定文件。
    *   `git add .`：添加当前目录下所有变动的文件。
    *   `git add` **将文件添加到暂存区中**

3.  **`git commit`：提交更改到本地仓库**
    *   将暂存区中的内容创建一个新的“版本快照”，并永久保存在本地仓库中。
    *   `git commit -m "提交信息"`：**向本地仓库提交一个新的版本**。提交信息是对本次修改的简要描述，非常重要。

#### 三、分支管理

分支是 Git 的核心功能，它允许你偏离主线进行开发，而不会影响主线。

1.  **`git branch`：列出、创建或删除分支**
    *   `git branch`：列出所有本地分支。
    *   `git branch -v`：**列出本地分支及每个分支的最后一次提交信息。**
    *   `git branch <branch-name>`：创建一个新分支。

2.  **`git checkout`：切换分支**
    *   `git checkout <branch-name>`：切换到已存在的分支。
    *   `git checkout -b <new-branch-name>`：**创建并立即切换到一个新分支。** 这个命令是 `git branch <new-branch-name>` 和 `git checkout <new-branch-name>` 两条命令的简写。

3.  **`git merge`：合并分支**
    *   将一个分支的修改内容合并到当前所在的分支。
    *   `git merge <branch-name>`

#### 四、远程协作

1.  **`git remote`：管理远程仓库连接**
    *   `git remote -v`：**显示所有远程仓库的连接信息（URL）。**
    *   `git remote add <shortname> <url>`：添加一个新的远程仓库连接，通常我们把默认的远程仓库命名为 `origin`。

2.  **`git push`：推送本地提交到远程仓库**
    *   `git push -u <远程仓库名> <分支名>`：**向远程仓库进行提交。**
    *   `-u` 参数（`--set-upstream` 的简写）会建立本地分支与远程分支的追踪关系。设置后，未来推送该分支时只需使用 `git push` 即可。

3.  **`git pull`：拉取远程更新并合并**
    *   **拉取远程仓库最新的版本并将远端同名分支合并到本地分支。**
    *   `git pull` **默认是 `git pull origin <当前分支名>`**（前提是设置了上游分支）。
    *   理解 `git pull` 的本质很重要：**`git pull = git fetch + git merge`**。它首先执行 `git fetch` 从远程获取最新版本，然后执行 `git merge` 将远程分支合并到当前本地分支。

4.  **`git fetch`：从远程仓库获取最新版本**
    *   它只会将远程仓库的最新数据下载到本地，但**不会**自动合并到你的工作分支中。这让你可以在合并前先查看远程的修改。

#### 五、版本回滚与撤销

1.  **`git reset`：版本回滚（重置提交）**
    *   这个命令会移动 `HEAD` 指针，并且根据不同参数，可能会修改暂存区和工作区。**谨慎使用，尤其是在共享分支上。**
    *   `git reset --soft <commit-id>`：**将版本库回滚到指定提交。** 你的工作区和暂存区的代码都不会改变，只是 `HEAD` 指针移动了。你可以重新提交。
    *   `git reset --mixed <commit-id>`：（默认模式）**对版本库与暂存区进行回滚。** 工作区代码不变，但暂存区会被清空，你需要重新 `git add`。
    *   `git reset --hard <commit-id>`：**对版本库、暂存区和工作区都进行回滚。** 这是一个危险的操作，因为它会**彻底丢弃**你工作区中未提交的修改。

2.  **`git revert`：撤销提交**
    *   **创建一个新的提交来撤销某次旧的提交所做的更改。**
    *   与 `git reset` 不同，`revert` 不会改变项目历史，它会产生一个新的、反向的提交。这对于撤销已经推送到远程仓库的提交来说，是更安全的方式。
    *   `git revert <commit-id>`


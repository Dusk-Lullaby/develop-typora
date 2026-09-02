# Git

`Git Gui Here` ： 打开Git 图形界面

`Git Bash Here` ： 打开Git 命令行



## 常用命令

### 1. 设置用户信息

* `git config --global user.name "Sonnet"`
* `git config --global user.emial "Sonnet@Sonnet14.cn"` 

### 2. 查明配置信息

* `git config --list`

### 3. 获取git仓库

* 本地初始化： `git init`
* 克隆远程仓库： `git clong 远程仓库地址`

### 4. 本地仓库常用命令

* 查看文件状态：`git status`
* 将文件的修改加入暂存区：`git add`
* 添加所有文件到暂存区：`git add .`
* 将暂存区的文件取消暂存或者是切换到指定版本：`git reset --hard 版本号`
* 将暂存区的文件修改提交到版本库：`git commit`
* 提交到本地仓库：`git commit -m "初始提交"`
* 覆盖上一次提交记录`git commit --amend -m "src"`
  * `--amend` 参数会覆盖上一次的提交记录

* 查看日志：`git log`

### 5. 远程仓库操作

* 查看远程仓库： `git remote`
* 查看远程仓库详细信息：`git remote -v`
* 添加远程仓库：`git remote add 远程仓库别名 <url>`
* 从远程仓库克隆：`git clone 远程仓库地址`
* 从远程仓库拉取：`git pull 远程仓库别名 分支名称`
* 推送到远程仓库：`git push`

### 6. 分支操作

* 列出所有本地分支：`git branch`
* 将本地分支重命名为main：`git branch -M main`
* 列出所有远程分支：`git branch -r`
* 列出所有本地分支和远程分支：`git branch -a`
* 创建分支：`git branch 分支名称`
* 切换分支：`git checkout 分支名称`
* 推送至远程仓库分支：`git push 远程仓库别名 分支名称`
* 合并分支：`git merge 分支名称`（把指定分支的代码合并到当前分支）
* 删除分支：`git branch -d 分支名称`

### 7. 标签操作

* 列出已有标签：`git tag`
* 创建标签：`git tag 标签名称`
* 将标签推送至远程仓库：`git push 远程仓库别名 标签名字`
* 检出标签：`git checkout -b 分支名称 标签名称`



### 8. 操作流程

```tex
git init
git add .
git commit -m "Initial commit"
git remote add origin (仓库地址)
git branch -M main
git push -u origin main
```


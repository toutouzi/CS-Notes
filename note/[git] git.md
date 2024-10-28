### Git

---

**参考资料：**

* [Pro Git](https://git-scm.com/book/en/v2)
* [MIT missing lecture](https://missing.csail.mit.edu/2020/version-control/)
* [Learing Git Branching](https://learngitbranching.js.org/?locale=en_US)



#### VCS

---

> Version control is a system that records changes to a file or set of files over time so that you can recall specific versions later

> **Local vcs --> Centalized VCS -->Distributed VCS**

##### Distributed Version Control Systems

has full history ,not just a snapshot

- 本地操作，速度很快（🆚 CVCS）

<img src="../img/Git/distributed.png" style="zoom:50%;" />



####  Git data model

---

> On disk, all Git stores are objects and references: that’s all there is to Git’s data model. 

##### Snapshots

a series of snapshots : the history of a collection of files and folders within some top-level directory  

- ==blob== is file (just a bunch of bytes) 
  ==tree== is directory(map name to blob or tree) 

<img src="../img/Git/snapshots.png" style="zoom:50%;" />

- if files have not changed, Git doesn’t store the file again, just a <u>link to the previous identical file</u> it has already stored.

  <img src="../img/Git/snapshots1.png" style="zoom:80%;" />

##### Modeling history: relating snapshots

a history is a directed acyclic graph (DAG) of snapshots.(多个父节点)

- ==commit :== is the snapshot

   `o` : individual commit(snapshot)

<img src="../img/Git/commit-history.png" style="zoom:80%;" />

##### Data model , as pseudocode

```shell
type blob = array<byte>
type tree = map<string, tree | blob>

type commit = struct{
		parants: array<commits>
		author: string
		message: string
		snapshot: tree
}
```

##### Objects and content-addressing

> Everything in Git is checksummed before it is stored and is then referred to by that checksum.

- based on the contents of a file or directory structure in Git

- `type object  = blob | tree | commit` 
- all object 都被映射了地址
- all the snapshots can be identified by their address

```shell
objects = map<string, object>

def store(object):
    id = sha1(object)
    objects[id] = object

def load(id):
    return objects[id]
```

##### References

Git can use human-readable names like “master” to refer to a particular snapshot in the history, instead of a long hexadecimal string.

- 由于地址不好记，将地址map为好记的`references = map<string, string>` 
-  References are pointers to commits. Unlike objects, which are immutable, references are mutable
  -  `master` :表示主分支的最近commit
  - `head`: 当前位置

##### Repositories

just data `objects` and `references` 

##### Staging area

---

> Git accommodates such scenarios by allowing you to specify which modifications should be included in the next snapshot through a mechanism called the “staging area”.
>
> ==The basic Git workflow :==
>
> 1. modify files in your working tree
> 2. selectively stage just those changes you want to be part of your next commit, which adds *only* those changes to the staging area.
> 3. do a commit, which takes the files as they are in the staging area and stores that snapshot permanently to your Git directory.

- file has 3 states : ***modified*, *staged*, and committed**
  - **Modified** means that you have changed the file but have not committed it to your database yet.
  - **Staged** means that you have marked a modified file in its current version to go into your next commit snapshot.
  - **Committed** means that the data is safely stored in your local database

- checkout在working directory之间切换；reset从staging area回复到working directory
  - **Working tree**: a single checkout of one version of the project.  从database中将压缩后的数据拿出，放到磁盘上
  - **Staging area：** is a file, generally contained in your Git directory
    - stores information about what will go into your next commit. 
  - **Git directory：** is where Git stores the metadata and object database for your project.
    - `clone`的时候就是复制的这个，包含项目的全部内容

![](../img/Git/areas.png)



#### Git Basic

---

##### git config

- 配置文件有3个位置，==级别依次增加==

  - `[path]/etc/gitconfig` file:  **系统级别；** 使用 option `--system` to `git config`
  - `~/.gitconfig` or `~/.config/git/config` file:   **用户级别**；使用`--global` option
  -  `.git/config` the repository you’re currently using: **项目级别**；不使用option｜使用`--local` option

- 查看配置

  ```console
  $ git config --list --show-origin
  //查看某一配置key value
  $ git config user.name
  ```

- 查看某一配置位置

  ```console
  $ git config --show-origin rerere.autoUpdate
  file:/home/johndoe/.gitconfig	false
  ```

- 配置身份

  ```console
  $ git config --global user.name "John Doe"
  $ git config --global user.email johndoe@example.com
  ```

- 配置默认分支名称

  ```console
  $ git config --global init.defaultBranch main
  ```

##### repository

- 获得一个git repository

  1. Initializing a Repository in an Existing Directory

     cd到项目下，`git init` ;会产生 `.git`子目录。 

  2. Cloning an Existing Repository

- each file in your working directory can be in one of two states: *tracked* or *untracked*. 

##### Ignoring Files

- .gitignore

#### Git command-line interface

---

##### Basic：

- `git help <command>` :   查看某个命令的帮助
- `git init`: 将当前目录初始化为git目录， 数据存储在.git下
- `git add <filename>`:   track ； stage ； mark merge-conflicted files as resolved.
- `git status`:  [option 选项  -s](https://git-scm.com/book/en/v2/Git-Basics-Recording-Changes-to-the-Repository)
- `git commit`:  create new commit
- `git log`:  show a falttend log history
  - `git log --all --gragh --decorate`:  常用，更方便看
- `git diff <filename>`:  show changes you made relative to the staging area
- `git diff <revision> <filename>`:  shows differences in a file between snapshots
- `git checkout <revision>`: updates HEAD and current branch

##### Branching and merging

- `git branch`: shows branches
- `git branch <name>`: creates a branch
- `git checkout -b <name>`: creates a branch and switches to it
  - same as `git branch <name>; git checkout <name>`
- `git merge <revision>`: merges into current branch
- `git mergetool`: use a fancy tool to help resolve merge conflicts
- `git rebase`: rebase set of patches onto a new base

##### Remotes

- `git remote`:  list remotes
- `git remote add <name> <url>`: add a remote
- `git push <remote> <local branch>:<remote branch>`
- `git branch --set-upstream-to=<remote>/<remote branch> `:set up correspondence between local and remote branch   
- `git fetch`: retrieve objects/references from a remote
- `git pull`:  same as `git fetch; git merge`
- `git clone`：$ git clone https://github.com/libgit2/libgit2 mylibgit（克隆的时候改名）

##### Undo 

- `git commit --amend`: edit a commit’s contents/message
- `git reset HEAD <file>`: unstage a file
- `git checkout -- <file>`: discard changes

##### Advanced Git

- `.gitignore`: [specify](https://git-scm.com/docs/gitignore) intentionally untracked files to ignore
  - `.gitignore_global`，[参考设定](https://github.com/huangrt01/dotfiles/blob/master/gitignore_global)
- `git bisect`: binary search history (e.g. for regressions)
- `git clone --depth=1`: shallow clone, without entire version history
- `git add -p`: interactive staging
- `git rebase -i`: interactive rebasing
- `git blame`: show who last edited which line

- `git cat-file -p`: 显示对象信息
  * 40位Hash值，前2位是文件夹，后38位是文件名
  * 存在`.git/objects/`中 
  * [理解git常用命令原理](http://www.cppblog.com/kevinlynx/archive/2014/09/09/208257.html)
- `git config`: Git is [highly customizable](https://git-scm.com/docs/git-config)
  - `/etc/gitconfig`对应`--system` 
  - `~/.gitconfig`或`~/.config/git/config`对应 `--global`
  - `path/.git/config`对应`--local`
  - `git config --list (--show-origin)`显示所有config
  - 设置Identity，见下面Github部分
- `git stash`: temporarily remove modifications to working directory
  - `git stash pop [--index][stash@{id}]`
    - `git stash pop` 恢复最新的进度到工作区
    - `git stash pop --index` 恢复最新的进度到工作区和暂存区
    - `git stash pop stash@{1}` 恢复指定的进度到工作区。stash_id是通过git stash list命令得到的。通过git stash pop命令恢复进度后，会删除当前进度

  - `git stash show -p | git apply -R`
- `git cherry-pick`
  - [利用它只pull request一个特定的commit](https://www.iteye.com/blog/bucketli-2442195)
  - `git cherry-pick commit1..commit2`
- `git submodule add <url> /path`
  * clone之后初始化：`git submodule update --init --recursive`
    * 仅初始化一个特定的submodule：`git submodule update <specific path to submodule>`
  * 更新：`git submodule update --remote & ga . & gc -m "commit message"  `
  * 移除submodule：https://stackoverflow.com/questions/1260748/how-do-i-remove-a-submodule/36593218#36593218
  * 如果报错already exists in the index ，用`git rm -r --cached /path`解决此问题 
  * 这个特性很适合和[dotfiles](https://github.com/huangrt01/dotfiles)搭配，但如果用在项目里可能[出现问题](https://codingkilledthecat.wordpress.com/2012/04/28/why-your-company-shouldnt-use-git-submodules/)，尤其是需要commit模块代码的时候
  * [使用时可能遇到的坑的集合](https://blog.csdn.net/a13271785989/article/details/42777793)
  * commit的时候有坑，需要先commit子模块，再commit主体，参考：https://stackoverflow.com/questions/8488887/git-error-changes-not-staged-for-commit



#### 与GitHub交互

---

- **SSH原理**

  - **ssh-keygen是针对每台主机的**

  - 当本地主机需要登录远程主机时，本地主机向远程主机发送一个登录请求，远程收到消息后，随机生成一个字符串并用公钥加密，发回给本地。本地拿到该字符串，用存放在本地的私钥进行解密，再次发送到远程，远程比对该解密后的字符串与源字符串是否等同，如果等同则认证成功

  - [用SSH连接Github](https://help.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh)

    - 生成SSH key ` ssh-keygen -t <type> -f <filename> -c <commment>` 

    - 获取ssh key 公钥(< filename >.pub)
  
    - ` cat < filename >.pub` 显示内容
  
  <img src="../img/Git/ssh-key-pub.png" style="zoom:50%;" />
  
  - 去Github添加公钥
  
  - 验证是否添加成功`ssh -T git@github.com` 

<img src="./../img/Git/验证github的ssh添加成功.png" style="zoom:50%;" />

  - 使用自定义的ssh key名称 (非id_rsa.pub 和id_rsa)

    - 设置**~/.ssh/config**文件

​					  <img src="../img/Git/自定ssh-key-name.png" style="zoom:60%;" />

  - 提示私钥too open

    - 尝试修改私钥权限 `chmod 600 /Users/dhw/.ssh/id_rsa97`

      <img src="../img/Git/ssh-key无法验证.png" style="zoom:50%;" />

- 设置本机推送的账户

  ``` shell
  #全局
  git config --global user.name "YourGlobalUserName"
  git config --global user.email "your_global_email@example.com"
  #针对特定仓库
  git config user.name "YourUserName"
  git config user.email "your_email@example.com"
  #检查配置信息
  git config user.name  # 检查用户名
  git config user.email # 检查邮箱
  ```
  
- 建立仓库

  ``` 
  git init
  git remote add origin git@github.com:huangrt01/dotfiles.git
  git pull --rebase origin master
  git push --set-upstream origin master
  ```

  

#### Some Bugs

---

##### 连接相关

- `Failed to connect to github 443` 问题解决方案
  1. `git remote set-url origin git@github.com:huangrt01/XXX.git` , 先把连接方式由https改成ssh
  2. 再在`~/.ssh/config`中把ssh的端口22改成https端口443

```shell
Host github.com
	User xxxxxxx@163.com
	Hostname ssh.github.com
	PreferredAuthentications publickey
	IdentityFile ~/.ssh/id_rsa
	Port 443
```

- git push时提示`Username for 'https://github.com' ` 

  - 建议更改为ssh连接；
  - 使用 `git remote -v` 查看origin使用的是https还是ssh
  - 如果GitHub已经配置了ssh，使用`git remote set-url origin git@github.com:toutouzi/xxx.git` (会修改该项目.git文件下的url)



#### Other Resources

---

##### Miscellaneous

- **GUIs**: there are many [GUI clients](https://git-scm.com/downloads/guis) out there for Git. We personally don’t use them and use the command-line interface instead.
- **Shell integration**: it’s super handy to have a Git status as part of your shell prompt ([zsh](https://github.com/olivierverdier/zsh-git-prompt), [bash](https://github.com/magicmonty/bash-git-prompt)). Often included in frameworks like [Oh My Zsh](https://github.com/ohmyzsh/ohmyzsh).
- **Editor integration**: similarly to the above, handy integrations with many features. [fugitive.vim](https://github.com/tpope/vim-fugitive) is the standard one for Vim.
- **Workflows**: we taught you the data model, plus some basic commands; we didn’t tell you what practices to follow when working on big projects (and there are [many](https://nvie.com/posts/a-successful-git-branching-model/) [different](https://www.endoflineblog.com/gitflow-considered-harmful) [approaches](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)).

##### 教程

- [Pro Git](https://git-scm.com/book/en/v2) is **highly recommended reading**. Going through Chapters 1–5 should teach you most of what you need to use Git proficiently, now that you understand the data model. The later chapters have some interesting, advanced material.
- [Oh Shit, Git!?!](https://ohshitgit.com/) is a short guide on how to recover from some common Git mistakes.
- [Git for Computer Scientists](https://eagain.net/articles/git-for-computer-scientists/) is a short explanation of Git’s data model, with less pseudocode and more fancy diagrams than these lecture notes.
- [Git from the Bottom Up](https://jwiegley.github.io/git-from-the-bottom-up/) is a detailed explanation of Git’s implementation details beyond just the data model, for the curious.
- [How to explain git in simple words](https://smusamashah.github.io/blog/2017/10/14/explain-git-in-simple-words)
- [Learn Git Branching](https://learngitbranching.js.org/) is a browser-based game that teaches you Git.

##### [安装Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)

###### git config

- 配置文件有3个位置，==级别依次增加==

  - `[path]/etc/gitconfig` file:  **系统级别；** 使用 option `--system` to `git config`
  - `~/.gitconfig` or `~/.config/git/config` file:   **用户级别**；使用`--global` option
  -  `.git/config` the repository you’re currently using: **项目级别**；不使用option｜使用`--local` option

- 查看配置

  ```console
  $ git config --list --show-origin
  //查看某一配置key value
  $ git config user.name
  ```

- 查看某一配置位置

  ```console
  $ git config --show-origin rerere.autoUpdate
  file:/home/johndoe/.gitconfig	false
  ```

- 配置身份

  ```console
  $ git config --global user.name "John Doe"
  $ git config --global user.email johndoe@example.com
  ```

- 配置默认分支名称

  ```console
  $ git config --global init.defaultBranch main
  ```
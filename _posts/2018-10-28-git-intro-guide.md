---
title: Git 入门操作笔记总结
date: 2018-10-28 10:14:39
categories:
- 运维
tags:
- git
---

# Git 的基本概念的介绍
与 CVS、Subversion 一类的**集中式**版本控制工具不同，它采用了**分布式**版本库的作法，不需要服务器端软件，就可以用作版本控制，使得源代码的发布和交流极其方便。git 的**速度很快**，这对于诸如 Linux 内核这样的大项目来说自然很重要。git 最为出色的是它的**合并追踪 （merge tracing） 能力**。（选自维基百科）

------

# 为什么使用 git
> * 节省时间（可以高效创建分支，运行速度快）
> * 离线工作（无需联网联机，可随时随地工作）
> * 撤销错误操作（可通过恢复日志撤销任意的操作）
> * 可靠性高（Git 是增量保存，可以进行任意回滚）
> * 避免混乱（相比其他版本控制软件，更加快速简单）
> * 顺应潮流（Git 有大量的教程，工具和服务和活跃的技术社区）

# git 相比 svn 有哪些区别？

## 设计理念不同

Git 与 SVN 两种版本控制最大不同在于前者是**分布式**，后者是**集中式**。SVN 只有本地工作仓库与线上仓库，文件变更后通过 `add` 命令提交到本地仓库，然后通过 `commit` 提交到线上仓库。如果需要创建分支，则只能在线上通过复制的方式创建一个新的仓库，成本很高。而 Git 在本地有**工作区**、**暂存区**和**仓库区**三个概念，工作区是项目的实际目录，暂存区临时保存你的文件变更，仓库区保存代码的提交记录。Git 可以通过命令快速创建分支，成本非常低。

简单来说，SVN 的代码仓库在线上，Git在本地。所以 Git 可以离线操作，只要通过推送远程分支的方式同步到线上的一个仓库就可以与别人进行协同合作。


## 工作机制不同

- Git 的分支是非常轻量级的，一个分支仅是对一个commit的引用，彼此切换极为高效。而SVN的分支是线上拷贝的一个目录，操作效率相比很低。

- 鉴于分布式的机制，**Git 无法做到像 SVN 一样的文件级别的权限控制**，如果必须对开发者的访问权限进行控制，可以集成第三方的软件，比如通过安装 `gitolite、gitosis` (推荐 gitolite )来配置文件来实现文件级别权限控制。

- SVN 的版本号基于数字递增，Git 的版本号基于提交的 `commit id`（可以通过 `git log` 查看）

# Git 常用命令速查表
![img](http://hiccboy.xyz/git-command-img.png)

## Add:将文件变更从工作区提交到暂存区

| 命令                     | 解释                             |
| ------------------------ | -------------------------------- |
| `git add .` `git add -A` | 添加当前目录的所有文件到暂存区   |
| `git add -u`             | 添加已经变更的文件               |
| `git add [dir]`          | 添加指定目录到暂存区，包括子目录 |
| `git add [file1]`        | 添加指定文件到暂存区             |

## Commit:将文件变更从暂存区提交到本地仓库

| 命令                              | 解释                                                         |
| --------------------------------- | ------------------------------------------------------------ |
| `git commit -m [message]`         | 提交暂存区到本地仓库,message代表说明信息                     |
| `git commit -a -m [message]`      | 将所有已跟踪文件中的执行修改或删除操作的文件都提交到本地仓库 |
| `git commit [file1] -m [message]` | 提交暂存区的指定文件到本地仓库                               |
| `git commit --amend -m [message]` | 修改注释，使用一次新的commit，替代上一次提交的注释           |

## Branch:管理 Git 分支的命令

| 命令                                         | 解释                                               |
| -------------------------------------------- | -------------------------------------------------- |
| `git branch`                                 | 列出所有本地分支                                   |
| `git branch -r`                              | 列出所有远程分支                                   |
| `git branch -a`                              | 列出所有本地分支和远程分支                         |
| `git branch [branch-name]`                   | 新建一个分支，但依然停留在当前分支                 |
| `git checkout [branch-name]`                 | 切换到指定分支，并更新工作区(前两个命令的合写方式) |
| `git checkout -b [branch-name]`              | 新建一个分支，并切换到该分支                       |
| `git branch --track [branch][remote-branch]` | 新建一个分支，与指定的远程分支建立追踪关系         |
| `git branch -d [branch-name]`                | 删除本地分支                                       |
| `git push origin --delete [branch-name]`     | 删除远程分支                                       |

> 在执行 `git push` 命令时，会自动获取追踪关系并推送。

## Git 打标签

Git 打标签的命令组合与分支的命令组合类似，`git tag` 没有参数时显示标签，加上 -d 时删除标签，新增标签时直接在命令后面跟上标签名称。

| 命令                   | 解释     |
| ---------------------- | -------- |
| `git tag <tagname>`    | 新建标签 |
| `git tag -d <tagname>` | 删除标签 |
| `git tag`              | 列出标签 |

## Merge 和 Rebase 合并分支的命令
合并的概念，如下，R3等分支要合并到主干上，最终保存到M节点上。
![img](http://hiccboy.xyz/git-merge.png)

- Merge 命令

| 命令                 | 解释                                         |
| -------------------- | -------------------------------------------- |
| `git fetch [remote]` | merge之前先拉一下远程仓库最新代码            |
| `git merge [branch]` | 合并指定分支到当前分支， 推荐加上--no-ff参数 |

- Rebase 命令

![img](http://hiccboy.xyz/git-rebase.png)

Rebase 命令又称**变基命令**，主要用于生成一个干净的，没 有 `merge commit` 的线性历史树，操作步骤如下：

1. 先在某分支（一般是主干master）执行
```js
git rebase [branch-name]
```
1. 没有冲突则自动合并，将两个分支合并，并消除原分支，不会产生新的 commit；有冲突先在冲突节点上依次处理冲突，完成后的步骤同上。处理冲突的方式如下，对冲突节点依次进行处理:
```js
// 将处理的冲突变更写入暂存区
git add -u
// 执行以下命令，继续处理下一个节点的冲突
git rebase --continue
// 如果中间遇到某个节点不需要解决冲突，可以用下面命令忽略：
git rebase --skip
// 如果想回到rebase执行之前的状态，可以执行
git rebase --abort
```

> 注：rebase 之后，不需要执行 commit，也不存在新的修改需要提交，都是 git 自动完成。

## Reset 和 Revert 回滚分支的命令

> git reset 的作用是修改 HEAD 的位置，即将 HEAD 指向的位置改变为之前存在的某个版本，并且**以后的版本都不要了**

![img](http://hiccboy.xyz/git-reset.png)

| 命令                        | 解释                                                   |
| --------------------------- | ------------------------------------------------------ |
| `git reset —soft [commit]`  | 只改变提交点，暂存区和工作目录的内容都不改变           |
| `git reset —mixed [commit]` | 改变提交点，同时改变暂存区的内容                       |
| `git reset —hard [commit]`  | 暂存区、工作区的内容都会被修改到与提交点完全一致的状态 |
| `git reset --hard HEAD`     | 让工作区回到上次提交时的状态                           |

> 回滚远程代码

本地回滚后提交到远端进行远程回滚，需要注意由于本地回滚后版本号落后一位，所以需要**强制推送**

> git revert 会创建一个新的版本，这个版本的内容与我们要回退到的目标版本一样，将HEAD指针是指向这个新生成的版本。简单说就是**通过新建版本来达到回滚版本的目的**。这样的好处是不会丢失回滚后的版本内容。

![img](http://hiccboy.xyz/git-revert.jpg)

## Push 推送分支到远端仓库的

| 命令                                                           | 解释                                                                                          |
| -------------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| `git push -u [远程主机（一般是origin）] [本地分支]:[远程分支]` | 推送分支，-u 表示第一次与 origin 主机建立追踪关系，如果本地分支与远程分支相同，则可以省略后者 |
| `git push [remote] --force`                                    | 强行推送当前分支到远程仓库，即使有冲突                                                        |
| `git push [remote] --all`                                      | 推送所有分支到远程仓库                                                                        |

## Git 的其他常用命令

| 命令            | 解释                                     |
| --------------- | ---------------------------------------- |
| `git status`    | 显示有变更的文件                         |
| `git log`       | 显示当前分支的版本历史                   |
| `git diff`      | 显示暂存区和工作区的差异                 |
| `git diff HEAD` | 显示工作区与当前分支最新commit之间的差异 |


## 可视化工具

我主要是用 `vscode` 的 `git graph` 插件来进行可视化操作的，其他IDE也有对应插件，可自行寻找。另外提供了在win系统上比较好用的GUI客户端，如下所示：

## Git 官方 GUI 工具

在安装 Git 的时候自动安装了 GUI，在本地工作区鼠标右键点击"打开 Git GUI "即可。

官方工具比较简洁，可以完成 **commit、merge、push、pull** 操作，可以完成大部分操作。至于 Git 的一些高级功能，可以配合命令行来实现。

## TortoiseGit
如果习惯了 svn 的操作，可以使用这个软件，比较容易上手。需要注意 Git 必须提前安装，因为这个软件只是个外壳，需要调用终端的命令行才行。

## SourceTree
这是一个开源免费的 Git 可视化工具，可以从[官方下载地址](https://blog.sourcetreeapp.com/2018/04/24/sourcetree-for-windows-enterprise-now-available/)下载。由于网络的问题，buket 账户无法登录，可以采用修改配置文件的方式来使用，具体参考[这篇博客](https://my.oschina.net/u/868789/blog/2208147)

# 5. 在现有代码基础上，一个新功能的研发的完整流程有哪些？

> 并结合完整的操作流程，简述 git 的代码随着操作流程的变化导致的存储的变化及存储原理

## 0. 数据存储随着操作流程发生变化的示意图
![img](http://hiccboy.xyz/git-process-graph.png)

## 1. git clone 克隆项目

当我们需要开发一个新的功能时，首先，通过**克隆**远程仓库获取到当前最新的源码(注：`git clone` 会创建远程 `origin/master` 的追踪关系)
```js
git clone git@git.intra.yunpaper.com:xxx/xxx.git
```
## 2. .gitingore忽略文件
然后通过编辑忽略文件 `.gitignore` 忽略不需要的文件（一般包括缓存、图片等多媒体文件、密码等重要配置文件等），该文件一般保存在根目录下，语法如下所示：
```js
# 忽略所有 .a 结尾的文件
*.a
# 但 lib.a 除外
!lib.a
# 只忽略文件夹下的所有文件，而不忽略文件夹，可以在该文件夹下新建`.gitignore`文件，并写入如下代码
*
!.gitignore
# 忽略 build/ 目录下的所有文件
build/
# 忽略 doc/notes.txt 但不包括 doc/server/arch.txt
doc/*.txt
# 忽略doc下所有以txt结尾的文件，包括子文件夹
doc/**/*.txt
```
## 3. git 建立分支
得到最新分支的源码后，在本地通过 `git branch [新分支的名字]` 新建一个分支，为分支起一个有意义的名字，比如开发新功能的分支，可以这样写 `feature_xxx`，改bug写成 `fixbug_xxx`。然后通过 `git chekcout [目标分支的名字]` 切换到该分支。

## 4. git add 保存到暂存区
通过 `git add .` 的命令可以将所有被追踪的文件保存到暂存区，暂存区是介于工作区与本地仓库的中间状态，一般用于存储未测试或者未全部完成的代码。如果需要撤销 `git add`，可先用 `git status` 查看文件追踪状态，使用 `git reset HEAD` 撤销上次 `add` 的全部提交，或者 `git reset HEAD [文件路径]` 撤销某个文件的 add 提交。

## 5. git commit 提交到本地仓库
当功能开发并且测试完毕后，可以通过 `git commit -m '本次提交的注释信息'` 提交到本地仓库，如果需要撤回本地操作，需要使用 `git log` 查看本地 `commit` 提交的 `commit id`。然后通过 `git reset [commit id]` 来进行回滚。

## 6. git push 提交到远程仓库
通过多次 commit 提交，最终在这个分支上完成了全部的功能开发，下一步是将这个分支通过 `git push -u origin master` 推送到远程仓库。该命令的含义是将本地的 `master` 分支推送到 `origin` 主机的 `master` 分支。如果 `master` 不存在，则会被新建。

## 7. git pull 拉取代码
多人开发时，需要先拉取最新的代码，然后再开始工作。 `git pull` 相当于 `git fetch`+`git merge`。所以本地落后远程分支时会产生一条多余的 commit 信息。如果需要避免这个 commit 信息，可以用 `git pull --rebase` 拉取代码，这个命令相当于 `git fetch`+`git rebase` 没有冲突时自动合并，有冲突时 fix 冲突。不会产生多余信息。


## 关于Github flow工作流

**线上只有一个分支 master**，修改 bug 和开发功能都在本地分支开发，然后进行分支推送，发起合并请求

- 第一步：根据需求，从 master 拉出新分支，不区分功能分支或补丁分支。
- 第二步：新分支开发完成后，或者需要讨论的时候，就向 master 发起一个 **pull request（简称PR，在Gitlab中叫做Merge Request）**。
- 第三步：**Pull Request 既是一个通知，让别人注意到你的请求，又是一种对话机制**，大家一起评审和讨论你的代码。对话过程中，你还可以不断提交代码。
- 第四步：你的 Pull Request 被接受，合并进 master，重新部署后，原来你拉出来的那个分支就被删除。（先部署再合并也可。）

# 使用 git 一般需要注意哪些问题
1. 第一次提交时，应该在项目中提前声明 `.gitignore` 文件来忽略相关文件及资源（密码文件，中间生成的文件和目录，本地设置的文件，临时文件等）。
2. 应该在测试完代码之后再进行 commit 提交到本地仓库
3. Git Merge 有两种合并：一种是"直进式合并"（fast forward），不生成单独的合并节点；另一种是"非直进式合并"（none fast-forword），会生成单独节点。前者不利于保持 commit 信息的清晰，也不利于以后的回滚，**建议总是采用后者（即使用--no-ff参数）。只要发生合并，就要有一个单独的合并节点**。
4. 执行 `git reset --hard [commit]` 之前，要确保本地工作目录中的文件已经提交到远程仓库，或者已经备份到另一个目录中。永远要慎重使用这个命令，**因为当 reset 回滚到某一个版本，该版本以后的提交就会被全部删除**。
5. 一次提交只包含一个相关的改动。例如，在一次提交中解决一个 bug，或者在一次提交中增加一个新功能。最好不要出现，在一次提交中，既添加了一个新功能，又包含了bug修复。这样的好处是，
   1. 在 `git revert` 时，可以方便的撤销那个 commit 的改动。
   2. 对开发团队的其他成员来说，更容易理解你做的改动。
6. 如果已经把 commit 推送到远程仓库服务器了，就**不要轻易对本地仓库中的该 commit 做改动**，例如，不要使用 git reset、git rebase、git commit --amend 对该 commit 修改。
7. 在执行 git revert 和 git reset 等命令时，一定要清楚当前在哪个分支上。查看当前分支：git branch -v
8. commit message 要简洁明确，方便他人理解。如果需要重写 message 可以用 `git commit --amend -m 'message'` 重新编辑。
9. 开发一个相对独立的功能时，新建一个分支来是比较合适且易于管理的。新建分支在Git中是廉价且方便的。
10. 当分支过多时，应该及时删掉不用的分支，来保证历史提交的结构的清晰。
11. 及时恢复误删掉的 commit。如果 git reset 删除了已经提交的分支，可以通过 `git reflog` 看到被删掉的 commit。如果需要恢复 commit，可以通过 `git merge <deleted_commit>` 来恢复。
12. 发布本地分支时，对于同一个分支，-u 只使用一次即可，不用每次都加 -u。
13. 当工作完成一半时需要紧急处理其他任务，不要提交不完整的改动。此时可以用 git stash 将你一半的工作临时存储起来，需要的时候再通过 `git stash pop` 或者 `git stash apply [index]` 重新应用，更详细的用法可以参考[官方文档](https://git-scm.com/book/zh/v1/Git-%E5%B7%A5%E5%85%B7-%E5%82%A8%E8%97%8F%EF%BC%88Stashing%EF%BC%89)

# 附录与问题

## 附录一：换行符CRLF的问题

起因：Windows(\r\n)、Linux(\n) 和 MacOS(\r) 三个主流系统的换行符各不相同，所以在跨平台容易出来换行符的问题。一般保持统一的配置即可，通用的配置是将换行符设为 Unix，设置 Git 的参数 `autocrlf` 为 `false`。

```js
#签出签入均不转换
git config --global core.autocrlf false
```
更详细的解释可以参考[这篇博客](https://blog.zengrong.net/post/1722.html)

## 问题一：相比SVN恢复误删除的文件，Git应该怎么操作[2]

```js
// 使用 checkout 检出分支，恢复删除文件。如文件较多，可用通配符 '.' 来恢复
git checkout [branch-name] [file-name]
```

------

参考文章：

- [《版本控制、Git 及其在企业中的应用》(https://www.infoq.cn/article/git-enterprise/)](https://www.infoq.cn/article/git-enterprise/)
- [《Git Bash 提交代码的正确姿势》(https://juejin.im/post/5c2f4e07f265da61483bbf4b)](https://juejin.im/post/5c2f4e07f265da61483bbf4b)
- [《Git由浅入深之存储原理》(https://zhuanlan.zhihu.com/p/25989135)](https://zhuanlan.zhihu.com/p/25989135)
- [《Git由浅入深之版本控制系统》(http://ju.outofmemory.cn/entry/295710)](http://ju.outofmemory.cn/entry/295710)
- [《使用Git、Git GUI和TortoiseGit》 (https://blog.zengrong.net/post/1722.html)](https://blog.zengrong.net/post/1722.html)
- [《Git 工作流程》(http://www.ruanyifeng.com/blog/2015/12/git-workflow.html)](http://www.ruanyifeng.com/blog/2015/12/git-workflow.html)
- [《Git 使用规范流程》(http://www.ruanyifeng.com/blog/2015/08/git-use-process.html)](http://www.ruanyifeng.com/blog/2015/08/git-use-process.html)
- [《常用 Git 命令清单》(http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html)](http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html)
- [《Git 远程操作详解》(http://www.ruanyifeng.com/blog/2014/06/git_remote.html)](http://www.ruanyifeng.com/blog/2014/06/git_remote.html)
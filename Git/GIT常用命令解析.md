* ### 术语
  - 工作区/work tree
  仓库所在的文件夹叫做工作区,就是你在电脑里能看到的目录。
  - 暂存区
  也就是在git add之后,git commit之前的缓冲区,一般在.git/index文件中。
  - 仓库
  git commit之后,根据暂存区的文件生成一个commit id,暂存区的文件归档到仓库中,一般在.git/objects里面

  ### git 
  ```
  # 输出git版本信息
  $ git -v
  git version 2.38.1.windows.1
  
  # 输出常用的命令列表
  $ git -h
  
  # 用于临时配置 Git 的配置选项。
  # 临时把commit id长度限制为 7 个字符
  $ git -c log.abbrevCommit=true -c log.abbrevCommitLength=7  log
  ```

  ### git config
  ```
  # 全局修改配置
  $ git config --global user.email=you@example.com
  $ git config --global user.name=Your Name
  # 获取某个配置项的值
  $ git config user.name
  # 列出所有配置项
  $ git config --list
  $ 删除某个配置项
  $ git config --unset --global user.name
  ```
  ### git init
  ```
  # 初始化一个新的git仓库
  $ git init
  ```

  ### git clone 克隆远程仓库
  ```
  # 克隆远程仓库到本地文件夹my-linux
  $ git clone git://git.kernel.org/pub/scm/.../linux.git my-linux
  
  $ 初始化一个纯版本仓库,没有工作区。用于创建远程仓库
  $ git init --bare
  ```

  ### git add
  对于将工作区的修改添加到暂存区非常重要,只有暂存过的修改才能被git commit提交到仓库
  ```
  # 添加指定的文件到暂存区,可以用正则匹配
  $ git add <file>
  $ git add git-*.sh
  # 添加当前目录下所有文件到暂存区
  $ git add . 
  # 添加之前已经被git管理的文件到暂存区
  $ git add -u
  # 添加之前被git忽略的文件到暂存区
  $ git add -f <file>
  ```

  ### git status 
  可以清楚地了解工作区和暂存区的文件变化情况
  ```
  # 显示详细的修改状态
  $ git status 
  # 以短格式显示状态,更简洁
  $ git status -s 
  # 顺便显示当前分支名
  $ git status -b
  # 显示忽略的文件
  $ git status --ignored
  ```

  ### git diff
  能够显示各个区域之间的差异,理解其返回结果对于分析版本变化和调试非常重要
  ```
  # 显示工作区与暂存区的差异。
  $ git diff 
  # 显示暂存区与仓库的差异
  $ git diff --staged 
  $ git diff --cached
  # 显示工作区与仓库的差异
  $ git diff HEAD
  # 显示两个提交之间的差异
  $ git diff 02d1a7b d2974d6
  # 显示两个分支之间的差异。
  $ git diff dev master
  # 导出差异文件成压缩包
  $ git diff --name-only   144d196 188df06a | xargs tar -cvf pacakge.tar.gz
  ```

  ### git commit
  ```
  # 提交暂存区的修改,并添加提交信息,生成commit id
  $ git commit -m "message"
  # 提交工作区和暂存区的所有修改,不包含新建的文件
  $ git commit -am "message"
  # 修改最后一次提交的信息
  $ git commit --amend
  # 绕过钩子程序
  $ git commit --no-verify
  # 允许空白提交,git tag可能会用到
  $ git commit --allow-empty
  ```

  ### git notes
  非常适合用来为提交添加额外的评论信息,这些信息存储在.git/notes目录中
  ```
  # 添加注释信息到指定提交,如果不指定提交则默认为HEAD
  $ git notes add -m "This is a note"  7d68213
  # 删除指定提交的注释信息
  $ git notes remove 7d68213	
  # 显示指定提交的注释信息,如果不指定提交则默认为HEAD
  $ git notes show 7d68213
  # 显示全部的注释信息
  $ git notes list
  # 编辑指定提交的注释信息
  $ git notes edit 7d68213
  ```

  ### git restore
  可以用于回滚工作区和暂存区的修改,将文件恢复到指定状态,非常适合于版本控制和修改回退。
  ```
  # 放弃对工作区文件的修改,恢复到仓库HEAD的状态,可用正则匹配
  $ git restore hello.c                   
  $ git restore '*.c'
  $ git restore .
  # 恢复工作区文件到指定提交的状态
  $ git restore --source 7d68213 hello.c
  $ git restore -s 7d68213 hello.c
  # 仅恢复暂存区的文件到HEAD状态,不影响工作区,相当于撤销git add hello.c操作
  $ git restore --staged hello.c
  ```

  ### git reset
  用于重置当前分支的HEAD为指定状态,它会改变reflog和仓库历史。
  ```
  # 重置HEAD为指定提交,重置暂存区但保留工作区修改
  $ git reset 7d68213
  $ git reset --mixed 7d68213
  # 重置HEAD为指定提交,但保留工作区修改
  $ git reset --soft 7d68213
  # 回滚HEAD两个版本,但保留工作区修改
  $ git reset --soft HEAD~2
  # 重置HEAD为指定提交,重置暂存区和工作区,丢弃所有修改
  $ git reset --hard dc7ccb1
  # 重置时保留未跟踪文件。
  $ git reset --mixed --keep 2d3a2b9
  ```

  ### git rm
  用于从git中删除文件。它会将文件从工作区和暂存区移除,并且提交修改,使文件从仓库中永久删除
  ```
  # 删除指定文件,并提交移除操作到暂存区,删除前会检查文件状态,以免误删未提交的修改。
  $ git rm index.php
  # 删除指定文件,并提交移除操作到暂存区,删除前不会检查文件状态
  $ git rm -f index.php
  # 删除暂存区和仓库中的文件,但保留工作区中的文件
  $ git rm --cached xx.log
  # 递归删除整个目录
  $ git rm -r runtime/
  # 删除目录中的所有文件的暂存区和仓库版本,但保留工作区中的文件,一般用于.gitignore文件添加.idea文件夹时,把.idea从暂存区和仓库删掉同时保留工作区文件
  $ git rm -r --cached .idea/
  ```

  ### git mv
  用于移动或重命名文件和目录。它实际上是git rm和git add的组合,用于跟踪文件移动和重命名
  ```
  # 移动/重命名文件或目录 <source> 为 <destination>
  $ git mv index.php index.php.bak
  ```

  ### git branch
  用于列出、创建和删除分支
  ```
  # 列出本地所有分支
  $ git branch
  # 创建本地新分支
  $ git branch feature/devlop
  # 删除本地分支,只能删除已合并的分支,或者HEAD和master一致的分支(还没commit过)
  $ git branch -d feature/devlop
  # 删除本地分支,可以删除未合并的分支
  $ git branch -D feature/devlop
  # 列出本地分支及其最后一个提交
  $ git branch -v
  # 列出本地已经合并到当前分支的分支
  $ git branch --merged
  # 列出尚未合并到当前分支的分支
  $ git branch --no-merged
  # 列出本地和远程所有分支
  $ git branch -a
  # 重命名分支
  $ git branch -m master main
  # 复制一个分支到新创建的分支上,拥有前分支完整的历史
  $ git branch -c new_branch
  # 将本地分支与指定的远程分支建立追踪关系。
  $ git branch --set-upstream-to main main
  # 删除对远程分支的追踪设置。
  $ git branch --unset-upstream new_branch
  # 从 c83c925f6 提交处创建新的分支。
  $ git branch test c83c925f6
  ```

  ### git checkout
  用于切换分支或恢复工作区文件
  ```
  # 切换到指定分支
  $ git checkout master:切换到指定分支
  # 创建新分支并切换到该分支
  $ git checkout -b develop
  # 丢弃工作区的修改,恢复与暂存区一致的版本
  $ git checkout -- index.php
  # 工作区中文件的版本回滚到仓库指定提交
  $ git checkout cdc5737 -- index.php
  # 切换到指定tag所指向的提交,配合rebase使用可以在重要的标记状态继续工作,同时不必担心丢失之前的提交
  $ git checkout v1.0
  ```

  ### git switch
  git 2.23版本新增的命令,用于切换分支或恢复工作区文件
  ```
  # 切换到master分支
  $ git switch master  
  # 创建并切换到新分支new-feature 
  $ git switch -c new-feature  
  # 删除已合并的分支 
  $ git switch -d old-feature
  # 强制切换,重置工作区
  $ git switch -f master 
  # 创建空分支  
  $ git switch --orphan new-branch 
  ```

  ### git merge
  用于将两个或多个开发历史合并在一起
  ```
  # 将指定分支 develop 合并到当前分支,如果可以快进则快进,如果不能快进则创建一个新的合并提交,保留完整的分支记录
  # 快进指的是如果develop HEAD是master HEAD的上游,则可以直接把master HEAD改成develop HEAD
  $ git merge develop
  # 禁用快进,创建一个新的合并提交,即使可以进行快进合并,保留完整的分支记录,常用于开发阶段
  $ git merge --no-ff develop
  # 仅在可以进行快进合并时才会合并指定分支
  $ git merge --ff-only develop
  # 将指定分支 develop 的多个提交压缩到一个提交,合并到当前分支的暂存区,需要手动提交
  $ git merge --squash develop
  # 执行合并但不创建提交,需要手动提交
  $ git merge --no-commit develop
  ```

  ### git mergetool
  用于调解merge冲突的命令,在发生merge冲突时,直接运行
  ```
  # 指定使用哪个工具调解冲突
  $ git mergetool --tool=vimdiff
  $ git mergetool --tool=kdiff3
  $ git mergetool --tool=meld
  ```


  ### git log 
  用于查看提交历史
  ```
  # 显示最近两次提交
  $ git log -2
  # 显示某个作者的提交日志 
  $ git log --author="ben"
  # 显示某个日期范围内的提交 
  $ git log --since=1.month --until="2018-02-28"
  # 显示包含某个关键词的提交
  $ git log --grep="README"
  # 显示某个文件被修改的历史 
  $ git log --follow README.md
  # 一条命令显示简洁日志、分支和版本标签
  $ git log --oneline --decorate --all --graph
  # 显示提交统计信息 
  $ git log --stat
  # 显示某个tag到当前提交的日志 
  $ git log v1.0.0..HEAD
  # 显示某个tag和最后一个tag之间的所有提交
  git log v1.0.0..v1.2.1
  # 显示某个分支的日志
  $ git log master
  # 查看所有日志记录
  git log --all 
  ```

  ### git stash
  用于临时存储和还原未提交的工作
  当你在某个分支开发某个新的特性,代码还在工作区,突然需要切换到另一个分支解决一个bug,如果直接切换分支可能会提示代码会被覆盖
  ```
  error: Your local changes to the following files would be overwritten by checkout:
  	ttt
  Please commit your changes or stash them before you switch branches.
  ```
  需要你先提交再切换分支,然后你因为代码没有完成各种原因并不想提交,这个时候就可以用git stash,具体操作如下
  1. git stash保存当前工作进度
  2. 切换分支并修复Bug
  3. git stash pop恢复工作进度
  4. 继续开发新特性

  ```
  # 显示所有存储的stash,可以使用git stash apply恢复指定的stash。
  $ git stash list
  # 显示指定stash的更改信息,不恢复stash。
  $ git stash show stash@{0}
  # 以补丁的形式显示更改信息。
  $ git stash show -p
  # 恢复指定的stash到一个新分支上。
  $ git stash branch new_branch
  # 恢复最近一次的stash,并从stash list中移除它。
  $ git stash pop
  # 从stash list中移除一个stash,不会恢复它。
  $ git stash drop
  # 清除所有stash。
  $ git stash clear
  # 恢复指定的stash,但是不从stash list中移除它,可以用于测试恢复效果。
  $ git stash apply stash@{0}
  # 保存当前工作并添加注释,便于之后查找恢复。
  $ git stash save "some comments"
  # 交互式地选择要暂存的更改部分,不保存全部的工作进度。
  $ git stash save --patch
  # 只暂存已经添加至暂存区的更改,未添加的更改不会被暂存。
  $ git stash save --keep-index
  ```

  ### git tag
  用于创建和管理标签,可以给项目的重要历史提交打上标签,以示重要的发布版本或里程碑
  ```
  # 打一个轻量级标签
  $ git tag v1.0
  # 打一个带注释的标签
  $ git tag -a v2.0 -m "Release version 2.0"
  # 给过去的某次提交打标签
  $ git tag -a v1.1 9fa1799
  # 查看v1.1的标签信息和提交信息
  $ git show v1.1
  # 从标签v1.1创建新分支
  $ git checkout -b release-v1.1 v1.1
  # 删除本地标签v1.1
  $ git tag -d v1.1
  # 推送单个新标签到远程
  $ git push origin v1.0
  # 推送所有新标签到远程
  $ git push origin --tags
  # 删除远程标签
  $ git tag -r <tagname>
  ```

  ### git worktree
  用于管理同一仓库的多个工作树
  ```
  需要用到再研究@todo
  ```

  ### git fetch
  将远程仓库的最新提交下载到本地,但是不会自动合并到当前分支。这意味着使用fetch获取的数据需要手动merge到本地分支之后才会生效
  ```
  # 获取所有远程仓库最新提交:
  $ git fetch --all
  # 查看本地和远程仓库差异,然后合并
  $ git log origin/dev..dev
  $ git diff origin/dev dev
  $ git merge
  
  # 只获取origin远程仓库master分支的最新两个提交
  $ git fetch origin master --depth 2
  # 获取origin远程仓库,并清除服务器上已经删除的分支
  $ git fetch --prune origin
  # 将一个浅仓库转为完整仓库:
  $ git fetch --unshallow
  # 预览将要获取的提交,但不实际获取
  $ git fetch --dry-run
  ```

  ### git pull
  用于从远程仓库获取最新数据后与本地当前分支自动合并,实际上是git fetch和git merge的组合
  ```
  # 从origin远程仓库获取master分支并合并: 
  $ git pull origin master
  # 使用rebase合并:
  $ git pull --rebase
  # 只在可以快速向前合并的情况下进行合并:
  $ git pull --ff-only
  # 从所有远程仓库获取数据并合并: 
  $ git pull --all
  # 删除远程仓库origin并更新引用:
  $ git pull -d origin
  # 拉取远程仓库的标签
  $ git pull --tags
  ```

  ### git push
  将本地提交推送到远程仓库,与其他协作者分享同步工作成果
  ```
  # 推送master分支到origin远程仓库: 
  $ git push origin master
  # 推送全部本地分支到origin:
  $ git push origin --all
  # 删除远程仓库origin的dev分支:
  $ git push origin --delete dev
  # 强制推送master分支到origin,会覆盖远程分支:
  $ git push origin master --force
  # 只有在远程master分支是当前master分支的上游分支时,才强制推送:
  $ git push origin master --force-with-lease
  # 推送所有标签到远程仓库
  $ git push <remote> --tags
  ```

  ### git remote
  用于管理远程仓库的别名,从而指定不同的远程仓库及其URL。Git支持为同一个仓库添加多个远程仓库。可以通过多次运行git remote add命令添加多个远程仓库别名,并指定不同的仓库URL。
  ```
  git remote add origin https://github.com/user/repo.git
  git remote add upstream https://gitlab.com/user/repo.git 
  git remote add alias https://bitbucket.org/user/repo.git
  ```
  ```
  # 列出当前所有的远程仓库别名
  $ git remote
  # 添加一个新的远程仓库origin,URL为https://github.com/user/repo.git
  $ git remote add origin https://github.com/user/repo.git
  # 重命名远程仓库upstream为up
  $ git remote rename upstream up
  # 移除远程仓库up
  $ git remote remove up
  # 修改远程仓库origin的URL
  $ git remote set-url origin https://gitee.com/user/repo.git
  # 列出远程仓库origin的详细信息
  $ git remote show origin
  ```

  ### git submodule
  主要用于管理仓库内嵌套的子模块。
  子模块允许我们将一个仓库当作另一个仓库的子目录,这个子目录又是一个完整的仓库。
  这样,我们可以独立开发子模块中的项目,同时在主项目中可以指定相应提交记录。
  当克隆仓库时,需要运行git submodule init和git submodule update以初始化子模块及其历史。

  ### git show
  可以让我们查看某次提交的所有细节,包括提交信息、修改的文件、各文件差异等。
  这在开发和调试代码过程中非常有用,可以快速定位引入某个问题的提交,检查相关文件变化等。
  ```
  # 显示HEAD指向的提交信息
  $ git show
  # 显示master分支的最新提交815e1c2的信息
  $ git show 815e1c2
  # 仅显示该提交修改的文件列表
  $ git show 815e1c2 --name-only
  # 显示该提交的文件差异 
  $ git show 815e1c2 -p
  # 以medium格式显示提交信息
  $ git show 815e1c2 --pretty=medium
  # 显示要应用提交815e1c2,需要运行的cherry-pick命令
  $ git show 815e1c2 --cherry-pick
  ```

  ### git shortlog
  用于简洁地浏览提交日志,方便查看贡献者的提交,git log的简洁版
  ```
  # 显示所有提交的简要日志,按提交者排序
  $ git shortlog
  # 仅显示最近10次提交
  $ git shortlog -n 10
  # 显示提交者和提交次数的汇总,不显示提交摘要
  $ git shortlog -s
  # 显示所有提交者的邮箱和提交SHA-1校验和
  $ git shortlog -e --all
  ```

  ### git describe
  根据最近的标签来描述未打标签的提交
  在持续集成或开发过程中,经常需要知道当前代码距离最后一个发布标签的提交次数,以判断当前进度或是否需要发布新版本。
  此时,可以使用git describe快速获取这一信息,例如:
  ```
  # 表示当前提交g6145309距离标签v1.2.3有5次提交。
  $ git describe
  v1.2.3-5-g6145309
  ```

  ### git apply
  将补丁文件的更改应用到当前的Git树对象或工作树上,这在版本升级、临时测试补丁效果以及分发开发组内的修补等方面非常有用
  ```
  # 应用0001-fix-bug.patch补丁
  $ git apply 0001-fix-bug.patch
  # 仅显示应用该补丁后文件的变化统计:
  $ git apply --stat 0001-fix-bug.patch
  # 检查补丁是否可以应用成功:
  $ git apply --check 0001-fix-bug.patch
  # 应用补丁,更新索引而非工作树
  $ git apply --cached 0001-fix-bug.patch
  # 反转补丁的更改
  $ git apply --reverse 0001-fix-bug.patch
  # 在无法应用的部分中创建reject文件
  $ git apply --reject 0001-fix-bug.patch
  ```

  ### git cherry-pick
  用于将指定提交的更改应用到当前分支,这与git merge的效果类似。
  但是,cherry-pick会记录为一个全新的提交,而非合并提交。
  git cherry-pick是一个高级工具,可以通过应用特定提交的更改来调整项目的开发进展。
  但是,如果对其原理不太清晰,反而可能产生许多无意义的提交,破坏提交历史的整洁性。
  ```
  # 应用master分支的最近3次提交
  $ git cherry-pick master~3..master
  # 记录为新的提交,而非应用指定提交
  $ git cherry-pick -x 8fd4e43
  # 编辑提交信息后进行提交:
  $ git cherry-pick -e 8fd4e43
  # 不进行提交,仅更新工作树
  $ git cherry-pick -n 8fd4e43
  # 应用提交更改,不创建新的提交:
  $ git cherry-pick --no-commit 8fd4e43
  # 使用ours合并策略应用提交
  $ git cherry-pick -Xours 8fd4e43
  ```

  ### git rebase
  git rebase用于将指定分支的变更重新定位到另一个分支上,可以看作是"变基"。
  它会取消指定分支的所有提交,并将其内容应用到另一分支上,出现新的提交记录。
  git rebase是一个高级工具,可以使提交历史变得更加整洁。
  但是,如果在公开分享的分支上使用,可能会对其他开发者产生影响。
  ```
  # 将experiment分支rebase到master分支  
  $ git checkout experiment 
  $ git rebase master
  # 交互式rebase,合并最近3次提交  
  $ git rebase -i HEAD~3
  # 继续rebase操作  
  $ git rebase --continue  
  # 终止rebase操作  
  $ git rebase --abort
  ```

  ### git revert
  git revert用于创建一个新的提交来撤销指定提交的更改。
  它会创建一个全新的提交,回滚指定的提交,但是不会修改提交历史,线上仓库需要回滚的时候可以用revert取代reset,避免了git push -f强推的操作
  ```
  # 撤销最近一次提交 
  $ git revert HEAD
  # 撤销指定的提交 
  $ git revert 8fd4e43
  # 编辑revert信息
  $ git revert -e 8fd4e43
  # 撤销指定提交,不记录revert到操作
  $ git revert --no-commit 8fd4e43
  ```

  ### git reflog
  reset回滚之后,以前的提交就不会显示在git log里面了,找不到提交id就没办法reset回来,可以在reflog查到分支变更记录
  ```
  # 查找master最近5次变更
  $ git reflog show master | head - 5
  750c435 (HEAD -> master) master@{0}: commit: xx
  2d3a2b9 (tag: v1.0) master@{1}: reset: moving to 2d3a2b9d
  dc7ccb1 master@{2}: reset: moving to dc7ccb1
  2d3a2b9 (tag: v1.0) master@{3}: reset: moving to 2d3a2b9dc
  
  # 回滚到master两次改变之前的commit,git log也会恢复
  $ git reset --hard master@{2}
  ```

  ### git cat-file
  用于获取git对象的内容、类型和长度信息
  ```
  # 显示提交对象a6be41的内容
  $ git cat-file -p a6be41
  # 显示提交对象a6be41的类型
  $ git cat-file -t a6be41
  # 显示提交对象a6be41的大小
  $ git cat-file -s a6be41
  # 显示提交对象a6be41的头信息
  $ git cat-file -e a6be41
  # 显示多个对象(a6be41,cat-file, 9fceb46)的类型
  $ git cat-file --batch-check=type a6be41 cat-file 9fceb46
  ```

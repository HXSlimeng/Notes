- git push --set-upstream origin master：push到origin的master分支

  ##### 分支

  - git pull origin master ，在master分支下，保证当前代码与线上同步，是最新的
  - git branch <分支名> ，正儿八经的新建分支。
  - git checkout <分支名> ，切换分支。
  - git push origin <分支名> ,把本地分支推到远端，让远端也有一个你的分支，用来后面提交你的代码。
  - git checkout -b , <分支名>，新建分支并切换到该分支
  - git branch -d <分支名> ，删除本地分支
  - git branch origin --delete <分支名>
  ##### 提交
  - git status ,查看自己写了哪些东西。
  - git add .
  - git commit -m ‘本次提交的描述（写自己的话，不要抄我的，哼！）’
  - git push origin <分支名> ,push是从本地向远端推代码，也就是把自己写的代码提交上去，像给老师交作业一样。
执行完上一步，有可能报错，此时淡定，不认识英语的话在线翻译是干嘛的，大多数时候提示你，本地落后于远端，（意思就是远端比本地多了一些代码，为什么会出现这种情况呢，肯定是你跟别人共用一个分支，他提交了一段代码到远端，所以导致你的本地落后于远端）既然落后了，那就把他更新成最新的呗，
  - git pull --rebase origin 远程分支名
执行完之后，你的代码就跟远端又一样了。并且你的修改仍然在，此时再提交你的代码
  - git push origin <分支名>

我是错的
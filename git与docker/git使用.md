# git使用

+ Gitflow工作流

  + Master 主分支,用于服务器运行
  + Hotfix 热更新分支,用于bug,补丁修复
  + Release 发布分支 ,用于为发布准备
  + Develop  开发分支,员工在这个分支上进行开发
  + Feature  个人拉进行的分支
  
+ 工作流程

  + 搭建主分支,中央仓库Master

  + 创建Develop开发分支

    + git branch develop#创建分支
  
    + git push -u origin develop# 将本地develop分支推送到origin主机的develop分支,以后直接使用git push即可,
  
  + ```
      git push <远程主机名> <本地分支名>:<远程分支名>
    分支推送顺序的写法是<来源地>:<目的地>，所以git pull是<远程分支>:<本地分支>，而git push是<本地分支>:<远程分支>。
    ```

    + git clone 网址   其他用户拉去中央仓库
  
    + git chechout -b develop origin/develop#切换到develop分支
  
  + 开发Feature
  
    + git checkout -b some-feature develop#个人基于develop进行开发
  
      ```
      git status
    git add <some-file>
      git commit正常使用
    git pull origin develop
      git checkout develop
      git merge some-feature
    git push
      git branch -d some-feature
      ```
    ```
    
    ```
  
  + 准备发布分支
  
    + git checkout -b release-0.1 develop
    + 这个分支是清理发布、执行所有测试、更新文档和其它为下个发布做准备操作的地方，像是一个专门用于改善发布的功能分支。
  
  + 完成发布,使用release同步master与develop
  
    + ```
    git checkout master
      git merge release-0.1#分支合并命令
    git push
      git checkout develop
      git merge release-0.1
      git push
      git branch -d release-0.1
      ```
  
  + 合并到master就需要打上标签
  
    ```
      git tag -a 0.1 -m "Initial public release" master
      git push --tags
    ```
  
+ bug修复Hotfix
  
    + 热更新修复master
  
      ```
    git checkout -b issue-#001 master
      # Fix the bug
      git checkout master
      git merge issue-#001
      git push
      ```
  
    + 同步到develop
  
      ```
      git checkout develop
      git merge issue-#001
      git push
      git branch -d issue-#001
      ```
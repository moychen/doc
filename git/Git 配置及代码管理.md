# 版本控制工具

## GIT

### 本地配置

Git是分布式的代码管理工具，远程的代码管理是基于SSH的，所以要使用远程的Git则需要SSH的配置。
#### windows配置

配置Git全局的username和email
```
git config --global user.name "julymemory"
git config --global user.email "enumhack@163.com"
```

生成ssh key
```
ssh-keygen -t rsa -C “enumhack@163.com"
```
生成的时候注意选择sshkey保存的位置，设置连接时的password（建议不设置密码，方便本地使用）。

### 版本控制

#### 基本流程

```bash
1. 查看远程库信息，使用git remote -v；
2. 本地新建的分支如果不推送到远程，对其他人就是不可见的；
3. 从本地推送分支，使用git push origin branch-name，如果推送失败，先用git pull抓取远程的新提交；
4. 在本地创建和远程分支对应的分支，使用git checkout -b branch-name origin/branch-name，本地和远程分支的名称最好一致；
5. 建立本地分支和远程分支的关联，使用git branch --set-upstream branch-name origin/branch-name；
6. 从远程抓取分支，使用git pull，如果有冲突，要先处理冲突。
```



#### git branch and git Merge 

#### git pull and git fetch
# Git 配置及代码管理

## Git 本地配置
Git是分布式的代码管理工具，远程的代码管理是基于SSH的，所以要使用远程的Git则需要SSH的配置。
### windows配置
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

## Git 代码管理
### Git Branching and Merging 
```

```
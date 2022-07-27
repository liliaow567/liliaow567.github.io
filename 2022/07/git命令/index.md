# Git命令


<!--more-->

## 1 git 配置

在 `%UserProfile%\.gitconfig`文件中，配置用户信息和代理设置，如下

```toml
[user]
	email = email@email.dom
	name = username

[http]
	proxy = http://127.0.0.1:7890

[https]
	proxy = http://127.0.0.1:7890
```

## 2 和 github 连接

### 2.1 密钥生成和相关配置

1. 使用 `ssh-keygen` 生成密钥
2. 在 `%UserProfile%\.ssh\config` 文件中添加配置，配置如下
3. 登录 github 账号，在用户设置中，上传第一步生成的密钥

```yaml
# github
Host github
    HostName github.com
    AddKeysToAgent yes
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/gitsshkey
    User username
```

### 2.2 建立连接

1. 在github新建仓库 `example`
2. 本地仓库初始化，并连接

```bash
git init
git add .
git commit -m 'init'
# 如有必要修改分支名称为 main
git branch -m main
# 修改远程仓库名
git remote add origin git@github.com:username/example.git
# -f 为强制推送
git push -f -u origin main

# 从远端拉取到本地
git pull origin main
```

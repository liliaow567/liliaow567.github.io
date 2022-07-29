# windows系统 Git Bash 中文乱码问题解决


<!--more-->


## 1 Windows系统和Git 版本
1. windows 10 v 1909
2. Git version 2.62

## 2 中文乱码问题
运行 `"%GIT_HOME%\git-bash.exe"` 未发现中文乱码问题，**中文乱码主要来源于运行 `"%GIT_HOME%\bin\bash.exe"`** 

乱码表现主要发现来自于四个方面：

1. git 相关显示的中文乱码

2. 中文输入的乱码

3. vi 编辑器的中文乱码

4. 运行一些 bash 命令时（如 ls），出现乱码

## 3 解决方案
修改配置文件

1. `gitconfig` 文件，路径 `"%GIT_HOME%\etc\gitconfig"`，添加如下项

```bash
[core]
	quotepath = off
[gui]
	encoding = utf-8
[i18n]
	commitencoding = utf-8
	logoutputencoding = utf-8
```
2. `inputrc` 文件，路径 `"%GIT_HOME%\etc\inputrc"`，修改如下项

```bash
# disable/enable 8bit input
set input-meta off
set output-meta on
set convert-meta off
```
3. `vimrc` 文件，路径 `"%GIT_HOME%\etc\vimrc"`， 添加如下项
```bash
set encoding=utf-8
```
4. `.bashrc` 文件，路径 `%USERPROFILE%\.bashrc`，(没有就自己创建)，添加如下项
```bash
export LESSCHARSET=utf-8 # 配合 gitconfig 文件，解决 git 相关乱码问题
export LANG="C.UTF-8"
```

# nsd1906_devops_day04

## ansible

如果你连接远程主机是个普通用户，怎么执行管理任务？

```shell
# vim ansible.cfg
[defaults]
inventory = inventory
remote_user = zhangsan

[priviledge_escalation]    # 提权
become = yes               # 需要切换用户
become_method = sudo       # 切换的方式是sudo（另一种方式是su）
become_user = root         # 切换成管理员
become_ask_pass = no       # 不询问切换密码

# 被管理的服务器，需要配置sudo
# visudo
zhangsan	ALL=(ALL)		NOPASSWD: ALL
```

### ansible-cmdb

- 将ansible收集的主机信息转换成web页面

```python
# 收集远程主机的信息
(nsd1906) [root@room8pc16 myansible]# ansible all -m setup --tree /tmp/nsd1906out

# 安装ansible-cmdb
(nsd1906) [root@room8pc16 myansible]# pip install /var/ftp/pub/zzg_pypkgs/ansible-cmdb_pkgs/*
# 或在线安装
(nsd1906) [root@room8pc16 myansible]# pip install ansible-cmdb

# 生成web页面
(nsd1906) [root@room8pc16 myansible]# ansible-cmdb /tmp/nsd1906out > /tmp/hosts.html
(nsd1906) [root@room8pc16 myansible]# firefox /tmp/hosts.html &
```

## git

- 它是一个分布式的软件版控制系统

```shell
# 安装
[root@node4 ~]# yum install -y git
# 配置基本信息
[root@node4 ~]# git config --global user.name "Mr.Zhang"
[root@node4 ~]# git config --global user.email "zzg@tedu.cn"
[root@node4 ~]# git config --global core.editor vim
# 查看信息
[root@node4 ~]# git config --list 
user.name=Mr.Zhang
user.email=zzg@tedu.cn
core.editor=vim
[root@node4 ~]# cat ~/.gitconfig 
[user]
	name = Mr.Zhang
	email = zzg@tedu.cn
[core]
	editor = vim

```

### git的重要工作区域

- 工作区：编写代码的工作目录
- 暂存区：.git/index，工作区和版本库之间的缓冲地带
- 版本库：工作区中的.git目录

```mermaid
graph LR
w(工作区)--git add-->s(暂存区)
s--git commit-->g(版本库)
```

### git应用

```shell
# 创建版本库，方法一：编写项目之初创建
[root@node4 ~]# git init mytest
初始化空的 Git 版本库于 /root/mytest/.git/
[root@node4 ~]# ls -A mytest/
.git
# 创建版本库，方法二：在已存在的项目中创建版本库
[root@node4 ~]# mkdir myweb
[root@node4 ~]# cd myweb
[root@node4 myweb]# echo '<h1>Hello World</h1>' > index.html
[root@node4 myweb]# git init
初始化空的 Git 版本库于 /root/myweb/.git/
[root@node4 myweb]# ls -A
.git  index.html

# 大多数的操作都可以通过git status得到提示
[root@node4 myweb]# git status
# 位于分支 master
#
# 初始提交
#
# 未跟踪的文件:
#   （使用 "git add <file>..." 以包含要提交的内容）
#
#	index.html
提交为空，但是存在尚未跟踪的文件（使用 "git add" 建立跟踪）

[root@node4 myweb]# git status -s
?? index.html   # ??表示当前文件的状态未知

# 提交数据到暂存区
[root@node4 myweb]# cp /etc/hosts .
[root@node4 myweb]# git add index.html
[root@node4 myweb]# git status
# 位于分支 master
#
# 初始提交
#
# 要提交的变更：
#   （使用 "git rm --cached <file>..." 撤出暂存区）
#
#	新文件：    index.html
#
# 未跟踪的文件:
#   （使用 "git add <file>..." 以包含要提交的内容）
#
#	hosts
[root@node4 myweb]# git status -s
A  index.html   # A表示新增
?? hosts
[root@node4 myweb]# git add .
[root@node4 myweb]# git status -s
A  hosts
A  index.html

# 从暂存区撤出hosts
[root@node4 myweb]# git rm --cached hosts
rm 'hosts'
[root@node4 myweb]# git status -s
A  index.html
?? hosts

```









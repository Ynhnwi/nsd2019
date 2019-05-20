# tedu_py1812_devops_day03

## zabbix

官方文档页：https://www.zabbix.com/documentation/3.4/zh/manual

这里演示的环境路径是：/var/www/html/zabbix/

那么zabbix api地址是：http://192.168.4.2/zabbix/api_jsonrpc.php

## ansible

### 准备环境

启动三台虚拟机：

| node4.tedu.cn | 192.168.4.4/24 |
| ------------- | -------------- |
| node5.tedu.cn | 192.168.4.5/24 |
| node6.tedu.cn | 192.168.4.6/24 |

名称解析：

```shell
[root@room8pc16 nsd2019]# for i in {1..254}
> do
> echo -e "192.168.4.$i\tnode$i.tedu.cn\tnode$i" >> /etc/hosts
> done
```

收集主机密钥：

```shell
[root@room8pc16 ~]# ssh-keyscan 192.168.4.{4..6} node{4..6} node{4..6}.tedu.cn >> ~/.ssh/known_hosts 
```

安装ansible：

```shell
[root@room8pc16 zzg_pypkgs]# yum install -y sshpass
[root@room8pc16 zzg_pypkgs]# cd ansible_pkg/
[root@room8pc16 ansible_pkg]# pip3 install *
或
[root@room8pc16 ansible_pkg]# pip3 install ansible
```

### 配置ansible的基础应用环境

```shell
[root@room8pc16 day03]# mkdir myansible
[root@room8pc16 day03]# cd myansible/
[root@room8pc16 myansible]# vim ansible.cfg
[defaults]
inventory = hosts
remote_user = root
[dbservers]
node4.tedu.cn

[webservers]
node5.tedu.cn
node6.tedu.cn
[root@room8pc16 myansible]# ansible all -m ping -k
```

### playbook

调整vim的配置，使它能够适应yaml的语法

```shell
[root@room8pc16 ~]# cat ~/.vimrc 
set ai
set ts=4
set et
set encoding=utf8
autocmd FileType yaml setlocal sw=2 ts=2 et ai
```

创建playbook，实现免密登陆

```shell
[root@room8pc16 ~]# ansible-doc authorized_key
[root@room8pc16 myansible]# vim auth.yml
---
- name: user auth key
  hosts: all
  tasks:
    - name: upload root pub key
      authorized_key:
        user: root
        state: present
        key: "{{ lookup('file', '/root/.ssh/id_rsa.pub') }}"
[root@room8pc16 myansible]# ansible-playbook --syntax-check auth.yml
[root@room8pc16 myansible]# ansible-playbook auth.yml -k
```

通过playbook配置yum

```shell
[root@room8pc16 myansible]# mkdir files
[root@room8pc16 myansible]# vim files/server.repo
[root@room8pc16 myansible]# vim yum.yml
---
- name: upload yum repo file
  hosts: all
  tasks:
    - name: upload server.repo
      copy:
        src: files/server.repo
        dest: /etc/yum.repos.d/server.repo
[root@room8pc16 myansible]# ansible-playbook --syntax-check yum.yml
[root@room8pc16 myansible]# ansible-playbook yum.yml
# 检查web服务器是否安装了mariadb-server
[root@room8pc16 myansible]# ansible dbservers -m yum -a "list=mariadb-server"
```

playbook：在dbservers上配置mariadb，在webservers上配置apache

```shell
[root@room8pc16 myansible]# vim lamp.yml
---
- name: configure dbservers
  hosts: dbservers
  tasks:
    - name: install mariadb
      yum:
        name: mariadb-server
        state: present
    - name: start mariadb
      service:
        name: mariadb
        state: started
        enabled: yes

- name: configure webservers
  hosts: webservers
  tasks:
    - name: install apache
      yum:
        name: [httpd, php, php-mysql]
        state: latest
    - name: start httpd
      service:
        name: httpd
        state: started
        enabled: yes
[root@room8pc16 myansible]# ansible-playbook --syntax-check lamp.yml
[root@room8pc16 myansible]# ansible-playbook lamp.yml
```





### 





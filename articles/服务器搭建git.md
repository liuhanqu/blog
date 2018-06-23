# 在服务器上搭建 git

##  生成 SSH 公钥

首先，你需要确认自己是否已经拥有秘钥。默认情况下，用户的 SSH 秘钥存储在~/.ssh 目录下。进入该目录并列出其中内容，便可以快速确认自己是否已拥有秘钥:

```shell
# 本地
$ cd ~/.ssh
$ ls
config      id_rsa      id_rsa.pub  known_hosts
```

我们需要寻找一对以 id_dsa 或 id_rsa 命名的文件，其中一个带有 .pub 扩展名。.pub 文件是你的公钥，另 一个则是私钥。如果找不到这样的文件或者根本没有 .ssh 目录， 你可以通过运行 ssh-keygen 程序来创建它们

```shell
# 操作环境： Mac
# 本地
$ ssh-keygen
Generating public/private rsa key pair.
# 输入 enter 健
Enter file in which to save the key (/Users/laohan/.ssh/id_rsa):
Created directory '/Users/laohan/.ssh'.
# 两次输入密码
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /Users/laohan/.ssh/id_rsa.
Your public key has been saved in /Users/laohan/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:XhI9aeGsVJklGyUTvNu+6ABzOZdZL2+y5aMOVQa+ZvI laohan@bogon
The key's randomart image is:
+---[RSA 2048]----+
|         .O*+    |
|         =+X .   |
|        o O.o o  |
|       . =.= =   |
|      o S *o* .  |
|       = =.*.o   |
|        o ..E +  |
|         . o.*.  |
|         .o.=o.. |
+----[SHA256]-----+
```

首先 ssh-keygen 会确认密钥的存储位置(默认是 .ssh/id_rsa)，然后它会让你重复一个密码两次，如果不想在使用公钥的时候输入密码，可以留空。

现在，所有做过这一步的用户都得把它们的公钥给你或者 Git 服务器的管理员（假设 SSH 服务被设定为使用公钥机制）。他们所要做的就是复制各自的 .pub 文件内容，并将其通过邮件发送。公钥看起来是这样的:

```shell
$ cat ~/.ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDc4xbJxlMTgiE13I1RL6IsG44+3CQc8Ft03eZnYfNrPVeQIf9r9kTuArSiUnY+BHXn3mrQ5i+5AGi+alys94Pum2dZ68QtlY1QdEl4iN3LFXUkbJc+M0rllaDGH5JNtfk5imVqDo8Tn7aJsFd4IXbwrl3Euf+ccOb+s92RHwzbSRx37tP9pLF9ujfL0UXfg3+DmRJMJT7iN3OiJxfuF5k8KSySEz+YbhQoNeySuvVPeRHG/U6xOGcpzNjQIPApGsuFdLT5R/5x15W7SrC//XWuIQMmlVTW2X0YH+5NjT0nlLVWxS4drtRCS66JXtRceVqs5H5InbsLfALfTPyIkZ4t laohan@bogon
```

具体可以参考 https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/

## 配置服务器

```shell
# 创建一个操作系统用户git
$ sudo adduser git
# 切换用户
$ su git
$ cd
$ mkdir .ssh && chmod 700 .ssh
$ touch .ssh/authorized_keys && chmod 600 .ssh/authorized_keys
```

接下来，把开发者的 SSH 公钥添加到这个用户的 authorized_keys 文件中。

```shell
$ vim .ssh/authorized_keys
# 然后把在之前生成的SSH公钥复制文件尾部
```

又或者假设你通过电邮收到了几个公钥并存到了临时文件里。重复一下，公钥大致看起来是这个样子：

```shell
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDc4xbJxlMTgiE13I1RL6IsG44+3CQc8Ft03eZnYfNrPVeQIf9r9kTuArSiUnY+BHXn3mrQ5i+5AGi+alys94Pum2dZ68QtlY1QdEl4iN3LFXUkbJc+M0rllaDGH5JNtfk5imVqDo8Tn7aJsFd4IXbwrl3Euf+ccOb+s92RHwzbSRx37tP9pLF9ujfL0UXfg3+DmRJMJT7iN3OiJxfuF5k8KSySEz+YbhQoNeySuvVPeRHG/U6xOGcpzNjQIPApGsuFdLT5R/5x15W7SrC//XWuIQMmlVTW2X0YH+5NjT0nlLVWxS4drtRCS66JXtRceVqs5H5InbsLfALfTPyIkZ4t laohan@bogon
```

你可以通过下面的命令追加到 authorized_keys 的末尾

```shells
$ cat /tmp/id_rsa.laohan.pub >> ~/.ssh/authorized_keys
```

这个时候，你可以使用下面的命令，测试是否已经添加成功。

```shell
# gitserver 是你的远程主机的 ip 地址
$ ssh git@gitserver
Last login: Mon Apr 23 18:49:59 2018

# 如果需要你输入密码，那就是没有成功
```

现在，初始化一个不带目录的裸仓库。

```shell
$ cd /opt/git
$ mkdir project.git
$ cd project.git
$ git init --bare
```

这个时候，你就可以把它添加为远程仓库，推送一个分支，从而把第一个版本的项目文件上传到仓库里了。值得注意的是，每次添加一个新项目都需要通过 shell 登入主机并创建一个裸仓库目录。

```shell
$ cd myproject
$ git init
$ git add .
$ gti commit -m 'initial commit'
$ git remote add origin git@gitserver:/opt/git/project.git
# gitserver 是你远程主机的 ip 地址
$ git push origin master
```

作为一个额外的防范措施，你可以用 Git 自带的 git-shell 工具限制 git 用户的活动范围。只要把它设为 git 用户登入的 shell，那么该用户就无法使用普通的 bash 或者 csh 什么的 shell 程序。编辑 /etc/passwd 文件：

```shell
$ sudo vim /etc/passwd
```

在文件末尾，你应该能找到类似这样的行：

```shell
git:x:1000:1000::/home/git:/bin/sh
```

把 bin/sh 改为 /usr/bin/git-shell （或者用 which git-shell 查看它的实际安装路径）。该行修改后的样子如下：

```shell
git:x:1001:1001::/home/git:/usr/bin/git-shell
```

现在 git 用户只能用 SSH 连接来推送和获取 Git 仓库，而不能直接使用主机 shell。尝试普通 SSH 登录的话，会看到下面这样的拒绝信息

```shell
# gitserver 是你远程主机的 ip 地址
$ ssh git@gitserver
fatal: Interactive git shell is not enabled.
hint: ~/git-shell-commands should exist and have read and execute access.
```

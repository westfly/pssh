pssh - Parallel SSH
-------------------

This repo is a clone of the "official" pssh repo at git://aml.cs.byu.edu/pssh.git

I have created it as I want to add some additional options to prsync, and this
seems to be the most straight-forward way to do it.


在原版的基础上，添加了-C 参数，用于指定公共的密码，依赖于[sshpass](http://sourceforge.net/projects/sshpass/)，用于更好的服务于命令行。


## Install
下载 [sshpass](http://sourceforge.net/projects/sshpass/), 编译并安装到系统中。
git pssh 到本地安装，注意pssh需要python的版本>=2.6

```
git clone url
cd local_dir
python setup.py install --record log
```

## UnInstall

```
cat log | xargs rm -rf

```

## Usage 

pssh是一个轻量级的，可以在多台服务器上执行命令的工具，同时支持拷贝文件，是同类工具中很出色的。如果在各个服务器上配置好密钥认证访问，对管理机器显得游刃有余。

## pssh

pssh在多个主机上并行地运行命令
```
-h 执行命令的远程主机列表,文件内容格式[user@]host[:port]
   如 test@172.16.10.10:229
-H 执行命令主机，主机格式 user@ip:port
-l 远程机器的用户名（全局有效）
-p 一次最大允许多少连接
-P 执行时输出执行信息
-o 输出内容重定向到一个文件
-e 执行错误重定向到一个文件
-t 设置命令执行超时时间（一般设置为-1，表示超时）
-A 提示输入密码并且把密码传递给ssh(如果私钥也有密码也用这个参数)
-O 设置ssh一些选项
-x 设置ssh额外的一些参数，可以多个，不同参数间空格分开
-X 同-x,但是只能设置一个参数
-i 显示标准输出和标准错误在每台host执行完毕后
-C 公共密码（新添加，基于sshpass）
```

如下是具体的实例

```
pssh -P -h ip.txt date
pssh -H op_biz@10.139.36.92 -C xman uptime
```
以上命令 需要 ssh到具体机器确认先建立基本的信任关系，如果没有确认，需要在命令行参数中指定StrictHostKeyChecking=no，如下
```
pssh -x "-o StrictHostKeyChecking=no" -P -i -h ip.txt date
```
将结果保存在/tmp目录下
```
pssh -l op_biz -C xman -h ip.txt  -o /tmp 'grep root /etc/passwd'
```

## pscp
pssh 和 pscp 公用psshlib/cli.py 解析命令行参数，所以pssh的参数对于pscp 也有效。具体使用方法
将本机的  /etc/sysconfig/network，scp到目标机器的/tmp目录下，如果有network文件，则会覆盖。
```
pscp -h ip.txt /etc/sysconfig/network /tmp
```
拷贝文件夹
```
pscp -h ip.txt /etc/sysconfig/ -x "-T -r" /tmp
```
"-r" 参数递归拷贝，用于拷贝目录
"-T" 参数用于避免 [Pseudo-terminal will not be allocated because stdin is not a terminal](http://stackoverflow.com/questions/7114990/pseudo-terminal-will-not-be-allocated-because-stdin-is-not-a-terminal)



## prsync
将本地的/etc/sysconfig目录递归同步到目标服务器的tmp/etc目录下,并保持原来的时间戳,使用用户 dongwm
```
prsync -h ip.txt -l dongwm -a -r /etc/sysconfig /tmp/etc /
```

## pslurp
将目标服务器的/tmp/network文件复制到本地的/tmp/test目录下，并更名为test
```
pslurp -h test.txt   -L /tmp/test -l root /tmp/network test 
```
## pnuke
杀死目标服务器的syslog进程,只要ps进程中出现相关词语 都能杀死

```
pnuke -h test.txt   syslog 
```
---
title: 一些基础shell命令
description: Shell 是一个用 C 语言编写的程序，它是用户使用 Linux 的桥梁。Shell 既是一种命令语言，又是一种程序设计语言。
tag:
 - shell
categories:
 - linux
 - code
---

shell if :[Shell if 条件判断 - 小白一生 - 博客园 (cnblogs.com)](https://www.cnblogs.com/liudianer/p/12071476.html)

### ctrl+r

查找历史中输入过的命令

### nohup

- ‘nohup’ 表示不挂起， 即正常退出终端连接也不挂起进程。
- ‘command’ 表示要运行的后台进程 例如：sh run.sh
- ‘>’ 表示重定向 表示将 >左边的消息定向输出>右边的某个文件中 例如以下命令将标出输出重定向到out.log文件中
- ‘2>&1’ 是将标准出错重定向到标准输出， 2 >代表标准出错定向 ，&1代表标准输出
- ‘&’ 表示将程序进程提交到后台运行 ，但只使用& 屏幕还是会打印程序相关日志

使用如下命令后台运行程序，out.log为程序日志输出的目录

    nohup command > out.log 2>&1 &

使用 exit 命令 正常退出终端

    exit


### SSH

- 链接 server：ssh user@192.168.0.1
- 生成 keygen：ssh-keygen -t rsa -C "abc@bac.com"

### ethtool

修改网卡速率: **-s**

这个命令多用于手工设置网络速率，一般千兆网卡支持10|100|1000三个速率，单位是Mbps。


用法:

    ethtool -s eth0 speed 1000 duplex full autoneg off


效果：将设备号eth0对应的物理端口设置为速率为1000Mbps，全双工工作模式，同时关闭自动协商。

注：若需要永久更改有两种方法：

1. 将上述命令写入 /etc/rc.local 文件中，开机自动执行；
2. 在 /etc/sysconfig/network-scripts/ifcfg-eth0 中添加一行 ETHTOOL_OPTS="speed 1000 duplex full autoneg off"。这里仅仅以 eth0 为例，其他设备号同理。


查询 ethx 网口基本设置

    ethtool ens33


网卡执行自我检测: **-t**

    sudo ethtool -t ens33


### dmesg

查看所有硬件的内容

**实时监控 dmesg 日志输出**

在某些发行版中可以使用命令 ‘tail -f /var/log/dmesg’ 来实时监控 dmesg 的日志输出。

    [root@real_2 ~]# watch "dmesg | tail -20"


### 根据进程名杀死进程 ps -ef

查看进程，并杀死特定进程 pname是进程名称

    ps -ef | grep pname | grep -v grep | awk '{print $2}' | xarg skill -9

    ps -T -p <pid> --查看特定进程下的线程运行情况

    top -H -p <pid> --查看特定进程下的线程运行情况


### 字符分割+绝对值比较

    set -x

    DEVID=$1
    STIME=$2

    if [ $# != 2 ];then
        echo "usage: sh Sphc2sys.sh /dev/ptp0 1"
        exit
    fi

    while true
    do

    HWTIME=`phc_ctl $DEVID get`
    SWTIME=`date +%s`

    hwarr=(${HWTIME// / })
    itss=(${hwarr[4]//./ })
    difftime=$[ ${itss[0]} - $SWTIME ]

    if [ ${difftime#-} > 1 ];then
        date -s "${hwarr[6]} ${hwarr[7]} ${hwarr[8]} ${hwarr[9]} ${hwarr[10]}"
    fi

    sleep $STIME

    done

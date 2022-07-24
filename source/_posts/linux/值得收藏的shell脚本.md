---
title: 值得收藏的shell脚本
date: 2021-09-23 16:28:03
categories: linux
---
**1\. 轮询检测Apache状态并启用钉钉报警**

```
#!/bin/bash

shell_user="root"
shell_domain="apache"

shell_list="/root/ip_list"
shell_row=`cat $shell_list |wc -l`

function trans_text(){
text=$1

curl 'https://oapi.dingtalk.com/robot/send?access_token=b4fcf5862088a1bc7f2bf66a' -H'Content-Type: application/json' -d'{      #指定钉钉机器人hook地址
            "msgtype": "text", 
            "text": {
            "content": "'"$text"'"
        }, 
}'
}

function apache_check_80(){
    ip=$1
    URL="http://$ip/index.html"
    HTTP_CODE=`curl -o /dev/null -s -w "%{http_code}" "${URL}"`

    if [ $HTTP_CODE != 200 ]
        then
            trans_text "
            =================================================================
                                \n $ip Apache 服务器状态异常，网页返回码: '"$HTTP_CODE"' 请及时处理 ! \n
                                ================================================================= \n"
    fi
}

while true
do

shell_list="/root/ip_list"
shell_row=`cat $shell_list |wc -l`
    for temp in `seq 1 $shell_row`
    do
            Ip_Addr=`cat $shell_list |head -n $temp |tail -n 1`
        apache_check_80 $Ip_Addr
    done

    sleep 10
done
```

**2\. 一台监控主机，一台被监控主机。被监控主机分区使用率大于80%，就发告警邮件。放到crontab里面，每10分钟执行一次**

```
#！/bin/bash

FSMAX="80"     
remote_user='root'  
remote_ip=(IP地址列表)  
ip_num='0'      

while [ "$ip_num" -le "$(expr ${#remote_ip[@]} -l)"]  
do  
read_num='1'           
        ssh "$remote_user"@"${remote_ip[$ip_num]}"  df -h > /tmp/diskcheck_tmp
        grep '^/dev/*'  /tmp/diskcheck_tmp | awk '{print $5}'|sed 's/\%//g'  > /tmp/diskcheck_num_tmp

        while [ "$read_num" -le $(wc -l < /tmp/diskcheck_num_tmp) ]  
        do
                size=$(sed -n "$read_num" 'p'  /tmp/diskcheck_num_tmp)
                if [ "size" -gt "$FSMAX" ]
                then                       
                        $(grep '^/dev/*'  /tmp/diskcheck_tmp |sed -n $read_num'p'  > /tmp/disk_check_mail)
                        $(echo ${remote_ip[$ip_num]}) >> /tmp/disk_check_mail)
                        $(mail  -s "diskcheck_alert"  admin  <  /tmp/disk_check_mail)
                fi                         

                read_num=$(expr  $read_num + 1)
        done               

        ip_num=$(expr  $ip_num + 1)
done
```
**3.监控主机的磁盘空间,当使用空间超过90％就通过发mail来发警告**

```

#!/bin/bash 
#monitor available disk space 
#提取本服务器的IP地址信息 
IP=`ifconfig eth0 | grep "inet addr" | cut -f 2 -d ":" | cut -f 1 -d " "`      
SPACE=` df -hP | awk '{print int($5)}'`  
if [ $SPACE -ge 90 ]  
then  
  echo "$IP 服务器 磁盘空间 使用率已经超过90%，请及时处理。"|mail -s "$IP 服务器硬盘告警，
  公众号：Geek安全"   fty89@163.com  
fi
```



**4\. 自动ftp上传**


```

#! /bin/bash

ftp -n << END_FTP  
open 192.168.1.22  
user  test testing      //用户名test  密码：testing  
binary  
prompt  off    //关闭提示  
mput   files     //上传files文件  
close  
bye  
END_FTP
```



**5.mysqlbak.sh备份数据库目录脚本**


```

>#!/bin/bash

DAY=`date +%Y%m%d`
SIZE=`du -sh /var/lib/mysql`
echo "Date: $DAY" >> /tmp/dbinfo.txt
echo "Data Size: $SIZE" >> /tmp/dbinfo.txt
cd /opt/dbbak &> /dev/null || mkdir /opt/dbbak
tar zcf /opt/dbbak/mysqlbak-${DAY}.tar.gz /var/lib/mysql /tmp/dbinfo.txt &> /dev/null
rm -f /tmp/dbinfo.txt

crontab-e
55 23 */3 * * /opt/dbbak/dbbak.sh
```



[图片上传失败...(image-4f1eeb-1632412285686)] 

**6.打印彩虹**


```

declare -a ary

for i in `seq 40 49`
do

    ary[$i]=" "
    echo -en "\e[$i;5m ${ary[@]}\e[;0m"

done

declare -a ary
for s in `seq 1 10000`
do
    for i in `seq 40 49`
    do
        ary[$i]=" "
        echo -en "\e[$i;5m ${ary[@]}\e[;0m"    
    done
done
```



**7.打印菱形**


```

#!/bin/bash

for (( i = 1; i < 12; i++))
do
    if [[ $i -le 6 ]]
    then
        for ((j = $((12-i)); j > i; j--))
        do
            echo -n " "
        done

        for ((m = 1; m <= $((2*i-1)); m++))
        do
             echo -n "* "
        done
            echo ""
#*****************************************************************************
    elif [[ $i -gt 6 ]]
    then
        n=$((12-i))
        for ((j = $((12-n)); j > n; j--))
        do
            echo -n " "
        done

        for ((m = 1; m <= $((2*n-1)); m++))
        do
                        echo -n "* "
        done
            echo ""
    fi

done
```



**8.expect实现远程登陆自动交互**


```

#!/usr/bin/expect -f

set ipaddress [lindex $argv 0]

set passwd [lindex $argv 1]

set timeout 30

spawn ssh-copy-id root@$ipaddress

expect {

"yes/no" { send "yes\r";exp_continue }

"password:" { send "$passwd\r" }

}

#expect "*from*"

#send "mkdir -p ./tmp/testfile\r"

#send "exit\r"

#expect "#" #i# 命令运行完, 你要期待一个结果, 结果就是返回shell提示符了(是# 或者$)
```



**9.http心跳检测**


```

#!/bin/bash

function MyInstall
{
        if ! rpm -qa |grep -q "^$1"
        then

                yum install $1
                if [ $? -eq 0 ]
                then
                        echo -e "$i install is ok\n"
                else
                        echo -e "$1 install no\n"
                fi
        else
                echo -e "yi an zhuang ! \n"
        fi
}

for ins in mysql php httpd
do
        MyInstall $ins
done
```



**12.shell实现插入排序**


```

#!/bin/bash

declare -a array

for i in `seq 1 10`
do
    array[$i]=$RANDOM

done

echo -e "Array_1:  ${array[@]}"

for (( x=1;x<=9;x++ ))
do
    for(( y=1;y<=9;y++ ))
    do
        if [ ${array[$y]} -gt ${array[$y+1]} ]
        then
            temp=${array[$y]}
            array[$y]=${array[$y+1]}
            array[$y+1]=$temp
        fi

    done

done

echo -e "Array_2:  ${array[@]}"
```



**13.bash实现动态进度条**


```

#!/bin/bash
i=0
bar=''
index=0
arr=( "|" "/" "-" "\\" )

while [ $i -le 100 ]
do
    let index=index%4
    printf "[%-100s][%d%%][\e[43;46;1m%c\e[0m]\r" "$bar" "$i" "${arr[$index]}"
    let i++
    let index++
    usleep 30000
    bar+='#'
    clear
done

printf "\n"
```



**14\. 根据文件内容创建账号**


```

#!/bin/bash

for Uname in `cat /root/useradd.txt |gawk '{print $1}'`
do

                id $Uname &> /dev/null
                if [ $? -eq 0 ]
                then
                        echo -e "这个账号已存在!来源：微信公众号【网络技术干货圈】"
                        continue
                fi
        for Upasswd in `cat /root/useradd.txt |gawk '{print $2}'`
        do
                useradd $Uname &> /dev/null
                echo "$Upasswd" |passwd --stdin $Uname &> /dev/null
                if [ $? -eq 0 ]
                then
                        echo -e "账号创建成功!"
                else
                        echo -e "创建失败!"
                fi

        done

done
```



**15\. 红色进度条**


```

#!/bin/bash

declare -a ary

for i in `seq 0 20`
do

    ary[$i]=" "
    echo -en "\e[41;5m ${ary[@]}\e[;0m"
    sleep 1

done
```



**16.监控服务器网卡流量**


```

#!/bin/bash
#network
#Mike.Xu
while : ; do
speedtime='date +%m"-"%d" "%k":"%M'
speedday='date +%m"-"%d'
speedrx_before='ifconfig eth0|sed -n "8"p|awk '{print $2}'|cut -c7-'
speedtx_before='ifconfig eth0|sed -n "8"p|awk '{print $6}'|cut -c7-'
sleep 2
speedrx_after='ifconfig eth0|sed -n "8"p|awk '{print $2}'|cut -c7-'
speedtx_after='ifconfig eth0|sed -n "8"p|awk '{print $6}'|cut -c7-'
speedrx_result=$[(speedrx_after-speedrx_before)/256]
speedtx_result=$[(speedtx_after-speedtx_before)/256]
echo"$speedday$speedtime Now_In_Speed: "$speedrx_result"kbps Now_OUt_Speed: "$speedtx_result"kbps"
sleep 2
done
```


**17\. 检测CPU剩余百分比**


```

#!/bin/bash

#Inspect CPU

#Sun Jul 31 17:25:41 CST 2016

PATH=/usr/local/bin:/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/sbin:/home/wl/bin
export PATH

TERM=linux
export TERM

CpuResult=$(top -bn 1 | grep "Cpu" | awk '{print $5}' | sed 's/\..*$//g')

if [[ $CpuResult < 20 ]];then
  echo "CPU WARNING : $CpuResult" > /service/script/.cpu_in.txt
  top -bn 1 >> /service/script./cpu_in.txt
  mail -s "Inspcet CPU" wl < /service/script/.cpu_in.txt
fi
```



**18.检测磁盘剩余空间**


```

#!/bin/bash

#Insepct Harddisk , If the remaining space is more than 80%, the message is sent to the wl

#Tue Aug  2 09:45:56 CST 2016

PATH=/usr/local/bin:/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/sbin:/home/wl/bin

export PATH

for RemainingSpace in $(df -h | awk '{print $5}' | grep -v 'Use' | sed -e 's/[%]//g')
do
  if [[ $RemainingSpace > 80 ]];then
    echo -e "$RemainingSpace"
    echo -e "$(df -h | grep $RemainingSpace)" > /service/script/.HarddiskWarning
    mail -s "disk Warning" wl < /service/script/.HarddiskWarning
  fi
done
```



**19\. bash-实现检测apache状态并钉钉报警**


```

#!/bin/bash

function trans_text(){
    text=$1
curl 'https://oapi.dingtalk.com/robot/send?access_token=b4fcf5862088a1bc7f2bf66aea051869e62ff5879fa0e0fddb0db9b1494781c2' -H'Content-Type: application/json' -d'
{
    "msgtype": "text", 
    "text": {
        "content": "'"$text"'"
    }, 
}'
}

function desk_check(){

    dftype=$1
    shell_row=`df |wc -l`

for i in `seq 2 $shell_row`
do

    temp=(`df -h |head -n $i |tail -n 1 |awk '{print $5 "\t" $6}'`)
    disk="`echo ${temp[0]} |cut -d "%" -f 1`"
    name="${temp[1]}"
    hostname=`hostname`
    IP=`ifconfig |grep -v "127.0.0.1" |grep "inet addr:" |sed 's/^.*inet addr://g'|sed 's/ Bcas..*$//g'`
    #echo -e "$disk     $name"
    Dat=`date "+%F %T"`

    if [ $disk -ge $dftype ]
    then
        echo  "
                            ======================== \n
                    >磁盘分区异常< \n
               主机名: $hostname \n        
               IP地址: $IP \n
                分区名: $name \n
               使用率: $disk %\n 
               发生时间: $Dat \n
                          ========================= \n"
    fi
done
}

function apache_check(){
    url=$1
URL="http://$url/"
HTTP_CODE=`curl -o /dev/null -s -w "%{http_code}" "${URL}"`

if [ $HTTP_CODE != 200 ]
    then
        echo  "
                           ======================== \n
                   >Apache服务异常<
                           主机名: $hostname \n         
                           IP地址: $IP \n
                           返回代码: $HTTP_CODE \n
                           发生时间: $Dat \n
                          ========================= \n"                        
fi
}

while true
do
    desk_check 10
    apache_check 127.0.0.1

    sleep 10
done
```



**20.内存检测**


```

#!/bin/bash

#Inspect Memory : If the memory is less than 500 , then send mail to wl

#Tue Aug  2 09:13:43 CST 2016

PATH=/usr/local/bin:/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/sbin:/home/wl/bin

export PATH

MEM=$(free -m | grep "Mem" | awk '{print $4}')

if [[ MEM < 500 ]];then
  echo -e "Memory Warning : Memory free $MEM" > /service/script/.MemoryWarning
  mail -s "Memory Warning" wl < /service/script/.MemoryWarning
fi
```



**21.剩余inode检测**


```

#!/bin/bash

#Inspcet Inode : If the free INODE is less than 200, the message is sent to the wl

#Tue Aug  2 10:21:29 CST 2016

PATH=/usr/local/bin:/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/sbin:/home/wl/bin

export PATH

for FreeInode in $(df -i | grep -v "Filesystem" | awk '{print $4}')
do
  if [[ $FreeInode < 200 ]];then
    echo -e "$(df -i | grep "$FreeInode")" > /service/script/.FreeInode
    mail -s "FreeInode Warning" wl < /service/script/.FreeInode
  fi
done
```



**22.判断哪些用户登陆了系统**


```

#!/bin/bash

declare -i count=0

while true;do

        if who |grep -q -E "^wang"
        then
                echo -e "用户wang 登陆了系统\n 这是第$count 次!威信公众浩：wljsghq"
                break
        else
                let count++
        fi

        sleep 3
done
~    

示例：找出UID为偶数的所有用户，显示其用户名和ID号；

#!/bin/bash
while read line; do
    userid=$(echo $line | cut -d: -f3)
    if [ $[$userid%2] -eq 0 ]; then
echo $line | cut -d: -f1,3
    fi
done < /etc/passwd
```



**23.批量创建账号**

```

#!/bin/bash

sum=1

while [ $sum -le 30 ]
do
    if [ $sum -le 9 ]
    then
        user="user_0$sum"
    else
        user="user_$sum"
    fi

        useradd $user
        echo "123456" |passwd --stdin $user
        chage -d 0 $user
        let sum=sum+1

done
```



**24.批量扫面存活**


```

#!/bin/bash
#By:lyshark

#nmap 192.168.22.0/24>ip

MAC=`cat ip |awk '$1 == "MAC" && $NF == "(VMware)"{print $3}'`

for i in `seq 1 20`

do

temp=`echo ${MAC[@]} |awk '{print $i}'`

IP=`cat /ip |grep  -B5 $temp |grep "Nmap scan"|awk '{print $5}'`

    echo $IP |awk '{print $1}'
done
```



**25.正则匹配IP**


```

^[0-9]{0,2}|^1[0-9]{0,2}|^2[0-5]{0,2}

 egrep "(^[0-9]{1,2}|^1[0-9]{0,2}|^2[0-5]{0,2})\.([0-9]{1,2}|1[0-9]{0,2}|2[0-5]{0,2})\.([0-9]{1,2}|1[0-9]{0,2}|2[0-5]{0,2})\.([0-9]{1,2}|1[0-9]{0,2}|2[0-5]{0,2})$"

([0-9]{1,2}|1[0-9]{0,2}|2[0-5]{0,2})
([0-9]{1,2}|1[0-9]{0,2}|2[0-5]{0,2})
([0-9]{1,2}|1[0-9]{0,2}|2[0-5]{0,2})
([0-9]{1,2}|1[0-9]{0,2}|2[0-5]{0,2})

egrep "((25[0-5]|2[0-4][0-9]|((1[0-9]{2})|([1-9]?[0-9])))\.){3}(25[0-5]|2[0-4][0-9]|((1[0-9]{2})|([1-9]?[0-9])))"

ls |egrep "((25[0-5]|2[0-4][0-9]|((1[0-9]{2})|([1-9]?[0-9])))\.){3}(25[0-5]|2[0-4][0-9]|((1[0-9]{2})|([1-9]?[0-9])$))"
```



**26.正则匹配邮箱**


```

egrep "^[0-9a-zA-Z][0-9a-zA-Z_]{1,16}[0-9a-zA-Z]\@[0-9a-zA-Z-]*([0-9a-zA-Z])?\.(com|com.cn|net|org|cn)$" rui

ls |egrep "^(([1-9]|[1-9][0-9]|1[0-9][0-9]|2[0-4][0-9]|25[0-5])\.){3}([0-9]|[1-9][0-9]|1[0-9][0-9]|2[0-4][0-9]|25[0-4])$"
```



**27.实现布片效果**


```

#!/bin/bash

function ary_go
{
    $1 $2

    for (( i=0;i<=$1;i++ ))
    do
        for (( s=0;s<=$2;s++ ))
        do
            if [ $[$i%2] == 0 ]
            then

                if [ $[$s%2] == 0 ]
                then
                    echo -en "  "
                else
                    echo -en "\e[;44m  \e[;m"
                fi
            else
                                                if [ $[$s%2] == 0 ]
                                then
                                        echo -en "\e[;42m  \e[;m"
                                else
                                        echo -en "  "
                                fi

            fi

        done
            echo 

    done

}

ary_go 25 50
```



**28.剔除白名单以外的用户**


```

#!/bin/bash
w | awk 'NR>=3 {printf $1 "\t" $2 "\t" $3 "\n"}' > /tmp/who.txt
for i in $(awk '{printf $1}' /tmp/bai.txt)
do
        k=$(egrep -v "$i" /tmp/who.txt | awk '{printf $2} "\n"' | awk '{printf $2 "\n"}')
        for j in $k
        do
                pkill -9 -t "$j"
        done
done
```



+++
title = 'Shell(md5sum\sha1sum\sha256sum) 编程杂记'
date = 2019-12-05T20:40:09+08:00
draft = true
categories = ["随笔"]
tags = ["杂记","Shell"]
+++
&nbsp;&nbsp;发现三个 Shell 命令 `md5sum`、`sha1sum`、`sha256sum`，发现用于文件下载辨别还是很有用处的。网络上充斥着许许多多魔改的程序，有些改动可能是善意、玩闹的，同时也包含一些改动是恶意的！我们无法保证所下载的资源没有被修改，更不敢保证他们都没有恶意。因此可以使用 [md5][] 、 [sha1][]、[sha256][] 等一系列[密码散列函数][]进行验证。   这里我们不过多的介绍 Shell 命令的具体使用方法，感兴趣的朋友可以自行搜索或者[`* -help`](https://baike.baidu.com/item/正则表达式/1700215 "这里的‘*’是通配符，表示所有的字符，你要直接输入新手也就罢了，不是的话我管你叫大哥")。预期的效果类似与 `mkdir -p /opt/demo/demo` 简单的花了半个小时进行查找资料发现了有三种方式可以在执行命令的时候动态的改变参数。
- 在执行 Shell 脚本的后面直接添加字符，例如创建一个'demo.sh'
```shellcode
#!/bin/bash
echo $1
echo $2$3
```
<!-- more -->
```
$ chmod +x demo.sh
$ ./demo.sh


$ ./demo.sh 4 5 6
4
56
$ ./demo.sh 14 5
14
5
$ ./demo.sh 14
14

$ ./demo.sh 14 4 14 14
14
414
```
- 使用 `getopts` 命令（[获知途径](https://blog.csdn.net/sinat_36521655/article/details/79296181)）
```shellcode
#!/bin/bash
while getopts ":a:b:c:" opt
do
    case $opt in
        a)
        echo "参数a的值$OPTARG"
        ;;
        b)
        echo "参数b的值$OPTARG"
        ;;
        c)
        echo "参数c的值$OPTARG"
        ;;
        ?)
        echo "未知参数"
        exit 1;;
    esac
done
```
```
$ ./demo.sh -a 1 -b 2 -c 3
参数a的值1
参数b的值2
参数c的值3
$ ./demo.sh -a 1
参数a的值1
$ ./demo.sh -a 11 -d 2
参数a的值11
未知参数
```
- 使用`read`命令（[获知途径](https://rangaofei.github.io/2018/02/10/shell变量/)）
```shellcode
#!/bin/bash
echo "请根据下面的提示输入你想要设置的值"
read -p "var1= " var1
read -p "var2= " var2
read -p "你已经输入了所有的变量值，是否立即打印？y/n:" print
if [ ${print}x == yx ];then
    echo "var1= $var1"
    echo "var2= $var2"
elif [ ${print}x == nx ];then
    echo "complete"
else
    echo unknow command
fi
```
```
$ ./demo.sh
请根据下面的提示输入你想要设置的值
var1= 9
var2= 8
你已经输入了所有的变量值，是否立即打印？y/n:y
var1= 9
var2= 8

```

ps: 出于惰性并没有详细的跟进以及解剖~~.
最后的阶段性偷懒方案
```shellcode
#!/bin/bash
Var1=$1
Var2=$2
FilePath=$Var1
# echo $FilePath

md5=`md5sum "$FilePath"`
#unset FilePath
echo "md5:${md5}"
#echo "md5:$(md5sum "${FilePath}")"
echo "sha1:$(sha1sum "${FilePath}")"
echo "sha2:$(sha256sum "${FilePath}")"
```


[md5]: https://baike.baidu.com/item/MD5/212708
[sha1]: https://baike.baidu.com/item/SHA-1/1699692
[sha256]: https://baike.baidu.com/item/sha256/9726070
[密码散列函数]: https://baike.baidu.com/item/%E5%AF%86%E7%A0%81%E6%95%A3%E5%88%97%E5%87%BD%E6%95%B0

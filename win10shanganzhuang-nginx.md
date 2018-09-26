# win10上安装nginx

由于公司让我给福特项目搭一个WebService服务器基于nginx实现负载才开始接触nginx，在这里先分享一下nginx的安装

另参考：http://blog.163.com/njut\_wangjian/blog/static/1657964252013327103716818/

我本地环境：win10-64位。

1. 到nginx官网上下载相应的安装包，[https://www.cnblogs.com/qfb620/p/5508468.html](http://nginx.org/en/download.html)；

![](https://images2015.cnblogs.com/blog/30319/201605/30319-20160519135514544-2082693869.png)



下载进行解压，将解压后的文件放到自己心仪的目录下，我的解压文件放在了d盘根目录下，如下图所示：



![](https://images2015.cnblogs.com/blog/30319/201605/30319-20160519135102466-209954266.png)

![](https://images2015.cnblogs.com/blog/30319/201605/30319-20160519135137685-308263093.png)

 进入window的cmd窗口，输入如下图所示的命令，进入到nginx目录（D:/nginx-1.8.1），使用“**start nginx.exe**”进行nginx的安装，如下图所示：



![](https://images2015.cnblogs.com/blog/30319/201605/30319-20160519135601216-1124784200.png)

安装成功后，在“任务管理器”中会看到“nginx.exe”进程，如下图所示：



![](https://images2015.cnblogs.com/blog/30319/201605/30319-20160519135700466-41397978.png)

在浏览器地址栏输入：127.0.0.1，会看到如下图所示的nginx欢迎界面



![](https://images2015.cnblogs.com/blog/30319/201605/30319-20160519141328169-60480502.png)



如果发现进程中并没有，那么说明你安装失败，你可以到你的D:\nginx-1.8.1目录下的logs文件夹下的error下查看，

如果发现里面写着：

![](https://images2015.cnblogs.com/blog/30319/201605/30319-20160519140004951-212419701.png)

说明你的80端口被占用了，或是cmd命令进入dos下执行：netstat -aon \| findstr :80 查看80端口是否被占用，如果占用，那么你需要修改注册表，如下步骤：

1、打开注册表：regedit

2、找到：HKEY\_LOCAL\_MACHINE\SYSTEM\CurrentControlSet\services\HTTP

3、找到一个REG\_DWORD类型的项Start，将其改为0

4、重启系统，System进程不会占用80端口

此时此刻，你可以再次执行

相应的命令：**start nginx.exe**命令了。

**nginx.exe -s stop //停止nginx**

**nginx.exe -s reload //重新加载nginx**

**nginx.exe -s quit //退出nginx**

注：以上的命令中，.exe可以去掉

相应命令的操作如下图所示:

![](https://images2015.cnblogs.com/blog/30319/201605/30319-20160519141342154-1669071808.png)

2. nginx配置文件

nginx配置文件为nginx.conf，相应操作如下图所示：

![](https://images2015.cnblogs.com/blog/30319/201605/30319-20160519141434263-248448966.png)

nginx配置文件解析：

[http://www.2cto.com/os/201212/176520.html](http://www.2cto.com/os/201212/176520.html)

 --------------------- 本文来自 不死的鱼 的CSDN 博客 ，全文地址请点击：https://blog.csdn.net/weixin\_40151334/article/details/80681173?utm\_source=copy


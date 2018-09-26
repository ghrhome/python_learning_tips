# Nginx配置文件nginx.conf中文详解（总结）

PS：Nginx使用有两三年了，现在经常碰到有新用户问一些很基本的问题，我也没时间一一回答，今天下午花了点时间，结合自己的使用经验，把Nginx的主要配置参数说明分享一下，也参考了一些网络的内容，这篇是目前最完整的Nginx配置参数中文说明了。更详细的模块参数请参考：http://wiki.nginx.org/Main





\#定义Nginx运行的用户和用户组

user www www;



\#nginx进程数，建议设置为等于CPU总核心数。

worker\_processes 8;



\#全局错误日志定义类型，\[ debug \| info \| notice \| warn \| error \| crit \]

error\_log ar/loginx/error.log info;



\#进程文件

pid ar/runinx.pid;



\#一个nginx进程打开的最多文件描述符数目，理论值应该是最多打开文件数（[系统](https://www.2cto.com/os/)的值ulimit -n）与nginx进程数相除，但是nginx分配请求并不均匀，所以建议与ulimit -n的值保持一致。

worker\_rlimit\_nofile 65535;



\#工作模式与连接数上限

events

{

\#参考事件模型，use \[ kqueue \| rtsig \| epoll \| /dev/poll \| select \| poll \]; epoll模型是[Linux](https://www.2cto.com/os/linux/)2.6以上版本内核中的高性能网络I/O模型，如果跑在FreeBSD上面，就用kqueue模型。

use epoll;

\#单个进程最大连接数（最大连接数=连接数\*进程数）

worker\_connections 65535;

}



\#设定http服务器

http

{

include mime.types; \#文件扩展名与文件类型映射表

default\_type application/octet-stream; \#默认文件类型

\#charset utf-8; \#默认编码

server\_names\_hash\_bucket\_size 128; \#服务器名字的hash表大小

client\_header\_buffer\_size 32k; \#上传文件大小限制

large\_client\_header\_buffers 4 64k; \#设定请求缓

client\_max\_body\_size 8m; \#设定请求缓

sendfile on; \#开启高效文件传输模式，sendfile指令指定nginx是否调用sendfile函数来输出文件，对于普通应用设为 on，如果用来进行下载等应用磁盘IO重负载应用，可设置为off，以平衡磁盘与网络I/O处理速度，降低系统的负载。注意：如果图片显示不正常把这个改成off。

autoindex on; \#开启目录列表访问，合适下载服务器，默认关闭。

tcp\_nopush on; \#防止网络阻塞

tcp\_nodelay on; \#防止网络阻塞

keepalive\_timeout 120; \#长连接超时时间，单位是秒



\#FastCGI相关参数是为了改善网站的性能：减少资源占用，提高访问速度。下面参数看字面意思都能理解。

fastcgi\_connect\_timeout 300;

fastcgi\_send\_timeout 300;

fastcgi\_read\_timeout 300;

fastcgi\_buffer\_size 64k;

fastcgi\_buffers 4 64k;

fastcgi\_busy\_buffers\_size 128k;

fastcgi\_temp\_file\_write\_size 128k;



\#gzip模块设置

gzip on; \#开启gzip压缩输出

gzip\_min\_length 1k; \#最小压缩文件大小

gzip\_buffers 4 16k; \#压缩缓冲区

gzip\_http\_version 1.0; \#压缩版本（默认1.1，前端如果是squid2.5请使用1.0）

gzip\_comp\_level 2; \#压缩等级

gzip\_types text/plain application/x-javascript text/css application/xml;

\#压缩类型，默认就已经包含textml，所以下面就不用再写了，写上去也不会有问题，但是会有一个warn。

gzip\_vary on;

\#limit\_zone crawler $binary\_remote\_addr 10m; \#开启限制IP连接数的时候需要使用



upstream blog.ha97.com {

\#upstream的负载均衡，weight是权重，可以根据机器配置定义权重。weigth参数表示权值，权值越高被分配到的几率越大。

server 192.168.80.121:80 weight=3;

server 192.168.80.122:80 weight=2;

server 192.168.80.123:80 weight=3;

}



\#虚拟主机的配置

server

{

\#监听端口

listen 80;

\#域名可以有多个，用空格隔开

server\_name www.ha97.com ha97.com;

index index.html index.htm index.php;

root /data/www/ha97;

location ~ .\*.\(php\|php5\)?$

{

fastcgi\_pass 127.0.0.1:9000;

fastcgi\_index index.php;

include fastcgi.conf;

}

\#图片缓存时间设置

location ~ .\*.\(gif\|jpg\|jpeg\|png\|bmp\|swf\)$

{

expires 10d;

}

\#JS和[CSS](https://www.2cto.com/kf/qianduan/css/)缓存时间设置

location ~ .\*.\(js\|css\)?$

{

expires 1h;

}

\#日志格式设定

log\_format access '$remote\_addr - $remote\_user \[$time\_local\] "$request" '

'$status $body\_bytes\_sent "$http\_referer" '

'"$http\_user\_agent" $http\_x\_forwarded\_for';

\#定义本虚拟主机的访问日志

access\_log ar/loginx/ha97access.log access;



\#对 "/" 启用反向代理

location / {

proxy\_pass http://127.0.0.1:88;

proxy\_redirect off;

proxy\_set\_header X-Real-IP $remote\_addr;

\#后端的Web服务器可以通过X-Forwarded-For获取用户真实IP

proxy\_set\_header X-Forwarded-For $proxy\_add\_x\_forwarded\_for;

\#以下是一些反向代理的配置，可选。

proxy\_set\_header Host $host;

client\_max\_body\_size 10m; \#允许客户端请求的最大单文件字节数

client\_body\_buffer\_size 128k; \#缓冲区代理缓冲用户端请求的最大字节数，

proxy\_connect\_timeout 90; \#nginx跟后端服务器连接超时时间\(代理连接超时\)

proxy\_send\_timeout 90; \#后端服务器数据回传时间\(代理发送超时\)

proxy\_read\_timeout 90; \#连接成功后，后端服务器响应时间\(代理接收超时\)

proxy\_buffer\_size 4k; \#设置代理服务器（nginx）保存用户头信息的缓冲区大小

proxy\_buffers 4 32k; \#proxy\_buffers缓冲区，网页平均在32k以下的设置

proxy\_busy\_buffers\_size 64k; \#高负荷下缓冲大小（proxy\_buffers\*2）

proxy\_temp\_file\_write\_size 64k;

\#设定缓存文件夹大小，大于这个值，将从upstream服务器传

}



\#设定查看Nginx状态的地址

location /NginxStatus {

stub\_status on;

access\_log on;

auth\_basic "NginxStatus";

auth\_basic\_user\_file confpasswd;

\#htpasswd文件的内容可以用apache提供的htpasswd工具来产生。

}



\#本地动静分离反向代理配置

\#所有[jsp](https://www.2cto.com/kf/web/jsp/)的页面均交由tomcat或resin处理

location ~ .\(jsp\|jspx\|do\)?$ {

proxy\_set\_header Host $host;

proxy\_set\_header X-Real-IP $remote\_addr;

proxy\_set\_header X-Forwarded-For $proxy\_add\_x\_forwarded\_for;

proxy\_pass http://127.0.0.1:8080;

}

\#所有静态文件由nginx直接读取不经过tomcat或resin

location ~ .\*.\(htm\|html\|gif\|jpg\|jpeg\|png\|bmp\|swf\|ioc\|rar\|zip\|txt\|flv\|mid\|doc\|ppt\|pdf\|xls\|mp3\|wma\)$

{ expires 15d; }

location ~ .\*.\(js\|css\)?$

{ expires 1h; }

}

}



更详细的模块参数请参考：http://wiki.nginx.org/Main


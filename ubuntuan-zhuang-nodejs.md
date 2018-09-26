## 基于Linux 发布的 Debian 和 Ubuntu 系统 {#header-linux-debian-ubuntu}

同时包括**Linux Mint**,**Linux Mint Debian Edition \(LMDE\)**,**elementaryOS**,**bash on Windows**和其它系统等。

你同时也可以从[NodeSource](https://nodesource.com/)获取 Debian 和 Ubuntu 的 Node.js 二进制分发库（在此之前则是通过[Chris Lea's](https://github.com/chrislea)Launchpad PPA）。 有关于这些库和脚本代码方面的分发支持，你可以在[nodesource/distributions](https://github.com/nodesource/distributions)找到相关信息。

**注意：**如果你在使用 Ubuntu Precise 或 Debian Wheezy 系统，你可能需要阅读相关信息：[在更古老的发行版系统上运行大于 6.0 版的 Nodejs](https://github.com/nodesource/distributions/blob/master/OLDER_DISTROS.md)。

```
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
sudo apt-get install -y nodejs
```

而在 Node.js 10 版本中：

```
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
sudo apt-get install -y nodejs
```

_**可选**_：安装构建工具

为构建和安装本地化的 npm 插件，你或许需要安装构建工具：

```
sudo apt-get install -y build-essential
```

**支持的架构：**

* **i386**
  \(32 位\)
* **amd64**
  \(64 位\)
* **armhf**
  \(ARM 32 位 hard-float, ARMv7 和以上的
  _arm-linux-gnueabihf_
  \)

**Ubuntu 中支持的版本：**

* **Ubuntu 14.04 LTS**
  \(Trusty Tahr\)
* **Ubuntu 16.04 LTS**
  \(Xenial Xerus\)

**Debian 中支持的版本：**

* **Debian 8**
  \(Jessie，上一个稳定版\)
* **Debian 9 / stable**
  \(当前稳定版\)
* **Debian testing**
  \(目前仍在测试的下一个稳定版\)
* **Debian unstable**
  \(依然在不断迭代的测试版\)

您也可以在[官网发布版](http://packages.debian.org/search?searchon=names&keywords=nodejs)获取针对 Debian Sid（非稳定版）、Jessie（测试版）和 Wheezy \(wheezy补丁版\) 的 Nodejs 程序包。它仅仅安装一个`nodejs`二进制程序包。

[nodejs-legacy 程序包](http://packages.debian.org/search?searchon=names&keywords=nodejs-legacy)安装一个被许多模块使用、并使它们能够正常构建和运行的`node`符号链接。 注：官方发布的 Node.js 模块库并不需要此链接。


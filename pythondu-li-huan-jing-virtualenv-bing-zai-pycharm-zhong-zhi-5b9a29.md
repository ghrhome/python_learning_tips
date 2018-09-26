# python独立环境virtualenv\(并在pycharm中指定\)

> 本机环境：
>
> mac Sierra10.12.1 \(16B2657\)
>
> Python 2.7.10 \(default, Jul 30 2016, 18:31:42\)
>
> pip 9.0.1
>
> IDE:pycharm

### 两版本mac自带 直接加版本号

python就是2的版本  
 pip是2的版本

python3就是3的版本  
 pip3是3的版本

### virtualenv介绍

每个应用需要的运行环境经常不同（包的版本／python版本等），所以需要用virtualenv创建一套**属于这个程序的“隔离”的Python运行环境**。  
 而且系统Python环境不会被修改，保持干净。  
 可打包后移动目录，但**无法跨平台**

### 安装

pip install /Users/w/Downloads/virtualenv-15.1.0-py2.py3-none-any.whl

### 配置virtualenv的终端命令

例如，运行一个RESTful的pythonWeb程序。  
 创建该虚拟环境，要求：python2.7.10 并用pip安装所需包。

```
pip install virtualenv #先安装这个 环境切换的东西
#virtualenv -h  #查看帮助


#virtualenv venv #创建虚拟环境的目录，名为venv，它包含了Python可执行文件和拷贝过来的pip 库（能安装需要的包到虚拟环境）

#（若省略名字将不创建目录，文件均直接放在当前目录）

virtualenv -p /usr/bin/python2.7 venv# -p选项 选择使用哪一版本的Python解释器
#--no-site-packages 选项 virtualenv 将不会包括物理机python环境安装的包（这在 virtualenv 1.7及之后是默认的）

source venv/bin/activate #激活刚才创建的虚拟环境
# deactivate #有必要时可关闭虚拟环境

pip install -r requirements.txt #安装项目需要的包

#pip list #查看 当前环境中所有包及各自的版本的列表。

#创建一个 requirements.txt 文件（包含了当前环境中所有包及各自的版本的简单列表）
#pip freeze > requirements.txt

#virtualenv --relocatable env
#打包虚拟环境env 。 可以解压到本机其他目录后直接使用


deactivate
#退出虚拟环境，回到正常shell
```

### python3虚拟环境

```
virtualenv -p /usr/local/bin/python3 py3venv
```

### PyCharm设置

注意当前项目的python解释器是哪个  
**一定要设置成虚拟环境中的python**  
在PyCharm搜索：project interpreter  
即可设置

### docker下安装mongoDB

```
docker pull mongo # 拉取docker mongoDB镜像（官方）
docker run -d -p 27017:27017 mongo:latest  #跑容器 mongoDB默认端口为27017
# docker ps显示正运行容器
# docker ps -a所有容器
```

### mac下 临时使用国内源 进行安装

```
pip install requests -i http://mirrors.aliyun.com/pypi/simple
pip install requests -i https://pypi.douban.com/simple
```

### mac下 设置当前（虚拟环境目录的）国内源

在虚拟目录 venv/bin 中创建pip.conf 内容为

```
[glbal]
 trusted-host =  mirrors.aliyun.com
 index-url = http://mirrors.aliyun.com/pypi/simple
```




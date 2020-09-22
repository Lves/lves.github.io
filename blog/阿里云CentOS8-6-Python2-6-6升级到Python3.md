阿里云CentOS服务器Python环境默认2.x环境，我想体验下Python3的新特性，准备升级一下。折腾了一下午记录一下。

升级步骤：
###准备编译环境
环境如果不对的话，可能遇到各种问题。
```
yum groupinstall 'Development Tools'
yum install zlib-devel bzip2-devel  openssl-devel ncurses-devel
```
###安装
使用命令查看系统自带的Python版本
```
python -V
```
####1.0 下载对应版本的Python
```
wget https://www.python.org/ftp/python/3.6.1/Python-3.6.1.tgz
```
####2.0 解压下载的文件
```
tar -zxvf Python-3.6.1.tgz
```
####3.0 切换到源码包
```
cd Python-3.6.1
```
####4.0 配置指定Python的安装目录
```
./configure --prefix=/usr/local/python3
```



####5.0 编译和安装Python
```
make && make install
```

####6.0 备份原有的老版本Python
```
mv /usr/bin/python /usr/bin/python2.6.6
```

####7.0 创建软链接指向
```
ln -s /usr/local/python3/bin/python3  /usr/bin/python
```

到这Python就升级完了，可以用`python -V`命令查看Python版本了。

> 升级玩Python3后我们再使用yum命令，发现已经不能使用了，处理方法也很简单
```
vim /usr/bin/yum
```

把文件头部的`#!/usr/bin/python`改成老版本的`#!/usr/bin/python2.6.6`。




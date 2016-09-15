1、下载python

官网 http://www.python.org/ 下载python-3.3.4.tgz

不管是浏览器另存为下载还是shell命令下载，反正是下载到本地某个目录下然后解压出来，再编译安装，最后配置环境变量ok，本人本地下载目录是“~/下载”这个目录。

注意：shell命令下载为 # wget http://www.python.org/ftp/python/3.3.4/Python-3.3.4.tgz

CentOS 6.4下就会自动下载。

2、解压Python-3.3.4.tgz

解压命令： # tar -xzvf Python-3.3.4.tgz

注意：由于是.tgz后缀的文件，参数选择是-xzvf，若是其他格式的压缩文件，参数会有不同

没有特别指定解压到某个目录的话默认解压到同级目录下，这里方便就解压在下载目录里。

3、编译安装 Python-3.3.4

进入解压后的目录,# cd ~/下载/python-3.3.4

设置一会将python安装到自己设定的目录中去，# ./configure --prefix=/usr/local/python-3.3

注意：/usr/local/python-3.3 就是自己创建的目录。

编译命令 # make

... 编译执行中... 等待

安装命令 # make install

4.change the default python PATH

 [frank@frankvpc bin]$ ls -l |grep python
-rwxr-xr-x.   1 root root       11216 12月  1 2015 abrt-action-analyze-python
lrwxrwxrwx.   1 root root           7 9月  13 18:23 python -> python2
lrwxrwxrwx.   1 root root           9 9月  13 18:23 python2 -> python2.7
-rwxr-xr-x.   1 root root        7136 8月  18 23:59 python2.7
-rwxr-xr-x.   1 root root        1835 8月  18 23:58 python2.7-config
lrwxrwxrwx.   1 root root          16 9月  14 18:38 python2-config -> python2.7-config
lrwxrwxrwx.   1 root root          14 9月  14 18:38 python-config -> python2-config

==remove old link==
[frank@frankvpc bin]$ sudo rm python
[frank@frankvpc bin]$ sudo rm python-config

==create new link==
[frank@frankvpc bin]$ sudo ln -s /usr/local/bin/python3.5.2/bin/python3.5 /usr/bin/python
[frank@frankvpc bin]$ sudo ln -s /usr/local/bin/python3.5.2/bin/python3-config /usr/bin/python-config

5. Add environment variable PATH
[frank@frankvpc bin]$ PATH="$PATH":/usr/local/bin/python3.5.2/bin
[frank@frankvpc bin]$ echo $PATH
/usr/local/bin:/usr/local/sbin:/usr/bin:/usr/sbin:/bin:/sbin:/home/frank/.local/bin:/home/frank/bin:/usr/local/bin/python3.5.2/bin
[frank@frankvpc bin]$ 

6. 进行更改后，yum果然无法运行了。修改/usr/bin/yum文件，将第一行的
#!/usr/bin/python
中的python改为系统原有的python版本，我的如下：
#!/usr/bin/python2
到此完毕。
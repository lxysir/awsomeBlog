CentOS安装Sublime Text 3
1) 安装ST3

# 建个目录
mkdir -p /opt/Backup
cd /opt/Backup

# 下载相应的tarball: http://www.sublimetext.com/3
wget http://[st3.tar.bz2]
tar jxvf [st3.tar.bz2]
mv sublime_text_3 /opt/sublime_text  # 放到/opt下

# 建立软链接，以方便终端打开
ln -s /opt/sublime_text/sublime_text /usr/bin/sublime
# 建立桌面快捷
cp /opt/sublime_text/sublime_text.desktop /usr/share/applications
cd /usr/share/applications
vi sublime_text.desktop
# 更改"Icon=/opt/sublime_text/Icon/48x48/sublime-text.png"
# 然后，于"Applications > Programming > Sublime Text"右键"Add this launcher to desktop"


2) 基本插件

2.1) Package Control：包管理器

"ctrl+`"调出控制台，输入标题链接内提供的Code，回车运行即可。于菜单栏"Preferences"下可看到"Package Control"。

然后，通过它的"Install Package"安装其他插件。

    import urllib.request,os,hashlib; h = '2915d1851351e5ee549c20394736b442' + '8bc59f460fa1548d1514676163dafc88'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by) 

2.2) SideBarEnhancements：侧边栏右键操作增强

Preferences > Key Bindings - User，添加快捷：

{ "keys": ["ctrl+t"], "command": "side_bar_new_file2" },
{ "keys": ["f2"], "command": "side_bar_rename" },

2.3) ConvertToUTF8：其他编码支持

有个注意事项：

Linux 用户：Sublime Text 2 和 3 内嵌 Python 版本中缺失几个 ConvertToUTF8 依赖的动态库。您必须手工安装这些文件才能让本插件完全运作。

2.4) SublimeLinter：检查语法错误

好多，选自己要用的语言，如Python的"SublimeLinter-pep8"。

    PEP 8 -- Style Guide for Python Code

2.5) SublimeCodeIntel：代码提示

安装完成后，会提示你：

For Linux:
  * Jump to definition = ``Super+Click``
  * Jump to definition = ``Control+Super+Alt+Up``
  * Go back = ``Control+Super+Alt+Left``
  * Manual CodeIntel = ``Control+Shift+space``

C系列语言的话，用SublimeClang。

2.6) Markdown Preview：Markdown预览

设置快捷"ctrl+m+p"：

{ "keys": ["ctrl+m", "ctrl+p"], "command": "markdown_preview", "args": {"target": "browser", "parser":"markdown"} },

2.7) InputHelper：输入帮助

Sublime输入不了中文，首先安装中文字体：

# 下载"YaHei.Consolas.1.12.Revise.ttf"字体
mkdir -p /usr/share/fonts/truetype/
# 将下载的字体拷贝到上述目录
fc-cache /usr/share/fonts/truetype

# 配置为该字体，见第3节"font_face"

然后安装InputHelper，重启Sublime。之后"Ctrl+Shift+Z"，调出对话框，切换输入法，即可输入中文。

如果很不幸没效果的话，可以参考下最后附录：InputHelper插件问题。
3) 基本设置

Preferences > Settings - User，添加Tab空格及显示：

"draw_white_space": "all",
"tab_size": 4,
"translate_tabs_to_spaces": true,
"font_face": "YaHei Consolas Hybrid",

4) 参考

略。
附录：InputHelper插件问题，"Ctrl+Shift+Z"调不出对话框，解决手顺。

Step 1: "Ctrl+`"调控制台，"Ctrl+Shift+Z"看输出：

Traceback (most recent call last):
  File "/opt/sublime_text/sublime_plugin.py", line 549, in run_
    return self.run(edit)
  File "inputhelper in /home/join/.config/sublime-text-3/Installed Packages/InputHelper.sublime-package", line 21, in run
  File "X/subprocess.py", line 818, in __init__
  File "X/subprocess.py", line 1416, in _execute_child
FileNotFoundError: [Errno 2] No such file or directory: '/home/join/.config/sublime-text-3/Packages/InputHelper/lib/linux_text_input_gui.py'

Step 2: 解压"Installed Packages/"下"InputHelper.sublime-package"到"Packages/"，并删除原包，重启Sublime。以直接用源代码。

Step 3: "Ctrl+Shift+Z"再看输出：

Traceback (most recent call last):
  File "/opt/sublime_text/sublime_plugin.py", line 549, in run_
    return self.run(edit)
  File "/home/join/.config/sublime-text-3/Packages/InputHelper/inputhelper.py", line 21, in run
    proc = subprocess.Popen(args, stdout=subprocess.PIPE)
  File "X/subprocess.py", line 818, in __init__
  File "X/subprocess.py", line 1416, in _execute_child
PermissionError: [Errno 13] Permission denied

Step 4: 修改"inputhelper.py"添加输出，看Popen了什么。为"InputHelper/lib/linux_text_input_gui.py"。继而查看其权限为"-rw-rw-r--"，如下直接改为777：

chmod 777 /home/join/.config/sublime-text-3/Packages/InputHelper/lib/linux_text_input_gui.py

Step 5: 继续"Ctrl+Shift+Z"，没效果。直接运行"linux_text_input_gui.py"，发现：

ImportError: No module named pygtk

Step 6: "pip install PyGTK"安装pygtk，但：

CompressionError: bz2 module is not available

Step 7: 源于编译Python2.7时没bz2模块，make时会提示缺失"bz2"。编译前安装有bzip2即可：

# 需要bzip2，不论如何，执行下行命令
yum install bzip2 bzip2-devel -y

重编译安装，见CentOS升级Python2.7及安装pip。

Step 8: 重新"pip install PyGTK"，然后：

Command python setup.py egg_info failed with error code 1 in /tmp/pip_build_root/PyGTK
Storing debug log for failure in /root/.pip/pip.log

# 直接从源码安装
wget http://pypi.douban.com/packages/source/P/PyGTK/pygtk-2.24.0.tar.bz2
tar jxvf pygtk-2.24.0.tar.bz2
cd pygtk-2.24.0
python setup.py install
# 一样提示错误：
# ********************************************************************
# * Building PyGTK using distutils is only supported on windows. *
# * To build PyGTK in a supported way, read the INSTALL file.    *
# ********************************************************************
vi INSTALL
# 看来要重编
./configure

# issue: configure: error: Package requirements (pygobject-2.0 >= 2.21.3) were not met: ... No package 'pygobject-2.0' found ...
# 缺少pygobject
# yum list pygobject*
yum install pygobject2 pygobject2-devel -y
# issue: configure: error: Package requirements (pygobject-2.0 >= 2.21.3) were not met:
# Requested 'pygobject-2.0 >= 2.21.3' but version of PyGObject is 2.20.0
# pygobject版本不够
rpm -e pygobject2-devel

# 下个高版本的PyGObject
wget http://ftp.gnome.org/pub/GNOME/sources/pygobject/3.10/pygobject-3.10.2.tar.xz
tar Jxvf pygobject-3.10.2.tar.xz
cd pygobject-3.10.2

Step 9: 感觉很折腾，应该是由于目前为Python2.7才依赖了高版本的PyGObject，所以：

# 而原先Python2.6还装着，所以：
vi /home/join/.config/sublime-text-3/Packages/InputHelper/lib/linux_text_input_gui.py
# 把第一行改为"#!/usr/bin/env python2.6.6"
# 然后"Ctrl+Shift+Z"果真弹出了对话框，但Sublime UI阻塞，未响应。

cd /home/join/.config/sublime-text-3/Packages/InputHelper/lib/
python2.6.6 linux_text_input_gui.py
# 输入内容后回车，看到如下错误：
# AttributeError: 'gtk.Entry' object has no attribute 'get_buffer'
# Traceback (most recent call last):
#   File "linux_text_input_gui.py", line 14, in destroy
#     self.print_text()
#   File "linux_text_input_gui.py", line 9, in print_text
#     buffer = self.textInput.get_buffer()
# AttributeError: 'gtk.Entry' object has no attribute 'get_buffer'
# 给"linux_text_input_gui.py"的"SimpleTextInput::print_text()"添加打印，看"self.textInput"的属性。直接有"get_text"，所以做如下修改：
# def print_text(self):
#     # buffer = self.textInput.get_buffer()
#     # print(buffer.get_text())
#     print(self.textInput.get_text())
# 问题解决。pygtk版本不同的原因么？

Step 10: 折腾死我了，不想活了TT。
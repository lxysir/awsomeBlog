#在CentOS7下安装virtualenv和mariadb

##1.我的环境是CentOS7，首先安装virtualenv，使用命令：
	
	sudo yum install virtualenv
	cd workdirectory/awsomeBlog #进入工作目录
	virtualenv --no-site-packages venv #创建干净的python环境venv
	source venv/bin/active #进入venv环境
	deactive #退出

##2.在全局环境下安装mysql

###<1>由于centos默认使用mariadb，因此安装命令如下：
	
	sudo yum -y install mariadb-server mariadb-devel

###<2>安装完成之后查看mysql：

	[frank@frankvpc awsomeBlog]$ which mysql
	/usr/bin/mysql

##3.启动数据库服务，报错：

	[frank@frankvpc awsomeBlog]$ sudo service mysqld start
	Redirecting to /bin/systemctl start  mysqld.service
	Failed to start mysqld.service: Unit mysqld.service failed to load: \
	No such file or directory.

###搜索后得知，需要按以下步骤操作：

###<1>To autostart MariaDB on Fedora 20, execute the following command:

	[frank@frankvpc awsomeBlog]$ systemctl enable mariadb.service
	==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-unit-files ===
	Authentication is required to manage system service or unit files.
	Authenticating as: frankli (frank)
	Password:
	==== AUTHENTICATION COMPLETE ===
	Created symlink from /etc/systemd/system/multi-user.target.wants/mariadb.service to /usr/lib/systemd/system/mariadb.service.
	==== AUTHENTICATING FOR org.freedesktop.systemd1.reload-daemon ===
	Authentication is required to reload the systemd state.
	Authenticating as: frankli (frank)
	Password:
	==== AUTHENTICATION COMPLETE ===

###<2>To start MariaDB on Fedora 20, execute the following command:

	[frank@frankvpc awsomeBlog]$ systemctl start mariadb.service
	==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
	Authentication is required to manage system services or units.
	Authenticating as: frankli (frank)
	Password:
	==== AUTHENTICATION COMPLETE ===

###<3>After you started MariaDB (do this only once), execute the following command:

	[frank@frankvpc awsomeBlog]$ /usr/bin/mysql_secure_installation
	/usr/bin/mysql_secure_installation:行379: find_mysql_client: 未找到命令
	
	NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
	      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!
	
	In order to log into MariaDB to secure it, we'll need the current
	password for the root user.  If you've just installed MariaDB, and
	you haven't set the root password yet, the password will be blank,
	so you should just press enter here.
	
	Enter current password for root (enter for none):
	OK, successfully used password, moving on...
	
	Setting the root password ensures that nobody can log into the MariaDB
	root user without the proper authorisation.
	
	Set root password? [Y/n] y
	New password:
	Re-enter new password:
	Password updated successfully!
	Reloading privilege tables..
	 ... Success!
	
	
	By default, a MariaDB installation has an anonymous user, allowing anyone
	to log into MariaDB without having to have a user account created for
	them.  This is intended only for testing, and to make the installation
	go a bit smoother.  You should remove them before moving into a
	production environment.
	
	Remove anonymous users? [Y/n] n
	 ... skipping.
	
	Normally, root should only be allowed to connect from 'localhost'.  This
	ensures that someone cannot guess at the root password from the network.
	
	Disallow root login remotely? [Y/n] n
	 ... skipping.
	
	By default, MariaDB comes with a database named 'test' that anyone can
	access.  This is also intended only for testing, and should be removed
	before moving into a production environment.
	
	Remove test database and access to it? [Y/n] n
	 ... skipping.
	
	Reloading the privilege tables will ensure that all changes made so far
	will take effect immediately.
	
	Reload privilege tables now? [Y/n] y
	 ... Success!
	
	Cleaning up...
	
	All done!  If you've completed all of the above steps, your MariaDB
	installation should now be secure.
	
	Thanks for using MariaDB!

###<4> Try to access mariadb

	[frank@frankvpc awsomeBlog]$ mysql -u root -p
	Enter password:
	Welcome to the MariaDB monitor.  Commands end with ; or \g.
	Your MariaDB connection id is 6
	Server version: 5.5.50-MariaDB MariaDB Server
	
	Copyright (c) 2000, 2016, Oracle, MariaDB Corporation Ab and others.
	
	Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
	
	MariaDB [(none)]>

##4.在venv python环境下操作mariadb

	首先需要安装 sudo yum install maria-devel
	否则在安装mysql-python时，会报 mysql-config not found
	然后 sudo yum install mysql-python
	验证 
	>>> import MySQLdb
	>>>
	没有报错，表示安装成功！


# xhjh-xyf
OS：
1、基本命令
 	id
 	ls
 	pwd
 	hostname
 	file
 	touch
 	vim
 	mkdir -p
 	cp -r 
 	mv
 	rm
 	tar -cvf
 			bzip2\gzip
 		 	tar jcvf etc.tar.bz2 /etc
 		 	tar zcvf etc.tar.gz /etc
 		  	file etc.tar.bz2
 		  	du -h etc.tar.bz2
 	
 	du -sh 
 	ln -s 
 	
 	# root
 	$ 一般用户
 	/ 根
 	～ 家目录


2、用户、组、权限
	groupadd sysmgrs -g 30000
	useradd -G sysmgrs natasha
	useradd -G sysmgrs harry 
	useradd sarah -s /sbin/nologin
	echo 123 | passwd --stdin natasha
	echo 123 | passwd --stdin harry
	echo 123 | passwd --stdin sarah
	useradd user2 -u 3388
	echo 123 | passwd --stdin user2
	usermod -g devopers -G g1 lisi
	usermod -u 8000 lisi
	userdel -r zhang
	usermod -c "user lisi" lisi
	
	mkdir /home/managers
	chgrp sysmgrs /home/managers/
	ls -ld /home/managers/ 
	chmod g=rwx,o=--- /home/managers/
	chmod g+s /home/managers/
	ls -ld /home/managers/
		
	yum install mailx postfix -y
	systemctl enable --now postfix
	echo "this is a mail conent" | mail -s
	"to lisi" lisi
	mail -u lisi
	cd /var/spool/mail/	
	vim /etc/sudoers
	chown myuser passwd
	chgrp g1 passwd

3、文件系统

	mkdir /root/dfiles
	find / -user user3 -exec cp -a {} /root/dfiles \;
	grep re /usr/share/rhel.xml >/root/files
	tar -zcvf books.tar.gz /usr/local
	
	挂载
	/etc/fstab
	stat file
	
	[一些参数]
	grep -w 完全匹配（word）
	grep -i	忽略大小写
	grep -n	现实行号
	grep -r	递归(+dictionary)
	grep ^*** 以***开头
	grep ***$	以***结尾
	grep -v	反向选择


4、进程、服务管理
	ps aux
	yum install httpd -y
	systemctl start httpd
 	ss -ltpn |grep 80
	

	yum install mariadb-server -y
	systemctl start mariadb
	ss -ltpn |grep 3306
	systemctl enable --now mariadb
	systemctl status mariadb -l
 	journalctl -xn
 	tail -f /var/log/messages
 	tail -f /var/log/mariadb/mariadb.log

5、软件安装、升级、配置	
	查看操作系统已经安装过的软件包
	rpm -qa
	查询软件包⾥⾯的⽂档⽂件和配置⽂件
	rpm -qc yum
	rpm -qd yum

	查询⼀个RPM包包含的⽂件
	rpm -ql yum

	根据⽂件路径查询，属于是那个RPM包安装
	rpm -qf /etc/my.cnf

	重装：
	yum -y reinstall sudo

	下载httpd+依赖
	yum install --downloadonly --
	downloaddir=/tmp httpd -y

	查询软件包的信息# 查询未安装的RPM包需要加p参数
	rpm -qip httpd-2.4.6-97.el7.centos.x86_64.rpm
	rpm -ivh httpd-2.4.6-97.el7.centos.x86_64.rpm

	卸载
	rpm -e

	搭建web服务器。指定虚拟主机：修改host文件、指定server_name。修改web服务器端口。修改index.html。最后用curl指令验证你的修改结果。

	/var/www/html/index.html
	setenforce 0
	/etc/selinux/config

6、linux网络配置&故障排除
	方法一：修改
	nmcli connection modify "Wired connection 1"
	ipv4.addresses 172.24.10.150/24 ipv4.gateway
	172.24.10.100 ipv4.dns 172.24.10.254
	nmcli con up "Wired connection 1"

	方法二：新增
	nmcli con add con-name static ifname eth0 type ethernet ipv4.addresses 172.24.10.150/24 ipv4.gateway 172.24.10.100 ipv4.dns 172.24.10.254
	#激活添加的配置⽂件
	nmcli con up static

7、os故障排除
	破解密码：
	console=tty0 rd.break
	ctrl+x
	
	mount -o remount,rw /sysroot
	chroot /sysroot
	passwd root
	touch /.autorelabel
	exit
	
	grub2-mkconfig -o /boot/grub2/grub.cfg
	reboot

8、数据库mysql安装

1、yum源配置（如果虚机源不行的话，拷贝宿主机的yum）
2、yum install -y mysql*
3、systemctl start mysqld
4、systemctl enable mysqld
5、cat /var/log/mysqld.log |grep password  查找root密码
6、mysql -uroot -p  
7、set password = password('Training@123');  修改root密码
8、grant all privileges on *.* to 'root' @'%' identified by 'Training@123'; 授权访问权限
9、flush privileges; 立即生效

mysqldump -u username -p password --databases database_name > backup.sql
mysqldump -u username -p password database_name table_name > backup.sql
systemctl restart vncserver@\:1

yum install bridge-utils tunctl
 lsmod | grep tun
  lsmod | grep bridge
  cd /etc/sysconfig/network-scripts/
  vim ifcfg-br0

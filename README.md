# xhjh-xyf
OS：
1、基本命令
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

9、网桥
yum install bridge-utils tunctl
 lsmod | grep tun
  lsmod | grep bridge
  cd /etc/sysconfig/network-scripts/
  vim ifcfg-br0
  DEVICE=br0
TYPE=Bridge
STP=yes
IPADDR=172.31.200.46
PREFIX=16
GATEWAY=172.31.0.254
DNS1=172.31.0.248
BOOTPROTO=none
ONBOOT=yes
DELAY=0

 vim ifcfg-ens224
 TYPE=Ethernet
BOOTPROTO=none
NAME=ens224
DEVICE=ens224
ONBOOT=yes
BRIDGE=br0

 vim /etc/libvirt/qemu/centos7.xml
  virsh define /etc/libvirt/qemu/centos7.xml

10、NAT
 virsh net-list --all
 sysctl net.ipv4.ip_forward=1
 vim /etc/libvirt/qemu/networks/nat_net.xml
 virsh net-define /etc/libvirt/qemu/networks/nat_net.xml
 virsh net-list --all
 virsh net-start nat_net
 virsh net-autostart nat_net

  virsh attach-interface kvm1 --type bridge \
--source virbr1 --model virtio --config --live

 virsh domiflist kvm1
 virsh start kvm1
 virsh console kvm1


11、LVS负载均衡
====node2和node3上配置=====
systemctl disable firewalld
systemctl stop firewalld
setenforce 0
yum install net-tools epel-release nginx
mv /usr/share/nginx/html/* /tmp

---node2---
echo "this is node2" > /usr/share/nginx/html/index.html
---node3---
echo "this is node3" > /usr/share/nginx/html/index.html

systemctl enable nginx --now
ifconfig lo:0 192.168.122.20 broadcast 192.168.122.20 netmask 255.255.255.255 up
route add -host 192.168.122.20 dev lo:0
echo "1" >/proc/sys/net/ipv4/conf/lo/arp_ignore
echo "2" >/proc/sys/net/ipv4/conf/lo/arp_announce
echo "1" >/proc/sys/net/ipv4/conf/all/arp_ignore
echo "2" >/proc/sys/net/ipv4/conf/all/arp_announce
ip addr

===node1===
systemctl stop firewalld
systemctl disable firewalld
setenforce 0
cp /etc/sysconfig/network-scripts/ifcfg-eth0 /etc/sysconfig/network-scripts/ifcfg-eth0:0

vim /etc/sysconfig/network-scripts/ifcfg-ens3:0
DEVICE=ens3:0
NM_CONTROLLED=no
ONBOOT=yes
IPADDR=192.168.122.20
NETMASK=255.255.255.0

systemctl restart network
ip addr

vim /etc/sysctl.conf
net.ipv4.ip_forward = 1

sysctl -p
yum install ipvsadm -y
ipvsadm
lsmod | grep ip_vs
touch /etc/sysconfig/ipvsadm
systemctl start ipvsadm

ipvsadm -A -t 192.168.122.20:80 -s wrr
ipvsadm -a -t 192.168.122.20:80 -r 192.168.122.124:80 -g -w 1
ipvsadm -a -t 192.168.122.20:80 -r 192.168.122.191:80 -g -w 1
ipvsadm -Ln

curl 192.168.122.20		//多执行几次，查看效果

12 nginx反向代理
yum -y install httpd
vim /etc/httpd/conf/httpd.conf
echo 'hello httpd' > /var/www/html/index.html
systemctl start httpd
vim /etc/nginx/conf.d/proxy.conf
server {
 listen 80;
 server_name www.proxy.com;
 location / {
 proxy_pass http://127.0.0.1:8080;
 } }

 nginx -t
 systemctl restart nginx
 curl 127.0.0.1


13、七层负载
后端2台：
yum install httpd -y
echo `hostname` > /var/www/html/index.html
systemctl enable --now httpd
systemctl disable firewalld
systemctl stop firewalld
setenforce 0

前端1台：
先确认nginx正常运行：
systemctl status nginx
cd /etc/nginx/conf.d/
vim httpLB.conf
upstream servers{
 server vm02:80;#用主机名称的话，先在/etc/hosts里配置一下
 server vm03:80;
}
server {
listen 80;
server_name vm01;
location / {
proxy_pass http://servers;
}}


nginx -t
nginx -s reload


curl vm01#curl自己测试


15、DB
docker run -p 13306:3306 --name mysql8 -e MYSQL_ROOT_PASSWORD=Test_pwd_2023 -d mysql:8.0.26
docker ps -a # 观察进程已经处于 up 状态, 再进行下一步
docker cp  mysql8:/etc/mysql /opt/mysql_test/master/conf
docker cp  mysql8:/etc/mysql /opt/mysql_test/slave/conf
docker stop mysql8
docker rm mysql8

# 修改 slave 配置文件中的 server-id, 防止后面搭建复制异常
$ echo 'server-id = 2' | sudo tee -a /opt/mysql_test/slave/conf/mysql/my.cnf





docker run  -itd  -p 13307:3306 \
--name mysql8-slave \
--privileged=true  \
-v /opt/mysql_test/slave/conf/mysql:/etc/mysql  \
-v /opt/mysql_test/slave/logs:/logs  \
-v /opt/mysql_test/slave/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=Test_pwd_2023  \
-e MYSQL_ROOT_HOST='%' \
-d mysql:8.0.26 \
--default_authentication_plugin=mysql_native_password

create user admin@'%' identified by 'Test_pwd_2023';
grant all on *.* to admin@'%' with grant option;



change master to
master_user='admin',
master_password='Test_pwd_2023',
master_host='', 
master_port=13306, 
master_log_file='xxx', 
master_log_pos=xxx; 

CREATE TABLE `test1` (
  `id` int NOT NULL AUTO_INCREMENT,
  `k` int NOT NULL DEFAULT '0',
  `c` char(120) NOT NULL DEFAULT '',
  `pad` char(60) NOT NULL DEFAULT '',
  PRIMARY KEY (`id`),
  KEY `k_1` (`k`)
) ENGINE=InnoDB ;

mysqlbinlog --no-defaults -v  --base64-output=decode-rows  --start-datetime='2023-07-12 11:00:00'  --stop-datetime='2023-07-12 12:00:00'  -d xyf_db  /opt/mysql_test/slave/data/binlog.000002 > ~/recover.log

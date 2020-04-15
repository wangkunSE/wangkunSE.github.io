# Linux下安装ORACLE10g的注意事项总结

## 1. 配置网络

**vi /etc/sysconfig/network-scripts/ifcfg-eth0 **

> DEVICE=eth0
> TYPE=Ethernet
> UUID=eecaa6f0-7a48-4807-8d64-c0b7d490f8b2
> ONBOOT=yes
> NM_CONTROLLED=yes
> BOOTPROTO=none
> <u>IPADDR=192.168.152.136</u>
> <u>NETMASK=255.255.255.0</u>
> <u>GATEWAY=192.168.152.2</u>
> <u>HWADDR=00:0C:29:A9:44:BC</u>
> PREFIX=24
> DEFROUTE=yes
> IPV4_FAILURE_FATAL=yes
> IPV6INIT=no
> NAME="System eth0"
> LAST_CONNECT=1495547591
> <u>DNS1=192.168.152.2</u>

**下划线部分以自己的机子为准，若能ping通ip地址而ping不同域名，可以加上最后一行的DNS，地址为网关地址**

## 2. 安装必要软件

> libXp-1.0.0-8.1.el5.i386.rpm
> binutils-2.17.50.0.6-12.el5.i386.rpm
> compat-db-4.2.52-5.1.i386.rpm
> compat-libstdc++-296-2.96-138.i386.rpm
> control-center-2.16.0-16.el5.i386.rpm
> gcc-4.1.2-46.el5.i386.rpm
> gcc-c++-4.1.2-46.el5.i386.rpm
> glibc-2.5-42.i386.rpm
> glibc-common-2.5-42.i386.rpm
> libstdc++-4.1.2-46.el5.i386.rpm
> libstdc++-devel-4.1.2-46.el5.i386.rpm
> make-3.81-3.el5.i386.rpm
> pdksh-5.2.14-36.el5.i386.rpm
> sysstat-7.0.2-3.el5.i386.rpm
> setarch-2.0-1.1.i386.rpm

**需要注意的是64位系统需要将32位软件也安装上，不然后面会报错。**

## 3.配置java环境

> 这一步不多说了，可以设置一个全局的环境变量

**vi /etc/profile  在最底下加入**

> export JAVA_HOME=/home/app/jdk
> export CLASSPATH=.:\$JAVA_HOME/lib:$CLASSPATH
> export PATH=\$PATH:\$JAVA_HOME/bin:\$JAVA_HOME/jre/bin



## 4.设置必要的用户以及文件

> - 创建用户和组
>
>   [root@oracle ~]# groupadd dba
>   [root@oracle ~]# groupadd oinstall
>   [root@oracle ~]# groupadd oper   
>   [root@oracle ~]# useradd -g oinstall -G dba,oper oracle
>
> - 添加文件
>
>   [root@oracle ~]# vim /etc/sysctl.conf
>   kernel.shmall = 2097152
>   kernel.shmmax = 2147483648
>   kernel.shmmni = 4096
>   kernel.sem = 250 32000 100 128
>   fs.file-max = 65536
>   net.ipv4.ip_local_port_range = 1024 65000
>   net.core.rmem_default = 1048576
>   net.core.rmem_max = 1048576
>   net.core.wmem_default = 262144
>   net.core.wmem_max = 262144
>   [root@oracle ~]# vim /etc/security/limits.conf
>   oracle              soft    nproc   2047
>   oracle              hard    nproc   16384
>   oracle              soft    nofile  1024
>   oracle              hard    nofile  65536
>   [root@oracle ~]# vim /etc/pam.d/login
>   session    required     /lib/security/pam_limits.so
>   session    required     pam_limits.so
>   [root@oracle ~]# vi /etc/profile
>
>   ```shell
>   if [ $USER = "oracle" ]; then
>   	if [ $SHELL = "/bin/ksh" ]; then
>             ulimit -p 16384
>             ulimit -n 65536
>       else
>             ulimit -u 16384 -n 65536
>       fi
>   fi
>   ```
>
> - 新建文件，修改权限
>
>   [root@oracle ~]# mkdir /u01   
>   [root@oracle ~]# mkdir -pv /u01/app/oracle
>   mkdir: created directory `/u01/app'
>   mkdir: created directory `/u01/app/oracle'
>   [root@oracle ~]# chown -R oracle:oinstall /u01/app/oracle/
>   [root@oracle ~]# chmod -R 775 /u01/app/oracle/
>   [root@oracle ~]# mkdir /u01/flash_recovery_area
>   [root@oracle ~]# chown oracle:oinstall /u01/flash_recovery_area/
>   [root@oracle ~]# chmod 755 /u01/flash_recovery_area/

## 5.安装oracle

1. #### 添加以下几行

   > [root@oracle ~]# su - oracle
   > [oracle@oracle ~]$ vim .bash_profile
   > export ORACLE_BASE=/u01/app/oracle
   > export ORACLE_HOME=/u01/app/oracle/product/10.2.0
   > export ORACLE_SID=xjzhujunjie
   > [oracle@oracle ~]$ . .bash_profile
   > [oracle@oracle ~]$ env | grep ORA
   > ORACLE_SID=soul
   > ORACLE_BASE=/u01/app/oracle
   > ORACLE_HOME=/u01/app/oracle/product/10.2.0
   >
   > **其中的ORACLE_SID可以写你自己的，但是要记住，后面要用**

2. #### 找到runInstaller进行安装，要在图形化界面下才能成功

   基本的安装过程不是很难，一路点过去就可以了。在准备的时候可以不要它的oral数据库，后面自己建库。

   > **过程中可能会出现一些问题，多看log进行解决**
   >
   > 大部分都是缺包，用yum把需要的包都打上就没问题了
   >
   >  `yum install libXp.i686 libXp-devel.i686 libXt.i686 libXt-devel.i686 libXtst.i686 libXtst-devel.i686 make.x86_64 gcc.x86_64 libaio.x86_64 glibc-devel.i686 libgcc.i686`
   > `glibc-devel.x86_64 compat-libstdc++-33 -y`
   > `yum install glibc* gcc* make* compat-db* libstdc* libXp* libXtst* compat-libstdc++* -y` 
   >
   > 
   >
   > **Error in invoking target 'collector' of makefile '/u01/app/oracle/product/10.2.0/sysman/lib/ins_emdb.mk'. **
   >
   > 上面这个问题特殊的说一下。出现这个问题可以按continue。然后有两种解决方式：
   >
   > 1.修改bash_porfile 添加LD_LIBRARY_PATH=$ORACLE_HOME/lib
   >
   > 2.将/u01/app/oracle/product/10.2.0/bin下的oracle改成oracle.bin
   >
   > 并新建一个oracle在其中添加以下内容：
   >
   > `export DISABLE_HUGETLBFS=1`
   > `exec $ORACLE_HOME/bin/oracle.bin$@`
   >
   > `EOF`
   >
   > 两个方式我都用了，后面启动没有出现问题。

3. #### 建立监听器--netca

   > 在图形化界面下使用netca命令，按步骤添加就好，协议选择tcp。

4. #### 查看监听器--lsnrctl status

   > The command completed successfully
   >
   > 如果有上面这句话就说明监听器启动成功了，如果没成功，用**lsnrctl start** 开启，停用为**lsnrctl stop**命令

5. #### 创建数据库--dbcp

   > 一路点过去就行，注意SID属性选择之前你在配置文件中写的那个。最后注意将**scott hr**两个账户解锁。

6. #### 启动数据库实例--sqlplus

   > 第一次账号为**sys as sysdba**密码为空
   >
   > 进去后挂载数据库**startup**
   >
   > 至此就完成数据库的安装了。

## 6.远程访问的问题

> 因为我是在虚拟机中安装，本地环境为windows，所以特殊说明一下。
>
> 首先要安装sqlplus工具包并加入path中，然后在cmd中输入
>
> `sqlplus scott/tiger@oracle10:1521/soul`
>
> 其中的oracle10是我设置的虚拟机域名。
>
> 可能会存在一些问题，**ORA-12514**等。
>
> 解决办法：
>
> 在$ORACLE_HOME/network/admin/listener.ora中更改
>
> 
>
> ```shell
> SID_LIST_LISTENER =  
> (SID_LIST =  
>   (SID_DESC =  
>     (SID_NAME = PLSExtProc)  
>     (ORACLE_HOME = /u01/app/oratt/product/10.2.0/dbhome_1)  
>     (PROGRAM = extproc)  
>   )  
>   (SID_DESC =  
>     (SID_NAME = webdb)  
>     (ORACLE_HOME = /u01/app/oratt/product/10.2.0/dbhome_1)  
>   )  
>  )
>  LISTENER =  
>   (DESCRIPTION_LIST =  
>   (DESCRIPTION =  
>   (ADDRESS = (PROTOCOL = TCP)(HOST = serv1)(PORT = 1844))  
>   (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC0))  
> 	)  
> )  
> ```
1. 安装命令：

   ```shell
   rpm -ivh /home/vsftpd-3.0.2-25.el7.x86_64.rpm
   ```

2. 查看是否安装成功：

   ```shell
   vsftpd -version
   #或
   rpm -qa |grep vsftpd
   ```

3. 新建ftp用户：

   ```shell
   #ftp用户目录也可在新建用户时指定：
   useradd -d /opt/images -s /bin/bash ftpname
   #添加并不允许登录：
   useradd -d /home/ftp -s /sbin/nologin ftpname
   #设置主目录（更改登录时看到的目录）：
   usermod -d /opt ftpname
   #useradd：警告：此主目录已经存在。
   #不从 skel 目录里向其中复制任何文件。
   userdel -rf ftpname
   ```

4. 修改密码

   ```shell
   passwd ftpname
   ```

5. ftp启动、重启、停止、状态查询命令

   ```shell
   service vsftpd start        #启动ftp
   service vsftpd stop         #停止ftp
   service vsftpd restart      #重启ftp
   service vsftpd status       #查询ftp状态
   ```

6. 防火墙添加ftp服务：

   ```shell
   firewall-cmd --permanent --add-service=ftp
   ```

7. 查看端口命令：

   ```shell
   ss -ntlp
   ```

8. 配置vsftpd配置文件

   ```shell
   # vi /etc/vsftpd/vsftpd.conf
   # 禁止匿名访问
   # 将配置文件中”anonymous_enable=YES “改为 “anonymous_enable=NO”
   ```

9. 可能会出现登录后访问到的是空白文件

   ```shell
   #网上查了下说是用：
   setsebool ftpd_disable_trans 1
   #解决
   #可是当我执行setsebool ftpd_disable_trans 1的时候，竟然出现了下面的提示
   #“Could not change active booleans: Invalid boolean”
   #再百度，得到如下解决方案，测试可行
   
   #下面命令逐条执行后，重启动VSFTP就能登陆了
   setsebool allow_ftpd_full_access 1
   setsebool allow_ftpd_use_cifs 1
   setsebool allow_ftpd_use_nfs 1
   setsebool ftp_home_dir 1
   setsebool httpd_enable_ftp_server 1
   setsebool tftp_anon_write 1
   
   service vsftpd restart
   ```

   
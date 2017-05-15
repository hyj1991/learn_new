# 新构建服务器流程，本人熟悉 CentOS 6.5 系列

## I.新建用户相关

### 新建用户

* 1. useradd hyj
* 2. passwd hyj

### 给新用户增加 sudo 权限
* chmod -v u+w /etc/sudoers
* vim /etc/sudoers
	* root    ALL=(ALL)       ALL
	* hyj     ALL=(ALL)       ALL #新增用户信息

### 取消用户 sudo 权限
* chmod -v u-w /etc/sudoers

## II.基础工具相关

### 安装 gcc
* yum -y install gcc

### 安装 g++
* yum install gcc-c++

### 升级 gcc 和 g++ 版本
* http://ftp.gnu.org/gnu/gcc 选择版本
* wget http://ftp.gnu.org/gnu/gcc/gcc-4.8.2/gcc-4.8.2.tar.gz
* ar -zxvf gcc-4.8.2.tar.gz
* cd gcc-4.8.2
* ./contrib/download_prerequisites
* mkdir gcc-build-4.8.2
* cd gcc-build-4.8.2
* ../configure -enable-checking=release -enable-languages=c,c++ -disable-multilib
* make -j4
* sudo make install
* ls /usr/local/bin | grep gcc
* sudo update-alternatives --install /usr/bin/gcc gcc /usr/local/bin/x86_64-unknown-linux-gnu-gcc 40
* 新窗口验证

### shadowsocks 防火墙配置

```
*filter
:INPUT DROP [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT DROP [10:600]
-A INPUT -p tcp -m tcp --dport 26053 -j ACCEPT 
-A INPUT -p icmp -m icmp --icmp-type any -j ACCEPT 
-A INPUT -s 127.0.0.1/32 -d 127.0.0.1/32 -j ACCEPT 
-A INPUT -s 127.0.0.1/32 -d 127.0.0.1/32 -j ACCEPT 
-A INPUT -s 127.0.0.1/32 -d 127.0.0.1/32 -j ACCEPT 
-A INPUT -s 127.0.0.1/32 -d 127.0.0.1/32 -j ACCEPT 
-A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT 
-A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT 
-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT 
-A OUTPUT -p icmp -m icmp --icmp-type any -j ACCEPT 
-A OUTPUT -s 127.0.0.1/32 -d 127.0.0.1/32 -j ACCEPT 
-A OUTPUT -s 127.0.0.1/32 -d 127.0.0.1/32 -j ACCEPT 
-A OUTPUT -s 127.0.0.1/32 -d 127.0.0.1/32 -j ACCEPT 
-A OUTPUT -s 127.0.0.1/32 -d 127.0.0.1/32 -j ACCEPT 
-A OUTPUT -m state --state RELATED,ESTABLISHED -j ACCEPT 
-A OUTPUT -p udp -m udp --dport 53 -j ACCEPT 
-A OUTPUT -p tcp -m state --state NEW -m tcp --dport 80 -j ACCEPT 
COMMIT

```

[详细链接](http://www.jianshu.com/p/28b8536a6c8a)

### 配置秘钥登录

#### 在服务器生成秘钥
* ssh-keygen -t rsa
* /root/.ssh/下面会生成id_rsa 和 id_rsd.pub

#### 秘钥添加到服务器
* mv /root/.ssh/id_rsa.pub /root/.ssh/authorized_keys
* chmod 600 /root/.ssh/authorized_keys
* /etc/ssh/sshd_config 中，RSAAuthentication 和 PubkeyAuthentication 都修改为 yes
* service sshd restart

#### 客户端登录
* 下载第一步中生成的 id_rsa 文件到客户端
* chmod 600 /本地路径/id_rsa
* ssh username@server_address -p ssh_port -i /本地路径/id_rsa，即可秘钥登录
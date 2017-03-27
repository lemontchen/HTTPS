Linux Apache SSL证书安装

一、安装准备 
1.安装Openssl
要使Apache支持SSL，需要首先安装Openssl支持。（现在的服务器一般都已经预装了，可以直接直接跳到下一步。）安装Openssl有两种方式：
1）下载源码编译安装：
推荐下载安装openssl-0.9.8k.tar.gz   

下载Openssl：http://www.openssl.org/source/

tar -zxf openssl-0.9.8k.tar.gz //解压安装包

cd openssl-0.9.8k//进入已经解压的安装包 

./config //配置安装。推荐使用默认配置   

make && make install //编译及安装  

openssl默认将被安装到/usr/local/ssl 
2）使用软件管理工具进行安装：
Centos: sudo yum install openssl

Ubuntu: sudo apt-get install openssl 
2. 安装Apache

1）下载源码编译安装：
wget http://mirror.bit.edu.cn/apache//httpd/httpd-2.4.12.tar.gz  //下载源代码文件

tar -zxf httpd-2.4.12.tar.gz //解压安装包

cd httpd-2.4.12.tar.gz//进入已经解压的安装包 

./configure --prefix=/usr/local/apache --enable-so --enable-ssl --with-ssl=/usr/local/ssl --enable-mods-shared=all //配置安装。推荐动态编译模块 (openssl路径请根据实际情况填写）

make && make install //编译及安装  

动态编译Apache模块，便于模块的加载管理。Apache 将被安装到/usr/local/apache
2）使用软件管理工具进行安装：
Centos: sudo yum install httpd

Ubuntu: sudo apt-get install httpd 

二、 安装服务器证书
1. 获取中级CA证书 
为保障服务器证书在客户端的兼容性，需要安装两张中级CA证书(以证书签发为准)。 
在订单页面中下载CA证书，保存为CA.crt
2. 获取服务器证书
在订单页面下载服务器证书（以域名命名的文件），改名为server.crt文件。
3. Apache 2.0.63的配置 
打开apache安装目录下conf目录中的httpd.conf文件，找到 
#LoadModule ssl_module modules/mod_ssl.so 删除行首的配置语句注释符号“#” 保存退出。 
打开apache安装目录下conf目录中的ssl.conf文件，找到 
在配置文件中查找以下配置语句 
SSLCertificateFile conf/ssl.crt/server.crt 将服务器证书配置到该路径下 
SSLCertificateKeyFile conf/ssl.key/server.key 将服务器证书私钥配置到该路径下 
#SSLCertificateChainFile conf/ssl.crt/ca.crt 删除行首的“#”号注释符，并将中级CA证书CA.crt（请到订单页面下载）配置到该路径下 保存退出，并重启Apache。重启方式：
进入Apache安装目录下的bin目录，运行如下命令 ./apachectl -k stop
./apachectl startssl
4. Apache 2.2.* 的配置 
打开apache安装目录下conf目录中的httpd.conf文件，找到 
#LoadModule ssl_module modules/mod_ssl.so
#Include conf/extra/httpd-ssl.conf 
删除行首的配置语句注释符号“#” 
保存退出。 
打开apache安装目录下conf/extra目录中的httpd-ssl.conf文件 
在配置文件中查找以下配置语句 
SSLCertificateFile conf/ssl.crt/server.crt 将服务器证书配置到该路径下 
SSLCertificateKeyFile conf/ssl.key/server.key 将服务器证书私钥配置到该路径下 
#SSLCertificateChainFile conf/ssl.crt/ca.crt 删除行首的“#”号注释符，并将中级CA证书 ca.crt（从订单页面下载的）配置到该路径下 保存退出，并重启Apache。

重启方式： 进入Apache安装目录下的bin目录，运行如下命令 ./apachectl -k stop ./apachectl -k start 通过https方式访问您的站点，测试站点证书的安装配置。


另外建议在httpd-ssl.conf里进行如下操作： 
a. 添加SSL 协议支持语句，关闭不安全的协议和加密套件: 
SSLProtocol all -SSLv2 -SSLv3 
b.修改加密套件如下: 
SSLCipherSuite AESGCM:ALL:!DH:!EXPORT:!RC4:+HIGH:!MEDIUM:!LOW:!aNULL:!eNULL;

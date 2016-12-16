# ssh认证技巧

---

2016-08-04   李家宜

### 1. ssh简介

SSH为SecureSHell的缩写，建立在应用层和传输层基础上的安全协议。

SSH最初作为UNIX系统上的一个程序，后来由于其安全性等优势又迅速扩展到其他操作平台，几乎所有UNIX平台—包括HP-UX、Linux、AIX、Solaris、DigitalUNIX、Irix，以及其他平台，都可运行SSH。

SSH作为一个网间服务，对应监听端口号为22号端口。

### 2.认证机制

ssh登录认证的方式有两种：

+ ​     采用用户名和密码认证

  - ​        客户端向服务器发出密码认证请求，将用户名和密码发送给服务器，服务器得到客户端提供的用户名和密码后，与服务器本地的用户名密码比对，比对成功则返回成功的消息。

+ ​     采用主机秘钥方式认证

  - ​        通过秘钥算法来实现签名认证，该认证前提有一对秘钥，公用钥匙放在需要被ssh的主机上，用于加密数据，私钥存放在发起登录连接请求的主机上，用于解密数据。

    ​         当客户端发起基于秘钥的认证请求，服务端会先在主目录下寻找公钥，然后和客户端发送的公钥进行比较，如果两个公钥一致，则使用该公钥加密数据并发送给客户端，客户端收到该数据后，则用私钥解密再发送给服务器。

第一种认证方式需要知道密码口令，第二种认证方式则不需要登录连接时输入密码。

### 3.ssh基于秘钥认证的配置方法

​    1）ssh-keygen -t [rsa|dsa]用于生成公钥和私钥，生成id_rsa,id_rsa.pub或id_dsa,id_dsa.pub，存放于用户~/.ssh目录下

````
[root@servera ~] ssh-keygen 
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
50:b0:3f:5b:0f:8e:8a:b4:50:50:38:88:19:e1:9c:42 root@servera.example.com
The key's randomart image is:
+--[ RSA 2048]----+
|+E .. ...        |
|B +.   o         |
|.+..  o          |
|.  .   o         |
|    .   S o      |
|   .     * o     |
|  . .   o . .    |
|   o o .         |
|    o .          |
+-----------------+
[root@servera ~]# cd ~/.ssh/
[root@servera .ssh]# ls
authorized_keys  id_rsa  id_rsa.pub
[root@servera .ssh]# ll
total 12
-rw-------. 1 root root  803 Apr 18 09:28 authorized_keys
-rw-------. 1 root root 1675 Jul 22 06:56 id_rsa
-rw-r--r--. 1 root root  406 Jul 22 06:56 id_rsa.pub
````

​            id_rsa | id_dsa为私钥文件，需要存放在本地。

​            id_rsa.pub | id_dsa.pub为公钥文件，需要追加到远程被登录的主机上~/.ssh/authorized_keys文件中。



  2）ssh-copy-id指令，可使用该指令完成复制公钥的操作。

​            用法： ssh-copy-id 用户名@远程主机地址

    [root@servera ~] ssh-copy-id root@172.25.0.11
    The authenticity of host '172.25.0.11 (172.25.0.11)' can't be established.
    ECDSA key fingerprint is 6c:49:1a:9c:b8:9c:4c:91:20:f6:7f:9c:da:ef:57:7f.
    Are you sure you want to continue connecting (yes/no)? yes
    root@172.25.0.11's password: 输入远程root用户的密码
    
    Number of key(s) added: 1
    
    Now try logging into the machine, with:   "ssh 'root@172.25.0.11'"
    and check to make sure that only the key(s) you wanted were added.


​            使用该指令后，则会将本地生成的公钥内容追加进172.25.0.11该主机的/root/.ssh/authorized_keys文件中。

3）远程登录

````
[root@servera .ssh]# ssh root@172.25.0.11
Last login: Thu Aug  4 07:34:32 2016 from 172.25.0.10
````

  登录成功





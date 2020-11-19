# pscp转移文件

pscp可实现Windows和Linux服务器之间的远程文件传递

[pscp下载链接](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)

下载后放在`C:\Windows\system32`下(因为这个目录是命令行界面缺省路径,以后打开命令行界面可直接使用pscp命令)，或者命令行界面进入存放`pscp.exe`文件的路径

参数用法:  
      pscp [选项] [用户名@]主机:源 目标  
      pscp [选项] 源 [其他源...] [用户名@]主机:目标  
      pscp [选项] -ls [用户名@]主机:指定文件  
选项:  
  -V        显示版本信息后退出  
  -pgpfp    显示 PGP 密钥指纹后退出  
  -p        保留文件属性  
  -q        安静模式，不显示状态信息  
  -r        递归拷贝目录  
  -v        显示详细信息  
  -load 会话名  载入保存的会话信息  
  -P 端口   连接指定的端口  
  -l 用户名 使用指定的用户名连接  
  -pw 密码  使用指定的密码登录  
  -1 -2     强制使用 SSH 协议版本  
  -4 -6     强制使用 IPv4 或 IPv6 版本  
  -C        允许压缩  
  -i 密钥   认证使用的密钥文件  
  -noagent  禁用 Pageant 认证代理  
  -agent    启用 Pageant 认证代理  
  -hostkey aa:bb:cc:...  
            手动指定主机密钥(可能重复)  
  -batch    禁止所有交互提示  
  -proxycmd 命令  
            使用 '命令' 作为本地代理  
  -unsafe   允许服务端通配符(危险操作)  
  -sftp     强制使用 SFTP 协议  
  -scp      强制使用 SCP 协议  
  -sshlog 文件  
  -sshrawlog 文件 记录协议详细日志到指定文件  

实例：  
1.把Linux服务器上的/usr/soft目录内容取回Windows本地”d:\data\”目录

    pscp -r root@IP:/usr/soft/ d:\data

2.把Linux服务器上的/usr/file.txt文件取回到Windows本地”d:\data\”目录

    pscp root@IP:/usr/file.txt d:\data\

3.把Windows本地目录"d:\data"传输到Linux服务器的/usr/soft

    pscp -r d:\data\ root@IP:/usr/soft/

4.把Windows本地文件"d:\data\file.txt"传输到Linux服务器的/usr/soft

    pscp d:\data\file.txt root@IP:/usr/soft/

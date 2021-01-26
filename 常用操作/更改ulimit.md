# 更改ulimit

```shell
vim /etc/security/limits.conf

# End of file
# open files (-n) 最大文件打开数
* soft nofile 65535
* hard nofile 65535

# max user processes (-u) 最大进程打开数
* soft nproc 65535
* hard nproc 65535

# 如果修改后还没有改过来，则编辑下面文件
vim /etc/pam.d/login

session required /lib/security/pam_limits.so
```

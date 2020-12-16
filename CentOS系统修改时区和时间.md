# CentOS 系统修改时间和时区

## 修改时区

### 方法1

```shell
cp  /usr/share/zoneinfo/Asia/Shanghai  /etc/localtime
```

### 方法2

```shell
# 列出时区, 太多了, 可以根据实际情况过滤一下
timedatectl list-timezones
# 设置时区, 比如上海, 时间也会自动更改
timedatectl set-timezone Asia/Shanghai
```

### 方法3

```shell
# 输入命令按照提示选择时区
tzselect
# 查看是否成功
date
# 如果显示CST则说明时区设置成功
```

说明:

- CST：中国标准时间（China Standard Time）,这个解释可能是针对RedHat Linux.
- UTC：协调世界时,又称世界标准时间,简称UTC,从英文国际时间/法文协调时间"Universal Time/Temps Cordonn"而来.中国大陆、香港、澳门、台湾、蒙古国、新加坡、马来西亚、菲律宾、澳洲西部的时间与UTC的时差均为+8,也就是UTC+8.
- GMT：格林尼治标准时间（旧译格林威治平均时间或格林威治标准时间; 英语：Greenwich Mean Time,GMT）是指位于英国伦敦郊区的皇家格林尼治天文台的标准时间,因为本初子午线被定义在通过那里的经线.

设置完系统时间后,还需要同步到硬件时钟上

## 查看和修改时间

```shell
# 显示时间
date
# 修改时间
date -s  时间
# 如：设置当前时间为：2020年12月10点50分
date -s "2020-12-14 10:50:00"
# 根据网络同步时间
# 使用ntp同步标准时间,ntp：网络时间协议（network time protol）
yum install ntp
ntpdate pool.ntp.org
```

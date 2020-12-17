# CentOS 系统修改时间和时区

RedHat系Linux发行版 (CentOS/Fedora/RHEL等) 系统修改时间和时区方法的几种尝试,可以根据时区自动修正时间或者自定义时间.

具体RedHat系列有哪些Linux发行版, 请查看[GNU/Linux Distributions Timeline](https://upload.wikimedia.org/wikipedia/commons/1/1b/Linux_Distribution_Timeline.svg)

世界主要时区说明:

- CET: 欧洲中部时间（英語: Central European Time,CET）是一个时区名称,比世界标准时间（UTC）早一个小时,在大部分欧洲国家和部分北非国家采用.
- UTC: 协调世界时（英语: Coordinated Universal Time,法语: Temps Universel Coordonné,简称UTC）是最主要的世界时间标准,其以原子时秒长为基础,在时刻上尽量接近于格林威治标准时间.
- GMT: 格林尼治平均时间（英语: Greenwich Mean Time,GMT）是指位于英国伦敦郊区的皇家格林尼治天文台当地的平太阳时,因为本初子午线被定义为通过那里的经线.
- CST: (在中国通常指中国标准时间, 也就是北京时间)
  - 北京时间,China Standard Time
  - 中原标准时间,Chungyuan Standard Time
  - 澳洲中部时间,Central Standard Time (Australia)
  - 北美中部时区,Central Standard Time (North America)
  - 古巴标准时间,Cuba Standard Time

UTC=GMT
CET=UTC/GMT + 1小时
CST=UTC/GMT + 8小时
CST=CET+9

## 修改时区

### 方法1

cp  /usr/share/zoneinfo/Asia/Shanghai  /etc/localtime

```shell
[root@k8s-master manifests]# date
Thu Dec 17 08:29:48 EST 2020
[root@k8s-master manifests]# cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
cp: overwrite '/etc/localtime'? y
[root@k8s-master manifests]# date
Thu Dec 17 21:32:23 CST 2020
```

### 方法2

```shell
# 列出时区, 太多了, 可以根据实际情况过滤一下
timedatectl list-timezones
# 设置时区, 比如上海, 时间也会自动更改
[root@k8s-node1 ~]# date
Thu Dec 17 08:29:57 EST 2020
[root@k8s-node1 ~]# timedatectl list-timezones | grep Asia/Shanghai
Asia/Shanghai
[root@k8s-node1 ~]# timedatectl set-timezone Asia/Shanghai
[root@k8s-node1 ~]# date
Thu Dec 17 21:33:19 CST 2020
```

### 方法3

```shell
# 输入命令按照提示选择时区
tzselect
# 查看是否成功
date
# 如果显示CST则说明时区设置成功
[root@k8s-node2 ~]# date
Thu Dec 17 08:30:01 EST 2020
[root@k8s-node2 ~]# tzselect
Please identify a location so that time zone rules can be set correctly.
Please select a continent, ocean, "coord", or "TZ".
 1) Africa
 2) Americas
 3) Antarctica
 4) Asia
 5) Atlantic Ocean
 6) Australia
 7) Europe
 8) Indian Ocean
 9) Pacific Ocean
10) coord - I want to use geographical coordinates.
11) TZ - I want to specify the time zone using the Posix TZ format.
#? 4
Please select a country whose clocks agree with yours.
 1) Afghanistan           18) Israel                35) Palestine
 2) Armenia               19) Japan                 36) Philippines
 3) Azerbaijan            20) Jordan                37) Qatar
 4) Bahrain               21) Kazakhstan            38) Russia
 5) Bangladesh            22) Korea (North)         39) Saudi Arabia
 6) Bhutan                23) Korea (South)         40) Singapore
 7) Brunei                24) Kuwait                41) Sri Lanka
 8) Cambodia              25) Kyrgyzstan            42) Syria
 9) China                 26) Laos                  43) Taiwan
10) Cyprus                27) Lebanon               44) Tajikistan
11) East Timor            28) Macau                 45) Thailand
12) Georgia               29) Malaysia              46) Turkmenistan
13) Hong Kong             30) Mongolia              47) United Arab Emirates
14) India                 31) Myanmar (Burma)       48) Uzbekistan
15) Indonesia             32) Nepal                 49) Vietnam
16) Iran                  33) Oman                  50) Yemen
17) Iraq                  34) Pakistan
#? 9
Please select one of the following time zone regions.
1) Beijing Time
2) Xinjiang Time
#? 1

The following information has been given:

        China
        Beijing Time

Therefore TZ='Asia/Shanghai' will be used.
Selected time is now:   Thu Dec 17 21:35:31 CST 2020.
Universal Time is now:  Thu Dec 17 13:35:31 UTC 2020.
Is the above information OK?
1) Yes
2) No
#? 1

You can make this change permanent for yourself by appending the line
        TZ='Asia/Shanghai'; export TZ
to the file '.profile' in your home directory; then log out and log in again.

Here is that TZ value again, this time on standard output so that you
can use the /usr/bin/tzselect command in shell scripts:
Asia/Shanghai
[root@k8s-node2 ~]# date
Thu Dec 17 08:35:50 EST 2020
# 按照提示导入环境变量才可生效
[root@k8s-node2 ~]# TZ='Asia/Shanghai'; export TZ
[root@k8s-node2 ~]# date
Thu Dec 17 21:36:28 CST 2020
```

设置完系统时间后,还需要同步到硬件时钟上

## 查看和修改时间

```shell
# 显示时间
date
# 修改时间
date -s  时间
# 如: 设置当前时间为: 2020年12月10点50分
date -s "2020-12-17 21:50:00"
# 根据网络同步时间
# 使用ntp同步标准时间,ntp: 网络时间协议（network time protol）
yum install ntp
ntpdate pool.ntp.org
```

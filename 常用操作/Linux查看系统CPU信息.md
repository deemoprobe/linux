# Linux查看系统CPU信息

```shell
# 总核数 = 物理CPU个数 X 每颗物理CPU的核数
# 总逻辑CPU数 = 物理CPU个数 X 每颗物理CPU的核数 X 超线程数

# 查看物理CPU个数
cat /proc/cpuinfo | grep "physical id" | uniq | wc -l
or
grep "physical id" /proc/cpuinfo | uniq | wc -l

# 查看每个物理CPU中core的个数(即核数)
cat /proc/cpuinfo| grep "cpu cores"| cut -f2 -d: | uniq
or
grep "cpu cores" /proc/cpuinfo | cut -f2 -d: | uniq

# 查看逻辑CPU的个数
cat /proc/cpuinfo | grep "processor"| wc -l
or
grep ^processor /proc/cpuinfo | wc -l

# 查看CPU型号
cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq
or
grep name /proc/cpuinfo | cut -f2 -d: | uniq

# 是否开启intel的超线程技术(HT)，如果有两个逻辑CPU具有相同的"core id"，那么超线程是打开的。可以根据以下原则,来判断是否支持HT技术。
# 如果"siblings"和"cpu cores"一致，则说明不支持超线程，或者超线程未打开。
# 如果"siblings"是"cpu cores"的两倍，则说明支持超线程，并且超线程已打开。

cat /proc/cpuinfo | grep "sibling" | uniq
or
grep sibling /proc/cpuinfo | cut -f2 -d: | uniq

cat /proc/cpuinfo | grep "cpu cores" | uniq

# 查看CPU运行时位数
getconf LONG_BIT
# 注意：如果结果是32，代表是运行在32位模式下，但不代表CPU不支持64bit。

cat /proc/cpuinfo | grep flags | grep lm | wc -l
or
grep flags /proc/cpuinfo | grep lm | wc -l
# (结果大于0, 说明支持64bit计算. lm指long mode, 支持lm则是64bit)

# 查看CPU位数
uname -m

```

# 日常遇到的问题汇总（不分类）

1. Apache编译时出错`aclocal-1.15' is missing on your system`

    ```shell
    #进入configure所在目录,执行下面自动编译修复语句
    autoreconf -f -i
    # 然后再依次执行./configure, make, make install
    ```

2. 编译pcre时报错`rm: cannot remove 'libtoolT': No such file or directory`

    ```shell
    vi configure
    Change the line
    $RM "$cfgfile"
    to
    $RM -f "$cfgfile"
    This will resolve the error
    ```

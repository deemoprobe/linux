# Linux 和 Windows 下部署JDK环境

本文主要记录环境变量的配置，安装太简单就不记录了。

## Windows下部署JDK环境

主要是Windows10之前的系统或者服务器  
1.下载安装JDK  
2.配置环境变量，右键左下角Win图标，系统-->高级系统设置-->环境变量  
    变量名：JAVA_HOME  
    变量值：D:\app\Java\jdk1.8.0_151  
    变量名：CLASSPATH  
    变量值：.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;  
3.找到Path变量  
    在变量值最开始加入  
    %JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;  
4.打开CMD输入“java -version” 和 “javac” 命令验证

## Linux下部署JDK环境

```shell
vi ~/.bash_profile

export JAVA_HOME=/app/jdk1.6.0_45
export JRE_HOME=/app/jdk1.6.0_45/jre
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
export PATH=$JAVA_HOME/bin:$PATH

source ~/.bash_profile
```

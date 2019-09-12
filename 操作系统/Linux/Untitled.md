## JDK安装

```
//创建文件夹
mkdir -p /usr/local/java

//将压缩包放入/usr/local/java

//解压jdk
tar -xzvf jdk-8u181-linux-x64.tar.gz

//设置环境变量
vim /etc/profile

export JAVA_HOME=/usr/local/java/jdk1.8.0_181
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

/////////////////////////////////////////////////////

export JAVA_HOME=/usr/local/jdk1.8.0_162
export JRE_HOME=/usr/local/jdk1.8.0_162/jre
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib:$CLASSPATH
export PATH=$JAVA_HOME/bin:$PATH


//执行profile文件
source /etc/profile

//检查jdk
java -version
```
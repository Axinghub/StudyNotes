---
typora-copy-images-to: images
typora-root-url: ./
---

## 日常报错请参考

https://blog.csdn.net/qq_21387171/article/details/53577115

## elasticsearch安装

参考：https://blog.csdn.net/luo1544943710/article/details/93196147

1. 下载资源

   ```shell
   wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.1.0-linux-x86_64.tar.gz
   ```

2. 解压资源文件

   ```shell
   tar -czvf elasticsearch-7.1.0-linux-x86_64.tar.gz
   ```

3. 新建data目录

   ```shell
   mkdir data
   ```

4. 修改配置文件config/elasticsearch.yml

   ```shell
   # ======================== Elasticsearch Configuration =========================
   #
   # NOTE: Elasticsearch comes with reasonable defaults for most settings.
   #       Before you set out to tweak and tune the configuration, make sure you
   #       understand what are you trying to accomplish and the consequences.
   #
   # The primary way of configuring a node is via this file. This template lists
   # the most important settings you may want to configure for a production cluster.
   #
   # Please consult the documentation for further information on configuration options:
   # https://www.elastic.co/guide/en/elasticsearch/reference/index.html
   #
   # ---------------------------------- Cluster -----------------------------------
   #
   # Use a descriptive name for your cluster:
   #
   cluster.name: my-application
   #
   # ------------------------------------ Node ------------------------------------
   #
   # Use a descriptive name for the node:
   #
   node.name: node-1
   #
   # Add custom attributes to the node:
   #
   #node.attr.rack: r1
   
   #
   # ----------------------------------- Paths ------------------------------------
   #
   # Path to directory where to store the data (separate multiple locations by comma):
   #
   #path.data: /path/to/data
   path.data: /usr/local/elasticsearch/elasticsearch-7.1.0/data
   #
   # Path to log files:
   #
   #path.logs: /path/to/logs
   path.logs: /usr/local/elasticsearch/elasticsearch-7.1.0/logs
   #
   # ----------------------------------- Memory -----------------------------------
   #
   # Lock the memory on startup:
   #
   #bootstrap.memory_lock: true
   #
   # Make sure that the heap size is set to about half the memory available
   # on the system and that the owner of the process is allowed to use this
   # limit.
   #
   # Elasticsearch performs poorly when the system is swapping the memory.
   #
   # ---------------------------------- Network -----------------------------------
   #
   # Set the bind address to a specific IP (IPv4 or IPv6):
   #
   #network.host: 192.168.0.1
   network.host: 0.0.0.0
   #
   
   #
   # Set a custom port for HTTP:
   #
   http.port: 9200
   #
   # For more information, consult the network module documentation.
   #
   # --------------------------------- Discovery ----------------------------------
   #
   # Pass an initial list of hosts to perform discovery when this node is started:
   # The default list of hosts is ["127.0.0.1", "[::1]"]
   #
   #discovery.seed_hosts: ["host1", "host2"]
   #
   # Bootstrap the cluster using an initial set of master-eligible nodes:
   #
   #cluster.initial_master_nodes: ["node-1", "node-2"]
   cluster.initial_master_nodes: ["node-1"]
   #
   # For more information, consult the discovery and cluster formation module documentation.
   #
   # ---------------------------------- Gateway -----------------------------------
   #
   # Block initial recovery after a full cluster restart until N nodes are started:
   #
   #gateway.recover_after_nodes: 3
   #
   # For more information, consult the gateway module documentation.
   #
   # ---------------------------------- Various -----------------------------------
   #
   # Require explicit names when deleting indices:
   #
   #action.destructive_requires_name: true
   
   ```

5. 进入bin目录启动es

   ```shell
   ./elasticsearch
   ```

6. 错误提示

   ```shell
   OpenJDK 64-Bit Server VM warning: Option UseConcMarkSweepGC was deprecated in version 9.0 and will likely be removed in a future release.
   OpenJDK 64-Bit Server VM warning: INFO: os::commit_memory(0x00000000c5330000, 986513408, 0) failed; error='Not enough space' (errno=12)
   #
   # There is insufficient memory for the Java Runtime Environment to continue.
   # Native memory allocation (mmap) failed to map 986513408 bytes for committing reserved memory.
   # An error report file with more information is saved as:
   # logs/hs_err_pid29282.log
   
   ```

7. 这是由于云服务器内存太小，elasticsearch使用java的jvm默认是使用1G的内存的，这里修改一下修./config/jvm.options配置就行了

   ```shell
   -Xms200m
   -Xmx200m
   ```

   

8. 在次启动时报错

   ```shell
   OpenJDK 64-Bit Server VM warning: Option UseConcMarkSweepGC was deprecated in version 9.0 and will likely be removed in a future release.
   [2019-07-21T01:35:53,413][WARN ][o.e.b.ElasticsearchUncaughtExceptionHandler] [node-1] uncaught exception in thread [main]
   org.elasticsearch.bootstrap.StartupException: java.lang.RuntimeException: can not run elasticsearch as root
   	at org.elasticsearch.bootstrap.Elasticsearch.init(Elasticsearch.java:163) ~[elasticsearch-7.1.0.jar:7.1.0]
   	at org.elasticsearch.bootstrap.Elasticsearch.execute(Elasticsearch.java:150) ~[elasticsearch-7.1.0.jar:7.1.0]
   	at org.elasticsearch.cli.EnvironmentAwareCommand.execute(EnvironmentAwareCommand.java:86) ~[elasticsearch-7.1.0.jar:7.1.0]
   	at org.elasticsearch.cli.Command.mainWithoutErrorHandling(Command.java:124) ~[elasticsearch-cli-7.1.0.jar:7.1.0]
   	at org.elasticsearch.cli.Command.main(Command.java:90) ~[elasticsearch-cli-7.1.0.jar:7.1.0]
   	at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:115) ~[elasticsearch-7.1.0.jar:7.1.0]
   	at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:92) ~[elasticsearch-7.1.0.jar:7.1.0]
   Caused by: java.lang.RuntimeException: can not run elasticsearch as root
   	at org.elasticsearch.bootstrap.Bootstrap.initializeNatives(Bootstrap.java:102) ~[elasticsearch-7.1.0.jar:7.1.0]
   	at org.elasticsearch.bootstrap.Bootstrap.setup(Bootstrap.java:169) ~[elasticsearch-7.1.0.jar:7.1.0]
   	at org.elasticsearch.bootstrap.Bootstrap.init(Bootstrap.java:325) ~[elasticsearch-7.1.0.jar:7.1.0]
   	at org.elasticsearch.bootstrap.Elasticsearch.init(Elasticsearch.java:159) ~[elasticsearch-7.1.0.jar:7.1.0]
   	... 6 more
   ```

9. 这是不能使用root用户操作，添加一个其他的用户再试试：

   ```shell
   adduser es
   passwd es
   
   #改一下es目录所属用户
   chown es /usr/local/elasticsearch/elasticsearch-7.1.0/ -R
   
   #vim 编辑 /etc/security/limits.conf，在末尾加上：
   es soft nofile 65536
   es hard nofile 65536
   es soft nproc 4096
   es hard nproc 4096
   
   #vim 编辑 vim /etc/security/limits.d/20-nproc.conf，将* 改为用户名（es）：
   es          soft    nproc     4096
   
   #vim 编辑 /etc/sysctl.conf，在末尾加上：
   vm.max_map_count = 655360
   
   #执行：
   sysctl -p
   
   #登录刚才新建的es用户，并启动elasticsearch，OK
   su es
   ./bin/elasticsearch
   
   ```

10. 后台启动

    ```shell
    ./bin/elasticsearch -d
    ```

11. 查看进程

    ```shell
    ps -ef|grep elasticsearch
    ```

12. 访问入口

    ```shell
    http://123.206.135.16:9200/
    
    #结果
    {
      "name" : "node-1",
      "cluster_name" : "my-application",
      "cluster_uuid" : "K2T2R226SEeVdZvVDm_5jw",
      "version" : {
        "number" : "7.1.0",
        "build_flavor" : "default",
        "build_type" : "tar",
        "build_hash" : "606a173",
        "build_date" : "2019-05-16T00:43:15.323135Z",
        "build_snapshot" : false,
        "lucene_version" : "8.0.0",
        "minimum_wire_compatibility_version" : "6.8.0",
        "minimum_index_compatibility_version" : "6.0.0-beta1"
      },
      "tagline" : "You Know, for Search"
    }
    ```

    

## 基于第8点启动时报错的补充

参考：https://blog.csdn.net/wozniakzhang/article/details/96213281

创建一个非root用户来解决这个问题

```shell
#创建用户组
groupadd esgroup
#创建用户并设置密码
useradd esuser -g esgroup -p espassword
#给esuser用户授权(对文件夹循环授权,命令不作过多阐述)不授权后面启动会报jvm.options授权异常(报错Exception in thread "main" java.nio.file.AccessDeniedException: /usr/local/elasticsearch/elasticsearch-7.1.0/config/jvm.options)
chown -R esuser:esgroup /user/local/elasticsearch/elasticsearch-7.1.0
#切换用户
su esuser
#启动es
./elasticsearch
#看到以下标识这段话启动正常了
# license [44e05c9c-2cb2-4e07-9cc4-627142e154b1] mode [basic] - valid
```

    这样基本是就ok了,但是实际情况是要让外网


 ​这个时候需要配置文件elasticsearch.yml发挥作用了,进入/usr/local/elasticsearch/elasticsearch-7.1.0/config,使用记事本等打开,在elasticsearch.yml中(去注释后保证代码顶格,这样才规范)
    

```shell

cluster.name: my-application    #放开注释
node.name: node-1    #放开注释
network.host: 192.168.69.110  #放开IP注释
http.port: 9200    #放开端口注释
cluster.initial_master_nodes: ["node-1"]    #集群所有节点

```
 启动  ./elasticsearch报错，(Linux环境默认分配资源不足以满足elasticsearch导致的,毕竟是大数据相关框架)

```shell
ERROR: [4] bootstrap checks failed
[1]: max file descriptors [4096] for elasticsearch process is too low, increase to at least [65535]
[2]: max number of threads [3795] for user [esuser] is too low, increase to at least [4096]
[3]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
[4]: the default discovery settings are unsuitable for production use; at least one of [discovery.seed_hosts, discovery.seed_providers, cluster.initia

```


 解决如下：
    
```shell
#(切换为root用户才可以操作这些命令)
su root

#进入以下路径/etc/security找到limits.conf进行编辑,在# End of file前(注意,不要以为是注释的就无所谓),*代表用户,esuser也行,添加：

* soft nofile 65536
* hard nofile 65536
* soft nproc 4096
* hard nproc 4096

#使配置生效(执行以下命令后会有4个bash: elasticsearch: 未找到命令,忽略它)
source /etc/security/limits.conf 

#编辑/etc/sysctl.conf,在文本最后添加
vm.max_map_count=655360
fs.file-max=655360
#使配置生效
sysctl -p

#切换为普通账户elasticsearch
su esuser

#最后进入bin目录再次启动
./elasticsearch
```
 ## 安装查看插件

    ```shell
    #查看
    ./elasticsearch-plugin  list
    http://123.206.135.16:9200/_cat/plugins
    #安装
    ./elasticsearch-plugin  install analysis-icu

## Kibana下载安装

1. 下载：

   ```shell
   wget https://artifacts.elastic.co/downloads/kibana/kibana-7.1.0-linux-x86_64.tar.gz
   ```

2. 解压

   ```shell
   tar -xzvf kibana-7.1.0-linux-x86_64.tar.gz
   ```

3. 授权用户

   ```shell
   chown es /usr/local/kibana/kibana-7.1.0-linux-x86_64/ -R
   
   //chown -R esuser:esgroup /opt/software/kibana
   ```

4. 修改配置文件config/kibana.yml

   ```shell
   # Kibana is served by a back end server. This setting specifies the port to use.
   server.port: 5601
   
   # Specifies the address to which the Kibana server will bind. IP addresses and host names are both valid values.
   # The default is 'localhost', which usually means remote machines will not be able to connect.
   # To allow connections from remote users, set this parameter to a non-loopback address.
   server.host: "0.0.0.0"
   
   # Enables you to specify a path to mount Kibana at if you are running behind a proxy.
   # Use the `server.rewriteBasePath` setting to tell Kibana if it should remove the basePath
   # from requests it receives, and to prevent a deprecation warning at startup.
   # This setting cannot end in a slash.
   #server.basePath: ""
   
   # Specifies whether Kibana should rewrite requests that are prefixed with
   # `server.basePath` or require that they are rewritten by your reverse proxy.
   # This setting was effectively always `false` before Kibana 6.3 and will
   # default to `true` starting in Kibana 7.0.
   #server.rewriteBasePath: false
   
   # The maximum payload size in bytes for incoming server requests.
   #server.maxPayloadBytes: 1048576
   
   # The Kibana server's name.  This is used for display purposes.
   #server.name: "your-hostname"
   
   # The URLs of the Elasticsearch instances to use for all your queries.
   elasticsearch.hosts: ["http://123.206.135.16:9200"]
   
   # When this setting's value is true Kibana uses the hostname specified in the server.host
   # setting. When the value of this setting is false, Kibana uses the hostname of the host
   # that connects to this Kibana instance.
   #elasticsearch.preserveHost: true
   
   # Kibana uses an index in Elasticsearch to store saved searches, visualizations and
   # dashboards. Kibana creates a new index if the index doesn't already exist.
   #kibana.index: ".kibana"
   
   # The default application to load.
   #kibana.defaultAppId: "home"
   
   # If your Elasticsearch is protected with basic authentication, these settings provide
   # the username and password that the Kibana server uses to perform maintenance on the Kibana
   # index at startup. Your Kibana users still need to authenticate with Elasticsearch, which
   # is proxied through the Kibana server.
   #elasticsearch.username: "user"
   #elasticsearch.password: "pass"
   
   # Enables SSL and paths to the PEM-format SSL certificate and SSL key files, respectively.
   # These settings enable SSL for outgoing requests from the Kibana server to the browser.
   #server.ssl.enabled: false
   #server.ssl.certificate: /path/to/your/server.crt
   #server.ssl.key: /path/to/your/server.key
   
   # Optional settings that provide the paths to the PEM-format SSL certificate and key files.
   # These files validate that your Elasticsearch backend uses the same key files.
   #elasticsearch.ssl.certificate: /path/to/your/client.crt
   #elasticsearch.ssl.key: /path/to/your/client.key
   
   # Optional setting that enables you to specify a path to the PEM file for the certificate
   # authority for your Elasticsearch instance.
   #elasticsearch.ssl.certificateAuthorities: [ "/path/to/your/CA.pem" ]
   
   # To disregard the validity of SSL certificates, change this setting's value to 'none'.
   #elasticsearch.ssl.verificationMode: full
   
   # Time in milliseconds to wait for Elasticsearch to respond to pings. Defaults to the value of
   # the elasticsearch.requestTimeout setting.
   #elasticsearch.pingTimeout: 1500
   
   # Time in milliseconds to wait for responses from the back end or Elasticsearch. This value
   # must be a positive integer.
   #elasticsearch.requestTimeout: 30000
   
   # List of Kibana client-side headers to send to Elasticsearch. To send *no* client-side
   # headers, set this value to [] (an empty list).
   #elasticsearch.requestHeadersWhitelist: [ authorization ]
   
   # Header names and values that are sent to Elasticsearch. Any custom headers cannot be overwritten
   # by client-side headers, regardless of the elasticsearch.requestHeadersWhitelist configuration.
   #elasticsearch.customHeaders: {}
   
   # Time in milliseconds for Elasticsearch to wait for responses from shards. Set to 0 to disable.
   #elasticsearch.shardTimeout: 30000
   
   # Time in milliseconds to wait for Elasticsearch at Kibana startup before retrying.
   #elasticsearch.startupTimeout: 5000
   
   # Logs queries sent to Elasticsearch. Requires logging.verbose set to true.
   #elasticsearch.logQueries: false
   
   # Specifies the path where Kibana creates the process ID file.
   #pid.file: /var/run/kibana.pid
   
   # Enables you specify a file where Kibana stores log output.
   #logging.dest: stdout
   
   # Set the value of this setting to true to suppress all logging output.
   #logging.silent: false
   
   # Set the value of this setting to true to suppress all logging output other than error messages.
   #logging.quiet: false
   
   # Set the value of this setting to true to log all events, including system usage information
   # and all requests.
   #logging.verbose: false
   
   # Set the interval in milliseconds to sample system and process performance
   # metrics. Minimum is 100ms. Defaults to 5000.
   #ops.interval: 5000
   
   # Specifies locale to be used for all localizable strings, dates and number formats.
   #i18n.locale: "en"
   
   ```

5. 切换用户

   ```shell
   su es
   su esuser
   ```

6. 启动

   ```shell
   nohup ./kibana &
   ```

   7. **关闭使用(注意是node)**

        ```shell
      ps -ef | grep node
        ```


8. 返回的进程第一行的进程号可以用于关闭,比如

   ```shell
   kill -9 8558
   ```

9. 访问http://123.206.135.16:5601/


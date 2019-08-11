---
typora-copy-images-to: images
typora-root-url: ./
---

## docker安装：

参考

https://www.xttblog.com/?p=4402

https://www.xttblog.com/?p=4404

https://www.xttblog.com/?p=4408

## elasticsearch exited with code 78

如果您查看整个日志消息，您会找到类似的行

```
elasticsearch     | [1]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
```

因此我们需要增加`vm.max_map_count`限制：

```
sudo sysctl -w vm.max_map_count = 524288
```

如果sysctl -w无效的话，直接vi /etc/sysctl.conf这个文件进行修改。

```shell
vm.max_map_count=524288
#运行
sysctl -p
```



## **docker运行：**

sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

**docker加速器：**https://blog.csdn.net/dingsai88/article/details/52638758

curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://f1361db2.m.daocloud.io

sudo systemctl restart docker

**启动ES**

```
cd docker-es-7.2
docker-compose up
docker-compose up -d   #后台启动
```

## 安装logstash

下载并解压

链接: https://pan.baidu.com/s/1ezBh9WsRp9Jb4FTo_ByNxQ 提取码: jhwi 复制这段内容后打开百度网盘手机App，操作更方便哦

```
wget https://artifacts.elastic.co/downloads/logstash/logstash-7.2.0.tar.gz
tar -xzvf logstash-7.2.0.tar.gz
```

修改logstash配置文件

```
input {
  file {
    path => "/Users/yiruan/dev/elk7/logstash-7.0.1/bin/movies.csv"
    start_position => "beginning"
    sincedb_path => "/dev/null"
  }
}
filter {
  csv {
    separator => ","
    columns => ["id","content","genre"]
  }

  mutate {
    split => { "genre" => "|" }
    remove_field => ["path", "host","@timestamp","message"]
  }

  mutate {

    split => ["content", "("]
    add_field => { "title" => "%{[content][0]}"}
    add_field => { "year" => "%{[content][1]}"}
  }

#  mutate {

#    gsub => [
#      
#      "year", "\\)", ""
#    ]
#  }


  mutate {
    convert => {
      "year" => "integer"
    }
    strip => ["title"]
    remove_field => ["path", "host","@timestamp","message","content"]
  }



}
output {
   elasticsearch {
     hosts => "http://localhost:9200"
     index => "movies"
     document_id => "%{id}"
   }
  stdout {}
}

```

启动

```
./logstash -f ../config/logstash.conf
```




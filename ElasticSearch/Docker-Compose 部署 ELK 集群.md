# Docker-Compose 部署 ELK 集群

[toc]

## 1. ELK集群部署

### 1.1 创建 docker-compose.yml

docker-compose.yml

``` yaml version: '2.2'
# docker-compose.yml
services:
  cerebro:
    image: lmenezes/cerebro:0.8.3
    container_name: cerebro
    ports:
      - "9000:9000"
    command:
      - -Dhosts.0.host=http://elasticsearch:9200
    networks:
      - es7net
  kibana:
    image: docker.elastic.co/kibana/kibana:7.1.0
    container_name: kibana7
    environment:
      - I18N_LOCALE=zh-CN
      - XPACK_GRAPH_ENABLED=true
      - TIMELION_ENABLED=true
      - XPACK_MONITORING_COLLECTION_ENABLED="true"
    ports:
      - "5601:5601"
    networks:
      - es7net
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.1.0
    container_name: es7_01
    environment:
      - cluster.name=testcluster
      - node.name=es7_01
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      - discovery.seed_hosts=es7_01,es7_02
      - cluster.initial_master_nodes=es7_01,es7_02
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - es7data1:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
    networks:
      - es7net
  elasticsearch2:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.1.0
    container_name: es7_02
    environment:
      - cluster.name=testcluster
      - node.name=es7_02
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      - discovery.seed_hosts=es7_01,es7_02
      - cluster.initial_master_nodes=es7_01,es7_02
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - es7data2:/usr/share/elasticsearch/data
    networks:
      - es7net
  logstash:
    image: docker.elastic.co/logstash/logstash:7.1.0
    container_name: logstash7
    volumes:
      - ./logstash/config/logstash.yml:/usr/share/logstash/config/logstash.yml:ro
      - ./logstash/pipeline/logstash.conf:/usr/share/logstash/pipeline/logstash.conf:ro
      - ./logstash/pipeline:/usr/share/logstash/pipeline:ro
      - ./logstash/data/movies.csv:/usr/share/logstash/data/movies.csv
    ports:
      - "5000:5000/tcp"
      - "5000:5000/udp"
      - "9600:9600"
    environment:
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    networks:
      - es7net
    depends_on:
      - elasticsearch
      - elasticsearch2
volumes:
  es7data1:
    driver: local
  es7data2:
    driver: local

networks:
  es7net:
    driver: bridge
```

启动es集群

``` shell
# 进入 docker-compose.yml 文件所在目录
# 运行 docker-compose 启动指令
docker-compose up 
# docker-compose 关闭指令
docker-compose down
```

### 1.2 LogStash 目录映射

创建 logstash 目录

``` shell
sudo mkdir -p ./logstash/config
sudo mkdir -p ./logstash/pipeline
sudo mkdir -p ./logstash/data
```

创建 logstash.yml 配置文件

``` yml
# 目录: ./logstash/config/logstash.yml 
http.host: 0.0.0.0
```

创建 logstash.conf 配置文件

``` json
// 目录: ./logstash/pipeline/logstash.conf
// 素材数据 movies.csv
input {
  file {
    path => "/usr/share/logstash/data/movies.csv" // 素材数据在容器内的目录
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
     hosts => "http://elasticsearch:9200" // es 集群地址
     index => "movies"
     document_id => "%{id}"
   }
  stdout {}
}

```



### 1.3  注意事项

1. 虚拟内存过小导致 elasticsearch 启动失败  

``` shell
# 报错信息
max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]

# 解决方法
vim /etc/sysctl.conf
# 添加参数
vm.max_map_count=262144
# 保存后执行
sysctl -p 
# 重启电脑
```

2. logstash.conf 配置文件格式须严格按照要求编写，否则 LogStash 会启动失败

```  
   // 异常信息
[2022-08-02T10:05:09,084][ERROR][logstash.agent           ] Failed to execute action {:action=>LogStash::PipelineAction::Create/pipeline_id:main, :exception=>"LogStash::ConfigurationError", :message=>"Expected one of #, input, filter, output at line 1, column 1 (byte 1)", :backtrace=>["/usr/share/logstash/logstash-core/lib/logstash/compiler.rb:41:in `compile_imperative'", "/usr/share/logstash/logstash-core/lib/logstash/compiler.rb:49:in `compile_graph'", "/usr/share/logstash/logstash-core/lib/logstash/compiler.rb:11:in `block in compile_sources'", "org/jruby/RubyArray.java:2577:in `map'", "/usr/share/logstash/logstash-core/lib/logstash/compiler.rb:10:in `compile_sources'", "org/logstash/execution/AbstractPipelineExt.java:151:in `initialize'", "org/logstash/execution/JavaBasePipelineExt.java:47:in `initialize'", "/usr/share/logstash/logstash-core/lib/logstash/java_pipeline.rb:23:in `initialize'", "/usr/share/logstash/logstash-core/lib/logstash/pipeline_action/create.rb:36:in `execute'", "/usr/share/logstash/logstash-core/lib/logstash/agent.rb:325:in `block in converge_state'"]}
logstash7         | [2022-08-02T10:05:09,251][INFO ][logstash.runner          ] Logstash shut down.
```

   解决方法：

```shell
   vim ./logstash.conf
   # vim 指令
   # 设置文件编码为 UTF-8 
   :set fileencoding=utf-8
   # 设置 无bom 
   :set nobomb
   # 保存退出
   :wq
```

   


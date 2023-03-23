# Docker 安装 elasticsearch

[toc]

## 1. 下载 ElasticSearch 镜像

``` shell
# 从 docker 拉去 ElasticSearch 镜像
docker pull elasticsearch:8.3.2
```

## 2. 设置 docker 容器挂载目录

``` shell
sudo mkdir -p /opt/elasticsearch/config
sudo mkdir -p /opt/elasticsearch/data
sudo mkdir -p /opt/elasticsearch/plugins
```

## 3. 创建 ElasticSearch 容器

``` shell
sudo docker run --name es7.17.5 -p 9200:9200 -p 9300:9300 \
-e "discovery.type=single-node" \
-e ES_JAVA_OPTS="-Xms84m -Xmx512m" \
-v /opt/elasticsearch/config/elasticsearch.yml:/usr/share/elasticse[Barch/config/elasticsearch.yml \
-v /opt/elasticsearch/data:/usr/share/elasticsearch/data \
-v /opt/elasticsearch/plugins/:/usr/share/elasticsearch/plugins \
-d elasticsearch:7.17.5  #8.3.2
```

## 4. 默认账户信息

```shell
user: elastic
password: PT7B6SKNOFGqpdya2Rb4
```

## 5. 密码操作

``` shell
# 重置密码
docker exec -it esname /usr/share/elasticsearch/bin/elasticsearch-reset-password -u username 

# 修改密码
docker exec -it --user root elasticsearch /usr/share/elasticsearch/bin/elasticsearch-reset-password --username elastic -i
```

## 6. 安装插件

### 6.1. 安装 icu 分词器插件

``` shell
# 安装 elasticsearch-icu 分词器
./bin/elasticsearch-plugin install analysis-icu 

# 查看插件列表
./bin/elasticsearch-plugin list
```

### 6.2. 安装 ik 分词器插件

```shell
# 安装 ik 分词器
./bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.1.0/elasticsearch-analysis-ik-7.1.0.zip
```

使用 ik 分词插件

```shell
POST _analyze
{
  "analyzer": "ik_smart",
  "text": "他说的确实在理”"
}

POST _analyze
{
  "analyzer": "ik_max_word",
  "text": "他说的确实在理”"
}
```


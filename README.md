环境要求：grafana + elasticsearch + filebeat + logstash + redis(或者kafka + zookeeper)

流程：nginx产生日志，filebeat收集日志到kafka，logstash再从kafka消费，最终输出到elasticsearch；grafana以elasticsearch为数据源，通过仪表盘展示出来

# GeoLite2
这里用到了`GeoLite2`，通过ip地址获取国家，地区，经纬度等信息

需要手动下载：https://github.com/wp-statistics/GeoLite2-City

文件保存在这个位置：`logstash/GeoLite2-City.mmdb`

# 如果使用redis
此项目在`filebeat`和`logstash`中间使用了`kafka`(`kafka`依赖`zookeeper`)，如果想换成`redis`，需修改以下文件

logstash/pipeline/logstash.conf
```
vi logstash/pipeline/logstash.conf
input {
  # redis nginx key
  redis {
    data_type =>"list"
    key =>"nginx_logs"
    host =>"docker-redis"
    port => 6379
    db => 0
  }
}
```

filebeat/filebeat.yml
```
vi filebeat/filebeat.yml
#-------------------------- Redis output ------------------------------
output.redis:
  enabled: true
  hosts: ["docker-redis:6379"]
#  password: "password"
  key: "nginx_logs"   #redis中日志数据的key值ֵ
  db: 0
  timeout: 5
```


# 启动服务
```
docker-compose up -d
```

# 访问 Grafana

http://localhost:3000

# 文章

http://www.cuiwei.net/p/1021090107
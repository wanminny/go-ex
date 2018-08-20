
how to use ！


0. 设置 ./etc/river.toml;

1. root path

./cmd/go-mysql-elasticsearch/main -config=./etc/river.toml
启动后会首先全量mysqldump数据到es;

3. 启动 es
   elasticsearch

   查看索引
   http://localhost:9200/_cat/indices?v

   看到会有新增的索引以及状态；count;

   查看索引数据：
   http://localhost:9200/supplier/relation/379528
   http://localhost:9200/supplier/relation/1
   查看index = supplier type = relation的第几条数据；

4. 修改数据后可以查看 数据在es的更改；

http://localhost:9200/supplier/relation/1
比如修改第一条数据；注意 后面的数字是 id ；对应在es的顺序；

5. 删除 后查看会有对应的体现：
    http://localhost:9200/_cat/indices?v

   yellow open   supplier               XhATpWEQSFKCJOOSuZu0OQ   5   1         10            2       33kb           33kb

   http://localhost:9200/supplier/relation/379528 也不存在了；

6. insert 后  http://localhost:9200/_cat/indices?v 可以看出有体现

    yellow open   supplier               XhATpWEQSFKCJOOSuZu0OQ   5   1         10            3     38.1kb         38.1kb

    同时 http://localhost:9200/supplier/relation/3 中间也有体现！

7. http://127.0.0.1:12800/stat 可以监控状态；


8 总结：
  如果是第一次启动该程序，首先使用mysqldump工具对源mysql数据库进行一次全量同步，通过elasticsearch client执行操作写入数据到ES；
  然后实现了一个mysql client,作为slave连接到源mysql,
  源mysql作为master会将所有数据的更新操作通过binlog event同步给slave， 通过解析binlog event就可以获取到数据的更新内容，之后写入到ES.
  另外，该工具还提供了操作统计的功能，每当有数据增删改操作时，会将对应操作的计数加1，程序启动时会开启一个http服务，通过调用http接口可以查看增删改操作的次数。





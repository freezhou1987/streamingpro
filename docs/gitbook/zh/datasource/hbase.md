# HBase

HBase 是一个应用很广泛的存储系统。MLSQL也支持将其中的某个索引加载为表。

注意，HBase的包并没有包含在MLSQL默认发型包里，所以你需要通过 --jars 带上相关的依赖才能使用。

MLSQL实现了相对应的驱动，可以通过如下方式获取jar包：

```
git clone  https://github.com/allwefantasy/streamingpro .
mvn -Pshade -am -pl external/streamingpro-hbase -Pspark-2.4.0 -Pscala-2.11 -Ponline clean package
```

之后通过--jars带上 `external/streamingpro-hbase/target/streamingpro-hbase-x.x.x-SNAPSHOT.jar`

## 加载数据

示例：

```sql
connect hbase where `zk`="127.0.0.1:2181"
and `family`="cf" as hbase1;

load hbase.`hbase1:mlsql_example`
as mlsql_example;

select * from mlsql_example as show_data;


select '2' as rowkey, 'insert test data' as name as insert_table;

save insert_table as hbase.`hbase1:mlsql_example`;
```

MLSQL内置参数：

| Property Name  |  Meaning |
|---|---|
| tsSuffix |通过列名加这个后缀覆盖hbase value的timestamp|
|namespace|hbase命名空间|
| family |hbase列族，load的时候如果family=""，查询所有列族|
| field.type.ck |此示例为把ck字段设置为指定类型（LongType、FloatType、DoubleType、IntegerType、BooleanType、BinaryType、TimestampType、DateType），默认无StringType。|
```
connect hbase where `zk`="127.0.0.1:2181"
and `tsSuffix`="_ts"
and `family`="cf" as hbase_conn;

select 'a' as id, 1 as ck, 1552419324001 as ck_ts as test ;
save overwrite test
as hbase.`hbase_conn:mlsql_example`
options  rowkey="id";

load hbase.`hbase_conn:mlsql_example`
options `field.type.ck`="IntegerType"
as testhbase;
```

在HBase里，数据连接引用和表之间的分隔符不是`.`,而是`:`。


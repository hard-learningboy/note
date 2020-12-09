MySQL主流引擎是MyISAM模式，这种模式方便使用和维护，Innodb为后来加入的另外一种模式，这种模式支持的功能更强大，是未来发展趋势。 



第一种情况：如何设置MySQL5.5以及之前版本开启Innodb模式，流程如下：

1、设置默认引擎为Innodb：
default-storage-engine=Innodb
2、注释掉“skip-innodb”，表示不跳过Innodb模式：
\# skip-innodb
3、设置后，保存“my.ini”文件，重启MySQL服务，即可生效。

第二部分：如何设置MySQL5.6以及后续版本开启Innodb模式：

MySQL5.6以及后续版本，则需要设置多个地方才行，请参见如下设置流程：

1、设置“default-storage-engine”，并添加如下代码，参见：
default-storage-engine=Innodb
default-tmp-storage-engine=Innodb
2、找到“skip-innodb”，注释掉，并删除如下代码（如果没有，则无需处理）：
\# skip-innodb
loose-skip-innodb
loose-innodb-trx=0
loose-innodb-locks=0
loose-innodb-lock-waits=0
loose-innodb-cmp=0
loose-innodb-cmp-per-index=0
loose-innodb-cmp-per-index-reset=0
loose-innodb-cmp-reset=0
loose-innodb-cmpmem=0
loose-innodb-cmpmem-reset=0
loose-innodb-buffer-page=0
loose-innodb-buffer-page-lru=0
loose-innodb-buffer-pool-stats=0
loose-innodb-metrics=0
loose-innodb-ft-default-stopword=0
loose-innodb-ft-inserted=0
loose-innodb-ft-deleted=0
loose-innodb-ft-being-deleted=0
loose-innodb-ft-config=0
loose-innodb-ft-index-cache=0
loose-innodb-ft-index-table=0
loose-innodb-sys-tables=0
loose-innodb-sys-tablestats=0
loose-innodb-sys-indexes=0
loose-innodb-sys-columns=0
loose-innodb-sys-fields=0
loose-innodb-sys-foreign=0
loose-innodb-sys-foreign-cols=0
3、保存“my.ini”文件，并重新启动MySQL服务。



\1. InnoDB支持事务，MyISAM不支持，对于InnoDB每一条SQL语言都默认封装成事务，自动提交，这样会影响速度，所以最好把多条SQL语言放在begin和commit之间，组成一个事务； 

\2. InnoDB支持外键，而MyISAM不支持。对一个包含外键的InnoDB表转为MYISAM会失败； 

\3. InnoDB是聚集索引，使用B+Tree作为索引结构，数据文件是和（主键）索引绑在一起的（表数据文件本身就是按B+Tree组织的一个索引结构），必须要有主键，通过主键索引效率很高。但是辅助索引需要两次查询，先查询到主键，然后再通过主键查询到数据。因此，主键不应该过大，因为主键太大，其他索引也都会很大。



mysql -u root -p root --default-character-set=utf8  表名 < sql文件路径 
---
title: docker-compose安装Mysql以及redis
date: 2019-11-17 11:45:43
tags: [docker-compose,Mysql]
---

docker-compose.yml文件

```yaml
version: '3' #版本号 不能为1,其余随便
services: # 要启动的service列表,可以启动多个
  mysql: # service名称
    environment: #启动mysql镜像使用的镜像配置环境
      MYSQL_ROOT_PASSWORD: zyarcher #启动的mysql中root的密码
    restart: always #重启策略,一直重启
    container_name: mysql #容器名称
    image: mysql:5.7 #使用mysql的镜像版本,5.7就是Mysql5.7最新的版本
    command: #执行的命令lower-case_table_names是1,即大小写忽略,max_allowed_packet即最大传输包
        --lower-case_table_names=1
        --max_allowed_packet=128M        
    volumes: #将msyql镜像中的文件都外置出来
      - $PWD/mysql/conf:/etc/mysql #mysql的配置文件,自动生成目录有添加my.cnf即可进行配置
      - $PWD/mysql/data:/var/lib/mysql #mysql的数据文件,加入这个之后重启也不会丢失已有的文件
    ports: #开放端口
      - 3306:3306 #本地端口:镜像的端口
  redis:
    image: redis
    restart: always
    container_name: redis
    ports:
      - 6379:6379
    hostname: redis
    command: redis-server --requirepass zyarcher #开启远程连接,并指定远程密码为zyarcher
    volumes:
      - $PWD/redis/data:/data
```

在写入`docker-compose.yml`文件的目录下运行`docker-compose up -d`即可启动.

停止为`docker-compose down`

推荐的`my.cnf`文件

```mysql
[client]
host=localhost
user=root
password=zyarcher #配置中root的密码,写入这个主要是为了能够在本地快速操作数据库
[mysqld]
auto_increment_increment=1
auto_increment_offset=1
back_log=210
binlog_checksum=CRC32
binlog_format=ROW
binlog_row_image=MINIMAL
block_encryption_mode=aes-128-ecb
character_set_server=utf8mb4
concurrent_insert=AUTO
connect_timeout=10
default_week_format=0
delay_key_write=ON
delayed_insert_limit=100
delayed_insert_timeout=300
delayed_queue_size=1000
div_precision_increment=4
eq_range_index_dive_limit=200
event_scheduler=OFF
explicit_defaults_for_timestamp=OFF
ft_min_word_len=4
ft_query_expansion_limit=20
group_concat_max_len=1024
init_connect=
innodb_adaptive_hash_index=ON
innodb_autoinc_lock_mode=1
innodb_concurrency_tickets=5000
innodb_ft_enable_stopword=ON
innodb_ft_max_token_size=84
innodb_ft_min_token_size=3
innodb_ft_server_stopword_table=
innodb_ft_user_stopword_table=
innodb_large_prefix=ON
innodb_lock_wait_timeout=7200
innodb_max_dirty_pages_pct=75.000000
innodb_old_blocks_pct=37
innodb_old_blocks_time=1000
innodb_online_alter_log_max_size=134217728
innodb_open_files=1024
innodb_print_all_deadlocks=ON
innodb_purge_batch_size=300
innodb_purge_threads=4
innodb_read_ahead_threshold=56
innodb_read_io_threads=12
innodb_rollback_on_timeout=OFF
innodb_stats_method=nulls_equal
innodb_stats_on_metadata=OFF
innodb_stats_sample_pages=8
innodb_stats_transient_sample_pages=8
innodb_strict_mode=ON
innodb_table_locks=ON
innodb_thread_concurrency=0
innodb_thread_sleep_delay=10000
innodb_write_io_threads=12
interactive_timeout=3600
key_cache_age_threshold=300
key_cache_block_size=1024
key_cache_division_limit=100
lock_wait_timeout=31536000
log_queries_not_using_indexes=OFF
log_timestamps=SYSTEM
long_query_time=10.000000
low_priority_updates=OFF
lower_case_table_names=1
max_allowed_packet=128M        
max_connect_errors=999999999
max_connections=800
max_length_for_sort_data=1024
max_prepared_stmt_count=16382
max_user_connections=0
myisam_sort_buffer_size=8388608
net_read_timeout=30
net_retry_count=10
net_write_timeout=60
ngram_token_size=2
open_files_limit=102400
performance_schema=OFF
query_alloc_block_size=8192
query_cache_limit=1048576
query_cache_size=0
query_cache_type=OFF
query_cache_wlock_invalidate=OFF
query_prealloc_size=8192
slow_launch_time=2
#sql_auto_is_null=OFF
sql_mode=ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
#sql_safe_updates=OFF
table_definition_cache=768
table_open_cache=512
table_open_cache_instances=8
thread_cache_size=512
#time_zone=SYSTEM
tmp_table_size=209715200
wait_timeout=3600
```


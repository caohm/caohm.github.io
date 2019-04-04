---
title: mysql config
layout: post
date: 2017-03-23 22:35:24 +0800
categories: mysql
tags: mysql
---


* content
{:toc}
线上MySQL5.7配置文件模板








[client]
port = 3358
socket = /export/mysql/mysql/tmp/mysql.sock

[mysqld]
port = 3358
socket = /export/mysql/mysql/tmp/mysql.sock
datadir = /export/mysql/mysql/data
user = mysql
#--- GLOBAL ---#
#transaction-isolation=READ-COMMITTED
sql_mode = NO_ENGINE_SUBSTITUTION

secure_file_priv  =
#采用MySQL5.6默认sql_mode
default-storage-engine = INNODB
character-set-server = utf8
log_timestamps = SYSTEM
#character-set-server = utf8mb4
show_compatibility_56 = on
lower_case_table_names = 1
log-error = /export/mysql/mysql/log/error.log

general_log = 0
general_log_file = /export/mysql/mysql/log/general.log
pid-file = /export/mysql/mysql/data/mysql.pid
slow-query-log
slow_query_log_file = /export/mysql/mysql/log/slow.log
tmpdir = /export/mysql/mysql/tmp
long_query_time = 0.1
#enable slave slow log
log_slow_slave_statements = 1

thread_cache_size = 512
table_open_cache = 16384
table_definition_cache = 16384
table_open_cache_instances = 8
#下一参数在以后版本中可能会被取消
metadata_locks_hash_instances = 256

sort_buffer_size = 128K
join_buffer_size = 265K
read_buffer_size = 256K
read_rnd_buffer_size = 256K
key_buffer_size = 64M
myisam_sort_buffer_size = 64M
tmp_table_size = 64M
max_heap_table_size = 64M
open_files_limit = 65535
query_cache_size = 0
query_cache_type = 0
log_bin_trust_function_creators = 1
explicit_defaults_for_timestamp = 1
#批量插入数据缓存大小，可以有效提高插入效率，默认为8M
#以下2个参数为新增加
bulk_insert_buffer_size = 64M
binlog_rows_query_log_events =on


#--- NETWORK ---#
back_log = 2000
max_allowed_packet = 64M
interactive_timeout = 28800
wait_timeout = 28800
max-connections = 2000
skip-name-resolve
# read_only = 1

#--- REPL ---#
server-id = 1670153358
report_host = 192.168.1.1#本地IP地址
log-bin = mysql-bin

gtid_mode = ON
enforce-gtid-consistency = true
binlog_format = row
binlog_row_image = full
expire_logs_days = 7
relay-log = relay-log
log_slave_updates
slave_net_timeout = 30
skip-slave-start
sync_binlog = 1
#flush disk
sync_relay_log = 0

slave_pending_jobs_size_max = 128M
slave_rows_search_algorithms = INDEX_SCAN,HASH_SCAN

#rpl_semi_sync_master_enabled = 1

#rpl_semi_sync_slave_enabled = 1
#rpl_semi_sync_master_timeout = 1000
relay_log_recovery = 1
#从库并行复制-为新增加参数
slave-parallel-type=LOGICAL_CLOCK
#保证主从库并发执行时，事物顺序一致,可以动态修改
slave_preserve_commit_order=1
#物理机为16，docker为8
slave-parallel-workers=16#默认为4，DBA自己调整,需要stop slave;start slave;
#slave-parallel-workers=8
master_info_repository=TABLE
relay_log_info_repository=TABLE
relay_log_recovery=ON

#--- INNODB ---#
innodb_adaptive_flushing = ON
innodb_adaptive_flushing_lwm = 15
innodb_flushing_avg_loops = 30
innodb_adaptive_hash_index = ON
innodb_data_home_dir = /export/mysql/mysql/data
innodb_file_per_table = ON
innodb_log_group_home_dir = /export/mysql/mysql/data
innodb_log_files_in_group = 3
innodb_log_file_size = 2G
innodb_log_buffer_size = 32M
innodb_flush_log_at_trx_commit = 1
innodb_flush_log_at_timeout = 1
innodb_lock_wait_timeout = 30
innodb_flush_method = O_DIRECT
innodb_max_dirty_pages_pct = 75
innodb_max_dirty_pages_pct_lwm = 0
innodb_buffer_pool_size = 100G
innodb_buffer_pool_instances = 8
innodb_data_file_path = ibdata1:2048M:autoextend
innodb_change_buffering = all
innodb_change_buffer_max_size = 10
innodb_autoextend_increment = 64
innodb_thread_concurrency = 32
innodb_spin_wait_delay = 6
innodb_sync_spin_loops = 30
innodb_open_files = 65535
innodb_checksum_algorithm = crc32
innodb_old_blocks_time = 30
innodb_online_alter_log_max_size= 512M
innodb_write_io_threads = 12
innodb_read_io_threads = 12
innodb_flush_neighbors = 1
innodb_io_capacity = 1000
innodb_io_capacity_max = 1500
innodb_lru_scan_depth = 1024
innodb_purge_threads = 4
innodb_purge_batch_size = 300
#下一参数在以后版本中可能会被取消
innodb_file_format = Barracuda
innodb_undo_directory = /export/mysql/mysql/undo
innodb_undo_tablespaces = 4

innodb_large_prefix = ON
##innodb_force_load_corrupted = OFF
#innodb_force_recovery = 0
#buffer pool dump and load
innodb_buffer_pool_filename = ib_buffer_pool
innodb_buffer_pool_load_abort = OFF
innodb_buffer_pool_dump_at_shutdown = ON
innodb_buffer_pool_load_at_startup = ON
innodb_buffer_pool_dump_now = OFF
innodb_buffer_pool_load_now = OFF
#新增加参数
innodb_adaptive_hash_index_parts=128

[mysqldump]
quick
max_allowed_packet = 64M
[mysql]
no-auto-rehash
default-character-set=utf8
prompt = "MySQL \u@[\d]>"

[myisamchk]
key_buffer_size = 128M
sort_buffer_size = 128M
read_buffer = 2M
write_buffer = 2M

[mysqlhotcopy]
interactive-timeout

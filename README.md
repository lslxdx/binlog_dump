# Introduction
A simple &amp; reliable Shell script for dumping MySQL binlog, especially friendly to [Logstash](https://www.elastic.co/products/logstash).
`binlog_dump` tracks MySQL binlogs, save them as they are under `<data_path>/binlog.d/` and concatenate them to `<data_path>/binlog.log`. Only _**delta**_ parts of binlogs are dumped whenever `binlog_dump` is run.

# Platform
* CentOS 6.6
* macOS 10.12
* Other Linux(NOT TESTED)

# Requirements
* `mysqlbinlog` command
* `mysql` command
* `GRANT REPLICATION CLIENT ON *.* TO mysql_user_name@mysql.client.ip.or.domain;`
* `flock` command(best to have)
* `w(rite)` permission to `/var/lock/`

# Feature
* Simple: easy to deploy, only 1-2 script files, no need to run `yum install xxx`;
* Reliable: log rotation(`FLUSH LOGS` etc.) is supported by comparing log creation timestamp(e.g `#170228 15:44:27 server id 94219  end_log_pos 0 CRC32 0x84bf50b6        Start: binlog v 4, server v 10.0.14-MariaDB-log created 170228 15:44:27`);
* Incrementally Dumpping: only _**delta**_ parts of binlogs are dumped whenever `binlog_dump` is run. The whole of binlog is dumped when log rotation is detected;
* Single Instance: at most 1 instance of this script is allowed to run;
* Friendly to Logstash: binlogs are concatenated to `<data_path>/binlog.log` which can be easily monitored by Logstash. `# EOF by 'binlog_dump' script` line is appended to the end of `<data_path>/binlog.log` which can be used to mark the start of next event by the `multiline` codec;

# Tutorial
```
# set `USER`, `PASSWD`, `HOST`, `PORT` variables
vim binlog_dump

# create the <data_path> directory
mkdir data

# dump binlogs
binlog_dump data

# help
binlog_dump

# structure of <data_path>
data
├── binlog.d
│   ├── mysql-bin.000001
│   ├── mysql-bin.000002
│   ├── mysql-bin.000003
│   ├── mysql-bin.000004
│   ├── mysql-bin.000005
│   ├── mysql-bin.000006
│   ├── mysql-bin.000007
│   └── mysql-bin.000008
├── binlog.log
├── error.log
├── mysqlbinlog.task
├── show_binary_logs.all
└── tmp.d
# binlog.d: dirctory of separate binlog files
# binlog.log: concatenated binlogs
# error.log: running log
# mysqlbinlog.task: [binlog-file-name start-position stop-position] pair per line used by `mysqlbinlog` 
# show_binary_logs.all: [local-binlog-file-name length] pair per line
# tmp.d: directory for temporary files
```
# Any Question
lslxdx#163.com

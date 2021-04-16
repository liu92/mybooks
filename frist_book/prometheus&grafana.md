Prometheus和Grafana监控搭建

1、下载

```
brew install prometheus
brew install grafana
```

2、修改相关的配置文件

```
cd /usr/local/etc/prometheus.yml文件
```

3、启动

```shell
HB~ /usr/local/Cellar/prometheus/2.23.0/prometheus --config.file=/usr/local/etc/prometheus.yml

----
brew services start grafana
或者进入安装目录去启动
/usr/local/Cellar/grafana/7.3.5/bin/

#停止grafana
brew services stop grafana

https://cloud.tencent.com/developer/article/1433280 参考
```

4、sysbench测试mysql

先进行准备

```shell
sysbench --db-driver=mysql --time=300 --threads=10 --report-interval=1 --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-user=test_db --mysql-password=123 --mysql-db=test_db --tables=20 --table_size=500000 oltp_read_write --db-ps-mode=disable prepare

##
##1. --db-driver=mysql:表示基于mysql的驱动去连接mysql数据库
##2. --time=300: 这个表示连续访问300秒
##3. --thread=10:表示用10个线程模拟并发
##4. --report-interval=1: 表示每隔1秒输出压测情况
##5. --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-user=test_db --mysql-password=123
 ##表示 连接数据库信息

##6. --mysql-db=test_db --tables=20 --table_size=500000: 表示在test_db库中，构造20个测试表，每个测试表构造50万条测试数据

##7. oltp_read_write: 表示执行oltp数据库的读写测试
##8. --db-ps-mode=disable prepare 表示禁止ps模式
```

5、在进行压测

```shell
sysbench --db-driver=mysql --time=300 --threads=10 --report-interval=1 --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-user=test_db --mysql-password=123 --mysql-db=test_db --tables=20 --table_size=500000 oltp_read_write --db-ps-mode=disable run
sysbench 1.0.20 (using bundled LuaJIT 2.1.0-beta2)

Running the test with following options:
Number of threads: 10
Report intermediate results every 1 second(s)
Initializing random number generator from current time


Initializing worker threads...

Threads started!

[ 1s ] thds: 10 tps: 964.68 qps: 19416.99 (r/w/o: 13607.03/3870.66/1939.31) lat (ms,95%): 15.27 err/s: 0.00 reconn/s: 0.00
[ 2s ] thds: 10 tps: 1085.10 qps: 21713.10 (r/w/o: 15198.47/4344.43/2170.21) lat (ms,95%): 15.27 err/s: 0.00 reconn/s: 0.00
[ 3s ] thds: 10 tps: 984.77 qps: 19700.31 (r/w/o: 13795.72/3935.06/1969.53) lat (ms,95%): 16.71 err/s: 0.00 reconn/s: 0.00
[ 4s ] thds: 10 tps: 999.29 qps: 19929.77 (r/w/o: 13939.05/3992.15/1998.57) lat (ms,95%): 16.71 err/s: 0.00 reconn/s: 0.00
[ 5s ] thds: 10 tps: 1049.01 qps: 21063.23 (r/w/o: 14754.16/4211.05/2098.02) lat (ms,95%): 15.83 err/s: 0.00 reconn/s: 0.00
[ 6s ] thds: 10 tps: 890.83 qps: 17793.55 (r/w/o: 12463.59/3548.30/1781.66) lat (ms,95%): 19.29 err/s: 0.00 reconn/s: 0.00
.......


#	测试结果
SQL statistics:
    queries performed:
        read:                            4694704 //表示在300s的压测期间执行了469万多次的读请求
        write:                           1341334 //表示在300s的压测期间执行了134万多次的写请求
        other:                           670669//表示在300s的压测期间执行了67万多次的其他请求
        total:                           6706707 //表示在300s的压测期间一共执行了67万多次的请求
    //这里表示一共执行了33万多个事务，每秒执行1117个事务
    transactions:                        335333 (1117.66 per sec.)
    //表示一共执行了67万次的请求，每秒执行2.2万多次的请求
    queries:                             6706707 (22353.39 per sec.)
    ignored errors:                      3      (0.01 per sec.)
    reconnects:                          0      (0.00 per sec.)
//一共执行了300s，执行了33万+的事务
General statistics:
    total time:                          300.0294s
    total number of events:              335333

Latency (ms):
         min:                                    3.24 //请求中延迟最小的是3.24ms
         avg:                                    8.94 //所有请求平均延迟的是8.94ms
         max:                                  123.70 //请求中延迟最大的是123.70ms
         95th percentile:                       14.73 //95%的请求延迟都在14.73ms
         sum:                              2999180.32

Threads fairness:
    events (avg/stddev):           33533.3000/34.87
    execution time (avg/stddev):   299.9180/0.01

```




















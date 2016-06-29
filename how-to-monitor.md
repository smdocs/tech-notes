# How to Monitor a datastore


## Monitoring a datastore: metrics and alerts

The first question you need to ask is this: Given the plethora of metrics and alerts out there, which specific ones should we single out for monitoring? Which metrics do we care about; enough so that we let them pester us with alerts and notifications? 

The list should not be long. Our rule of thumb is *collect all possible/reasonable metrics that can help when troubleshooting, alert only on those that require an action from you*. 

For most database servers, you need to monitor those processes:

![required processes are running OK, resource usage is within limits, queries are executed successfully, typical failure points are monitored](https://serverdensity-wpengine.netdna-ssl.com/wp-content/uploads/2015/10/metrics_workflow.png)

Based on all this, what you will find below is a suggested collection of alerts. They obviously won’t work in every possible scenario. Even if they do, you will probably want to tweak the thresholds for your needs. One size does not fit all, people.

### MySQL process running

Metric | Comments | Suggested Alert
-------|----------|----------------
mysqld process count | Right binary daemon process running. | When process count /usr/sbin/mysqld != 1

### System Metrics 

These are the top system resources to monitor on a database server. When you experience any issue or bottleneck, these are the first you need to have a look at:

Metric | Comments | Suggested Alert
-------|----------|----------------
Load | An all-in-one performance metric. [Understanding Linux Load](http://injustfiveminutes.com/2013/02/28/understanding-linux-cpu-load/). | When load is > factor x (number of cores). Our suggested factor is 4.
CPU usage | A high CPU usage is not a bad thing as long as you don’t reach the limit. |None
Memory usage | Ideally your entire database should be stored in memory, but this is not always possible. Give MySQL as much as you can afford but leave enough for other processes to function. | None
Swap usage | Swap is for emergencies only, and it should not be used. | When used swap is > 128MB.
Network bandwidth | Unless doing backups or transferring huge amounts of data, it shouldn’t be the bottleneck. | None
Disk usage | Make sure you always have free space for new data, temporary files, snapshot or backups. | When database, logs and temp is > 85% usage.

Disk is one of the most common bottlenecks, so it’s worth keeping an eye on some detailed metrics here. You can get those with `iostat` (for more info on `iostat` check out [Monitoring IO performance using iostat & pt-diskstats](https://www.percona.com/live/mysql-conference-2013/sites/default/files/slides/Monitoring-Linux-IO.pdf)).

Metric | Comments | Suggested Alert
-------|----------|----------------
Read/Write requests | IOPS (Input/Output operations per second) | None
IO Queue length | Tracks how many operations are waiting for disk access. If a query hits the cache, it doesn't create any disk operation. If a query doesn't hit the cache (i.e. a miss), it will create multiple disk operations. | None
Average IO wait | Time that queue operations have to wait for disk access. | None
Average Read/Write time | Time it takes to finish disk access operations (latency). | None
Read/Write bandwidth | Data transfer from and towards your disk. | None

![Example of a MySQL metrics dashboard](https://serverdensity-wpengine.netdna-ssl.com/wp-content/uploads/2015/10/mysql_dashboard.png)

###  Metrics

#### Monitoring database for availability and connections

Let’s start with the metrics that establish if your  server is working.

Metric | Meaning / Comments | Suggested Alert
-------|--------------------|----------------
Uptime Seconds since the server was started. We can use this to detect respawns. | When uptime is < 180.
Threads_connected | Number of clients currently connected. If none or too high, something is wrong. | None
Max_used_connections | Max number of connections at a time since server started. (max_used_connections / max_connections) indicates if you could run out soon of connection slots. | When connections usage is > 85%.
Aborted_connects | Number of failed connection attempts. When growing over a period of time either some credentials are wrong or we are being attacked. | When aborted connects/min > 3. (only on not public exposed servers, otherwise will generate noise)

#### Typical server errors

Common failure points you need to keep an eye on.

Metric | Meaning / Comments | Suggested Alert
-------|--------------------|----------------
(Errors) | Are there any errors on the mysql.log file? | None
(Log files size) | Are all log files being rotated? | None
(Deleted log files) | Were any log files deleted but the file descriptor is still open? | None
(Backup space) | Do you have enough disk space for backups? | None

#### Monitoring MySQL queries

These metrics track whether our database does what it’s meant to do. Answer queries that is (!):

Metric | Meaning / Comments | Suggested Alert
-------|--------------------|----------------
Questions (/s) | Number of statements sent by clients. | None
Queries | Number of executed statements (including stored procedures) | None
Read / Writes | Reads: selects \+ cache hits Writes: inserts \+ updates \+ deletes | None

These metrics keep track of the queries that are affecting your server’s performance:

Metric | Meaning / Comments | Suggested Alert
-------|--------------------|----------------
Slow_queries | Number of queries that took more than long_query_time seconds to execute. Slow queries generate excessive disk reads, memory and CPU usage. Check slow_query_log to find them. | None
Select_full_join | Number of full joins needed to answer queries. If too high, improve your indexing or database schema. | None
Created_tmp_disk_tables | Number of temporary tables (typically for joins) stored on slow spinning disks, instead of faster RAM. | None
(Full table scans) Handler_read% | Number of times the system reads the first row of a table index. Sequential reads might indicate a faulty index. | None

#### Monitoring MySQL caches, buffers, and locks

Here are some key metrics for optimizing caches and buffers, whilst detecting any locked transactions. We would love to hear about your suggested metrics as well.

Metric | Meaning / Comments | Suggested Alert
-------|--------------------|----------------
Innodb_row_lock_waits | Number of times InnoDB had to wait before locking a row. | None
Innodb_buffer_pool_wait_free | Number of times InnoDB had to wait for memory pages to be flushed. If too high, innodb_buffer_pool_size is too small for current write load. | None
Open_tables | Number of tables currently open. If this is low and table_cache is high, we can reduce cache size. If opposite, we should increase it. If you increase table_cache you might have to increase available file descriptors for the mysql user. | None
(Long running transactions) | Tracks whether too many transactions are locked by other idle transactions, or because of a problem in InnoDB. | None
(Deadlocks) | Deadlocks happen when 2 transactions mutually hold. These are unavoidable in InnoDB and apps should deal with them. | None

## Determine Monitoring Methods and Tools

There are plenty of contenders out there. Below are the most popular ones:


### MyTOP / Mtop / Innotop

[MyTop](http://jeremy.zawodny.com/mysql/mytop/) displays statistics about threads, queries, slow queries, uptime, and load in a top-like interface. You can find `mytop` in Debian/Ubuntu/CentOS repositories. Here is [a great tutorial](https://www.digitalocean.com/community/tutorials/how-to-use-mytop-to-monitor-mysql-performance) by the folks at DigitalOcean.

Another very similar alternative is [Mtop](http://mtop.sourceforge.net/). However, this one is not included in the official repositories of most distributions and it is not actively maintained anymore.

If we had to chose one and only one interactive tool, that would be [Innotop](https://github.com/innotop/innotop). Why? Because it shows everything: InnoDB I/O, buffers, open tables, lock tables, queries, you name it. Innotop is also included in the mysql-client packages which means you don't need to install anything. We found this [write-up on Innotop](https://www.percona.com/blog/2013/10/14/innotop-real-time-advanced-investigation-tool-mysql/) very useful.

Another reporting tool that we didn’t want to miss out is `mysqlreport`. It provides an easy way to read the `SHOW STATUS` command. `mysqlreport` used to be a part of the [now retired hackmysql.com project](http://blog.codenode.com/2015/01/hack-mysql-has-been-retired.html). It is, however, still maintained and included in mysql-client packages. Check out the [Guide to Understanding mysqlreport](https://github.com/daniel-nichter/hackmysql.com/blob/master/mysqlreport/docs/The%20Guide%20To%20Understanding%20mysqlreport.pdf) for more details.

### Percona Monitoring Plugins

`mysqladmin` and `innotop` are great interactive / realtime tools. But when managing a MySQL server you will often need to record metrics. That helps with troubleshooting, alerting, and analysis. 

[Percona Monitoring Plugins](https://www.percona.com/software/mysql-tools/percona-monitoring-plulgins) is the most popular open source solution for that. It provides graphing and alerting on top of existing on-premise monitoring solutions like Nagios, Cacti or Zabbix. Some of these plugins make use of the [Percona Toolkit](https://www.percona.com/software/mysql-tools/percona-toolkit), a popular toolkit for database administrators developed by Percona (previously known as Maatkit).

Last but certainly not least, if you are looking for a hosted and robust monitoring solution that covers MySQL (and the rest of your infrastructure), you can sign up for a [2-week trial of Server Density](https://www.serverdensity.com/signup/).

### MySQL Performance schema

If you want to further your understanding of MySQL server performance, then give MySQL Performance Schema a try. A good starting point is [What is the MySQL Performance Schema and Why is It Needed](http://mysql.wisborg.dk/2012/09/03/what-is-the-mysql-performance-schema-and-why-is-it-needed/). Read together with the [MySQL sys schema documentation](https://github.com/MarkLeith/mysql-sys).

## Summary

Before closing, if you’re in the mood for more reading, then we’ve found some great posts about MySQL performance and operations at [Percona Data Performance Blog](https://www.percona.com/blog/). [High Performance MySQL book](http://www.highperfmysql.com/) is a great read too.

So what about you? Do you have a checklist or any best practices for monitoring MySQL? What databases do you have in place and how do you monitor them? Any interesting reads to suggest?

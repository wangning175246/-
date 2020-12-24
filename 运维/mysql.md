#### 查看锁等待相关的参数

show status like 'innodb_row_lock_%'

Innodb_row_lock_current_waits : 当前等待锁的数量

Innodb_row_lock_time : 系统启动到现在，锁定的总时间长度

Innodb_row_lock_time_avg : 每次平均锁定的时间

Innodb_row_lock_time_max : 最长一次锁定时间

Innodb_row_lock_waits : 系统启动到现在总共锁定的次数

information_schema 库提供了3个表可以查看发送锁的详细信息

innodb_locks

innodb_lock_waits

innodb_trx

https://www.lenzhao.com/topic/58ddd36991788f6621e758fb

#### 查看进程 

show processlist

#### 删除

delete from user where Host!='127.0.0.1' and Host!='localhost';

#### 查看

select Host,User from user where user=" ";

#### 监控

Percona

#### 主从信息

Show SLAVE STATUS \g
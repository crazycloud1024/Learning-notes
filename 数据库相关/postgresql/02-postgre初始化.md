### postgresql 初始化
##### 创建数据库实例
1. 创建数据目录
   ```shell
   mkdir -p /pgdata/11/{data,backups,scripts,archive_wals}
   chown -R postgres.postgres /pgdata/11
   chmod 0700 /pgdata/11/data
   ```
2. 初始化数据目录
   两种方式
   `/opt/pgsql/bin/initdb -D /pgdata/11/data -U postgre -W`
   `/opt/pgsql/bin/pg_ctl init -D /pgdata/11/data -U postgre -o "-W"` 
3. 启动和停止数据库服务器
   systemctl方式:
   ```shell
   systemctl start postgresql-11.service
   systemctl status postgresql-11.service
   systemctl stop postgresql-11.service
   ```
   pg_ctl方式:
   ```shell
   /opt/pgsql/bin/pg_ctl -D /pgdata/11/data -l logfile start
   /opt/pgsql/bin/pg_ctl -D /pgdata/11/data -l logfile status
   /opt/pgsql/bin/pg_ctl stop -D /pgdata/11/data -W -m -s -t
   ```
   -t 设置超时时间
   -s 开启和关闭屏幕上的消息输出
   -m 控制数据库用什么模式停止 (-ms -mf -mi)
    - smart：等待活动的事务提交结束，并等待客户端主动断开连接之后关闭数据库
    - fast：回滚所有活动的事务，并强制断开客户端的连接之后再关闭数据库
    - immediate 立即终止所有服务器的进程，当下一次数据库启动时它会首先进入恢复模式（不推荐使用）
4. 配置开机自启
   使用root账户
   - yum安装的已经有脚本，如果是编译安装的则需要在源码包中/contrib/start-scripts/,把linux复制到/etc/init.d/
   `cp /postgresql-11/contrib/start-scripts/linux /etc/init.d/postgresql-11`
   - 设置自启
    `chkconfig postgresql-11 on/off`
5. 配置配置文件
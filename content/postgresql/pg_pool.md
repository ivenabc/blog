---
title: "PGpool-II Watch dod(看门狗)"
date: 2019-11-15
author: "Iven"
draft: true
weight: 101
---

sudo yum install http://www.pgpool.net/yum/rpms/4.1/redhat/rhel-7-x86_64/pgpool-II-release-4.1-1.noarch.rpm

sudo yum install pgpool-II-pg11 #pg11 依赖 pg11

安装PostgreSQL11
https://www.postgresql.org/download/linux/redhat/



/usr/pgsql-11/bin/postgresql-11-setup initdb
systemctl enable postgresql-11
systemctl start postgresql-11


 cd /etc/pgpool-II/


sudo yum install https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm
sudo yum install postgresql11
sudo yum install postgresql11-server

/usr/pgsql-11/bin/postgresql-11-setup initdb
systemctl enable postgresql-11
systemctl start postgresql-11


```
host    replication         repuser         10.250.131.56/32          md5
host    replication         repuser         10.250.131.57/32          md5
host    replication         repuser         10.250.131.58/32          md5
host    all                 all             0.0.0.0/0                 md5
```

```
# "local" is for Unix domain socket connections only
local   all             all                                     trust
# IPv4 local connections:
host    all             all             0.0.0.0/0               md5
# IPv6 local connections:
host    all             all             ::1/128                 md5
# Allow replication connections from localhost, by a user with the
# replication privilege.
local   replication     all                                     trust
host    replication     all             127.0.0.1/32            trust
host    replication     all             ::1/128                 trust
```

```
listen_addresses = '*'		# what IP address(es) to listen on;
					# comma-separated list of addresses;
					# defaults to 'localhost'; use '*' for all
					# (change requires restart)
port = 1525				# (change requires restart)
max_connections = 1000			# (change requires restart)
```

alter user postgres with password 'Evolve123456';
CREATE USER repuser REPLICATION LOGIN CONNECTION LIMIT 5 ENCRYPTED PASSWORD 'Rel123456';
select rolpassword FROM pg_authid WHERE rolname='postgres';

https://www.pgpool.net/docs/latest/en/html/runtime-config.html



CREATE USER repuser REPLICATION LOGIN CONNECTION LIMIT 5 ENCRYPTED PASSWORD 'Rel123456';

```
# - Backend Connection Settings -

backend_hostname0 = 'db09'
backend_port0 = 1525
backend_weight0 = 1
backend_data_directory0 = '/home/postgres/data'
backend_flag0 = 'ALLOW_TO_FAILOVER'
backend_application_name0 = 'db09'
backend_hostname1 = 'db10'
backend_port1 = 1525
backend_weight1 = 2
backend_data_directory1 = '/home/postgres/data'
backend_flag1 = 'ALLOW_TO_FAILOVER'
backend_application_name1 = 'db10'

```

```

delegate_IP = '10.250.131.57'
if_cmd_path = '/sbin'
if_up_cmd = '/usr/bin/sudo /sbin/ip addr add $_IP_$/24 dev pbond0 label pbond0:1'
if_down_cmd = '/usr/bin/sudo /sbin/ip addr del $_IP_$/24 dev pbond0'
arping_path = '/usr/sbin'
arping_cmd = '/usr/bin/sudo /usr/sbin/arping -U $_IP_$ -w 1 -I pbond0'
```

https://yq.aliyun.com/articles/43361


在Pgpool-II 4.0或更高版本中，也可以使用scram-sha-256，cert，明文密码身份验证。 pool_passwd是默认的身份验证文件名。启用md5身份验证的过程如下所示。

使用数据库用户的操作系统帐户登录并执行以下操作：

	 pg_md5 --md5auth --username = your_username  your_passwd
	
md5加密的用户名和密码注册在pool_passwd中。 pool_passwd如果不存在，pg_md5命令自动pgpool.conf作为在同一目录pool_passwd创建。 pool_passwd的格式为用户名：encryption_passwd。

您还需要在pool_hba.conf中创建适当的md5条目。有关详细信息，请参见第6.1节。

在pool_passwd中注册的密码必须与在PostgreSQL中注册的密码完全相同。

更改md5密码（pool_passwd和PostgreSQL）后，执行pgpool reload。

https://segmentfault.com/a/1190000020850773#articleHeader10

https://www.cnblogs.com/huobi/p/10712427.html

pcp_node_info --verbose -h 10.250.131.67 -p 1528 -U pgpool 0
pcp_watchdog_info --verbose -h 10.250.131.67  -p 1528 -U pgpool
pcp_attach_node -n 0 -p 1528 -h 10.250.131.67  -U pgpool
psql -h 10.250.131.67 -p 1526 postgres postgres
pg_basebackup -D data -Fp -Xs -v -P -h db09 -p 1525 -U repuser
pg_rewind --target-pgdata=data/ --source-server='host=10.250.131.58 port=1525 user=postgres dbname=postgres password=Evolve123456' 
 /usr/bin/sudo /sbin/ip addr add $_IP_$/24 dev ens192 label ens192:1
 /usr/bin/sudo /sbin/ip addr del $_IP_$/24 dev ens192

10.250.131.67

/usr/bin/sudo /sbin/ip addr add 10.250.131.67/24 dev ens192 label ens192:1
/usr/bin/sudo /sbin/ip addr del 10.250.131.67/24 dev ens192

```
shared_buffers = 8GB
```

#effective_io_concurrency = 1           # 1-1000; 0 disables prefetching
max_worker_processes = 16               # (change requires restart)
max_parallel_maintenance_workers = 2    # taken from max_parallel_workers
max_parallel_workers_per_gather = 4     # taken from max_parallel_workers
#parallel_leader_participation = on
max_parallel_workers = 8                # maximum number of max_worker_processes that
                                        # can be used in parallel operations



最近遇到一个PgPool连接阻塞问题，PgPool刚开启是能成功连接的，过段时间就连接不上了。查看PgPool日志，启动成功，连接数据库节点成功，健康检查成功。然后怀疑是并发数过多导致阻塞。
　　一开始，更改了pgpool.conf的max_pool,num_init_children参数然后重启，结果仍然阻塞。查资料可知：

num_init_children：pgPool允许的最大并发数，默认32。
max_pool：连接池的数量，默认4。
pgpool需要的数据库连接数=num_init_children*max_pool；
后检查Postgresql数据库的postgresql.conf文件的

max_connections=100
superuser_reserved_connections=3。
pgpool的连接参数应当满足如下公式：

num_init_children*max_pool<max_connections-superuser_reserved_connections
1
当需要pgpool支持更多的并发时，需要更改num_init_children参数，同时要检查下num_init_children*max_pool是否超过了max_connections-superuser_reserved_connections，如果超过了，可将max_connections改的更大。
# 故障处理

此处收集使用 PostgreSQL 过程中最常见的故障，供您参考

> 大部分故障与云平台密切相关，如果你可以确认故障的原因是云平台造成的，请参考[云平台文档](https://support.websoft9.com/docs/faq/zh/tech-instance.html)

#### 导入数据库报错？

查看脚本里面是否有创建数据库的脚本

#### 数据库服务无法启动

数据库服务无法启动最常见的问题包括：磁盘空间不足，内存不足，配置文件错误。  
建议先通过命令进行排查  

```shell
# 查看磁盘空间
df -lh

# 查看内存使用
free -lh
```

#### 数据库日志文件太大，导致磁盘空间不足？

默认安装，postgresql会自动开启binlog，binlog是一个二进制格式的文件，用于记录用户对数据库**更新的****SQL语句****信息**，例如更改数据库表和更改内容的SQL语句都会记录到binlog里。

binlog主要用于出现没有备份的情况下，恢复数据库。但binlog会占用较大空间，长期不清理会让剩余磁盘空间为0，从而影响数据库或服务器无法启动

如果对自己的备份有信心，不需要binlog功能，参考如下步骤关闭之：

1. 编辑 [PostgreSQL 配置文件](/zh/stack-components.md#postgresql)，注释掉 binlog 日志
  ~~~
  #log-bin=postgresql-bin  
  ~~~
2. 重启postgresql
  ~~~
  systemctl restart postgresqld
  ~~~

#### 容器无法启动？

最常见的原因是用户没有按照该容器的要求，设置必须的环境变量，导致容器启动失败

#### 容器端口映射失败？

如果用户无法把握宿主机（服务器）操作系统端口与容器端口的映射关系、可用性情况，请开启端口自动映射

#### PostgreSQL 容器无法远程访问？

导致这个问题的可能原因有三点：

1. 端口映射设置错误，导致容器没有网络
2. 容器没有开启远程访问权限
3. PostgreSQL 8.0 特殊设置要求

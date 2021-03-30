# REDIS

## 搭建

### 下载地址

- 官网地址: http://redis.io/
- 中文官网地址: http://www.redis.cn/
- 下载地址: http://download.redis.io/releases/

### 安装步骤

1. gcc环境

   ```shell
   yum install -y gcc-c++
   yum install -y wget
   ```

2. 下载解压

   ```shell
   wget http://download.redis.io/releases/redis-5.0.5.tar.gz
   tar -zxf redis-5.0.5.tar.gz
   ```

3. 源码编译

   ```shell
   cd redis-5.0.5/src
   make
   ```

4. 安装

   ```shell
   mkdir /usr/redis -p
   make install PREFIX=/usr/redis
   ```

5. 配置文件

   ```shell
   cp redis.conf /usr/redis/bin/
   
   vim redis.conf
   
   # 将`daemonize`由`no`改为`yes`
   daemonize yes
   # 默认绑定的是回环地址,默认不能被其他机器访问
   # bind 127.0.0.1
   # 是否开启保护模式,由yes该为no
   protected-mode no
   ```

6. 指定配置文件启动

   ```shell
   ./redis-server redis.conf
   
   # 关闭
   ./redis-cli shutdown
   ```

7. 命令行工具

   ```shell
   ./redis-cli -h 127.0.0.1 -p 6379
   ```

### 工具说明

- redis-server：启动 redis 服务

- redis-cli：进入 redis 命令客户端

- redis-benchmark：性能测试的工具
- redis-check-aof：aof 文件进行检查的工具
- redis-check-dump：rdb 文件进行检查的工具
- redis-sentinel：启动哨兵监控服务

## <a name="ha">高可用集群</a>

### 主从














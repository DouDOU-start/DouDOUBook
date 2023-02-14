# 测试UDP端口连通性

## Linux使用netcat测试udp端口

Ubuntu和macos下使用的nc命令是netcat

``` shell
# Ubuntu安装
$ apt install netcat -y
```

## netcat常用参数

-k 在当前连接结束后保持继续监听
-l 用作端口监听，而不是发送数据
-n 不使用 DNS 解析
-N 在遇到 EOF 时关闭网络连接
-p 指定源端口
-u 使用 UDP 协议传输
-v (Verbose)显示更多的详细信息
-w 指定连接超时时间
-z 不发送数据

## 使用netcat创建TCP客户端和服务器

```shell
# 创建TCP服务器
# -l 监听地址 端口
$ nc -l 127.0.0.1 8080

# 客户端以TCP协议连接到服务器
$ nc 127.0.0.1 8080
```

**默认测试tcp端口**

```shell
# 测试百度的80端口是否畅通
$ nc -v -w 2 baidu.com 80
```

## 使用netcat创建UDP客户端和服务器

```shell
# 创建UDP服务器
# -l 监听地址 端口
# -u 使用udp协议
# -v 显示详情
$ nc -luv 0.0.0.0 8081

# 查看是否8081在UDP上被监听
$ netstat -antup

# 客户端以UDP协议连接到服务端
# -u 使用udp协议
# -v 显示详情
$ nc -uv 目的地IP 8081

# 连接后服务端和客户端即可实现互发消息
```


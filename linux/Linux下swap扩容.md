# Linux下swap扩容

### 1.使用命令查看当前swap大小

```shell
$ free -m
输出结果以兆（MB）为单位
```

### 2.使用dd命令创建一个分区

```shell
$ dd if=/dev/zero of=/home/swap bs=1M count=1024

# if 表示input file，表示输入的文件，这里的输入文件为/dev/zero，也就是说扩容的时候，以/dev/zero的内容进行扩容，这里的/dev/zero的内容一般为二进制数据
# of 表示output file，表示输出的文件，也就是我们要进行扩容的文件所在路径，这里是/home/swap
# bs 表示1 block=1024字节为扩容单位，也就是1K为基本单位扩容
# count 表示开辟多少个block，这里是1048576个block，大小即为1M*1024 = 1G，也就是说，当前扩容的文件为1GB大小的空间
```

### 3.进行格式化交换，将swap文件格式化成文件系统

```shell
$ mkswap /home/swap
```

### 4.使扩容的空间有效

```shell
$ swapon /home/swap
```

### 5.将扩容的信息文件写入到/ect/fstab中

```shell
$ vi /etc/fstab

$ /home/swap swap swap defaults 0 0
```

### 6.检查是否扩容成功

### 7.关闭swap

```shell
$ swapoff /home/swap

修改/etc/fstab文件，删除或注释相关配置

$ free -m 确定swap被关闭
```


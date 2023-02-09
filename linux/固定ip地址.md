# 固定ip地址

### Centos系统固定ip地址

```shell
cd /etc/sysconfig/network-scripts
vi ifcfg-ens33

# 修改以下配置
BOOTPROTO=static
ONBOOT=yes
IPADDR=`本机ip地址`
NETMASK=`子网掩码`
GATEWAY=`默认网关`
DNS1=`8.8.8.8`

# 重启network
systemctl restart network
```

### Debian11固定ip地址

```shell
ip link
# 从输出结果获取网络接口名称 ens33

vim /etc/network/interfaces

# 添加以下配置
auto ens33
iface ens33 inet static
address `本机ip地址`
netmask `子网掩码`
gateway `默认网关`
dns-nameservers 8.8.4.4 8.8.8.8

# 重启网络服务
systemctl restart NetworkManager.service
```

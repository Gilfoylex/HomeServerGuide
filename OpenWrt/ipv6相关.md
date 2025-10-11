## 大佬的视频教程地址
[IPv6 with OpenWrt](https://www.youtube.com/watch?v=LJPXz8eA3b8)

## 防火墙配置ipv6地址
1. 通过DHCPv6给设备分配固定的ipv6后缀来实现静态ipv6分配
2. 通过 1 中的方案分配的ipv6 在运营商下发的ipv6pd前缀变化的时候ipv6前缀地址是会变的，这种情况下要通过ipv6地址和掩码的方式确定设备的ipv6地址，以便配置到防火墙

* 这部分需要修改 openwrt 防火墙的 traffic rules
举例 ::5:0:0:0:245/::f:ffff:ffff:ffff:ffff  前面60位是运营商的PD前缀，不固定，61-64位的5是路由器ipv6 分配提示的设置(IPv6 assignment hint), 最后的124-128位的245是DHCPv6分配的静态后缀

## 通过热插拔事件重启ddns服务
* `vi /etc/hotplug.d/iface/99-ddns-restart`
```
#!/bin/sh

if [ "$ACTION" = "ifup" ] && [ "$INTERFACE" = "lan" ]; then
    /etc/init.d/ddns restart
fi

```

* `chmod +x /etc/hotplug.d/iface/99-ddns-restart`

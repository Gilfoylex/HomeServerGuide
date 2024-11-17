# 安装
* luci-app-upnp

# upnp启动失败提示没有 `listening_ip`
* upnp 启动读取的是openwrt->network->interfaces 下 "lan" 接口的地址，注意是小写的lan, 大小写不匹配都会导致无法获取监听的ip
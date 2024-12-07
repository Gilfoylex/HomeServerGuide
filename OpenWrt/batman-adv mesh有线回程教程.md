# batman-adv mesh组网相关教程
1. [B.A.T.M.A.N 维基百科介绍](https://en.wikipedia.org/wiki/B.A.T.M.A.N.) 
2. [OpenWrt 官网教程](https://openwrt.org/docs/guide-user/network/wifi/mesh/batman)
3. [国外OpenWrt大佬的教学视频](https://youtu.be/t4A0kfg2olo?si=24e6wxmG9ScBbj1J)
4. [Open Mesh 官网教程](https://www.open-mesh.org/projects/batman-adv/wiki/Batman-adv-openwrt-config)

# 基本原理讲解
1. B.A.T.M.A.N的核心是通过 bat 接口来接管所有流量，然后由B.A.T.M.A.N协议来决定联网设备接入最合适的 bat 节点来实现无缝漫游

![简单的示意图](./image/batman-adv.jpg)

路由器之间只需要通过一条网线连接(走本地LAN网段(192.168.1.1/24))，在bat0(Batman Device)的基础上桥接WIFI或者有线接口(192.168.100.1/24)给联网设备接入网络

# 有线部分搭建方法
## 需要安装的软件包
1. kmod-batman-adv, B.A.T.M.A.N 底层协议支持
2. luci-proto-batman-adv, luci界面配置 B.A.T.M.A.N 时需要
3. batctl-full, 用于调试和检查 B.A.T.M.A.N 网络

## OpenWrt界面配置(基于23.05.5)
### 主路由器配置
1. 添加 Batman Device (bat0), Advanced Settings 取消 Use default gateway 勾选项，Mesh Routing 勾选 Avoid Bridge Loops, Gateway Mode 选择 Server ，其他参数保持默认

![add-bat0](./image/add-bat0.jpg)

![add-bat0-1](./image//add-bat0-1.jpg)

![add-bat0-2](./image/add-bat-server.jpg)

2. 添加 BATWRITE 接口承载 bat0 的流量，使用现有的 br-lan 来创建 Batman Interface, 取消 Use default gateway 勾选项，设置防火墙，其他参数保持默认

![batwrite](./image/batwrite.jpg)

![batwrite-adv](./image/batwrite-adv.jpg)

![batwrite-fire](./image/batwrite-fire.jpg)

3. 桥接路由器网口和bat0, 打通需要连接上网设备的网口与 Batman Device (bat0) 的通信
* 创建桥接设备 br-bat

![create-bridge](./image/create-bridge.jpg)

![create-bridge2](./image/create-bridge1.jpg)

4. 在第三步创建的桥接设备上创建实际的网络接口 BATLAN, 协议选择 Static address, 地址192.168.100.1, 掩码255.255.255.0, 选择防火墙, 并且开启DHCP服务

![creat-batlan](./image/creat-batlan.jpg)

![create-batlan1](./image/creat-batlan1.jpg)

![create-batlan-fw](./image/creat-batlan-fw.jpg)

![create-batlan-dhcp](./image/creat-batlan-dhcp.jpg)

### 子路由器配置
* 子路由器配置和主路由器基本一样，只需要修改几个地方
1. 主路由配置第1步创建 Batman Device (bat0) 的时候 Mesh Routing 中的 Gateway Mode 选择 Server

![add-bat0-client](./image/add-bat-client.jpg)

2. 主路由配置第4步需要改为创建 DHCP Client 协议的接口，然后设置防火墙即可

![create-batlan-client](./image/creat-batlan-client.jpg)

![create-batlan-client-fw](./image/creat-batlan-client-fw.jpg)

3. 操作成功后，如果一切设置没问题，子路由的 BATLAN 会自动获取主路由BATLAN DHCP分配的地址

![batlan-client](./image/batlan-client.jpg)

# 无线部分搭建
* TODO


# batctl 使用 [命令说明](https://www.open-mesh.org/projects/batman-adv/wiki/Gateways)
1. `batctl o` 查看所有节点
2. `batctl gwl` 显示已知的网关列表及其相关属性
3. `batctl gw server 100MBit/40MBit` 配置当前节点为网关，并设置上行带宽为 100 MBit/s，下行带宽为 40 MBit/s
4. 为了方便观察，可以编辑`/etc/bat-hosts`，映射节点的mac地址为友好可读的名称

文件格式:

```
节点设备mac地址 节点名称
```
5. `batclt tp <mac地址或者/etc/bat-hosts中mac地址对应的名称>` 测试链路质量

# iperf3 使用
* 测试udp `iperf3 -c <服务器IP> -u -b 10M`


# 性能问题
1. 主要是MTU的问题，现在基本上所有的网络设备默认的MTU都是1500，B.A.T.M.A.N协议会多32字节的头，所有连接bat设置的网络链路应该是全链路1536，否则分包很多导致性能严重下降
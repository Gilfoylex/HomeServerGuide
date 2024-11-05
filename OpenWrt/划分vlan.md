# OpenWrt 家庭网络 VLAN

## VLAN 规划
| VLAN ID | 网关          | 描述        |
| :-----: | :-----------: | :------------: |
| 101     | 192.168.101.1 | LAN(家庭网络)   |
| 102     | 192.168.102.1 | IOT(物联网设备) |
| 103     | 192.168.103.1 | GUEST(访客网络) |

## OpenWrt -> LUCI -> NetWork -> Interface 设置

1. 创建桥接设备
2. 设置VLAN ID

    Tagged: 当前的网口数据标记VLAN ID, 处理的数据都必须带有VLAN ID标记, 用于与其他 VLAN 交换机连接

    Untagged: 接收没有标记的数据, 用于连接电脑、手机等非VLAN交换机设备
 
    Is Primary VLAN：未标记的流量：当端口的某些流量没有 VLAN 标签时，这些流量会被归入主要 VLAN；默认 VLAN：在某些网络设备中，这是当设备接口未指定特定 VLAN 时所使用的默认 VLAN


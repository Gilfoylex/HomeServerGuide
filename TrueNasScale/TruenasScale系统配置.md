## 程序集创建注意事项
1. ACL Type 使用 NSFv4

![ACL Type](./image/ACL%20Type.jpg)

2. 添加ACL的时候使用 Select preset ACL， ACL Option 选择 NFS4_RESTRICTED

![ACL Option](./image/preset%20ACL.jpg)

## 使用 Replication Task 结合 Periodic Snapshot 利用快照在本地备份数据集

![本地备份快照](./image/BackupDataSet.jpg)


## NFS共享配置
* 使用独立Photon OS跑docker来访问TrueNas的存储，在Photon中创建名称为apps、组ID和用户ID为568的用户和组匹配TrueNas的apps用户组和用户
* NFS 共享的时候 把root用户映射为 apps， Photon中用root操作相当于TrueNas中用apps操作

![NFS Map User](./image/NFS%20Maproot%20User.jpg)



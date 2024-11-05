## 文档标注
* <> 标记的一般是需要自己根据实际情况替换

## 修改root密码
1. 默认账户: root, 默认密码: changeme
2. 先用 changeme 密码登录，登录之后继续输 changeme 修改为自己的密码

## tdnf 包管理器配置http代理
* 修改tdnf配置文件: `etc/tdnf/tdnf.conf`

```
[main]
proxy=http://<address or host>:<prot>
```

## 挂载nfs共享
1. 安装nfs工具 `tdnf install nfs-utils`
2. 创建挂载目录 `mkdir /mnt/app-data`
3. 挂载nfs共享目录 `mount -t nfs <nfs_server_ip>:<nfs_server_dir> /mnt/app-data`
4. 验证是否挂载成功 `mount | grep nfs`
5. 开机自动挂载: `/etc/fstab` 配置文件追加内容 `<nfs_server_ip>:<nfs_server_dir> /mnt/app-data nfs defaults 0 0`
## 文档标注
* <> 标记的一般是需要自己根据实际情况替换

## 修改root密码
1. 默认账户: root, 默认密码: changeme
2. 先用 changeme 密码登录，登录之后继续输 changeme 修改为自己的密码

## 设置时区
* 执行命令 `timedatectl set-timezone Asia/Hong_Kong`

## ESXI通过ova格式安装后扩容
* ova格式安装的硬盘只有16GB大小，如果担心不够用就扩容 [官网扩容教程](https://vmware.github.io/photon/assets/files/html/3.0/photon_troubleshoot/expanding-disk-partition.html)
1. 关机状态下编辑虚拟机配置，硬盘容量调整为 32GB

![调整虚拟机硬盘容量](./image/调整虚拟机硬盘.jpg)

2. 安装扩容工具 `tdnf install parted`
3. `parted /dev/sda`

![parted](./image/parted.jpg)

4. 在parted工具中执行 `print` 指令查看信息

![parted-print](./image/parted-print.jpg)

5. 在parted工具中执行 `resizepart 2 100%` 扩展剩余空间

![parted-resizepart](./image/parted-resizepart.jpg)

6. q 退出 parted 工具，执行 `resize2fs /dev/sda2` 命令扩容
7. `df -h` 查看磁盘情况


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

## 创建用户和组用于访问nfs中文件(匹配truenas scale中的用户和组， 为了看着统一， 至于访问权限需要在 truenas 的nfs共享设置mapRootUser 和 mapRootGroup)
### 用户和组相关命令
1. 查看所有用户和组: `cat /etc/passwd`
2. 添加组: `groupadd -g <组ID> <组名>`
3. 删除组: `groupdel <组名>`
4. 添加用户: `useradd -u <用户ID> -g <组名> -m <用户名>`
5. 删除用户: `userdel -r <用户名>` -r 可选，用于删除用户相关的目录
6. 查看用户信息 `id <用户名>`
7. 修改文件或文件夹所属用户和组 `chown -R <用户名>:<组名> <目录>` -R 递归子文件和文件夹
8. 修改权限 `chmod`

### 创建apps用户和组(truenas 中apps的用户id和组id是568， 我们这里也用568)
1. 创建GID为568的apps用户组: `groupadd -g 568 apps`
2. 创建UID为568的apps用户并加到apps组: `useradd -u 568 -g apps -m apps`


## 迁移docker镜像目录
1. 目前无法将/var/lib/docker 迁移到nfs共享目录

## 启动docker并部署portainer-ce
1. 启动docker

```
systemctl start docker
systemctl enable docker
```

2. 查看docker信息 `docker info`
3. 共享目录创建portainer_data目录 `mkdir /mnt/app-data/portainer_data`
4. 部署portainer-ce

```
docker volume create portainer_data
docker run -d -p 9000:9000 --name Portainer --restart always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/mnt/app-data/portainer_data portainer/portainer-ce
```
### NFS服务
1. 修改/etc/exports
   `/data/backup 192.168.1.0/24(rw,sync,no_subtree_check,no_root_squash)`
   IP地址为192.168.1.0/24网段都可以访问，且有读写权限
   rw表示读写权限
   sync资料同步写入到内存和硬盘中
   no_subtree_check不检查父目录权限
   no_root_squash客户机用root访问该共享目录，不映射root用户
2. 执行`exportfs -rv`使修改生效，无需重启NFS服务
3. 查看配置：`exportfs`

### 客户端
``` shell
yum install nfs-utils -y
showmount -e 192.168.1.19
mount -t nfs 192.168.1.19:/data /mnt
```
### 挂载NFS
#### 准备
``` SHELL
sudo apt install nfs-common
```
#### 创建 NFS Docker 卷
``` SHELL
docker volume create --driver local \
  --opt type=nfs \
  --opt o=addr=[ip-address],rw \
  --opt device=:[path-to-directory] \
  [volume-name]
```

例如：
``` shell
docker volume create --driver local \
  --opt type=nfs \
  --opt o=addr=192.168.1.19,rw \
  --opt device=:/data/docker \
  nfs-volume
```
Docker-Composer:
```yml
volumes:
  videos:
    driver_opts:
      type: "nfs"
      o: "addr=192.168.1.19,nfsvers=4"
      device: ":/data/docker"
```
解释：
创建一个名为 `nfs-volume` 的 NFS Docker 卷。该卷包含位于服务器上的 `/data/docker` 目录，具有 `rw` （读/写）权限。服务器的 IP 地址是 `192.168.1.19` 。

``` shell
docker volume ls
docker volume inspect [volume-name]
```
查询docker volume
### Ubuntu挂载NFS
1. 创建挂载点：在本地创建一个目录，用于挂载NFS共享
   ```shell
   mkdir -p /mnt/nfs
```
2. 挂载NFS共享：使用`mount`命令来挂载远程NFS共享，假设NFS服务器IP为192.168.1.19,共享目录为`/data`
   ```shell
   sudo mount -t nfs -o nolock 192.168.1.19:/data /mnt/nfs
```
3. 开机自动挂载：可以将挂载信息添加到`/etc/fstab`
   ```shell
   echo '192.168.1.19:/data /mnt/nfs nfs auto,nofail,noatime,nolock,intr,tcp,actimeo=1800 0 0' | sudo tee -a /etc/fstab

```
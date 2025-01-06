---
title: 一次断电引发的 Kubernetes NFS 挂载错误排查与解决
published: 2025-01-06
description: '记录一次断电后，Kubernetes 集群的 NFS 挂载因路径错配报错'
image: ''
tags: [linux,服务器运维,服务器,Kubernetes,k8s]
category: '记录'
draft: false
---

## 一、背景介绍

在生产环境中，笔者的 Kubernetes 集群使用了 NFS 作为部分应用的持久化存储。某天机房突然发生了断电事故，导致服务器被迫关机。恢复供电并重启后，集群里一些依赖 NFS 的 Pod 无法正常启动。

查看 Pod 日志，发现报错如下：

```bash
MountVolume.SetUp failed for volume "pvc-d41859f8-3d99-4714-8710-7ca9fb1327d2" : mount failed: exit status 32
Mounting command: mount
Mounting arguments: -t nfs 192.168.7.5:/mnt/data1/mongodb-mongodb-pvc-pvc-d41859f8-3d99-4714-8710-7ca9fb1327d2 /var/lib/kubelet/pods/...
Output: mount.nfs: mounting 192.168.7.5:/mnt/data1/... failed, reason given by server: No such file or directory
```

关键提示是 `No such file or directory`，表明 Kubernetes 正尝试挂载 `/mnt/data1`，但服务端并没有检测到对应目录或文件。

---

## 二、问题现象

重启后的服务器可以正常对外提供其他服务，但一旦有 Pod 需要访问 NFS 卷时，就会报类似的 “No such file or directory” 错误。进入服务器查看后发现：

1. `/mnt/data1` 目录为空；
2. `/mnt/data` 目录中却有此前存放的各种文件；

   ```bash
   [root@masternode01 ~]# ll /mnt/data
   total 132
   drwxrwxrwx  4 nfsnobody nfsnobody  4096 Dec  1 12:51 alist-alist-pvc-pvc-141346a0-7ef3-4804-acd9-f62910d445d8
   
   …………
   
   drwxrwxrwx  5 nfsnobody nfsnobody  4096 Jan  6 13:08 mongodb-mongodb-pvc-pvc-d41859f8-3d99-4714-8710-7ca9fb1327d2 # 报错中提到缺失的目录
   ```

   
3. 服务器上依然把 NFS 对外暴露为 `/mnt/data1`；

   ```bash
   [root@masternode01 ~]# cat /etc/exports
   /mnt/data1 192.168.7.0/24(rw,sync,no_subtree_check)
   ```

   
4. **断电重启前**，NFS 一直正常运作。

显然，这种情况相当矛盾：应用尝试访问 `/mnt/data1`，可数据却都在 `/mnt/data` 目录。

---

## 三、排查过程

1. **查看本地目录和挂载情况**
    
    - `ls -l /mnt` 发现 `/mnt/data`、`/mnt/data1` 均存在，但 `/mnt/data1` 里什么都没有。
    - `lsblk` 显示 `/dev/sdb1` 挂载到了 `/mnt/data`。
    
      ```bash
      [root@masternode01 ~]# lsblk
      NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda               8:0    0  372G  0 disk 
      ├─sda1            8:1    0    1G  0 part /boot
      └─sda2            8:2    0  371G  0 part 
        ├─centos-root 253:0    0   50G  0 lvm  /
        ├─centos-swap 253:1    0    4G  0 lvm  
        └─centos-home 253:2    0  317G  0 lvm  /home
      sdb               8:16   0  3.7T  0 disk 
      └─sdb1            8:17   0  3.7T  0 part /mnt/data1
      sdc               8:32   0  931G  0 disk 
      sr0              11:0    1 1024M  0 rom
      ```
    
      
    
2. **分析 NFS 配置**
    
    - 历史记录里，NFS 一直是共享 `/mnt/data1`。
    - 由此猜测：服务器断电重启后，操作系统依照某个配置把 `/dev/sdb1` 挂载到 `/mnt/data`，导致原先应该在 `/mnt/data1` 下的文件，其实都放到了 `/mnt/data` 下。
    
3. **检查 /etc/fstab**
    
    - 打开 `/etc/fstab`，发现有如下配置：
      ```
      /dev/sdb1  /mnt/data  ext4  defaults  0  0
      ```
    - 这说明系统每次重启时，都会自动将 `/dev/sdb1` 加载到 `/mnt/data`。
    - NFS 对外共享配置却依旧指向 `/mnt/data1`，因此出现了路径错配。

---

## 四、解决方案

1. **修改 /etc/fstab**
    - 将 `/mnt/data` 改为 `/mnt/data1`：
      ```
      /dev/sdb1  /mnt/data1  ext4  defaults  0  0
      ```
    - 保存后退出。

2. **重启服务器**
    - 等服务器再次启动后，使用 `lsblk` 命令检查挂载点：
      ```bash
      [root@masternode01 ~]# lsblk
      NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda               8:0    0  372G  0 disk 
      ├─sda1            8:1    0    1G  0 part /boot
      └─sda2            8:2    0  371G  0 part 
        ├─centos-root 253:0    0   50G  0 lvm  /
        ├─centos-swap 253:1    0    4G  0 lvm  
        └─centos-home 253:2    0  317G  0 lvm  /home
      sdb               8:16   0  3.7T  0 disk 
      └─sdb1            8:17   0  3.7T  0 part /mnt/data1
      sdc               8:32   0  931G  0 disk 
      sr0              11:0    1 1024M  0 rom
      ```
    - 这时 `/mnt/data1` 中的文件与 NFS 共享目录一致了。
    
3. **验证 Kubernetes Pod**
    
    - 重启相关 Pod 或让其自动重调度，观察日志是否仍然报错；
    - 此时 Pod 可以正常挂载 `/mnt/data1`，应用也能成功启动并读写数据。

问题至此得到解决。

---

## 五、总结与反思

1. **断电重启带来的风险**
    - 当服务器发生意外关机或重启后，最容易出现存储挂载异常的问题，尤其是分区挂载点和真实路径不匹配的情况。
    - 运维上要在意 `/etc/fstab`、NFS Export 配置等是否一致。

2. **应用依赖的存储路径要保持一致**
    - Kubernetes 或者任何容器编排工具在使用 NFS 时，必须确保操作系统挂载的物理路径与 NFS 服务端对外暴露的路径严格对应。

3. **善用常规排查命令**
    - `ls -l /mnt`、`df -h`、`mount`、`lsblk` 等工具可以迅速定位分区挂载点。
    - `kubectl describe pod`、`kubectl describe pvc` 等指令可以查看容器与存储卷的绑定关系。

4. **经验教训**
    - 在生产环境要做好突发断电的预案，尤其是存储配置部分。
    - 一旦发现类似 “No such file or directory” 的提示，第一时间核查 NFS 服务器端的物理路径是否真的存在。

---

## 六、结语

至此，断电后导致的 Kubernetes NFS 挂载错误问题就排查完毕了。虽然问题看起来并不复杂，但在生产环境中，任何存储路径的细微差异都可能导致业务应用无法正常启动。

如果你也遇到过类似的场景，可以对比本文的方法进行核查；如果没有遇到，也可以当作一次小小的运维知识储备。感谢阅读，欢迎在评论区交流你的思考和经验。祝大家工作顺利，系统稳定！
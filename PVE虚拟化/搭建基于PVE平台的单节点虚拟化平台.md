



## 搭建基于PVE平台的单节点虚拟化平台

#### ![PVE 虚拟化系统 Proxmox Virtual Environment](https://img.iplaysoft.com/wp-content/uploads/2023/pve/pve.jpg!0x0.webp)

### 搭建环境说明：

---

* Thinkserver sr558
* Intel(R) Xeon(R) Silver 4210R CPU @ 2.40GHz *1
* 64G内存
* SSD 400G+8T
* NFS 8T（群晖NAS提供）
* 测试环境网络使用千兆，管理网络业务网络均在一个千兆口上，NFS存储采用另一个千兆口。生产环境由于条件限制想采用服务器双千兆bond方式连接NFS存储。虚拟机业务也采用双千兆BOND模式。

#### 为何采用PVE搭建虚拟化环境：

---

题主公司本地业务系统不是特别多，接手时只有一台DELL R740服务器配置与本次测试环境类似。安装的ESXI 6.7版本，并安装有一台虚拟的VCENTER机器。为什么此次将ESXI切换至PVE主要有以下几点考虑：

1. PVE原生可支持KVM虚拟化及LXC容器技术，运行容器更方便。
2. 基于DEBIAN构建，可以随时获取DEBIAN的更新。
3. 无中心管理节点架构，不用担心集群架构下中心节点出现问题后整个集群出现问题。
4. 三节点以上可以使用ceph分布式存储搭建超融合架构。
5. 开源虚拟化平台不牵扯版权问题。
6. 管理简单，可以单机部署也可集群部署。

#### 开干

----

1.制作安装镜像

​		镜像下载地址：https://mirrors.tuna.tsinghua.edu.cn/proxmox/iso/

​		使用软碟通软件或Linux dd命令将ISO镜像写入U盘

  ```
  dd if=/xxx/*.iso of=/dev/xxx 
  ```

2. 服务器初始化配置（由于品牌的不通，创建方式具体可看厂家文档）

   > 1.创建RAID
   >
   > > > 400G SSD创建RAID0 安装系统使用
   > > >
   > > > 3块4T硬盘创建RAID5配置LVM卷


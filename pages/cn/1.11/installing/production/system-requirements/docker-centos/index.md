---
layout: layout.pug
navigationTitle: CenTos/RHEL 的 Docker
title: 在 CenTos/RHEL 上安装 Docker
menuWeight: 5
excerpt: 在 CenTos/RHEL 上安装 Docker CE 的要求、建议和程序
---

## 客户咨询
最近在 Docker 17.x 的处理 cgroups 内核内存控制器 (kmem) 中发现的程序错误在 `kmem` 会计功能已激活时会导致整个系统不稳定。客户可能会注意到任务或命令无限期地卡住以及在系统日志中与内核相关的错误消息。强烈建议使用 RedHat 或 CentOS 的作为基本操作系统的 Mesosphere DC/OS 客户和社区成员安装和使用 RedHat 的 Docker 1.13 分叉。Docker 的这个分叉不需要 RHN 订阅。

<p class="message--note"><strong>注意：</strong>有关 Docker 程序错误和缓解说明的更多具体详情，请在<a href="https://mesosphere-community.force.com/s/article/Critical-Issue-KMEM-MSPH-2018-0006">这里</a>查看。</p>

# 要求和建议

确保 Docker 的 [`live-restore` 设置已禁用](https://docs.docker.com/config/containers/live-restore/)。在 Docker 配置文件中，应缺少此项或设置为 false。

在 CentOS/RHEL 上安装 Docker 之前，请先查看通用的 [在 DC/OS 上运行 Docker 的要求和建议][1] 以及下面特定于 CentOS/RHEL 的建议：

* OverlayFS 现在是 Docker CE 中的默认值。无需另行指定或配置覆盖驱动程序。首选 OverlayFS 存储驱动程序。OverlayFS 避免已知问题 `devicemapper` 在 `loop-lvm` 模式下发生，并在必要时允许容器使用 docker-in-docker。

* 使用 `ftype=1` 选项将节点存储格式化为 XFS。对于 CentOS/RHEL 7.2，[目前仅支持 XFS 用作较低层文件系统][2]。

* 有关安装 Docker 的更详细分解，[请参阅 Docker CE for CentOS 安装页][4]。

<p class="message--note"><strong>注意：</strong>在新近的 Centos 和 RHEL 版本中，<code>ftype=1</code> 是默认值。可以使用 <code>xfs_info</code> 实用程序来验证 <code>ftype=1</code>。</p>

```bash
mkfs -t xfs -n ftype=1 /dev/sdc1
```

[1]: /1.11/installing/production/system-requirements/#docker
[2]: https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/7.2_Release_Notes/technology-preview-file_systems.html
[3]: https://docs.docker.com/install/linux/docker-ce/centos/
[4]: /1.11/installing/production/deploying-dcos/installation/

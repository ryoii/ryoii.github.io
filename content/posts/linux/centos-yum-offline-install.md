---
title: "Centos 离线安装 yum 软件"
date: 2021-12-21T14:48:53+08:00
authors: ["ryoii"]
tags: ["centos", "yum", "运维"]
categories: ["Linux"]
---

---

最近在一个离线的电脑上要安装一个 `fastdfs` 做内网的文件服务器。`fastdfs` 又只提供了源码提供编译安装，于是要在一台我十分讨厌的 Centos 系统上做离线安装 `gcc-c++`, `libevnet`等一众依赖库支持编译。麻烦在于这些库的依赖也很多，一个个找依赖肯定是不可行的。就想着是不是可以通过 yum 导出所有的依赖。

## yumdownloader

`yumdownloader` 可以根据 yum 一样的依赖管理，把所需要的 rpm 包一并下载到本地，包括所有嵌套的依赖项。

那么只要找一台有网络的机器把所有依赖包下好，再在离线安装的机器上一键安装就好了。

### 安装 yumdownloader

```bash
yum install -y yum-utils
```

### 下载所有依赖包

```bash
# 下载 gcc—c++,libevent 及其依赖
yumdownloader gcc-c++ libevent
```

{{< admonition type="warning" title="注意" open=true >}}
默认会将所有 rpm 包下载到当前目录，用 --destdir 参数指定下载目录
{{< /admonition >}}

### 打包复制离线机器后安装

```bash
# 安装所有 rpm 包
rpm -Uvh *.rpm

# 如果出现依赖缺失可以尝试关闭依赖分析
rpm -Uvh --nodeps *.rpm
```

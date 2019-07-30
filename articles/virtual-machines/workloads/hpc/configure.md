---
title: ハイ パフォーマンス コンピューティング - Azure Virtual Machines | Microsoft Docs
description: Azure 上のハイ パフォーマンス コンピューティングについて説明します。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 10549abfbdacf1fc1ae6b99f4cab20a290c32a2d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707822"
---
# <a name="optimization-for-linux"></a>Linux 向けの最適化

この記事では、OS イメージを最適化するための重要な手法をいくつか紹介します。 [InfiniBand を有効にして](enable-infiniband.md) OS イメージを最適化する方法について説明します。

## <a name="update-lis"></a>LIS を更新する

カスタム イメージ (たとえば、CentOS/RHEL 7.4 や 7.5 などの古い OS) を使用してデプロイする場合は、VM 上の LIS を更新します。

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>メモリを再利用する

リモート メモリ アクセスを回避するためにメモリを自動的に再利用することで効率を向上します。

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

VM の再起動後もこれを永続化するには、次の手順を実行します。

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>ファイアウォールと SELinux を無効にする

ファイアウォールと SELinux を無効にします。

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

## <a name="disable-cpupower"></a>cpupower を無効にする

cpupower を無効にします。

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>次の手順

* [InfiniBand を有効にして](enable-infiniband.md) OS イメージを最適化する方法について説明します。

* Azure での [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) の詳細を確認する。

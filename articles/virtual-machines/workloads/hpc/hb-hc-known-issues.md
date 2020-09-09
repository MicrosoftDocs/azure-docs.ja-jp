---
title: HB シリーズおよび HC シリーズ VM の既知の問題 - Azure Virtual Machines | Microsoft Docs
description: Azure の HB シリーズの VM サイズに関する既知の問題について説明します。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 6316bcc91bb381facb4f77b2d8dbd8b22f9ed387
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660097"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>H シリーズおよび N シリーズ VM に関する既知の問題

この記事では、[H シリーズ](../../sizes-hpc.md)と [N シリーズ](../../sizes-gpu.md)の VM を使用する場合の最も一般的な問題とその解決方法について説明します。

## <a name="dram-on-hb-series"></a>HB シリーズの DRAM

現時点では、HB シリーズの VM はゲスト VM に 228 GB の RAM しか公開できません。 これは、ゲスト VM 用に予約されている AMD CCX (NUMA ドメイン) のローカル DRAM にページが割り当てられないようにするための Azure ハイパーバイザーの既知の制限によるものです。

## <a name="accelerated-networking"></a>高速ネットワーク

現時点では Azure Accelerated Networking は有効ではありませんが、プレビュー期間中に進める予定です。 この機能がサポートされたら、お客様にお知らせします。

## <a name="qp0-access-restriction"></a>qp0 アクセス制限

セキュリティの脆弱性の原因となる可能性がある低レベルのハードウェア アクセスを防ぐために、Queue Pair 0 はゲスト VM からアクセスできません。 これは、通常、ConnectX-5 NIC の管理や、ibdiagnet などの InfiniBand 診断の実行に関連するアクションのみに影響します。エンド ユーザー アプリケーション自体には影響しません。

## <a name="gss-proxy"></a>GSS プロキシ

GSS プロキシには CentOS/RHEL 7.5 の既知のバグがあり、NFS と共に使用すると、パフォーマンスと応答性が大幅に低下することがあります。 これは、次の方法で軽減できます。

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>キャッシュのクリーニング

HPC システムでは、ジョブの完了後、次のユーザーに同じノードが割り当てられる前にメモリをクリーンアップすると便利なことがよくあります。 Linux でアプリケーションを実行した後、アプリケーションを実行していないにもかかわらず、バッファーのメモリが増加する一方で使用可能なメモリが減少することがあります。

![コマンド プロンプトのスクリーンショット](./media/known-issues/cache-cleaning-1.png)

`numactl -H` を使用すると、メモリをバッファー処理している 1 つまたは複数 (場合によってはすべて) の NUMAnode がわかります。 Linux では、ユーザーは 3 つの方法でキャッシュをクリーンアップし、バッファー処理またはキャッシュされたメモリを "空き" に戻すことができます。 ルートになるか、sudo アクセス許可を持つ必要があります。

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![コマンド プロンプトのスクリーンショット](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>カーネルの警告

Linux で HB シリーズの VM を起動すると、次のようなカーネルの警告メッセージが表示されることがあります。

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```

この警告は無視できます。 これは、Azure ハイパーバイザーの既知の制限事項であり、今後対処する予定です。


## <a name="infiniband-driver-installation-on-infiniband-enabled-n-series-vm-sizes"></a>InfiniBand 対応 N シリーズ VM サイズの InfiniBand ドライバー インストール

NC24r_v3 と ND40r_v2 では SR-IOV が有効になります。一方、NC24r と NC24r_v2 では SR-IOV は有効になりません。 2 つに分かれることの詳細は[こちら](../../sizes-hpc.md#rdma-capable-instances)にあります。
InfiniBand (IB) は、OFED ドライバーがある SR-IOV 対応 VM サイズ上で構成できます。一方、SR-IOV ではない VM サイズには ND ドライバーが必要です。 この IB サポートは [CentOS-HPC VMI](configure.md) で適切に利用できます。 Ubuntu の場合、[こちらのドキュメント](enable-infiniband.md#vm-images-with-infiniband-drivers)で説明されているように OFED ドライバーと ND ドライバーの両方をインストールする方法については[こちらの指示](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)をご覧ください。


## <a name="next-steps"></a>次のステップ

- [HB シリーズの概要](hb-series-overview.md)と [HC シリーズの概要](hc-series-overview.md)に関するページを参照して、パフォーマンスとスケーラビリティのためにワークロードを最適に構成する方法を学習します。
- [Azure Compute Tech Community のブログ](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)で、最新の発表および HPC の例と結果について参照します。
- HPC ワークロードの実行をアーキテクチャの面から見た概要については、「[Azure でのハイ パフォーマンス コンピューティング (HPC)](/azure/architecture/topics/high-performance-computing/)」をご覧ください。

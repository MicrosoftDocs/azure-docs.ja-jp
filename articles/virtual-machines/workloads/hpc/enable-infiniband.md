---
title: HPC VM で InifinBand を有効にする - Azure Virtual Machines | Microsoft Docs
description: Azure HPC VM で InfiniBand を有効にする方法について説明します。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/01/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 0cbfed307cea1bd98bf864046a8c08edb849226a
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87797987"
---
# <a name="enable-infiniband"></a>InfiniBand の有効化

[RDMA 対応](../../sizes-hpc.md#rdma-capable-instances)の [H シリーズ](../../sizes-hpc.md)および [N シリーズ](../../sizes-gpu.md) VM の通信は、低待機時間で高帯域幅の InfiniBand ネットワークを介して行われます。 そのような相互接続を介した RDMA 機能は、分散ノードの HPC および AI ワークロードのスケーラビリティとパフォーマンスを向上させるために重要です。 InfiniBand 対応の H シリーズおよび N シリーズの VM は、最適で一貫した RDMA のパフォーマンスのため、小直径設計の非ブロッキング ファット ツリーで接続されます。

対応する VM サイズで InfiniBand を有効にするには、さまざまな方法があります。

## <a name="vm-images-with-infiniband-drivers"></a>InfiniBand ドライバーが含まれる VM イメージ
Marketplace でサポートされている VM イメージの一覧については、「[VM イメージ](configure.md#vm-images)」を参照してください。これらは、InfiniBand ドライバーがあらかじめ読み込まれているか (SR-IOV VM または非 SR-IOV VM の場合)、または適切なドライバーを使用して構成できます。
SR-IOV が有効な [RDMA 対応 VM](../../sizes-hpc.md#rdma-capable-instances) の場合、Marketplace の [CentOS-HPC バージョン 7.6 またはそれ以降](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557)のバージョンの VM イメージを使用するのが、最も簡単に始める方法です。
Ubuntu VM イメージは、[こちらの手順](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)を使用して、SR-IOV および非 SR-IOV 対応 VM に対する適切なドライバーで構成できます。

## <a name="infiniband-driver-vm-extensions"></a>InfiniBand ドライバー VM 拡張機能
Linux の場合、SR-IOV が有効になっている H シリーズと N シリーズの VM で、[InfiniBandDriverLinux VM 拡張機能](../../extensions/hpc-compute-infiniband-linux.md) を使用して、Mellanox OFED ドライバーをインストールし、InfiniBand を有効にすることができます。

Windows では、[InfiniBandDriverWindows VM 拡張機能](../../extensions/hpc-compute-infiniband-windows.md)は、RDMA 接続用に Windows Network Direct ドライバーを (SR-IOV に非対応の VM 上に) インストールするか、または Mellanox OFED ドライバーを (SR-IOV VM 上に) インストールします。 A8 インスタンスと A9 インスタンスの特定のデプロイでは、HpcVmDrivers 拡張機能は自動的に追加されます。 HpcVmDrivers VM 拡張機能は廃止される予定であり、更新されないことに注意してください。

VM に VM 拡張機能を追加するには、[Azure PowerShell](/powershell/azure/) コマンドレットを使用できます。 詳しくは、[仮想マシン拡張機能とその機能](../../extensions/overview.md)に関する記事をご覧ください。 [クラシック デプロイ モデル](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic)にデプロイされている VM にも拡張機能を使用できます。

## <a name="manual-installation"></a>手動のインストール
[Mellanox OpenFabrics ドライバー (OFED)](https://www.mellanox.com/products/InfiniBand-VPI-Software) は、[SR-IOV が有効になっている](../../sizes-hpc.md#rdma-capable-instances) [H シリーズ](../../sizes-hpc.md)と [N シリーズ](../../sizes-gpu.md)の VM に手動でインストールできます。

### <a name="linux"></a>Linux
[Linux 用 OFED ドライバー](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed)は、次の例を使用してインストールできます。 これらの例は RHEL/CentOS に対するものですが、手順は一般的で、Ubuntu (16.04、18.04、19.04、20.04)、SLES (12 SP4、15) などの互換性のある Linux オペレーティング システムで使用できます。 他のディストリビューションのその他の例については、[azhpc-images リポジトリ](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mellanoxofed.sh)を参照してください。 受信トレイドライバーも同様に機能しますが、OFED ドライバーではさらに多くの機能が提供されます。

```bash
MLNX_OFED_DOWNLOAD_URL=http://content.mellanox.com/ofed/MLNX_OFED-5.0-2.1.8.0/MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz
# Optionally verify checksum
wget --retry-connrefused --tries=3 --waitretry=5 $MLNX_OFED_DOWNLOAD_URL
tar zxvf MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz

KERNEL=( $(rpm -q kernel | sed 's/kernel\-//g') )
KERNEL=${KERNEL[-1]}
# Uncomment the lines below if you are running this on a VM
#RELEASE=( $(cat /etc/centos-release | awk '{print $4}') )
#yum -y install http://olcentgbl.trafficmanager.net/centos/${RELEASE}/updates/x86_64/kernel-devel-${KERNEL}.rpm
yum install -y kernel-devel-${KERNEL}
./MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64/mlnxofedinstall --kernel $KERNEL --kernel-sources /usr/src/kernels/${KERNEL} --add-kernel-support --skip-repo
```

### <a name="windows"></a>Windows
Windows の場合は、[Windows 用の Mellanox OFED ドライバー](https://www.mellanox.com/products/adapter-software/ethernet/windows/winof-2)をダウンロードしてインストールします。

## <a name="enable-ip-over-infiniband-ib"></a>IP over InfiniBand (IB) を有効にする
MPI ジョブを実行する予定がある場合は、通常、IPoIB は必要ありません。 MPI ライブラリでは、IB 通信に動詞インターフェイスが使用されます (MPI ライブラリの TCP/IP チャネルを明示的に使用しない場合)。 ただし、通信に TCP/IP を使用するアプリがあり、IB 経由で実行したい場合は、IB インターフェイスで IPoIB を使用できます。 次のコマンド (RHEL/CentOS 用) を実行して、IP over InfiniBand を有効にします。

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>次のステップ

- VM で[サポートされているさまざまな MPI ライブラリ](setup-mpi.md)のインストールと最適な構成の詳細について確認します。
- [HB シリーズの概要](hb-series-overview.md)と [HC シリーズの概要](hc-series-overview.md)に関するページを参照して、パフォーマンスとスケーラビリティのためにワークロードを最適に構成する方法を学習します。
- [Azure Compute Tech Community のブログ](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)で、最新の発表および HPC の例と結果について参照します。
- HPC ワークロードの実行をアーキテクチャの面から見た概要については、「[Azure でのハイ パフォーマンス コンピューティング (HPC)](/azure/architecture/topics/high-performance-computing/)」をご覧ください。

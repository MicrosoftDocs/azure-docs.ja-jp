---
title: Azure Site Recovery を使用した Azure VM ディザスター リカバリーのための高速ネットワークを有効にする
description: Azure 仮想マシンのディザスター リカバリーを行う Azure Site Recovery で高速ネットワークを有効にする方法を説明します
services: site-recovery
documentationcenter: ''
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: harshacs
ms.openlocfilehash: 1d2d3b3aacc00428c96cde0f8230421a98151ae2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000251"
---
# <a name="accelerated-networking-with-azure-virtual-machine-disaster-recovery"></a>高速ネットワークと Azure 仮想マシンのディザスター リカバリー

高速ネットワークは VM へのシングル ルート I/O 仮想化 (SR-IOV) を可能にします。これにより、ネットワークのパフォーマンスが大幅に向上します。 この高パフォーマンスのパスによってデータパスからホストがバイパスされ、サポートされる VM タイプの最も要件の厳しいネットワーク ワークロードで使用した場合でも、待ち時間、ジッター、CPU 使用率が軽減されます。 次の図は、2 台の VM 間の通信を、高速ネットワークを使う場合と使わない場合とで比較したものです。

![比較](./media/azure-vm-disaster-recovery-with-accelerated-networking/accelerated-networking-benefit.png)

Azure Site Recovery では、別の Azure リージョンにフェールオーバーされる Azure 仮想マシンで、高速ネットワークのメリットを活用できます。 この記事では、Azure Site Recovery でレプリケートされた Azure 仮想マシンに対して高速ネットワークを有効にする方法について説明します。

## <a name="prerequisites"></a>前提条件

開始する前に、次の事柄について理解していることを確認してください。
-   Azure 仮想マシンの[レプリケーション アーキテクチャ](azure-to-azure-architecture.md)
-   Azure 仮想マシンの[レプリケーションの設定](azure-to-azure-tutorial-enable-replication.md)
-   Azure 仮想マシンの[フェールオーバー](azure-to-azure-tutorial-failover-failback.md)

## <a name="accelerated-networking-with-windows-vms"></a>高速ネットワークと Windows VM

Azure Site Recovery では、ソース仮想マシンで高速ネットワークが有効になっている場合にのみ、レプリケートされた仮想マシンに対する高速ネットワークをサポートします。 ソース仮想マシンで高速ネットワークが有効になっていない場合は、[ここ](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)をクリックして、Windows 仮想マシンに対して高速ネットワークを有効にする方法を確認できます。

### <a name="supported-operating-systems"></a>サポートされるオペレーティング システム
Azure ギャラリーでは次のディストリビューションが既定でサポートされています。
* **Windows Server 2016 Datacenter**
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>サポートされている VM インスタンス
高速ネットワークは、2 つ以上の vCPU を持つ、コンピューティングに最適化された多くの汎用のインスタンス サイズでサポートされています。  サポートされているシリーズは、D/DSv2 と F/Fs です。

ハイパースレッディングをサポートするインスタンスでは、4 以上の vCPU を持つ VM インスタンスで高速ネットワークがサポートされています。 サポートされている系列は、D/DSv3、E/ESv3、Fsv2、Ms/Mms です。

VM インスタンスの詳細については、[Windows VM のサイズ](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページを参照してください。

## <a name="accelerated-networking-with-linux-vms"></a>高速ネットワークと Linux VM

Azure Site Recovery では、ソース仮想マシンで高速ネットワークが有効になっている場合にのみ、レプリケートされた仮想マシンに対する高速ネットワークをサポートします。 ソース仮想マシンで高速ネットワークが有効になっていない場合は、[ここ](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms)をクリックして、Linux 仮想マシンに対して高速ネットワークを有効にする方法を確認できます。

### <a name="supported-operating-systems"></a>サポートされるオペレーティング システム
Azure ギャラリーでは次のディストリビューションが既定でサポートされています。
* **Ubuntu 16.04**
* **SLES 12 SP3**
* **RHEL 7.4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian "Stretch" (バックポート カーネルを含む)**
* **Oracle Linux 7.4**

### <a name="supported-vm-instances"></a>サポートされている VM インスタンス
高速ネットワークは、2 つ以上の vCPU を持つ、コンピューティングに最適化された多くの汎用のインスタンス サイズでサポートされています。  サポートされているシリーズは、D/DSv2 と F/Fs です。

ハイパースレッディングをサポートするインスタンスでは、4 以上の vCPU を持つ VM インスタンスで高速ネットワークがサポートされています。 サポートされている系列は、D/DSv3、E/ESv3、Fsv2、Ms/Mms です。

VM インスタンスの詳細については、「[Linux 仮想マシンのサイズ](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。

## <a name="enabling-accelerated-networking-for-replicated-vms"></a>レプリケートされた VM に対する高速ネットワークの有効化

Azure 仮想マシンに対して[レプリケーションを有効にする](azure-to-azure-tutorial-enable-replication.md)と、Site Recovery によって、その仮想マシンのネットワーク インターフェイスで高速ネットワークが有効になっているかどうかが自動的に検出されます。 高速ネットワークが既に有効になっている場合、Site Recovery は、レプリケートされた仮想マシンのネットワーク インターフェイスに高速ネットワークを自動的に構成します。

高速ネットワークの状態は、レプリケートされた仮想マシンの **[コンピューティングとネットワーク]** 設定の **[ネットワーク インターフェイス]** セクションで確認できます。

![高速ネットワーク設定](./media/azure-vm-disaster-recovery-with-accelerated-networking/compute-network-accelerated-networking.png)

ソース仮想マシンで高速ネットワークを有効にした場合は、レプリケートされた仮想マシンのネットワーク インターフェイスに対する高速ネットワークを次のプロセスで有効にできます。
1. レプリケートされた仮想マシンの **[コンピューティングとネットワーク]** 設定を開きます。
2. **[ネットワーク インターフェイス]** セクションでネットワーク インターフェイスの名前をクリックします。
3. **[ターゲット]** 列の [高速ネットワーク] のドロップダウンから **[有効]** を選択します。

![Accelerated Networking の有効化](./media/azure-vm-disaster-recovery-with-accelerated-networking/network-interface-accelerated-networking-enabled.png)

Site Recovery によって高速ネットワークが自動的に有効になっていない既存のレプリケートされた仮想マシンでも、上記のプロセスを実行する必要があります。

## <a name="next-steps"></a>次のステップ
- [高速ネットワークのメリット](../virtual-network/create-vm-accelerated-networking-powershell.md#benefits)の詳細を確認する。
- [Windows 仮想マシン](../virtual-network/create-vm-accelerated-networking-powershell.md#limitations-and-constraints)と[Linux 仮想マシン](../virtual-network/create-vm-accelerated-networking-cli.md#limitations-and-constraints)に対する高速ネットワークの制限と制約の詳細を確認する。
- アプリケーションのフェールオーバーを自動化するための[復旧計画](site-recovery-create-recovery-plans.md)の詳細を確認する。
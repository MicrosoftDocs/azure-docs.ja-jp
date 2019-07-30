---
title: Azure 仮想マシンのネットワーク スループット | Microsoft Docs
description: Azure 仮想マシンのネットワーク スループットについて説明します。
services: virtual-network
documentationcenter: na
author: steveesp
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/26/2019
ms.author: steveesp
ms.reviewer: kumud, mareat
ms.openlocfilehash: f5694e18d5743118e2b6e73708dd3acb17151198
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67874937"
---
# <a name="virtual-machine-network-bandwidth"></a>仮想マシンのネットワーク帯域幅

Azure の VM には多様なサイズと種類があり、パフォーマンス機能の組み合わせもそれぞれ異なっています。 機能の 1 つがネットワーク スループット (帯域幅) で、メガビット/秒 (Mbps) で測定されます。 仮想マシンは共有ハードウェアでホストされているため、同じハードウェアを共有する仮想マシン間でネットワーク容量が公平に分配される必要があります。 大きな仮想マシンには、小さい仮想マシンよりも相対的に多くの帯域幅が割り当てられます。
 
各仮想マシンに割り当てられたネットワーク帯域幅は、仮想マシンからのエグレス (送信) トラフィックで測定されます。 仮想マシンから送信されるすべてのネットワーク トラフィックは、送信先に関係なく、割り当てられた制限に達するまでカウントされます。 たとえば、ある仮想マシンの制限が 1,000 Mbps である場合、送信トラフィックの送信先が同じ仮想ネットワーク内の仮想マシンであっても Azure 外部であっても、この制限が適用されます。
 
受信は、直接的には測定も制限もされません。 ただし、CPU やストレージの制限などの他の要素があり、仮想マシンの受信データ処理機能に影響する可能性はあります。

高速ネットワークは、ネットワーク パフォーマンス (待ち時間、スループット、CPU 使用率など) の向上を目的として設計された機能です。 高速ネットワークは仮想マシンのスループットを向上させますが、向上できるのは仮想マシンに割り当てられた帯域幅までです。 高速ネットワークの詳細については、[Windows](create-vm-accelerated-networking-powershell.md) または [Linux](create-vm-accelerated-networking-cli.md) 仮想マシンの高速ネットワークに関する記事を参照してください。
 
Azure の仮想マシンには、少なくとも 1 つ (複数可) のネットワーク インターフェイスが接続されている必要があります。 仮想マシンに割り当てられる帯域幅は、仮想マシンに接続されているすべてのネットワーク インターフェイス全体の送信トラフィックの合計です。 つまり、帯域幅は仮想マシンごとに割り当てられており、仮想マシンに接続されているネットワーク インターフェイスの数は関係ありません。 さまざまな Azure VM サイズがサポートするネットワーク インターフェイスの数については、[Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) と [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の Azure VM サイズに関するページを参照してください。 

## <a name="expected-network-throughput"></a>想定されるネットワーク スループット

VM の各サイズで想定される送信スループットとサポートされるネットワーク インターフェイスの数については、[Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) と [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の Azure VM サイズに関するページを参照してください。 型 (汎用など) を選択し、結果として表示されるページでサイズ シリーズ (Dv2 シリーズなど) を選択します。 各シリーズの表の最後に、**最大 NIC 数/想定ネットワーク パフォーマンス (Mbps)** というネットワーク仕様の列があります。 

このスループット制限が仮想マシンに適用されます。 スループットは、次の要因には影響されません。
- **ネットワーク インターフェイスの数**:帯域幅の制限は、仮想マシンからのすべての送信トラフィックの累積です。
- **高速ネットワーク**:この機能は公開された制限まで達成するためには役立ちますが、制限自体は変更されません。
- **トラフィックの送信先**:すべての送信先が、送信制限に達するまでカウントされます。
- **プロトコル**:すべてのプロトコルに対するすべての送信トラフィックが、制限に達するまでカウントされます。

## <a name="network-flow-limits"></a>ネットワーク フローの制限

帯域幅に加えて、特定の時点で VM 上に存在するネットワーク接続の数がそのネットワーク パフォーマンスに影響を与える場合があります。 Azure のネットワーク スタックは、TCP/UDP 接続の各方向の状態を 'フロー' と呼ばれるデータ構造の中に保持します。 標準的な TCP/UDP 接続では、受信方向に 1 つ、送信方向にもう 1 つの 2 つのフローが作成されます。 

エンドポイント間のデータ転送では、データ転送を実行するフローに加えていくつかのフローを作成する必要があります。 その例として、DNS 解決のために作成されるフローや、ロード バランサーの正常性プローブのために作成されるフローがあります。 また、ゲートウェイ、プロキシ、ファイアウォールなどのネットワーク仮想アプライアンス (NVA) では、アプライアンスで終了した接続やアプライアンスで開始された接続のためにもフローが作成されることに注意してください。 

![転送アプライアンスを経由した TCP 通信のためのフローの数](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-recommendations"></a>フローの制限と推奨事項

現在、Azure のネットワーク スタックは、CPU コアが 8 個を超える VM での良好なパフォーマンスには 250K 個の合計ネットワーク フロー、および CPU コアが 8 個未満の VM での良好なパフォーマンスには 100K 個の合計フローをサポートしています。 この制限を超えると、ネットワーク パフォーマンスは、ハード制限である 1M 個の合計フロー (500K 個の受信と 500K 個の送信) まで追加フローに対して適切に低下し、それ以降は追加フローが破棄されます。

||CPU コアが 8 個未満の VM|CPU コアが 8 個を超える VM|
|---|---|---|
|<b>良好なパフォーマンス</b>|100K 個のフロー |250K 個のフロー|
|<b>パフォーマンスの低下</b>|100K 個を超えるフロー|250K 個を超えるフロー|
|<b>フローの制限</b>|1M 個のフロー|1M 個のフロー|

[Azure Monitor](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines) では、ネットワーク フローの数や、VM または VMSS インスタンスでのフロー作成の頻度を追跡するためのメトリックを使用できます。

![azure-monitor-flow-metrics.png](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

接続の確立と終了はパケット処理ルーチンと CPU を共有するため、接続の確立と終了の頻度もネットワーク パフォーマンスに影響を与える場合があります。 予測されるトラフィック パターンに対してワークロードをベンチマークし、パフォーマンスのニーズを満たすようにワークロードを適切にスケールアウトすることをお勧めします。 

## <a name="next-steps"></a>次の手順

- [仮想マシンのオペレーティング システムに対するネットワーク スループットを最適化する](virtual-network-optimize-network-bandwidth.md)
- 仮想マシンの[ネットワーク スループットをテストする](virtual-network-bandwidth-testing.md)

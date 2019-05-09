---
title: Azure Service Fabric のネットワークに関するベスト プラクティス | Microsoft Docs
description: Service Fabric ネットワークを管理するためのベスト プラクティス。
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 86ad6fce34f323d94f7b9c318ba81f547360d4df
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2019
ms.locfileid: "56804962"
---
# <a name="networking"></a>ネットワーク

Azure Service Fabric のクラスターを作成し、管理するときには、ノードとアプリケーションにネットワーク接続を提供します。 ネットワーク リソースには、IP アドレス範囲、仮想ネットワーク、ロード バランサー、およびネットワーク セキュリティ グループが含まれます。 この記事では、これらのリソースのベスト プラクティスについて学習します。

Azure の「[Service Fabric のネットワーク パターン](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking)」を調べ直して、次の機能を使用するクラスターの作成方法を学びます。既存の仮想ネットワークまたはサブネット、静的パブリック IP アドレス、内部専用ロード バランサー、内部および外部ロード バランサー。

## <a name="infrastructure-networking"></a>インフラストラクチャ ネットワーク
Resource Manager テンプレートで enableAcceleratedNetworking プロパティを宣言し、高速ネットワークを使用して仮想マシンのパフォーマンスを最大化します。次に示したのは、高速ネットワークを有効にする、仮想マシン スケール セットの NetworkInterfaceConfigurations のスニペットです。

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
Service Fabric クラスターは、[高速ネットワークを導入した Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) と[高速ネットワークを導入した Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) でプロビジョニングできます。

高速ネットワークは、Azure 仮想マシン シリーズの次の SKU でサポートされています。D/DSv2、D/DSv3、E/ESv3、F/FS、FSv2、Ms/Mms。 高速ネットワークのテストは、Service Fabric Windows クラスターについては 2019 年 1 月 23 日に Standard_DS8_v3 SKU を使用して、Service Fabric Linux クラスターについては 2019 年 1 月 29 日に Standard_DS12_v2 を使用して、正常に行われました。

既存の Service Fabric クラスターで高速ネットワークを有効にするには、最初に[仮想マシン スケール セットを追加することで Service Fabric クラスターをスケールアウト](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)する必要があります。
1. 高速ネットワークを有効にして 1 つの NodeType をプロビジョニングします
2. 高速ネットワークを有効にしてプロビジョニングした NodeType に、サービスとその状態を移行します

所定の位置で高速ネットワークを有効にするとダウンタイムが発生するため、既存のクラスターで高速ネットワークを有効にするためには、インフラストラクチャのスケール アウトが必要です。可用性セット内のすべての仮想マシンは、[任意の既存 NIC で高速ネットワークを有効にする前に停止し、割り当てを解除する](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms)必要があるためです。

## <a name="cluster-networking"></a>クラスター ネットワーク

* Service Fabric クラスターは、「[Service Fabric のネットワーク パターン](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking)」で概要が説明されている手順に従って、既存の仮想ネットワークにデプロイできます。

* クラスターへの受信および送信トラフィックを制限するノードの種類の場合は、ネットワーク セキュリティ グループ (NSG) の使用が推奨されます。 NSG で、必要なポートが開かれていることを確認します。 例: ![Service Fabric の NSG ルール][NSGSetup]

* Service Fabric システム サービスを含むプライマリのノードの種類は、外部ロード バランサーを介して公開する必要はなく、[内部ロード バランサー](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer)によって公開できます

* お使いのクラスターには[静的パブリック IP アドレス](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1)を使用します。

## <a name="application-networking"></a>アプリケーション ネットワーク

* Windows コンテナー ワークロードを実行するには、[Open ネットワーク モード](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode)を使用してサービス間の通信を容易にします。

* [Traefik](https://docs.traefik.io/configuration/backends/servicefabric/) や [Service Fabric リバース プロキシ](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy)などのリバース プロキシを使用して、80 や 443 などの一般的なアプリケーション ポートを公開します。

## <a name="next-steps"></a>次の手順

* Windows Server を実行している VM またはコンピューター上にクラスターを作成する:[Windows Server 用の Service Fabric クラスターの作成](service-fabric-cluster-creation-for-windows-server.md)
* Linux を実行している VM またはコンピューター上にクラスターを作成する:[Linux クラスターの作成](service-fabric-cluster-creation-via-portal.md)
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png

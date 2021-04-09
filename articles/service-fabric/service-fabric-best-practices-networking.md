---
title: Azure Service Fabric のネットワークに関するベスト プラクティス
description: Azure Service Fabric を使用したネットワーク接続の管理に関するルールと設計上の考慮事項。
author: chrpap
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: chrpap
ms.openlocfilehash: caba864e77822ccab649f694df7e63e0ee5d6e51
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732566"
---
# <a name="networking"></a>ネットワーク

Azure Service Fabric のクラスターを作成し、管理するときには、ノードとアプリケーションにネットワーク接続を提供します。 ネットワーク リソースには、IP アドレス範囲、仮想ネットワーク、ロード バランサー、およびネットワーク セキュリティ グループが含まれます。 この記事では、これらのリソースのベスト プラクティスについて学習します。

Azure の「[Service Fabric のネットワーク パターン](./service-fabric-patterns-networking.md)」を調べ直して、次の機能を使用するクラスターの作成方法を学びます。既存の仮想ネットワークまたはサブネット、静的パブリック IP アドレス、内部専用ロード バランサー、内部および外部ロード バランサー。

## <a name="infrastructure-networking"></a>インフラストラクチャ ネットワーク
Resource Manager テンプレートで *enableAcceleratedNetworking* プロパティを宣言することにより、高速ネットワークでの仮想マシンのパフォーマンスを最大にします。高速ネットワークを有効にする、仮想マシン スケール セットの NetworkInterfaceConfigurations のスニペットを次に示します。

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
Service Fabric クラスターは、[高速ネットワークを導入した Linux](../virtual-network/create-vm-accelerated-networking-cli.md) と[高速ネットワークを導入した Windows](../virtual-network/create-vm-accelerated-networking-powershell.md) でプロビジョニングできます。

高速ネットワークは、Azure 仮想マシン シリーズの次の SKU でサポートされています。D/DSv2、D/DSv3、E/ESv3、F/FS、FSv2、Ms/Mms。 高速ネットワークのテストは、Service Fabric Windows クラスターについては 2019 年 1 月 23 日に Standard_DS8_v3 SKU を使用して、Service Fabric Linux クラスターについては 2019 年 1 月 29 日に Standard_DS12_v2 を使用して、問題なく行われました。 高速ネットワークには 4 つ以上の vCPU が必要であることに注意してください。 

既存の Service Fabric クラスターで高速ネットワークを有効にするには、最初に[仮想マシン スケール セットを追加することで Service Fabric クラスターをスケールアウト](./virtual-machine-scale-set-scale-node-type-scale-out.md)する必要があります。
1. 高速ネットワークを有効にして 1 つの NodeType をプロビジョニングします
2. 高速ネットワークを有効にしてプロビジョニングした NodeType に、サービスとその状態を移行します

所定の位置で高速ネットワークを有効にするとダウンタイムが発生するため、既存のクラスターで高速ネットワークを有効にするためには、インフラストラクチャのスケール アウトが必要です。可用性セット内のすべての仮想マシンは、[任意の既存 NIC で高速ネットワークを有効にする前に停止し、割り当てを解除する](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms)必要があるためです。

## <a name="cluster-networking"></a>クラスター ネットワーク

* Service Fabric クラスターは、「[Service Fabric のネットワーク パターン](./service-fabric-patterns-networking.md)」で概要が説明されている手順に従って、既存の仮想ネットワークにデプロイできます。

* クラスターの受信および送信トラフィックを制限するには、ノードの種類に対してネットワーク セキュリティ グループ (NSG) を使用することをお勧めします。 NSG で、必要なポートが開かれていることを確認します。 

* Service Fabric システム サービスを含むプライマリのノードの種類は、外部ロード バランサーを介して公開する必要はなく、[内部ロード バランサー](./service-fabric-patterns-networking.md#internal-only-load-balancer)によって公開できます

* お使いのクラスターには[静的パブリック IP アドレス](./service-fabric-patterns-networking.md#static-public-ip-address-1)を使用します。

## <a name="network-security-rules"></a>ネットワーク セキュリティの規則

ここでの基本的なルールは、Azure マネージド Service Fabric クラスターのセキュリティ ロックダウンに対する最小限のものです。 次のポートを開かなかったり、IP/URL を許可しなかったりすると、クラスターの適切な動作が妨げられ、サポートされない可能性があります。 このルール セットでは、[OS イメージの自動アップグレード](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)を使用する必要があります。そうでない場合は、追加のポートを開く必要があります。

### <a name="inbound"></a>着信 
|Priority   |名前               |Port        |Protocol  |source             |到着地       |アクション   
|---        |---                |---         |---       |---                |---               |---
|3900       |Azure              |19080       |TCP       |インターネット           |VirtualNetwork    |Allow
|3910       |Client             |19000       |TCP       |インターネット           |VirtualNetwork    |Allow
|3920       |クラスター            |1025-1027   |TCP       |VirtualNetwork     |VirtualNetwork    |Allow
|3930       |エフェメラル          |49152-65534 |TCP       |VirtualNetwork     |VirtualNetwork    |Allow
|3940       |Application        |20000-30000 |TCP       |VirtualNetwork     |VirtualNetwork    |Allow
|3950       |SMB                |445         |TCP       |VirtualNetwork     |VirtualNetwork    |Allow
|3960       |RDP                |3389-3488   |TCP       |インターネット           |VirtualNetwork    |Deny
|3970       |SSH                |22          |TCP       |インターネット           |VirtualNetwork    |Deny
|3980       |カスタム エンドポイント    |80          |TCP       |インターネット           |VirtualNetwork    |Allow
|4100       |受信ブロック      |443         |Any       |Any                |Any               |Allow

受信セキュリティ規則に関する詳細:

* **Azure** このポートは、クラスターを参照および管理するために、Service Fabric Explorer によって使用されます。また、Azure の管理ポータルに表示するクラスターに関する情報を照会するため、Service Fabric リソース プロバイダーによっても使用されます。 Service Fabric リソース プロバイダーからこのポートにアクセスできない場合、"ノードが見つかりません" や "UpgradeServiceNotReachable" といったメッセージが Azure portal に表示され、ノードとアプリケーションの一覧には何も表示されません。 つまり、Azure の管理ポータルにクラスターが表示されるようにするには、ロード バランサーでパブリック IP アドレスが公開されており、NSG で受信 19080 トラフィックが許可されている必要があります。  

* **クライアント**。 REST、PowerShell、CLI などの API に対するクライアント接続エンドポイント。 

* **クラスター**。 ノード間通信に使用されます。ブロックしないでください。

* **エフェメラル**。 Service Fabric がこれらのポートの一部をアプリケーション ポートとして使用し、残りは OS に使用できます。 また、この範囲が OS にある既存の範囲にマップされるため、ここのサンプルで指定した範囲はあらゆる用途に使用できます。 開始ポートと終了ポートの差は 255 以上にしてください。 この範囲は OS と共有されるため、この差が小さすぎると競合が発生する場合があります。 構成されている動的ポート範囲を確認するには、*netsh int ipv4 show dynamic port tcp* を実行します。 これらのポートは、Linux クラスターの場合は必要ありません。

* **アプリケーション**。 アプリケーション ポートの範囲は、アプリケーションのエンドポイント要求に対応できるように十分な大きさにする必要があります。 この範囲は、マシン上の動的なポートの範囲 (つまり、構成で設定されている ephemeralPorts の範囲) とは排他的である必要があります。 Service Fabric では、新しいポートが必要なときはこれらのポートが常に使用され、ノード上でこれらのポートに対してファイアウォールを開く処理が行われます。

* **SMB**。 SMB プロトコルは、2 つのシナリオのために ImageStore サービスによって使用されています。 このポートは、ノードで ImageStore からパッケージをダウンロードするため、およびこれらをレプリカ間でレプリケートするために必要です。 

* **RDP**。 省略可能。ジャンプボックスのシナリオでインターネットまたは VirtualNetwork から RDP が必要な場合。 

* **SSH**。 省略可能。ジャンプボックスのシナリオでインターネットまたは VirtualNetwork から SSH が必要な場合。

* **カスタム エンドポイント**。 アプリケーションでインターネット アクセス可能なエンドポイントを有効にする例。

### <a name="outbound"></a>送信

|Priority   |名前               |Port        |Protocol  |source             |到着地       |アクション   
|---        |---                |---         |---       |---                |---               |---
|3900       |ネットワーク            |Any         |TCP       |VirtualNetwork     |VirtualNetwork    |Allow
|3910       |リソース プロバイダー  |443         |TCP       |VirtualNetwork     |ServiceFabric     |Allow
|3920       |アップグレード            |443         |TCP       |VirtualNetwork     |インターネット          |Allow
|3950       |送信ブロック     |Any         |Any       |Any                |Any               |拒否

送信セキュリティ規則に関する詳細:

* **ネットワーク**: サブネット用および別の仮想ネットワークへの通信チャネル。

* **リソース プロバイダー**。 Service Fabric リソース プロバイダーですべての ARM デプロイを実行するための、UpgradeService による接続。

* **アップグレード**。 取得にアドレス download.microsoft.com を使用しているアップグレード サービス。これは、セットアップ、再イメージ化、およびランタイム アップグレードに必要です。 サービスは、動的 IP アドレスを使用して動作します。 "内部専用" ロード バランサーのシナリオでは、ポート 443 での送信トラフィックを許可する規則が含まれるテンプレートに、追加の外部ロード バランサーを追加する必要があります。 必要に応じて、セットアップが成功した後でこのポートをブロックできますが、その場合、アップグレード パッケージをノードに配布するか、短時間だけポートを開く必要があり、後で手動アップグレードが必要になります。

セキュリティ ロックダウンに関する問題を追跡するには、[NSG フロー ログ](../network-watcher/network-watcher-nsg-flow-logging-overview.md)および [Traffic Analytics](../network-watcher/traffic-analytics.md) と共に Azure Firewall を使用します。 [Service Fabric と NSG](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) の ARM テンプレートは、始めるのに適した例です。 


## <a name="application-networking"></a>アプリケーション ネットワーク

* Windows コンテナー ワークロードを実行するには、[Open ネットワーク モード](./service-fabric-networking-modes.md#set-up-open-networking-mode)を使用してサービス間の通信を容易にします。

* [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) や [Service Fabric リバース プロキシ](./service-fabric-reverseproxy.md)などのリバース プロキシを使用して、80 や 443 などの一般的なアプリケーション ポートを公開します。

* Azure クラウド ストレージからベース レイヤーをプルできない、インターネットから物理的に隔離されたエアギャップ マシン上でホストされている Windows コンテナーでは、Docker デーモンの [--allow-nondistributable-artifacts](/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) フラグを使用して、外部レイヤーの動作を上書きします。

## <a name="next-steps"></a>次のステップ

* Windows Server を実行している VM またはコンピューター上にクラスターを作成する:[Windows Server 用の Service Fabric クラスターの作成](service-fabric-cluster-creation-for-windows-server.md)
* Linux を実行している VM またはコンピューター上にクラスターを作成する:[Linux クラスターの作成](service-fabric-cluster-creation-via-portal.md)
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png

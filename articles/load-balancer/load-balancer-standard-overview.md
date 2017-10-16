---
title: "Azure Load Balancer Standard の概要 | Microsoft Docs"
description: "Azure Load Balancer Standard の機能の概要"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: 8ad98f7ef226fa94b75a8fc6b2885e7f0870483c
ms.openlocfilehash: 2728e8b1e190b4ecd0635925b96e97775564a2ee
ms.contentlocale: ja-jp
ms.lasthandoff: 09/29/2017

---

# <a name="azure-load-balancer-standard-overview-preview"></a>Azure Load Balancer Standard の概要 (プレビュー)

Azure Load Balancer Standard SKU とパブリック IP Standard SKU を組み合わせて使用すると、拡張性と信頼性に優れたアーキテクチャを構築できます。  Load Balancer Standard を使用するアプリケーションは、低待機時間、高スループット、すべての TCP および UDP アプリケーションの数百万のフローに対応するスケールに加え、新しい機能も利用できます。

>[!NOTE]
> Load Balancer Standard SKU は現在プレビュー段階です。 プレビュー期間は、一般公開リリースの機能と同じレベルの可用性と信頼性がない場合があります。 詳細については、[Microsoft Azure プレビューのMicrosoft Azure 追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 運用サービスには、一般公開されている [Load Balancer Basic SKU](load-balancer-overview.md) を使用してください。

## <a name="why-use-load-balancer-standard"></a>Load Balancer Standard を使用する理由

小規模のデプロイから大規模で複雑なマルチゾーン アーキテクチャまで、さまざまな仮想データ センターで Load Balancer Standard を使用すると、次の機能を利用できます。

- Load Balancer Standard では、[エンタープライズ スケール](#enterprisescale)を実現できます。  エンタープライズ スケールは、仮想ネットワーク内の仮想マシン インスタンス (最大 1000 個の VM インスタンス) でご利用いただけます。

- [新しい診断インサイト](#diagnosticinsights)を使用すると、仮想データ センターのこの重要なコンポーネントについて理解し、コンポーネントの管理やトラブルシューティングを行うことができます。 Azure Monitor (プレビュー) を使用して、新しい多次元メトリックを表示、フィルター処理、グループ化します。多次元メトリックは、フロントエンド、VM、エンドポイントごとの正常性プローブ、TCP 接続試行、送信接続など、データ パスの継続的な正常性測定のためのメトリックです。

- Load Balancer またはパブリック IP の Standard SKU に関連付けられている VM インスタンスで[ネットワーク セキュリティ グループ](#nsg)が必須になりました。ネットワーク セキュリティ グループにより、セキュリティが強化されます。

- ネットワーク仮想アプライアンスや他のアプリケーション シナリオで、[HA ポートは高信頼性とスケールを提供](#highreliability)します。 HA ポートは、内部ロード バランサー フロントエンドのすべてのポートを VM インスタンスのプールに負荷分散します。

- 優れた回復性とスケールを実現する新しい SNAT ポート割り当てモデルが[送信接続](#outboundconnections)で使用されるようになりました。

- [可用性ゾーンを使用する Load Balancer Standard](#availabilityzones) を使用して、ゾーン冗長アーキテクチャとゾーン ベース アーキテクチャを構築できます。これらのアーキテクチャは、どちらもクロスゾーン負荷分散に対応します。 DNS レコードに依存せずにゾーン冗長性を実現できます。単一の IP アドレスが既定でゾーン冗長になり、すべての可用性ゾーンでリージョンの VNet 内の VM に到達できます。


Load Balancer Standard をパブリック構成または内部構成で使用して、次の基本的なシナリオを引き続きサポートできます。

- 受信トラフィックを正常なバックエンド インスタンスに負荷分散する。
- 受信トラフィックを 1 つのバックエンド インスタンスにポート転送する。
- 送信トラフィックを、VNet 内のプライベート IP アドレスからパブリック IP アドレスに変換する。

### <a name = "enterprisescale"></a>エンタープライズ スケール

 高パフォーマンスの仮想データ センターを設計する際に Load Balancer Standard を使用します。 バックエンド プールのスタンドアロン VM インスタンスまたは最大 1000 インスタンスの仮想マシン スケール セットを使用し、任意の TCP または UDP アプリケーションをサポートできます。 Load Balancer Standard では、アプリケーションは低転送待機時間、高スループット パフォーマンス、完全に管理された Azure サービスの数百万のフローに対応するスケールを引き続き利用できます。
 
Load Balancer Standard は、リージョンの VNet 内の任意の VM インスタンスにトラフィックを転送できます。 バックエンド プールのサイズは、次のいずれかの組み合わせで最大 1000 インスタンスまで可能です。

- 可用性セットに含まれていないスタンドアロン VM
- 可用性セットに含まれているスタンドアロン VM
- 最大 1000 インスタンスの仮想マシン スケール セット
- 複数の仮想マシン スケール セット
- VM と仮想マシン スケール セットの混合

可用性セットの要件はなくなりましたが、可用性セットがもたらす他のメリットを得るために可用性セットの使用を選択することもできます。

### <a name = "diagnosticinsights"></a>診断インサイト

Load Balancer Standard には、パブリックおよび内部ロード バランサー構成の新しい多次元診断機能が用意されています。 Azure Monitor (プレビュー) によって次の新しいメトリックが提供されます。これらのメトリックでは、さまざまなダウンストリーム コンシューマーと統合するための機能など、関連するすべての機能を利用します。

| メトリック | Description |
| --- | --- |
| VIP 可用性 | Load Balancer Standard は、リージョン内から ロード バランサー フロントエンドを経て、VM をサポートする SDN スタックに至るまでのデータ パスを継続的に学習します。 正常なインスタンスが保持されていれば、測定ではアプリケーションの負荷分散されたトラフィックと同じパスに従い、顧客が使用している可能性のあるデータ パスを検証します。 測定はアプリケーションには見えないので、干渉することはありません。|
| DIP 可用性 | Load Balancer Standard では、構成内容に従ってアプリケーション エンドポイントの正常性を監視する、分散型の正常性プローブ サービスを使用します。  このメトリックは、Load Balancer プールの個々のインスタンス エンドポイントの集計ビューまたはエンドポイントごとのフィルター ビューを提供します。  正常性プローブ構成で示されているアプリケーションの正常性を、Load Balancer がどのように表示するのかを確認できます。
| SYN パケット | Load Balancer Standard が TCP 接続を終了したり、TCP または UDP パケット フローと対話したりすることはありません。フローとハンドシェイクは、常にソースと VM インスタンスの間で発生します。 TCP プロトコルのすべてのシナリオのトラブルシューティングを適切に行うために、このメトリックを使用して TCP 接続試行の数を把握できます。 このメトリックは、受信済みの TCP SYN パケットの数を報告します。この数はクライアントによるサービスへの接続確立試行を反映している可能性があります。|
| SNAT 接続 | Load Balancer Standard は、パブリック IP アドレス フロントエンドにマスカレードされた送信接続の数を報告します。  SNAT ポートは有限のリソースであるため、このメトリックはアプリケーションが送信接続で SNAT にどれくらい依存しているかを示すことができます。|
| バイト カウンター | Load Balancer Standard は、フロントエンドごとに処理されたデータを報告します。|
| パケット カウンター | Load Balancer Standard は、フロントエンドごとに処理されたパケットを報告します。|

### <a name = "highreliability"></a>高信頼性

アプリケーションをスケールし、高信頼性を維持するために、負荷分散規則を構成します。  個々のポートの規則を構成できます。また、新しい HA ポートを使用して、TCP または UDP ポート番号に関係なく、すべてのトラフィックを分散させることもできます。  

新しい HA ポート機能を使用すると、内部のネットワーク仮想アプライアンスの高可用性とスケールなどの各種シナリオや、個々のポートを指定するのは実用的ではなかったり、望ましくなかったりするその他のシナリオにも対応できるようになります。 HA ポートは、アクティブ/パッシブ シナリオに制限するのではなく、必要な数だけインスタンスを使用できるようにすることで、冗長性とスケールを提供します。 正常性プローブ構成により、正常なインスタンスにのみトラフィックを転送することでサービスが保護されます。

ネットワーク仮想アプライアンスのベンダーは、顧客の単一障害点を排除し、スケールに合わせて複数のアクティブ インスタンスを使用できるようにすることで、ベンダーが完全にサポートする回復性の高いシナリオを提供できます。 アプライアンスで許可されていれば、複数のインタンスにスケールできます。 ネットワーク仮想アプライアンスのベンダーは、これらのシナリオの詳細なガイダンスを提供する必要があります。

### <a name = "availabilityzones"></a>可用性ゾーン

[!INCLUDE [availability-zones-preview-statement](../../includes/availability-zones-preview-statement.md)]

サポートされているリージョンで可用性ゾーンを使用して、アプリケーションの回復性を高めます。 可用性ゾーンは特定のリージョンで現在プレビュー段階であり、追加のオプトインが必要となります。

### <a name="automatic-zone-redundancy"></a>自動ゾーン冗長性

Load Balancer でアプリケーションごとにゾーン冗長またはゾーン フロントエンドを提供するかどうかを選択できます。  Load Balancer Standard では、ゾーン冗長性を簡単に作成できます。  単一のフロントエンド IP アドレスが自動的にゾーン冗長になります。  ゾーン冗長フロントエンドは、リージョン内のすべての可用性ゾーンによって同時に提供されます。 これにより、受信接続と送信接続のゾーン冗長データ パスが作成されます。 Azure のゾーン冗長性には、複数の IP アドレスや DNS レコードは不要です。 

このゾーン冗長性は、パブリックまたは内部フロントエンドで提供されます。 パブリック IP アドレスと内部ロード バランサー フロントエンドのプライベート IP をゾーン冗長にすることができます。

次のコードを使用して、ゾーン冗長パブリック IP アドレスを作成します ("sku" を既存の Resource Manager テンプレートに追加します)。

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

次のコードを使用して、内部ロード バランサーのゾーン冗長フロントエンド IP 構成を作成します ("sku" を既存の Resource Manager テンプレートに追加します)。

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zone_redundant_frontend",
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

パブリック IP フロントエンドがゾーン冗長の場合、VM インスタンスからのすべての送信接続が自動的にゾーン冗長になり、ゾーンの障害から保護されます。  ゾーンの障害が発生しても、SNAT ポートの割り当ては保持されます。

#### <a name="cross-zone-load-balancing"></a>クロスゾーン負荷分散

クロスゾーン負荷分散は、リージョン内のバックエンド プールで利用できるため、仮想マシン インスタンスの柔軟性を最大限に高めることができます。  フロントエンドは、VM インスタンスの可用性ゾーンに関係なく、VNet 内の任意の VM へのフローを提供できます。

さらに、フロントエンド インスタンスとバックエンド インスタンスに特定のゾーンを指定して、特定のゾーンに合わせてデータ パスとリソースを調整することもできます。

VNet とサブネットにはゾーンの制約がないので、必要な VM インスタンスを含むバックエンド プールを定義するだけで構成が完了します。

#### <a name="zonal-deployments"></a>ゾーン展開

必要に応じて、ゾーン フロントエンドを定義して、フロントエンドを特定のゾーンに配置することもできます。  ゾーン フロントエンドは、指定された 1 つの可用性ゾーンでのみ提供されます。ゾーンの VM インスタンスと組み合わせると、リソースを特定のゾーンに配置できます。

次のコードを使用して、可用性ゾーン 1 にゾーンのパブリック IP アドレスを作成します ("zones" と "sku" を既存の Resource Manager テンプレートに追加します)。

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "zones": [ "1" ],
            "sku":
            {
                "name": "Standard"
            },
```

次のコードを使用して、内部ロード バランサー フロントエンドを可用性ゾーン 1 に作成します ("sku" を既存の Resource Manager テンプレートに追加し、"zones" をフロントエンド IP 構成の子リソースに配置します)。

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zonal_frontend_in_az1",
                        "zones": [ "1" ],
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

さらに、VNet 内の VM インスタンスをバックエンド プールに配置することで、バックエンドでクロスゾーン負荷分散をご利用いただくこともできます。

可用性ゾーンがサポートされている場合、Load Balancer Standard リソース自体は常にリージョン ベースであり、ゾーン冗長です。 ゾーンが割り当てられていないパブリック IP アドレスまたは内部ロード バランサー フロントエンドは、可用性ゾーンのサポートに関係なく、どのリージョンにでもデプロイできます。 後日、リージョンで可用性ゾーンが使用できるようになった場合、既にデプロイされているパブリック IP または内部ロード バランサー フロントエンドは自動的にゾーン冗長になります。 ゾーン冗長データ パスは、パケット損失が 0% であることを意味するわけではありません。

### <a name = "nsg"></a>ネットワーク セキュリティ グループ

VNet と ネットワーク セキュリティ グループ (NSG) への Load Balancer Standard とパブリック IP Standard の完全なオンボードが必須になりました。 NSG により、フローのホワイトリスト登録が可能になり、他の構成が完了したときではなく、NSG によってデプロイへのトラフィックが許可されたときにお客様が完全に制御できる構成が可能になります。

NSG をサブネットまたはバックエンド プールの VM インスタンスの NIC に関連付けます。  これは、Load Balancer Standard と、インスタンス レベルのパブリック IP として使用されているパブリック IP Standard に適用されます。 トラフィックが流れるようにするには、NSG で許可するトラフィックを明示的にホワイトリストに登録する必要があります。

ネットワーク セキュリティ グループと、ネットワーク セキュリティ グループをシナリオに適用する方法の詳細については、[ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md)に関する記事をご覧ください。

### <a name ="outboundconnections"></a>送信接続

 ポート マスカレード送信元ネットワーク アドレス変換 (SNAT) を使用して、VNet 内の VM が負荷分散に関連付けられている場合、Load Balancer Standard はそれらの VM に送信接続を提供します。

パブリック ロード バランサー リソースが VM インスタンスに関連付けられている場合、各送信接続の送信元は、VNet のプライベート IP アドレス空間からロード バランサー フロントエンドのパブリック IP アドレスに書き換えられます。  Load Balancer Standard では、堅牢性とスケールを強化するために、新しいポート マスカレード送信元ネットワーク アドレス変換 (SNAT) アルゴリズムを使用します。  

さらに、ゾーン冗長フロントエンドと共に使用すると、送信接続もゾーン冗長になり、ゾーンの障害が発生しても、SNAT ポートの割り当ては保持されます。

Load Balancer Standard の新しいアルゴリズムでは、SNAT ポートがプールに追加された時点で、次のレベルに従って、各 VM のネットワーク インターフェイスに SNAT ポートを事前に割り当てます。

| バックエンド プールのサイズ | 事前に割り当てられる SNAT ポート |
| --- | --- |
| 1-50 | 1024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 |

SNAT ポートは接続数に直接変換されるわけではありません。 複数の一意の送信先に SNAT ポートを再利用できます。  詳細については、[送信接続](load-balancer-outbound-connections.md)に関する記事をご覧ください。

バックエンド プールが拡大し、現在のサイズ レベルから 1 つ大きいサイズに移行すると、割り当て済みポートの半分が解放されます。 解放されたポートに関連付けられている接続はタイムアウトになるので、再確立する必要があります。 新しい接続試行はすぐに成功します。 バックエンド プールが現在のサイズ レベルから 1 つ小さいサイズに縮小されると、使用できる SNAT ポートが増えますが、既存の接続には影響しません。

Load Balancer Standard には、複数のフロントエンドが使用可能な場合に、ポート マスカレード SNAT に使用するフロントエンドをお客様が制御できる、規則ごとの追加構成オプションもあります。

最後に、Load Balancer Standard だけが VM インスタンスを提供している場合、SNAT 送信接続は使用できません。 VM インスタンスをパブリック ロード バランサーに割り当てるか、パブリック IP をインスタンス レベルのパブリック IP として各 VM インスタンスに直接割り当てることで、この機能を明示的に復元できます。 これは、オペレーティング システムとアプリケーションの一部のシナリオで必要になる場合があります。 

### <a name="port-forwarding"></a>ポート フォワーディング

 Basic と Standard の Load Balancer では、フロントエンド ポートを個々のバックエンド インスタンスにマップする受信 NAT 規則を構成できます。  リモート デスクトップ プロトコル エンドポイントの公開、SSH エンドポイント、他のさまざまなアプリケーション シナリオなど、この機能には多くの用途があります。

Load Balancer Standard では、受信 NAT 規則を通じてポート フォワーディング機能を引き続き提供します。  ゾーン冗長フロントエンドと共に使用すると、受信 NAT 規則はゾーン冗長になり、ゾーンの障害が発生しても保持されます。

### <a name="multiple-frontends"></a>複数のフロントエンド

アプリケーションが複数の個別の IP アドレスを公開する必要がある場合は (TLS Web サイトや SQL AlwaysOn 可用性グループのエンドポイントなど) 、設計の柔軟性を確保するために複数のフロントエンドを構成します。  詳細については、[こちら](load-balancer-multivip-overview.md)をご覧ください。

Load Balancer Standard では、一意の IP アドレスで特定のアプリケーション エンドポイントを公開することが望ましい場合に、複数のフロントエンドを引き続き提供します。

 詳細については、[こちら](load-balancer-multivip-overview.md)をご覧ください。

## <a name = "sku"></a>SKU について

SKU は、Azure Resource Manager デプロイメント モデルでのみ利用できます。  このプレビューでは、Load Balancer リソースとパブリック IP リソースの 2 つの SKU (Basic と Standard) が導入されています。  これらの SKU は、機能、パフォーマンス特性、制限事項、一部の組み込み動作が異なります。 仮想マシンはどちらの SKU でもご利用いただけます。 Load Balancer リソースとパブリック IP リソースのどちらについても、SKU は引き続き省略可能な属性であり、省略すると既定の Basic になります。

>[!IMPORTANT]
>リソースの SKU は変更不可です。  既存のリソースの SKU を変更することはできません。  

### <a name="load-balancer"></a>Load Balancer

[既存の Load Balancer リソース](load-balancer-overview.md)は Basic SKU になりますが、一般公開されている状態に変わりはありません。

Load Balancer Standard SKU は新しい SKU であり、現在プレビュー段階です。 Microsoft.Network/loadBalancers の 2017-08-01 API バージョンで、リソースに SKU が導入されています。

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```
Load Balancer Standard は、可用性ゾーンも提供するリージョンで使用すると、ゾーン ベースとして宣言されている場合を除き、自動的にゾーン回復性を持ちます。

### <a name="public-ip"></a>パブリック IP

[既存のパブリック IP リソース](../virtual-network/virtual-network-ip-addresses-overview-arm.md)は Basic SKU になり、Basic の機能、パフォーマンス特性、制限事項が適用されて引き続き一般公開されます。

パブリック IP Standard SKU は新しい SKU であり、現在プレビュー段階です。 Microsoft.Network/publicIPAddresses の 2017-08-01 API バージョンで、リソースに SKU が導入されています。

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

複数の割り当て方法を提供するパブリック IP Basic とは異なり、パブリック IP Standard は常に静的割り当てです。

パブリック IP Standard は、可用性ゾーンも提供するリージョンで使用すると、ゾーン ベースとして宣言されている場合を除き、自動的にゾーン回復性を持ちます。

## <a name="migration-between-skus"></a>SKU 間での移行

一方の SKU からもう一方の SKU に移行する場合は、以下の手順に従います。

### <a name="migrating-from-basic-to-standard-sku"></a>Basic SKU から Standard SKU への移行

1. 新しい Standard リソース (必要に応じて、Load Balancer とパブリック IP) を作成し、規則とプローブ定義を再作成します。
2. すべての VM インスタンス (可用性セットのすべてのインタンスを含む) から Basic SKU リソース (パブリック IP と LB) を削除します。
3. すべての VM インスタンスを新しい Standard SKU リソースに接続します。

### <a name="migrating-from-standard-to-basic-sku"></a>Standard SKU から Basic SKU への移行

1. 新しい Basic リソース (必要に応じて、Load Balancer とパブリック IP) を作成し、規則とプローブ定義を再作成します。 
2. すべての VM インスタンス (可用性セットのすべてのインタンスを含む) から Standard SKU リソース (パブリック IP と LB) を削除します。
3. すべての VM インスタンスを新しい Basic SKU リソースに接続します。

  >[!NOTE]
  >Standard SKU の HA ポート診断機能は、Standard SKU でのみ提供されます。 Standard から Basic に移行し、この機能を保持することはできません。

Load Balancer リソースとパブリック IP リソースには一致する SKU を使用する必要があります。  Basic SKU リソースと Standard SKU リソースを組み合わせたり、VM、可用性セットの VM、または仮想マシン スケール セットを両方の SKU に同時に接続したりすることはできません。

## <a name="region-availability"></a>利用可能なリージョン

現在、Load Balancer Standard は次のリージョンで利用できます。
- 米国東部 2
- 米国中央部
- 北ヨーロッパ
- 米国中西部
- 西ヨーロッパ
- 東南アジア

## <a name="service-limits--abilities-comparison"></a>サービスの制限と機能の比較

[Azure サービスのネットワークの制限](https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits#networking-limits)は、各サブスクリプションのリージョンごとに適用されます。 

次の表では、Load Balancer の Basic SKU と Standard SKU の制限と機能を比較しています。

| Load Balancer | 基本 | 標準 |
| --- | --- | --- |
| バックエンド プールのサイズ | 最大 100 | 最大 1000 |
| バックエンド プールの境界 | 可用性セット | VNet、リージョン |
| バックエンド プールの設計 | 可用性セットの VM または可用性セットの仮想マシン スケール セット | VNet 内の任意の VM インスタンス |
| HA ポート | サポートされていません | 使用可能 |
| 診断 | 制限付き、パブリックのみ | 使用可能 |
| VIP 可用性  | サポートされていません | 使用可能 |
| 高速 IP モビリティ | サポートされていません | 使用可能 |
|可用性ゾーンのシナリオ | ゾーン ベースのみ | ゾーン ベース、ゾーン冗長、クロスゾーン負荷分散 |
| 送信 SNAT アルゴリズム | オンデマンド | 事前割り当て |
| 送信 SNAT フロントエンドの選択 | 構成不可、複数の候補 | 候補を減らすオプション構成 |
| ネットワーク セキュリティ グループ | NIC/サブネットでは省略可能 | 必須 |

次の表では、パブリック IP の Basic SKU と Standard SKU の制限と機能を比較しています。

| パブリック IP | 基本 | 標準 |
| --- | --- | --- |
| 可用性ゾーンのシナリオ | ゾーン ベースのみ | ゾーン冗長 (既定)、ゾーン ベース (オプション) | 
| 高速 IP モビリティ | サポートされていません | 使用可能 |
| VIP 可用性 | サポートされていません | 使用可能 |
| Counters | サポートされていません | 使用可能 |
| ネットワーク セキュリティ グループ | NIC では省略可能 | 必須 |


## <a name="preview-sign-up"></a>プレビューのサインアップ

Load Balancer Standard SKU とそのコンパニオンのパブリック IP Standard SKU のプレビューに参加するには、PowerShell または Azure CLI 2.0 を使用してサブスクリプションを登録し、アクセス権を取得します。

- PowerShell を使用したサインアップ

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

- Azure CLI 2.0 を使用したサインアップ

    ```cli
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    ```

>[!NOTE]
>Load Balancer Standard の機能の登録には、1 時間ほどかかる場合があります。

>[!NOTE]
>Load Balancer とパブリック IP で可用性ゾーンを使用する場合は、可用性ゾーン プレビューのサブスクリプションも登録する必要があります。

## <a name="pricing"></a>価格

Load Balancer Standard SKU は、構成済みの規則と処理されたデータに基づいて課金されます。  プレビュー期間中は、料金は発生しません。  詳細については、[Load Balancer](https://aka.ms/lbpreviewpricing) と[パブリック IP](https://aka.ms/lbpreviewpippricing) の価格のページをご覧ください。

Load Balancer Basic SKU は、引き続き無料でご利用いただけます。

## <a name="limitations"></a>制限事項

プレビュー時点で次の制限事項が適用されます。これらの制限事項は変更されることがあります。

- 現時点では、Load Balancer のバックエンド インスタンスをピアリングされた VNet に配置することはできません。 バックエンド インスタンスはすべて同じリージョンに存在する必要があります。
- SKU は変更不可です。 既存のリソースの SKU を変更することはできません。
- スタンドアロン VM、可用性セットのすべての VM インスタンス、または仮想マシン スケール セットで、Basic SKU または Standard SKU のいずれかをご利用いただけます。 スタンドアロン VM、可用性セットのすべての VM インスタンス、または仮想マシン スケール セットを両方の SKU で同時に使用することはできません。 SKU の組み合わせは許可されていません。
- VM インスタンス (または可用性セットの VM インスタンス) で内部 Load Balancer Standard を使用すると、[既定の SNAT 送信接続](load-balancer-outbound-connections.md)が無効になります。  パブリック Load Balancer Standard またはインスタンス レベルのパブリック IP としてのパブリック IP Standard を同じ VM インスタンスに同時に割り当てることで、スタンドアロン VM、可用性セットの VM インスタンス、または仮想マシン スケール セットにこの機能を復元し、送信接続を行うことができます。 完了すると、パブリック IP アドレスに対するポート マスカレード SNAT が再び提供されます。
- フル スケースのバックエンド プールを実現するために、VM インスタンスを可用性セットにグループ化することが必要な場合があります。 最大 150 個の可用性セットとスタンドアロン VM を 1 つのバックエンド プールに配置できます。
- IPv6 はサポートされていません。
- 可用性ゾーンのコンテキストでは、フロントエンドをゾーン ベースからゾーン冗長に変更したり、ゾーン冗長からゾーン ベースに変更したりすることはできません。 ゾーン冗長として作成すると、常にゾーン冗長になります。 ゾーン ベースとして作成すると、常にゾーン ベースになります。
- 可用性ゾーンのコンテキストでは、ゾーンのパブリック IP アドレスをゾーン間で移動することはできません。


## <a name="next-steps"></a>次のステップ

- [Basic Load Balancer](load-balancer-overview.md) の詳細を確認する
- [可用性ゾーン](../availability-zones/az-overview.md)の詳細を確認する
- Azure のその他の重要な[ネットワーク機能](../networking/networking-overview.md)にについて確認する。



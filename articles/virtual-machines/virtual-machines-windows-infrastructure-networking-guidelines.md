---
title: "Azure ネットワーク インフラストラクチャのガイドライン - Windows | Microsoft Docs"
description: "Azure インフラストラクチャ サービスでの 仮想ネットワークのデプロイに関する主要な設計と実装のガイドラインについて説明します。"
documentationcenter: 
services: virtual-machines-windows
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e2d45973-5eba-4904-8ba0-1821f64feed7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 9e0a007a910b6ba887800184fb66bf9198b13adc
ms.lasthandoff: 03/03/2017


---
# <a name="azure-networking-infrastructure-guidelines-for-windows-vms"></a>Windows VM 用の Azure ネットワーク インフラストラクチャのガイドライン 

[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

この記事は、Azure 内の仮想ネットワークと、既存のオンプレミス環境間の接続に必要な計画について説明します。

## <a name="implementation-guidelines-for-virtual-networks"></a>仮想ネットワークに関する実装ガイドライン
決めること:

* IT ワークロードまたはインフラストラクチャをホストするために必要な仮想ネットワークの種類は (クラウドのみまたはクロスプレミス)。
* クロスプレミス仮想ネットワークの場合、現在のサブネットと VM をホストし、将来の妥当な拡張のために必要なアドレス空間の大きさ。
* 一元的な仮想ネットワークを作成するのか、あるいは各リソース グループに対し個別の仮想ネットワークを作成するのか。

タスク:

* 作成する仮想ネットワークのアドレス空間を定義します。
* サブネットのセットおよびそれぞれに対するアドレス空間を定義します。
* クロスプレミス仮想ネットワークの場合、仮想ネットワーク内の VM が到達する必要のあるオンプレミスの場所に対するローカル ネットワーク アドレス空間のセットを定義します。
* クロスプレミス接続用の仮想ネットワークを作成する際は、オンプレミスのネットワーク チームと協力して最適なルーティングの構成を確保します。
* 名前付け規則を使用して仮想ネットワークを作成します。

## <a name="virtual-networks"></a>仮想ネットワーク
仮想ネットワークは、仮想マシン (VM) 間の通信をサポートするために必要です。 物理ネットワークと同じように、サブネット、カスタム IP アドレス、DNS 設定、セキュリティ フィルター処理、負荷分散を定義できます。 [VPN ゲートウェイ](../vpn-gateway/vpn-gateway-about-vpngateways.md)または [Express Route 回線](../expressroute/expressroute-introduction.md)を使用して、Azure 仮想ネットワークをオンプレミスのネットワークに接続できます。 詳細については、 [仮想ネットワークとそのコンポーネント](../virtual-network/virtual-networks-overview.md)に関するページを参照してください。

リソース グループを使用することにより、仮想ネットワークのコンポーネント設計を柔軟に行うことができます。 VM は自身が属するリソース グループの外の仮想ネットワークに接続できます。 一般的な設計の手法は、共通のチームで管理可能な、コア ネットワーク インフラストラクチャを含む一元的なリソース グループを作成し、それから別々のリソース グループにデプロイする VM とそのアプリケーションを作成することです。 これによりアプリケーション所有者は、幅広い仮想ネットワーク リソースの構成へのアクセスを開かなくても、VM を含むリソース グループにアクセスできます。

## <a name="site-connectivity"></a>サイトの接続
### <a name="cloud-only-virtual-networks"></a>クラウド専用仮想ネットワーク
オンプレミスのユーザーとコンピューターが、Azure Virtual Network 内の仮想マシンへの常時接続を必要としない場合、仮想ネットワーク設計はとても簡単です。

![基本的なクラウド専用仮想ネットワークの図](./media/virtual-machines-common-infrastructure-service-guidelines/vnet01.png)

これは通常、インターネット ベースの Web サーバーなど、インターネットに接続されたワークロード用です。 RDP またはポイント対サイト VPN 接続を使って VM を管理できます。

オンプレミス ネットワークに接続しないので、Azure 専用仮想ネットワークではプライベート IP アドレス空間の任意の部分を、同じプライベート空間がオンプレミスで使われていた場合でも、使用できます。

### <a name="cross-premises-virtual-networks"></a>クロスプレミス接続用の仮想ネットワーク
オンプレミスのユーザーとコンピューターが、Azure Virtual Network 内の VM への常時接続を必要とする場合は、クロスプレミス接続用の仮想ネットワークを作成します。  これを、ExpressRoute またはサイト間 VPN 接続を使用してオンプレミス ネットワークに接続します。

![クロスプレミス仮想ネットワークの図](./media/virtual-machines-common-infrastructure-service-guidelines/vnet02.png)

この構成では、Azure Virtual Network は基本的に、オンプレミス ネットワークのクラウド ベースの拡張機能です。

オンプレミス ネットワークに接続するため、クロスプレミス仮想ネットワークは、組織によって使用されるアドレス空間の一意な一部分を使用する必要があります。 企業内の異なる場所に特定の IP サブネットが割り当てられるのと同様に、ネットワークを拡張すると、Azure を別の場所として扱うことができます。

クロスプレミス仮想ネットワークからオンプレミス ネットワークにパケットが移動できるようにするには、仮想ネットワークのローカル ネットワークの定義の一部として、一連の関連するオンプレミス アドレスのプレフィックスを構成する必要があります。 仮想ネットワークのアドレス空間および関連するオンプレミスの場所によっては、ローカル ネットワークに多数のアドレス プレフィックスがあってもかまいません。

クラウド専用仮想ネットワークをクロスプレミス仮想ネットワークに変換できますが、通常は、仮想ネットワーク アドレス空間と Azure リソースの IP アドレスを再設定する必要があります。 そのため、IP サブネットを割り当てるときは、仮想ネットワークがオンプレミス ネットワークに接続する必要があるかどうかを慎重に検討します。

## <a name="subnets"></a>サブネット
サブネットを使用すると、関連するリソースを論理的に (たとえば、同じアプリケーションに関連する VM 用に&1; つのサブネット)、または物理的に (たとえば、リソース グループごとに&1; つのサブネット) まとめることができます。 また、サブネット分離手法を使用してセキュリティを強化することもできます。

クロスプレミス仮想ネットワークでは、オンプレミス リソースで使用する場合と同じ規則を使用してサブネットを設計する必要があります。 **Azure は常に、各サブネットのアドレス空間の最初の&3; つの IP アドレスを使用します**。 サブネットに必要なアドレスの数を判断するには、まずは今必要な VM の数をカウントすることから始めます。 将来の成長を予測し、次の表を使用してサブネットのサイズを決定します。

| 必要な VM の数 | 必要なホスト ビットの数 | サブネットのサイズ |
| --- | --- | --- |
| 1 ～&3; |3 |/29 |
| 4 ～&11; |4 |/28 |
| 12 ～&27; |5 |/27 |
| 28 ～&59; |6 |/26 |
| 60 ～&123; |7 |/25 |

> [!NOTE]
> 通常のオンプレミス サブネットの場合、ホスト ビット数 n のサブネットに対するホスト アドレスの最大数は 2<sup>n</sup> – 2 です。 Azure サブネットの場合、ホスト ビット数 n のサブネットに対するホスト アドレスの最大数は 2<sup>n</sup> – 5 (2 に加えて、Azure が各サブネットに使用するアドレス数の 3)。
> 
> 

選択したサブネットのサイズが小さすぎる場合、サブネット内の VM の IP アドレスを再設定して再デプロイする必要があります。

## <a name="network-security-groups"></a>ネットワーク セキュリティ グループ
ネットワーク セキュリティ グループを使用して、仮想ネットワークを流れるトラフィックにフィルタ リング規則を適用することができます。 詳細なフィルタリング規則を構築して仮想ネットワーク環境を保護し、受信トラフィックと送信トラフィック、ソースと宛先の IP 範囲、許可されたポートなどを制御できます。ネットワーク セキュリティ グループは、仮想ネットワーク内のサブネット、または直接指定された仮想ネットワーク インターフェイスに適用できます。 ネットワーク セキュリティ グループによるフィルター処理を、仮想ネットワーク上トラフィックに対してある程度設定することをお勧めします。 詳細については、 [ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md)に関するページを参照してください。

## <a name="additional-network-components"></a>その他のネットワーク コンポーネント
オンプレミスの物理ネットワーク インフラストラクチャと同様に、Azure の仮想ネットワークはサブネットと IP アドレス指定以外のものも使用可能です。 アプリケーション インフラストラクチャを設計する際に、これらの追加コンポーネントの一部を組み込むことができます。

* [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) - Azure の仮想ネットワークを他の Azure 仮想ネットワークに接続するか、サイト間 VPN 接続を経由してオンプレミス ネットワークに接続します。 セキュリティで保護された専用の接続を使用する場合は、Express Route 接続を実装します。 また、ポイント対サイト VPN 接続を使用して、ユーザーに直接アクセスを提供することもできます。
* [ロード バランサー](../load-balancer/load-balancer-overview.md) - 必要に応じて、外部および内部のトラフィックの負荷分散を提供します。
* [Application Gateway](../application-gateway/application-gateway-introduction.md) - アプリケーション層の HTTP 負荷分散で、Azure のロード バランサーをデプロイするよりも Web アプリケーションにとって利点があります。
* [Traffic Manager](../traffic-manager/traffic-manager-overview.md) - DNS ベースのトラフィック分散で、エンドユーザーを使用可能な最も近いアプリケーション エンドポイントにルーティングすることで、異なるリージョンの Azure データセンターからアプリケーションをホストできます。

## <a name="next-steps"></a>次のステップ
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]



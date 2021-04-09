---
title: Azure Load Balancer の概要
titleSuffix: Azure Load Balancer
description: Azure Load Balancer の機能の概要、アーキテクチャ、実装。 Load Balancer の動作とクラウドでの使用方法について説明します。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/25/2021
ms.author: allensu
ms.openlocfilehash: 51ceb72d53f78264edcadd2255e20c8fbdac2cae
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181212"
---
# <a name="what-is-azure-load-balancer"></a>Azure Load Balancer の概要

"*負荷分散*" とは、負荷 (受信ネットワーク トラフィック) をバックエンド リソースまたはサーバーのグループ全体に均等に分散することです。 

Azure Load Balancer は、開放型システム間相互接続 (OSI) モデルの第 4 レイヤーで動作します。 クライアントにとっての単一接続点となります。 Load Balancer は、ロード バランサーのフロントエンドに到着したインバウンド フローを、バックエンド プールのインスタンスに分配します。 これらのフローは、構成された負荷分散規則と正常性プローブに従っています。 バックエンド プール インスタンスには、Azure Virtual Machines か、仮想マシン スケール セット内のインスタンスを使用できます。

**[パブリック ロード バランサー](./components.md#frontend-ip-configurations)** は、仮想ネットワーク内の仮想マシン (VM) にアウトバウンド接続を提供できます。 これらの接続は、プライベート IP アドレスをパブリック IP アドレスに変換することで実現されます。 パブリック ロード バランサーは、インターネット トラフィックを VM に負荷分散する目的で使用されます。

**[内部 (プライベート) ロード バランサー](./components.md#frontend-ip-configurations)** は、フロントエンドのみでプライベート IP が必要な場合に使用されます。 内部ロード バランサーは、仮想ネットワーク内でトラフィックを負荷分散させるために使用されます。 ハイブリッド シナリオでは、オンプレミスのネットワークからロード バランサー フロントエンドにアクセスできます。

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" alt="Figure depicts both public and internal load balancers directing traffic to port 80 on multiple servers on a Web tier and port 443 on multiple servers on a business tier." width="512" title="Azure Load Balancer">
</p>

*図:パブリック ロード バランサーと内部ロード バランサーの両方を使った、多層アプリケーションの負荷分散*

個々のロード バランサー コンポーネントの詳細については、「[Azure Load Balancer のコンポーネント](./components.md)」を参照してください。

>[!NOTE]
> Azure では、ユーザーのシナリオのためにフル マネージドの負荷分散ソリューションのスイートが提供されます。 
> * DNS ベースのグローバルなルーティングを検討中であり、トランスポート層セキュリティ (TLS) プロトコル終端 ("SSL オフロード") の要件や、HTTP/HTTPS 要求ごとまたはアプリケーション層の処理の要件が **ない** 場合は、[Traffic Manager](../traffic-manager/traffic-manager-overview.md) を検討してください。 
> * アプリケーション レイヤーでリージョン内のサーバー間の負荷分散が必要な場合は、[Application Gateway](../application-gateway/overview.md) に関するページを参照してください。
> * Web トラフィックのグローバル ルーティングを最適化し、迅速なグローバル フェールオーバーによってトップレベルのエンドユーザー パフォーマンスと信頼性を最適化する必要がある場合は、[Front Door](../frontdoor/front-door-overview.md) に関するページを参照してください。
> 
> 実際のエンド ツー エンドのシナリオでは、必要に応じてこれらのソリューションを組み合わせると役に立つことがあります。
> Azure の負荷分散オプションの比較については、「[Azure の負荷分散オプションの概要](/azure/architecture/guide/technology-choices/load-balancing-overview)」を参照してください。


## <a name="why-use-azure-load-balancer"></a>Azure Load Balancer を使用する理由
Azure Load Balancer を使用すると、アプリケーションをスケーリングしたり、高可用性サービスを作成したりすることができます。 ロード バランサーは、インバウンドとアウトバウンドの両方のシナリオをサポートしています。 ロード バランサーは、低遅延と高スループットを実現できるだけでなく、あらゆる TCP アプリケーションと UDP アプリケーションの数百万ものフローにスケールアップできます。

Azure Standard Load Balancer を使用して実現できる主なシナリオは、次のとおりです。

- **[内部](./quickstart-load-balancer-standard-internal-portal.md)** および **[外部](./quickstart-load-balancer-standard-public-portal.md)** トラフィックを Azure 仮想マシンに負荷分散する。

- ゾーンの **[内部で](./tutorial-load-balancer-standard-public-zonal-portal.md)** 、および複数のゾーンに **[わたって](./tutorial-load-balancer-standard-public-zone-redundant-portal.md)** リソースを分散することによって、可用性を向上させる。

- Azure 仮想マシンの **[アウトバウンド接続](./load-balancer-outbound-connections.md)** を構成する。

- **[正常性プローブ](./load-balancer-custom-probe-overview.md)** を使用して、負荷分散されたリソースを監視する。

- **[ポート転送](./tutorial-load-balancer-port-forwarding-portal.md)** を使用して、仮想ネットワーク内の仮想マシンにパブリック IP アドレスとポートでアクセスする。

- **[IPv6](../virtual-network/ipv6-overview.md)** の **[負荷分散](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)** のサポートを有効にする。

- Standard Load Balancer は [Azure Monitor](../azure-monitor/overview.md) を通じて多次元メトリックを提供します。  これらのメトリックは、フィルター処理し、グループ化し、特定のディメンションに分割することができます。  サービスのパフォーマンスと正常性に関する現在と過去の分析情報を提供します。 [[Azure Load Balancer の分析情報]](./load-balancer-insights.md) には、これらのメトリックがわかりやすく可視化された事前構成済みのダッシュボードが用意されています。  リソースの正常性もサポートされます。 詳細については、 **[Standard Load Balancer の診断](load-balancer-standard-diagnostics.md)** に関するページを参照してください。

- **[複数のポート、複数の IP アドレス、またはその両方](./load-balancer-multivip-overview.md)** にサービスを負荷分散する。

- 複数の Azure リージョンにわたる **[内部](./move-across-regions-internal-load-balancer-portal.md)** および **[外部](./move-across-regions-external-load-balancer-portal.md)** のロード バランサー リソースに移動する。

- **[HA ポート](./load-balancer-ha-ports-overview.md)** を使用して、TCP と UDP のフローをすべてのポートに同時に負荷分散する。

### <a name="secure-by-default"></a><a name="securebydefault"></a>既定でのセキュリティ保護

* Standard Load Balancer は、ゼロ トラスト ネットワーク セキュリティ モデルに基づいて構築されています。

* Standard Load Balancer は既定ではセキュリティで保護され、仮想ネットワークの一部です。 仮想ネットワークは、プライベートの分離されたネットワークです。  

* Standard Load Balancer と標準のパブリック IP アドレスは、ネットワーク セキュリティ グループによって開かれない限り、インバウンド接続に対して閉じられています。 NSG は、トラフィックを明示的に許可するために使用されます。  お使いの仮想マシン リソースのサブネットまたは NIC に NSG がない場合、トラフィックはこのリソースに到達することを許可されません。 NSG と、それをシナリオに適用する方法については、「[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)」をご覧ください。

* Basic Load Balancer は、既定ではインターネットに対して公開されています。 

* Load Balancer には顧客データは格納されません。

## <a name="pricing-and-sla"></a>料金と SLA

Standard Load Balancer の価格の情報については、[Load Balancer の価格](https://azure.microsoft.com/pricing/details/load-balancer/)に関するページを参照してください。
Basic Load Balancer は無料で提供されます。
[Load Balancer の SLA](https://aka.ms/lbsla) に関するページを参照してください。 Basic Load Balancer には SLA がありません。

## <a name="whats-new"></a>新機能

RSS フィードを購読し、[Azure の更新情報](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer)ページで、最新の Azure Load Balancer 機能の更新を確認します。

## <a name="next-steps"></a>次のステップ

ロード バランサーの使用を開始するには、[パブリックな標準ロード バランサーの作成](quickstart-load-balancer-standard-public-portal.md)に関する記事をご覧ください。

Azure Load Balancer の制限とコンポーネントの詳細については、「[Azure Load Balancer のコンポーネント](./components.md)」と [Azure Load Balancer の概念](./concepts.md)に関するページを参照してください
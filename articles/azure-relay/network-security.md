---
title: Azure Relay のネットワーク セキュリティ
description: この記事では、Azure Relay で IP ファイアウォール規則とプライベート エンドポイントを使用する方法について説明します。
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 735a0b7dc303ad63cb301151e3e14e4488c856b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98217245"
---
# <a name="network-security-for-azure-relay"></a>Azure Relay のネットワーク セキュリティ 
この記事では、Azure Relay で次のセキュリティ機能を使用する方法について説明します。 

- IP ファイアウォール規則
- プライベート エンドポイント 

> [!NOTE]
> Azure Relay では、ネットワーク サービス エンドポイントはサポートされていません。 


## <a name="ip-firewall"></a>IP ファイアウォール 
既定では、要求に有効な認証と承認がある限り、Relay 名前空間にはインターネットからアクセスできます。 これは IP ファイアウォールを使用して、さらに [CIDR (クラスレス ドメイン間ルーティング)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 表記の一連の IPv4 アドレスまたは IPv4 アドレス範囲のみに制限できます。

この機能は、Azure Relay へのアクセスを特定の既知のサイトからのみに制限するシナリオに役立ちます。 ファイアウォール規則を使用すると、特定の IPv4 アドレスから送信されたトラフィックを受け入れる規則を構成できます。 たとえば、[Azure ExpressRoute](../expressroute/expressroute-faqs.md#supported-services) で Relay を使用する場合、オンプレミス インフラストラクチャ IP アドレスからのトラフィックのみ許可する **ファイアウォール規則** を作成できます。 

IP ファイアウォール規則は、Relay 名前空間レベルで適用されます。 したがって、規則は、サポートされているプロトコルを使用するクライアントからのすべての接続に適用されます。 Relay 名前空間上の許可 IP 規則に一致しない IP アドレスからの接続試行は、未承認として拒否されます。 IP 規則に関する記述は応答に含まれません。 IP フィルター規則は順に適用され、IP アドレスと一致する最初の規則に基づいて許可アクションまたは拒否アクションが決定されます。

詳細については、[Relay の名前空間の IP ファイアウォールを構成する方法](ip-firewall-virtual-networks.md)に関する記事を参照してください

## <a name="private-endpoints"></a>プライベート エンドポイント

Azure **Private Link サービス** を使用すると、自分の仮想ネットワーク内のプライベート エンドポイント経由で、Azure サービス (Azure Relay、Azure Service Bus、Azure Event Hubs、Azure Storage、Azure Cosmos DB など) や、Azure でホストされている顧客またはパートナーのサービスにアクセスできます。 詳細については、「[Azure Private Link とは](../private-link/private-link-overview.md)」を参照してください。

**プライベート エンドポイント** は、仮想ネットワークで実行されているワークロードが、**プライベート リンク リソース** (Relay 名前空間など) を持つサービスにプライベートで安全に接続できるようにするネットワーク インターフェイスです。 プライベート エンドポイントは、ご自分の VNet からのプライベート IP アドレスを使用して、サービスを実質的に VNet に取り込みます。 サービスへのすべてのトラフィックをプライベート エンドポイントを介してルーティングできるため、ゲートウェイ、NAT デバイス、ExpressRoute、VPN 接続、パブリック IP アドレスは不要です。 仮想ネットワークとサービスの間のトラフィックは、Microsoft のバックボーン ネットワークを経由するので、パブリック インターネットから公開されることがなくなります。 特定の Azure Relay 名前空間への接続を許可することにより、アクセスをきめ細かく制御できます。

詳細については、[プライベート エンドポイントを構成する方法](private-link-service.md)に関するページを参照してください


## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- [IP ファイアウォールを構成する方法](ip-firewall-virtual-networks.md)
- [プライベート エンドポイントを構成する方法](private-link-service.md)

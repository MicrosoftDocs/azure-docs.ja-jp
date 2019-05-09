---
title: Azure Virtual Network サービス エンドポイントを使用して Azure Cosmos DB アカウントへのアクセスをセキュリティで保護する
description: このドキュメントでは、Azure Cosmos アカウントの仮想ネットワークとサブネット アクセス制御について説明します。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 672c62c440708f8e949d67d545bee2179c6066b2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58894937"
---
# <a name="access-azure-cosmos-db-from-virtual-networks-vnet"></a>仮想ネットワーク (VNet) から Azure Cosmos DB にアクセスする

Azure Cosmos アカウントは、仮想ネットワーク (VNet) の特定のサブネットからのアクセスのみを許可するように構成することができます。 [サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)が仮想ネットワーク内のサブネット上の Azure Cosmos DB にアクセスできるようにすることで、そのサブネットからのトラフィックは、サブネットと仮想ネットワークの ID を使用して Azure Cosmos DB に送信されます。 Azure Cosmos DB サービス エンドポイントが有効になると、Azure Cosmos アカウントにサブネットを追加することで、サブネットへのアクセスを制限できます。

既定で、有効な承認トークンと共に要求が送信される場合、任意のソースから Azure Cosmos アカウントにアクセスできます。 VNet 内に 1 つまたは複数のサブネットを追加した場合、それらのサブネットから送信された要求のみが有効な応答を受け取ります。 その他のソースから送信された要求は、403 (禁止) 応答を受け取ります。 

## <a name="frequently-asked-questions"></a>よく寄せられる質問

仮想ネットワークからのアクセスを構成する場合についてよく寄せられる質問を次に示します。

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Azure Cosmos アカウントに対して仮想ネットワーク サービス エンドポイントと IP アクセス制御ポリシーの両方を指定できますか。 

Azure Cosmos アカウントに対して、仮想ネットワーク サービス エンドポイントと IP アクセス制御ポリシー (ファイアウォールと呼ばれます) の両方を有効にすることができます。 この 2 つの機能は補完的であり、組み合わせることで Azure Cosmos アカウントの分離とセキュリティを達成できます。 IP ファイアウォールを使用すると、確実に静的 IP からアカウントにアクセスできます。 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>仮想ネットワーク内のサブネットへのアクセスを制限するにはどうすればよいですか。 

サブネットから Azure Cosmos アカウントへのアクセスを制限するには、2 つの手順が必要です。 まず、サブネットからのトラフィックで、そのサブネットおよび仮想ネットワーク ID を Azure Cosmos DB に伝達できるようにします。 このために、サブネット上で Azure Cosmos DB のサービス エンドポイントを有効にします。 次に、Azure Cosmos アカウントにアクセスできるソースとしてサブネットを指定する規則を Azure Cosmos アカウントに追加します。

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>仮想ネットワーク ACL と IP ファイアウォールは要求または接続を拒否しますか。 

IP ファイアウォールまたは仮想ネットワークのアクセス規則が追加されると、許可されたソースからの要求のみが有効な応答を受け取ります。 他の要求は 403 (禁止) で拒否されます。 Azure Cosmos アカウントのファイアウォールを接続レベルのファイアウォールと区別することが重要です。 ソースからは引き続きサービスに接続できます。また、接続自体は拒否されません。

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>サブネット上の Azure Cosmos DB に対してサービス エンドポイントを有効にすると、要求がブロックされ始めました。 なぜでしょうか?

Azure Cosmos DB 用のサービス エンドポイントがサブネット上で有効になると、アカウントに到達するトラフィックのソースが、パブリック IP から、仮想ネットワークとサブネットに切り替わります。 IP ベースのファイアウォールのみが Azure Cosmos アカウントに設定されている場合、サービスで有効なサブネットからのトラフィックは IP ファイアウォール規則と一致しなくなるため、拒否されるようになります。 IP ベースのファイアウォールから仮想ネットワーク ベースのアクセス制御にシームレスに移行するための手順を実行してください。

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>ピアリングされた仮想ネットワークも Azure Cosmos アカウントにアクセスできますか。 
Azure Cosmos アカウントに追加された仮想ネットワークとそのサブネットのみがアクセスできます。 ピアリングされた仮想ネットワーク内のサブネットをアカウントに追加するまで、ピアリングされた VNet はアカウントにアクセスできません。

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>1 つの Cosmos アカウントにアクセスできるサブネットの最大数はいくつですか。 
現在、Azure Cosmos アカウントには最大 64 のサブネットを使用できます。

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>VPN と Express Route からのアクセスを有効にすることはできますか。 
オンプレミスから Express Route 経由で Azure Cosmos アカウントにアクセスするには、Microsoft ピアリングを有効にする必要があります。 IP ファイアウォールまたは仮想ネットワーク アクセス規則を設定すると、Azure Cosmos アカウントの IP ファイアウォールに Microsoft ピアリングに使用するパブリック IP アドレスを追加して、オンプレミス サービスが Azure Cosmos アカウントにアクセスできるようにすることができます。 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>ネットワーク セキュリティ グループ (NSG) の規則を更新する必要はありますか。 
NSG 規則は、仮想ネットワークを使用したサブネットとの接続を制限するために使用されます。 Azure Cosmos DB のサービス エンドポイントをサブネットに追加する場合、Azure Cosmos アカウントのために NSG で送信接続を開く必要はありません。 

### <a name="are-service-endpoints-available-for-all-vnets"></a>サービス エンドポイントはすべての VNet に使用できますか。
いいえ。サービス エンドポイントを有効にすることができるのは Azure Resource Manager 仮想ネットワークのみです。 従来の仮想ネットワークはサービス エンドポイントをサポートしていません。

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Azure Cosmos DB のサービス エンドポイント アクセスが有効になっているときに "パブリック Azure データセンター内からの接続を受け入れる" ことはできますか?  
これは、Azure Data Factory、Azure Search、または特定の Azure リージョンにデプロイされるサービスのような他の Azure ファースト パーティ サービスによって Azure Cosmos DB アカウントがアクセスされる場合にのみ必要です。


## <a name="next-steps"></a>次の手順

* [仮想ネットワーク内のサブネットへの Azure Cosmos アカウントのアクセスを制限する方法](how-to-configure-vnet-service-endpoint.md)
* [Azure Cosmos アカウントに IP ファイアウォールを構成する方法](how-to-configure-firewall.md)


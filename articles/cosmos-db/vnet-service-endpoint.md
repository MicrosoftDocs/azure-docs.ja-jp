---
title: 仮想ネットワーク サービス エンドポイントを使用して Azure Cosmos DB アカウントへのアクセスをセキュリティ保護する
description: このドキュメントでは、Azure Cosmos アカウントの仮想ネットワークとサブネット アクセス制御について説明します。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: d264ead87e7fa638830bf25fdb07983b164334b7
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83698667"
---
# <a name="access-azure-cosmos-db-from-virtual-networks-vnet"></a>仮想ネットワーク (VNet) から Azure Cosmos DB にアクセスする

Azure Cosmos アカウントは、仮想ネットワーク (VNet) の特定のサブネットからのアクセスのみを許可するように構成することができます。 [サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)が仮想ネットワーク内のサブネット上の Azure Cosmos DB にアクセスできるようにすることで、そのサブネットからのトラフィックは、サブネットと仮想ネットワークの ID を使用して Azure Cosmos DB に送信されます。 Azure Cosmos DB サービス エンドポイントが有効になると、Azure Cosmos アカウントにサブネットを追加することで、サブネットへのアクセスを制限できます。

既定で、有効な承認トークンと共に要求が送信される場合、任意のソースから Azure Cosmos アカウントにアクセスできます。 VNet 内に 1 つまたは複数のサブネットを追加した場合、それらのサブネットから送信された要求のみが有効な応答を受け取ります。 その他のソースから送信された要求は、403 (禁止) 応答を受け取ります。 

## <a name="frequently-asked-questions"></a>よく寄せられる質問

仮想ネットワークからのアクセスを構成する場合についてよく寄せられる質問を次に示します。

### <a name="are-notebooks-and-mongo-shell-currently-compatible-with-virtual-network-enabled-accounts"></a>Notebook と Mongo シェルは、現在、Virtual Network が有効になっているアカウントと互換性がありますか。

現在、[Cosmos DB Data Explorer での Mongo シェル統合](https://devblogs.microsoft.com/cosmosdb/preview-native-mongo-shell/)と [Jupyter Notebook サービス](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-jupyter-notebooks)は、VNET アクセスではサポートされていません。 これは現在開発が進められています。

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Azure Cosmos アカウントに対して仮想ネットワーク サービス エンドポイントと IP アクセス制御ポリシーの両方を指定できますか。 

Azure Cosmos アカウントに対して、仮想ネットワーク サービス エンドポイントと IP アクセス制御ポリシー (ファイアウォールと呼ばれます) の両方を有効にすることができます。 この 2 つの機能は補完的であり、組み合わせることで Azure Cosmos アカウントの分離とセキュリティを達成できます。 IP ファイアウォールを使用すると、確実に静的 IP からアカウントにアクセスできます。 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>仮想ネットワーク内のサブネットへのアクセスを制限するにはどうすればよいですか。 

サブネットから Azure Cosmos アカウントへのアクセスを制限するには、2 つの手順が必要です。 まず、サブネットからのトラフィックで、そのサブネットおよび仮想ネットワーク ID を Azure Cosmos DB に伝達できるようにします。 このために、サブネット上で Azure Cosmos DB のサービス エンドポイントを有効にします。 次に、Azure Cosmos アカウントにアクセスできるソースとしてサブネットを指定する規則を Azure Cosmos アカウントに追加します。

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>仮想ネットワーク ACL と IP ファイアウォールは要求または接続を拒否しますか。 

IP ファイアウォールまたは仮想ネットワークのアクセス規則が追加されると、許可されたソースからの要求のみが有効な応答を受け取ります。 他の要求は 403 (禁止) で拒否されます。 Azure Cosmos アカウントのファイアウォールを接続レベルのファイアウォールと区別することが重要です。 ソースは引き続きサービスに接続でき、接続自体は拒否されません。

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>サブネット上の Azure Cosmos DB に対してサービス エンドポイントを有効にすると、要求がブロックされ始めました。 なぜでしょうか?

Azure Cosmos DB 用のサービス エンドポイントがサブネット上で有効になると、アカウントに到達するトラフィックのソースが、パブリック IP から、仮想ネットワークとサブネットに切り替わります。 IP ベースのファイアウォールのみが Azure Cosmos アカウントに設定されている場合、サービスで有効なサブネットからのトラフィックは IP ファイアウォール規則と一致しなくなるため、拒否されるようになります。 IP ベースのファイアウォールから仮想ネットワーク ベースのアクセス制御にシームレスに移行するための手順を実行してください。

### <a name="are-additional-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>Azure Cosmos アカウントと VNET サービス エンドポイントの組み合わせには、追加の RBAC アクセス許可が必要ですか。

VNet サービス エンドポイントを Azure Cosmos アカウントに追加した後、アカウント設定を変更する場合、自分の Azure Cosmos アカウントで構成されているすべての VNET について、`Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` アクションの権限が必要になります。 このアクセス許可が必要になるのは、あらゆるプロパティを評価する前に、リソース (データベースや仮想ネットワーク リソースなど) へのアクセス権限が認証プロセスで検証されるためです。
 
この認証では、ユーザーが Azure CLI を使用して VNET ACL を指定しない場合でも、VNet リソース アクションのアクセス許可が検証されます。 現在のところ、Azure Cosmos アカウントのコントロール プレーンでは、Azure Cosmos アカウントの完全な状態を設定できます。 コントロール プレーン呼び出しのパラメーターの 1 つは `virtualNetworkRules` です。 このパラメーターが指定されていない場合、Azure CLI では、`virtualNetworkRules` を取得する目的でデータベースの取得呼び出しを行い、更新呼び出しでこの値を使用します。

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>ピアリングされた仮想ネットワークも Azure Cosmos アカウントにアクセスできますか。 
Azure Cosmos アカウントに追加された仮想ネットワークとそのサブネットのみがアクセスできます。 ピアリングされた仮想ネットワーク内のサブネットをアカウントに追加するまで、ピアリングされた VNet はアカウントにアクセスできません。

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>1 つの Cosmos アカウントにアクセスできるサブネットの最大数はいくつですか。 
現在、Azure Cosmos アカウントでは最大 256 のサブネットが許可されています。

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>VPN と Express Route からのアクセスを有効にすることはできますか。 
オンプレミスから Express Route 経由で Azure Cosmos アカウントにアクセスするには、Microsoft ピアリングを有効にする必要があります。 IP ファイアウォールまたは仮想ネットワーク アクセス規則を設定すると、Azure Cosmos アカウントの IP ファイアウォールに Microsoft ピアリングに使用するパブリック IP アドレスを追加して、オンプレミス サービスが Azure Cosmos アカウントにアクセスできるようにすることができます。 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>ネットワーク セキュリティ グループ (NSG) の規則を更新する必要はありますか。 
NSG 規則は、仮想ネットワークを使用したサブネットとの接続を制限するために使用されます。 Azure Cosmos DB のサービス エンドポイントをサブネットに追加する場合、Azure Cosmos アカウントのために NSG で送信接続を開く必要はありません。 

### <a name="are-service-endpoints-available-for-all-vnets"></a>サービス エンドポイントはすべての VNet に使用できますか。
いいえ。サービス エンドポイントを有効にすることができるのは Azure Resource Manager 仮想ネットワークのみです。 クラシック仮想ネットワークはサービス エンドポイントをサポートしていません。

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Azure Cosmos DB のサービス エンドポイント アクセスが有効になっているときに "パブリック Azure データセンター内からの接続を受け入れる" ことはできますか?  
これは、Azure Data Factory、Azure Cognitive Search、または特定の Azure リージョンにデプロイされるサービスのような他の Azure ファースト パーティ サービスによって Azure Cosmos DB アカウントがアクセスされる場合にのみ必要です。


## <a name="next-steps"></a>次のステップ

* [仮想ネットワーク内のサブネットへの Azure Cosmos アカウントのアクセスを制限する方法](how-to-configure-vnet-service-endpoint.md)
* [Azure Cosmos アカウントに IP ファイアウォールを構成する方法](how-to-configure-firewall.md)


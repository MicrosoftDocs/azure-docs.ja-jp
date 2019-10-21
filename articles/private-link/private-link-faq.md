---
title: Azure Private Link のよく寄せられる質問 (FAQ)
description: Azure Private Link について学習します。
services: private-link
author: KumudD
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 32e570e098c9a3f95dfc5393348cccb1ebe1fb60
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104581"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Azure Private Link のよく寄せられる質問 (FAQ)

## <a name="private-link"></a>Private Link

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>Azure プライベート エンドポイントおよび Private Link サービスとは何ですか。

- **[Azure プライベート エンドポイント](private-endpoint-overview.md)** : Azure プライベート エンドポイントは、Azure Private Link を使用するサービスにプライベートかつ安全に接続するネットワーク インターフェイスです。 プライベート エンドポイントを使用すると、Private Link をサポートする Azure PaaS サービスや、独自の Private Link サービスに接続できます。
- **[Azure Private Link サービス](private-link-service-overview.md)** : Azure Private Link サービスは、サービス プロバイダーによって作成されるサービスです。 現時点では、Private Link サービスは、Standard Load Balancer のフロントエンド IP 構成に接続できます。 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>Private Link を使用すると、トラフィックはどのように送信されますか。
トラフィックは、Microsoft バックボーンを使用してプライベートに送信されます。 トラフィックはインターネットを経由しません。  
 
### <a name="what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints"></a>サービス エンドポイントとプライベート エンドポイントの違いは何ですか。
- プライベート エンドポイントを使用すると、特定のサービスの背後にある特定のリソースへのネットワーク アクセスが許可され、きめ細かなセグメント化が可能になります。また、トラフィックは、パブリック エンドポイントを使用せずにオンプレミスからサービスのリソースに到達できるようになります。

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Private Link サービスとプライベート エンドポイントの関係はどのようなものですか。
プライベート エンドポイントは、Azure PaaS サービスや独自の Private Link サービスなど、各種プライベート リンク リソースへのアクセスを提供します。 これは一対多の関係です。 1 つの Private Link サービスで複数のプライベート エンドポイントから接続を受信できます。 一方、1 つのプライベート エンドポイントで接続できるのは 1 つの Private Link サービスのみです。    

## <a name="private-endpoint"></a>プライベート エンドポイント 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>同じ VNet 内に複数のプライベート エンドポイントを作成することはできますか。 それらは異なるサービスに接続できますか。 
はい。 同じ VNet またはサブネット内に複数のプライベート エンドポイントを作成できます。 それらは異なるサービスに接続できます。  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>プライベート エンドポイント専用のサブネットが必要ですか。 
いいえ。 プライベート エンドポイント専用のサブネットは必要ありません。 対象のサービスがデプロイされている VNet 内にある任意のサブネットのプライベート エンドポイント IP を選択できます。  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>プライベート エンドポイントは、Azure Active Directory テナントをまたいで Private Link サービスに接続できますか。 
はい。 プライベート エンドポイントは、AD テナントをまたいで Private Link サービスまたは Azure PaaS に接続できます。  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>プライベート エンドポイントは、Azure リージョンをまたいで Azure PaaS リソースに接続できますか。
はい。 プライベート エンドポイントは、Azure リージョンをまたいで Azure PaaS リソースに接続できます。

## <a name="private-link-service"></a>Private Link サービス
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Private Link サービスを作成するための前提条件は何ですか。 
対象のサービスのバックエンドが仮想ネットワーク内にあり、かつ Standard Load Balancer の背後にある必要があります。
 
### <a name="how-can-i-scale-my-private-link-service"></a>Private Link サービスをスケーリングするにはどうすればよいですか。 
Private Link サービスは、次のいくつかの方法でスケーリングできます。 
- バックエンド VM を Standard Load Balancer の背後にあるプールに追加する 
- IP を Private Link サービスに追加する。 Private Link サービスあたり最大 8 個の IP が許可されます。  
- 新しい Private Link サービスを Standard Load Balancer に追加する。 ロード バランサーあたり最大 8 個の Private Link サービスが許可されます。   

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>自分のサービスを複数のプライベート エンドポイントに接続することはできますか。
はい。 1 つの Private Link サービスで複数のプライベート エンドポイントから接続を受信できます。 ただし、1 つのプライベート エンドポイントで接続できるのは 1 つの Private Link サービスのみです。  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Private Link サービスの公開を制御するにはどうすればよいですか。
公開を制御するには、Private Link サービスの可視性構成を使用します。 可視性では、次の 3 つの設定がサポートされます。

- **[なし]** - RBAC アクセス権を持つサブスクリプションでのみ、サービスを見つけることができます。 
- **[Restrictive]\(制限\)** - ホワイトリストに登録され、RBAC アクセス権を持つサブスクリプションでのみ、サービスを見つけることができます。 
- **[すべて]** - すべてのユーザーがサービスを見つけることができます。 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Basic Load Balancer で Private Link サービスを作成することはできますか。 
いいえ。 Basic Load Balancer では Private Link サービスはサポートされていません。
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Private Link サービスには専用サブネットが必要ですか。 
いいえ。 Private Link サービスに専用サブネットは必要ありません。 対象のサービスがデプロイされている VNet 内の任意のサブネットを選択できます。   

### <a name="i-am-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>当社は Azure Private Link を使用しているサービス プロバイダーです。 当社のすべての顧客が一意の IP 空間を持ち、当社の IP 空間と重複しないようにする必要がありますか。 
いいえ。 その機能は Azure Private Link によって提供されます。 したがって、顧客のアドレス空間と重複しないアドレス空間を持つ必要はありません。 

##  <a name="next-steps"></a>次の手順

- [Azure Private Link](private-link-overview.md) について学習します

---
title: Azure Private Link のよく寄せられる質問 (FAQ)
description: Azure Private Link について学習します。
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: allensu
ms.openlocfilehash: d06e90a691389b99d8f439364203b921f49b2305
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103496475"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Azure Private Link のよく寄せられる質問 (FAQ)

## <a name="private-link"></a>Private Link

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>Azure プライベート エンドポイントおよび Private Link サービスとは何ですか。

- **[Azure プライベート エンドポイント](private-endpoint-overview.md)** : Azure プライベート エンドポイントは、Azure Private Link を使用するサービスにプライベートかつ安全に接続するネットワーク インターフェイスです。 プライベート エンドポイントを使用すると、Private Link をサポートする Azure PaaS サービスや、独自の Private Link サービスに接続できます。
- **[Azure Private Link サービス](private-link-service-overview.md)** : Azure Private Link サービスは、サービス プロバイダーによって作成されるサービスです。 現時点では、Private Link サービスは、Standard Load Balancer のフロントエンド IP 構成に接続できます。 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>Private Link を使用すると、トラフィックはどのように送信されますか。
トラフィックは、Microsoft バックボーンを使用してプライベートに送信されます。 トラフィックはインターネットを経由しません。 Azure プライベート リンクには、顧客データが格納されません。
 
### <a name="what-is-the-difference-between-service-endpoints-and-private-endpoints"></a>サービス エンドポイントとプライベート エンドポイントの違いは何ですか。
- プライベート エンドポイントでは、詳細なセグメンテーションを提供する特定のサービスの背後にある特定のリソースへのネットワーク アクセスが許可されます。 トラフィックは、パブリック エンドポイントを使用せずにオンプレミスからサービス リソースに到達できます。
- サービス エンドポイントは、公的にルーティング可能な IP アドレスのままです。  プライベート エンドポイントは、プライベート エンドポイントが構成されている仮想ネットワークのアドレス空間にあるプライベート IP です。

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Private Link サービスとプライベート エンドポイントの関係はどのようなものですか。
複数プライベート リンク リソースの種類では、プライベート エンドポイント経由のアクセスがサポートされます。 リソースには、Azure PaaS サービスとユーザー独自の Private Link サービスが含まれます。 これは一対多の関係です。 

1 つの Private Link サービスでは複数のプライベート エンドポイントから接続を受信できます。 1 つのプライベート エンドポイントは 1 つの Private Link サービスに接続します。    

### <a name="do-i-need-to-disable-network-policies-for-private-link"></a>Private Link ではネットワーク ポリシーを無効にする必要がありますか。
はい。 プライベート エンドポイントと Private Link サービスでは、どちらも正常に機能させるためにネットワーク ポリシーを無効にする必要があります。 これらの両方に、互いに独立したプロパティがあります。

## <a name="private-endpoint"></a>プライベート エンドポイント 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>同じ VNet 内に複数のプライベート エンドポイントを作成することはできますか。 それらは異なるサービスに接続できますか。 
正解です。 同じ VNet またはサブネット内に複数のプライベート エンドポイントを作成できます。 それらは異なるサービスに接続できます。  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>プライベート エンドポイント専用のサブネットが必要ですか。 
不正解です。 プライベート エンドポイント専用のサブネットは必要ありません。 対象のサービスがデプロイされている VNet 内にある任意のサブネットのプライベート エンドポイント IP を選択できます。  
 
### <a name="can-a-private-endpoint-connect-to-private-link-services-across-azure-active-directory-tenants"></a>プライベート エンドポイントから Azure Active Directory テナントをまたいで Private Link サービスに接続できますか? 
はい。 プライベート エンドポイントでは、Azure Active Directory テナントをまたいで Private Link サービスまたは Azure PaaS に接続できます。 テナントをまたいで接続するプライベート エンドポイントには、手動による要求の承認が必要です。 
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>プライベート エンドポイントは、Azure リージョンをまたいで Azure PaaS リソースに接続できますか。
正解です。 プライベート エンドポイントは、Azure リージョンをまたいで Azure PaaS リソースに接続できます。

### <a name="can-i-modify-my-private-endpoint-network-interface-nic-"></a>プライベート エンドポイント ネットワーク インターフェイス (NIC) を変更できますか。
プライベート エンドポイントが作成されると、読み取り専用の NIC が割り当てられます。 これは変更できず、プライベート エンドポイントのライフ サイクルの間、そのままになります。

### <a name="how-do-i-achieve-availability-while-using-private-endpoints-in-case-of-regional-failures-"></a>リージョンで障害が発生した場合にプライベート エンドポイントを使用して可用性を実現するにはどうすればよいですか。

プライベート エンドポイントは、SLA が 99.99% の高可用性リソースです [[Azure Private Link の SLA]](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/)。 ただし、これらはリージョン リソースであるため、Azure リージョンの停止が可用性に影響する可能性があります。 リージョンで障害が発生した場合に可用性を実現するために、同じ宛先リソースに接続された複数の PE を、異なるリージョンにデプロイできます。 このようにして、1 つのリージョンがダウンした場合でも、別のリージョンの PE を介して復旧シナリオ用のトラフィックをルーティングし、宛先リソースにアクセスすることができます。 宛先サービス側でリージョンの障害がどのように処理されるかについての詳細は、フェールオーバーと復旧に関するサービスのドキュメントを参照してください。 Private Link トラフィックは、宛先エンドポイントの Azure DNS 解決に従います。 


## <a name="private-link-service"></a>Private Link サービス
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Private Link サービスを作成するための前提条件は何ですか。 
対象のサービスのバックエンドが仮想ネットワーク内にあり、かつ Standard Load Balancer の背後にある必要があります。
 
### <a name="how-can-i-scale-my-private-link-service"></a>Private Link サービスをスケーリングするにはどうすればよいですか。 
Private Link サービスは、次のいくつかの方法でスケーリングできます。 
- バックエンド VM を Standard Load Balancer の背後にあるプールに追加する 
- IP を Private Link サービスに追加する。 Private Link サービスあたり最大 8 個の IP が許可されます。  
- 新しい Private Link サービスを Standard Load Balancer に追加する。 ロード バランサーあたり最大 8 個の Private Link サービスが許可されます。   

### <a name="what-is-natnetwork-address-translation-ip-configuration-used-in-private-link-service-how-can-i-scale-in-terms-of-available-ports-and-connections"></a>Private Link サービスで使用される NAT (ネットワーク アドレス変換) IP 構成とは何ですか。 使用可能なポートと接続をスケーリングするにはどうすればよいですか。 

NAT IP 構成を使用して、宛先側 (サービス プロバイダー側) の Private Link トラフィックにソース NAT を指定することにより、ソース (コンシューマー側) と宛先 (サービス プロバイダー) のアドレス空間の間で IP の競合が生じなくなります。 NAT IP アドレスは、対象のサービスで受信されたすべてのパケットのソース IP および対象のサービスで送信されたすべてのパケットの宛先 IP として表示されます。  NAT IP は、サービス プロバイダーの仮想ネットワーク内の任意のサブネットから選択できます。 

各 NAT IP により、Standard Load Balancer の背後にある VM ごとに 64,000 個の TCP 接続 (64,000 個のポート) を使用できるようになります。 接続をスケールして追加するには、新しい NAT IP を追加するか、Standard Load Balancer の背後に VM を追加します。 こうすることでポートの可用性がスケールされ、より多くの接続が可能になります。 接続は、NAT IP と Standard Load Balancer の背後にある VM に分散されます。

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>自分のサービスを複数のプライベート エンドポイントに接続することはできますか。
正解です。 1 つの Private Link サービスで複数のプライベート エンドポイントから接続を受信できます。 ただし、1 つのプライベート エンドポイントで接続できるのは 1 つの Private Link サービスのみです。  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Private Link サービスの公開を制御するにはどうすればよいですか。
公開を制御するには、Private Link サービスの可視性構成を使用します。 可視性では、次の 3 つの設定がサポートされます。

- **[なし]** - Azure RBAC アクセス権を持つサブスクリプションでのみ、サービスを見つけることができます。 
- **[制限]** - 承認され、Azure RBAC アクセス権を持つサブスクリプションでのみ、サービスを見つけることができます。 
- **[すべて]** - すべてのユーザーがサービスを見つけることができます。 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Basic Load Balancer で Private Link サービスを作成することはできますか。 
いいえ。 Basic Load Balancer では Private Link サービスはサポートされていません。
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Private Link サービスには専用サブネットが必要ですか。 
不正解です。 Private Link サービスに専用サブネットは必要ありません。 対象のサービスがデプロイされている VNet 内の任意のサブネットを選択できます。   

### <a name="im-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>当社は Azure Private Link を使用しているサービス プロバイダーです。 当社のすべての顧客が一意の IP 空間を持ち、当社の IP 空間と重複しないようにする必要がありますか。 
不正解です。 その機能は Azure Private Link によって提供されます。 顧客のアドレス空間と重複しないアドレス空間を持つ必要はありません。 

##  <a name="next-steps"></a>次のステップ

- [Azure Private Link](private-link-overview.md) について学習します

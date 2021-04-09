---
title: よく寄せられる質問 - Azure Load Balancer
description: Azure Load Balancer についてよく寄せられる質問とその回答を紹介します。
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 3752a36d22f879b95b02bd49436be78212fe56a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99576043"
---
# <a name="load-balancer-frequently-asked-questions"></a>Load Balancer に関してよく寄せられる質問

## <a name="what-types-of-load-balancer-exist"></a>Azure Load Balancer には、どんな種類がありますか?
VNET 内のトラフィックを負荷分散する内部ロード バランサーと、インターネットに接続されたエンドポイントとの間のトラフィックを負荷分散する外部ロード バランサーがあります。 詳細については、[Load Balancer の種類](components.md#frontend-ip-configurations)に関するページを参照してください。 

これらのどちらの種類についても、Azure では、さまざまな機能、パフォーマンス、セキュリティ、正常性の追跡機能を含む Basic SKU と Standard SKU が提供されています。 これらの違いについては、記事「[SKU の比較](skus.md)」で説明しています。

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>Basic Load Balancer から Standard Load Balancer にアップグレードするには、どうすればよいですか?
Load Balancer の SKU をアップグレードするための自動スクリプトとガイダンスについては、[Basic から Standard へのアップグレード](upgrade-basic-standard.md)に関する記事を参照してください。

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Azure の負荷分散オプションには、どのような違いがありますか?
使用可能な負荷分散サービスとそれぞれの推奨される使用方法については、[ロード バランサーのテクノロジ ガイド](/azure/architecture/guide/technology-choices/load-balancing-overview)を参照してください。

## <a name="where-can-i-find-load-balancer-arm-templates"></a>Azure Load Balancer の ARM テンプレートはどこで入手できますか?
一般的なデプロイの ARM テンプレートについては、[Azure Load Balancer クイックスタート テンプレート](/azure/templates/microsoft.network/loadbalancers#quickstart-templates)の一覧を参照してください。

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>インバウンド NAT 規則と負荷分散規則には、どのような違いがありますか?
NAT 規則は、トラフィックをルーティングするバックエンド リソースを指定するために使用されます。 たとえば、特定の VM に RDP トラフィックを送信するように特定のロード バランサー ポートを構成することができます。 負荷分散規則は、トラフィックをルーティングするバックエンド リソースのプールを指定して、各インスタンス間で負荷を分散します。 たとえば、負荷分散規則では、ロード バランサーのポート 80 上の TCP パケットを Web サーバーのプール全体にルーティングできます。

## <a name="what-is-ip-1686312916"></a>IP 168.63.129.16 とは何か?
Azure の正常性プローブが開始される Azure インフラストラクチャのロード バランサーとしてタグ付けされたホストの仮想 IP アドレスです。 バックエンド インスタンスを構成するとき、正常性プローブに正常に応答するために、この IP アドレスからのトラフィックを許可する必要があります。 この規則は、Load Balancer フロントエンドへのアクセスには影響しません。 Azure ロード バランサーを使用していない場合は、この規則を無視できます。 サービス タグの詳細については、[こちら](../virtual-network/service-tags-overview.md#available-service-tags)を参照してください。

## <a name="can-i-use-global-vnet-peering-with-basic-load-balancer"></a>Basic Load Balancer でグローバル VNet ピアリングを使用できますか?
いいえ。 Basic Load Balancer では、グローバル VNET ピアリングはサポートされていません。 代わりに Standard ロード バランサーを使用できます。 シームレスなアップグレードについては、[Basic から Standard へのアップグレード](upgrade-basic-standard.md)に関する記事を参照してください。

## <a name="how-can-i-discover-the-public-ip-that-an-azure-vm-uses"></a>Azure VM で使用されているパブリック IP を検出するにはどうすればよいですか?

送信接続のパブリック ソース IP アドレスを判別する方法は多数あります。 OpenDNS によって提供されるサービスで、VM のパブリック IP アドレスを表示できます。
nslookup コマンドを使用することで、名前 myip.opendns.com に関する DNS クエリを OpenDNS Resolver に送信できます。 このサービスは、クエリの送信に使用されたソース IP アドレスを返します。 VM から次のクエリを実行すると、その VM で使用されるパブリック IP が応答として返されます。

 ```nslookup myip.opendns.com resolver1.opendns.com```
 
## <a name="can-i-add-a-vm-from-the-same-availability-set-to-different-backend-pools-of-a-load-balancer"></a>同じ可用性セットから Load Balancer の異なるバックエンド プールに VM を追加することはできますか?
いいえ、それはできません。

## <a name="what-is-the-maximum-data-throughput-that-can-be-achieved-via-an-azure-load-balancer"></a>Azure Load Balancer で実現できる最大データ スループットはどのくらいですか?
Azure LB はパススルー ネットワーク ロード バランサーであるため、スループットの制限は、バックエンド プールで使用される仮想マシンの種類によって決まります。 その他のネットワーク スループットに関連する情報については、「[仮想マシンのネットワーク スループット](../virtual-network/virtual-machine-network-throughput.md)」を参照してください。

## <a name="how-do-connections-to-azure-storage-in-the-same-region-work"></a>同じリージョン内の Azure Storage への接続はどのように機能しますか?
上記のシナリオでの送信接続の使用では、VM と同じリージョン内の Storage に接続する必要はありません。 これを望まない場合は、前述のようにネットワーク セキュリティ グループ (NSG) を使用します。 他のリージョン内の Storage への接続では、送信接続が必要です。 同じリージョン内の VM から Storage に接続する場合、Storage 診断ログ内のソース IP アドレスは、VM のパブリック IP アドレスではなく、内部プロバイダー アドレスになることに注意してください。 お使いの Storage アカウントへのアクセスを、同じリージョン内の 1 つ以上の仮想ネットワーク サブネット内の VM に制限する場合は、ストレージ アカウントのファイアウォールを構成するときに、パブリック IP アドレスではなく、[仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)を使用します。 サービス エンドポイントを構成すると、診断ログには、内部プロバイダー アドレスではなく、お使いの仮想ネットワークのプライベート IP アドレスが表示されます。

## <a name="does-azure-load-balancer-support-tlsssl-termination"></a>Azure Load Balancer は TLS/SSL 終端をサポートしていますか?
いいえ。Azure Load Balancer はパススルー ネットワーク ロード バランサーであるため、現在、終端をサポートしていません。 お使いのアプリケーションでこれが必要な場合は、Application Gateway が解決策になる可能性があります。

## <a name="what-are-best-practises-with-respect-to-outbound-connectivity"></a>アウトバウンド接続に関するベスト プラクティスは何ですか。
Standard Load Balancer および Standard パブリック IP では、アウトバウンド接続に機能とさまざまな動作が導入されています。 これらは Basic SKU と同じではありません。 Standard SKU を操作するときにアウトバウンド接続が必要な場合は、Standard パブリック IP アドレスまたは Standard パブリック Load Balancer で明示的に定義する必要があります。 これには、内部 Standard Load Balancer を使用する場合のアウトバウンド接続の作成が含まれます。 Standard パブリック Load Balancer では常にアウトバウンド規則を使用することをお勧めします。 つまり、内部 Standard Load Balancer が使用されているときに、アウトバウンド接続が必要な場合、バックエンド プール内の VM に対してアウトバウンド接続を作成する手順を行う必要があります。 アウトバウンド接続のコンテキストでは、単一スタンドアロン VM、可用性セット内のすべての VM、VMSS のすべてのインスタンスがグループとして動作します。 つまり、可用性セット内の単一 VM が Standard SKU に関連付けられている場合、この可用性セット内のすべての VM インスタンスが、Standard SKU に関連付けられている場合と同じ規則に従って動作するようになります。個々のインスタンスが直接関連付けられていない場合でも同様です。 この動作は、ロード バランサーに複数のネットワーク インターフェイスカードが接続されているスタンドアロン VM の場合にも見られます。 1 つの NIC をスタンドアロンとして追加された場合、同じ動作が行われます。 このドキュメント全体をよく読み、全体的な概念を理解し、SKU 間の違いについて [Standard Load Balancer](./load-balancer-overview.md) を確認し、[アウトバウンド規則](load-balancer-outbound-connections.md#outboundrules)を確認してください。
アウトバウンド規則を使用することで、アウトバウンド接続のすべての側面を細かく制御できます。
 
## <a name="next-steps"></a>次の手順
ご不明な点が上記の一覧にない場合は、このページに関するフィードバックにご質問を添えてお送りください。 お送りいただくと、製品チームでは、お客様からの貴重なご質問すべてにお答えできるよう、GitHub 問題を作成しています。

---
title: Azure DDoS Protection Standard のよくあるご質問
description: Azure DDoS Protection Standard についてよく寄せられる質問です。DDoS 攻撃に対する防御に役立ちます。
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: yitoh
ms.openlocfilehash: 89685d1b8c3a57fa142bbbfd1114f9aa0ff0c400
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98611119"
---
# <a name="azure-ddos-protection-standard-frequent-asked-questions"></a>Azure DDoS Protection Standard のよくあるご質問

この記事では、Azure DDoS Protection Standard についてよく寄せられる質問にお答えします。 

## <a name="what-is-a-distributed-denial-of-service-ddos-attack"></a>分散型サービス拒否 (DDoS) 攻撃とはどのようなものですか?
分散型サービス拒否 (DDoS) は、アプリケーションで処理できるより多くの要求が攻撃者によってアプリケーションに送信される攻撃の一種です。 結果として、リソースが枯渇し、アプリケーションの可用性と顧客にサービスを提供できる機能に影響するようになります。 この数年で、業界では攻撃が大幅に増加しており、攻撃はますます高度で大規模になっています。 DDoS 攻撃は、インターネット経由で一般に到達可能なすべてのエンドポイントで実行できます。

## <a name="what-is-azure-ddos-protection-standard-service"></a>Azure DDoS Protection Standard サービスとはどのようなものですか?
Azure DDoS Protection Standard は、アプリケーションの設計に関するベスト プラクティスと組み合わせることにより、DDoS 攻撃から保護するための強化された DDoS 軽減機能が提供されます。 この機能は、仮想ネットワーク内にあるお客様固有の Azure リソースを保護するために、自動的に調整されます。 保護は新規または既存の仮想ネットワークで簡単に有効にでき、アプリケーションやリソースの変更は必要ありません。 ログ、アラート、テレメトリなど、基本サービスにはない利点がいくつかあります。 詳細については、「[Azure DDoS Protection Standard の概要](ddos-protection-overview.md)」を参照してください。 

## <a name="how-does-pricing-work"></a>価格体系について
DDoS 保護プランには、1 か月あたり $2,944 の固定月額料金が適用されます。これは、最大 100 のパブリック IP アドレスに対応しています。 追加のリソースを保護すると、1 か月あたりリソースごとに $30 の追加料金が発生します。 

テナントの場合、複数のサブスクリプションに対して 1 つの DDoS 保護プランを使用できるため、複数の DDoS 保護プランを作成する必要はありません。

詳細については、[Azure DDoS Protection Standard の価格](https://azure.microsoft.com/pricing/details/ddos-protection/)に関するページを参照してください。

## <a name="is-the-service-zone-resilient"></a>サービスにゾーン回復性はありますか?
はい。 既定では、Azure DDoS Protection はゾーン回復性を備えています。

## <a name="how-do-i-configure-the-service-to-be-zone-resilient"></a>サービスにゾーン回復性を構成するにはどのようにすればよいですか?
ゾーン回復性を有効にするために、顧客による構成は必要ありません。 Azure DDoS Protection リソースのゾーン回復性は、既定で使用でき、サービス自体によって管理されます。

## <a name="what-about-protection-at-the-service-layer-layer-7"></a>サービス レイヤー (レイヤー 7) での保護とはどのようなものですか?
お客様は、Azure DDoS Protection サービスと Web Application Firewall (WAF) を組み合わせて使用し、ネットワーク レイヤー (レイヤー 3 と 4、Azure DDoS Protection Standard によって提供されます) とアプリケーション レイヤー (レイヤー 7、WAF によって提供されます) の両方で保護を行うことができます。 WAF オファリングには、Azure [Application Gateway WAF SKU](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) に加えて、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall) で利用できるサードパーティの Web アプリケーション ファイアウォール オファリングが含まれています。

## <a name="are-services-unsafe-in-azure-without-the-service"></a>このサービスを使用しないと Azure のサービスが安全ではくなりますか?
Azure で実行されているサービスは、Azure のインフラストラクチャを保護するために導入されている Azure DDoS Protection Basic によって本質的に保護されています。 ただし、インフラストラクチャに対する保護の場合、ほとんどのアプリケーションで処理できる容量よりはるかに高いしきい値を持ち、テレメトリやアラートが提供されないため、トラフィック量がプラットフォームによって無害であると認識されても、それを受信するアプリケーションにとっては壊滅的になる可能性があります。 

Azure DDoS Protection Standard サービスにオンボードすることで、アプリケーションには、攻撃とアプリケーション固有のしきい値を検出するための専用の監視が提供されます。 サービスは、予想されるトラフィック量に合わせて調整されたプロファイルを使用して保護され、DDoS 攻撃に対する防御は、はるかに厳しくなります。

## <a name="what-are-the-supported-protected-resource-types"></a>どのようなリソースの種類が保護対象としてサポートされていますか?
現在、ARM ベースの VNET でのパブリック IP が、保護されるリソースの唯一の種類です。 PaaS サービス (マルチテナント) は、現時点ではサポートされていません。 詳細については、[Azure DDoS Protection Standard の参照アーキテクチャ](ddos-protection-reference-architectures.md)に関する記事を参照してください。

## <a name="are-classicrdfe-protected-resources-supported"></a>クラシックおよび RDFE の保護されたリソースはサポートされていますか?
プレビューでは、ARM ベースの保護されたリソースのみがサポートされています。 クラシックおよび RDFE のデプロイの VM はサポートされていません。 現在、クラシックおよび RDFE リソースのサポートは計画されていません。 詳細については、[Azure DDoS Protection Standard の参照アーキテクチャ](ddos-protection-reference-architectures.md)に関する記事を参照してください。

## <a name="can-i-protect-my-paas-resources-using-ddos-protection"></a>DDoS Protection を使用して PaaS のリソースを保護することはできますか?
マルチテナント、単一 VIP PaaS サービスに接続されているパブリック IP は現在サポートされていません。 サポートされていないリソースの例には、ストレージ VIP、イベントハブ VIP、App または Cloud Services アプリケーションなどがあります。 詳細については、[Azure DDoS Protection Standard の参照アーキテクチャ](ddos-protection-reference-architectures.md)に関する記事を参照してください。

## <a name="can-i-protect-my-on-premise-resources-using-ddos-protection"></a>DDoS Protection を使用してオンプレミスのリソースを保護することはできますか?
DDoS Protection を有効にするには、サービスのパブリック エンドポイントが、Azure の VNet に関連付けられている必要があります。 設計の例には次ものが含まれます。
- Azure の Web サイト (IaaS) と、オンプレミスのデータセンターのバックエンド データベース。 
- Azure の Application Gateway (App Gateway と WAF で DDoS Protection が有効) と、オンプレミス データセンターの Web サイト。

詳細については、[Azure DDoS Protection Standard の参照アーキテクチャ](ddos-protection-reference-architectures.md)に関する記事を参照してください。

## <a name="can-i-register-a-domain-outside-of-azure-and-associate-that-to-a-protected-resource-like-vm-or-elb"></a>Azure の外部にあるドメインを登録し、VM や ELB などの保護されたリソースにそれを関連付けることはできますか?
パブリック IP のシナリオの場合、アプリケーションは、関連付けられたパブリック IP が Azure でホストされている限り、関連付けられているドメインがどこで登録またはホストされているかに関係なく、DDoS Protection サービスによってサポートされます。 

## <a name="can-i-manually-configure-the-ddos-policy-applied-to-the-vnetspublic-ips"></a>VNet やパブリック IP に適用される DDoS ポリシーを、手動で構成することはできますか?
いいえ、現時点では残念ながらポリシーのカスタマイズは利用できません。

## <a name="can-i-allowlistblocklist-specific-ip-addresses"></a>特定の IP アドレスを許可リストまたはブロックリストに登録することはできますか?
いいえ、現時点では残念ながら手動で構成することはできません。

## <a name="how-can-i-test-ddos-protection"></a>どうすれば DDoS Protection をテストできますか?
[シミュレーションによるテスト](test-through-simulations.md)に関する記事を参照してください。

## <a name="how-long-does-it-take-for-the-metrics-to-load-on-portal"></a>メトリックがポータルに読み込まれるまでにどれくらいの時間がかかりますか?
メトリックは、5 分以内にポータルに表示されるはずです。 リソースが攻撃を受けている場合、他のメトリックはポータルに表示され始めるまでに 5 分から 7 分かかります。 

## <a name="does-the-service-store-customer-data"></a>サービスによって顧客データは保存されますか?
いいえ、Azure DDoS Protection によって顧客データが保存されることはありません。
    

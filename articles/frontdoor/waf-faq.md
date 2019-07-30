---
title: Azure Web アプリケーション ファイアウォール - よく寄せられる質問
description: このページでは、Azure Front Door Service に関してよく寄せられる質問の回答を示します
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: c993e465bc439ff52cba3241dbff64b7655d1f12
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849171"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall"></a>Azure Web アプリケーション ファイアウォールに関してよく寄せられる質問

この記事では、Azure Web アプリケーション ファイアウォール (WAF) の機能に関する一般的な質問に回答します。 

## <a name="what-is-azure-waf"></a>Azure WAF とは何ですか?

Azure WAF は Web アプリケーション ファイアウォールです。SQL インジェクション、クロスサイト スクリプティング、その他の Web エクスプロイトなどの一般的な脅威から Web アプリケーションを保護するのに役立ちます。 Web アプリケーションへのアクセスを制御するために、カスタム ルールとマネージド ルールの組み合わせからなる WAF ポリシーを定義できます。

Azure WAF ポリシーは、Application Gateway または Azure Front Door Service でホストされている Web アプリケーションに適用できます。

## <a name="what-is-waf-for-azure-front-door-service"></a>Azure Front Door Service 用の WAF とは何ですか? 

Azure Front Door は、高度にスケーラブルでグローバルに分散したアプリケーションおよびコンテンツ配信ネットワークです。 Azure WAF を Front Door と統合すると、サービス拒否攻撃や標的型アプリケーション攻撃がユーザーの仮想ネットワークに侵入する前に攻撃元に近い Azure ネットワーク エッジで阻止し、パフォーマンスを犠牲にすることなく保護を提供できます。

## <a name="does-azure-waf-support-https"></a>Azure WAF では HTTPS がサポートされますか?

Front Door Service では SSL オフロードが提供されます。 WAF はネイティブに Front Door と統合され、要求が復号化された後にその要求を検査できます。

## <a name="does-azure-waf-support-ipv6"></a>Azure WAF では IPv6 がサポートされますか?

はい。 IPv4 と IPv6 に IP 制限を構成できます。

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>マネージド ルール セットの更新頻度はどの程度ですか?

変化する脅威の状況に対応するために最善を尽くしています。 新しいルールが更新されると、新しいバージョン番号で既定のルール セットに追加されます。

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>WAF ポリシーを変更した場合、伝播されるのにどのくらい時間がかかりますか?

WAF のポリシーをグローバルにデプロイするには、通常約 5 分かかりますが、多くの場合より早く完了します。

## <a name="can-waf-policies-be-different-for-different-regions"></a>WAF のポリシーはリージョンごとに異なるものにできますか?

WAF は、Front Door Service と統合されると、グローバルなリソースになります。 同じ構成がすべての Front Door の場所に適用されます。
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>バックエンドへのアクセスを Front Door からのみに制限するにはどうすればよいですか?

Front Door の送信 IP アドレス範囲のみを許可し、インターネットからの直接アクセスを拒否するように、バックエンドで IP アクセス制御リストを構成できます。 仮想ネットワークで使用するためのサービス タグがサポートされています。 さらに、X-Forwarded-Host HTTP ヘッダー フィールドが Web アプリケーションに対して有効であることを確認できます。




## <a name="which-azure-waf-options-should-i-choose"></a>どの Azure WAF オプションを選択すればよいですか?

Azure に WAF ポリシーを適用する場合、2 つの選択肢があります。 Azure Front Door を使用した WAF は、グローバルに分散したエッジ セキュリティ ソリューションです。 Application Gateway を使用した WAFは、リージョンの専用ソリューションです。 全体的なパフォーマンスとセキュリティの要件に基づいてソリューションを選択することをお勧めします。 詳細については、「[Azure のアプリケーション配信スイートでの負荷分散](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite)」を参照してください。


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>すべての統合プラットフォームで同じ WAF 機能がサポートされますか?

現時点では、ModSec CRS 2.2.9 および CRS 3.0 ルールは、Application Gatewayの WAFで のみサポートされています。 レート制限、geo フィルタリング、Azure 管理の既定のルール セットのルールは、Azure Front Door の WAF でのみサポートされています。

## <a name="is-ddos-protection-integrated-with-front-door"></a>DDoS 保護は Front Door と統合されていますか? 

Azure のネットワーク エッジにグローバルに分散された Azure Front Door は、大量の攻撃を受け止めて地理的に隔離することができます。 既知のシグネチャを持つ http(s) 攻撃を自動的にブロックおよびレート制限するためのカスタム WAF ポリシーを作成できます。 さらに、バックエンドがデプロイされている VNet で DDoS Protection Standard を有効にすることができます。 Azure DDoS Protection Standard をご利用のお客様は、コスト保護、SLA 保証、攻撃の際に DDoS Rapid Response Team の専門家に相談してすぐに支援を受けるなど、追加の利点を得ることができます。 

## <a name="next-steps"></a>次の手順

- [Azure Web アプリケーション ファイアウォール](waf-overview.md)について学習します。
- [Azure Front Door](front-door-overview.md) の詳細を確認します。

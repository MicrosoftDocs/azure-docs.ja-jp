---
title: Azure Front Door Service の Azure Web アプリケーション ファイアウォール - よく寄せられる質問
description: この記事では、Azure Front Door の Web アプリケーション ファイアウォールに関してよく寄せられる質問の回答を示します
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 5c2763112b1aa2d58f5dc57cea72a3d0bdea961e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "95545671"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Azure Front Door Service の Azure Web アプリケーション ファイアウォールに関してよく寄せられる質問

この記事では、Azure Front Door Service の Azure Web アプリケーション ファイアウォール (WAF) 機能に関する一般的な質問に回答します。 

## <a name="what-is-azure-waf"></a>Azure WAF とは何ですか?

Azure WAF は Web アプリケーション ファイアウォールです。SQL インジェクション、クロスサイト スクリプティング、その他の Web エクスプロイトなどの一般的な脅威から Web アプリケーションを保護するのに役立ちます。 Web アプリケーションへのアクセスを制御するために、カスタム ルールとマネージド ルールの組み合わせからなる WAF ポリシーを定義できます。

Azure WAF ポリシーは、Application Gateway または Azure Front Door でホストされている Web アプリケーションに適用できます。

## <a name="what-is-waf-on-azure-front-door"></a>Azure Front Door の WAF とは何ですか? 

Azure Front Door は、高度にスケーラブルでグローバルに分散したアプリケーションおよびコンテンツ配信ネットワークです。 Azure WAF を Front Door と統合すると、サービス拒否攻撃や標的型アプリケーション攻撃がユーザーの仮想ネットワークに侵入する前に攻撃元に近い Azure ネットワーク エッジで阻止し、パフォーマンスを犠牲にすることなく保護を提供できます。

## <a name="does-azure-waf-support-https"></a>Azure WAF では HTTPS がサポートされますか?

Front Door では TLS オフロードが提供されます。 WAF はネイティブに Front Door と統合され、暗号化解除された後にその要求を検査できます。

## <a name="does-azure-waf-support-ipv6"></a>Azure WAF では IPv6 がサポートされますか?

はい。 IPv4 と IPv6 に IP 制限を構成できます。

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>マネージド ルール セットの更新頻度はどの程度ですか?

変化する脅威の状況に対応するために最善を尽くしています。 新しいルールが更新されると、新しいバージョン番号で既定のルール セットに追加されます。

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>WAF ポリシーを変更した場合、伝播されるのにどのくらい時間がかかりますか?

WAF のポリシーをグローバルにデプロイするには、通常約 5 分かかりますが、多くの場合より早く完了します。

## <a name="can-waf-policies-be-different-for-different-regions"></a>WAF のポリシーはリージョンごとに異なるものにできますか?

WAF は、Front Door と統合されると、グローバルなリソースになります。 同じ構成がすべての Front Door の場所に適用されます。
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>バックエンドへのアクセスを Front Door からのみに制限するにはどうすればよいですか?

Front Door の送信 IP アドレス範囲のみを許可し、インターネットからの直接アクセスを拒否するように、バックエンドで IP アクセス制御リストを構成できます。 仮想ネットワークで使用するためのサービス タグがサポートされています。 さらに、X-Forwarded-Host HTTP ヘッダー フィールドがご利用の Web アプリケーションに対して有効であることを確認できます。

## <a name="which-azure-waf-options-should-i-choose"></a>どの Azure WAF オプションを選択すればよいですか?

Azure に WAF ポリシーを適用する場合、2 つの選択肢があります。 Azure Front Door を使用した WAF は、グローバルに分散したエッジ セキュリティ ソリューションです。 Application Gateway を使用した WAFは、リージョンの専用ソリューションです。 全体的なパフォーマンスとセキュリティの要件に基づいてソリューションを選択することをお勧めします。 詳細については、「[Azure のアプリケーション配信スイートでの負荷分散](../../frontdoor/front-door-lb-with-azure-app-delivery-suite.md)」を参照してください。

## <a name="whats-the-recommended-approach-to-enabling-waf-on-front-door"></a>Front Door での WAF の有効化に向けて推奨されるアプローチには、どのようなものがありますか?

既存のアプリケーションで WAF を有効にする場合、正当なトラフィックが WAF ルールによって脅威として検知されるという誤検知が発生するのは一般的なことです。 ユーザーへの影響のリスクを最小限に抑えるには、次のプロセスをお勧めします。

* このプロセスの実行中に WAF によって要求がブロックされないようにするには、[**検出** モード](./waf-front-door-create-portal.md#change-mode)で WAF を有効にします。
  > [!IMPORTANT]
  > このプロセスでは、アプリケーションのユーザーにもたらされる支障を最小限に抑えることが最優先される場合に、新規または既存のソリューションで WAF を有効にする方法について説明します。 攻撃を受けている場合や、脅威が発生している場合は、代わりに、**防止** モードで WAF を直ちにデプロイし、チューニング プロセスを使用して時間をかけて WAF を監視および調整することをお勧めします。 これにより、正当なトラフィックの一部がブロックされてしまうおそれがあります。脅威を受けている場合にのみこれを行うようにお勧めするのは、このような理由からです。
* [WAF のチューニングに関するガイダンス](./waf-front-door-tuning.md)に従ってください。 このプロセスでは、診断ログを有効にし、ログを定期的に確認して、ルールの除外やその他の軽減策を追加する必要があります。
* このプロセス全体を繰り返し実行し、正当なトラフィックがブロックされていないことを確認できるまで、ログを定期的に確認します。 プロセス全体を実行するには、数週間かかることがあります。 チューニングを変更するたびに、誤検知が減ることが理想的です。
* 最後に、**防止モード** で WAF を有効にします。
* 運用環境で WAF を実行している場合でも、他の誤検知を特定するために、ログの監視を続ける必要があります。 また、ログを定期的に確認することで、ブロックされている本物の攻撃を特定しやすくなります。

## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>すべての統合プラットフォームで同じ WAF 機能がサポートされますか?

現時点では、ModSec CRS 2.2.9、CRS 3.0、CRS 3.1 ルールは、Application Gateway の WAF でのみサポートされています。 レート制限、geo フィルタリング、Azure 管理の既定のルール セットのルールは、Azure Front Door の WAF でのみサポートされています。

## <a name="is-ddos-protection-integrated-with-front-door"></a>DDoS 保護は Front Door と統合されていますか? 

Azure のネットワーク エッジにグローバルに分散された Azure Front Door は、大量の攻撃を受け止めて地理的に隔離することができます。 既知のシグネチャを持つ http(s) 攻撃を自動的にブロックおよびレート制限するためのカスタム WAF ポリシーを作成できます。 さらに、バックエンドがデプロイされている VNet で DDoS Protection Standard を有効にすることができます。 Azure DDoS Protection Standard をご利用のお客様は、コスト保護、SLA 保証、攻撃の際に DDoS Rapid Response Team の専門家に相談してすぐに支援を受けるなど、追加の利点を得ることができます。 詳細については、「[Front Door での DDoS Protection](../../frontdoor/front-door-ddos.md)」を参照してください。

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>レート制限のルールに構成されているしきい値を超えた追加の要求が、バックエンド サーバーに渡されるのはなぜですか?

レート制限のルールでは、任意のクライアント IP アドレスからの異常に高いトラフィックを制限できます。 1 分間または 5 分間に許可されるクライアント IP アドレスからの Web 要求の数に、しきい値を構成することができます。 詳細なレート制御については、レート制限を、HTTP(S) パラメーター一致などの他の一致条件と組み合わせることができます。 

多くの場合、同じクライアントからの要求は、同じ Front Door にたどりつきます。 その場合、しきい値を超える追加の要求は、すぐにブロックされます。 

しかし、同じクライアントからの要求が、レート制限のカウンターをまだ更新していない別の Front Door サーバーにたどりつく可能性があります。 たとえば、クライアントは要求ごとに新しい接続を開く可能性があり、しきい値が低いとします。 この場合、新しい Front Door サーバーへの最初の要求は、レート制限のチェックを通過します。 通常、レート制限のしきい値は、任意のクライアント IP アドレスからのサービス拒否攻撃に対して防御するために高く設定されます。 しきい値が非常に低い場合は、しきい値を超える追加の要求が通知されることがあります。

## <a name="next-steps"></a>次のステップ

- [Azure Web アプリケーション ファイアウォール](../overview.md)について学習します。
- [Azure Front Door](../../frontdoor/front-door-overview.md) の詳細を確認します。
---
title: API Management でのクライアント応答のタイムアウトとエラーのトラブルシューティング
description: API Management での断続的な接続エラーとそれに関連した待ち時間の問題をトラブルシューティングする
author: vladvino
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: apimpm
ms.service: api-management
ms.openlocfilehash: 6cace4a02c8d45cacbbc34e9778b5c4a78ada27f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100576528"
---
# <a name="troubleshooting-client-response-timeouts-and-errors-with-api-management"></a>API Management でのクライアント応答のタイムアウトとエラーのトラブルシューティング

この記事は、[Azure API Management](./api-management-key-concepts.md) での断続的な接続エラーとそれに関連した待ち時間の問題をトラブルシューティングするのに役立ちます。 具体的には、この記事では、ソース アドレス ネットワーク変換 (SNAT) ポートの枯渇に関する情報とトラブルシューティングについて説明します。 さらにヘルプが必要な場合は、[Azure コミュニティ サポート](https://azure.microsoft.com/support/community/)で Azure の専門家に連絡するか、[Azure サポート](https://azure.microsoft.com/support/options/)でサポート要求を提出してください。

## <a name="symptoms"></a>現象

API Management (APIM) サービスを介して API を呼び出すクライアント アプリケーションでは、次の 1 つ以上の現象が発生する可能性があります。

* 断続的な HTTP 500 エラー
* タイムアウト エラー メッセージ

これらの現象は、[Azure Monitor リソース ログ](../azure-monitor/essentials/resource-logs.md)に `BackendConnectionFailure` のインスタンスとして現れます。

## <a name="cause"></a>原因

この現象のパターンは、多くの場合、APIM サービスでのネットワーク アドレス変換 (SNAT) ポートの制限が原因で発生します。

クライアントでいずれかの APIM API が呼び出されるたびに、Azure API Management サービスでは、バックエンド API にアクセスするために SNAT ポートを開きます。 [Azure の送信接続](../load-balancer/load-balancer-outbound-connections.md)に関するページで説明しているように、Azure では、ソース ネットワーク アドレス変換 (SNAT) と Load Balancer (お客様には公開していません) を使用して、パブリック IP アドレス空間内で Azure の外側にあるエンドポイントと通信し、[仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)を利用していない Azure 内部のエンドポイントとも通信します。 この状況は、パブリック IP で公開されているバックエンド API にのみ適用されます。

API Management サービスの各インスタンスには、事前に割り当てられた数の SNAT ポートが最初に提供されます。 この制限は、同じホストとポートの組み合わせへの接続を開くことに影響します。 SNAT ポートが使い果たされるのは、同じアドレスとポートの組み合わせへの呼び出しを繰り返したときです。 SNAT ポートが解放されると、そのポートは必要に応じて再利用できるようになります。 Azure ネットワーク ロード バランサーは、4 分間待機した後でないと、閉じられた接続の SNAT ポートを回収しません。

API に対するクライアント要求が立て続けに行われると、ポートが迅速に閉じられて再利用されない場合、SNAT ポートの事前に割り当てられたクォータが枯渇して、APIM サービスではクライアント要求を適時に処理できなくなる可能性があります。

## <a name="mitigations-and-solutions"></a>軽減策とソリューション

SNAT ポート枯渇の問題に対処するには、まずバックエンド サービスのパフォーマンスを診断して最適化する必要があります。

SNAT ポートの枯渇を軽減するための一般的な戦略については、*Azure Load Balancer* の [送信接続エラーのトラブルシューティング](../load-balancer/troubleshoot-outbound-connection.md)に関する記事で説明しています。 これらの戦略の中で、API Management には次が適用されます。

### <a name="scale-your-apim-instance"></a>APIM インスタンスをスケーリングする

各 API Management インスタンスには、APIM ユニットに基づいて複数の SNAT ポートが割り当てられます。 追加のユニットを使用して API Management インスタンスをスケーリングすることで、追加の SNAT ポートを割り当てることができます。 詳細については、[API Management サービスのスケーリング](upgrade-and-scale.md#scale-your-api-management-service)に関する記事を参照してください。

> [!NOTE]
> SNAT ポートの使用状況は、現在、自動スケーリング API Management ユニットのメトリックとして使用できません。

### <a name="use-multiple-ips-for-your-backend-urls"></a>バックエンド URL に複数の IP を使用する

APIM インスタンスからバックエンド サービスの同じ宛先 IP および宛先ポートへの各接続では、個別のトラフィック フローを維持するために SNAT ポートが使用されます。 バックグラウンド サービスからの戻りトラフィックに対して異なる SNAT ポートがない場合、APIM では、ある応答を別のものと区別できません。

宛先 IP または宛先ポートが異なれば SNAT ポートを再利用できるため、SNAT ポートの枯渇を回避するもう 1 つの方法は、バックエンド サービス URL に複数の IP を使用することです。

詳細については、[送信プロキシの Azure Load Balancer](../load-balancer/load-balancer-outbound-connections.md) に関する記事を参照してください。

### <a name="place-your-apim-and-backend-service-in-the-same-vnet"></a>APIM とバックエンド サービスを同じ VNet に配置する

バックエンド API が、App Service などの、"*サービス エンドポイント*" をサポートする Azure サービスでホストされている場合は、APIM インスタンスとバックエンド サービスを同じ仮想ネットワークに配置し、[サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)または [プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用して公開することにより、SNAT ポート枯渇の問題を回避できます。 共通の VNet を使用し、サービス エンドポイントを統合サブネットに配置すると、APIM インスタンスからこれらのサービスへの送信トラフィックがインターネットをバイパスするため、SNAT ポート制限が回避されます。 同様に、VNet とプライベート エンドポイントを使用する場合、その宛先には、送信 SNAT ポート問題が発生しません。

詳細については、[仮想ネットワークで Azure API Management を使用する方法](api-management-using-with-vnet.md)と [App Service と Azure 仮想ネットワークの統合](../app-service/web-sites-integrate-with-vnet.md)に関する記事を参照してください。

### <a name="place-your-apim-in-a-virtual-network-and-route-outbound-calls-to-azure-firewall"></a>APIM を仮想ネットワークに配置し、送信呼び出しを Azure Firewall にルーティングする

APIM とバックエンド サービスを仮想ネットワークに配置するのと同様に、APIM サービスと共に VNet 内で Azure Firewall を使用し、送信 APIM 呼び出しを Azure Firewall にルーティングできます。 (同じ VNet 内にある) APIM と Azure Firewall の間には、SNAT ポートは必要ありません。 バックエンド サービスへの SNAT 接続の場合、Azure Firewall には、APIM インスタンスに割り当てられている数よりもはるかに多い 64,000 個の使用可能ポートがあります。

詳細については、[Azure Firewall](../firewall/overview.md) に関するドキュメントを参照してください。

### <a name="consider-response-caching-and-other-backend-performance-tuning"></a>応答キャッシュとその他のバックエンド パフォーマンスのチューニングを検討する

検討すべきもう 1 つの軽減策としては、バックエンド API の処理時間の改善が挙げられます。 これを行う 1 つの方法は、応答キャッシュを使用して特定の API を構成して、API を呼び出すクライアント アプリケーションと APIM バックエンド読み込み間の待機時間を短縮することです。

詳細については、「[キャッシュを追加して Azure API Management のパフォーマンスを向上させる](api-management-howto-cache.md)」を参照してください。

### <a name="consider-implementing-access-restriction-policies"></a>アクセス制限ポリシーの実装を検討する

ビジネス シナリオに適している場合は、API Management 製品に対するアクセス制限ポリシーを実装できます。 たとえば、`rate-limit-by-key` ポリシーを使用すると、指定期間の呼び出しレート制限することで、キーごとに API 使用量の急増を防ぐことができます。

詳細については、[API Management のアクセス制限ポリシー](api-management-access-restriction-policies.md)に関するページを参照してください。

## <a name="see-also"></a>関連項目

* [Azure Load Balancer:送信接続エラーのトラブルシューティング](../load-balancer/troubleshoot-outbound-connection.md)
* [Azure App Service:断続的な送信接続エラーのトラブルシューティング](../app-service/troubleshoot-intermittent-outbound-connection-errors.md)

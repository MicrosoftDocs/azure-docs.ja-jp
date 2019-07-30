---
title: Azure Relay に関する FAQ | Microsoft Docs
description: Azure Relay についてよく寄せられる質問とその回答を紹介します。
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 886d2c7f-838f-4938-bd23-466662fb1c8e
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2018
ms.author: spelluru
ms.openlocfilehash: c6ea5e72f70620004b4b00be0c779893a3b2ad90
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706183"
---
# <a name="azure-relay-faqs"></a>Azure Relay に関する FAQ

この記事では、[Azure Relay](https://azure.microsoft.com/services/service-bus/) についてよく寄せられる質問とその回答を紹介します。 Azure の価格およびサポートに関する一般的な情報については、「[Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions"></a>一般的な質問
### <a name="what-is-azure-relay"></a>Azure Relay とは
ハイブリッド アプリケーションに [Azure Relay サービス](relay-what-is-it.md)を使用すると、企業のエンタープライズ ネットワーク内部にあるサービスをパブリック クラウドに安全に公開することができます。 サービスを公開するにあたって、ファイアウォール接続を開いたり、企業ネットワークのインフラストラクチャへの大幅な変更を要求したりする必要はありません。

### <a name="what-is-a-relay-namespace"></a>Relay 名前空間とは何ですか?
[名前空間](relay-create-namespace-portal.md)とは、アプリケーション内で Relay リソースをアドレス指定するために使用できるスコープ コンテナーです。 Relay を使用するには、名前空間を作成する必要があります。 これは、最初に実行する手順の 1 つです。

### <a name="what-happened-to-service-bus-relay-service"></a>Service Bus Relay サービスはどうなりましたか?
以前は Service Bus Relay サービスという名称でしたが、現在では [WCF リレー](relay-wcf-dotnet-get-started.md)と呼ばれています。 このサービスは、これまでと同じように引き続き使用できます。 ハイブリッド接続機能は、Azure BizTalk Services から移植されたサービスの更新バージョンです。 WCF リレーとハイブリッド接続はどちらもサポートが継続されます。

## <a name="pricing"></a>価格
このセクションでは、Relay の価格体系についてよく寄せられる質問とその回答を紹介します。 Azure の価格に関する一般的な情報については、「[Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」も参照してください。 Relay の価格の詳細については、 [Service Bus の価格の詳細][Pricing overview]に関するページを参照してください。

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>ハイブリッド接続と WCF リレーの課金方法を教えてください。
Relay の価格の詳細については、Service Bus の価格の詳細ページで、[ハイブリッド接続と WCF リレー][Pricing overview]の表をご覧ください。 このページで説明されている価格に加え、ご利用のアプリケーションがプロビジョニングされているデータ センターから外部に送信される関連データ転送にも料金が発生します。

### <a name="how-am-i-billed-for-hybrid-connections"></a>ハイブリッド接続はどのように課金されますか?
ここでは、ハイブリッド接続での課金シナリオの例を 3 つ紹介します。

*   シナリオ 1:
    *   ハイブリッド接続マネージャーのインスタンスなどのリスナーを 1 つインストールし、1 か月間継続的に稼働させます。
    *   その接続を経由して、1 か月で 3 GB のデータを送信します。 
    *   合計料金は 5 ドルです。
*   シナリオ 2:
    *   ハイブリッド接続マネージャーのインスタンスなどのリスナーを 1 つインストールし、1 か月間継続的に稼働させます。
    *   その接続を経由して、1 か月で 10 GB のデータを送信します。
    *   合計料金は 7.50 ドルです。 内訳は、接続および最初の 5 GB に 5 ドル、追加のデータ 5 GB に 2.50 ドルです。
*   シナリオ 3:
    *   ハイブリッド接続マネージャーの A と B という 2 つのインスタンスをインストールし、1 か月間継続的に稼働させます。
    *   接続 A を経由して、1 か月で 3 GB のデータを送信します。
    *   接続 B を経由して、1 か月で 6 GB のデータを送信します。
    *   合計料金は 10.50 ドルです。 内訳は、接続 A に 5 ドル、接続 B に 5 ドル、接続 B の 6 GB 目の料金として 0.50 ドルです。

サンプルで使用された料金はハイブリッド接続のプレビュー期間にのみ適用されるものですので、ご注意ください。 価格はハイブリッド接続の一般公開時に変更されます。

### <a name="how-are-hours-calculated-for-relay"></a>Relay では、時間はどのように計算されますか?

WCF リレーは、Standard レベルの名前空間でのみ使用できます。 それ以外では、リレーの価格と[接続クォータ](../service-bus-messaging/service-bus-quotas.md)は変更されていません。 つまり、リレーは、引き続き (操作ではなく) メッセージの数およびリレー時間に基づいて課金されます。 詳細については、価格の詳細に関するページで ["ハイブリッド接続と WCF Relay"](https://azure.microsoft.com/pricing/details/service-bus/) の表をご覧ください。

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>特定のリレーに複数のリスナーを接続した場合はどうなりますか?
場合によっては、1 つのリレーに複数のリスナーが接続されていることも考えられます。 リレーは、接続されているリレー リスナーが 1 つでもあれば、開いていると見なされます。 開いているリレーにリスナーを追加すると、追加のリレー時間が発生します。 また、リレーに接続されたリレー センダー (リレーに対するメッセージの呼び出しまたは送信を実行するクライアント) の数は、リレー時間の計算には影響しません。

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>WCF Relay の場合、メッセージ数のメーターはどのようにして計算されますか?
(**これは、WCF リレーにのみ該当します。メッセージはハイブリッド接続では課金されません。** )

一般に、リレーの場合も、前述した仲介型エンティティ (キュー、トピック、サブスクリプション) と同じ方法で、課金対象のメッセージが計算されます。 ただし、注目すべき違いがいくつかあります。

Service Bus リレーへのメッセージ送信は、そのメッセージを受信するリレー リスナーに対する "直接の" 送信として扱われます。 リレー リスナーに配信する前に行われる Service Bus Relay への送信操作のようには扱われません。 リレー リスナーに対する (最大 64 KB の) 要求/応答形式のサービス呼び出しの場合、課金対象のメッセージは、要求の課金対象メッセージと応答の課金対象メッセージ (応答も 64 KB 以下を想定) の 2 つになります。 これは、クライアントとサービスの間の仲介にキューを使用した場合とは異なります。 クライアントとサービスの間の仲介にキューを使用する場合は、同じ要求/応答パターンでも、キューに対する要求の送信、そのキューからサービスへのデキュー/配信の順番で実行する必要があります。 この後さらに、別のキューへの応答の送信、そのキューからクライアントへのデキュー/配信の順番で実行する必要があります。 メッセージのサイズが最初から最後まで同じ (64 KB 以下) であると仮定した場合、仲介型キュー パターンでは課金対象のメッセージは 4 つとなります。 Relay を使って同じパターンを実装した場合と比べてメッセージ数が 2 倍となり、それに対する料金が課金されることになります。 もちろん、キューを使用してこのパターンを実現すれば、持続性や負荷平準化などの利点が生まれます。 これらは追加費用に見合う可能性のある利点です。

**netTCPRelay** WCF バインドを使って開いたリレーでは、メッセージは個別のメッセージではなく、システムを流れるデータ ストリームとして扱われます。 このバインドを使用すると、センダーとリスナーだけが、送受信された個々のメッセージを 1 つのまとまりとして認識できます。 **netTCPRelay** バインドを使ったリレーの場合、課金対象のメッセージ数を計算するために、すべてのデータがストリームとして扱われます。 この場合、Service Bus は、個々のリレーを介して送受信されたデータ量の合計を 5 分ごとに計算します。 次に、データ量の合計を 64 KB で除算して、その期間内でのリレーについて、課金対象のメッセージ数を決定します。

## <a name="quotas"></a>クォータ
| クォータ名 | Scope (スコープ) |  メモ | 値 |
| --- | --- | --- | --- |
| リレーの同時リスナー |エンティティ |追加の接続に関する後続の要求は拒否され、呼び出し元のコードが例外を受け取ります。 |25 |
| あるサービス名前空間に含まれるリレー エンドポイント全部の同時リレー接続 |名前空間 |- |5,000 |
| サービス名前空間ごとのリレー エンドポイント |名前空間 |- |10,000 |
| [NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) と [NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) リレーのメッセージ サイズ |名前空間 |これらのクォータを超える受信メッセージは拒否され、呼び出し元のコードが例外を受け取ります。 |64 KB |
| [HttpRelayTransportBindingElement](/dotnet/api/microsoft.servicebus.httprelaytransportbindingelement) と [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) リレーのメッセージ サイズ |名前空間 |メッセージ サイズに制限はありません。 |無制限 |

### <a name="does-relay-have-any-usage-quotas"></a>Relay に使用量クォータはありますか?
既定では、Microsoft はすべてのクラウド サービスに関し、お客様のサブスクリプション全体で算出する月単位の総使用量クォータを設定しています。 Microsoft では、実際のニーズがこれらの制限を上回る可能性があることを認識しています。 お気軽にカスタマー サービスまでお問い合わせください。お客様のニーズを確認のうえ、適宜制限を調整させていただきます。 Service Bus の総使用量クォータは次のとおりです。

* 50 億メッセージ
* 200 万リレー時間

Microsoft は、月ごとの使用量クォータを超えた場合にお客様のアカウントを無効にする権利を保有しています。ただし、その場合は電子メールでお客様にその旨をお知らせし、何度かお客様に連絡を試みたうえで、措置を講じることといたします。 そのクォータを超えたお客様についても、超過分は課金の対象となります。

### <a name="naming-restrictions"></a>名前に関する制限
Relay 名前空間名の長さは 6 ～ 50 文字である必要があります。

## <a name="subscription-and-namespace-management"></a>サブスクリプションと名前空間の管理
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>別の Azure サブスクリプションに名前空間を移行する方法を教えてください

ある Azure サブスクリプションから別のサブスクリプションに名前空間を移行する場合は、[Azure Portal](https://portal.azure.com) または PowerShell コマンドを使用することができます。 名前空間を別のサブスクリプションに移行するには、名前空間が既にアクティブになっている必要があります。 コマンドを実行するユーザーは、ソースとターゲットの両方のサブスクリプションの管理者ユーザーである必要があります。

#### <a name="azure-portal"></a>Azure ポータル

Azure Portal を使用して、あるサブスクリプションから別のサブスクリプションに Azure Relay 名前空間を移行する方法については、[新しいリソース グループまたはサブスクリプションへのリソースの移動](../azure-resource-manager/resource-group-move-resources.md#use-the-portal)に関する記事を参照してください。 

#### <a name="powershell"></a>PowerShell

PowerShell を使用して、ある Azure サブスクリプションから別のサブスクリプションに名前空間を移行するには、次の一連のコマンドを使用します。 この操作を実行するには、名前空間が既にアクティブになっており、PowerShell コマンドを実行するユーザーが、ソースとターゲットの両方のサブスクリプションの管理者である必要があります。

```azurepowershell-interactive
# Create a new resource group in the target subscription.
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move the namespace from the source subscription to the target subscription.
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="troubleshooting"></a>トラブルシューティング
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Azure Relay API によって生成されるいくつかの例外と、それを解決するための推奨されるアクションを教えてください。
一般的な例外と、それを解決するための推奨されるアクションの説明については、[Relay の例外][Relay exceptions]に関する記事を参照してください。

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>Shared Access Signature とは何ですか? また、どの言語で署名を生成できますか?
Shared Access Signature (SAS) は、SHA-256 セキュア ハッシュまたは URI に基づいた認証メカニズムです。 Node、PHP、Java、C、C# で独自の署名を生成する方法については、「[Shared Access Signature による Service Bus の認証][Shared Access Signatures]」を参照してください。

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>Relay エンドポイントをホワイトリストに登録することはできますか?
はい。 リレー クライアントは、完全修飾ドメイン名を使用して Azure Relay サービスへの接続を確立します。 お客様は、DNS ホワイトリスト登録をサポートするファイアウォールで、`*.servicebus.windows.net` のエントリを追加できます。

## <a name="next-steps"></a>次の手順
* [名前空間を作成する](relay-create-namespace-portal.md)
* [.NET を使って作業を開始する](relay-hybrid-connections-dotnet-get-started.md)
* [Node を使って作業を開始する](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas.md

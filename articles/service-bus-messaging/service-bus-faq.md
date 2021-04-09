---
title: Azure Service Bus のよく寄せられる質問 (FAQ) | Microsoft Docs
description: この記事では、Azure Service Bus に関連する、よく寄せられる質問 (FAQ) の一部の回答を示します。
ms.topic: article
ms.date: 01/20/2021
ms.openlocfilehash: 3a96cf94ca4a7edd115f12b3e2eded11a5894e04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98693404"
---
# <a name="azure-service-bus---frequently-asked-questions-faq"></a>Azure Service Bus - よく寄せられる質問 (FAQ)

この記事では、Microsoft Azure Service Bus についてよく寄せられる質問 (FAQ) とその回答について説明します。 Azure の価格およびサポートに関する一般的な情報については、「[Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」も参照してください。


## <a name="general-questions-about-azure-service-bus"></a>Azure Service Bus に関する一般的な質問
### <a name="what-is-azure-service-bus"></a>Azure Service Bus とは
[Azure Service Bus](service-bus-messaging-overview.md) は、分離されたシステム間でデータを送信できるようにする非同期メッセージング クラウド プラットフォームです。 Microsoft では、この機能がサービスとして提供されています。つまり、この機能を使用するために独自にハードウェアをホストする必要はありません。

### <a name="what-is-a-service-bus-namespace"></a>Service Bus 名前空間とは何ですか。
[名前空間](service-bus-create-namespace-portal.md)は、アプリケーション内で Service Bus リソースをアドレス指定するためのスコープ コンテナーを提供します。 Service Bus を使用するには名前空間を作成する必要があります。これは最初に実行する手順の 1 つです。

### <a name="what-is-an-azure-service-bus-queue"></a>Azure Service Bus キューとは何ですか。
[Service Bus キュー](service-bus-queues-topics-subscriptions.md)は、メッセージが格納されるエンティティです。 キューは、複数のアプリケーションがある場合や、互いにやり取りする必要がある分散アプリケーションの複数の部分がある場合に便利です。 キューは、複数の製品 (メッセージ) の受け取り (受信) と発送 (送信) が行われる点で、集配送センターに似ています。

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Service Bus トピックおよびサブスクリプションとは何ですか。
トピックは、キューとして視覚化できます。複数のサブスクリプションを使用している場合、トピックはより多機能なメッセージング モデルになります (基本的には一対多通信ツール)。 この発行/サブスクライブ モデル (または *pub/sub*) により、複数のサブスクリプションを持つトピックにメッセージを送信するアプリケーションで、そのメッセージが複数のアプリケーションによって受信されるように設定できます。

### <a name="what-is-a-partitioned-entity"></a>パーティション分割されたエンティティとは何ですか。
従来のキューまたはトピックは、単一のメッセージ ブローカーで処理されて 1 つのメッセージング ストアに格納されます。 Basic および Standard のメッセージング プランのみでサポートされている[パーティション分割されたキューまたはトピック](service-bus-partitioning.md)は、複数のメッセージ ブローカーによって処理され、複数のメッセージング ストアに格納されます。 この機能により、パーティション分割されたキューまたはトピックの全体のスループットは、単一のメッセージ ブローカーまたはメッセージング ストアのパフォーマンスによって制限されなくなりました。 また、1 つのメッセージング ストアが一時的に停止しても、パーティション分割されたキューまたはトピックは使用することができます。

パーティション分割されたエンティティを使用する場合、順序が保証されません。 あるパーティションが使用できなくなった場合は、他のパーティションからメッセージの送受信を行うことができます。

 パーティション分割されたエンティティは [Premium SKU](service-bus-premium-messaging.md) ではサポートされなくなりました。 

### <a name="where-does-azure-service-bus-store-data"></a><a name="in-region-data-residency"></a>Azure Service Bus ではデータはどこに格納されますか。
Azure Service Bus の Standard レベルでは、Azure SQL Database をそのバックエンド ストレージ レイヤーに利用します。 ブラジル南部と東南アジアを除くすべてのリージョンでは、データベース バックアップは別のリージョン (通常は Azure ペア リージョン) でホストされます。 ブラジル南部および東南アジア リージョンでは、データベース バックアップは、これらのリージョンのデータ所在地の要件に対応するために同じリージョンに格納されます。

Azure Service Bus の Premium レベルでは、選択されたリージョンにメタデータとデータが格納されます。 Azure Service Bus の Premium 名前空間に対して geo ディザスター リカバリーを設定すると、メタデータは、選択されたセカンダリ リージョンにコピーされます。


### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>ファイアウォールで開く必要があるのはどのポートですか。 
Azure Service Bus でメッセージを送受信する場合、次のプロトコルを使用できます。

- Advanced Message Queuing Protocol 1.0 (AMQP)
- TLS を使用したハイパーテキスト転送プロトコル 1.1 (HTTPS)

これらのプロトコルを使用して Azure Service Bus と通信するために開く必要がある送信 TCP ポートについては、次の表を参照してください。

| Protocol | ポート | 詳細 | 
| -------- | ----- | ------- | 
| AMQP | 5671 | TLS を使用した AMQP。 [AMQP プロトコル ガイド](service-bus-amqp-protocol-guide.md)に関するページを参照してください | 
| HTTPS | 443 | このポートは、HTTP/REST API と AMQP (WebSocket 経由) で使用されます。 |

通常、HTTPS ポートは、ポート 5671 で AMQP が使用されている場合に送信通信にも必要です。これは、クライアント SDK によって実行されるいくつかの管理操作と Azure Active Directory からのトークンの取得 (使用する場合) が HTTPS 経由で実行されるためです。 

公式の Azure SDK では、通常、Service Bus に対するメッセージの送受信で AMQP プロトコルが使用されます。 

[!INCLUDE [service-bus-websockets-options](../../includes/service-bus-websockets-options.md)]

.NET Framework の以前の WindowsAzure.ServiceBus パッケージには、従来の "Service Bus Messaging Protocol" (SBMP) を使用するオプションがあります。これは "NetMessaging" とも呼ばれます。 このプロトコルでは、TCP ポート 9350 から 9354 が使用されます。 このパッケージの既定のモードでは、これらのポートが通信に使用できるかどうかが自動的に検出されます。そうでない場合は、ポート 443 経由での TLS を使用した WebSocket に切り替えられます。 この設定をオーバーライドきして、このモードを強制するには、[`ServiceBusEnvironment.SystemConnectivity`](/dotnet/api/microsoft.servicebus.servicebusenvironment.systemconnectivity) 設定に `Https` [CConnectivityMode](/dotnet/api/microsoft.servicebus.connectivitymode) を設定します。これは、アプリケーションにグローバルに適用されます。

### <a name="what-ip-addresses-do-i-need-to-add-to-allow-list"></a>どのような IP アドレスを許可リストに追加する必要がありますか。
接続の許可リストに追加する適切な IP アドレスを検索するには、次の手順を実行します。

1. コマンド プロンプトで、次のコマンドを実行します。 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. `Non-authoritative answer` で返された IP アドレスをメモします。 

名前空間に **ゾーン冗長性** を使用している場合は、次の追加手順を実行する必要があります。 

1. まず、名前空間に対して nslookup を実行します。

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. **non-authoritative answer** セクションの名前をメモします。これは、次のいずれかの形式になります。 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. s1、s2、s3 のサフィックスが付いているそれぞれについて nslookup を実行し、3 つの可用性ゾーンで実行されている 3 つのインスタンスすべての IP アドレスを取得します。 

    > [!NOTE]
    > `nslookup` コマンドによって返された IP アドレスは、静的 IP アドレスではありません。 ただし、基になるデプロイが削除されるか別のクラスターに移動されるまでは変わりません。

### <a name="where-can-i-find-the-ip-address-of-the-client-sendingreceiving-messages-tofrom-a-namespace"></a>名前空間との間でメッセージを送受信するクライアントの IP アドレスはどこで確認できますか。 
名前空間との間でメッセージを送受信しているクライアントの IP アドレスはログに記録されません。 すべての既存クライアントの認証が失敗するようにキーを再生成し、Azure ロールベースのアクセス制御 ([Azure RBAC](authenticate-application.md#azure-built-in-roles-for-azure-service-bus)) の設定を確認して、許可されているユーザーまたはアプリケーションのみが名前空間にアクセスできることを確認します。 

**Premium** 名前空間を使用している場合は、名前空間へのアクセスを制限するには、[IP フィルタリング](service-bus-ip-filtering.md)、[仮想ネットワーク サービス エンドポイント](service-bus-service-endpoints.md)、[プライベート エンドポイント](private-link-service.md)を使用します。 

## <a name="best-practices"></a>ベスト プラクティス
### <a name="what-are-some-azure-service-bus-best-practices"></a>Azure Service Bus のベスト プラクティスを教えてください。
「[Service Bus メッセージングを使用したパフォーマンス向上のためのベスト プラクティス][Best practices for performance improvements using Service Bus]」を参照してください。この記事では、メッセージを交換するときのパフォーマンスを最適化する方法について説明します。

### <a name="what-should-i-know-before-creating-entities"></a>エンティティを作成する前に知っておく必要があることは何ですか。
キューとトピックの次のプロパティは変更できません。 エンティティをプロビジョニングするときはこの制限を考慮してください。代替の新しいエンティティを作成しない限り、これらのプロパティは変更できません。

* パーティション分割
* セッション
* 重複検出
* Express エンティティ

## <a name="pricing"></a>価格
このセクションでは、Service Bus の価格体系についてよく寄せられる質問 (FAQ) とその回答を紹介します。

「[Service Bus の料金と課金](https://azure.microsoft.com/pricing/details/service-bus/)」の記事では、Service Bus の課金メーターについて説明しています。 Service Bus の価格オプションの特定の情報については、[Service Bus の価格の詳細](https://azure.microsoft.com/pricing/details/service-bus/)に関するページをご覧ください。

Azure の全般的な価格情報については、[Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/) も参照してください。 

### <a name="how-do-you-charge-for-service-bus"></a>Service Bus の課金方法を教えてください
Service Bus の価格の詳細については、 [Service Bus の価格の詳細][Pricing overview]に関するページを参照してください。 既に説明した価格に加え、ご利用のアプリケーションがプロビジョニングされているデータ センターから外部に送信される関連データ転送に料金が発生します。

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-isnt"></a>Service Bus でデータ転送の対象となる用途と 対象外の用途を教えてください。
特定の Azure リージョン内でのデータ転送および受信データ転送はすべて無料です。 リージョン外でのデータ転送は、送信料金の対象になります。[こちら](https://azure.microsoft.com/pricing/details/bandwidth/)をご覧ください。

### <a name="does-service-bus-charge-for-storage"></a>Service Bus ではストレージに対して課金されますか
いいえ。 Service Bus ではストレージに対して課金されません。 ただし、キューまたはトピックごとに保持できる最大データ量を制限するクォータはあります。 次の FAQ を参照してください。

### <a name="i-have-a-service-bus-standard-namespace-why-do-i-see-charges-under-resource-group-system"></a>Service Bus Standard 名前空間があります。 リソースグループ ' $system ' に料金がされるのはなぜですか?
Azure Service Bus は最近、課金コンポーネントをアップグレードしました。 この変更のため、Service Bus Standard 名前空間がある場合は、リソース グループ "$system" の下に、リソース "/subscriptions/<azure_subscription_id>/resourceGroups/$system/providers/Microsoft.ServiceBus/namespaces/$system" の明細項目が表示されることがあります。

これらの料金は、Service Bus Standard 名前空間をプロビジョニングした Azure サブスクリプションごとの基本料金を表します。 

これらの料金は新しいものではなく、以前の課金モデルにも存在していたことに、注意する必要があります。 唯一の変更は、"$system" の下に一覧表示されるようになったことです。 これは、新しい課金システムの制約によって行われています。この制約により、特定のリソースに関連付けられていないサブスクリプション レベルの料金が "$system" リソース ID でグループ化されます。

## <a name="quotas"></a>Quotas (クォータ)

Service Bus の制限とクォータの一覧については、[Service Bus のクォータの概要][Quotas overview]に関するページを参照してください。

### <a name="how-to-handle-messages-of-size--1-mb"></a>1 MB を超えるサイズのメッセージを処理する方法を教えてください
Service Bus メッセージング サービス (キューおよびトピック/サブスクリプション) では、アプリケーションは、最大 256 KB (Standard レベル) または 1 MB (Premium レベル) のサイズのメッセージを送信できます。 1 MB を超えるサイズのメッセージを扱う場合は、[このブログの投稿](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern)で説明されている要求チェック パターンを使用します。

## <a name="troubleshooting"></a>トラブルシューティング
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>別のサブスクリプションから名前空間を削除した後に、その名前空間を作成できないのはなぜですか。 
サブスクリプションから名前空間を削除した場合、別のサブスクリプションで同じ名前を使用して再作成するには、4 時間ほど時間を空けてから行ってください。 そうしないと、`Namespace already exists` というエラー メッセージが表示される場合があります。 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Azure Service Bus API によって生成される例外とその推奨される操作をいくつか教えてください。
Service Bus で可能性のある例外の一覧については、「[例外の概要][Exceptions overview]」をご覧ください。

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Shared Access Signature とは何ですか。どの言語で署名の生成がサポートされますか。
Shared Access Signature は、SHA-256 セキュア ハッシュまたは URI に基づいた認証メカニズムです。 Node.js、PHP、Java、Python、C# で独自の署名を生成する方法については、[Shared Access Signature][Shared Access Signatures] に関する記事を参照してください。

## <a name="subscription-and-namespace-management"></a>サブスクリプションと名前空間の管理
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>別の Azure サブスクリプションに名前空間を移行する方法を教えてください

[Azure Portal](https://portal.azure.com) または PowerShell コマンドを使用して、ある Azure サブスクリプションから別のサブスクリプションに名前空間を移行できます。 操作を実行するには、名前空間が既にアクティブになっている必要があります。 コマンドを実行するユーザーは、ソースとターゲットの両方のサブスクリプションの管理者である必要があります。

#### <a name="portal"></a>ポータル

Azure Portal を使用して Service Bus 名前空間を別のサブスクリプションに移行するには、[こちら](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal)の説明に従ってください。 

#### <a name="powershell"></a>PowerShell

次の一連の PowerShell コマンドでは、別の Azure サブスクリプションへ名前空間を移行します。 この操作を実行するには、名前空間がアクティブになっており、PowerShell コマンドを実行するユーザーが、ソースとターゲットのサブスクリプションの管理者である必要があります。

```powershell
# Create a new resource group in target subscription
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```
## <a name="is-it-possible-to-disable-tls-10-or-11-on-service-bus-namespaces"></a>Service Bus 名前空間で TLS 1.0 または 1.1 を無効にすることはできますか。
いいえ。 Service Bus 名前空間で TLS 1.0 または 1.1 を無効にすることはできません。 Service Bus に接続するクライアント アプリケーションでは、TLS 1.2 以降を使用してください。 詳細については、「[Azure Service Bus での TLS 1.2 の使用の強制 - Microsoft Tech Community](https://techcommunity.microsoft.com/t5/messaging-on-azure/enforcing-tls-1-2-use-with-azure-service-bus/ba-p/370912)」を参照してください。

## <a name="next-steps"></a>次のステップ
Service Bus の詳細については、次の記事をご覧ください。

* [Azure Service Bus Premium の概要 (ブログの投稿)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Azure Service Bus Premium の概要 (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Service Bus の概要](service-bus-messaging-overview.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md

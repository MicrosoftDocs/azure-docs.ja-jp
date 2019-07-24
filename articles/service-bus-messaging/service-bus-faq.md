---
title: Azure Service Bus のよく寄せられる質問 (FAQ) | Microsoft Docs
description: Azure Service Bus についてよく寄せられる質問 (FAQ) とその回答を紹介します。
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 41a5f08be833d1235146d6e748580751af2c9d73
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046089"
---
# <a name="service-bus-faq"></a>Service Bus に関する FAQ

この記事では、Microsoft Azure Service Bus についてよく寄せられる質問 (FAQ) とその回答について説明します。 Azure の価格およびサポートに関する一般的な情報については、「[Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」も参照してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions-about-azure-service-bus"></a>Azure Service Bus に関する一般的な質問
### <a name="what-is-azure-service-bus"></a>Azure Service Bus とは
[Azure Service Bus](service-bus-messaging-overview.md) は、分離されたシステム間でデータを送信できるようにする非同期メッセージング クラウド プラットフォームです。 Microsoft では、この機能をサービスとして提供しています。つまり、この機能を使用するために独自にハードウェアをホストする必要はありません。

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

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>Service Bus でデータ転送の対象となる用途と 対象外の用途を教えてください
特定の Azure リージョン内でのデータ転送および受信データ転送はすべて無料です。 リージョン外でのデータ転送は、送信料金の対象になります。[こちら](https://azure.microsoft.com/pricing/details/bandwidth/)をご覧ください。

### <a name="does-service-bus-charge-for-storage"></a>Service Bus ではストレージに対して課金されますか
いいえ、Service Bus ではストレージに対して課金されません。 ただし、キューまたはトピックごとに保持できる最大データ量を制限するクォータはあります。 次の FAQ を参照してください。

## <a name="quotas"></a>Quotas (クォータ)

Service Bus の制限とクォータの一覧については、[Service Bus のクォータの概要][Quotas overview]に関するページを参照してください。

### <a name="does-service-bus-have-any-usage-quotas"></a>Service Bus に使用量クォータはありますか
マイクロソフトは、既定で、任意のクラウド サービスに関して、お客様の全サブスクリプション全体で算出される月単位の総使用量クォータを設定しています。 上限を超える量が必要な場合は、カスタマー サービスまでお問い合わせください。お客様に必要な使用量を確認したうえで、これらの上限を適切に調整させていただきます。 Service Bus の場合、総使用量クォータは、1 か月あたり 50 億メッセージです。

マイクロソフトは、ある月に使用量クォータを超えた場合に、お客様のアカウントを無効にする権利を保有しています。その場合は電子メールでお客様にその旨をお知らせし、何度かお客様に連絡を試みたうえで、措置を講じることといたします。 そのクォータを超えたお客様についても、その超過分は課金の対象となります。

Azure の他のサービスと同様、Service Bus では、リソースが公平に使用されるように一連のクォータを適用します。 これらのクォータの詳細については、「[Service Bus のクォータ][Quotas overview]」を参照してください。

### <a name="how-to-handle-messages-of-size--1-mb"></a>1 MB を超えるサイズのメッセージを処理する方法を教えてください
Service Bus メッセージング サービス (キューおよびトピック/サブスクリプション) では、アプリケーションは、最大 256 KB (Standard レベル) または 1 MB (Premium レベル) のサイズのメッセージを送信できます。 1 MB を超えるサイズのメッセージを扱う場合は、[このブログの投稿](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern)で説明されている要求チェック パターンを使用します。

## <a name="troubleshooting"></a>トラブルシューティング
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>別のサブスクリプションから名前空間を削除した後に、その名前空間を作成できないのはなぜですか。 
サブスクリプションから名前空間を削除した場合、別のサブスクリプションで同じ名前を使用して再作成するには、4 時間ほど時間を空けてから行ってください。 そうしないと、`Namespace already exists` というエラー メッセージが表示される場合があります。 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Azure Service Bus API によって生成される例外とその推奨される操作をいくつか教えてください。
Service Bus で可能性のある例外の一覧については、「[例外の概要][Exceptions overview]」をご覧ください。

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Shared Access Signature とは何ですか。どの言語で署名の生成がサポートされますか。
Shared Access Signature は、SHA-256 セキュア ハッシュまたは URI に基づいた認証メカニズムです。 Node.js、PHP、Java、および C\# で独自の署名を生成する方法については、[Shared Access Signature][Shared Access Signatures] に関する記事を参照してください。

## <a name="subscription-and-namespace-management"></a>サブスクリプションと名前空間の管理
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>別の Azure サブスクリプションに名前空間を移行する方法を教えてください

[Azure Portal](https://portal.azure.com) または PowerShell コマンドを使用して、ある Azure サブスクリプションから別のサブスクリプションに名前空間を移行できます。 操作を実行するには、名前空間が既にアクティブになっている必要があります。 コマンドを実行するユーザーは、ソースとターゲットの両方のサブスクリプションの管理者である必要があります。

#### <a name="portal"></a>ポータル

Azure Portal を使用して Service Bus 名前空間を別のサブスクリプションに移行するには、[こちら](../azure-resource-manager/resource-group-move-resources.md#use-portal)の説明に従ってください。 

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

## <a name="next-steps"></a>次の手順
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

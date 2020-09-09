---
title: Azure Service Bus メッセージングの概要 | Microsoft Docs
description: この記事では、フル マネージド エンタープライズ統合メッセージ ブローカーである Azure Service Bus の概要を簡単に説明します。
ms.topic: overview
ms.date: 06/23/2020
ms.openlocfilehash: 478dd0debb5117e76cf8d0ab6599dcf363c12ab3
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87501476"
---
# <a name="what-is-azure-service-bus"></a>Azure Service Bus とは

Microsoft Azure Service Bus は、完全なマネージド エンタープライズ統合メッセージ ブローカーです。 Service Bus は、アプリケーションとサービスを分離できます。 Service Bus は、データと状態の非同期転送のための信頼性が高く安全なプラットフォームを提供します。

データは、*メッセージ*を使用してさまざまなアプリとサービス間で転送されます。 メッセージはバイナリ形式であり、JSON、XML、または単なるテキストを含むことができます。 詳細については、「[Integration Services](https://azure.com/integration)」を参照してください。

一般的なメッセージング シナリオの例を次にいくつか示します。

* *メッセージング*。 販売または購入の注文、仕訳帳、在庫移動などのビジネス データを転送します。
* *アプリケーションの切り離し*。 アプリケーションとサービスの信頼性とスケーラビリティを向上します。 クライアントとサービスが同時にオンラインになっている必要はありません。
* *トピックとサブスクリプション*。 公開元とサブスクライバーの間で 1:*n* の関係が可能になります。
* *メッセージ セッション*。 メッセージの順序付けやメッセージの遅延が必要なワークフローを実装します。

## <a name="namespaces"></a>名前空間

名前空間は、すべてのメッセージング コンポーネントのコンテナーです。 複数のキューとトピックを 1 つの名前空間に格納できます。多くの場合、名前空間はアプリケーション コンテナーとして機能します。

## <a name="queues"></a>キュー

メッセージは*キュー*に送受信されます。 受信側アプリがメッセージを受信して処理できるようになるまで、キューがメッセージを格納します。

![キュー](./media/service-bus-messaging-overview/about-service-bus-queue.png)

キュー内のメッセージは到着順に並べ替えられ、タイムスタンプが付けられます。 メッセージは、受理されると、冗長ストレージに安全に保管されます。 メッセージは*プル* モードで配信され、要求された場合のみメッセージが配信されます。

## <a name="topics"></a>トピック

*トピック*を使用してメッセージを送受信することもできます。 キューはポイント間通信によく使用されますが、トピックは公開/サブスクライブのシナリオで役立ちます。

![トピック](./media/service-bus-messaging-overview/about-service-bus-topic.png)

トピックは、複数の独立したサブスクリプションを持つことができます。 トピックのサブスクライバーは、そのトピックに送信された各メッセージのコピーを受信できます。 サブスクリプションは名前付きエンティティです。 サブスクリプションは永続的ですが、有効期限が切れたり自動的に削除されたりすることがあります。

トピックに送信されたすべてのメッセージを個々のサブスクリプションに受信させたくない場合があります。 そのような場合は、*ルール*と*フィルター*を使用して、オプションの*アクション*をトリガーする条件を定義できます。 指定したメッセージをフィルター処理したり、メッセージのプロパティを設定または変更したりできます。 詳細については、「[トピック フィルターとアクション](topic-filters.md)」を参照してください。

## <a name="advanced-features"></a>高度な機能

Service Bus には、より複雑なメッセージングの問題を解決できる高度な機能があります。 次のセクションでは、これらの機能のいくつかを説明します。

### <a name="message-sessions"></a>メッセージ セッション

Service Bus の先入れ先出し (FIFO) 処理を作成するには、セッションを使用します。 メッセージ セッションでは、関連メッセージのバインドなしシーケンスの結合および順序指定処理が可能です。 詳細については、「[メッセージ セッション: 先入れ先出し (FIFO)](message-sessions.md)」を参照してください。

### <a name="autoforwarding"></a>自動転送

自動転送機能は、キューまたはサブスクリプションを別のキューまたはトピックにチェーンします。 これらは同じ名前空間に属している必要があります。 自動転送を使用すると、Service Bus はキューまたはサブスクリプションからメッセージを自動的に削除し、別のキューまたはトピックに格納します。 詳細については、「[自動転送を使用した Service Bus エンティティのチェーン](service-bus-auto-forwarding.md)」を参照してください。

### <a name="dead-letter-queue"></a>配信不能キュー

Service Bus は配信不能キュー (DLQ) をサポートしています。 DLQ は、受信者に配信できないメッセージを保持します。 これは処理できないメッセージを保持します。 Service Bus を使用すると、DLQ のメッセージを削除し、検査することができます。 詳しくは、「[Service Bus の配信不能キューの概要](service-bus-dead-letter-queues.md)」をご覧ください。

### <a name="scheduled-delivery"></a>スケジュールされた配信

メッセージをキューまたはトピックに送信して、処理を遅延させることができます。 特定の時点にシステムで処理できるようにジョブをスケジュールすることができます。 詳細については、「[スケジュールされたメッセージ](message-sequencing.md#scheduled-messages)」を参照してください。

### <a name="message-deferral"></a>メッセージ遅延

キューまたはサブスクリプション クライアントは、メッセージの取得を遅延させることができます。 この遅延は、アプリケーションの特殊な状況によって発生する場合があります。 メッセージは、キューまたはサブスクリプションに留まりますが、確保されます。 詳細については、「[メッセージの遅延](message-deferral.md)」を参照してください。

### <a name="batching"></a>バッチ処理

クライアント側のバッチ処理により、キューまたはトピックのクライアントはメッセージの送信を一定期間遅らせることができます。 クライアントがこの期間内に追加のメッセージを送信すると、1 つのバッチで複数のメッセージが送信されます。 詳細については、「[クライアント側のバッチ処理](service-bus-performance-improvements.md#client-side-batching)」を参照してください。

### <a name="transactions"></a>トランザクション

トランザクションにより、複数の操作が 1 つの*実行スコープ*にグループ化されます。 Service Bus は、単一トランザクションのスコープ内の単一メッセージング エンティティに対するグループ化操作をサポートしています。 メッセージ エンティティは、キュー、トピック、またはサブスクリプションとすることができます。 詳しくは、「[Service Bus のトランザクション処理の概要](service-bus-transactions.md)」を参照してください。

### <a name="filtering-and-actions"></a>フィルター処理とアクション

サブスクライバーは、トピックから受信するメッセージを定義できます。 これらのメッセージは、1 つ以上の名前付きのサブスクリプション ルールの形式で指定されます。 サブスクリプションは、対応するルールの条件ごとに、対応する各ルールに異なる注釈を付けることができる、メッセージのコピーを作成します。 詳細については、「[トピック フィルターとアクション](topic-filters.md)」を参照してください。

### <a name="autodelete-on-idle"></a>アイドル状態時の自動削除

アイドル状態時の自動削除機能を使用すると、アイドル間隔を指定できます。この間隔が経過すると、キューは自動的に削除されます。 最小時間は、5 分です。 詳細については、「[QueueDescription.AutoDeleteOnIdle プロパティ](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle)」を参照してください。

### <a name="duplicate-detection"></a>重複検出

エラーが発生すると、クライアントは送信操作の結果について不明な状態になる可能性があります。 重複検出を使用すると、送信側は同じメッセージを再送信できます。 別の方法として、キューまたはトピックで重複するコピーを破棄することもできます。 詳しくは、「[重複検出](duplicate-detection.md)」をご覧ください。

### <a name="security-protocols"></a>セキュリティ プロトコル
<a name="sas-rbac-and-managed-identities-for-azure-resources"></a>

Service Bus では、[Shared Access Signatures](service-bus-sas.md) (SAS)、[Azure ロールベースのアクセス制御 (Azure RBAC)](authenticate-application.md)、[Azure リソースのマネージド ID](service-bus-managed-service-identity.md) などのセキュリティ プロトコルがサポートされています。

### <a name="geo-disaster-recovery"></a>geo ディザスター リカバリー

Azure リージョンまたはデータセンターでダウンタイムが発生すると、geo ディザスター リカバリーにより、異なるリージョンまたはデータ センターでデータ処理を継続できます。 詳細については、「[Azure Service Bus の geo ディザスター リカバリー](service-bus-geo-dr.md)」を参照してください。

### <a name="security"></a>Security

Service Bus は、標準の [AMQP 1.0](service-bus-amqp-overview.md) および [HTTP/REST](/rest/api/servicebus/) プロトコルをサポートしています。

## <a name="client-libraries"></a>クライアント ライブラリ

Service Bus は、[.NET](https://github.com/Azure/azure-service-bus-dotnet/tree/master)、[Java](https://github.com/Azure/azure-service-bus-java/tree/master)、[JMS](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client) のクライアント ライブラリをサポートしています。

## <a name="integration"></a>統合

Service Bus は、以下の Azure サービスと完全に統合されています。

* [Event Grid](https://azure.microsoft.com/services/event-grid/)
* [Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Dynamics 365](https://dynamics.microsoft.com)
* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)

## <a name="next-steps"></a>次のステップ

Service Bus メッセージングの基本的な使い方については、以下の記事を参照してください。

* Azure メッセージング サービスの比較については、「[サービスの比較](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json)」を参照してください。
* [.NET](service-bus-dotnet-get-started-with-queues.md)、[Java](service-bus-java-how-to-use-queues.md)、または [JMS](service-bus-java-how-to-use-jms-api-amqp.md) のクイック スタートを試す。
* Service Bus リソースを管理するには、「[Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)」を参照してください。
* Standard レベルと Premium レベルとその価格の詳細については、「[Service Bus の価格](https://azure.microsoft.com/pricing/details/service-bus/)」を参照してください。
* Premium レベルのパフォーマンスと待機時間の詳細については、「[Premium メッセージング](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722)」を参照してください。

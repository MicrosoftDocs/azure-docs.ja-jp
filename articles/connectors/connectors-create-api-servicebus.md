---
title: "ロジック アプリでの Azure Service Bus コネクタの使用方法 | Microsoft Docs"
description: "Azure App Service を使用してロジック アプリを作成します。 Azure Service Bus に接続して、メッセージを送受信します。 キューに送信、トピックに送信、キューから受信、サブスクリプションから受信などのアクションを実行できます。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/02/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 1e23402cbc63aeb262bfb471745589cc0bbd734f
ms.lasthandoff: 02/16/2017


---
# <a name="get-started-with-the-azure-service-bus-connector"></a>Azure Service Bus コネクタの使用
Azure Service Bus に接続して、メッセージを送受信します。 キューに送信、トピックに送信、キューから受信、サブスクリプションから受信などのアクションを実行できます。

[任意のコネクタ](apis-list.md)を使用するには、まずロジック アプリを作成する必要があります。 [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)から始めることができます。

## <a name="connect-to-service-bus"></a>Service Bus への接続
ロジック アプリから任意のサービスにアクセスできるようにするには、まず、そのサービスへの接続を作成する必要があります。 [接続](connectors-overview.md)により、ロジック アプリと別のサービスとの接続が実現します。  

> [!INCLUDE [Steps to create a connection to Azure Service Bus](../../includes/connectors-create-api-servicebus.md)]
> 
> 

## <a name="use-a-service-bus-trigger"></a>Service Bus トリガーの使用
トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。 トリガーの詳細については[こちら](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)を参照してください。  

> [!INCLUDE [Steps to create a Service Bus trigger](../../includes/connectors-create-api-servicebus-trigger.md)]
> 
> 

## <a name="use-a-service-bus-action"></a>Service Bus アクションの使用
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 アクションの詳細については[こちら](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)を参照してください。

[!INCLUDE [Steps to create a Service Bus action](../../includes/connectors-create-api-servicebus-action.md)]

## <a name="technical-details"></a>技術的な詳細
ここでは、この接続でサポートされるトリガー、アクション、応答について詳しく説明します。

### <a name="service-bus-triggers"></a>Service Bus トリガー
Service Bus には、次のトリガーがあります。  

| トリガー | 説明 |
| --- | --- |
| [When a message is received in a queue (キューでメッセージを受け取ったとき)](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-queue) |この操作では、キューでメッセージを受け取ったときにフローをトリガーします。 |
| [When a message is received in a topic subscription (トピック サブスクリプションでメッセージを受け取ったとき)](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-topic-subscription) |この操作では、トピック サブスクリプションでメッセージを受け取ったときにフローをトリガーします。 |

### <a name="service-bus-actions"></a>Service Bus アクション
Service Bus には、次のアクションがあります。

| アクション | 説明 |
| --- | --- |
| [メッセージを送信する](connectors-create-api-servicebus.md#send-message) |この操作は、メッセージをキューまたはトピックに送信します。 |

### <a name="action-and-trigger-details"></a>アクションとトリガーの詳細
ここでは、このコネクタのアクションとトリガー、およびその応答について詳しく説明します。

#### <a name="send-message"></a>メッセージを送信する
| プロパティ名 | 表示名 | 説明 |
| --- | --- | --- |
| ContentData* |コンテンツ |メッセージのコンテンツ |
| ContentType |コンテンツの種類 |メッセージのコンテンツの種類 |
| プロパティ |プロパティ |各ブローカー プロパティのキーと値のペア |
| entityName* |キュー/トピック名 |キューまたはトピックの名前 |

他にも次の詳細パラメーターが用意されています。

| プロパティ名 | 表示名 | 説明 |
| --- | --- | --- |
| MessageId |メッセージ ID |有効になっている場合に、重複するメッセージを識別するために Service Bus が使用できるユーザー定義の値 |
| To |To |送信先アドレス |
| ReplyTo |返信 |返信するキューのアドレス |
| ReplyToSessionId |返信セッション ID |返信するセッションの識別子 |
| ラベル |ラベル |アプリケーション固有のラベル |
| ScheduledEnqueueTimeUtc |ScheduledEnqueueTimeUtc |メッセージがキューに追加される日付と時刻 (UTC) |
| SessionId |セッション ID |セッションの識別子 |
| CorrelationId |関連付け ID |関連付けの識別子 |
| TimeToLive |有効期限 |メッセージが有効である期間 (ティック単位) 期間は、メッセージが Service Bus に送信されたときから開始します。 |

* は、必須のプロパティを示します。

#### <a name="when-a-message-is-received-in-a-queue"></a>When a message is received in a queue (キューでメッセージを受け取ったとき)
| プロパティ名 | 表示名 | 説明 |
| --- | --- | --- |
| queueName* |キュー名 |キューの名前。 |

* は、必須のプロパティを示します。

##### <a name="output-details"></a>出力の詳細
ServiceBusMessage: このオブジェクトには、Service Bus メッセージのコンテンツとプロパティが含まれます。

| プロパティ名 | データ型 | Description |
| --- | --- | --- |
| ContentData |string |メッセージのコンテンツ |
| ContentType |string |メッセージのコンテンツの種類 |
| プロパティ |オブジェクト |各ブローカー プロパティのキーと値のペア |
| MessageId |文字列 |有効になっている場合に、重複するメッセージを識別するために Service Bus が使用できるユーザー定義の値 |
| To |string |送信先アドレス |
| ReplyTo |文字列 |返信するキューのアドレス |
| ReplyToSessionId |string |返信するセッションの識別子 |
| ラベル |文字列 |アプリケーション固有のラベル |
| ScheduledEnqueueTimeUtc |string |メッセージがキューに追加される日付と時刻 (UTC) |
| SessionId |string |セッションの識別子 |
| CorrelationId |string |関連付けの識別子 |
| TimeToLive |string |メッセージが有効である期間 (ティック単位) 期間は、メッセージが Service Bus に送信されたときから開始します。 |

#### <a name="when-a-message-is-received-in-a-topic-subscription"></a>When a message is received in a topic subscription (トピック サブスクリプションでメッセージを受け取ったとき)
| プロパティ名 | 表示名 | 説明 |
| --- | --- | --- |
| topicName* |トピック名 |トピックの名前。 |
| subscriptionName* |Topic subscription name (トピック サブスクリプション名) |トピック サブスクリプションの名前。 |

* は、必須のプロパティを示します。

##### <a name="output-details"></a>出力の詳細
ServiceBusMessage: このオブジェクトには、Service Bus メッセージのコンテンツとプロパティが含まれます。

| プロパティ名 | データ型 | Description |
| --- | --- | --- |
| ContentData |string |メッセージのコンテンツ |
| ContentType |string |メッセージのコンテンツの種類 |
| プロパティ |オブジェクト |各ブローカー プロパティのキーと値のペア |
| MessageId |文字列 |有効になっている場合に、重複するメッセージを識別するために Service Bus が使用できるユーザー定義の値 |
| To |string |送信先アドレス |
| ReplyTo |文字列 |返信するキューのアドレス |
| ReplyToSessionId |string |返信するセッションの識別子 |
| ラベル |文字列 |アプリケーション固有のラベル |
| ScheduledEnqueueTimeUtc |string |メッセージがキューに追加される日付と時刻 (UTC) |
| SessionId |string |セッションの識別子 |
| CorrelationId |string |関連付けの識別子 |
| TimeToLive |string |メッセージが有効である期間 (ティック単位) 期間は、メッセージが Service Bus に送信されたときから開始します。 |

### <a name="http-responses"></a>HTTP 応答
上記のアクションとトリガーは、次の HTTP 状態コードを&1; つ以上返す場合があります。

| Name | 説明 |
| --- | --- |
| 200 |OK |
| 202 |承認済み |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました。 |
| default |操作に失敗しました。 |

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成](../logic-apps/logic-apps-create-a-logic-app.md)します。



---
title: Azure Service Bus での AMQP 1.0 要求/応答操作
description: この記事では、Microsoft Azure Service Bus での AMQP 要求および応答ベースの操作の一覧を定義します。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: b845f4086ee1ac4fe868571c1754caf6d29b9021
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88064416"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>Microsoft Azure Service Bus における AMQP 1.0: 要求/応答ベースの操作

この記事では、Microsoft Azure Service Bus の要求/応答ベースの操作の一覧を示します。 この情報は、AMQP Management Version 1.0 ワーキング ドラフトに基づいています。  
  
OASIS AMQP 技術仕様に基づいて Service Bus が実装および構築される方法について説明している、詳細な回線レベルの AMQP 1.0 プロトコル ガイドについては、「[Azure Service Bus と Event Hubs における AMQP 1.0 プロトコル ガイド][AMQP 1.0 プロトコル ガイド]」を参照してください。  
  
## <a name="concepts"></a>概念  
  
### <a name="entity-description"></a>エンティティの説明  

エンティティの説明は、Service Bus の [QueueDescription クラス](/dotnet/api/microsoft.servicebus.messaging.queuedescription)、[TopicDescription クラス](/dotnet/api/microsoft.servicebus.messaging.topicdescription)、または [SubscriptionDescription クラス](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription)のオブジェクトを指します。  
  
### <a name="brokered-message"></a>ブローカー メッセージ  

AMQP メッセージにマップされている Service Bus のメッセージを表します。 マッピングは、[Service Bus AMQP プロトコル ガイド](service-bus-amqp-protocol-guide.md)で定義されています。  
  
## <a name="attach-to-entity-management-node"></a>エンティティ管理ノードへの接続  

このドキュメントで説明するすべての操作は、要求/応答パターンに従います。操作のスコープはエンティティであり、エンティティ管理ノードに接続する必要があります。  
  
### <a name="create-link-for-sending-requests"></a>要求を送信するためのリンクの作成  

要求を送信するために管理ノードへのリンクを作成します。  
  
```
requestLink = session.attach(
role: SENDER,
    target: { address: "<entity address>/$management" },
    source: { address: ""<my request link unique address>" }
)

```
  
### <a name="create-link-for-receiving-responses"></a>応答を受信するためのリンクの作成  

管理ノードからの応答を受信するためのリンクを作成します。  
  
```
responseLink = session.attach(
role: RECEIVER,
    source: { address: "<entity address>/$management" }
    target: { address: "<my response link unique address>" }
)

```
  
### <a name="transfer-a-request-message"></a>要求メッセージの転送  

要求メッセージを転送します。  
トランザクションをサポートする操作の場合は、必要に応じてトランザクション状態を追加することができます。

```
requestLink.sendTransfer(
        Message(
                properties: {
                        message-id: <request id>,
                        reply-to: "<my response link unique address>"
                },
                application-properties: {
                        "operation" -> "<operation>",
                }
        ),
        [Optional] State = transactional-state: {
                txn-id: <txn-id>
        }
)
```
  
### <a name="receive-a-response-message"></a>応答メッセージの受信  

応答リンクから応答メッセージを受信します。  
  
```
responseMessage = responseLink.receiveTransfer()
```
  
応答メッセージは次の形式になります。
  
```
Message(
properties: {
        correlation-id: <request id>
    },
    application-properties: {
            "statusCode" -> <status code>,
            "statusDescription" -> <status description>,
           },
)

```
  
### <a name="service-bus-entity-address"></a>Service Bus エンティティのアドレス  

Service Bus エンティティは、次のようにアドレス指定する必要があります。  
  
|エンティティ型|Address|例|  
|-----------------|-------------|-------------|  
|queue|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|topic|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|subscription|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>メッセージ操作  
  
### <a name="message-renew-lock"></a>メッセージ更新ロック  

エンティティの説明で指定された時間までメッセージのロックを延長します。  
  
#### <a name="request"></a>要求  

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|いいえ|操作のサーバー タイムアウト (ミリ秒単位)。|  
  
 要求メッセージの本文は、次のエントリが含まれたマップを含む amqp-value セクションで構成されている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|UUID の配列|はい|更新するメッセージ ロック トークン。|  

> [!NOTE]
> ロック トークンは、受信メッセージの　`DeliveryTag`　プロパティです。 [.NET SDK](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) で、それらを取得する次の例を参照してください。 トークンは 'DeliveryAnnotations' に 'x-opt-lock-token' と表示されることもありますが、これは確実ではないため、`DeliveryTag` を使用することをお勧めします。 
> 
  
#### <a name="response"></a>Response  

応答メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|はい|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - 成功、それ以外の場合は失敗。|  
|statusDescription|string|いいえ|ステータスの説明。|  
  
応答メッセージの本文は、次のエントリが含まれたマップを含む amqp-value セクションで構成されている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|expirations|タイムスタンプの配列|はい|要求ロック トークンに対応するメッセージ ロック トークンの新しい有効期限。|  
  
### <a name="peek-message"></a>メッセージのピーク  

ロックせずにメッセージをピークします。  
  
#### <a name="request"></a>要求  

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|いいえ|操作のサーバー タイムアウト (ミリ秒単位)。|  
  
要求メッセージの本文は、次のエントリが含まれた **マップ** を含む **amqp-value** セクションで構成されている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|long|はい|ピークを開始するシーケンス番号。|  
|`message-count`|INT|はい|ピークするメッセージの最大数。|  
  
#### <a name="response"></a>Response  

応答メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|はい|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - さらにメッセージがあります。<br /><br /> 204: No content - これ以上メッセージはありません。|  
|statusDescription|string|いいえ|ステータスの説明。|  
  
応答メッセージの本文は、次のエントリが含まれた **マップ** を含む **amqp-value** セクションで構成されている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|messages|マップのリスト|はい|各マップが表すメッセージのリスト。|  
  
メッセージを表すマップには、次のエントリが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|message|byte 型の配列|はい|AMQP 1.0 のワイヤーエンコードされたメッセージ。|  
  
### <a name="schedule-message"></a>メッセージのスケジュール設定  

メッセージのスケジュールを設定します。 この操作はトランザクションをサポートします。
  
#### <a name="request"></a>要求  

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|いいえ|操作のサーバー タイムアウト (ミリ秒単位)。|  
  
要求メッセージの本文は、次のエントリが含まれた **マップ** を含む **amqp-value** セクションで構成されている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|messages|マップのリスト|はい|各マップが表すメッセージのリスト。|  
  
メッセージを表すマップには、次のエントリが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|message-id|string|はい|文字列としての `amqpMessage.Properties.MessageId`|  
|session-id|string|いいえ|`amqpMessage.Properties.GroupId as string`|  
|partition-key|string|いいえ|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|via-partition-key|string|いいえ|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|message|byte 型の配列|はい|AMQP 1.0 のワイヤーエンコードされたメッセージ。|  
  
#### <a name="response"></a>Response  

応答メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|はい|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - 成功、それ以外の場合は失敗。|  
|statusDescription|string|いいえ|ステータスの説明。|  
  
応答メッセージの本文は、次のエントリが含まれたマップを含む **amqp-value** セクションで構成されている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|long の配列|はい|スケジュール済みメッセージのシーケンス番号。 シーケンス番号は取り消しに使用されます。|  
  
### <a name="cancel-scheduled-message"></a>スケジュール済みメッセージの取り消し  

スケジュール済みメッセージを取り消します。  
  
#### <a name="request"></a>要求  

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|いいえ|操作のサーバー タイムアウト (ミリ秒単位)。|  
  
要求メッセージの本文は、次のエントリが含まれた **マップ** を含む **amqp-value** セクションで構成されている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|long の配列|はい|取り消すスケジュール済みメッセージのシーケンス番号。|  
  
#### <a name="response"></a>Response  

応答メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|はい|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - 成功、それ以外の場合は失敗。|  
|statusDescription|string|いいえ|ステータスの説明。|   
  
## <a name="session-operations"></a>セッション操作  
  
### <a name="session-renew-lock"></a>セッション更新ロック  

エンティティの説明で指定された時間までメッセージのロックを延長します。  
  
#### <a name="request"></a>要求  

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|いいえ|操作のサーバー タイムアウト (ミリ秒単位)。|  
  
要求メッセージの本文は、次のエントリが含まれた **マップ** を含む **amqp-value** セクションで構成されている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|session-id|string|はい|セッション ID。|  
  
#### <a name="response"></a>Response  

応答メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|はい|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - さらにメッセージがあります。<br /><br /> 204: No content - これ以上メッセージはありません。|  
|statusDescription|string|いいえ|ステータスの説明。|  
  
応答メッセージの本文は、次のエントリが含まれたマップを含む **amqp-value** セクションで構成されている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|expiration|timestamp|はい|新しい有効期限。|  
  
### <a name="peek-session-message"></a>セッション メッセージのピーク  

ロックせずにセッション メッセージをピークします。  
  
#### <a name="request"></a>要求  

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|いいえ|操作のサーバー タイムアウト (ミリ秒単位)。|  
  
要求メッセージの本文は、次のエントリが含まれた **マップ** を含む **amqp-value** セクションで構成されている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|from-sequence-number|long|はい|ピークを開始するシーケンス番号。|  
|message-count|INT|はい|ピークするメッセージの最大数。|  
|session-id|string|はい|セッション ID。|  
  
#### <a name="response"></a>Response  

応答メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|はい|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - さらにメッセージがあります。<br /><br /> 204: No content - これ以上メッセージはありません。|  
|statusDescription|string|いいえ|ステータスの説明。|  
  
応答メッセージの本文は、次のエントリが含まれたマップを含む **amqp-value** セクションで構成されている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|messages|マップのリスト|はい|各マップが表すメッセージのリスト。|  
  
 メッセージを表すマップには、次のエントリが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|message|byte 型の配列|はい|AMQP 1.0 のワイヤーエンコードされたメッセージ。|  
  
### <a name="set-session-state"></a>セッションの状態の設定  

セッションの状態を設定します。  
  
#### <a name="request"></a>要求  

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|いいえ|操作のサーバー タイムアウト (ミリ秒単位)。|  
  
要求メッセージの本文は、次のエントリが含まれた **マップ** を含む **amqp-value** セクションで構成されている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|session-id|string|はい|セッション ID。|  
|session-state|バイト配列|はい|非透過的なバイナリ データ。|  
  
#### <a name="response"></a>Response  

応答メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|はい|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - 成功、それ以外の場合は失敗|  
|statusDescription|string|いいえ|ステータスの説明。|  
  
### <a name="get-session-state"></a>セッションの状態の取得  

セッションの状態を取得します。  
  
#### <a name="request"></a>要求  

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|いいえ|操作のサーバー タイムアウト (ミリ秒単位)。|  
  
要求メッセージの本文は、次のエントリが含まれた **マップ** を含む **amqp-value** セクションで構成されている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|session-id|string|はい|セッション ID。|  
  
#### <a name="response"></a>Response  

応答メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|はい|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - 成功、それ以外の場合は失敗|  
|statusDescription|string|いいえ|ステータスの説明。|  
  
応答メッセージの本文は、次のエントリが含まれた **マップ** を含む **amqp-value** セクションで構成されている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|session-state|バイト配列|はい|非透過的なバイナリ データ。|  
  
### <a name="enumerate-sessions"></a>セッションの列挙  

メッセージング エンティティのセッションを列挙します。  
  
#### <a name="request"></a>要求  

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|いいえ|操作のサーバー タイムアウト (ミリ秒単位)。|  
  
要求メッセージの本文は、次のエントリが含まれた **マップ** を含む **amqp-value** セクションで構成されている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|last-updated-time|timestamp|はい|指定の時間以降に更新されたセッションだけを含めるためのフィルター。|  
|skip|INT|はい|多数のセッションをスキップします。|  
|top|INT|はい|セッションの最大数。|  
  
#### <a name="response"></a>Response  

応答メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|はい|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - さらにメッセージがあります。<br /><br /> 204: No content - これ以上メッセージはありません。|  
|statusDescription|string|いいえ|ステータスの説明。|  
  
応答メッセージの本文は、次のエントリが含まれた **マップ** を含む **amqp-value** セクションで構成されている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|skip|INT|はい|スキップされたセッションの数 (状態コードが 200 の場合)。|  
|sessions-ids|文字列の配列|はい|セッション ID の配列 (状態コードが 200 の場合)。|  
  
## <a name="rule-operations"></a>ルール操作  
  
### <a name="add-rule"></a>ルールの追加  
  
#### <a name="request"></a>要求  

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|いいえ|操作のサーバー タイムアウト (ミリ秒単位)。|  
  
要求メッセージの本文は、次のエントリが含まれた **マップ** を含む **amqp-value** セクションで構成されている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|rule-name|string|はい|ルール名。サブスクリプションとトピックの名前は含まれません。|  
|rule-description|map|はい|次のセクションで指定されたルールの説明。|  
  
**rule-description** マップには、次のエントリが含まれている必要があります。**sql-filter** と **correlation-filter** を同時に含めることはできません。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|sql-filter|map|はい|次のセクションで指定された `sql-filter`。|  
|correlation-filter|map|はい|次のセクションで指定された `correlation-filter`。|  
|sql-rule-action|map|はい|次のセクションで指定された `sql-rule-action`。|  
  
sql-filter マップには、次のエントリが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|expression|string|はい|SQL フィルター式。|  
  
**correlation-filter** マップには、次のエントリが 1 つ以上含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|correlation-id|string|いいえ||  
|message-id|string|いいえ||  
|to|string|いいえ||  
|reply-to|string|いいえ||  
|label|string|いいえ||  
|session-id|string|いいえ||  
|reply-to-session-id|string|いいえ||  
|content-type|string|いいえ||  
|properties|map|いいえ|Service Bus の [BrokeredMessage.Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) へのマップ。|  
  
**sql-rule-action** マップには、次のエントリが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|expression|string|はい|SQL アクションの式。|  
  
#### <a name="response"></a>Response  

応答メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|はい|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - 成功、それ以外の場合は失敗|  
|statusDescription|string|いいえ|ステータスの説明。|  
  
### <a name="remove-rule"></a>ルールの削除  
  
#### <a name="request"></a>要求  

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|いいえ|操作のサーバー タイムアウト (ミリ秒単位)。|  
  
要求メッセージの本文は、次のエントリが含まれた **マップ** を含む **amqp-value** セクションで構成されている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|rule-name|string|はい|ルール名。サブスクリプションとトピックの名前は含まれません。|  
  
#### <a name="response"></a>Response  

応答メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|はい|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - 成功、それ以外の場合は失敗|  
|statusDescription|string|いいえ|ステータスの説明。|  
  
### <a name="get-rules"></a>ルールの取得

#### <a name="request"></a>要求

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。

|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|いいえ|操作のサーバー タイムアウト (ミリ秒単位)。|  

要求メッセージの本文は、次のエントリが含まれた **マップ** を含む **amqp-value** セクションで構成されている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|top|INT|はい|ページ内でフェッチされるルールの数。|  
|skip|INT|はい|スキップするルールの数。 ルールの一覧で開始インデックス (+1) を定義します。 | 

#### <a name="response"></a>Response

応答メッセージには、次のプロパティが含まれています。

|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|はい|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - 成功、それ以外の場合は失敗|  
|rules| マップの配列|はい|ルールの配列。 各ルールはマップで表現されます。|

配列内の各マップ エントリには、次のプロパティが含まれています。

|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|rule-description|記述されたオブジェクトの配列|はい|AMQP で記述されたコード 0x0000013700000004 が含まれる `com.microsoft:rule-description:list`| 

`com.microsoft.rule-description:list` は記述されたオブジェクトの配列です。 配列の内容は次のとおりです。

|インデックス|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
| 0 | 記述されたオブジェクトの配列 | はい | 以下に指定された `filter`。 |
| 1 | 記述されたオブジェクトの配列 | はい | 以下に指定された `ruleAction`。 |
| 2 | string | はい | ルールの名前です。 |

`filter` は次のいずれかの種類になります。

| 記述子名 | 記述子コード | 値 |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | SQL フィルター |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | 関連付けフィルター |
| `com.microsoft:true-filter:list` | 0x000001370000007 | 1=1 を表す true フィルター |
| `com.microsoft:false-filter:list` | 0x000001370000008 | 1=0 を表す false フィルター |

`com.microsoft:sql-filter:list` は以下の内容が含まれる、配列の記述です。

|インデックス|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
| 0 | string | はい | SQL フィルター式 |

`com.microsoft:correlation-filter:list` は以下の内容が含まれる、配列の記述です。

|インデックス (存在する場合)|値の型|値の内容|  
|---------|----------------|--------------|
| 0 | string | 関連付け ID |
| 1 | string | メッセージ ID |
| 2 | string | 終了 |
| 3 | string | 返信 |
| 4 | string | Label |
| 5 | string | セッション ID |
| 6 | string | 返信セッション ID|
| 7 | string | コンテンツの種類 |
| 8 | マップ | アプリケーションで定義されているプロパティのマップ |

`ruleAction` は次のいずれかの種類になります。

| 記述子名 | 記述子コード | 値 |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x0000013700000005 | 空のルール アクション - ルール アクションは存在しません |
| `com.microsoft:sql-rule-action:list` | 0x0000013700000006 | SQL ルール アクション |

`com.microsoft:sql-rule-action:list` は記述されたオブジェクトの配列であり、この配列の最初のエントリは SQL ルール アクションの式が含まれている文字列となります。

## <a name="deferred-message-operations"></a>遅延メッセージ操作  
  
### <a name="receive-by-sequence-number"></a>シーケンス番号での受信  

遅延メッセージをシーケンス番号で受信します。  
  
#### <a name="request"></a>要求  

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|いいえ|操作のサーバー タイムアウト (ミリ秒単位)。|  
  
要求メッセージの本文は、次のエントリが含まれた **マップ** を含む **amqp-value** セクションで構成されている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|long の配列|はい|シーケンス番号。|  
|receiver-settle-mode|ubyte|はい|AMQP core v1.0 で指定されている **受信側解決** モード。|  
  
#### <a name="response"></a>Response  

応答メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|はい|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - 成功、それ以外の場合は失敗|  
|statusDescription|string|いいえ|ステータスの説明。|  
  
応答メッセージの本文は、次のエントリが含まれた **マップ** を含む **amqp-value** セクションで構成されている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|messages|マップのリスト|はい|各マップがメッセージを表すメッセージのリスト。|  
  
メッセージを表すマップには、次のエントリが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|lock-token|uuid|はい|ロック トークン (`receiver-settle-mode` が 1 の場合)。|  
|message|byte 型の配列|はい|AMQP 1.0 のワイヤーエンコードされたメッセージ。|  
  
### <a name="update-disposition-status"></a>廃棄状態の更新  

遅延メッセージの廃棄状態を更新します。 この操作はトランザクションをサポートします。
  
#### <a name="request"></a>要求  

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|いいえ|操作のサーバー タイムアウト (ミリ秒単位)。|  
  
要求メッセージの本文は、次のエントリが含まれた **マップ** を含む **amqp-value** セクションで構成されている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|disposition-status|string|はい|完了<br /><br /> abandoned<br /><br /> suspended|  
|lock-tokens|UUID の配列|はい|廃棄状態を更新するメッセージ ロック トークン。|  
|deadletter-reason|string|No|廃棄状態が **suspended** に設定されている場合に設定できます。|  
|deadletter-description|string|No|廃棄状態が **suspended** に設定されている場合に設定できます。|  
|properties-to-modify|map|No|変更する Service Bus ブローカー メッセージのプロパティのリスト。|  
  
#### <a name="response"></a>Response  

応答メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|Key|値型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|はい|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - 成功、それ以外の場合は失敗|  
|statusDescription|string|いいえ|ステータスの説明。|

## <a name="next-steps"></a>次のステップ

AMQP と Service Bus の詳細については、次のリンクを参照してください。

* [Service Bus AMQP の概要]
* [AMQP 1.0 プロトコル ガイド]
* [Windows Server 用 Service Bus の AMQP]

[Service Bus AMQP の概要]: service-bus-amqp-overview.md
[AMQP 1.0 プロトコル ガイド]: service-bus-amqp-protocol-guide.md
[Windows Server 用 Service Bus の AMQP]: /previous-versions/service-bus-archive/dn282144(v=azure.100)
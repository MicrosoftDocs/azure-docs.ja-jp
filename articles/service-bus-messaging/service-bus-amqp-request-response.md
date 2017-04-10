---
title: "Azure Service Bus における AMQP 1.0: 要求/応答ベースの操作 | Microsoft Docs"
description: "Microsoft Azure Service Bus の要求/応答ベースの操作の一覧。"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: a09aefd00a89c48acdc885f98e34d7faa9c5629a
ms.lasthandoff: 03/24/2017


---

# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>Microsoft Azure Service Bus における AMQP 1.0: 要求/応答ベースの操作

このトピックでは、Microsoft Azure Service Bus の要求/応答ベースの操作の一覧を示します。 この情報は、AMQP Management Version 1.0 ワーキング ドラフトに基づいています。  
  
OASIS AMQP 技術仕様に基づいて Service Bus が実装および構築される方法について説明している、詳細な回線レベルの AMQP 1.0 プロトコル ガイドについては、「[Azure Service Bus と Event Hubs における AMQP 1.0 プロトコル ガイド](service-bus-amqp-protocol-guide.md)」を参照してください。  
  
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
  
```  
requestLink.sendTransfer(  
        Message(  
                properties: {  
                        message-id: <request id>,  
                        reply-to: "<my response link unique address>"  
                },  
                application-properties: {  
                        "operation" -> "<operation>",  
                },  
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
  
|エンティティの種類|Address|例|  
|-----------------|-------------|-------------|  
|キュー|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|トピック|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|サブスクリプション|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>メッセージ操作  
  
### <a name="message-renew-lock"></a>メッセージ更新ロック  

エンティティの説明で指定された時間までメッセージのロックを延長します。  
  
#### <a name="request"></a>要求  

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|なし|操作のサーバー タイムアウト (ミリ秒単位)。|  
  
 要求メッセージの本文は、次のエントリが含まれたマップを含む amqp-value セクションで構成されている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|UUID の配列|あり|更新するメッセージ ロック トークン。|  
  
#### <a name="response"></a>応答  

応答メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|あり|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - 成功、それ以外の場合は失敗。|  
|statusDescription|string|いいえ|ステータスの説明。|  
  
応答メッセージの本文は、次のエントリが含まれたマップを含む amqp-value セクションで構成されている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|expirations|タイムスタンプの配列|はい|要求ロック トークンに対応するメッセージ ロック トークンの新しい有効期限。|  
  
### <a name="peek-message"></a>メッセージのピーク  

ロックせずにメッセージをピークします。  
  
#### <a name="request"></a>要求  

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|なし|操作のサーバー タイムアウト (ミリ秒単位)。|  
  
要求メッセージの本文は、次のエントリが含まれた**マップ**を含む **amqp-value** セクションで構成されている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|long|はい|ピークを開始するシーケンス番号。|  
|`message-count`|int|はい|ピークするメッセージの最大数。|  
  
#### <a name="response"></a>応答  

応答メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|あり|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - さらにメッセージがあります。<br /><br /> 0xcc: No content - これ以上メッセージはありません。|  
|statusDescription|string|いいえ|ステータスの説明。|  
  
応答メッセージの本文は、次のエントリが含まれた**マップ**を含む **amqp-value** セクションで構成されている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|最大配信数|マップのリスト|あり|各マップが表すメッセージのリスト。|  
  
メッセージを表すマップには、次のエントリが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|message|byte 型の配列|はい|AMQP 1.0 のワイヤーエンコードされたメッセージ。|  
  
### <a name="schedule-message"></a>メッセージのスケジュール設定  

メッセージのスケジュールを設定します。  
  
#### <a name="request"></a>要求  

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|なし|操作のサーバー タイムアウト (ミリ秒単位)。|  
  
要求メッセージの本文は、次のエントリが含まれた**マップ**を含む **amqp-value** セクションで構成されている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|最大配信数|マップのリスト|あり|各マップが表すメッセージのリスト。|  
  
メッセージを表すマップには、次のエントリが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|message-id|string|はい|文字列としての `amqpMessage.Properties.MessageId`|  
|session-id|string|あり|`amqpMessage.Properties.GroupId as string`|  
|partition-key|string|はい|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|  
|message|byte 型の配列|はい|AMQP 1.0 のワイヤーエンコードされたメッセージ。|  
  
#### <a name="response"></a>応答  

応答メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|あり|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - 成功、それ以外の場合は失敗。|  
|statusDescription|string|いいえ|ステータスの説明。|  
  
応答メッセージの本文は、次のエントリが含まれたマップを含む **amqp-value** セクションで構成されている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|long 型の配列|はい|スケジュール済みメッセージのシーケンス番号。 シーケンス番号は取り消しに使用されます。|  
  
### <a name="cancel-scheduled-message"></a>スケジュール済みメッセージの取り消し  

スケジュール済みメッセージを取り消します。  
  
#### <a name="request"></a>要求  

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|なし|操作のサーバー タイムアウト (ミリ秒単位)。|  
  
要求メッセージの本文は、次のエントリが含まれた**マップ**を含む **amqp-value** セクションで構成されている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|long 型の配列|あり|取り消すスケジュール済みメッセージのシーケンス番号。|  
  
#### <a name="response"></a>応答  

応答メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|あり|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - 成功、それ以外の場合は失敗。|  
|statusDescription|string|いいえ|ステータスの説明。|  
  
応答メッセージの本文は、次のエントリが含まれたマップを含む **amqp-value** セクションで構成されている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|long 型の配列|はい|スケジュール済みメッセージのシーケンス番号。 シーケンス番号は取り消しに使用されます。|  
  
## <a name="session-operations"></a>セッション操作  
  
### <a name="session-renew-lock"></a>セッション更新ロック  

エンティティの説明で指定された時間までメッセージのロックを延長します。  
  
#### <a name="request"></a>要求  

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|なし|操作のサーバー タイムアウト (ミリ秒単位)。|  
  
要求メッセージの本文は、次のエントリが含まれた**マップ**を含む **amqp-value** セクションで構成されている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|session-id|string|あり|セッション ID。|  
  
#### <a name="response"></a>応答  

応答メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|あり|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - さらにメッセージがあります。<br /><br /> 0xcc: No content - これ以上メッセージはありません。|  
|statusDescription|string|いいえ|ステータスの説明。|  
  
応答メッセージの本文は、次のエントリが含まれたマップを含む **amqp-value** セクションで構成されている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|expiration|timestamp|あり|新しい有効期限。|  
  
### <a name="peek-session-message"></a>セッション メッセージのピーク  

ロックせずにセッション メッセージをピークします。  
  
#### <a name="request"></a>要求  

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|なし|操作のサーバー タイムアウト (ミリ秒単位)。|  
  
要求メッセージの本文は、次のエントリが含まれた**マップ**を含む **amqp-value** セクションで構成されている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|from-sequence-number|long|はい|ピークを開始するシーケンス番号。|  
|message-count|int|はい|ピークするメッセージの最大数。|  
|session-id|string|あり|セッション ID。|  
  
#### <a name="response"></a>応答  

応答メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|あり|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - さらにメッセージがあります。<br /><br /> 0xcc: No content - これ以上メッセージはありません。|  
|statusDescription|string|いいえ|ステータスの説明。|  
  
応答メッセージの本文は、次のエントリが含まれたマップを含む **amqp-value** セクションで構成されている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|最大配信数|マップのリスト|あり|各マップが表すメッセージのリスト。|  
  
 メッセージを表すマップには、次のエントリが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|message|byte 型の配列|はい|AMQP 1.0 のワイヤーエンコードされたメッセージ。|  
  
### <a name="set-session-state"></a>セッションの状態の設定  

セッションの状態を設定します。  
  
#### <a name="request"></a>要求  

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|なし|操作のサーバー タイムアウト (ミリ秒単位)。|  
  
要求メッセージの本文は、次のエントリが含まれた**マップ**を含む **amqp-value** セクションで構成されている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|session-id|string|あり|セッション ID。|  
|session-state|バイト配列|あり|非透過的なバイナリ データ。|  
  
#### <a name="response"></a>応答  

応答メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|あり|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - 成功、それ以外の場合は失敗|  
|statusDescription|string|いいえ|ステータスの説明。|  
  
### <a name="get-session-state"></a>セッションの状態の取得  

セッションの状態を取得します。  
  
#### <a name="request"></a>要求  

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|なし|操作のサーバー タイムアウト (ミリ秒単位)。|  
  
要求メッセージの本文は、次のエントリが含まれた**マップ**を含む **amqp-value** セクションで構成されている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|session-id|string|あり|セッション ID。|  
  
#### <a name="response"></a>応答  

応答メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|あり|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - 成功、それ以外の場合は失敗|  
|statusDescription|string|いいえ|ステータスの説明。|  
  
応答メッセージの本文は、次のエントリが含まれた**マップ**を含む **amqp-value** セクションで構成されている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|session-state|バイト配列|あり|非透過的なバイナリ データ。|  
  
### <a name="enumerate-sessions"></a>セッションの列挙  

メッセージング エンティティのセッションを列挙します。  
  
#### <a name="request"></a>要求  

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|なし|操作のサーバー タイムアウト (ミリ秒単位)。|  
  
要求メッセージの本文は、次のエントリが含まれた**マップ**を含む **amqp-value** セクションで構成されている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|last-updated-time|timestamp|あり|指定の時間以降に更新されたセッションだけを含めるためのフィルター。|  
|skip|int|はい|多数のセッションをスキップします。|  
|top|int|はい|セッションの最大数。|  
  
#### <a name="response"></a>応答  

応答メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|あり|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - さらにメッセージがあります。<br /><br /> 0xcc: No content - これ以上メッセージはありません。|  
|statusDescription|string|いいえ|ステータスの説明。|  
  
応答メッセージの本文は、次のエントリが含まれた**マップ**を含む **amqp-value** セクションで構成されている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|skip|int|あり|スキップされたセッションの数 (状態コードが 200 の場合)。|  
|sessions-ids|文字列配列|はい|セッション ID の配列 (状態コードが 200 の場合)。|  
  
## <a name="rule-operations"></a>ルール操作  
  
### <a name="add-rule"></a>ルールの追加  
  
#### <a name="request"></a>要求  

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|なし|操作のサーバー タイムアウト (ミリ秒単位)。|  
  
要求メッセージの本文は、次のエントリが含まれた**マップ**を含む **amqp-value** セクションで構成されている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|rule-name|string|はい|ルール名。サブスクリプションとトピックの名前は含まれません。|  
|rule-description|map|あり|次のセクションで指定されたルールの説明。|  
  
**rule-description** マップには、次のエントリが含まれている必要があります。**sql-filter** と **correlation-filter** を同時に含めることはできません。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|sql-filter|map|あり|次のセクションで指定された `sql-filter`。|  
|correlation-filter|map|あり|次のセクションで指定された `correlation-filter`。|  
|sql-rule-action|map|あり|次のセクションで指定された `sql-rule-action`。|  
  
sql-filter マップには、次のエントリが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|expression|string|はい|SQL フィルター式。|  
  
**correlation-filter** マップには、次のエントリが 1 つ以上含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|correlation-id|string|いいえ||  
|message-id|string|なし||  
|to|string|なし||  
|reply-to|string|なし||  
|label|string|なし||  
|session-id|string|なし||  
|reply-to-session-id|string|なし||  
|content-type|string|なし||  
|プロパティ|map|いいえ|Service Bus の [BrokeredMessage.Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Properties) へのマップ。|  
  
**sql-rule-action** マップには、次のエントリが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|expression|string|はい|SQL アクションの式。|  
  
#### <a name="response"></a>応答  

応答メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|あり|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - 成功、それ以外の場合は失敗|  
|statusDescription|string|いいえ|ステータスの説明。|  
  
### <a name="remove-rule"></a>ルールの削除  
  
#### <a name="request"></a>要求  

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|なし|操作のサーバー タイムアウト (ミリ秒単位)。|  
  
要求メッセージの本文は、次のエントリが含まれた**マップ**を含む **amqp-value** セクションで構成されている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|rule-name|string|はい|ルール名。サブスクリプションとトピックの名前は含まれません。|  
  
#### <a name="response"></a>応答  

応答メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|あり|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - 成功、それ以外の場合は失敗|  
|statusDescription|string|いいえ|ステータスの説明。|  
  
## <a name="deferred-message-operations"></a>遅延メッセージ操作  
  
### <a name="receive-by-sequence-number"></a>シーケンス番号での受信  

遅延メッセージをシーケンス番号で受信します。  
  
#### <a name="request"></a>要求  

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|なし|操作のサーバー タイムアウト (ミリ秒単位)。|  
  
要求メッセージの本文は、次のエントリが含まれた**マップ**を含む **amqp-value** セクションで構成されている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|long 型の配列|はい|シーケンス番号。|  
|receiver-settle-mode|ubyte|あり|AMQP core v1.0 で指定されている受信側解決モード。|  
  
#### <a name="response"></a>応答  

応答メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|あり|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - 成功、それ以外の場合は失敗|  
|statusDescription|string|いいえ|ステータスの説明。|  
  
応答メッセージの本文は、次のエントリが含まれた**マップ**を含む **amqp-value** セクションで構成されている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|最大配信数|マップのリスト|はい|各マップがメッセージを表すメッセージのリスト。|  
  
メッセージを表すマップには、次のエントリが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|lock-token|uuid|はい|ロック トークン (`receiver-settle-mode` が 1 の場合)。|  
|message|byte 型の配列|はい|AMQP 1.0 のワイヤーエンコードされたメッセージ。|  
  
### <a name="update-disposition-status"></a>廃棄状態の更新  

遅延メッセージの廃棄状態を更新します。  
  
#### <a name="request"></a>要求  

要求メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|operation|string|はい|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|なし|操作のサーバー タイムアウト (ミリ秒単位)。|  
  
要求メッセージの本文は、次のエントリが含まれた**マップ**を含む **amqp-value** セクションで構成されている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|disposition-status|string|はい|完了<br /><br /> abandoned<br /><br /> suspended|  
|lock-tokens|UUID の配列|はい|廃棄状態を更新するメッセージ ロック トークン。|  
|deadletter-reason|string|なし|廃棄状態が **suspended** に設定されている場合に設定できます。|  
|deadletter-description|string|なし|廃棄状態が **suspended** に設定されている場合に設定できます。|  
|properties-to-modify|map|いいえ|変更する Service Bus ブローカー メッセージのプロパティのリスト。|  
  
#### <a name="response"></a>応答  

応答メッセージには、次のアプリケーション プロパティが含まれている必要があります。  
  
|キー|値の型|必須|値の内容|  
|---------|----------------|--------------|--------------------|  
|StatusCode|int|あり|HTTP 応答コード [RFC2616]<br /><br /> 200: OK - 成功、それ以外の場合は失敗|  
|statusDescription|string|いいえ|ステータスの説明。|

## <a name="next-steps"></a>次のステップ
AMQP と Service Bus の詳細については、次のリンクを参照してください。

* [Service Bus AMQP の概要]
* [パーティション分割された Service Bus のキューとトピックにおける AMQP 1.0 のサポート]
* [Windows Server 用 Service Bus の AMQP]

[Service Bus AMQP の概要]: service-bus-amqp-overview.md
[パーティション分割された Service Bus のキューとトピックにおける AMQP 1.0 のサポート]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[Windows Server 用 Service Bus の AMQP]: https://msdn.microsoft.com/library/dn574799.asp

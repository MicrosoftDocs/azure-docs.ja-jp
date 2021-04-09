---
title: Azure Service Bus のメッセージ、ペイロード、およびシリアル化 | Microsoft Docs
description: この記事では、Azure Service Bus のメッセージ、ペイロード、メッセージ ルーティング、およびシリアル化について概要を説明します。
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: e1baca1cd3dedee5917670a00b62c68752b5e4b8
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095097"
---
# <a name="messages-payloads-and-serialization"></a>メッセージ、ペイロード、およびシリアル化

Microsoft Azure Service Bus はメッセージを処理します。 メッセージには、ペイロードとメタデータが含まれています。 メタデータはキーと値のペアのプロパティという形式をとり、ペイロードを記述し、処理の指示を Service Bus とアプリケーションに提供します。 ときには、メタデータ単独で送信者が受信者に伝える必要がある情報が運搬され、ペイロードは空のままという場合もあります。

.NET および Java 用の公式 Service Bus クライアントのオブジェクト モデルには、Service Bus でサポートされているワイヤ プロトコルと相互にマップされた Service Bus の抽象的なメッセージ構造が反映されています。
 
Service Bus メッセージは、どのような形式でもサービス側で Service Bus によって処理されないバイナリ ペイロード セクションと、2 つのプロパティ セットで構成されます。 "*ブローカー プロパティ*" は、システムによって事前定義されています。 この事前定義されているプロパティは、ブローカー内のメッセージ レベルの機能を制御するか、一般的な標準化されたメタデータ項目にマップされます。 "*ユーザー プロパティ*" は、アプリケーションが定義および設定できるキーと値のペアのコレクションです。
 
次の表に、事前定義されているブローカー プロパティの一覧を示します。 これらの名前は、すべての公式クライアント API と、HTTP プロトコル マッピングの JSON オブジェクト [BrokerProperties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) でも使用されます。
 
かっこ内には、AMQP プロトコル レベルで使用される同等の名前を示しています。 

| プロパティ名                         | 説明                                                                                                                                                                                                                                                                                                                                                                                                                               |
|---------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|  [`ContentType`](/dotnet/api/microsoft.azure.servicebus.message.contenttype) (content-type)           | これは省略可能であり、RFC2045 のセクション 5 の形式に従った記述子を使用してメッセージのペイロードを記述します (例: `application/json`)。                                                                                                                                                                                                                                                                                             |
|  [`CorrelationId`](/dotnet/api/microsoft.azure.servicebus.message.correlationid#Microsoft_Azure_ServiceBus_Message_CorrelationId) (correlation-id)       | 相関関係のために、アプリケーションがメッセージのコンテキストを指定できるようにします (たとえば、応答されるメッセージの **MessageId** を示すなど)。                                                                                                                                                                                                                                                                  |
| [`DeadLetterSource`](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource)                      | 配信不能になっていて、後で配信不能キューから別のエンティティに自動転送されるメッセージでのみ設定されます。 メッセージが配信不能になったエンティティを示します。 このプロパティは読み取り専用です。                                                                                                                                                                                                                                  |
| [`DeliveryCount`](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deliverycount)                         | <p>このメッセージに対して試行された配信の数です。 このカウントは、メッセージのロックが有効期限切れになった場合、またはメッセージが受信者によって明示的に破棄された場合に増分されます。 このプロパティは読み取り専用です。</p> <p>基になる AMQP 接続が閉じられると、配信の数は増加しません。</p>                                                                                                                                                                                                                                                 |
| [`EnqueuedSequenceNumber`](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber)                | このプロパティは、自動転送されたメッセージの場合に、元の送信地点で最初にメッセージに割り当てられたシーケンス番号を示します。 このプロパティは読み取り専用です。                                                                                                                                                                                                                                                                |
| [`EnqueuedTimeUtc`](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc)                       | メッセージがエンティティで受け入れおよび格納された UTC 時刻。 この値は、受信者が送信者の時計を信頼したくない場合に、信頼できる中立的な到着時間インジケーターとして使用できます。 このプロパティは読み取り専用です。                                                                                                                                                                                                   |
|  [`ExpiresAtUtc`](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) (absolute-expiry-time) | 有効期限切れが原因でメッセージが削除対象としてマークされ、エンティティから取得できなくなった UTC 時刻。 有効期限は **TimeToLive** プロパティによって制御されるため、このプロパティは EnqueuedTimeUtc と TimeToLive に基づいて計算されます。 このプロパティは読み取り専用です。                                                                                                                                                                           |
| [`ForcePersistence`](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence)                      | [`EnableExpress`](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress) フラグが設定されたキューまたはトピックの場合に、このプロパティを設定すると、メッセージを確認前にディスクに保存するように指定できます。 この動作は、エクスプレス エンティティ以外のすべてのエンティティに対する標準の動作です。                                                                                                                                                                                                         |
| [`Label`](/dotnet/api/microsoft.azure.servicebus.message.label) (subject)                       | このプロパティを使用すると、アプリケーションは、電子メールの件名行と同様に標準化された方法でメッセージの目的を受信者に示すことができます。                                                                                                                                                                                                                                                                                  |
| [`LockedUntilUtc`](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc)                        | このプロパティは、ロックされた状態 (事前設定ではなくピーク ロック受信モード) で取得されたメッセージの場合に、メッセージがキュー/サブスクリプション内でロック状態に保持される期限の UTC 時刻を示します。 ロックが有効期限切れになると [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deliverycount) が増分され、メッセージが再び取得可能になります。 このプロパティは読み取り専用です。                                                                                                                         |
| [`LockToken`](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.locktoken)                             | ロック トークンは、ブローカーが "*ピーク ロック*" 受信モードで保持しているロックへの参照です。 このトークンを使用すると、[遅延](message-deferral.md) API を使用してロックをピン留めし、メッセージを通常の配信状態フローから除外することができます。 このプロパティは読み取り専用です。                                                                                                                                                               |
| [`MessageId`](/dotnet/api/microsoft.azure.servicebus.message.messageid) (message-id)                | メッセージ識別子は、アプリケーションによって定義される、メッセージとそのペイロードを一意に識別する値です。 この識別子は自由形式の文字列で、GUID またはアプリケーションのコンテキストから派生した識別子を反映することができます。 有効にした場合、[重複データ検出](duplicate-detection.md)機能は、同じ **MessageId** を持つメッセージの 2 回目以降の送信を識別して削除します。                                                                |
| [`PartitionKey`](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)                          | [パーティション分割されたエンティティ](service-bus-partitioning.md)の場合、この値を設定すると、関連するメッセージを同じ内部パーティションに割り当てて、送信順序が正しく記録されるようにできます。 パーティションはハッシュ関数でこの値を介して選択され、直接選択することはできません。 セッションを認識するエンティティの場合、この値は **SessionId** プロパティによってオーバーライドされます。                                                                                                       |
| [`ReplyTo`](/dotnet/api/microsoft.azure.servicebus.message.replyto) (reply-to)                    | これはアプリケーションによって定義される省略可能な値であり、メッセージの受信者への応答パスを表す標準的な方法です。 応答が必要な場合、送信者は、この値を応答の送信先にするキューまたはトピックの絶対または相対パスに設定します。                                                                                                                                           |
| [`ReplyToSessionId`](/dotnet/api/microsoft.azure.servicebus.message.replytosessionid) (reply-to-group-id)  | この値は、**ReplyTo** の情報を補足し、応答が応答エンティティに送信されるときに設定する必要がある **SessionId** を指定します。                                                                                                                                                                                                                                                                            |
| [`ScheduledEnqueueTimeUtc`](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc)               | このプロパティは、遅延後にのみ取得可能になるメッセージの場合に、メッセージが論理的にエンキューされ、シーケンス処理されて取得可能になる UTC 時刻を定義します。                                                                                                                                                                                                                 |
| [`SequenceNumber`](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber)                        | このシーケンス番号は、メッセージがブローカーおよび関数によって受け入れおよび格納されるときに真の識別子として割り当てられる 64 ビットの整数です。 パーティション分割されたエンティティの場合、最上位の 16 ビットはパーティション識別子を表します。 シーケンス番号はギャップなしで単調に増分されます。 48 - 64 ビット範囲が使い果たされると、0 にロールオーバーされます。 このプロパティは読み取り専用です。                                                                |
| [`SessionId`](/dotnet/api/microsoft.azure.servicebus.message.sessionid) (group-id)                  | この値はアプリケーションによって定義され、セッションを認識するエンティティの場合に、メッセージのセッションへの所属を指定します。 同じセッション識別子を持つメッセージがまとめてロックされ、正確な順序での処理と多重化が可能になります。 セッションを認識しないエンティティの場合、この値は無視されます。                                                                                                                                     |
| [`Size`](/dotnet/api/microsoft.azure.servicebus.message.size)                                  | ストレージ クォータに対してカウントされる際に、ブローカー ログに格納されているメッセージのサイズをバイト数で示します。 このプロパティは読み取り専用です。                                                                                                                                                                                                                                                                                                       |
| [`State`](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.state)                                 | ログ内のメッセージの状態を示します。 このプロパティはメッセージの参照中 ("ピーク") にのみ関係し、メッセージがキューの先頭に到達したときに "アクティブ" で取得可能かどうか、メッセージが遅延しているかどうか、またはスケジュールされるのを待機しているかどうかを示します。 このプロパティは読み取り専用です。                                                                                                                                           |
| [`TimeToLive`](/dotnet/api/microsoft.azure.servicebus.message.timetolive)                            | この値は、**EnqueueTimeUtc** でキャプチャされた、メッセージがブローカーによって受け入れおよび格納された時刻から、メッセージが有効期限切れになるまでの相対的な期間です。 明示的に設定しない場合、それぞれのキューまたはトピックの **DefaultTimeToLive** が値として見なされます。 メッセージレベルの **TimeToLive** 値をエンティティの **DefaultTimeToLive** の設定より長くすることはできません。 長くした場合、自動的に調整されます。 |
| [To](/dotnet/api/microsoft.azure.servicebus.message.to) (to)                               | このプロパティはルーティング シナリオでの将来の使用のために予約されており、現在はブローカー自体で無視されます。 アプリケーションは、ルールに基づく自動転送チェーンのシナリオでこの値を使用して、メッセージの目的の論理送信先を示すことができます。                                                                                                                                                                                   |
| [`ViaPartitionKey`](/dotnet/api/microsoft.azure.servicebus.message.viapartitionkey)                       | メッセージがトランザクションのスコープ内の転送キュー経由で送信される場合、この値によって転送キューのパーティションが選択されます。                                                                                                                                                                                                                                                                                                                 |

この抽象メッセージ モデルでは、メッセージを HTTPS を介してキューに送信し、AMQP を介して取得することができます。 いずれの場合も、メッセージはそれぞれのプロトコルのコンテキストで正常と見なされます。 ブローカー プロパティは必要に応じて変換され、ユーザー プロパティはそれぞれのプロトコルのメッセージ モデルで最適な場所にマップされます。 ユーザー プロパティは、HTTP では直接 HTTP ヘッダーと相互にマップされ、AMQP では **application-properties** マップと相互にマップされます。

## <a name="message-routing-and-correlation"></a>メッセージのルーティングと相関関係

アプリケーションが特定の宛先にメッセージをルーティングできるように、上記のブローカー プロパティのサブセット (具体的には、[To](/dotnet/api/microsoft.azure.servicebus.message.to)、[ReplyTo](/dotnet/api/microsoft.azure.servicebus.message.replyto)、[ReplyToSessionId](/dotnet/api/microsoft.azure.servicebus.message.replytosessionid)、[MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid)、[CorrelationId](/dotnet/api/microsoft.azure.servicebus.message.correlationid)、および [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid)) が使用されます。 この機能を示すために、いくつかのパターンについて考えてみましょう。

- **単純な要求/応答**: 公開元がキューにメッセージを送信し、メッセージ コンシューマーからの応答を待ちます。 応答を受信するために、公開元は応答が配信されると予想されるキューを所有します。 そのキューのアドレスは、送信メッセージの **ReplyTo** プロパティで示されます。 コンシューマーが応答するときは、処理されるメッセージの **MessageId** を応答メッセージの **CorrelationId** プロパティにコピーし、**ReplyTo** プロパティで示された宛先にメッセージを配信します。 アプリケーションのコンテキストによっては、1 つのメッセージから複数の応答が生成される場合もあります。
- **マルチキャストの要求/応答**: 前のパターンのバリエーションとして、公開元がトピックにメッセージを送信し、複数のサブスクライバーがメッセージを使用する資格を持ちます。 各サブスクライバーは、上記の方法で応答することができます。 このパターンは検出またはロール呼び出しのシナリオで使用され、応答者は通常、ユーザー プロパティによって、またはペイロード内で自身を識別します。 **ReplyTo** がトピックをポイントしている場合、そのような検出応答のセットを対象ユーザーに配布できます。
- **多重化**: このセッション機能により、1 つのキューまたはサブスクリプションで関連するメッセージのストリームを多重化して、受信者がロックされたセッションを保持している間に、一致する **SessionId** 値で識別された関連するメッセージの各セッション (またはグループ) が特定の受信者にルーティングされるようにできます。 セッションの詳細については、[こちら](message-sessions.md)をご覧ください。
- **多重化された要求/応答**: このセッション機能を使用すると、多重化された応答を有効にして、複数の発行元が応答キューを共有できるようになります。 発行元は **ReplyToSessionId** を設定することにより、その値を応答メッセージの **SessionId** プロパティにコピーするようにコンシューマーに指示できます。 発行キューまたはトピックがセッションを認識する必要はありません。 メッセージが送信されると、発行元はセッション レシーバーを条件付きで受け入れることにより、特定の **SessionId** を持つセッションだけを待機してキューで具体化できます。 

Service Bus 名前空間内のルーティングは、自動転送チェーンとトピック サブスクリプション規則によって実現できます。 名前空間の間のルーティングは、[Azure LogicApps の使用](https://azure.microsoft.com/services/logic-apps/)によって実現できます。 前の一覧に示したように、**To** プロパティは将来の使用のために予約されており、最終的には特別に有効にされた機能を持つブローカーによって解釈される可能性があります。 ルーティングを実装するアプリケーションは、**To** プロパティを使用せずにユーザー プロパティに基づいて実装する必要があります。ただし、現時点では To プロパティを使用しても互換性の問題は発生しません。

## <a name="payload-serialization"></a>ペイロードのシリアル化

ペイロードは、転送中または Service Bus 内に格納されているときには常に、不透明なバイナリ ブロックです。 [ContentType](/dotnet/api/microsoft.azure.servicebus.message.contenttype) プロパティにより、アプリケーションは IETF RFC2045 に従ってペイロードを記述できます。これはプロパティ値に対する推奨形式である MIME コンテンツ タイプの記述です (例: `application/json;charset=utf-8`)。

Java または .NET Standard バージョンとは異なり、.NET Framework バージョンの Service Bus API では、任意の .NET オブジェクトをコンストラクターに渡して **BrokeredMessage** インスタンスを作成することができます。 

レガシ SBMP プロトコルを使用する場合、それらのオブジェクトは、既定のバイナリ シリアライザーまたは外部から提供されたシリアライザーを使用してシリアル化されます。 AMQP プロトコルを使用する場合、オブジェクトは AMQP オブジェクトにシリアル化されます。 受信側は、必要な型を指定して [GetBody\<T>()](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) メソッドを実行することで、これらのオブジェクトを取得できます。 AMQP の場合、オブジェクトは `ArrayList` および `IDictionary<string,object>` の各オブジェクトの AMQP グラフにシリアル化され、どの AMQP クライアントでも復号化できます。 

この見えないシリアル化のマジックは便利ですが、アプリケーションはオブジェクトのシリアル化を明示的に制御して、オブジェクト グラフをメッセージに含める前にストリームに変換し、受信側ではその逆の操作を行う必要があります。 これは相互運用可能な結果を生成します。 また、AMQP には強力なバイナリ エンコード モデルがありますが、それは AMQP メッセージング エコシステムに関連付けられており、HTTP クライアントがそのようなペイロードをデコードすると問題が発生します。 

.NET Standard および Java バージョンの API はバイト配列しか受け入れません。つまり、アプリケーションはオブジェクトのシリアル化制御を処理する必要があります。 

## <a name="next-steps"></a>次のステップ

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)

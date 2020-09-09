---
title: Azure Event Hubs - 例外
description: この記事では、Azure Event Hubs メッセージングの例外と推奨アクションの一覧を示します。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: a93daa88c468a22838a6f9012f0c4622447f5555
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512369"
---
# <a name="event-hubs-messaging-exceptions---net"></a>Event Hubs メッセージングの例外 - .NET
このセクションでは、.NET Framework API によって生成される .NET 例外を示します。 

## <a name="exception-categories"></a>例外のカテゴリ

Event Hubs の API .NET で生成される例外をカテゴリ別に分類し、修復のために実行できる関連するアクションと共に以下に示します。

 - ユーザー コードのエラー: 
 
   - [System.ArgumentException](/dotnet/api/system.argumentexception?view=netcore-3.1)
   - [System.InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=netcore-3.1)
   - [System.OperationCanceledException](/dotnet/api/system.operationcanceledexception?view=netcore-3.1)
   - [System.Runtime.Serialization.SerializationException](/dotnet/api/system.runtime.serialization.serializationexception?view=netcore-3.1)
   
   一般的なアクション:処理を続行する前にコードの修正を試みます。
 
 - セットアップ/構成エラー: 
 
   - [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception)
   - [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception)
   - [System.UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1)
   
   一般的なアクション:構成を確認し、必要に応じて変更します。
   
 - 一時的な例外: 
 
   - [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception)
   - [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception)
   - [Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception)
   - [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)
   
   一般的なアクション:操作を再試行するか、またはユーザーに通知します。
 
 - その他の例外: 
 
   - [System.Transactions.TransactionException](/dotnet/api/system.transactions.transactionexception?view=netcore-3.1)
   - [System.TimeoutException](#timeoutexception)
   - [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception)
   - [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception)
   
   一般的なアクション:例外の種類に固有です。次のセクションの表を参照してください。 

## <a name="exception-types"></a>例外の種類
次の表は、メッセージングの例外の種類および原因と実行できる推奨アクションを示したものです。

| 例外の種類 | 説明/原因/例 | 推奨アクション | 自動/即時再試行に関する注意 |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1) |サーバーは、[OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) によって制御される指定された時間内に、要求された操作に対して応答しませんでした。 サーバーで、要求された操作が完了した可能性があります。 この例外は、ネットワークや他のインフラストラクチャの遅延が原因で発生することがあります。 |システム状態の整合性をチェックして、必要な場合は再試行してください。<br /> 「[TimeoutException](#timeoutexception)」を参照してください。 | 再試行によって解決する場合があります。再試行ロジックをコードに追加してください。 |
| [InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=netcore-3.1) |要求されたユーザー操作は、サーバーまたはサービス内で許可されていません。 詳細については、例外メッセージを参照してください。 たとえば、 [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) は、 [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) モードでメッセージを受信した場合に、この例外を生成します。 | コードとドキュメントを確認します。 要求した操作が有効なことを確かめてください。 | 再試行によって解決することはありません。 |
| [OperationCanceledException](/dotnet/api/system.operationcanceledexception?view=netcore-3.1) | 既に終了、中止、または破棄されたオブジェクトに対して操作を呼び出そうとしました。 まれに、アンビエント トランザクションが既に破棄されている場合があります。 | コードを確認し、破棄されたオブジェクトに対して操作を呼び出していないことを確認します。 | 再試行によって解決することはありません。 |
| [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1) | [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) オブジェクトはトークンを取得できませんでした。トークンが無効です。または、操作の実行に必要な要求がトークンに含まれていません。 | トークン プロバイダーが正しい値を使用して作成されていることを確認します。 Access Control Service の構成を確認します。 | 再試行によって解決する場合があります。再試行ロジックをコードに追加してください。 |
| [ArgumentException](/dotnet/api/system.argumentexception?view=netcore-3.1)<br /> [ArgumentNullException](/dotnet/api/system.argumentnullexception?view=netcore-3.1)<br />[ArgumentOutOfRangeException](/dotnet/api/system.argumentoutofrangeexception?view=netcore-3.1) | メソッドに指定された 1 つまたは複数の引数が無効です。 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) または [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) に指定された URI にパス セグメントが含まれています。 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) または [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) に指定された URI スキームが無効です。 プロパティ値が 32 KB を超えています。 | 呼び出し元のコードを確認し、引数が正しいことを確かめます。 | 再試行によって解決することはありません。 |
| [Microsoft.ServiceBus.Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft.Azure.EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | 操作に関連付けられているエンティティが存在しないか、削除されました。 | エンティティが存在することを確認します。 | 再試行によって解決することはありません。 |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | クライアントから Event Hub への接続を確立できません。 |指定されたホスト名が正しく、ホストが到達可能なことを確認してください。 | 断続的な接続の問題がある場合は、再試行によって解決することがあります。 |
| [Microsoft.ServiceBus.Messaging ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft.Azure.EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | この時点では、このサービスで要求を処理できません。 | クライアントは、しばらく待機してから操作をやり直すことができます。 <br /> 「[ServerBusyException](#serverbusyexception)」を参照してください。 | クライアントは、一定の間隔をおいてから再試行することができます。 再試行の結果として別の例外が発生した場合は、その例外の再試行動作を確認します。 |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | 次の場合にスローされる可能性がある一般なメッセージング例外です。異なるエンティティの種類 (たとえば、トピック) に属する名前またはパスを使用して、[QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) を作成しようとした場合。 1 MB を超えるメッセージを送信しようとした場合。 サーバーまたはサービスで要求の処理中にエラーが発生しました。 詳細については、例外メッセージを参照してください。 この例外は通常、一時的な例外です。 | コードを確認し、メッセージ本文にシリアル化可能なオブジェクトのみを使用していることを確かめます (または、カスタム シリアライザーを使用します)。 サポートされているプロパティ値の型をドキュメントで確認し、サポートされている型だけを使用します。 [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) プロパティを確認します。 それが **true**である場合は、操作を再試行できます。 | 再試行動作は未定義であり、解決にならない場合もあります。 |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | そのサービスの名前空間で別のエンティティによって既に使用されている名前を持つエンティティを作成しようとしました。 | 既存のエンティティを削除するか、作成するエンティティに別の名前を選択します。 | 再試行によって解決することはありません。 |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | メッセージング エンティティが最大許容サイズに達しています。 この例外は、受信者の最大数 (5) がコンシューマー グループ レベルで既に開かれている場合に発生する可能性があります。 | エンティティまたはそのサブキューからメッセージを受信して、エンティティ内に領域を作成します。 <br /> 「[QuotaExceededException](#quotaexceededexception)」を参照してください | メッセージがそれまでに削除されている場合は、再試行によって解決することがあります。 |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | 無効になっているエンティティに対してランタイム操作を要求しました。 |エンティティをアクティブ化します。 | エンティティがそれまでにアクティブ化されている場合は、再試行によって解決することがあります。 |
| [Microsoft.ServiceBus.Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft.Azure.EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | メッセージ ペイロードが 1 MB の制限を超えています。 この 1 MB の制限はメッセージの合計サイズであり、システム プロパティや .NET のオーバーヘッドも含めたサイズです。 | メッセージ ペイロードのサイズを小さくし、操作を再試行します。 |再試行によって解決することはありません。 |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) は、特定のエンティティのクォータが超過していることを示します。

この例外は、受信者の最大数 (5) がコンシューマー グループ レベルで既に開かれている場合に発生する可能性があります。

### <a name="event-hubs"></a>Event Hubs
Event Hubs には、Event Hub あたり 20 個のコンシューマー グループという上限があります。 それ以上作成しようとすると、 [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception)が発生します。 

## <a name="timeoutexception"></a>TimeoutException
[TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1) は、ユーザーが開始した操作が操作タイムアウトより時間がかかっていることを示します。 

Event Hubs では、タイムアウトは接続文字列の一部として、または [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder)を通じて指定されます。 エラー メッセージ自体はさまざまですが、これには常に現在の操作に指定されたタイムアウト値が含まれます。 

### <a name="common-causes"></a>一般的な原因
この例外の一般的な原因には、次の 2 つがあります。正しくない構成と、一時的なサービス エラーです。

- **構成が正しくない** 操作状態に対して、操作タイムアウトが小さすぎる可能性があります。 クライアント SDK の操作タイムアウトの既定値は 60 秒です。 コードに小さすぎる値を設定していないかどうかを確認します。 ネットワークの状態と CPU 使用率は、特定の操作が完了する時間に影響します。このため、操作タイムアウトに小さい値を設定しないでください。
- **一時的なサービス エラー** Event Hubs サービスで、要求の処理に遅延が発生する場合 (トラフィック量の多い場合など) があります。 このような場合、操作が成功するまで、遅延後に操作を再試行できます。 複数回試行しても同じ操作が失敗する場合は、[Azure のサービス状態のサイト](https://azure.microsoft.com/status/)にアクセスして、既知のサービス停止がないかどうかを確認してください。

## <a name="serverbusyexception"></a>ServerBusyException

[Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) または [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) はサーバーが過負荷になっていることを示します。 この例外には関連するエラー コードが 2 つあります。

### <a name="error-code-50002"></a>エラー コード 50002
このエラーが発生する原因は次の 2 ついずれかです。

- イベント ハブ上のすべてのパーティション間で負荷が均等に分散されていません。パーティションの 1 つがローカルのスループット単位制限に達しています。
    
    **解決方法**:パーティション分散戦略を変更するか、[EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) を実行すると、問題の解決に役立つ場合があります。

- Event Hubs 名前空間に十分なスループット ユニットがありません ([Azure portal](https://portal.azure.com) の Event Hubs 名前空間ウィンドウで **[メトリック]** 画面を確認します)。 ポータルに集計 (1 分) 情報が表示されていますが、スループットはリアルタイムで測定しているため、これは単なる推定値です。

    **解決方法**:名前空間のスループット単位数を増やすと、問題の解決に役立つ場合があります。 この操作は、ポータルの Event Hubs 名前空間画面の **[スケール]** ウィンドウで実行できます。 または、[自動インフレ](event-hubs-auto-inflate.md)を使用することができます。

### <a name="error-code-50001"></a>エラー コード 50001

このエラーはあまり発生しません。 名前空間に対してコードを実行しているコンテナーの CPU が不足している場合、つまり、Event Hubs ロード バランサーの開始前の時間が数秒もないときに発生します。

**解決方法**:GetRuntimeInformation メソッドの呼び出しの制限。 Azure Event Hubs は、GetRuntimeInfo に対して 1 秒あたり最大 50 の呼び出しをサポートしています。 制限に達すると、次のような例外が表示される可能性があります。

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```


## <a name="next-steps"></a>次のステップ

Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs の概要](./event-hubs-about.md)
* [Event Hub を作成する](event-hubs-create.md)
* [Event Hubs の FAQ](event-hubs-faq.md)

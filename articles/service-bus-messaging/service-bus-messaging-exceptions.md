---
title: Azure Service Bus メッセージングの例外 | Microsoft Docs
description: Service Bus メッセージングの例外と推奨アクションの一覧。
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: aschhab
ms.openlocfilehash: b90e87310bf6dec505176b7f4d4cb9e15ac57c20
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2019
ms.locfileid: "55891812"
---
# <a name="service-bus-messaging-exceptions"></a>Service Bus メッセージングの例外
この記事では、Microsoft Azure Service Bus のメッセージング API によって生成される例外を示します。 このリファレンスは変更される可能性があるため、最新情報をご確認ください。

## <a name="exception-categories"></a>例外のカテゴリ
メッセージング API で生成される例外をカテゴリ別に分類し、修復のために実行できる関連するアクションと共に以下に示します。 例外の意味と原因は、メッセージング エンティティの種類によって異なる場合があります。

1. ユーザー コードのエラー ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)、[System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx)、[System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx)、[System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx))。 一般アクション: 処理を実行する前にコードの修正を試みます。
2. セットアップ/構成エラー ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception)、[System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx))。 一般アクション: 構成を確認し、必要に応じて変更します。
3. 一時的な例外 ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception)、[Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception)、[Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception))。 一般アクション: 操作をやり直すか、ユーザーに通知します。 クライアント SDK の `RetryPolicy` クラスを、再試行を自動的に処理するように構成できます。 詳細については、[再試行のガイダンス](/azure/architecture/best-practices/retry-service-specific#service-bus)に関するページを参照してください。
4. その他の例外 ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx)、[System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)、[Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception)、[Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception))。 全般的なアクション: 例外の種類に固有。次のセクションの表を参照してください。 

## <a name="exception-types"></a>例外の種類
次の表は、メッセージングの例外の種類および原因と実行できる推奨アクションを示したものです。

| **例外の種類** | **説明/原因/例** | **推奨アクション** | **自動/即時再試行に関する注意** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |サーバーは、 [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings)によって制御される指定された時間内に、要求された操作に対して応答しませんでした。 サーバーで、要求された操作が完了した可能性があります。 これは、ネットワークや他のインフラストラクチャの遅延が原因で発生することがあります。 |システム状態の整合性をチェックして、必要な場合は再試行してください。 「 [TimeoutException](#timeoutexception)」を参照してください。 |再試行によって解決する場合があります。再試行ロジックをコードに追加してください。 |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |要求されたユーザー操作はサーバーまたはサービス内で許可されていません。 詳細については、例外メッセージを参照してください。 たとえば、[Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) は、[ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) モードでメッセージを受信した場合に、この例外を生成します。 |コードとドキュメントを確認します。 要求した操作が有効なことを確かめてください。 |再試行は役に立ちません。 |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |既に終了、中止、または破棄されたオブジェクトに対して操作を呼び出そうとしました。 まれに、アンビエント トランザクションが既に破棄されている場合があります。 |コードを確認し、破棄されたオブジェクトに対して操作を呼び出していないことを確かめます。 |再試行は役に立ちません。 |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) オブジェクトはトークンを取得できませんでした。トークンが無効です。または、操作の実行に必要な要求がトークンに含まれていません。 |トークン プロバイダーが正しい値を使用して作成されていることを確認します。 Access Control Service の構成を確認します。 |再試行によって解決する場合があります。再試行ロジックをコードに追加してください。 |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |メソッドに指定された 1 つまたは複数の引数が無効です。<br /> [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) または [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) に指定された URI にパス セグメントが含まれています。<br /> [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) または [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) に指定された URI スキームが無効です。 <br />プロパティ値が 32 KB を超えています。 |呼び出し元のコードを確認し、引数が正しいことを確かめます。 |再試行は役に立ちません。 |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |操作に関連付けられているエンティティが存在しないか、削除されました。 |エンティティが存在することを確認します。 |再試行は役に立ちません。 |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |特定のシーケンス番号を持つメッセージを受信しようとしました。 このメッセージが見つかりません。 |メッセージがまだ受信されていないことを確認します。 配信不能キューを確認し、メッセージが配信不能になっているかどうかを確かめます。 |再試行は役に立ちません。 |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |クライアントから Service Bus への接続を確立できません。 |指定されたホスト名が正しく、ホストが到達可能なことを確認してください。 |断続的な接続の問題がある場合は、再試行によって解決することがあります。 |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |この時点では、このサービスで要求を処理できません。 |クライアントは、しばらく待機してから操作をやり直すことができます。 |クライアントは、一定の間隔をおいてから再試行することができます。 再試行の結果として別の例外が発生した場合は、その例外の再試行動作を確認します。 |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |メッセージに関連付けられているロック トークンが期限切れになったか、ロック トークンが見つかりません。 |メッセージを破棄してください。 |再試行は役に立ちません。 |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |このセッションに関連付けられているロックが失われました。 |[MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) オブジェクトを中止してください。 |再試行は役に立ちません。 |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |次の場合にスローされる可能性がある一般なメッセージング例外です。<br /> 異なるエンティティの種類 (たとえば、トピック) に属する名前またはパスを使用して、[QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) を作成しようとした場合。<br />  256 KB を超えるメッセージを送信しようとした場合。 サーバーまたはサービスで要求の処理中にエラーが発生しました。 詳細については、例外メッセージを参照してください。 これは通常、一時的な例外です。 |コードを確認し、メッセージ本文にシリアル化可能なオブジェクトのみを使用していることを確かめます (または、カスタム シリアライザーを使用します)。 サポートされているプロパティ値の型をドキュメントで確認し、サポートされている型だけを使用します。 [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) プロパティを確認します。 それが **true**である場合は、操作を再試行できます。 |再試行動作は未定義であり、解決にならない場合もあります。 |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |そのサービスの名前空間で別のエンティティによって既に使用されている名前を持つエンティティを作成しようとしました。 |既存のエンティティを削除するか、作成するエンティティに別の名前を選択します。 |再試行は役に立ちません。 |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |メッセージング エンティティが最大許容サイズに達したか、名前空間への最大接続数を超えました。 |エンティティまたはそのサブキューからメッセージを受信して、エンティティ内に領域を作成します。 「 [QuotaExceededException](#quotaexceededexception)」を参照してください。 |メッセージがそれまでに削除されている場合は、再試行によって解決することがあります。 |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |無効なルール アクションを作成しようとした場合、Service Bus からこの例外が返されます。 メッセージのルール アクションの処理中にエラーが発生した場合、Service Bus はその配信不能なメッセージにこの例外をアタッチします。 |ルール アクションが正しいことを確認してください。 |再試行は役に立ちません。 |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |無効なフィルターを作成しようとした場合、Service Bus からこの例外が返されます。 メッセージのフィルターの処理中にエラーが発生した場合、Service Bus はその配信不要なメッセージにこの例外をアタッチします。 |フィルターが正しいことを確認してください。 |再試行は役に立ちません。 |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |特定のセッション ID を持つセッションを使用しようとしましたが、セッションは現在別のクライアントによってロックされています。 |別のクライアントによるセッションのロックが解除されたことを確認します。 |セッションがそれまでに解放されている場合は、再試行によって解決することがあります。 |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |トランザクションの一部になっている操作が多すぎます。 |このトランザクションの一部である操作の数を減らします。 |再試行は役に立ちません。 |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |無効になっているエンティティに対してランタイム操作を要求しました。 |エンティティをアクティブ化します。 |エンティティがそれまでにアクティブ化されている場合は、再試行によって解決することがあります。 |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |事前フィルター処理が有効になっていて、一致するフィルターのないトピックにメッセージを送信した場合、Service Bus からこの例外が返されます。 |少なくとも 1 つのフィルターに一致することを確認します。 |再試行は役に立ちません。 |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |メッセージ ペイロードが 256 KB の制限を超えています。 ただし 256 KB の制限はメッセージの合計サイズであり、システム プロパティや .NET のオーバーヘッドも含めたサイズです。 |メッセージ ペイロードのサイズを小さくし、操作を再試行します。 |再試行は役に立ちません。 |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |アンビエント トランザクション (*Transaction.Current*) が無効です。 トランザクションは完了または中止された可能性がありますがなります。 内部例外で追加情報が提供される場合があります。 | |再試行は役に立ちません。 |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |未確定トランザクションに対して操作が試行されたか、トランザクションのコミットが試行され、トランザクションが未確定になりました。 |トランザクションは既にコミットされた可能性があるため、アプリケーションはこの例外を (特殊なケースとして) 処理する必要があります。 |- |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) は、特定のエンティティのクォータが超過していることを示します。

### <a name="queues-and-topics"></a>キューとトピック
キューとトピックは、通常、キューのサイズに関連します。 エラー メッセージのプロパティには、次の例のようにさらに詳しい情報が含まれます。

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: ‘xxx-xxx-xxx’. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

メッセージは、トピックがそのサイズの上限を超えたことを示します (この場合 1 GB (既定のサイズ上限))。 

### <a name="namespaces"></a>名前空間

名前空間の場合、[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) はアプリケーションが名前空間への最大接続数を超えたことを示す場合があります。 例: 

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

#### <a name="common-causes"></a>一般的な原因
このエラーには 2 つの一般的な原因があります。配信不能キューと機能しないメッセージ受信者です。

1. **[配信不能キュー](service-bus-dead-letter-queues.md)** リーダーがメッセージを完了できない状態でロックの有効期限が切れたときにメッセージがキュー/トピックに返されます。 これは、リーダーが [BrokeredMessage.Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete)を呼び出せない例外がリーダーに発生した場合に発生することがあります。 メッセージは 10 回読み取られた後、既定で配信不能キューに移動します。 この動作は [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) プロパティによって制御され、既定値は 10 です。 メッセージが配信不能キューに溜まるほど、領域が占有されます。
   
    この問題を解決するには、他のキューの場合と同様に、配信不能キューからメッセージを読み取り、完了します。 配信不能キューのパスのフォーマットに役立つ [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) メソッドを使用できます。
2. **受信者が停止しました**。 受信者によるキューまたはサブスクリプションからのメッセージの受信が停止されています。 これを特定するには、メッセージの完全な詳細情報を表示する [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) プロパティを確認します。 [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) プロパティ値が大きいか増えている場合は、メッセージが読み取られる速度が書き込まれる速度に追いついていません。

## <a name="timeoutexception"></a>TimeoutException
[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) は、ユーザーが開始した操作が操作タイムアウトより時間がかかっていることを示します。 

[ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) プロパティの値を確認する必要があります。この制限に達した場合も、[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) が発生する可能性があります。

### <a name="queues-and-topics"></a>キューとトピック
キューとトピックでは、タイムアウトは [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) プロパティで接続文字列の一部として、または [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder) を通じて指定されます。 エラー メッセージ自体はさまざまですが、これには常に現在の操作に指定されたタイムアウト値が含まれます。 



## <a name="next-steps"></a>次の手順

Service Bus の詳細な .NET API リファレンスについては、「[Azure .NET API reference](/dotnet/api/overview/azure/service-bus)」(Azure .NET API リファレンス) を参照してください。

[Service Bus](https://azure.microsoft.com/services/service-bus/) の詳細については、次の記事をご覧ください。

* [Service Bus メッセージングの概要](service-bus-messaging-overview.md)
* [Service Bus のアーキテクチャ](service-bus-architecture.md)


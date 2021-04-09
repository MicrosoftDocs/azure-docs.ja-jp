---
title: Azure Service Bus - メッセージングの例外 | Microsoft Docs
description: この記事では、Azure Service Bus メッセージングの例外と、例外が発生したときに実行する推奨アクションの一覧を示します。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 3b56aff2635593d6cb49adbcf3784ddd5cb4fa39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99219147"
---
# <a name="service-bus-messaging-exceptions"></a>Service Bus メッセージングの例外
この記事では、.NET Framework API によって生成される .NET 例外を一覧表示します。 

## <a name="exception-categories"></a>例外のカテゴリ
メッセージング API で生成される例外をカテゴリ別に分類し、修復のために実行できる関連するアクションと共に以下に示します。 例外の意味と原因は、メッセージング エンティティの種類によって異なる場合があります。

1. ユーザー コードのエラー ([System.ArgumentException](/dotnet/api/system.argumentexception)、[System.InvalidOperationException](/dotnet/api/system.invalidoperationexception)、[System.OperationCanceledException](/dotnet/api/system.operationcanceledexception)、[System.Runtime.Serialization.SerializationException](/dotnet/api/system.runtime.serialization.serializationexception))。 一般アクション: 処理を実行する前にコードの修正を試みます。
2. セットアップ/構成エラー ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception)、[System.UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception))。 一般アクション: 構成を確認し、必要に応じて変更します。
3. 一時的な例外 ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception)、[Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception)、[Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception))。 一般アクション: 操作をやり直すか、ユーザーに通知します。 クライアント SDK の `RetryPolicy` クラスは、再試行を自動的に処理するように構成できます。 詳細については、[再試行のガイダンス](/azure/architecture/best-practices/retry-service-specific#service-bus)を参照してください。
4. その他の例外 ([System.Transactions.TransactionException](/dotnet/api/system.transactions.transactionexception)、[System.TimeoutException](/dotnet/api/system.timeoutexception)、[Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception)、[Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception))。 全般的なアクション: 例外の種類に固有。次のセクションの表を参照してください。 

## <a name="exception-types"></a>例外の種類
次の表は、メッセージングの例外の種類および原因と実行できる推奨アクションを示したものです。

| **例外の種類** | **説明/原因/例** | **推奨アクション** | **自動/即時再試行に関する注意** |
| --- | --- | --- | --- |
| [TimeoutException](/dotnet/api/system.timeoutexception) |サーバーは、[OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) によって制御される指定された時間内に、要求された操作に対して応答しませんでした。 サーバーで、要求された操作が完了した可能性があります。 これは、ネットワークや他のインフラストラクチャの遅延が原因で発生することがあります。 |システム状態の整合性をチェックして、必要な場合は再試行してください。 「 [TimeoutException](#timeoutexception)」を参照してください。 |再試行によって解決する場合があります。再試行ロジックをコードに追加してください。 |
| [InvalidOperationException](/dotnet/api/system.invalidoperationexception) |要求されたユーザー操作は、サーバーまたはサービス内で許可されていません。 詳細については、例外メッセージを参照してください。 たとえば、[Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) は、[ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) モードでメッセージを受信した場合に、この例外を生成します。 |コードとドキュメントを確認します。 要求した操作が有効なことを確かめてください。 |再試行は役に立ちません。 |
| [OperationCanceledException](/dotnet/api/system.operationcanceledexception) |既に終了、中止、または破棄されたオブジェクトに対して操作を呼び出そうとしました。 まれに、アンビエント トランザクションが既に破棄されている場合があります。 |コードを確認し、破棄されたオブジェクトに対して操作を呼び出していないことを確認します。 |再試行は役に立ちません。 |
| [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception) |[TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) オブジェクトはトークンを取得できませんでした。トークンが無効です。または、操作の実行に必要な要求がトークンに含まれていません。 |トークン プロバイダーが正しい値を使用して作成されていることを確認します。 Access Control Service の構成を確認します。 |再試行によって解決する場合があります。再試行ロジックをコードに追加してください。 |
| [ArgumentException](/dotnet/api/system.argumentexception)<br /> [ArgumentNullException](/dotnet/api/system.argumentnullexception)<br />[ArgumentOutOfRangeException](/dotnet/api/system.argumentoutofrangeexception) |メソッドに指定された 1 つまたは複数の引数が無効です。<br /> [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) または [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) に指定された URI にパス セグメントが含まれています。<br /> [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) または [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) に指定された URI スキームが無効です。 <br />プロパティ値が 32 KB を超えています。 |呼び出し元のコードを確認し、引数が正しいことを確かめます。 |再試行は役に立ちません。 |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |操作に関連付けられているエンティティが存在しないか、削除されました。 |エンティティが存在することを確認します。 |再試行は役に立ちません。 |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |特定のシーケンス番号を持つメッセージを受信しようとしました。 このメッセージが見つかりません。 |メッセージがまだ受信されていないことを確認します。 配信不能キューを確認し、メッセージが配信不能になっているかどうかを確かめます。 |再試行は役に立ちません。 |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |クライアントは Service Bus への接続を確立できません。 |指定されたホスト名が正しく、ホストが到達可能なことを確認してください。 <p>ファイアウォール/プロキシを使用している環境でコードを実行する場合は、Service Bus のドメイン/IP アドレスとポートへのトラフィックがブロックされていないことを確認します。</p>|断続的な接続の問題がある場合は、再試行によって解決することがあります。 |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |この時点では、このサービスで要求を処理できません。 |クライアントは、しばらく待機してから操作をやり直すことができます。 |クライアントは、一定の間隔をおいてから再試行することができます。 再試行の結果として別の例外が発生した場合は、その例外の再試行動作を確認します。 |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |次の場合にスローされる可能性がある一般なメッセージング例外です。<p>異なるエンティティの種類 (たとえば、トピック) に属する名前またはパスを使用して、[QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) を作成しようとした場合。</p><p>256 KB を超えるメッセージを送信しようとした場合。 </p>サーバーまたはサービスで要求の処理中にエラーが発生しました。 詳細については、例外メッセージを参照してください。 これは通常、一時的な例外です。</p><p>エンティティが調整されているため、要求は終了されました。 エラー コード:50001、50002、50008。 </p> | コードを確認し、メッセージ本文にシリアル化可能なオブジェクトのみを使用していることを確かめます (または、カスタム シリアライザーを使用します)。 <p>サポートされているプロパティ値の型をドキュメントで確認し、サポートされている型だけを使用します。</p><p> [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) プロパティを確認します。 それが **true** である場合は、操作を再試行できます。 </p>| 制限のために例外が発生した場合は、数秒待ってから、操作を再試行してください。 再試行動作は未定義であり、他のシナリオには役に立たない可能性があります。|
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |そのサービスの名前空間で別のエンティティによって既に使用されている名前を持つエンティティを作成しようとしました。 |既存のエンティティを削除するか、作成するエンティティに別の名前を選択します。 |再試行は役に立ちません。 |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |メッセージング エンティティが最大許容サイズに達したか、名前空間への最大接続数を超えました。 |エンティティまたはそのサブキューからメッセージを受信して、エンティティ内に領域を作成します。 「 [QuotaExceededException](#quotaexceededexception)」を参照してください。 |メッセージがそれまでに削除されている場合は、再試行によって解決することがあります。 |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |無効なルール アクションを作成しようとした場合、Service Bus からこの例外が返されます。 メッセージのルール アクションの処理中にエラーが発生した場合、Service Bus はその配信不能なメッセージにこの例外をアタッチします。 |ルール アクションが正しいことを確認してください。 |再試行は役に立ちません。 |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |無効なフィルターを作成しようとした場合、Service Bus からこの例外が返されます。 メッセージのフィルターの処理中にエラーが発生した場合、Service Bus はその配信不要なメッセージにこの例外をアタッチします。 |フィルターが正しいことを確認してください。 |再試行は役に立ちません。 |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |特定のセッション ID を持つセッションを使用しようとしましたが、セッションは現在別のクライアントによってロックされています。 |別のクライアントによるセッションのロックが解除されたことを確認します。 |セッションがそれまでに解放されている場合は、再試行によって解決することがあります。 |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |トランザクションの一部になっている操作が多すぎます。 |このトランザクションの一部である操作の数を減らします。 |再試行は役に立ちません。 |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |無効になっているエンティティに対してランタイム操作を要求しました。 |エンティティをアクティブ化します。 |エンティティがそれまでにアクティブ化されている場合は、再試行によって解決することがあります。 |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |事前フィルター処理が有効になっていて、一致するフィルターのないトピックにメッセージを送信した場合、Service Bus からこの例外が返されます。 |少なくとも 1 つのフィルターに一致することを確認します。 |再試行は役に立ちません。 |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |メッセージ ペイロードが 256 KB の制限を超えています。 ただし 256 KB の制限はメッセージの合計サイズであり、システム プロパティや .NET のオーバーヘッドも含めたサイズです。 |メッセージ ペイロードのサイズを小さくし、操作を再試行します。 |再試行は役に立ちません。 |
| [TransactionException](/dotnet/api/system.transactions.transactionexception) |アンビエント トランザクション (*Transaction.Current*) が無効です。 トランザクションは完了または中止された可能性がありますがなります。 内部例外で追加情報が提供される場合があります。 | |再試行は役に立ちません。 |
| [TransactionInDoubtException](/dotnet/api/system.transactions.transactionindoubtexception) |未確定トランザクションに対して操作が試行されたか、トランザクションのコミットが試行され、トランザクションが未確定になりました。 |トランザクションは既にコミットされた可能性があるため、アプリケーションはこの例外を (特殊なケースとして) 処理する必要があります。 |- |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) は、特定のエンティティのクォータが超過していることを示します。

### <a name="queues-and-topics"></a>キューとトピック
キューとトピックは、通常、キューのサイズに関連します。 エラー メッセージのプロパティには、次の例のようにさらに詳しい情報が含まれます。

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: 'xxx-xxx-xxx'. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

メッセージは、トピックがそのサイズの上限を超えたことを示します (この場合 1 GB (既定のサイズ上限))。 

### <a name="namespaces"></a>名前空間

名前空間の場合、[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) はアプリケーションが名前空間への最大接続数を超えたことを示す場合があります。 次に例を示します。

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

### <a name="common-causes"></a>一般的な原因
このエラーには 2 つの一般的な原因があります。配信不能キューと機能しないメッセージ受信者です。

1. **[配信不能キュー](service-bus-dead-letter-queues.md)** リーダーがメッセージを完了できない状態でロックの有効期限が切れたときにメッセージがキュー/トピックに返されます。 これは、リーダーが [BrokeredMessage.Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete) を呼び出せない例外がリーダーに発生した場合に発生することがあります。 メッセージは 10 回読み取られた後、既定で配信不能キューに移動します。 この動作は [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) プロパティによって制御され、既定値は 10 です。 メッセージが配信不能キューに溜まるほど、領域が占有されます。
   
    この問題を解決するには、他のキューの場合と同様に、配信不能キューからメッセージを読み取り、完了します。 配信不能キューのパスのフォーマットに役立つ [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) メソッドを使用できます。
2. **受信者が停止しました**。 受信者によるキューまたはサブスクリプションからのメッセージの受信が停止されています。 これを特定するには、メッセージの完全な詳細情報を表示する [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) プロパティを確認します。 [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) プロパティ値が大きいか増えている場合は、メッセージが読み取られる速度が書き込まれる速度に追いついていません。

## <a name="timeoutexception"></a>TimeoutException
[TimeoutException](/dotnet/api/system.timeoutexception) は、ユーザーが開始した操作が操作タイムアウトより時間がかかっていることを示します。 

[ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) プロパティの値を確認する必要があります。この制限に達した場合も、[TimeoutException](/dotnet/api/system.timeoutexception) が発生する可能性があります。

タイムアウトは、サービスを実行するリソースに対する Service Bus サービスの更新、(または) OS の更新など、メンテナンス操作中またはその間に発生することが予想されます。 OS の更新中は、エンティティが移動され、ノードが更新または再起動されるため、タイムアウトが発生する可能性があります。 Azure Service Bus サービスのサービス レベル アグリーメント (SLA) の詳細については、「[Service Bus の SLA](https://azure.microsoft.com/support/legal/sla/service-bus/)」を参照してください。


### <a name="queues-and-topics"></a>キューとトピック
キューとトピックでは、タイムアウトは [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) プロパティで接続文字列の一部として、または [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder) を通じて指定されます。 エラー メッセージ自体はさまざまですが、これには常に現在の操作に指定されたタイムアウト値が含まれます。 

## <a name="messagelocklostexception"></a>MessageLockLostException

### <a name="cause"></a>原因

**MessageLockLostException** は、[PeekLock](message-transfers-locks-settlement.md#peeklock) 受信モードを使用してメッセージを受信し、クライアントによって保持されているロックがサービス側で期限切れになったときにスローされます。

メッセージのロックは、さまざまな理由により期限切れになる場合があります。 

  * ロック タイマーが、クライアント アプリケーションによって更新される前に期限切れになっている。
  * クライアント アプリケーションがロックを取得し、永続ストアにそれを保存してから再起動した。 再起動後、クライアント アプリケーションが、配信中のメッセージを調べて、これらを完了しようとした。

### <a name="resolution"></a>解像度

**MessageLockLostException** が発生した場合、クライアント アプリケーションはメッセージを処理できなくなります。 クライアント アプリケーションでは、必要に応じて、分析のために例外をログに記録することもできますが、クライアントはメッセージを破棄する "*必要があります*"。

メッセージのロックの有効期限が切れたため、キュー (またはサブスクリプション) に戻り、receive を呼び出す次のクライアント アプリケーションで処理できるようになります。

**MaxDeliveryCount** を超えた場合、メッセージは **DeadLetterQueue** に移動される可能性があります。

## <a name="sessionlocklostexception"></a>SessionLockLostException

### <a name="cause"></a>原因

**SessionLockLostException** は、セッションが受け入れられ、クライアントによって保持されているロックがサービス側で期限切れになったときにスローされます。

セッションのロックは、さまざまな理由により期限切れになる場合があります。 

  * ロック タイマーが、クライアント アプリケーションによって更新される前に期限切れになっている。
  * クライアント アプリケーションがロックを取得し、永続ストアにそれを保存してから再起動した。 再起動後、クライアント アプリケーションが、転送中のセッションを調べて、これらのセッション内のメッセージを処理しようとした。

### <a name="resolution"></a>解像度

**SessionLockLostException** が発生した場合、クライアント アプリケーションがセッションでメッセージを処理できなくなります。 クライアント アプリケーションでは、分析のために例外をログに記録することもできますが、クライアントはメッセージを破棄する "*必要があります*"。

セッションのロックの有効期限が切れたため、キュー (またはサブスクリプション) を破棄して、セッションを受け入れる次のクライアント アプリケーションでロックできるようになります。 セッション ロックは、特定の時点で 1 つのクライアント アプリケーションによって保持されるため、順番どおりの処理が保証されます。

## <a name="socketexception"></a>SocketException

### <a name="cause"></a>原因

次の場合には、**SocketException** がスローされます。
   * 指定された時間が経過してもホストが適切に応答しなかったために接続試行が失敗した場合 (TCP エラー コード 10060)。
   * 接続されたホストが応答できなかったため、確立された接続が失敗した場合。
   * メッセージの処理中にエラーが発生したか、リモート ホストがタイムアウトを超えた場合。
   * 基になるネットワーク リソースの問題。

### <a name="resolution"></a>解像度

**SocketException** エラーは、アプリケーションをホストしている VM が名前 `<mynamespace>.servicebus.windows.net` を対応する IP アドレスに変換できないことを示します。 

IP アドレスへのマッピングで、次のコマンドが成功するかどうかを確認してください。

```Powershell
PS C:\> nslookup <mynamespace>.servicebus.windows.net
```

出力は次のようになるはずです

```bash
Name:    <cloudappinstance>.cloudapp.net
Address:  XX.XX.XXX.240
Aliases:  <mynamespace>.servicebus.windows.net
```

上記の名前が、IP と名前空間のエイリアスに **解決されない** 場合は、どのネットワーク管理者がさらに調査するかを確認します。 名前解決は、通常、顧客ネットワークのリソースである DNS サーバーを介して行われます。 DNS 解決が Azure DNS によって行われる場合は、Azure サポートにお問い合わせください。

名前解決が **期待したとおりに機能している** 場合は、[ここで](service-bus-troubleshooting-guide.md#connectivity-certificate-or-timeout-issues) Azure Service Bus への接続が許可されているかどうかを確認してください


## <a name="messagingexception"></a>MessagingException

### <a name="cause"></a>原因

**MessagingException** は、さまざまな理由でスローされる可能性がある一般的な例外です。 その理由の一部を以下に示します。

   * **トピック** または **サブスクリプション** に **QueueClient** を作成しようとした。
   * 送信されたメッセージのサイズが、指定されたレベルの制限を超えている。 Service Bus のクォータと制限の詳細については、[こちら](service-bus-quotas.md)をお読みください。
   * 調整により、特定のデータ プレーン要求 (送信、受信、完了、破棄) が終了した。
   * サービスのアップグレードと再起動によって一時的な問題が発生した。

> [!NOTE]
> 上記の例外の一覧はすべてを網羅しているわけではありません。

### <a name="resolution"></a>解像度

解決手順は、**MessagingException** がスローされた原因によって異なります。

   * **一時的な問題** (**_isTransient_ *_ が _* _true_ *_ に設定されている) または* 調整の問題** の場合は、操作を再試行することによって解決される場合があります。 これには、SDK の既定の再試行ポリシーを利用できます。
   * その他の問題については、例外の詳細に問題が示され、解決手順を推測することができます。

## <a name="next-steps"></a>次のステップ
Service Bus の詳細な .NET API リファレンスについては、「[Azure .NET API reference](/dotnet/api/overview/azure/service-bus)」(Azure .NET API リファレンス) を参照してください。
トラブルシューティングのヒントについては、[トラブルシューティング ガイド](service-bus-troubleshooting-guide.md)に関するページを参照してください。

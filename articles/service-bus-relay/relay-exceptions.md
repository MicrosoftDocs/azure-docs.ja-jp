---
title: "Microsoft Azure Relay 例外と解決方法 | Microsoft Docs"
description: "Azure Relay の例外と推奨アクションの一覧。"
services: service-bus-relay
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 5f9dd02c-cce0-43b3-8eb8-744f0c27f38c
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: sethm;jotaub
translationtype: Human Translation
ms.sourcegitcommit: bc71995deef3f9795ebd33d9642c7ffa1995021b
ms.openlocfilehash: 346851226dce8ca8e48d3ae291b97e6e43d1ae03
ms.lasthandoff: 02/02/2017


---
# <a name="relay-exceptions"></a>Relay の例外

この記事では、Microsoft Azure Relay API によって生成される例外を示します。 このリファレンスは変更される可能性があるため、最新情報をご確認ください。

## <a name="exception-categories"></a>例外のカテゴリ

Relay API で生成される例外をカテゴリ別に分類し、修復のために実行できる関連するアクションと共に以下に示します。

1. ユーザー コードのエラー ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)、[System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx)、[System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx)、[System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx))。 一般アクション: 処理を実行する前にコードの修正を試みます。
2. セットアップ/構成エラー ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception)、[System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx))。 一般アクション: 構成を確認し、必要に応じて変更します。
3. 一時的な例外 ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception)、[Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)、[Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception.aspx))。 一般アクション: 操作をやり直すか、ユーザーに通知します。
4. その他の例外 ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx)、[System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)、[Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception)、[Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception))。 全般的なアクション: 例外の種類に固有。次のセクションの表を参照してください。 

## <a name="exception-types"></a>例外の種類

次の表は、メッセージングの例外の種類および原因と実行できる推奨アクションを示したものです。

| **例外の種類** | **説明** | **推奨アクション** | **自動または即時再試行に関する注意** |
| --- | --- | --- | --- |
| [タイムアウト](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |サーバーは、 [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout)によって制御される指定された時間内に、要求された操作に対して応答しませんでした。 サーバーで、要求された操作が完了した可能性があります。 これは、ネットワークや他のインフラストラクチャの遅延が原因で発生することがあります。 |システム状態の整合性をチェックして、必要な場合は再試行してください。 「 [TimeoutException](#timeoutexception)」を参照してください。 |再試行によって解決する場合があります。再試行ロジックをコードに追加してください。 |
| [無効な操作](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |要求されたユーザー操作はサーバーまたはサービス内で許可されていません。 詳細については、例外メッセージを参照してください。 たとえば、 [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete) は、 **ReceiveAndDelete** モードでメッセージを受信した場合に、この例外を生成します。 |コードとドキュメントを確認します。 要求した操作が有効なことを確かめてください。 |再試行によって解決することはありません。 |
| [操作は取り消されました](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |既に終了、中止、または破棄されたオブジェクトに対して操作を呼び出そうとしました。 まれに、アンビエント トランザクションが既に破棄されている場合があります。 |コードを確認し、破棄されたオブジェクトに対して操作を呼び出していないことを確かめます。 |再試行によって解決することはありません。 |
| [未承認のアクセス](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) オブジェクトはトークンを取得できませんでした。トークンが無効です。または、操作の実行に必要な要求がトークンに含まれていません。 |トークン プロバイダーが正しい値を使用して作成されていることを確認します。 Access Control Service の構成を確認します。 |再試行によって解決する場合があります。再試行ロジックをコードに追加してください。 |
| [引数の例外](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [引数が null です](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[範囲外の引数](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |メソッドに指定された&1; つまたは複数の引数が無効です。<br /> [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) または [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) に指定された URI にパス セグメントが含まれています。<br /> [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) または [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) に指定された URI スキームが無効です。 <br />プロパティ値が 32 KB を超えています。 |呼び出し元のコードを確認し、引数が正しいことを確かめます。 |再試行によって解決することはありません。 |
| [サーバー ビジー](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |この時点では、このサービスで要求を処理できません。 |クライアントは、しばらく待機してから操作をやり直すことができます。 |クライアントは、一定の間隔をおいてから再試行することができます。 再試行の結果として別の例外が発生した場合は、その例外の再試行動作を確認します。 |
| [クォータを超過した](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |メッセージング エンティティが最大許容サイズに達しています。 |エンティティまたはそのサブキューからメッセージを受信して、エンティティ内に領域を作成します。 「 [QuotaExceededException](#quotaexceededexception)」を参照してください。 |メッセージがそれまでに削除されている場合は、再試行によって解決することがあります。 |
| [メッセージ サイズの超過](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |メッセージ ペイロードが 256K の制限を超えています。 ただし 256k の制限はメッセージの合計サイズであり、システム プロパティや .NET のオーバーヘッドも含めたサイズです。 |メッセージ ペイロードのサイズを小さくし、操作を再試行します。 |再試行によって解決することはありません。 |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) は、特定のエンティティのクォータが超過していることを示します。

Relay の場合、この例外は [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx) をラップします。これは、このエンドポイントについてリスナーが最大数に達したことを示します。 これは、例外メッセージの **MaximumListenersPerEndpoint** 値に示されます。

## <a name="timeoutexception"></a>TimeoutException
[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) は、ユーザーが開始した操作が操作タイムアウトより時間がかかっていることを示します。 

[ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) プロパティの値を確認する必要があります。この制限に達した場合も、[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) が発生する可能性があります。

Relay では、リレー送信者接続を最初に開いたときにタイムアウトの例外を受け取る可能性があります。 この例外の一般的な原因には、次の&2; つがあります。

1. [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) 値が小さすぎる可能性があります (1 秒に満たないなど)。
2. オンプレミスのリレー リスナーが反応していない (または、リスナーに新しいクライアント接続を受け入れられないようにするファイアウォール ルールの問題が発生している) 可能性があり、 [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) 値が約 20 秒未満になっています。

次に例を示します。

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10. The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>一般的な原因
この例外の一般的な原因には、次の&2; つがあります。正しくない構成と、一時的なサービス エラーです。

1. **構成が正しくない**
    操作状態に対して、操作タイムアウトが小さすぎる可能性があります。 クライアント SDK の操作タイムアウトの既定値は 60 秒です。 コードに小さすぎる値を設定していないかどうかを確認します。 ネットワークの状態と CPU 使用率は、特定の操作が完了する時間に影響します。このため、操作タイムアウトに小さい値を設定することは推奨されません。
2. **一時的なサービス エラー**
    Relay で、要求の処理に遅延が発生する場合 (トラフィック量の多い場合など) があります。 このような場合、操作が成功するまで、遅延後に操作を再試行できます。 複数回試行しても同じ操作が失敗する場合は、「 [Azure Status](https://azure.microsoft.com/status/) 」にアクセスして、既知のサービス停止がないかどうかを確認してください。

## <a name="next-steps"></a>次のステップ:
* [Relay に関する FAQ](relay-faq.md)
* [名前空間を作成する](relay-create-namespace-portal.md)
* [.NET を使って作業を開始する](relay-hybrid-connections-dotnet-get-started.md)
* [Node を使って作業を開始する](relay-hybrid-connections-node-get-started.md)



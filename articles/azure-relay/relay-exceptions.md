---
title: Azure Relay の例外とその解決方法 | Microsoft Docs
description: Azure Relay の例外と、それを解決するための推奨されるアクションの一覧。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 44eeba6eb7b8cfd4e81a923c2d9a3155f1709f2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98625150"
---
# <a name="azure-relay-exceptions"></a>Azure Relay の例外

この記事では、Azure Relay API によって生成されることのある例外を示します。 このリファレンスは変更される可能性があるため、最新情報をご確認ください。

## <a name="exception-categories"></a>例外のカテゴリ

Relay API からは、次のカテゴリに分類できる例外が生成されます。 また、これらの例外を解決するための推奨されるアクションも一覧として示します。

*   **ユーザー コードのエラー**: [System.ArgumentException](/dotnet/api/system.argumentexception)、[System.InvalidOperationException](/dotnet/api/system.invalidoperationexception)、[System.OperationCanceledException](/dotnet/api/system.operationcanceledexception)、[System.Runtime.Serialization.SerializationException](/dotnet/api/system.runtime.serialization.serializationexception)。 

    **一般的なアクション**: 処理を実行する前にコードの修正を試みてください。
*   **セットアップ/構成エラー**: [System.UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception)。 

    **一般的なアクション**: 構成を確認してください。 必要に応じて、構成を変更します。
*   **一時的な例外**: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception)、[Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)、[Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)。 

    **一般的なアクション**: 操作を再試行するか、ユーザーに通知してください。
*   **その他の例外**: [System.Transactions.TransactionException](/dotnet/api/system.transactions.transactionexception)、[System.TimeoutException](/dotnet/api/system.timeoutexception)。 

    **一般的なアクション**: 例外の種類によって異なります。 次のセクションの表を参照してください。 

## <a name="exception-types"></a>例外の種類

次の表は、メッセージングの例外の種類および原因を示したものです。 また、これらの例外を解決するための推奨されるアクションも記載されています。

| **例外の種類** | **説明** | **推奨されるアクション** | **自動または即時再試行に関する注意** |
| --- | --- | --- | --- |
| [タイムアウト](/dotnet/api/system.timeoutexception) |サーバーは、 [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout)によって制御される指定された時間内に、要求された操作に対して応答しませんでした。 サーバーで、要求された操作が完了した可能性があります。 これは、ネットワークや他のインフラストラクチャの遅延が原因で発生することがあります。 |システム状態の整合性をチェックして、必要な場合は再試行します。 「[TimeoutException](#timeoutexception)」を参照してください。 |再試行によって解決する場合があります。再試行ロジックをコードに追加してください。 |
| [無効な操作](/dotnet/api/system.invalidoperationexception) |要求されたユーザー操作はサーバーまたはサービス内で許可されていません。 詳細については、例外メッセージを参照してください。 |コードとドキュメントを確認します。 有効な操作を要求してください。 |再試行によって解決することはありません。 |
| [操作は取り消されました](/dotnet/api/system.operationcanceledexception) |既に終了、中止、または破棄されたオブジェクトに対して操作を呼び出そうとしました。 まれに、アンビエント トランザクションが既に破棄されている場合があります。 |コードを確認し、破棄されたオブジェクトに対して操作を呼び出していないことを確かめます。 |再試行によって解決することはありません。 |
| [未承認のアクセス](/dotnet/api/system.unauthorizedaccessexception) |[TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) オブジェクトはトークンを取得できませんでした。トークンが無効です。または、操作の実行に必要な要求がトークンに含まれていません。 |正しい値を使用してトークン プロバイダーを作成します。 Access Control Service の構成を確認します。 |再試行によって解決する場合があります。再試行ロジックをコードに追加してください。 |
| [引数の例外](/dotnet/api/system.argumentexception)、<br /> [引数が null](/dotnet/api/system.argumentnullexception)、<br />[範囲外の引数](/dotnet/api/system.argumentoutofrangeexception) |以下のいずれかまたは複数が発生しています。<br />メソッドに指定された 1 つまたは複数の引数が無効です。<br /> [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) または [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) に指定された URI に 1 つまたは複数のパス セグメントが含まれています。<br />[NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) または [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) に指定された URI スキームが無効です。 <br />プロパティ値が 32 KB を超えています。 |呼び出し元のコードを確認し、引数が正しいことを確かめます。 |再試行によって解決することはありません。 |
| [サーバー ビジー](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |この時点では、このサービスで要求を処理できません。 |クライアントは、しばらく待ってから操作をやり直すことができます。 |クライアントは、特定の間隔の後に再試行することができます。 再試行の結果として別の例外が発生した場合は、その例外の再試行動作を確認します。 |
| [クォータ超過](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |メッセージング エンティティが最大許容サイズに達しています。 |エンティティまたはそのサブキューからメッセージを受信して、エンティティ内に領域を作成します。 「 [QuotaExceededException](#quotaexceededexception)」を参照してください。 |メッセージがそれまでに削除されている場合は、再試行によって解決することがあります。 |
| [メッセージ サイズの超過](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |メッセージ ペイロードが 256 KB の制限を超えています。 256 KB までに制限されているのはメッセージの合計サイズであることに注意してください。 メッセージの合計サイズには、システム プロパティや Microsoft .NET のオーバーヘッドも含まれる場合があります。 |メッセージ ペイロードのサイズを小さくし、操作を再試行します。 |再試行によって解決することはありません。 |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) は、特定のエンティティのクォータが超過していることを示します。

Relay の場合、この例外は [System.ServiceModel.QuotaExceededException](/dotnet/api/system.servicemodel.quotaexceededexception) をラップします。これは、このエンドポイントについてリスナーが最大数を超過したことを示します。 これは、例外メッセージの **MaximumListenersPerEndpoint** 値に示されます。

## <a name="timeoutexception"></a>TimeoutException
[TimeoutException](/dotnet/api/system.timeoutexception) は、ユーザーが開始した操作が操作タイムアウトより時間がかかっていることを示します。 

[ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) プロパティの値を確認します。 この制限に達した場合も、[TimeoutException](/dotnet/api/system.timeoutexception) が発生する場合があります。

Relay では、リレー送信者接続を最初に開いたときにタイムアウトの例外を受け取る可能性があります。 この例外の一般的な原因には、次の 2 つがあります。

*   [OpenTimeout](/previous-versions/) 値が小さすぎる可能性があります (1 秒未満の場合など)。
* オンプレミスのリレー リスナーが反応していない (または、リスナーが新しいクライアント接続を受け入れられなくなるようなファイアウォール規則の問題が発生している) 可能性があります。また、[OpenTimeout](/previous-versions/) 値が約 20 秒未満になっています。

例:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>一般的な原因
このエラーの一般的な原因としては、次の 2 つがあります。

*   **構成が正しくない**
    
    操作状態に対して、操作タイムアウトが小さすぎる可能性があります。 クライアント SDK の操作タイムアウトの既定値は 60 秒です。 コードの値が小さすぎる値に設定されていないか確認します。 CPU 使用率とネットワークの状態が、操作の完了に要する時間に影響する可能性があることに注意してください。 操作タイムアウトに非常に小さい値を設定するのは避けることをお勧めします。
*   **一時的なサービス エラー**

    場合によっては、Relay サービスで要求の処理が遅れることがあります。 これは、トラフィックの多い時間帯などに発生することがあります。 これが発生した場合は、遅延の後、操作が成功するまで操作を再試行できます。 複数回試行しても同じ操作が失敗する場合は、[Azure のサービス状態のサイト](https://azure.microsoft.com/status/)を確認し、既知のサービス停止がないかどうかを確認してください。

## <a name="next-steps"></a>次の手順
* [Azure Relay に関する FAQ](relay-faq.md)
* [Relay 名前空間の作成](relay-create-namespace-portal.md)
* [Azure Relay と .NET の使用](relay-hybrid-connections-dotnet-get-started.md)
* [Azure Relay と Node の使用](relay-hybrid-connections-node-get-started.md)

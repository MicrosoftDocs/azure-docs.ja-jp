---
title: Azure Relay の例外とその解決方法 | Microsoft Docs
description: Azure Relay の例外と、それを解決するための推奨されるアクションの一覧。
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 5f9dd02c-cce0-43b3-8eb8-744f0c27f38c
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2017
ms.author: spelluru
ms.openlocfilehash: fe8f057443b978e70e7cdd2591affd455fefdca8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60749038"
---
# <a name="azure-relay-exceptions"></a>Azure Relay の例外

この記事では、Azure Relay API によって生成されることのある例外を示します。 このリファレンスは変更される可能性があるため、最新情報をご確認ください。

## <a name="exception-categories"></a>例外のカテゴリ

Relay API からは、次のカテゴリに分類できる例外が生成されます。 また、これらの例外を解決するための推奨されるアクションも一覧として示します。

*   **ユーザー コードのエラー**: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)、[System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx)、[System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx)、[System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)。 

    **一般的なアクション**: 処理を続行する前にコードの修正を試みます。
*   **セットアップ/構成エラー**: [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx)。 

    **一般的なアクション**: 構成を確認します。 必要に応じて、構成を変更します。
*   **一時的な例外**: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception)、[Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)、[Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)。 

    **一般的なアクション**: 操作を再試行するか、またはユーザーに通知します。
*   **その他の例外**: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx)、[System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)。 

    **一般的なアクション**: 例外の種類によって異なります。 次のセクションの表を参照してください。 

## <a name="exception-types"></a>例外の種類

次の表は、メッセージングの例外の種類および原因を示したものです。 また、これらの例外を解決するための推奨されるアクションも記載されています。

| **例外の種類** | **説明** | **推奨されるアクション** | **自動または即時再試行に関する注意** |
| --- | --- | --- | --- |
| [タイムアウト](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |サーバーは、 [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout)によって制御される指定された時間内に、要求された操作に対して応答しませんでした。 サーバーで、要求された操作が完了した可能性があります。 これは、ネットワークや他のインフラストラクチャの遅延が原因で発生することがあります。 |システム状態の整合性をチェックして、必要な場合は再試行します。 「[TimeoutException](#timeoutexception)」を参照してください。 |再試行によって解決する場合があります。再試行ロジックをコードに追加してください。 |
| [無効な操作](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |要求されたユーザー操作はサーバーまたはサービス内で許可されていません。 詳細については、例外メッセージを参照してください。 |コードとドキュメントを確認します。 有効な操作を要求してください。 |再試行によって解決することはありません。 |
| [操作は取り消されました](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |既に終了、中止、または破棄されたオブジェクトに対して操作を呼び出そうとしました。 まれに、アンビエント トランザクションが既に破棄されている場合があります。 |コードを確認し、破棄されたオブジェクトに対して操作を呼び出していないことを確かめます。 |再試行によって解決することはありません。 |
| [未承認のアクセス](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) オブジェクトはトークンを取得できませんでした。トークンが無効です。または、操作の実行に必要な要求がトークンに含まれていません。 |正しい値を使用してトークン プロバイダーを作成します。 Access Control Service の構成を確認します。 |再試行によって解決する場合があります。再試行ロジックをコードに追加してください。 |
| [引数の例外](https://msdn.microsoft.com/library/system.argumentexception.aspx)、<br /> [引数が null](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)、<br />[範囲外の引数](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |以下のいずれかまたは複数が発生しています。<br />メソッドに指定された 1 つまたは複数の引数が無効です。<br /> [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) または [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) に指定された URI に 1 つまたは複数のパス セグメントが含まれています。<br />[NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) または [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) に指定された URI スキームが無効です。 <br />プロパティ値が 32 KB を超えています。 |呼び出し元のコードを確認し、引数が正しいことを確かめます。 |再試行によって解決することはありません。 |
| [サーバー ビジー](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |この時点では、このサービスで要求を処理できません。 |クライアントは、しばらく待ってから操作をやり直すことができます。 |クライアントは、特定の間隔の後に再試行することができます。 再試行の結果として別の例外が発生した場合は、その例外の再試行動作を確認します。 |
| [クォータを超過した](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |メッセージング エンティティが最大許容サイズに達しています。 |エンティティまたはそのサブキューからメッセージを受信して、エンティティ内に領域を作成します。 「 [QuotaExceededException](#quotaexceededexception)」を参照してください。 |メッセージがそれまでに削除されている場合は、再試行によって解決することがあります。 |
| [メッセージ サイズの超過](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |メッセージ ペイロードが 256 KB の制限を超えています。 256 KB までに制限されているのはメッセージの合計サイズであることに注意してください。 メッセージの合計サイズには、システム プロパティや Microsoft .NET のオーバーヘッドも含まれる場合があります。 |メッセージ ペイロードのサイズを小さくし、操作を再試行します。 |再試行によって解決することはありません。 |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) は、特定のエンティティのクォータが超過していることを示します。

Relay の場合、この例外は [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx) をラップします。これは、このエンドポイントについてリスナーが最大数を超過したことを示します。 これは、例外メッセージの **MaximumListenersPerEndpoint** 値に示されます。

## <a name="timeoutexception"></a>TimeoutException
[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) は、ユーザーが開始した操作が操作タイムアウトより時間がかかっていることを示します。 

[ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) プロパティの値を確認します。 この制限に達した場合も、[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) が発生する場合があります。

Relay では、リレー送信者接続を最初に開いたときにタイムアウトの例外を受け取る可能性があります。 この例外の一般的な原因には、次の 2 つがあります。

*   [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) 値が小さすぎる可能性があります (1 秒未満の場合など)。
* オンプレミスのリレー リスナーが反応していない (または、リスナーが新しいクライアント接続を受け入れられなくなるようなファイアウォール規則の問題が発生している) 可能性があります。また、[OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) 値が約 20 秒未満になっています。

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


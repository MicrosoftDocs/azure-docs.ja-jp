---
title: ハイブリッド接続の診断ログ
description: この記事では、Azure Relay で使用可能なすべてのアクティビティ ログと診断ログの概要について説明します。
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 9b459750ad1445da89a8e89a10a35b878bfb64e1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100590866"
---
# <a name="enable-diagnostics-logs-for-azure-relay-hybrid-connections"></a>Azure Relay ハイブリッド接続の診断ログを有効にする
Azure Relay ハイブリッド接続の使用を開始するときは、リスナーとセンダーが開かれて閉じられる方法とタイミング、およびハイブリッド接続が作成され、メッセージが送信される方法を監視することが必要な場合があります。 この記事では、Azure Relay サービスで提供されるアクティビティ ログと診断ログの概要について説明します。 

Azure Relay では次の 2 種類のログを表示できます。

- [[アクティビティ ログ]](../azure-monitor/essentials/platform-logs-overview.md):このログには、Azure portal または Azure Resource Manager テンプレートを使用して名前空間に対して実行された操作に関する情報が含まれています。 このログは常に有効になっています。 次に例を示します。"名前空間の作成または更新"、"ハイブリッド接続の作成または更新"。 
- [診断ログ](../azure-monitor/essentials/platform-logs-overview.md):診断ログを構成することで、API または言語 SDK を使用して名前空間に対して実行される操作とアクションで発生するすべてのイベントについてより深く考察できます。

## <a name="view-activity-logs"></a>アクティビティ ログを表示する
Azure Relay 名前空間のアクティビティ ログを表示するには、Azure portal の **[アクティビティ ログ]** ページに切り替えます。

![Azure Relay - アクティビティ ログ](./media/diagnostic-logs/activity-log.png)

## <a name="enable-diagnostic-logs"></a>Traffic Manager で診断ログを有効にする

> [!NOTE]
> 診断ログは、Windows Communication Foundation (WCF) Relay ではなく、ハイブリッド接続でのみ使用できます。

診断ログを有効にするには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) で、Azure Relay 名前空間に移動した後、 **[監視]** で **[診断設定]** を選択します。
1. **[診断設定]** ページで、 **[診断設定を追加する]** を選択します。  

   ![[診断設定を追加する] リンク](./media/diagnostic-logs/add-diagnostic-setting.png)

1. 次の手順に従って、診断設定を構成します。
    1. **[名前]** ボックスに、診断設定の名前を入力します。  
    2. ログの種類として **[HybridConnectionsEvent]** を選択します。 
    3. 診断ログの **送信先** として、次の 3 つのいずれかを選択します。  
        1. **[ストレージ アカウントへのアーカイブ]** を選択した場合は、診断ログが格納されるストレージ アカウントを構成します。  
        2. **[イベント ハブへのストリーム]** を選択した場合は、診断ログのストリーム先のイベント ハブを構成します。
        3. **[Log Analytics への送信]** を選択した場合は、診断の送信先の Log Analytics のインスタンスを指定します。  

        ![診断設定の例](./media/diagnostic-logs/sample-diagnostic-settings.png)
1. ツール バーの **[保存]** を選択して設定を保存します。

新しい設定は、10 分ほどで有効になります。 構成されたアーカイブ ターゲットの **[診断ログ]** ペインにログが表示されます。 診断設定の構成の詳細については、[Azure 診断ログの概要](../azure-monitor/essentials/platform-logs-overview.md)に関するページを参照してください。


## <a name="schema-for-hybrid-connections-events"></a>ハイブリッド接続イベントのスキーマ
ハイブリッド接続イベント ログの JSON 文字列には、次の表に示す要素が含まれます。

| 名前 | 説明 |
| ------- | ------- |
| ResourceId | Azure Resource Manager リソース ID |
| ActivityId | 指定された操作を識別するために使用される内部 ID。 "TrackingId" と呼ばれることもあります |
| エンドポイント | Relay リソースのアドレス |
| OperationName | ログに記録されるハイブリッド接続操作の種類 |
| EventTimeString | ログ レコードの UTC タイムスタンプ |
| Message | イベントに関する詳細メッセージ |
| カテゴリ | イベントのカテゴリ。 現時点では、`HybridConnectionsEvents` のみがあります。 


## <a name="sample-hybrid-connections-event"></a>ハイブリッド接続イベントの例
JSON 形式のハイブリッド接続イベントの例を次に示します。 

```json
{
    "resourceId": "/SUBSCRIPTIONS/0000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/MyResourceGroup/PROVIDERS/MICROSOFT.RELAY/NAMESPACES/MyRelayNamespace",
    "ActivityId": "7006a0db-27eb-445c-939b-ce86133014cc",
    "endpoint": "sb://myrelaynamespace.servicebus.windows.net/myhybridconnection/7006a0db-27eb-445c-939b-ce86133014cc_G5",
    "operationName": "Microsoft.Relay/HybridConnections/NewSenderRegistering",
    "EventTimeString": "2020-04-27T20:27:57.3842810Z",
    "message": "A new sender is registering.",
    "category": "HybridConnectionsEvent"
}
```

## <a name="events-and-operations-captured-in-diagnostic-logs"></a>診断ログでキャプチャされるイベントと操作

| Operation | 説明 | 
| --------- | ----------- | 
| AuthorizationFailed | 承認に失敗しました。|
| InvalidSasToken | SAS トークンが無効です。 | 
| ListenerAcceptingConnection | リスナーが接続を受け入れています。 |
| ListenerAcceptingConnectionTimeout | 接続を受け入れるリスナーがタイムアウトになりました。 |
| ListenerAcceptingHttpRequestFailed | 例外が発生したため、リスナーが HTTP 要求の受け入れに失敗しました。 |
| ListenerAcceptingRequestTimeout | 要求を受け入れるリスナーがタイムアウトになりました。 |  
| ListenerClosingFromExpiredToken | セキュリティ トークンの有効期限が切れているため、リスナーを閉じています。 | 
| ListenerRejectedConnection | リスナーが接続を拒否しました。 |
| ListenerReturningHttpResponse | リスナーが HTTP 応答を返しています。 |  
| ListenerReturningHttpResponseFailed | リスナーが、エラー コードを含む HTTP 応答を返しています。 | 
 ListenerSentHttpResponse | Relay サービスがリスナーからの HTTP 応答を受信しました。 | 
| ListenerUnregistered | リスナーの登録が解除されました。 | 
| ListenerUnresponsive | 応答を返すときにリスナーが応答しません。 | 
| MessageSendingToListener | メッセージがリスナーに送信されています。 |
| MessageSentToListener | メッセージがリスナーに送信されます。 | 
| NewListenerRegistered | 新しいリスナーが登録されました。 |
| NewSenderRegistering | 新しいセンダーを登録しています。 | 
| ProcessingRequestFailed | ハイブリッド接続操作の処理に失敗しました。 | 
| SenderConnectionClosed | センダーへの接続が閉じられました。 |
| SenderListenerConnectionEstablished | センダーとリスナーが接続を正常に確立しました。 |
| SenderSentHttpRequest | センダーが HTTP 要求を送信しました。 | 


## <a name="next-steps"></a>次のステップ

Azure Relay の詳細については、次の記事をご覧ください。

* [Azure Relay の概要](relay-what-is-it.md)
* [Relay ハイブリッド接続の概要](relay-hybrid-connections-dotnet-get-started.md)

---
title: Azure Service Bus 診断ログ | Microsoft Docs
description: この記事では、Azure Service Bus で使用可能なすべての操作と診断ログの概要について説明します。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: f2672d61285025eadb8f742ca2518297107595ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100585276"
---
# <a name="enable-diagnostics-logs-for-service-bus"></a>Service Bus の診断ログを有効にする

Azure Service Bus 名前空間の使用を開始するとき、名前空間がいつ、どのように作成、削除、またはアクセスされるかを監視したい場合があります。 この記事では、使用可能なすべての操作と診断ログの概要について説明します。

Azure Service Bus は現在、Azure Service Bus 名前空間上で実行される "*管理操作*" をキャプチャするアクティビティと操作のログをサポートしています。 具体的には、これらのログは操作の種類 (キューの作成、使用リソース、操作の状態など) をキャプチャします。

## <a name="operational-logs-schema"></a>操作ログのスキーマ

すべてのログは、以下の 2 つの場所に JavaScript Object Notation (JSON) 形式で格納されます。

- **AzureActivity**:Azure portal 内で、または Azure Resource Manager テンプレートのデプロイを使用して、名前空間に対して実行された操作とアクションのログを表示します。
- **AzureDiagnostics**:API を使用して、または言語 SDK 上の管理クライアントを使用して、名前空間に対して実行された操作とアクションのログを表示します。

操作ログの JSON 文字列には、次の表に示す要素が含まれます。

| 名前 | 説明 |
| ------- | ------- |
| ActivityId | 指定されたアクティビティを識別するために使用される内部 ID |
| EventName | 操作の名前 |
| ResourceId | Azure Resource Manager リソース ID |
| SubscriptionId | サブスクリプション ID |
| EventTimeString | 操作時間 |
| EventProperties | 操作プロパティ |
| Status | 操作の状態 |
| Caller | 操作の呼び出し元 (Azure portal または管理クライアント) |
| カテゴリ | OperationalLogs |

操作ログの JSON 文字列の例を次に示します。

```json
{
  "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
  "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

## <a name="events-and-operations-captured-in-operational-logs"></a>操作ログでキャプチャされるイベントと操作

操作ログでは、Azure Service Bus 名前空間上で実行されるすべての管理操作がキャプチャされます。 Azure Service Bus 上では大量のデータ操作が実行されるため、データ操作はキャプチャされません。

> [!NOTE]
> データ操作をより適切に追跡できるように、クライアント側のトレースを使用することをお勧めします。

操作ログでは、以下の管理操作がキャプチャされます。 

| Scope | Operation|
|-------| -------- |
| 名前空間 | <ul> <li> 名前空間の作成</li> <li> 名前空間の更新 </li> <li> 名前空間の削除 </li> <li> 名前空間 SharedAccess ポリシーの更新 </li> </ul> | 
| キュー | <ul> <li> キューの作成</li> <li> キューの更新</li> <li> キューの削除 </li> <li> キューの削除 (自動削除) </li> </ul> | 
| トピック | <ul> <li> トピックの作成 </li> <li> トピックの更新 </li> <li> トピックの削除 </li> <li> トピックの削除 (自動削除) </li> </ul> |
| サブスクリプション | <ul> <li> サブスクリプションの作成 </li> <li> サブスクリプションの更新 </li> <li> サブスクリプションの削除 </li> <li> サブスクリプションの削除 (自動削除) </li> </ul> |

> [!NOTE]
> 現在、*読み取り* 操作は操作ログで追跡されません。

## <a name="enable-operational-logs"></a>操作ログを有効にする

操作ログは、既定では無効になっています。 診断ログを有効にするには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) で、Azure Service Bus 名前空間に移動した後、 **[監視]** で **[診断設定]** を選択します。

   ![[診断設定] リンク](./media/service-bus-diagnostic-logs/image1.png)

1. **[診断設定]** ウィンドウで、 **[診断設定を追加する]** を選択します。  

   ![[診断設定を追加する] リンク](./media/service-bus-diagnostic-logs/image2.png)

1. 次の手順に従って、診断設定を構成します。

   a. **[名前]** ボックスに、診断設定の名前を入力します。  

   b. 診断ログの送信先として、以下の 3 つのいずれかを選択します。  
   - **[ストレージ アカウントへのアーカイブ]** を選択する場合は、診断ログが格納されるストレージ アカウントを構成する必要があります。  
   - **[イベント ハブへのストリーム]** を選択する場合は、診断ログのストリーム先のイベント ハブを構成する必要があります。
   - **[Log Analytics への送信]** を選択する場合は、診断の送信先の Log Analytics のインスタンスを指定する必要があります。  

   c. **[OperationalLogs]** チェック ボックスをオンにします。

    ![[診断設定] ウィンドウ](./media/service-bus-diagnostic-logs/image3.png)

1. **[保存]** を選択します。

新しい設定は、10 分ほどで有効になります。 構成されたアーカイブ ターゲットの **[診断ログ]** ペインにログが表示されます。

診断設定の構成の詳細については、[Azure 診断ログの概要](../azure-monitor/essentials/platform-logs-overview.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Service Bus の詳細を確認するには、以下を参照してください。

* [Service Bus の概要](service-bus-messaging-overview.md)
* [Service Bus の使用](service-bus-dotnet-get-started-with-queues.md)
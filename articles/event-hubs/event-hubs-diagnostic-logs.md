---
title: 診断ログの設定 - Azure Event Hub | Microsoft Docs
description: Azure のイベント ハブのアクティビティ ログおよび診断ログを設定する方法について説明します。
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: ff6466ddc6b64dfe85ebfb190205859aeb39ad17
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260838"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Azure イベント ハブの診断ログを設定する

Azure Event Hubs の 2 種類のログを表示できます。

* **[アクティビティ ログ](../azure-monitor/platform/activity-logs-overview.md)** : これらのログには、ジョブで実行された操作に関する情報が含まれます。 このログは常に有効になっています。
* **[診断ログ](../azure-monitor/platform/resource-logs-overview.md)** : 診断ログを構成することで、ジョブで発生するすべてのイベントについて、より深く考察できます。 診断ログは、ジョブが作成されたときからジョブが削除されるまでのアクティビティを記録します。ジョブの実行中に発生した更新やアクティビティも含まれます。

## <a name="enable-diagnostic-logs"></a>Traffic Manager で診断ログを有効にする

既定では、診断ログは無効になっています。 診断ログを有効にするには、次の手順を実行します。

1.  [Azure ポータル](https://portal.azure.com)の **[監視 + 管理]** で、 **[診断ログ]** をクリックします。

    ![ウィンドウで診断ログに移動する](./media/event-hubs-diagnostic-logs/image1.png)

2.  監視するリソースをクリックします。

3.  **[診断を有効にする]** をクリックします。

    ![診断ログを有効にする](./media/event-hubs-diagnostic-logs/image2.png)

4.  **[状態]** で、 **[オン]** をクリックします。

    ![診断ログの状態を変更する](./media/event-hubs-diagnostic-logs/image3.png)

5.  アーカイブ ターゲットを設定します (ストレージ アカウント、イベント ハブ、Azure Monitor ログなど)。

6.  新しい診断設定を保存します。

新しい設定は、10 分ほどで有効になります。 その後、構成されたアーカイブ ターゲットのログが **[診断ログ]** ウィンドウに表示されます。

診断の構成の詳細については、[Azure 診断ログの概要](../azure-monitor/platform/resource-logs-overview.md)に関するページを参照してください。

## <a name="diagnostic-logs-categories"></a>診断ログのカテゴリ

Event Hubs では、2 つのカテゴリの診断ログをキャプチャします。

* **アーカイブ ログ**: Event Hubs のアーカイブに関連するログ (特に、アーカイブ エラーに関連するログ)。
* **操作ログ**: Event Hubs の操作中に発生したことに関する情報 (特に、イベント ハブの作成などの操作の種類、使用されたリソース、操作の状態)。

## <a name="diagnostic-logs-schema"></a>診断ログのスキーマ

すべてのログは、JavaScript Object Notation (JSON) 形式で格納されます。 各エントリには、以下のセクションで説明している形式を使用する文字列フィールドがあります。

### <a name="archive-logs-schema"></a>アーカイブ ログ スキーマ

アーカイブ ログの JSON 文字列には、次の表に示す要素が含まれます。

名前 | 説明
------- | -------
TaskName | 失敗したタスクの説明。
ActivityId | 内部 ID。追跡目的で使用されます。
trackingId | 内部 ID。追跡目的で使用されます。
resourceId | Azure Resource Manager リソース ID。
eventHub | イベント ハブの完全名 (名前空間の名前を含みます)。
partitionId | 書き込み先のイベント ハブ パーティション。
archiveStep | ArchiveFlushWriter
startTime | 障害開始時刻。
failures | 障害が発生した回数。
durationInSeconds | 障害の時間。
message | エラー メッセージ。
category | ArchiveLogs

次のコードは、アーカイブ ログの JSON 文字列の例です。

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "21b89a0b-8095-471a-9db8-d151d74ecf26",
   "trackingId": "21b89a0b-8095-471a-9db8-d151d74ecf26_B7",
   "resourceId": "/SUBSCRIPTIONS/854D368F-1828-428F-8F3C-F2AFFA9B2F7D/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "eventHub": "fbettati-opera-eventhub:eventhub:eh123~32766",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

### <a name="operational-logs-schema"></a>操作ログのスキーマ

操作ログの JSON 文字列には、次の表に示す要素が含まれます。

名前 | 説明
------- | -------
ActivityId | 内部 ID。追跡目的で使用されます。
EventName | 操作の名前。  
resourceId | Azure Resource Manager リソース ID。
SubscriptionId | [サブスクリプション ID] が表示されます。
EventTimeString | 操作時間。
EventProperties | 操作プロパティ。
Status | 操作の状態。
Caller | 操作の呼び出し元 (Azure Portal または管理クライアント)。
category | OperationalLogs

次のコードは、操作ログの JSON 文字列の例です。

```json
Example:
{
   "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/SHOEBOXEHNS-CY4001",
   "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>次の手順
* [Event Hubs の概要](event-hubs-what-is-event-hubs.md)
* [Event Hubs API 概要](event-hubs-api-overview.md)
* [Event Hubs の使用](event-hubs-dotnet-standard-getstarted-send.md)

---
title: Azure Service Bus 診断ログ | Microsoft Docs
description: Service Bus の診断ログを設定する方法について説明します。
keywords: ''
documentationcenter: .net
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 6443cb727573645792a4e6c929b80c3406d72025
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261799"
---
# <a name="service-bus-diagnostic-logs"></a>Service Bus の診断ログ

Azure Service Bus の 2 種類のログを表示できます。
* **[アクティビティ ログ](../azure-monitor/platform/activity-logs-overview.md)** これらのログには、ジョブで実行された操作に関する情報が含まれます。 このログは常に有効になっています。
* **[診断ログ](../azure-monitor/platform/resource-logs-overview.md)** 。 ジョブで発生するすべてのイベントに関する豊富な情報を含んだ診断ログを構成することができます。 診断ログは、ジョブが作成されたときからジョブが削除されるまでのアクティビティを記録します。ジョブの実行中に発生した更新やアクティビティも含まれます。

## <a name="turn-on-diagnostic-logs"></a>診断ログを有効にする

既定では、診断ログは無効になっています。 診断ログを有効にするには、次の手順を実行します。

1.  [Azure ポータル](https://portal.azure.com)の **[監視 + 管理]** で、 **[診断ログ]** をクリックします。

    ![ブレードで診断ログに移動する](./media/service-bus-diagnostic-logs/image1.png)

2. 監視するリソースをクリックします。  

3.  **[診断を有効にする]** をクリックします。

    ![診断ログを有効にする](./media/service-bus-diagnostic-logs/image2.png)

4.  **[状態]** で、 **[オン]** をクリックします。

    ![診断ログの状態を変更する](./media/service-bus-diagnostic-logs/image3.png)

5.  アーカイブ ターゲットを設定します (ストレージ アカウント、イベント ハブ、Azure Monitor ログなど)。

6.  新しい診断設定を保存します。

新しい設定は、10 分ほどで有効になります。 その後、構成したアーカイブ ターゲットのログが **[診断ログ]** ブレードに表示されます。

診断の構成の詳細については、[Azure 診断ログの概要](../azure-monitor/platform/resource-logs-overview.md)に関するページを参照してください。

## <a name="diagnostic-logs-schema"></a>診断ログのスキーマ

すべてのログは、JavaScript Object Notation (JSON) 形式で格納されます。 各エントリには、以下のセクションで説明している形式を使用する文字列フィールドがあります。

## <a name="operational-logs-schema"></a>操作ログのスキーマ

**OperationalLogs** カテゴリのログは、Service Bus の操作中に起きていることをキャプチャします。 具体的には、これらのログは操作の種類 (キューの作成、使用リソース、操作の状態など) をキャプチャします。

操作ログの JSON 文字列には、次の表に示す要素が含まれます。

名前 | 説明
------- | -------
ActivityId | 内部 ID。追跡目的で使用されます
EventName | 操作の名前           
resourceId | Azure Resource Manager リソース ID
SubscriptionId | サブスクリプション ID
EventTimeString | 操作時間
EventProperties | 操作プロパティ
Status | 操作の状態
Caller | 操作の呼び出し元 (Azure Portal または管理クライアント)
category | OperationalLogs

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

## <a name="next-steps"></a>次の手順

Service Bus の詳細については、次のリンクを参照してください。

* [Service Bus の概要](service-bus-messaging-overview.md)
* [Service Bus の使用](service-bus-dotnet-get-started-with-queues.md)

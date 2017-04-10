---
title: "Azure Service Bus 診断ログ | Microsoft Docs"
description: "Service Bus の診断ログを設定する方法について説明します。"
keywords: 
documentationcenter: .net
services: service-bus-messaging
author: banisadr
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/23/2017
ms.author: babanisa;sethm
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: b8ed786fe0c049d9be7ba1ca1cb6adef1950b8e9
ms.lasthandoff: 03/24/2017


---
# <a name="service-bus-diagnostic-logs"></a>Service Bus の診断ログ

Azure Service Bus の 2 種類のログを表示できます。
* **[アクティビティ ログ](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)**。 これらのログには、ジョブで実行された操作に関する情報が含まれます。 このログは常に有効になっています。
* **[診断ログ](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)**。 ジョブで発生するすべてのイベントに関する豊富な情報を含んだ診断ログを構成することができます。 診断ログは、ジョブが作成されたときからジョブが削除されるまでのアクティビティを記録します。ジョブの実行中に発生した更新やアクティビティも含まれます。

## <a name="turn-on-diagnostic-logs"></a>診断ログを有効にする
既定では、診断ログは無効になっています。 診断ログを有効にするには、次の手順を実行します。

1.    [Azure Portal](https://portal.azure.com) で、[ストリーミング ジョブ] ブレードに移動します。

2.    **[監視]** の **[診断ログ]** ブレードに移動します。

    ![ブレードで診断ログに移動する](./media/service-bus-diagnostic-logs/image1.png)  

3.    **[診断を有効にする]** をクリックします。

    ![診断ログを有効にする](./media/service-bus-diagnostic-logs/image2.png)

4.    **[状態]** で、**[オン]** をクリックします。

    ![診断ログの状態を変更する](./media/service-bus-diagnostic-logs/image3.png)

5.    アーカイブ ターゲットを設定します (ストレージ アカウント、イベント ハブ、Azure Log Analytics など)。

6.    収集するログのカテゴリを選択します (**実行**、**作成**など)。

7.    新しい診断設定を保存します。

新しい設定は、10 分ほどで有効になります。 その後、構成したアーカイブ ターゲットのログが **[診断ログ]** ブレードに表示されます。

診断の構成の詳細については、[Azure 診断ログの概要](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)に関するページを参照してください。

## <a name="diagnostic-logs-schema"></a>診断ログのスキーマ

すべてのログは、JavaScript Object Notation (JSON) 形式で格納されます。 各エントリには、以下の例で説明している形式を使用する文字列フィールドがあります。

## <a name="operation-logs-example"></a>操作ログの例

**OperationalLogs** カテゴリのログは、Service Bus の操作中に起きていることをキャプチャします。 具体的には、これらのログは操作の種類 (キューの作成、使用リソース、操作の状態など) をキャプチャします。

操作ログの JSON 文字列には、次の表に示す要素が含まれます。

名前 | Description
------- | -------
ActivityId | 内部 ID。追跡目的で使用されます
EventName | 操作の名前             
resourceId | Azure Resource Manager リソース ID
SubscriptionId | サブスクリプション ID
EventTimeString | 操作時間
EventProperties | 操作プロパティ
[Status] | 操作の状態
Caller | 操作の呼び出し元 (Azure Portal または管理クライアント)
カテゴリ | OperationalLogs

操作ログの JSON 文字列の例を次に示します。

```json
Example:
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

## <a name="next-steps"></a>次のステップ
* [Service Bus の概要](service-bus-messaging-overview.md)
* [Service Bus の使用](service-bus-dotnet-get-started-with-queues.md)


---
title: "Azure Monitor のアクティビティ ログを使用した Azure サービス正常性の追跡 | Microsoft Docs"
description: "Azure でパフォーマンスの低下やサービスの中断がいつ発生したかを検出します。 "
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 30db9258-98e6-4855-abcd-f06c06277aa8
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d31ee12c783b845e0a8ff1791d21b2ef9013eee3


---
# <a name="track-azure-service-health-using-azure-monitor-activity-logs"></a>Azure Monitor のアクティビティ ログを使用した Azure サービス正常性の追跡
Azure は、サービスの中断やパフォーマンスの低下があるたびに、公開を行います。 Azure Portal でこれらのイベントを参照できますが、[REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx) または [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) を使用してすべてのイベントにプログラムでアクセスすることもできます。

## <a name="browse-the-service-health-logs-for-your-subscription"></a>サブスクリプションのサービス正常性のログの参照
1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. **[ホーム]** に **[サービス正常性]** というタイルがあります そのタイルをクリックします。
   
    ![ホーム](./media/insights-service-health/Insights_Home.png)
3. Azure のすべてのリージョンの一覧が表示されます。 任意のリージョンをクリックすると、アクティビティ ログ クエリが実行され、過去 24 時間にサブスクリプションのいずれかに影響を与えたサービス インシデントが表示されます。
   
    ![アクティビティ ログのサブスクリプションのサービス正常性](./media/insights-service-health/AzureActivityLogServiceHealth3.png)
4. 表内の個々のインシデントをクリックすると、そのイベントの詳細を確認できます。
5. 表示される期間を長くするには、**[期間]** を変更します。

## <a name="next-steps"></a>次のステップ
* [Web ページの可用性と応答性を監視](../application-insights/app-insights-monitor-web-app-availability.md) すると、ページがダウンしているかどうかを検出できます。




<!--HONumber=Nov16_HO3-->



---
title: "イベントとアクティビティ (監査) ログの表示"
description: "Azure サブスクリプションで発生するすべてのイベントを表示する方法について説明します。"
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 60c38217-c3b5-4ef1-98ac-d045fdd5549b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/28/2015
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c767480f072feaa0fbb0b5bdaec992a6f2381040


---
# <a name="view-events-and-activity-logs"></a>イベントとアクティビティ ログの表示
作成や削除からアクセスの許可や取り消しまで、Azure のリソースに対して実行されるすべての操作は Azure リソース マネージャーで完全に監査されます。 Azure Portal でこれらのログを参照できますが、[REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx) または [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) を使用してすべてのイベントにプログラムでアクセスすることもできます。

## <a name="browse-the-events-impacting-your-azure-subscription"></a>Azure サブスクリプションに影響するイベントの参照
1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. **[参照]** をクリックして **[監査ログ]** を選択します。  
    ![参照ハブ](./media/insights-debugging-with-events/Insights_Browse.png)
3. これによってブレードが開き、過去 7 日間にサブスクリプションに影響を与えたイベントがすべて表示されます。 上部には、レベル別にデータを示すグラフが表示され、その下には、すべてのログの一覧が表示されます。 ![すべてのイベント](./media/insights-debugging-with-events/Insights_AllEvents.png)

> [!NOTE]
> Azure ポータルでは、特定のサブスクリプションについて表示できるイベントは最新の 500 件のみです。
> 
> 

1. 任意のログ エントリをクリックして、そのエントリを構成するイベントを表示できます。 たとえば、リソース グループに何かをデプロイすると、さまざまなリソースが作成または変更される可能性があります。 各エントリでは、次の内容が表示されます。
   
   * イベントの**レベル**: たとえば、追跡するだけでよいこと (**情報**) もあれば、把握する必要がある問題が発生すること (**エラー**) もあります。
   * **状態**: 一般的に、最終的な状態は "**成功**" や "**失敗**" になりますが、実行時間の長い操作の場合は、"**承諾済み**" になることもあります。
   * *日時* 。
   * 操作を実行した "*ユーザー*" (いる場合)。 すべての操作がユーザーによって実行されるとは限りません。場合によっては、バックエンド サービスによって実行されるため、**呼び出し元**がない操作もあります。
   * イベントの **関連付け ID** : この操作のセットを示す一意の識別子。
2. そこから詳細ブレードに移動すると、イベントの詳細を確認できます。
   
    ![リソース グループ](./media/insights-debugging-with-events/Insights_EventDetails.png)
   
    **[失敗]** イベントに関しては、このページで通常、デバッグの目的で役立つ詳細を示す **[サブステータス]** および **[プロパティ]** というセクションが表示されます。

## <a name="filter-to-specific-logs"></a>特定のログのフィルター処理
特定のエンティティに当てはまるイベントまたは特定の種類のイベントを表示するには、 **[フィルター]** コマンドをクリックして [監査ログ] ブレードをフィルター処理できます。 また、[フィルター] ブレードを使用して、[監査ログ] ブレードの **[期間]** を変更することもできます。

このコマンドをクリックすると、新しいブレードが開きます。

![[フィルター]](./media/insights-debugging-with-events/Insights_EventFilter.png)

フィルターには、次の 4 種類があります。

1. サブスクリプション
2. **リソース グループ**
3. **リソースの種類**
4. 特定の **リソース** : この場合、関心のあるリソースの完全な *リソース ID* を渡す必要があります。

さらに、イベントを実行したユーザーやイベントのレベルによって、イベントをフィルター処理することもできます。

表示する内容の選択が完了したら、ブレードの下部にある **[更新]** をクリックします。

## <a name="monitor-events-impacting-specific-resources"></a>特定のリソースに影響するイベントの監視
1. **[参照]** をクリックして、関心のあるリソースを検索します。 ある **リソース グループ**全体のログをすべて表示することもできます。
2. そのリソースのブレードで、 **[イベント]** タイルが表示されるまで下へスクロールします。  
    ![イベント タイトル](./media/insights-debugging-with-events/Insights_EventsTile.png)
3. そのタイルをクリックすると、選択したリソースだけになるようにフィルター処理されたイベントが表示されます。 **[フィルター]** コマンドを使用すると、時間の範囲を変更したり、さらに詳細なフィルターを適用したりすることができます。

## <a name="next-steps"></a>次のステップ
* [アラート通知を受信](insights-receive-alert-notifications.md) します。
* [サービス メトリックスを監視](insights-how-to-customize-monitoring.md) して、サービスの可用性と応答性を確認します。
* [サービス正常性を追跡](insights-service-health.md) して、Azure でパフォーマンスの低下やサービスの中断が発生したことを検出します。  




<!--HONumber=Nov16_HO3-->



---
title: "DocumentDB の要求、使用状況、およびストレージの監視 | Microsoft Docs"
description: "パフォーマンス メトリック (要求やサーバー エラーなど) と使用状況メトリック (ストレージ消費など) を利用して、DocumentDB アカウントを監視する方法について説明します。"
services: documentdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 4c6a2e6f-6e78-48e3-8dc6-f4498b235a9e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: ed44ca2076860128b175888748cdaa8794c2310d
ms.openlocfilehash: aabca8fd228b1fc7c60d295a9502dece29db1b68
ms.lasthandoff: 01/14/2017


---
# <a name="monitor-documentdb-requests-usage-and-storage"></a>DocumentDB の要求、使用状況、およびストレージの監視
Azure DocumentDB アカウントは、 [Azure ポータル](https://portal.azure.com/)で監視できます。 各 DocumentDB アカウントに対して、パフォーマンス メトリック (要求やサーバー エラーなど) と使用状況メトリック (ストレージ消費など) が利用可能です。

メトリックは、[アカウント] ブレード、新しい [メトリック] ブレード、または Azure Monitor で確認できます。

## <a name="view-performance-metrics-on-the-metrics-blade"></a>[メトリック] ブレードでパフォーマンス メトリックを表示する
1. [Azure Portal](https://portal.azure.com/) で、**[その他のサービス]** をクリックします。**[データベース]** までスクロールし、**[NoSQL (DocumentDB)]** をクリックします。次に、パフォーマンス メトリックを表示する DocumentDB アカウントの名前をクリックします。
2. リソース メニューで、**[監視]** の下の **[メトリック]** をクリックします。

[メトリック] ブレードが開き、確認するコレクションを選択できます。 [利用可能]、[要求]、[スループット]、[ストレージ] メトリックを確認し、DocumentDB の SLA と比較することができます。

## <a name="view-performance-metrics-by-using-azure-monitoring"></a>Azure Monitoring を使用してパフォーマンス メトリックを表示する
1. [Azure Portal](https://portal.azure.com/) で、ジャンプバーの **[モニター]** をクリックします。
2. リソース メニューの **[メトリック]**をクリックします。
3. **[モニター - メトリック]** ウィンドウで、監視する DocumentDB アカウントに関連付けられたリソース グループを **[リソース グループ]** ドロップダウン メニューで選択します。 
4. **[リソース]**ドロップ ダウン メニューで、監視するデータベース アカウントを選択します。
5. **[利用可能なメトリック]** の一覧で、表示するメトリックを選択します。 複数選択するには、Cｔｒｌ キーを使用します。 

    メトリックは **[プロット]** ウィンドウに表示されます。 

## <a name="view-performance-metrics-on-the-account-blade"></a>アカウントのブレードでパフォーマンス メトリックを表示する
1. [Azure Portal](https://portal.azure.com/) で、**[その他のサービス]** をクリックします。**[データベース]** までスクロールし、**[NoSQL (DocumentDB)]** をクリックします。次に、パフォーマンス メトリックを表示する DocumentDB アカウントの名前をクリックします。
2. **[監視]** レンズには、既定では以下のタイルが表示されます。
   
   * 当日の要求数合計
   * ストレージ使用量
   
   データベースにデータがあるはずなのにテーブルに **[使用可能なデータがありません]** と表示される場合は、「 [トラブルシューティング](#troubleshooting) 」のセクションをご覧ください。
   
   ![要求とストレージ使用状況を示す [監視] レンズのスクリーン ショット](./media/documentdb-monitor-accounts/documentdb-total-requests-and-usage.png)
3. **[要求]** または **[使用量クォータ]** タイルをクリックすると、詳細な **[メトリック]** ブレードが表示されます。
4. **[メトリック]** ブレードには、選択したメトリックの詳細が表示されます。  ブレードの上部には時間別の要求数のグラフが表示され、その下には調整済み要求数と合計要求数の集計値を示す表が表示されます。  [メトリック] ブレードには、現在のメトリック ブレードに表示されるメトリックに対してフィルター処理された定義済みアラートの一覧も表示されます (これにより、さまざまなアラートがある場合、ここで示された関連するアラートのみが表示されます)。   
   
   ![Screenshot of the Metric blade which includes throttled requests](./media/documentdb-monitor-accounts/documentdb-metric-blade.png)

## <a name="customize-performance-metric-views-in-the-portal"></a>ポータルのパフォーマンス メトリック ビューをカスタマイズする
1. 特定のグラフに表示されるメトリックをカスタマイズするには、**[メトリック]** ブレード内でそのグラフをクリックし、**[グラフの​​編集]** をクリックします。  
   ![Screen shot of the Metric blade controls, with Edit chart highlighted](./media/documentdb-monitor-accounts/madocdb3.png)
2. **[グラフの編集]** ブレードには、グラフに表示されるメトリックおよび期間を変更するオプションがあります。  
   ![[グラフの編集] ブレードのスクリーンショット](./media/documentdb-monitor-accounts/madocdb4.png)
3. その部分に表示されるメトリックを変更するには、利用可能なパフォーマンス メトリックをオンまたはオフにした後、ブレードの下部にある **[OK]** をクリックするだけです。  
4. 期間を変更するには、別の期間を選択して (たとえば、**[カスタム]**)、ブレードの下部にある **[OK]** をクリックします。  
   
   ![[グラフの編集] ブレードの期間の部分で、カスタム期間の入力方法を示すスクリーンショット](./media/documentdb-monitor-accounts/madocdb5.png)

## <a name="create-side-by-side-charts-in-the-portal"></a>サイド バイ サイドのグラフを作成する
Azure ポータルでは、サイド バイ サイドのメトリック チャートを作成できます。  

1. 最初に、コピーするグラフを右クリックし、 **[カスタマイズ]**をクリックします。
   
   ![[カスタマイズ] オプションをハイライトした要求数合計チャートのスクリーンショット](./media/documentdb-monitor-accounts/madocdb6.png)
2. メニューの **[複製]** をクリックしてパーツをコピーし、**[カスタマイズの完了]** をクリックします。
   
   ![[複製] および [カスタマイズの完了] オプションをハイライトした要求数合計チャートのスクリーンショット](./media/documentdb-monitor-accounts/madocdb7.png)  

これで、このパーツを他のメトリック パーツとして扱って、パーツに表示されるメトリックと期間をカスタマイズすることができます。  これにより、2 種類のメトリック チャートを画面に並べて同時に見ることができます。  
    ![要求数合計チャートと、新たに作成した過去の要求数合計チャートのスクリーンショット](./media/documentdb-monitor-accounts/madocdb8.png)  

## <a name="set-up-alerts-in-the-portal"></a>ポータルでアラートを設定する
1. [Azure Portal](https://portal.azure.com/) で、**[その他のサービス]**、**[DocumentDB (NoSQL)]** の順にクリックし、パフォーマンス メトリック アラートを設定する DocumentDB アカウントの名前をクリックします。
2. リソース メニューで、 **[アラート ルール]** をクリックして [アラート ルール] ブレードを開きます。  
   ![[アラート ルール] パーツが選択された状態のスクリーンショット](./media/documentdb-monitor-accounts/madocdb10.5.png)
3. **[アラート ルール]** ブレードで、**[アラートの追加]** をクリックします。  
   ![[アラート ルール] ブレードで [アラートの追加] ボタンをハイライトした状態のスクリーンショット](./media/documentdb-monitor-accounts/madocdb11.png)
4. **[アラート ルールの追加]** ブレードで、以下を指定します。
   
   * 設定するアラート ルールの名前。
   * 新しいアラート ルールの説明。
   * アラート ルールのメトリック。
   * いつアラートをアクティブにするかを決定する条件、しきい値、および期間。 たとえば、過去 15 分間にサーバー エラー回数が 5 を超える場合を指定できます。
   * サービス管理者および共同管理者は、アラートが生成されると電子メールを受け取ります。
   * アラート通知用の追加の電子メール アドレス。  
     ![[アラート ルールの追加] ブレードのスクリーンショット](./media/documentdb-monitor-accounts/madocdb12.png)

## <a name="monitor-documentdb-programatically"></a>プログラムから DocumentDB を監視する
ポータルで使用できるアカウント レベルのメトリック (アカウント ストレージの使用状況、要求総数) は、DocumentDB API で使用することはできません。 ただし、DocumentDB API を使用してコレクション レベルで使用状況データを取得できます。 コレクション レベルのデータを取得するには、次の操作を行います。

* REST API を使用するには、 [コレクションに対して GET を実行](https://msdn.microsoft.com/library/mt489073.aspx)します。 コレクションのクォータおよび使用状況の情報が、応答の x-ms-resource-quota および x-ms-resource-usage ヘッダーに返されます。
* .NET SDK を使用するには、[DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) メソッドを使用します。これは、**CollectionSizeUsage**、**DatabaseUsage**、**DocumentUsage** などの多数の使用状況プロパティを含む [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) を返します。

その他のメトリックにアクセスするには、 [Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights)を使用します。 使用できるメトリック定義は、次の URL を呼び出すことで取得できます。

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

クエリでメトリックを個別に取得する場合には、次の形式を使用します。

    https://management.azure.com/subscriptions/{SubecriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z

詳しくは、[Azure Monitor REST API を使用したリソース メトリックの取得](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/02/23/retrieving-resource-metrics-via-the-azure-insights-api/)に関する記事を参照してください。 なお、"Azure Inights" は "Azure Monitor" に名称変更されましたが、  このブログ記事では古い名称を使用しています。

## <a name="troubleshooting"></a>トラブルシューティング
最近要求を作成したか、データベースにデータを追加したにもかかわらず、監視タイルに **使用可能なデータがない** という内容のメッセージが表示された場合は、タイルを編集して最新の使用状況を反映できます。

### <a name="edit-a-tile-to-refresh-current-data"></a>タイルに最新のデータを反映させる
1. 特定の部分に表示されるメトリックをカスタマイズするには、そのグラフをクリックして **[メトリック]** ブレードを開いた後、**[グラフの​​編集]** をクリックします。  
   ![Screen shot of the Metric blade controls, with Edit chart highlighted](./media/documentdb-monitor-accounts/madocdb3.png)
2. **[グラフの編集]** ブレードの **[時間範囲]** セクションで、**[過去&1; 時間]**、**[OK]** の順にクリックします。  
   ![過去&1; 時間を選択した [グラフの編集] ブレードのスクリーン ショット](./media/documentdb-monitor-accounts/documentdb-no-available-data-past-hour.png)
3. タイルが更新され、最新のデータと使用状況が表示されます。  
   ![更新後の過去の要求数合計タイルのスクリーン ショット](./media/documentdb-monitor-accounts/documentdb-no-available-data-fixed.png)

## <a name="next-steps"></a>次のステップ
DocumentDB の容量計画の詳細については、「[DocumentDB capacity planner calculator](https://www.documentdb.com/capacityplanner)」(DocumentDB Capacity Planner の計算機能) を参照してください。



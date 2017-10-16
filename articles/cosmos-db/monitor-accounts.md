---
title: "Azure Cosmos DB の要求およびストレージの監視 | Microsoft Docs"
description: "パフォーマンス メトリック (要求やサーバー エラーなど) と使用状況メトリック (ストレージ消費など) を利用して、Azure Cosmos DB アカウントを監視する方法について説明します。"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 4c6a2e6f-6e78-48e3-8dc6-f4498b235a9e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: mimig
ms.openlocfilehash: 2e5cce26bc8bebbe6b9f8ba3c3d03e8c3db8c87c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-azure-cosmos-db"></a>Azure Cosmos DB を監視する
Azure Cosmos DB アカウントは、[Azure Portal](https://portal.azure.com/) から監視できます。 Azure Cosmos DB アカウントごとに、メトリックの完全なスイートが提供されており、スループット、ストレージ、可用性、待機時間、一貫性を監視できます。

メトリックは、[アカウント] ページ、新しい [メトリック] ページ、または Azure Monitor で確認できます。

## <a name="view-performance-metrics-on-the-metrics-page"></a>[メトリック] ページでパフォーマンス メトリックを表示する
1. [Azure Portal](https://portal.azure.com/) で、**[その他のサービス]** をクリックして **[データベース]** までスクロールし、**[Azure Cosmos DB]** をクリックして、パフォーマンス メトリックを参照する Azure Cosmos DB アカウントの名前をクリックします。
2. 新しいページが読み込まれたら、リソース メニューで、**[監視]** の下にある **[メトリック]** をクリックします。
3. [メトリック] ページが開いたら、**[コレクション]** ドロップダウンから確認するコレクションを選択します。

   Azure Portal には、使用可能なコレクション メトリックのスイートが表示されます。 スループット、ストレージ、可用性、待機時間、一貫性の各メトリックは別々のタブに配置されています。 提供されているメトリックの詳細を表示するには、各メトリック ウィンドウの右上にある双方向矢印をクリックします。

   ![メトリック スイートを示す [監視] レンズのスクリーン ショット](./media/monitor-accounts/metrics-suite.png)

## <a name="view-performance-metrics-by-using-azure-monitoring"></a>Azure Monitoring を使用してパフォーマンス メトリックを表示する
1. [Azure Portal](https://portal.azure.com/) で、左側のバーの **[モニター]** をクリックします。
2. リソース メニューの **[メトリック]**をクリックします。
3. **[モニター - メトリック]** ウィンドウで、監視する Azure Cosmos DB アカウントに関連付けられたリソース グループを **[リソース グループ]** ドロップダウン メニューで選択します。 
4. **[リソース]**ドロップ ダウン メニューで、監視するデータベース アカウントを選択します。
5. **[利用可能なメトリック]** の一覧で、表示するメトリックを選択します。 複数選択するには、Cｔｒｌ キーを使用します。 

## <a name="view-performance-metrics-on-the-account-page"></a>アカウントのページでパフォーマンス メトリックを表示する
1. [Azure Portal](https://portal.azure.com/) で、**[その他のサービス]** をクリックして **[データベース]** までスクロールし、**[Azure Cosmos DB]** をクリックして、パフォーマンス メトリックを参照する Azure Cosmos DB アカウントの名前をクリックします。
2. **[監視]** レンズには、既定では以下のタイルが表示されます。
   
   * 当日の要求数合計
   * ストレージ使用量
   
   ![要求とストレージ使用状況を示す [監視] レンズのスクリーン ショット](./media/monitor-accounts/documentdb-total-requests-and-usage.png)
3. **[要求]** タイルの右上にある双方向矢印をクリックすると、詳細な **[メトリック]** ページが開きます。
4. **[メトリック]** ページには、合計要求数に関する詳細が表示されます。 

## <a name="set-up-alerts-in-the-portal"></a>ポータルでアラートを設定する
1. [Azure Portal](https://portal.azure.com/) で、**[その他のサービス]**、**[Azure Cosmos DB]** の順にクリックし、パフォーマンス メトリック アラートを設定する Azure Cosmos DB アカウントの名前をクリックします。
2. リソース メニューで、 **[アラート ルール]** をクリックして [アラート ルール] ページを開きます。  
   ![[アラート ルール] パーツが選択された状態のスクリーンショット](./media/monitor-accounts/madocdb10.5.png)
3. **[アラート ルール]** ページで、**[アラートの追加]** をクリックします。  
   ![[アラート ルール] ページで [アラートの追加] ボタンをハイライトした状態のスクリーンショット](./media/monitor-accounts/madocdb11.png)
4. **[アラート ルールの追加]** ページで、以下を指定します。
   
   * 設定するアラート ルールの名前。
   * 新しいアラート ルールの説明。
   * アラート ルールのメトリック。
   * いつアラートをアクティブにするかを決定する条件、しきい値、および期間。 たとえば、過去 15 分間にサーバー エラー回数が 5 を超える場合を指定できます。
   * サービス管理者および共同管理者は、アラートが生成されると電子メールを受け取ります。
   * アラート通知用の追加の電子メール アドレス。  
     ![[アラート ルールの追加] ページのスクリーンショット](./media/monitor-accounts/madocdb12.png)

## <a name="monitor-azure-cosmos-db-programmatically"></a>Azure Cosmos DB をプログラムで監視する
ポータルで使用できるアカウント レベルのメトリック (アカウント ストレージの使用状況、要求総数) は、DocumentDB API で使用することはできません。 ただし、DocumentDB API を使用してコレクション レベルで使用状況データを取得できます。 コレクション レベルのデータを取得するには、次の操作を行います。

* REST API を使用するには、 [コレクションに対して GET を実行](https://msdn.microsoft.com/library/mt489073.aspx)します。 コレクションのクォータおよび使用状況の情報が、応答の x-ms-resource-quota および x-ms-resource-usage ヘッダーに返されます。
* .NET SDK を使用するには、[DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) メソッドを使用します。これは、**CollectionSizeUsage**、**DatabaseUsage**、**DocumentUsage** などの多数の使用状況プロパティを含む [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) を返します。

その他のメトリックにアクセスするには、 [Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights)を使用します。 使用できるメトリック定義は、次の URL を呼び出すことで取得できます。

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

クエリでメトリックを個別に取得する場合には、次の形式を使用します。

    https://management.azure.com/subscriptions/{SubecriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z

詳しくは、[Azure Monitor REST API を使用したリソース メトリックの取得](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/02/23/retrieving-resource-metrics-via-the-azure-insights-api/)に関する記事を参照してください。 なお、"Azure Insights" は "Azure Monitor" に名称変更されましたが、  このブログ記事では古い名称を使用しています。

## <a name="next-steps"></a>次のステップ
Azure Cosmos DB の容量計画の詳細については、[Azure Cosmos DB Capacity Planner の計算機能](https://www.documentdb.com/capacityplanner)に関するページを参照してください。


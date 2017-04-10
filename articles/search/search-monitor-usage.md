---
title: "Azure Search サービスでの使用状況と統計の監視 | Microsoft Docs"
description: "Microsoft Azure のホスト型クラウド検索サービスである Azure Search のリソース使用量とインデックス サイズを追跡記録します。"
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 122948de-d29a-426e-88b4-58cbcee4bc23
ms.service: search
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/29/2016
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6e40acb01d6297aa66090859533fd48c108a9a88


---
# <a name="monitor-usage-and-query-metrics-in-an-azure-search-service"></a>Azure Search サービスでの使用状況の監視とメトリックのクエリ
Azure Search は、サービス レベルでのクエリの実行に関する統計を収集します。統計には、秒あたりのクエリ (QPS)、待機時間、およびボリュームが容量を超えた場合に破棄されたクエリの割合が含まれます。 メトリックは、ポータルの [監視] ブレードに表示されます。

   ![QPS アクティビティのスクリーン ショット][5]

この記事では、サービスのすべての機能が使用されている場合のメトリックについて説明します。 インデックス レベルでのアクティビティを調べるには、検索トラフィック分析を有効にし、Power BI を使用して分析を表示します。 詳細については、「 [Azure Search の検索トラフィックの分析](search-traffic-analytics.md) 」を参照してください。

## <a name="view-query-throughput-statistics"></a>クエリのスループット統計情報を表示する
サービス ダッシュボードの [監視] タイルをクリックして [監視] ブレードを開きます。

   ![[監視] タイル][2]

サービス レベルでのクエリ アクティビティでは、クエリの実行、待機時間、およびスロットルが記録されます。 メトリックは連続的に収集されますが、最新のアクティビティがポータルに表示されるまで数分かかります。 

詳細の表示、アラートの追加、診断の有効化、またはグラフの編集を行うには、[メトリック] タイルをクリックします。

  ![[メトリック] ブレードのコマンド][4]

### <a name="set-up-alerts"></a>アラートを設定する
メトリックの詳細ページから、クエリの実行、待機時間、またはスロットル アクティビティが定義された条件を超えた場合に電子メール通知をトリガーするアラートを構成できます。

### <a name="enable-diagnostics"></a>診断を有効化する
診断を有効にしたときは、診断データの保存場所、操作ログとメトリックを含めるかどうか、およびデータの保持期間を構成できます。

### <a name="change-chart-type-and-data-collection-interval"></a>グラフの種類とデータ収集の間隔を変更する
各メトリックごとに、**[編集]** をクリックして、表示を横棒グラフから折れ線グラフに変更したり、x 軸の時間範囲を変更したりできます。

  ![時間範囲の構成][3]

## <a name="view-counts-and-resource-usage-in-the-portal"></a>カウントとリソース使用量をポータルに表示する
インデックスとドキュメントのサイズの増加率を追跡することで、サービスに設定した上限に達する前に、容量を事前に調整することができます。 

リソース使用量を監視するには、サービスのカウントと統計を[ポータル](https://portal.azure.com)に表示します。 カスタム サービス管理ツールを構築する場合は、これらの情報をプログラムによって取得することもできます。

1. [ポータル](https://portal.azure.com)にサインインします。 
2. Azure Search サービスのサービス ダッシュボードを開きます。 [ホーム] ページに、サービスのタイルが表示されます。またはジャンプ バーの [参照] で、サービスを参照することもできます。 

[使用] セクションには、使用可能なリソースのうち、現在使用中の割合を示すメーターがあります。 インデックス、ドキュメント、および記憶域のサービスごとの制限に関する情報については、「[サービスの制限](search-limits-quotas-capacity.md)」を参照してください。

  ![[使用量] タイル][1]

> [!NOTE]
> 上記のスクリーン ショットは、無料サービスのデータを示しています。無料サービスでは、最大 1 つのレプリカとパーティションを使用でき、3 つのインデックス、10,000 個のドキュメント、または 50 MB のデータのいずれかが所定の数値に達するまでホストされます。 Basic または Standard レベルで作成されたサービスでは、サービス制限ははるかに高くなります。 レベル選択の詳細については、「[SKU または価格レベルの選択](search-sku-tier.md)」を参照してください。
> 
> 

### <a name="get-index-statistics-using-the-rest-api"></a>REST API を使用したインデックス統計情報の取得
Azure Search REST API または .NET SDK を使用することにより、プログラムからサービス メトリックにアクセスできます。  [インデクサー](https://msdn.microsoft.com/library/azure/dn946891.aspx) を使用して Azure SQL Database または DocumentDB からのインデックスの読み込む際には、必要な数値を取得するために追加されている API を使用できます。 

* [インデックス統計の取得](https://msdn.microsoft.com/library/azure/dn798942.aspx)
* [ドキュメントの数](https://msdn.microsoft.com/library/azure/dn798924.aspx)
* [インデクサーの状態の取得](https://msdn.microsoft.com/library/azure/dn946884.aspx)

## <a name="next-steps"></a>次のステップ
[レプリカとパーティションのスケーリング](search-limits-quotas-capacity.md)に関するページで、既存のサービスに対するパーティションとレプリカの割り当てのバランスをとる方法を検討します。 

サービス管理の詳細については、[Microsoft Azure での Search サービスの管理](search-manage.md)に関する記事を、チューニング ガイダンスについては、[パフォーマンスと最適化](search-performance-optimization.md)に関する考慮事項を参照してください。

<!--Image references-->
[1]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG
[2]: ./media/search-monitor-usage/AzSearch-Monitor-Tile.PNG
[3]: ./media/search-monitor-usage/AzSearch-Monitor-Intervals.PNG
[4]: ./media/search-monitor-usage/AzSearch-Monitor-AlertCmd.PNG
[5]: ./media/search-monitor-usage/AzSearch-Monitor-BarChart.PNG








<!--HONumber=Nov16_HO3-->



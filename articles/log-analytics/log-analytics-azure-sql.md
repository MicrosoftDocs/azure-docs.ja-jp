---
title: "Log Analytics の Azure SQL Analytics ソリューション | Microsoft Docs"
description: "Azure SQL Analytics ソリューションを使用して、Azure SQL データベースを管理できます。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 0184e95ca56e4bc4ffbe860da2b7a5cae9b5a043
ms.lasthandoff: 03/11/2017


---


# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview-in-log-analytics"></a>Log Analytics の Azure SQL Analytics (プレビュー) を使用した Azure SQL Database の監視

Azure Log Analytics の Azure SQL の監視ソリューションは、SQL Azure の重要なパフォーマンス メトリックを収集し、視覚化するものです。 このソリューションで収集したメトリックを使用して、独自の監視ルールおよびアラートを作成できます。 また、Azure SQL Database とエラスティック プールのメトリックを、複数の Azure サブスクリプションとエラスティック プールにわたって監視し、視覚化することもできます。 このソリューションは、アプリケーション スタックの各層の問題を特定する際にも役立ちます。  [Azure 診断メトリック](log-analytics-azure-storage.md)と Log Analytics ビューを使用して、すべての Azure SQL データベースとエラスティック プールのデータを単一の Log Analytics ワークスペースに表示します。

このソリューションは現在プレビュー段階にあり、ワークスペースごとに最大 150,000 個の Azure SQL データベースと、最大 5,000 個の SQL エラスティック プールをサポートしています。

この Azure SQL の監視ソリューションは、Log Analytics で利用可能な他のソリューションと同様に、Azure リソース (このソリューションの場合は Azure SQL Database) の正常性の監視と、それに関する通知の受信ができます。 Microsoft Azure SQL Database は、使い慣れた SQL Server と同様の機能を、Azure クラウドで実行されているアプリケーションで提供する、スケーラブルなリレーショナル データベース サービスです。 Log Analytics では、収集、関連付けのほか、構造化データおよび非構造化データの視覚化ができます。

## <a name="connected-sources"></a>接続先ソース

Azure SQL の監視ソリューションでは、Log Analytics サービスに接続するためのエージェントを使用しません。

次の表は、このソリューションの接続先としてサポートされているソースとその説明です。

| 接続されているソース | サポート | 説明 |
| --- | --- | --- |
| [Windows エージェント](log-analytics-windows-agents.md) | いいえ | このソリューションでは、直接の Windows エージェントは使用されません。 |
| [Linux エージェント](log-analytics-linux-agents.md) | いいえ | このソリューションでは、直接の Linux エージェントは使用されません。 |
| [SCOM 管理グループ](log-analytics-om-agents.md) | いいえ | このソリューションでは、SCOM エージェントから Log Analytics への直接接続は使用しません。 |
| [Azure Storage アカウント](log-analytics-azure-storage.md) | いいえ | Log Analytics は、ストレージ アカウントからデータを読み取ることはしません。 |
| [Azure 診断](log-analytics-azure-storage.md) | はい | Azure のメトリック データは、Azure によって直接 Log Analytics に送信されます。 |

## <a name="prerequisites"></a>前提条件

1. Azure サブスクリプション。 持っていない場合は、[無料](https://azure.microsoft.com/free/)で作成できます。
2. Log Analytics ワークスペース。 既存のものを使用します。ソリューションの使用を開始する前に[新しく作成](log-analytics-get-started.md)することもできます。
3. Azure SQL データベースとエラスティック プールの Azure 診断を有効にして、[Log Analytics にデータを送信するように構成](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/)します。

## <a name="configuration"></a>構成

Azure SQL の監視ソリューションをワークスペースに追加するには、次の手順を実行します。

1. Azure SQL Analytics ソリューションをワークスペースに追加します。[Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview) から追加するか、[ソリューション ギャラリーからの Log Analytics ソリューションの追加](log-analytics-add-solutions.md)に関するページに説明されている手順に従って追加してください。
2. Azure Portal で **[新規]** (+ 記号) をクリックし、次にリソースの一覧で **[監視 + 管理]** を選択します。  
    ![監視 + 管理](./media/log-analytics-azure-sql/monitoring-management.png)
3. **[監視 + 管理]** の一覧で、**[すべて表示]** をクリックします。
4. **[Recommended (推奨)]** の一覧で **[詳細]** をクリックし、新しい一覧で **[Azure SQL Analytics (Preview) (Azure SQL Analytics (プレビュー))]** を探して選択します。  
    ![Azure SQL Analytics のソリューション](./media/log-analytics-azure-sql/azure-sql-solution-portal.png)
5. **[Azure SQL Analytics (Preview) (Azure SQL Analytics (プレビュー))]** ブレードで、**[作成]** をクリックします。  
    ![作成](./media/log-analytics-azure-sql/portal-create.png)
6. **[新しいソリューションの作成]** ブレードで、ソリューションを追加するワークスペースを選択し、**[作成]** をクリックします。  
    ![ワークスペースに追加](./media/log-analytics-azure-sql/add-to-workspace.png)


### <a name="to-configure-multiple-azure-subscriptions"></a>複数の Azure サブスクリプションの構成方法

複数のサブスクリプションをサポートするためには、「[Enable Azure resource metrics logging using PowerShell (PowerShell を使用して Azure リソース メトリックのログ記録を有効にする)](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/)」の PowerShell スクリプトを使用します。 スクリプトを実行するときにパラメーターとしてワークスペース リソース ID を入力すると、1 つの Azure サブスクリプション内のリソースから別の Azure サブスクリプションのワークスペースに診断データを送信できます。

**例**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>ソリューションの使用

ソリューションをワークスペースに追加すると、Azure SQL Analytics のタイルがワークスペースに追加され、[概要] に表示されます。 このタイルには、Azure SQL データベースと、ソリューションが接続されている Azure SQL エラスティック プールの数が表示されます。

![Azure SQL Analytics のタイル](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-monitoring-data"></a>Azure SQL の監視データの表示

**[Azure SQL Monitoring (Azure SQL の監視)]** タイルをクリックして、Azure SQL Analytics ダッシュボードを開きます。 ダッシュボードには、次の表に示した列が存在します。 それぞれの列には、特定のスコープと時間範囲について、その列の基準に該当する項目が最大&10; 個表示されます。 すべてのレコードを返すログ検索を実行するには、列の一番下にある **[すべて表示]** をクリックするか、列ヘッダーをクリックします。

[サービス レベルごとの SQL Database のオプションとパフォーマンス](../sql-database/sql-database-service-tiers.md)に関する記事を確認してください。



![Azure SQL Analytics ダッシュボード](./media/log-analytics-azure-sql/azure-sql-dash-01.png)



![Azure SQL Analytics ダッシュボード](./media/log-analytics-azure-sql/azure-sql-dash-02.png)

| 分割 | Description |
| --- | --- |
| **Azure SQL Database Analytics** | &nbsp; |
| [Top N Databases by DTU Utilization &gt; 90% (DTU 使用率が 90% を超えた上位 N 個のデータベース)] | このパネルは、選択された期間内に DTU 使用率が 90% を超えていた Azure SQL データベースの数を表示します。 上部のタイルでは、Log Analytics で監視しているすべてのデータベースのうち、指定された期間内に利用可能なものとして割り当てられていた DTU の 90% 超を消費したデータベースの数が表示されます。  データベース名をクリックすると、ログ検索を実行できます。これにより、そのデータベースの DTU 使用率と、ワークスペースで監視している他のデータベースの DTU 使用率とを比較した折れ線グラフが表示されます。 |
| [Top N Databases by CPU Utilization &gt; 90% (CPU 使用率が 90% を超えた上位 N 個のデータベース)] | このパネルは、選択された期間内に CPU 使用率が 90% を超えていた Azure SQL データベースの数を表示します。 上部のタイルでは、Log Analytics で監視しているすべてのデータベースのうち、指定された期間内に利用可能なものとして割り当てられていた CPU の 90% 超を消費したデータベースの数が表示されます。  データベース名をクリックすると、ログ検索を実行できます。これにより、そのデータベースの CPU 使用率と、ワークスペースで監視している他のデータベースの CPU 使用率とを比較した折れ線グラフが表示されます。 |
| [Top N Databases by Storage Consumption &gt; 90% (ストレージ消費量が 90% を超えた上位 N 個のデータベース)] | このパネルは、選択された期間内に、割り当てられていたストレージの 90% 超を消費した Azure SQL データベースの数を表示します。 上部のタイルでは、Log Analytics で監視しているすべてのデータベースのうち、指定された期間内に 90% のしきい値を超えたデータベースの数が表示されます。  データベース名をクリックすると、ログ検索を実行できます。これにより、そのデータベースのストレージ消費量と、ワークスペースで監視している他のデータベースのストレージ消費量とを比較した折れ線グラフが表示されます。 |
| **Azure SQL エラスティック プール** | &nbsp; |
| [Top N Elastic Pools by DTU &gt; 90% (DTU が 90% を超えた上位 N 個のエラスティック プール)] | このパネルは、選択された期間内に、割り当てられていた DTU の 90% 超を消費した Azure SQL エラスティック プールの数を表示します。 上部のタイルでは、Log Analytics で監視しているすべての Azure SQL エラスティック プールのうち、指定された期間内に 90% のしきい値を超えたエラスティック プールの数が表示されます。  エラスティック プール名をクリックすると、ログ検索を実行できます。これにより、そのエラスティック プールのストレージ消費量と、ワークスペースで監視している他のエラスティック プールのストレージ消費量とを比較した折れ線グラフが表示されます。 |
| [Top N Elastic Pools by CPU &gt; 90% (CPU が 90% を超えた上位 N 個のエラスティック プール)] | このパネルは、選択された期間内に CPU 使用率が 90% を超えていた Azure SQL エラスティック プールの数を表示します。 上部のタイルでは、Log Analytics で監視しているすべての Azure SQL エラスティック プールのうち、指定された期間内に 90% のしきい値を超えたエラスティック プールの数が表示されます。  エラスティック プール名をクリックすると、ログ検索を実行できます。これにより、そのエラスティック プールの CPU 使用率と、ワークスペースで監視している他のエラスティック プールの CPU 使用率とを比較した折れ線グラフが表示されます。 |
| [Top N Elastic Pools by Storage Consumption &gt; 90% (ストレージ消費量が 90% を超えた上位 N 個のエラスティック プール)] | このパネルは、選択された期間内に、割り当てられていたストレージの 90% 超を消費した Azure SQL エラスティック プールの数を表示します。 上部のタイルでは、Log Analytics で監視しているすべてのエラスティック プールのうち、指定された期間内に 90% のしきい値を超えたエラスティック プールの数が表示されます。  エラスティック プール名をクリックすると、ログ検索を実行できます。これにより、そのエラスティック プールのストレージ消費量と、ワークスペースで監視している他のエラスティック プールのストレージ消費量とを比較した折れ線グラフが表示されます。 |
| **Azure SQL アクティビティ ログ** | &nbsp; |
| [SQL Azure Activity Audit (SQL Azure のアクティビティの監査)] | このパネルには、選択された期間内における Azure SQL に関連する Azure アクティビティ レコードの数が表示されます。 項目をクリックすると、ログ検索が実行され、項目の追加の詳細情報が表示されます。 |



### <a name="analyze-data-and-create-alerts"></a>データの分析とアラートの作成

このソリューションには、データを分析するのに役立つ便利なクエリが用意されています。 右へスクロールすると、使用頻度の高いいくつかのクエリがダッシュボードに一覧表示されます。そのクエリをクリックすると、Azure SQL データを探す[ログ検索](log-analytics-log-searches.md)が実行されます。

![クエリ](./media/log-analytics-azure-sql/azure-sql-queries.png)

このソリューションには、上に示したように、Azure SQL データベースとエラスティック プールの両方の特定のしきい値に関してアラートを生成するために使用できる、"*アラートに基づくクエリ*" がいくつか用意されています。

#### <a name="to-configure-an-alert-for-your-workspace"></a>ワークスペースのアラートの構成方法

1. [OMS ポータル](http://mms.microsoft.com/)に移動し、サインインします。
2. ソリューション用に構成したワークスペースを開きます。
3. [概要] ページの **[Azure SQL Analytics (Preview) (Azure SQL Analytics (プレビュー))]** タイルをクリックします。
4. 右にスクロールし、クエリをクリックして、アラートの作成を開始します。  
![アラート クエリ](./media/log-analytics-azure-sql/alert-query.png)
5. [ログ検索] で **[アラート]** をクリックします。  
![検索でアラートを作成](./media/log-analytics-azure-sql/create-alert01.png)
6. **[アラート ルールの追加]** ページで、適切なプロパティと特定のしきい値を構成し、**[保存]** をクリックします。  
![アラート ルールの追加](./media/log-analytics-azure-sql/create-alert02.png)

### <a name="act-on-azure-sql-monitoring-data"></a>Azure SQL の監視データの利用

たとえば、実行可能で非常に便利なクエリの&1; つに、すべての Azure サブスクリプションにわたるすべての Azure SQL エラスティック プールの DTU 使用率を比較するクエリがあります。 データベース スループット ユニット (DTU) は、Basic、Standard、Premium の各パフォーマンス レベルのデータベースとプールの性能の相対評価を表します。 DTU は、CPU、メモリ、読み書きレートを組み合わせた指標に基づいて算出されます。 DTU が大きいほど、そのパフォーマンス レベルの処理能力が高いことになります。 たとえば、あるパフォーマンス レベルの DTU が 5 であれば、DTU が 1 のパフォーマンス レベルと比較して 5 倍の処理能力があることを意味します。 DTU クォータの上限は、各サーバーとエラスティック プールに適用されます。

次のログ検索クエリを実行することで、SQL Azure のエラスティック プールを十分に活用できているか、または使いすぎていないかを簡単に判断できます。

```
Type=AzureMetrics ResourceId=*"/ELASTICPOOLS/"* MetricName=dtu_consumption_percent | measure avg(Average) by Resource | display LineChart
```

次の例では、1 つのエラスティック プールの DTU 使用率が 100% 近くまで上昇しているのに対し、他のエラスティック プールの使用量はごくわずかであることを確認できます。 さらに、Azure のアクティビティ ログを使えば、最近になって環境に生じた変更の有無を確認し、トラブルシューティングを行うことができます。

![ログ検索結果 - 高い使用率](./media/log-analytics-azure-sql/log-search-high-util.png)

## <a name="see-also"></a>関連項目

- Log Analytics の[ログ検索](log-analytics-log-searches.md)機能を使用して、詳細な Azure SQL データを確認します。
- Azure SQL データを表示する[独自のダッシュ ボードを作成](log-analytics-dashboards.md)します。
- Azure SQL の特定のイベントが発生した場合の[アラートを作成](log-analytics-alerts.md)します。


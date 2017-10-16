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
ms.date: 09/26/2017
ms.author: banders
ms.openlocfilehash: 0b0d91b130172eb3506fdebb9547ab6ba5cc3780
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview-in-log-analytics"></a>Log Analytics の Azure SQL Analytics (プレビュー) を使用した Azure SQL Database の監視

![Azure SQL Analytics のシンボル](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Azure Log Analytics の Azure SQL Analytics ソリューションは、SQL Azure の重要なパフォーマンス メトリックを収集し、視覚化します。 このソリューションで収集したメトリックを使用して、独自の監視ルールおよびアラートを作成できます。 また、Azure SQL Database とエラスティック プールのメトリックを、複数の Azure サブスクリプションとエラスティック プールにわたって監視し、視覚化することもできます。 このソリューションは、アプリケーション スタックの各層の問題を特定する際にも役立ちます。  [Azure 診断メトリック](log-analytics-azure-storage.md)と Log Analytics ビューを使用して、すべての Azure SQL データベースとエラスティック プールのデータを単一の Log Analytics ワークスペースに表示します。

このソリューションは現在プレビュー段階にあり、ワークスペースごとに最大 150,000 個の Azure SQL データベースと、最大 5,000 個の SQL エラスティック プールをサポートしています。

この Azure SQL Analytics ソリューションでは、Log Analytics で利用可能な他のソリューションと同様に、Azure リソース (この場合は Azure SQL Database) の正常性の監視と、それに関する通知の受信ができます。 Microsoft Azure SQL Database は、使い慣れた SQL Server と同様の機能を、Azure クラウドで実行されているアプリケーションで提供する、スケーラブルなリレーショナル データベース サービスです。 Log Analytics では、収集、関連付けのほか、構造化データおよび非構造化データの視覚化ができます。

## <a name="connected-sources"></a>接続先ソース

Azure SQL Analytics ソリューションでは、Log Analytics サービスに接続するためのエージェントを使用しません。

次の表は、このソリューションの接続先としてサポートされているソースとその説明です。

| 接続されているソース | サポート | 説明 |
| --- | --- | --- |
| [Windows エージェント](log-analytics-windows-agents.md) | いいえ | このソリューションでは、直接の Windows エージェントは使用されません。 |
| [Linux エージェント](log-analytics-linux-agents.md) | いいえ | このソリューションでは、直接の Linux エージェントは使用されません。 |
| [SCOM 管理グループ](log-analytics-om-agents.md) | いいえ | このソリューションでは、SCOM エージェントから Log Analytics への直接接続は使用しません。 |
| [Azure Storage アカウント](log-analytics-azure-storage.md) | いいえ | Log Analytics は、ストレージ アカウントからデータを読み取ることはしません。 |
| [Azure 診断](log-analytics-azure-storage.md) | あり | Azure のメトリックおよびログ データは、Azure によって直接 Log Analytics に送信されます。 |

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 持っていない場合は、[無料](https://azure.microsoft.com/free/)で作成できます。
- Log Analytics ワークスペース。 既存のものを使用します。ソリューションの使用を開始する前に[新しく作成](log-analytics-get-started.md)することもできます。
- Azure SQL データベースとエラスティック プールの Azure 診断を有効にして、[Log Analytics にデータを送信するように構成](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/)します。

## <a name="configuration"></a>構成

Azure SQL Analytics ソリューションをワークスペースに追加するには、次の手順を実行します。

1. Azure SQL Analytics ソリューションをワークスペースに追加します。[Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview) から追加するか、[ソリューション ギャラリーからの Log Analytics ソリューションの追加](log-analytics-add-solutions.md)に関するページに説明されている手順に従って追加してください。
2. Azure Portal で **[新規]** (+ 記号) をクリックし、次にリソースの一覧で **[監視 + 管理]** を選択します。  
    ![監視 + 管理](./media/log-analytics-azure-sql/monitoring-management.png)
3. **[監視 + 管理]** の一覧で、**[すべて表示]** をクリックします。
4. **[Recommended (推奨)]** の一覧で **[詳細]** をクリックし、新しい一覧で **[Azure SQL Analytics (Preview) (Azure SQL Analytics (プレビュー))]** を探して選択します。  
    ![Azure SQL Analytics のソリューション](./media/log-analytics-azure-sql/azure-sql-solution-portal.png)
5. **[Azure SQL Analytics (プレビュー)]** ウィンドウで、**[作成]** をクリックします。  
    ![作成](./media/log-analytics-azure-sql/portal-create.png)
6. **[新しいソリューションの作成]** ウィンドウで、ソリューションを追加するワークスペースを選択し、**[作成]** をクリックします。  
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

>[!NOTE]
> Azure SQL Analytics の最新バージョンを取得するために、Log Analytics ワークスペースをアップグレードします。
>

ソリューションをワークスペースに追加すると、Azure SQL Analytics のタイルがワークスペースに追加され、[概要] に表示されます。 このタイルには、Azure SQL データベースと、ソリューションが接続されている Azure SQL エラスティック プールの数が表示されます。

![Azure SQL Analytics のタイル](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>Azure SQL Analytics データの表示

**[Azure SQL Analytics]** タイルをクリックして、Azure SQL Analytics ダッシュボードを開きます。 ダッシュボードには、さまざまなパースペクティブから監視されるすべてのデータベースの概要が含まれています。 さまざまなパースペクティブが動作するには、適切なメトリックを有効にするか、SQL リソースにログオンして、Azure Log Analytics ワークスペースにストリーミングする必要があります。 

![Azure SQL Analytics の概要](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

タイルのいずれかを選択すると、特定のパースペクティブでドリルダウン レポートが開きます。

![Azure SQL Analytics のタイムアウト](./media/log-analytics-azure-sql/azure-sql-sol-timeouts.png)

各パースペクティブは、サブスクリプション、サーバー、エラスティック プール、およびデータベース レベルの概要を提供します。 さらに、各パースペクティブは、右側にパースペクティブ特定のレポートを示します。 一覧からサブスクリプション、サーバー、プール、またはデータベースを選択するとドリル ダウンが続行されます。

| パースペクティブ | Description |
| --- | --- |
| 種類別のリソース | 監視対象のすべてのリソースをカウントするパースペクティブです。 ドリルダウンは、DTU および GB のメトリックの概要を示します。 |
| 洞察 | インテリジェントな洞察の階層型のドリルダウンを提供します。 インテリジェントな洞察の詳細を参照してください。 |
| Errors | データベースで発生した SQL エラーの階層型のドリルダウンを提供します |
| タイムアウト | データベースで発生した SQL タイムアウトの階層型のドリルダウンを提供します |
| ブロッキング | データベースで発生した SQL ブロッキングの階層型のドリルダウンを提供します |
| データベース待機 | データベース レベルで発生した SQL 待機統計の階層型のドリルダウンを提供します 合計待機時間と待機の種類ごとの待機時間の概要が含まれます。 |
| クエリ実行時間 | クエリの実行時間、CPU 使用率、データ IO 使用率、ログ IO 使用率などのクエリ実行の統計の階層型のドリルダウンを提供します。 |
| クエリ待機 | 待機カテゴリ別に、クエリ待機統計の階層型のドリルダウンを提供します |

### <a name="intelligent-insights-report"></a>Intelligent Insights レポート

収集されたすべてのインテリジェントな洞察を Insights パースペクティブを使用して視覚化およびアクセスできます。 [Intelligent Insights の詳細については、こちらをクリックしてください](../sql-database/sql-database-intelligent-insights.md)

![Azure SQL Analytics Insights](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>エラスティック プールとデータベースのレポート

エラスティック プールとデータベースの両方に、指定された期間にリソース用に収集されるすべてのデータを表示する独自の具体的なレポートがあります。

![Azure SQL Analytics データベース](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Azure SQL Analytics エラスティック プール](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>クエリのレポート

クエリの実行時間とクエリの待機のパースペクティブでは、クエリ レポートを介してクエリのパフォーマンスを関連付けることができます。 このレポートは、異なるデータベース間で、クエリのパフォーマンスを比較し、選択したクエリの処理速度が速いデータベースと遅いデータベースを簡単に特定できるようになります。

![Azure SQL Analytics のクエリ](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="analyze-data-and-create-alerts"></a>データの分析とアラートの作成

アラートは、Azure SQL Database リソースから送られるデータを使用して簡単に作成できます。 アラートに使用できる実用的な[ログ検索](log-analytics-log-searches.md)クエリを以下に 2 つ示しました。

*高 DTU (Azure SQL Database 上)*

```
AzureMetrics | where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/DATABASES/" and MetricName=="dtu_consumption_percent" | summarize avg(Maximum) by ResourceId
```

*高 DTU (Azure SQL Database エラスティック プール上)*

```
AzureMetrics | where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/ELASTICPOOLS/" and MetricName=="dtu_consumption_percent" | summarize avg(Maximum) by ResourceId
```

こうしたアラートに基づくクエリを使用して、Azure SQL Database とエラスティック プールの両方の特定のしきい値に関してアラートを生成することができます。 OMS ワークスペースのアラートを構成するには:

#### <a name="to-configure-an-alert-for-your-workspace"></a>ワークスペースのアラートの構成方法

1. [OMS ポータル](http://mms.microsoft.com/)に移動し、サインインします。
2. ソリューション用に構成したワークスペースを開きます。
3. [概要] ページの **[Azure SQL Analytics (Preview) (Azure SQL Analytics (プレビュー))]** タイルをクリックします。
4. いずれかのクエリの例を実行します。
5. [ログ検索] で **[アラート]** をクリックします。  
![検索でアラートを作成](./media/log-analytics-azure-sql/create-alert01.png)
6. **[アラート ルールの追加]** ページで、適切なプロパティと特定のしきい値を構成し、**[保存]** をクリックします。  
![アラート ルールの追加](./media/log-analytics-azure-sql/create-alert02.png)

## <a name="see-also"></a>関連項目

- Log Analytics の[ログ検索](log-analytics-log-searches.md)機能を使用して、詳細な Azure SQL データを確認します。
- Azure SQL データを表示する[独自のダッシュ ボードを作成](log-analytics-dashboards.md)します。
- Azure SQL の特定のイベントが発生した場合の[アラートを作成](log-analytics-alerts.md)します。

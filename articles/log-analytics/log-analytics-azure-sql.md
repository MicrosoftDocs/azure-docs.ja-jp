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
ms.date: 07/13/2017
ms.author: banders
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: cab45cc6dd621eb4a95ef5f1842ec38c25e980b6
ms.contentlocale: ja-jp
ms.lasthandoff: 07/28/2017

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
| [Azure 診断](log-analytics-azure-storage.md) | はい | Azure のメトリック データは、Azure によって直接 Log Analytics に送信されます。 |

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

### <a name="viewing-azure-sql-analytics-data"></a>Azure SQL Analytics データの表示

**[Azure SQL Analytics]** タイルをクリックして、Azure SQL Analytics ダッシュボードを開きます。 ダッシュボードには、以下に定義されているブレードが含まれます。 各ブレードには、最大 15 個のリソース (サブスクリプション、サーバー、エラスティック プール、およびデータベース) がリストされます。 リソースのいずれかをクリックすると、その特定のリソースのダッシュボードが開きます。 [エラスティック プール] または [データベース] には、選択したリソースのメトリックを示すグラフが含まれています。 グラフをクリックすると、[ログ検索] ダイアログ ボックスが開きます。

| ブレード | Description |
|---|---|
| サブスクリプション | 接続されているサーバー、プール、およびデータベースの数を含むサブスクリプションのリスト。 |
| サーバー | 接続されているプールおよびデータベースの数を含むサーバーのリスト。 |
| エラスティック プール | 監視期間における最大 GB と eDTU を含む、接続されているエラスティック プールのリスト。 |
|データベース | 監視期間における最大 GB と DTU を含む、接続されているデータベースのリスト。|


### <a name="analyze-data-and-create-alerts"></a>データの分析とアラートの作成

アラートは、Azure SQL Database リソースから送られるデータを使用して簡単に作成できます。 アラートに使用できる実用的な[ログ検索](log-analytics-log-searches.md)クエリを以下に 2 つ示しました。

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]


*高 DTU (Azure SQL Database 上)*

```
Type=AzureMetrics ResourceProvider="MICROSOFT.SQL" ResourceId=*"/DATABASES/"* MetricName=dtu_consumption_percent | measure Avg(Average) by Resource interval 5minutes
```

*高 DTU (Azure SQL Database エラスティック プール上)*

```
Type=AzureMetrics ResourceProvider="MICROSOFT.SQL" ResourceId=*"/ELASTICPOOLS/"* MetricName=dtu_consumption_percent | measure avg(Average) by Resource interval 5minutes
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

### <a name="act-on-azure-sql-analytics-data"></a>Azure SQL Analytics データの利用

たとえば、実行可能で非常に便利なクエリの 1 つに、すべての Azure サブスクリプションにわたるすべての Azure SQL エラスティック プールの DTU 使用率を比較するクエリがあります。 データベース スループット ユニット (DTU) は、Basic、Standard、Premium の各パフォーマンス レベルのデータベースとプールの性能の相対評価を表します。 DTU は、CPU、メモリ、読み書きレートを組み合わせた指標に基づいて算出されます。 DTU が大きいほど、そのパフォーマンス レベルの処理能力が高いことになります。 たとえば、あるパフォーマンス レベルの DTU が 5 であれば、DTU が 1 のパフォーマンス レベルと比較して 5 倍の処理能力があることを意味します。 DTU クォータの上限は、各サーバーとエラスティック プールに適用されます。

次のログ検索クエリを実行することで、SQL Azure のエラスティック プールを十分に活用できているか、または使いすぎていないかを簡単に判断できます。

```
Type=AzureMetrics ResourceId=*"/ELASTICPOOLS/"* MetricName=dtu_consumption_percent | measure avg(Average) by Resource | display LineChart
```

>[!NOTE]
> ワークスペースが[新しい Log Analytics クエリ言語](log-analytics-log-search-upgrade.md)にアップグレードされている場合は、上記のクエリは次のように変更されます。
>
>`search in (AzureMetrics) isnotempty(ResourceId) and "/ELASTICPOOLS/" and MetricName == "dtu_consumption_percent" | summarize AggregatedValue = avg(Average) by bin(TimeGenerated, 1h), Resource | render timechart`

次の例では、1 つのエラスティック プールの DTU 使用率が 100% 近くまで上昇しているのに対し、他のエラスティック プールの使用量はごくわずかであることを確認できます。 さらに、Azure のアクティビティ ログを使えば、最近になって環境に生じた変更の有無を確認し、トラブルシューティングを行うことができます。

![ログ検索結果 - 高い使用率](./media/log-analytics-azure-sql/log-search-high-util.png)

## <a name="see-also"></a>関連項目

- Log Analytics の[ログ検索](log-analytics-log-searches.md)機能を使用して、詳細な Azure SQL データを確認します。
- Azure SQL データを表示する[独自のダッシュ ボードを作成](log-analytics-dashboards.md)します。
- Azure SQL の特定のイベントが発生した場合の[アラートを作成](log-analytics-alerts.md)します。


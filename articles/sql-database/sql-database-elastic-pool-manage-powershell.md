---
title: "PowerShell: Azure SQL エラスティック プールの作成と管理 | Microsoft Docs"
description: "PowerShell を使ってエラスティック プールを管理する方法について説明します。"
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 61289770-69b9-4ae3-9252-d0e94d709331
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 06/22/2016
ms.author: srinia
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: ffcf0f0aa80f0a6b65cbef65e361e4830fcca3ff
ms.lasthandoff: 03/10/2017


---

# <a name="create-and-manage-an-elastic-pool-with-powershell"></a>PowerShell を使用したエラスティック プールの作成と管理
このトピックでは、PowerShell を使用してスケーラブルな[エラスティック プール](sql-database-elastic-pool.md)を作成および管理する方法について説明します。  Azure エラスティック プールは、[Azure Portal](https://portal.azure.com/)、REST API、[C#](sql-database-elastic-pool-manage-csharp.md) を使用して作成および管理することもできます。 また、[Transact-SQL](sql-database-elastic-pool-manage-tsql.md) を使用して、データベースを作成したり、エラスティック プールに出し入れしたりすることもできます。

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-an-elastic-pool"></a>エラスティック プールの作成
エラスティック プールを作成するには、[New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378\(v=azure.300\).aspx) コマンドレットを使用します。 プールあたりの eDTU、最小 DTU、最大 DTU は、サービス レベルの値 (Basic、Standard、Premium) によって制限されます。 [エラスティック プールとエラスティック データベースの eDTU とストレージの制限](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools)に関するセクションを参照してください。

    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

## <a name="create-a-pooled-database-in-an-elastic-pool"></a>エラスティック プールにプールされるデータベースの作成
[New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339\(v=azure.300\).aspx) コマンドレットを使用して **ElasticPoolName** パラメーターを対象のプールに設定します。 既存のデータベースをエラスティック プールに移動する方法については、「[エラスティック プールへのデータベースの移動](sql-database-elastic-pool-manage-powershell.md#move-a-database-into-an-elastic-pool)」を参照してください。

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

### <a name="complete-script"></a>完全なスクリプト
これは、Azure リソース グループとサーバーを作成するスクリプトです。 メッセージが表示されたら、(自分の Azure 資格情報ではなく) 新しいサーバーの管理者のユーザー名とパスワードを指定してください。

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB

## <a name="create-an-elastic-pool-and-add-multiple-pooled-databases"></a>エラスティック プールの作成と、複数のプールされるデータベースの追加
ポータルまたは PowerShell コマンドレットで一度に作成できるデータベースは&1; つのみであるため、エラスティック プールに多数のデータベースを作成しようとすると時間がかかることがあります。 エラスティック プールへの作成処理を自動化する方法については、[CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae) に関するページを参照してください。   

## <a name="move-a-database-into-an-elastic-pool"></a>エラスティック プールへのデータベースの移動
エラスティック プールへの、またはエラスティック プールからのデータベースの移動は、[Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx) で行うことができます。

    Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="change-performance-settings-of-an-elastic-pool"></a>エラスティック プールのパフォーマンス設定の変更
パフォーマンスが低下したときは、成長に合わせてプールの設定を変更できます。 [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511\(v=azure.300\).aspx) コマンドレットを使用します。 -Dtu パラメーターにプールあたりの eDTU 数を設定します。 使用可能な値については、[eDTU とストレージの制限](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools)に関するセクションをご覧ください。  

    Set-AzureRmSqlElasticPool -ResourceGroupName “resourcegroup1” -ServerName “server1” -ElasticPoolName “elasticpool1” -Dtu 1200 -DatabaseDtuMax 100 -DatabaseDtuMin 50

## <a name="get-the-status-of-pool-operations"></a>プール操作の状態を取得する
エラスティック プールの作成には時間がかかることがあります。 作成や更新など、プール操作の状態を追跡するには、[Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812\(v=azure.300\).aspx) コマンドレットを使用します。

    Get-AzureRmSqlElasticPoolActivity -ResourceGroupName “resourcegroup1” -ServerName “server1” -ElasticPoolName “elasticpool1”

## <a name="get-the-status-of-moving-a-database-into-and-out-of-an-elastic-pool"></a>データベースをエラスティック プールに出し入れする際の状態の取得
データベースの移動には時間がかかることがあります。 移動の状態を追跡するには、[Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687\(v=azure.300\).aspx) コマンドレットを使用します。

    Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="get-resource-usage-data-for-an-elastic-pool"></a>エラスティック プールのリソース使用状況データの取得
リソース プールの制限値のパーセンテージとして取得できるメトリックを以下に示します。   

| メトリックの名前 | 説明 |
|:--- |:--- |
| cpu\_percent |プールの限度に対する割合で表した平均コンピューティング使用率。 |
| physical\_data\_read\_percent |プールの限度に対する割合で表した平均 I/O 使用率。 |
| log\_write\_percent |プールの限度に対する割合で表した平均書き込みリソース使用率。 |
| DTU\_consumption\_percent |プールの eDTU 限度に対する割合で表した平均 eDTU 使用率。 |
| storage\_percent |プールのストレージ限度に対する割合で表した平均ストレージ使用率。 |
| workers\_percent |プールの限度に対する割合で表した最大同時実行ワーカー (要求) 数。 |
| sessions\_percent |プールの限度に対する割合で表した最大同時実行セッション数。 |
| eDTU_limit |この期間中のこのエラスティック プールに対する現在の最大エラスティック プール DTU 設定。 |
| storage\_limit |この期間中のこのエラスティック プールに対する現在の最大エラスティック プール ストレージ制限 (メガバイト単位)。 |
| eDTU\_used |この期間にプールによって使用された平均 eDTU 数。 |
| storage\_used |この期間にプールによって使用された平均ストレージ (バイト単位)。 |

**メトリックの単位と保有期間を以下に示します。**

* データは 5 分間隔で返されます。  
* データは 35 日間保持されます。  

このコマンドレットと API で 1 回の呼び出しで取得できる行数が 1,000 行 (5 分間隔で 3 日分のデータ) に制限されます。 ただし、このコマンドは何度も呼び出すことができ、より多くのデータを取得するためにさまざまな開始から終了までの間隔で指定できます。

メトリックを取得するには:

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")  

## <a name="get-resource-usage-data-for-a-database-in-an-elastic-pool"></a>エラスティック プール内のデータベースのリソース使用状況データの取得
これらの API は、セマンティック上の相違が&1; 点あることを除いて、単一のデータベースのリソース使用率の監視に使用する現行の (V12) API と同じです。この API の場合、取得されたメトリックがプールに設定されている最大 eDTU 数 (または CPU、IO など、基盤となるメトリックに関してこれと同等の役割を果たす上限値) に対するパーセンテージで表示されます。 たとえば、これらのメトリックのうちのいずれかの使用率が 50% であることは、特定のリソースの消費量が、親となるプールでそのリソースに対して設けられているデータベースの上限の 50% であることを示しています。

メトリックを取得するには:

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")

## <a name="add-an-alert-to-an-elastic-pool-resource"></a>エラスティック プール リソースへのアラートの追加
エラスティック プールにアラート ルールを追加して、設定した使用率のしきい値にエラスティック プールが達したときに、[URL エンドポイント](https://msdn.microsoft.com/library/mt718036.aspx)への電子メール通知やアラート文字列の送信を行うことができます。 Add-AzureRmMetricAlertRule コマンドレットを使用します。

> [!IMPORTANT]
> エラスティック プールのリソース使用率の監視には、20 分以上の遅れがあります。 エラスティック プールに対して 30 分未満のアラートを設定することは現在サポートされていません。 30 分未満の期間 (PowerShell API の "-WindowSize" パラメーター) を含むエラスティック プールのアラート設定は、トリガーされない場合があります。 エラスティック プールに対して定義するアラートでは、30 分以上の期間 (WindowSize) を使用してください。
>
>

この例では、エラスティック プールの eDTU の消費量が一定のしきい値を上回った場合に通知を受け取るようにアラートを追加します。

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location =  '<location'                         # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name

    #$Target Resource ID
    $ResourceID = '/subscriptions/' + $subscriptionId + '/resourceGroups/' +$resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticpools/' + $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # create a unique rule name
    $alertName = $poolName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $ResourceID -MetricName "DTU_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail

## <a name="add-alerts-to-all-databases-in-an-elastic-pool"></a>エラスティック プール内のすべてのデータベースへのアラートの追加
エラスティック プール内のすべてのデータベースにアラート ルールを追加して、リソースがアラートで設定された使用率のしきい値に達したときに電子メール通知やアラート文字列を [URL エンドポイント](https://msdn.microsoft.com/library/mt718036.aspx) に送信することができます。

> [!IMPORTANT]
> エラスティック プールのリソース使用率の監視には、20 分以上の遅れがあります。 エラスティック プールに対して 30 分未満のアラートを設定することは現在サポートされていません。 30 分未満の期間 (PowerShell API の "-WindowSize" パラメーター) を含むエラスティック プールのアラート設定は、トリガーされない場合があります。 エラスティック プールに対して定義するアラートでは、30 分以上の期間 (WindowSize) を使用してください。
>
>

この例では、データベースの DTU 消費が特定のしきい値を上回った場合に通知を受け取るように、エラスティック プール内の各データベースにアラートを追加します。

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location = '<location'                          # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name

    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # Get resource usage metrics for a database in an elastic pool for the specified time interval.
    foreach ($db in $dbList)
    {
    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName

    # create a unique rule name
    $alertName = $db.DatabaseName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName  -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $dbResourceId -MetricName "dtu_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail

    # drop the alert rule
    #Remove-AzureRmAlertRule -ResourceGroup $resourceGroupName -Name $alertName
    }

## <a name="collect-and-monitor-resource-usage-data-across-multiple-pools-in-a-subscription"></a>サブスクリプション内の複数のプールのリソース使用状況データを収集して監視する
サブスクリプションに多数のデータベースがある場合、各エラスティック プールを個別に監視するのは面倒です。 代わりに、SQL Database の PowerShell コマンドレットと T-SQL クエリを組み合わせて、複数のプールおよびそのデータベースからリソース使用状況データを収集し、リソースの使用状況を監視および分析できます。 そのような一連の PowerShell スクリプトの [サンプル実装](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) が、内容と方法についてのドキュメントと共に GitHub の SQL Server サンプル リポジトリにあります。

このサンプル実装を使用するには、次の手順に従います。

1. [スクリプトとドキュメント](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools)をダウンロードします。
2. 環境に合わせてスクリプトを変更します。 エラスティック プールがホストされている&1; つまたは複数のサーバーを指定します。
3. 収集されたメトリックを格納するテレメトリ データベースを指定します。
4. スクリプトをカスタマイズして、スクリプトの実行期間を指定します。

スクリプトが行うことの概要は次のとおりです。

* 指定された Azure サブスクリプション (または指定されたサーバーの一覧) のすべてのサーバーを列挙します。
* 各サーバーに対してバック グラウンド ジョブを実行します。 ジョブは一定の間隔で繰り返し実行し、サーバー内のすべてのプールのテレメトリ データを収集します。 その後、収集されたデータを指定されたテレメトリ データベースに読み込みます。
* 各プール内のデータベースの一覧を列挙し、データベース リソース使用状況データを収集します。 その後、収集されたデータをテレメトリ データベースに読み込みます。

テレメトリ データベースの収集されたメトリックを分析して、エラスティック プールとその中のデータベースの状態を監視できます。 スクリプトでは、指定された時間範囲のメトリックの集計を支援する定義済みのテーブル値関数 (TVF) もテレメトリ データベースにインストールされます。 たとえば、TVF の結果を使用すると、"指定した時間範囲で eDTU 使用率が高い上位 N 個のエラスティック プール" を表示できます。 必要に応じて、Excel や Power BI などの分析ツールを使用して、収集されたデータをクエリおよび分析します。

### <a name="example-retrieve-resource-consumption-metrics-for-an-elastic-pool-and-its-databases"></a>例: エラスティック プールとそのデータベースのリソース消費メトリックを取得する
この例では、特定のエラスティック プールとそのすべてのデータベースの消費メトリックを取得します。 収集されたデータは書式設定されて .csv 形式のファイルに書き込まれます。 ファイルは Excel で表示できます。

    $subscriptionId = '<Azure subscription id>'          # Azure subscription ID
    $resourceGroupName = '<resource group name>'             # Resource Group
    $serverName = <server name>                              # server name
    $poolName = <elastic pool name>                          # pool name

    # Login to Azure account and select the subscription.
    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    # Get resource usage metrics for an elastic pool for the specified time interval.
    $startTime = '4/27/2016 00:00:00'  # start time in UTC
    $endTime = '4/27/2016 01:00:00'    # end time in UTC

    # Construct the pool resource ID and retrive pool metrics at 5-minute granularity.
    $poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
    $poolMetrics = (Get-AzureRmMetric -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime)

    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName

    # Get resource usage metrics for a database in an elastic pool for the specified time interval.
    $dbMetrics = @()
    foreach ($db in $dbList)
    {
        $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName
        $dbMetrics = $dbMetrics + (Get-AzureRmMetric -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime)
    }

    #Optionally you can format the metrics and output as .csv file using the following script block.
    $command = {
    param($metricList, $outputFile)

    # Format metrics into a table.
    $table = @()
    foreach($metric in $metricList) {
      foreach($metricValue in $metric.MetricValues) {
        $sx = New-Object PSObject -Property @{
            Timestamp = $metricValue.Timestamp.ToString()
            MetricName = $metric.Name;
            Average = $metricValue.Average;
            ResourceID = $metric.ResourceId
          }
          $table = $table += $sx
      }
    }

    # Output the metrics into a .csv file.
    write-output $table | Export-csv -Path $outputFile -Append -NoTypeInformation
    }

    # Format and output pool metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $poolMetrics,c:\temp\poolmetrics.csv

    # Format and output database metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $dbMetrics,c:\temp\dbmetrics.csv

## <a name="latency-of-elastic-pool-operations"></a>エラスティック プール操作の待機時間
* 通常、データベースあたりの最小 eDTU またはデータベースあたりの最大 eDTU の変更は、5 分以内で完了します。
* プールあたりの eDTU の変更は、プール内のすべてのデータベースで使用される領域の合計に依存します。 変更の平均時間は、100 GB あたり 90 分以下です。 たとえば、プール内のすべてのデータベースで使用される領域の合計が 200 GB の場合、プールあたりの eDTU の変更にかかる想定待機時間は、3 時間以下になります。

これらの PowerShell コマンドレットについては、次のリファレンス ドキュメントを参照してください。

* [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582\(v=azure.300\).aspx)
* [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403\(v=azure.300\).aspx)
* [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589\(v=azure.300\).aspx)

Stop- コマンドレットは、一時停止ではなく取り消しを意味します。 アップグレードを再開する方法は、最初からやり直す方法以外にありません。 Stop- コマンドレットにより、該当するすべてのリソースがクリーンアップされ、解放されます。

## <a name="next-steps"></a>次のステップ
* [エラスティック ジョブを作成する](sql-database-elastic-jobs-overview.md) : エラスティック ジョブを使用すると、プール内にある任意の数のデータベースに対して T-SQL スクリプトを実行できます。
* エラスティック ツールを使用してスケールアウト、データの移動、クエリ、トランザクションの作成を行う方法については、「[Azure SQL Database によるスケールアウト](sql-database-elastic-scale-introduction.md)」を参照してください。


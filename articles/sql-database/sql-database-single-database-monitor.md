---
title: "Azure SQL Database におけるデータベース パフォーマンスの監視 | Microsoft Docs"
description: "Azure ツールと動的管理ビューを使用してデータベースを監視するためのオプションについて説明します。"
keywords: "データベースの監視, クラウドのデータベース パフォーマンス"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: a2e47475-c955-4a8d-a65c-cbef9a6d9b9f
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 01/10/2017
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: e11ed3275413b428523eef78a5a89b537f6a4afc
ms.contentlocale: ja-jp
ms.lasthandoff: 06/13/2017


---
# <a name="monitoring-database-performance-in-azure-sql-database"></a>Azure SQL Database におけるデータベース パフォーマンスの監視
Azure での SQL データベースのパフォーマンスの監視は、選択したデータベース パフォーマンスのレベルに対するリソース使用率を監視することから始めます。 監視することで、データベースに余分な容量があるかどうかや、リソースが上限に達したことで問題が発生しているかどうかを判断できます。また、データベースのパフォーマンス レベルと[サービス レベル](sql-database-service-tiers.md)を調整する必要があるかどうかを判断することもできます。 データベースの監視には、[Azure Portal](https://portal.azure.com) のグラフィカル ツールや SQL の[動的管理ビュー](https://msdn.microsoft.com/library/ms188754.aspx)を使用できます。

## <a name="monitor-databases-using-the-azure-portal"></a>Azure ポータルを使用したデータベースの監視
[Azure Portal](https://portal.azure.com/) では、データベースを選択して **[監視]** グラフをクリックすると、単一のデータベースの使用率を監視することができます。 これにより、**[メトリック]** ウィンドウが表示されます。**[グラフの編集]** ボタンをクリックすると、内容を編集できます。 次のメトリックを追加します。

* CPU の割合
* DTU の割合
* データ IO の割合
* データベース サイズの割合

これらのメトリックを追加すると、**[監視]** グラフでこれらを引き続き確認しながら、**[メトリック]** ウインドウにさらに詳細な情報を表示することができます。 4 つのメトリックはいずれも、データベースの **DTU** を基準とする平均使用率を示しています。 DTU の詳細については、 [サービス階層](sql-database-service-tiers.md) に関する記事を参照してください。

![データベース パフォーマンスのサービス階層の監視](./media/sql-database-service-tiers/sqldb_service_tier_monitoring.png)

また、パフォーマンス メトリックに対してアラートを構成することができます。 **[メトリック]** ウィンドウの **[アラートの追加]** ボタンをクリックします。 ウィザードの指示に従ってアラートを構成します。 メトリックが特定のしきい値を超えた場合、またはメトリックが特定のしきい値を下回った場合に警告するオプションがあります。

たとえば、データベースのワークロードが増加する見込みの場合、データベースのいずれかのパフォーマンス メトリックが 80% に達すると電子メールのアラートを送信するように構成できます。 このアラートは、1 つ上位のパフォーマンス レベルに切り替えるタイミングを示す早期の警告として使用できます。

下位のパフォーマンス レベルにダウングレードできるかどうかを判断するために、パフォーマンス メトリックを利用することもできます。 たとえば、Standard S2 データベースを使用していて、すべてのパフォーマンス メトリックは、どの時点でもデータベースの平均的な使用率が 10% を超えないとします。 この場合、データベースは Standard S1 で快適に動作します。 ただし、下位のパフォーマンス レベルへの移行を決定する前に、急上昇や変動するワークロードに注意してください。

## <a name="monitor-databases-using-dmvs"></a>DMV を使用したデータベースの監視
Portal で公開されているものと同じメトリックを、システム ビューからも利用できます。それは、サーバーの論理 **master** データベースの [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) と、ユーザー データベースの [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) です。 詳細度の低いデータをより長い期間で監視する必要がある場合は、**sys.resource_stats** を使用します。 詳細度の高いデータをより短い期間で監視する必要がある場合は、**sys.dm_db_resource_stats** を使用します。 詳細については、[Azure SQL Database のパフォーマンス ガイダンス](sql-database-single-database-monitor.md#monitor-resource-use)に関する記事を参照してください。

> [!NOTE]
> **sys.dm_db_resource_stats** は、提供終了になった Web および Business Edition データベースで使用された場合、空の結果セットを返します。
>
>

### <a name="monitor-resource-use"></a>リソース使用量の監視

[SQL Database Query Performance Insight](sql-database-query-performance.md) および[クエリ ストア](https://msdn.microsoft.com/library/dn817826.aspx)を使用して、リソースの使用量を監視できます。

また、次の 2 つのビューを使用して使用量を監視することもできます。

* [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
* [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

#### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
[sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) ビューは、すべての SQL データベースで使用できます。 **sys.dm_db_resource_stats** ビューには、サービス レベルに関連した最近のリソース使用率データが表示されます。 CPU、データ I/O、ログ書き込み、メモリの平均 (%) が 15 秒ごとに記録され、1 時間保持されます。

このビューにはリソース使用率が詳細に表示されるので、現状の分析やトラブルシューティングが目的の場合、最初に **sys.dm_db_resource_stats** を使用してください。 たとえば次のクエリは、現在のデータベースの過去 1 時間の平均リソース使用率と最大リソース使用率を表示します。

    SELECT  
        AVG(avg_cpu_percent) AS 'Average CPU use in percent',
        MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
        AVG(avg_data_io_percent) AS 'Average data I/O in percent',
        MAX(avg_data_io_percent) AS 'Maximum data I/O in percent',
        AVG(avg_log_write_percent) AS 'Average log write use in percent',
        MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
        AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
        MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
    FROM sys.dm_db_resource_stats;  

その他のクエリについては、[sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) の例を参照してください。

#### <a name="sysresourcestats"></a>sys.resource_stats
**master** データベースの [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) ビューには、特定のサービス レベルとパフォーマンス レベルで SQL データベースのパフォーマンスを監視するのに役立つ追加情報があります。 データは 5 分ごとに集められ、約 35 日間保持されます。 このビューは、過去に SQL データベースでリソースがどのように使用されたかを長期にわたり分析する際に役立ちます。

次のグラフは、Premium データベースの CPU リソース使用率を示しています (P2 パフォーマンス レベル、1 週間における毎時間の使用率)。 このグラフは月曜日から始まります。5 営業日が経過した後の週末ではアプリケーションの活動が大幅に減っていることがわかります。

![SQL database resource use](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

このデータから、このデータベースのピーク CPU 負荷は現在のところ、P2 パフォーマンス レベルに対して 50% をわずかに超える CPU 利用率になっていることがわかります (火曜日の昼)。 アプリケーションのリソース プロファイルにおいて CPU が支配的要因である場合、P2 がワークロードに常に対処できる最適なパフォーマンス レベルであると決定できます。 時間の経過と共にアプリケーションの規模が大きくなると予測される場合、アプリケーションがパフォーマンス レベルの制限に達しないように、リソース バッファーを余分に確保しておくことをお勧めします。 パフォーマンス レベルを上げると、要求を効果的に処理するための十分な能力がないデータベースで発生するおそれのある、ユーザーに見えるエラーを回避できます。これは特に待機時間が重要な環境に当てはまります。 たとえば、データベース呼び出しの結果に基づいて Web ページを描画するアプリケーションをサポートしているデータベースなどです。

アプリケーションの種類が異なれば、同じグラフの解釈が異なる場合があります。 たとえば、あるアプリケーションで給与データを毎日処理し、同じグラフを生成する場合、P1 パフォーマンス レベルでこの種の "一括ジョブ" モデルに問題なく対応できる可能性があります。 P1 パフォーマンス レベルの DTU は 100 で、P2 パフォーマンス レベルの DTU は 200 です。 P1 パフォーマンス レベルのパフォーマンスは P2 パフォーマンス レベルの半分となります。 このため、P2 における 50% の CPU 使用率は、P1 における 100% の CPU 使用率に相当します。 アプリケーションにタイムアウトがない場合、当日に完了するのであれば、ジョブに 2 時間かかっても 2.5 時間かかっても問題ないものと思われます。 このカテゴリのアプリケーションではおそらく、P1 パフォーマンス レベルを利用できます。 1 日の中にはリソース使用率が低くなる時間帯があるという事実を利用できます。このため、"大きなピーク" が 1 日のそのような時間帯のいずれかに波及することがあります。 ジョブを毎日定刻に完了できる限り、P1 パフォーマンス レベルがこの種のアプリケーションに適している (コストが削減される) 場合があります。

Azure SQL Database では、各サーバーの **master** データベースの **sys.resource_stats** ビューにアクティブなデータベース別のリソース利用情報が公開されます。 テーブルのデータは 5 分おきに集計されます。 Basic、Standard、Premium のサービス レベルでは、データをテーブルに表示するのに 5 分を超える時間がかかる可能性があります。このため、このデータはほぼリアルタイムの分析よりも過去の分析に役に立ちます。 **sys.resource_stats** ビューに照会すると、データベースの最近の履歴が表示され、選択した予約で必要なときに望ましいパフォーマンスが発揮されたかどうかを検証できます。

> [!NOTE]
> 次の例で **sys.resource_stats** に照会するためには、論理 SQL データベース サーバーの **master** データベースに接続する必要があります。
> 
> 

次の例は、このビューのデータが表示されているところです。

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![The sys.resource_stats catalog view](./media/sql-database-performance-guidance/sys_resource_stats.png)

次の例では、**sys.resource_stats** カタログ ビューを使用して、SQL データベースでのリソースの使用状況に関する情報を取得するさまざまな方法を示します:

1. データベース userdb1 の過去 1 週間のリソース使用率を確認するには、このクエリを実行します。
   
        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;
2. ワークロードがパフォーマンス レベルにどの程度適合しているかを評価するには、リソース メトリック (CPU、読み取り、書き込み、ワーカー数、セッション数) の各点を分析する必要があります。 次に、**sys.resource_stats** を使用してこれらのリソース メトリックの平均値と最大値を報告するように修正したクエリを示します:
   
        SELECT
            avg(avg_cpu_percent) AS 'Average CPU use in percent',
            max(avg_cpu_percent) AS 'Maximum CPU use in percent',
            avg(avg_data_io_percent) AS 'Average physical data I/O use in percent',
            max(avg_data_io_percent) AS 'Maximum physical data I/O use in percent',
            avg(avg_log_write_percent) AS 'Average log write use in percent',
            max(avg_log_write_percent) AS 'Maximum log write use in percent',
            avg(max_session_percent) AS 'Average % of sessions',
            max(max_session_percent) AS 'Maximum % of sessions',
            avg(max_worker_percent) AS 'Average % of workers',
            max(max_worker_percent) AS 'Maximum % of workers'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
3. 各リソース メトリックの平均値と最大値に関するこの情報に基づいて、選択したパフォーマンス レベルにワークロードが適合しているかどうかを評価できます。 通常、**sys.resource_stats** からの平均値が目標サイズに対する有効な基準となります。 これを主要なものさしとしてください。 たとえば、S2 パフォーマンス レベルで Standard サービス レベルを使用しているとします。 CPU と I/O の読み取り/書き込みの平均使用率が 40% を下回り、ワーカーの平均数が 50 を下回り、セッションの平均数が 200 を下回っています。 このワークロードには、S1 パフォーマンス レベルが適している可能性があります。 データベースがワーカーとセッションの制限内に収まるかどうかは簡単にわかります。 CPU、読み取り、書き込みに関して、データベースが下位のパフォーマンス レベルに適合するかどうかを確認するには、下位パフォーマンス レベルの DTU 数を現在のパフォーマンス レベルの DTU 数で割り、その計算結果に 100 を掛けます。
   
    **S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40**
   
    この結果は、2 つのパフォーマンス レベルの間の相対的パフォーマンス差異を百分率で表したものになります。 リソースの使用がこの量を超えていない場合、ワークロードは下位のパフォーマンス レベルに適合する可能性があります。 ただし、リソース使用率の値を全範囲で見て、どのくらいの頻度でデータベースのワークロードが下位のパフォーマンス レベルに適合するかを割合の観点から判断する必要があります。 次のクエリは、この例で計算した 40% のしきい値に基づき、リソース ディメンション別の適合率を出力します。
   
        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    データベースのサービス レベル目標 (SLO) に基づき、ワークロードが下位のパフォーマンス レベルに適合するかどうかを判断できます。 データベース ワークロード SLO が 99.9% で、上記のクエリが 3 つすべてのリソース ディメンションに対して 99.9% を超える値を返す場合、そのワークロードはおそらく下位のパフォーマンス レベルに適合します。
   
    適合率を見ると、SLO を満たすために上位のパフォーマンス レベルに移行する必要があるかどうかもわかります。 たとえば、userdb1 では過去 1 週間の CPU 使用率が次のようになっています。
   
   | 平均 CPU 使用率 (%) | 最大 CPU 使用率 (%) |
   | --- | --- |
   | 24.5 |100.00 |
   
    平均 CPU は、パフォーマンス レベルの制限の約 4 分の 1 であり、データベースのパフォーマンス レベルにうまく適合するでしょう。 ただし最大値は、データベースがパフォーマンス レベルの制限に到達することを示しています。 次に上位のパフォーマンス レベルに移動する必要がありますか。 ワークロードが 100% に到達する回数を確認し、それをデータベースのワークロード SLO と比較します。
   
        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data I/O fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    このクエリが 3 つのリソース ディメンションのいずれにも 99.9% 未満の値を返す場合、次の上位のパフォーマンス レベルに移行すること、またはアプリケーションの調整手法を使用して SQL データベースの負荷を減らすことを検討してください。
4. この演習では、予測される将来のワークロードの増加も考慮しています。

エラスティック プールでは、このセクションで説明した手法を使用して、プール内の個々のデータベースを監視できます。 ただし、プールを全体として監視することもできます。 詳細については、[エラスティック プールの監視と管理](sql-database-elastic-pool-manage-portal.md)に関する記事を参照してください。


### <a name="maximum-concurrent-requests"></a>同時要求の最大数
同時要求の数を確認するには、SQL データベースで次の Transact-SQL クエリを実行します。

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

オンプレミス SQL Server データベースのワークロードを分析するには、分析したい特定のデータベースでフィルター処理するようにこのクエリを変更します。 たとえば、MyDatabase という名前のオンプレミス データベースがある場合、次の Transact-SQL クエリはそのデータベースの同時要求の数を返します。

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

これはある時点のスナップショットにすぎません。 ワークロードと同時要求の要件をさらに詳しく理解するには、時間をかけて多くのサンプルを収集する必要があります。

### <a name="maximum-concurrent-logins"></a>同時ログインの最大数
ユーザーやアプリケーションのパターンを分析すれば、ログインの頻度を理解できます。 さらに、テスト環境で実際の負荷を実行し、この上限やこの記事で説明されている他の制限に達していないことを確認できます。 同時ログイン数または履歴を表示できる単一のクエリや動的管理ビュー (DMV) はありません。

複数のクライアントで同じ接続文字列を使用している場合、サービスによってそれぞれのログインが認証されます。 10 人のユーザーが同じユーザー名とパスワードを使ってデータベースに同時に接続した場合、10 件の同時ログインが発生します。 この制限は、ログインと認証の期間のみに適用されます。 同じ 10 人のユーザーがデータベースに順番に接続した場合、同時ログイン数が 1 より大きくなることはありません。

> [!NOTE]
> この制限は現在のところ、エラスティック プールのデータベースには適用されません。
> 
> 

### <a name="maximum-sessions"></a>セッションの最大数
現在アクティブなセッションの数を確認するには、SQL データベースで次の Transact-SQL クエリを実行します。

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

オンプレミス SQL Server のワークロードを分析する場合は、特定のデータベースが対象になるようにクエリを変更してください。 このクエリは、Azure SQL Database への移行を検討している場合に、データベースの潜在的なセッション ニーズを判断するのに役立ちます。

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

ここでも、これらのクエリはある時点の数を返します。 時間をかけて複数のサンプルを集めると、セッションの使用状況を正確に把握できます。

SQL Database 分析の場合、[sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) ビューにクエリを実行し、**active_session_count** 列を確認して、セッションの過去の統計値を取得できます。 


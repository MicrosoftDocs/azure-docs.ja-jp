---
title: "データベースが 1 つの場合の Azure SQL Database のパフォーマンス | Microsoft Docs"
description: "この記事は、アプリケーションに適したサービス レベルを判断する際に役に立ちます。 Azure SQL Database を最大限活用できるようにアプリケーションを調整する方法についても紹介しています。"
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: dd8d95fa-24b2-4233-b3f1-8e8952a7a22b
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 03d38dbce86711395a967cf8bad440fd50a38631
ms.lasthandoff: 03/28/2017


---
# <a name="azure-sql-database-and-performance-for-single-databases"></a>データベースが 1 つの場合の Azure SQL Database とパフォーマンス
Azure SQL Database には、Basic、Standard、Premium、Premium RS の 4 つの[サービス レベル](sql-database-service-tiers.md)が用意されています。 各サービス レベルでは、SQL データベースで使用できるリソースが厳密に分離されており、そのサービス レベルの予測可能なパフォーマンスが保証されています。 この記事では、アプリケーションに適したサービス レベルを選択する際に役立つガイダンスを提供します。 さらに、Azure SQL Database を最大限活用できるようにアプリケーションを調整する方法について説明します。

> [!NOTE]
> この記事では、Azure SQL Database のデータベースが 1 つの場合のパフォーマンス ガイダンスについて説明しています。 エラスティック プールに関連するパフォーマンス ガイダンスについては、[エラスティック プールの価格とパフォーマンスに関する考慮事項](sql-database-elastic-pool-guidance.md)に関するトピックを参照してください。 ただし、この記事に記載されている調整の推奨事項の多くは、エラスティック プールのデータベースにも当てはまり、パフォーマンスに関して同様のメリットが得られることに注意してください。
>
>

## <a name="why-service-tiers"></a>サービス レベルを使用する理由
それぞれのデータベース ワークロードが変化する中でサービス レベルを使用する目的は、各種のパフォーマンス レベルでパフォーマンス予測可能性を提供することにあります。 データベース リソース要件の規模が大きなユーザーは、より専用度の高いコンピューティング環境で作業できます。

### <a name="common-service-tier-use-cases"></a>サービス レベルの一般的なユース ケース
#### <a name="basic"></a>Basic
* **Azure SQL Database の使用を始めたばかりである**。 多くの場合、開発中のアプリケーションは高いパフォーマンス レベルを必要としません。 Basic データベースは、低価格という点でデータベース開発に最適な環境です。
* **データベースのユーザーが 1 人である**。 1 人のユーザーがデータベースに関連付けられるアプリケーションでは通常、同時性とパフォーマンスの要件が高くありません。 これらのアプリケーションの場合、Basic サービス レベルが候補となります。

#### <a name="standard"></a>Standard
* **データベースに複数の同時要求がある**。 複数のユーザーに同時にサービスを提供するアプリケーションは通常、より高いパフォーマンス レベルを必要とします。 たとえば、トラフィック量が中程度の Web サイトや大量のリソースを必要とする部門用アプリケーションには、Standard サービス レベルが候補として適しています。

#### <a name="premium"></a>Premium
Premium サービス レベルのほとんどのユース ケースには、次の特性が 1 つ以上含まれています。

* **高いピーク負荷**。 操作の完了に多くの CPU、メモリ、または入力/出力 (I/O) を必要とするアプリケーションでは、専用の高いパフォーマンス レベルが必要になります。 たとえば、長時間にわたって複数の CPU コアが使用されることがわかっているデータベース操作では、Premium サービス レベルが候補となります。
* **多数の同時要求**。 トラフィック量が多い Web サイトにサービスを提供するなど、一部のデータベース アプリケーションは多くの同時要求にサービスを提供します。 Basic サービス レベルと Standard サービス レベルの場合、データベースごとに同時要求の数に制限があります。 より多くの接続を必要とするアプリケーションでは、必要な要求の最大数を処理できるように、適切な予約サイズを選択する必要があります。
* **低待機時間**。 一部のアプリケーションでは、データベースからの応答時間を最小限にする必要があります。 広範なユーザー操作の一環として特定のストアド プロシージャが呼び出されるとき、99% の割合で 20 ミリ秒以内にその呼び出しから応答を得ることが要求される場合があります。 この種類のアプリケーションでは、Premium サービス レベルを活用することで必要なコンピューティング パワーを確実に得られます。

* **Premium RS**。 I/O 集中型のワークロードを持つが、最高レベルの可用性の保証を必要としないお客様向けに設計されています。 例としては、高パフォーマンス ワークロードまたはデータベースがレコードのシステムではない分析ワークロードのテストが含まれます。

SQL データベースに必要なサービス レベルは、リソース ディメンションごとのピーク負荷要件に基づきます。 アプリケーションによっては、ある 1 つのリソースはほとんど使用せず、他のリソースは大量に使用するものがあります。

## <a name="service-tier-capabilities-and-limits"></a>サービス層の機能と制限
各サービス層とパフォーマンス レベルは、さまざまな制限やパフォーマンス特性に関連付けられています。 次の表は、単一データベースのこれらの特性についてまとめたものです。

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!IMPORTANT]
> P11 と P15 のパフォーマンス レベルを使用しているお客様は、追加料金なしで最大 4 TB の付属のストレージを使用できます。 この 4 TB のオプションは現在、米国東部 2、米国西部、西ヨーロッパ、東南アジア、東日本、オーストラリア東部、カナダ中部、カナダ東部の各リージョンで、パプリック プレビューの段階にあります。
>

### <a name="maximum-in-memory-oltp-storage"></a>インメモリ OLTP ストレージの最大サイズ
**sys.dm_db_resource_stats** ビューを使い、Azure インメモリ ストレージの使用状況を監視できます。 監視の詳細については、「 [インメモリ OLTP ストレージの監視](sql-database-in-memory-oltp-monitoring.md)」を参照してください。

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

## <a name="monitor-resource-use"></a>リソース使用量の監視

[SQL Database Query Performance Insight](sql-database-query-performance.md) および[クエリ ストア](https://msdn.microsoft.com/library/dn817826.aspx)を使用して、リソースの使用量を監視できます。

また、次の 2 つのビューを使用して使用量を監視することもできます。

* [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
* [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
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

### <a name="sysresourcestats"></a>sys.resource_stats
**master** データベースの [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) ビューには、特定のサービス レベルとパフォーマンス レベルで SQL データベースのパフォーマンスを監視するのに役立つ追加情報があります。 データは 5 分ごとに集められ、約 14 日間保持されます。 このビューは、過去に SQL データベースでリソースがどのように使用されたかを長期にわたり分析する際に役立ちます。

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

## <a name="tune-your-application"></a>アプリケーションの調整
従来のオンプレミス SQL Server では多くの場合、初回の容量計画のプロセスは、運用環境でアプリケーションを実行するプロセスから分離されます。 最初にハードウェアと製品ライセンスが購入され、パフォーマンス調整は後で行われます。 Azure SQL Database を使用する場合、アプリケーションの実行と調整のプロセスを組み合わせることをお勧めします。 オンデマンド容量の支払いモデルでは、現在必要とされる最小のリソースを使用するようにアプリケーションを調整できます。(不正確なことが多い) アプリケーションの将来的な成長計画の推測に基づいて、ハードウェアに過剰プロビジョニングを行うことはしません。 アプリケーションを調整しないでハードウェア リソースを過剰にプロビジョニングすることを選ぶユーザーもいます。 この方法は、利用が集中する期間に重要なアプリケーションの変更を望まない場合に適していることがあります。 しかし、アプリケーションを調整することで、リソース要件を最小限に抑え、Azure SQL Database のサービス レベルを使用する際に毎月の請求額を抑えることができます。

### <a name="application-characteristics"></a>アプリケーションの特性
Azure SQL Database のサービス レベルは、アプリケーションのパフォーマンスの安定性と予測可能性を高めるように設計されています。一方で、いくつかのベスト プラクティスを実践することで、パフォーマンス レベル内でリソースを最大限に活用するようアプリケーションを調整できます。 多くのアプリケーションは、上位のパフォーマンス レベルまたはサービス レベルに切り替えることでパフォーマンスが大幅に向上します。とはいえ、アプリケーションによっては、上位のサービス レベルの利点を活かすためにさらに調整が必要になります。 次のような特性を備えたアプリケーションでは、パフォーマンスを向上させるためにアプリケーションに調整を加えることを検討してください。

* **"煩雑な" 動作が原因でパフォーマンスの低いアプリケーション**。 煩雑なアプリケーションでは、ネットワークの待機時間が重要なデータ アクセス操作が過度に発生します。 この種のアプリケーションでは、SQL データベースに対するデータ アクセス操作の数を減らすよう変更を施す必要があります。 たとえば、アドホック クエリを一括処理したり、ストアド プロシージャにクエリを移動したりするなどの手法を使って、アプリケーションのパフォーマンスを向上させることができます。 詳細については、「 [バッチ クエリ](#batch-queries)」を参照してください。
* **単一のマシンではサポートし切れないほどワークロードが集中するデータベース**。 最高の Premium パフォーマンス レベルのリソースを超えるデータベースでは、ワークロードのスケールアウトを活用できる場合があります。 詳細については、「[データベース間のシャーディング](#cross-database-sharding)」と「[機能的パーティション分割](#functional-partitioning)」を参照してください。
* **最適でないクエリを含むアプリケーション**。 クエリが十分に調整されていない (特にデータ アクセス層の) アプリケーションの場合、上位のパフォーマンス レベルの利点を活かせないことがあります。 たとえば、WHERE 句がない、インデックスが足りない、統計が古いクエリです。 これらのアプリケーションの場合、クエリ パフォーマンスの標準的な調整方法で効果が得られます。 詳細については、「[インデックスの不足](#missing-indexes)」と「[クエリの調整とヒント](#query-tuning-and-hinting)」を参照してください。
* **データ アクセス設計が最適ではないアプリケーション**。 デッドロックなど、データ アクセスの同時性問題が内在するアプリケーションの場合、上位のパフォーマンス レベルの利点を活かせないことがあります。 Azure キャッシュ サービスや他のキャッシング技術を利用し、クライアント側でデータをキャッシュすることで、Azure SQL Database に対するラウンド トリップを減らすことを検討してください。 詳しくは、「 [アプリケーション層のキャッシュ](#application-tier-caching)」を参照してください。

## <a name="tuning-techniques"></a>調整技法
このセクションでは、Azure SQL Database を調整するいくつかの手法について説明します。これらの手法を使用すると、アプリケーションから最良のパフォーマンスを引き出し、可能な限り下位のパフォーマンス レベルでアプリケーションを実行することができます。 これらの手法の一部は従来の SQL Server 調整のベスト プラクティスと同じですが、その他のものは Azure SQL Database に固有です。 場合によっては、データベースで使用されるリソースを調べ、さらに調整すべき領域を見つけることができるほか、従来の SQL Server 手法を拡大し、Azure SQL Database に応用することができます。

### <a name="azure-portal-tools"></a>Azure ポータル ツール
Azure Portal の次のツールを使用すると、SQL データベースのパフォーマンスの問題を分析して修正する際に役立ちます。

* [Query Performance Insight](sql-database-query-performance.md)
* [SQL Database Advisor](sql-database-advisor.md)

これらの両方のツールに関する詳細情報とその使用方法は、Azure Portal にあります。 問題を効率的に診断して解決するには、まず Azure Portal のツールを試してみることをお勧めします。 (インデックスの不足とクエリの調整について) 次に説明する手動の調整手法は、特殊なケースで使用することをお勧めします。

### <a name="missing-indexes"></a>インデックスの不足
OLTP データベースのパフォーマンスの一般的問題は物理的なデータベース設計に関連します。 多くの場合、データベース スキーマは (負荷またはデータ量の) 規模の面で試験することなく設計され、出荷されます。 残念ながら、クエリ プランのパフォーマンスは、規模が小さい場合には許容されることがあるものの、実稼働レベルのデータ量では大幅に低下する可能性があります。 この問題の最も一般的な原因は、適切なインデックスがなく、クエリのフィルターまたはその他の制約を満たせないことにあります。 多くの場合、インデックスがないと、インデックス シークで足りるときにテーブル スキャンが行われます。

次の例では、シークで足りるときに、選択したクエリ プランでスキャンが使用されます。

    DROP TABLE dbo.missingindex;
    CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
    SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;

![A query plan with missing indexes](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Azure SQL Database は、インデックス不足の一般的な状態を発見して修正するのに役立ちます。 Azure SQL Database に組み込まれている DMV には、クエリ コンパイルが表示されます。クエリを実行するために見積もられたコストをインデックスで大幅に削減できる場合があります。 クエリの実行中、SQL Database によって、各クエリ プランが実行される頻度と、実行クエリ プランとそのインデックスが存在した想定クエリ プランの間で見積もられるギャップが追跡されます。 これらの DMV を使用し、データベースとその実際のワークロードに関してワークロード コストを全体的に改善できる物理データベース設計の変更をすばやく推測できます。

次のクエリは、潜在的なインデックス不足の評価に使用できます。

    SELECT CONVERT (varchar, getdate(), 126) AS runtime,
        mig.index_group_handle, mid.index_handle,
        CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
                (migs.user_seeks + migs.user_scans)) AS improvement_measure,
        'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
                  CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
                  (' + ISNULL (mid.equality_columns,'')
                  + CASE WHEN mid.equality_columns IS NOT NULL
                              AND mid.inequality_columns IS NOT NULL
                         THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
                  + ')'
                  + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement,
        migs.*,
        mid.database_id,
        mid.[object_id]
    FROM sys.dm_db_missing_index_groups AS mig
    INNER JOIN sys.dm_db_missing_index_group_stats AS migs
        ON migs.group_handle = mig.index_group_handle
    INNER JOIN sys.dm_db_missing_index_details AS mid
        ON mig.index_handle = mid.index_handle
    ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

この例では、クエリの結果として次が推奨されました。

    CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

作成後、同じ SELECT ステートメントを実行すると、異なるプランが選択されます。スキャンではなくシークが使用され、プランがより効率的に実行されます。

![A query plan with corrected indexes](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

重要なことは、共有される汎用システムの I/O 容量は専用サーバー コンピューターの I/O 容量より限られているということです。 不必要な I/O を最小限に抑え、Azure SQL Database サービス レベルの各パフォーマンス レベルの DTU 内でシステムを最大限に活用することが重要です。 適切な物理データベース設計を選択すると、個々のクエリの待機時間のほか、スケール ユニットごとに処理される同時要求のスループットを大幅に改善し、クエリを満たすために必要なコストを最小限に抑えることができます。 インデックス不足の DMV に関する詳細については、「[sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx)」を参照してください。

### <a name="query-tuning-and-hinting"></a>クエリの調整とヒント
Azure SQL Database のクエリ オプティマイザーは、従来の SQL Server クエリ オプティマイザーと似ています。 クエリを調整し、クエリ オプティマイザーの推論モデル制約を理解するためのベスト プラクティスのほとんどは、Azure SQL Database にも活かすことができます。 Azure SQL Database のクエリを調整すると、総リソース要求を減らせる場合があります。 下位のパフォーマンス レベルで実行できるため、クエリが調整されていない場合に比べて少ないコストでアプリケーションを実行できます。

SQL Server でよく見られ Azure SQL Database にも適用される例は、クエリ オプティマイザーによるパラメーターの "スニッフィング" です。 コンパイル中、クエリ オプティマイザーによってパラメーターの現在の値が評価され、より最適なクエリ プランを生成できるかどうかが判断されます。 この戦略を使用すると多くの場合、既知のパラメーター値を使用せずにコンパイルされたプランよりもはるかに速いクエリ プランが生成されます。ただし現時点では、SQL Server と Azure SQL Database の両方で動作が不完全です。 パラメーターがスニッフィングされなかったり、パラメーターがスニッフィングされたものの、生成されたプランがワークロードのすべてのパラメーター値に関して最適でなかったりする場合があります。 意図をより慎重に指定し、パラメーター スニッフィングの既定の動作を上書きできるように、Microsoft はクエリ ヒント (ディレクティブ) を追加しています。 多くの場合、ヒントを使用すると、SQL Server または Azure SQL Database の既定の動作で特定のユーザーのワークロードに完全に対応できない問題を修正できます。

次の例は、パフォーマンスとリソースの両方の要件について最適でないプランがクエリ プロセッサによって生成されるようすを示しています。 この例から、クエリ ヒントを使用すると、SQL データベースのクエリの実行時間とリソース要件を抑えることができることもわかります。

    DROP TABLE psptest1;
    CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));

    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO psptest1(col2) values (1);
        INSERT INTO psptest1(col2) values (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION
    CREATE INDEX i1 on psptest1(col2);
    GO

    CREATE PROCEDURE psp1 (@param1 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1
        WHERE col2 = @param1
        ORDER BY col2;
    END
    GO

    CREATE PROCEDURE psp2 (@param2 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
        ORDER BY col2
        OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
    END
    GO

    CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
    GO

このセットアップ コードによって、傾斜データ分布が含まれたテーブルが作成されます。 最適なクエリ プランは、選択されたパラメーターによって異なります。 残念ながらプラン キャッシング動作では、常に最も一般的なパラメーター値に基づいてクエリが再コンパイルされるとは限りません。 そのため、平均すると別のプランの方がプランとしてより良い選択になる場合でも、最適でないプランがキャッシュされ、多くの値に使用される可能性があります。 次に、(一方に特殊なクエリ ヒントが含まれていることを除いて) 同一の 2 つのストアド プロシージャがクエリ プランによって作成されます。

**例 (パート 1)**

    -- Prime Procedure Cache with scan plan
    EXEC psp1 @param1=1;
    TRUNCATE TABLE t1;

    -- Iterate multiple times to show the performance difference
    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp1 @param1=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

**例 (パート 2)**

(例のパート 2 を開始する前に少なくとも 10 分待つことをお勧めします。これにより、生成されるテレメトリ データの結果の差異がはっきりします)。

    EXEC psp2 @param2=1;
    TRUNCATE TABLE t1;

    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

この例の各パートでは、(テスト データ セットとして使用するのに十分な負荷を生成するために) パラメーター化された挿入ステートメントが 1,000 回試行されます。 ストアド プロシージャを実行するとき、クエリ プロセッサは、その最初のコンパイル中にプロシージャに渡されるパラメーター値を調べます (パラメーターの "スニッフィング")。 結果として生成されたプランがプロセッサによってキャッシュされ、パラメーター値が異なる場合でも、後の呼び出しで使用されます。 最適なプランが使用されないことがあります。 クエリが最初にコンパイルされたときのケースではなく、平均的なケースに対して最適なプランを選択するように、オプティマイザーを調整する必要がある場合があります。 この例では、最初のプランは、パラメーターに一致する各値を見つけるためにすべての行を読み取る "スキャン" プランを生成します。

![Query tuning by using a scan plan](./media/sql-database-performance-guidance/query_tuning_1.png)

値 1 を使用してプロシージャを実行したため、結果として生成されたプランは値 1 に対して最適ですが、テーブルにある他のすべての値に対しては最適ではありません。 各プランを無作為に選択した場合、結果は望んだものと異なることが予想されます。これは、プランの実行が遅く、より多くのリソースが使用されるためです。

`SET STATISTICS IO` を `ON` に設定してテストを実行すると、この例の論理スキャン作業がバックグラウンドで行われます。 このプランによって 1,148 件の読み取りが行われたことがわかります (平均的なケースで返される行がたった 1 つの場合、非効率的です)。

![Query tuning by using a logical scan](./media/sql-database-performance-guidance/query_tuning_2.png)

例の 2 つ目の部分では、クエリ ヒントを利用し、コンパイル プロセス中に特定の値を使用するようにオプティマイザーに伝えます。 この場合、パラメーターとして渡される値を無視し、`UNKNOWN` を想定するようにクエリ プロセッサに強制します。 これはテーブル内での頻度が平均的な値を示します (傾斜を無視)。 結果として生成されるプランはシークベースのプランです。このプランはこの例のパート 1 のプランより全体的に高速であり、このプランで使用されるリソースもパート 1 のプランより全体的に少なくなっています。

![Query tuning by using a query hint](./media/sql-database-performance-guidance/query_tuning_3.png)

**sys.resource_stats** テーブルで影響を確認できます (テストを実行してからデータがテーブルに入力されるまでの間に遅延があります)。 この例では、パート 1 は 22:25:00 の時間枠で実行され、パート 2 は 22:35:00 の時間枠で実行されています。 遅い方の時間枠と比べ、早い方の時間枠でその時間枠のリソースがより多く使用されます (プランの効率性改善が理由)。

    SELECT TOP 1000 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Query tuning example results](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> この例では規模を意図的に小さくしてありますが、最適でないパラメーターの影響は特に大規模なデータベースで顕著になります。 極端なケースでは、速い場合は数秒単位、遅い場合は数時間単位の差異になります。
>
>

**sys.resource_stats** を調べると、あるテストで使用されるリソースが別のテストより多いか少ないか判断できます。 データを比較するとき、**sys.resource_stats** ビューで同じ 5 分の枠に入らないようにテストのタイミングを離します。 この演習の目標は、使用されるリソースの総量を最小限に抑えることであり、ピーク リソースを最小限に抑えることではありません。 一般的に、待ち時間のコードの一部を最適化すると、リソースの消費量も減ります。 アプリケーションに施す変更が必要なものであることと、アプリケーションでクエリ ヒントを使用している他のユーザーのカスタマー エクスペリエンスに対し変更による悪影響がないことを確認してください。

ワークロードに一連の反復的なクエリが含まれる場合は、データベースをホストするために必要な最小リソース サイズ単位を把握できるため、たいてい、プラン選択肢の最適性を理解して検証することは合理的です。 検証した後、プランのパフォーマンスが低くなっていないことを確認するために、ときどきプランを調べ直してください。 詳細については、「 [クエリ ヒント (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx)」をご覧ください。

### <a name="cross-database-sharding"></a>データベース間のシャーディング
Azure SQL Database は汎用ハードウェアで実行されるため、従来のオンプレミス SQL Server インストールと比べ、単一データベースに対する容量制限が低くなります。 データベース操作が Azure SQL Database の単一データベースの制限内に収まらないときにシャーディング手法を使用して、複数のデータベースに操作を分散しているユーザーもいます。 Azure SQL Database でシャーディング手法を利用するほとんどのユーザーは、1 つのディメンションのデータを複数のデータベースで分割します。 この手法では、OLTP アプリケーションは多くの場合、スキーマ内の 1 行のみ、またはほんの数行から成るグループに適用されるトランザクションを実行することを理解しておく必要があります。

> [!NOTE]
> SQL Database にシャーディングを支援するライブラリが追加されました。 詳細については、「 [エラスティック データベース クライアント ライブラリの概要](sql-database-elastic-database-client-library.md)」をご覧ください。
>
>

たとえば、(SQL Server 付属の従来のサンプル Northwind データベースのように) あるデータベースに顧客名、注文、注文明細が含まれている場合、関連する注文と注文明細の情報を使って顧客をグループ化することで、このデータを複数のデータベースに分割できます。 顧客のデータは単一データベース内にとどめておくことができます。 アプリケーションはデータベース間で顧客を分割し、効果的に負荷を分散します。 シャーディングを使用すると、顧客がデータベース サイズの上限を回避できるだけでなく、個々のデータベースがその DTU に収まる限り、各パフォーマンス レベルの制限を大幅に超えるワークロードを Azure SQL Database で処理できます。

データベース シャーディングではソリューションの総リソース容量を減らすことはできませんが、複数のデータベースにまたがる非常に大規模なソリューションに対応する際に非常に効果的です。 各データベースを異なるパフォーマンス レベルで実行し、リソース要件の高い、非常に大規模で "効果的な" データベースに対応できます。

### <a name="functional-partitioning"></a>機能的パーティション分割
SQL Server ユーザーは多くの場合、1 つのデータベースのさまざまな機能を組み合わせます。 たとえば、店舗の在庫を管理するロジックがアプリケーションに含まれている場合、そのデータベースには、在庫に関連付けられているロジック、購買発注の追跡、ストアド プロシージャ、月末報告を管理するインデックス付きビュー/具体化されたビューが含まれていることがあります。 この手法では、バックアップなどの操作に関するデータベースの管理が容易になりますが、アプリケーションの機能全体でピーク負荷を処理できるようにハードウェアのサイズを調整する必要もあります。

Azure SQL Database 内でスケールアウト アーキテクチャを使用する場合、アプリケーションの異なる機能を異なるデータベースに分割することをお勧めします。 この手法を使用すると、各アプリケーションは独立してスケールされます。 管理者は、アプリケーションがビジー状態になった (データベースの負荷が増えた) ときに、アプリケーションの機能ごとにパフォーマンス レベルを個別に選択できます。 制限はありますが、このアーキテクチャを使用して、1 台の汎用コンピューターで処理できる範囲を超えてアプリケーションの規模を大きくできます。これは、複数のコンピューター間で負荷が分散されるためです。

### <a name="batch-queries"></a>バッチ クエリ
大量のアドホック クエリを頻繁に実行してデータにアクセスするアプリケーションの場合、アプリケーション層と Azure SQL Database 層の間で行われるネットワーク通信の応答に、相当な時間が費やされます。 アプリケーションと Azure SQL Database が両方同じデータ センターに存在する場合でも、データ アクセス操作の数が多ければ、この 2 つの間のネットワーク待機時間は長くなる可能性があります。 データ アクセス操作のネットワーク ラウンド トリップを減らすために、アドホック クエリを一括処理すること、またはストアド プロシージャとしてそれらをコンパイルすることを検討してください。 アドホック クエリを一括処理すると、複数のクエリを 1 つの大きなバッチとして 1 回のトリップで Azure SQL Database に送信できます。 アドホック クエリをストアド プロシージャにコンパイルすると、それらを一括処理した場合と同じ結果が得られます。 ストアド プロシージャを使用すると、クエリ プランが Azure SQL Database にキャッシュされる機会が増えるという利点もあるため、ストアド プロシージャを再度使用できます。

一部のアプリケーションでは、書き込みが集中します。 場合によっては、書き込みを一括処理する方法を検討することで、データベースの I/O 総負荷を減らすことができます。 これは多くの場合、ストアド プロシージャとアドホック バッチ内で自動コミット トランザクションではなく明示的なトランザクションを使用するのと同じくらい単純です。 使用できるさまざまな手法の評価については、 [Azure での SQL Database アプリケーションのバッチ処理手法](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx)に関する記事を参照してください。 独自のワークロードで実験を行って、一括処理に適したモデルを見つけてください。 モデルによってはトランザクションの整合性の保証がわずかに異なる場合があることを理解しておいてください。 リソース使用を最小限に抑える適切なワークロードを見つけるには、整合性とパフォーマンスの適度なバランスを見つける必要があります。

### <a name="application-tier-caching"></a>アプリケーション層のキャッシュ
一部のデータベース アプリケーションでは、ワークロードの大半が読み取りになります。 キャッシュ層を利用すれば、データベースの負荷を減らすことができます。また、Azure SQL Database を使用してデータベースをサポートするために必要なパフォーマンス レベルを下げられる可能性があります。 [Azure Redis Cache](https://azure.microsoft.com/services/cache/) を利用すると、読み取りが多いワークロードがある場合に、データを 1 回 (または、構成方法に応じてアプリケーション層コンピューターごとに 1 回) 読み込んでから、SQL データベースの外部にそのデータを格納することができます。 この方法は、データベースの負荷 (CPU と読み取り I/O) を減らすことができるものの、トランザクションの整合性に影響があります。データがキャッシュから読み込まれると、データベースのデータとの同期が失われることがあるためです。 多くのアプリケーションではある程度の不整合が許容されますが、すべてのワークロードで許容されるとは限りません。 アプリケーション層のキャッシュ手法を実装する前に、あらゆるアプリケーション要件を完全に理解しておく必要があります。

## <a name="next-steps"></a>次のステップ
* サービス レベルの詳細については、 [SQL Database のオプションとパフォーマンス](sql-database-service-tiers.md)
* エラスティック プールの詳細については、「[Azure エラスティック プールの概要](sql-database-elastic-pool.md)」を参照してください。
* パフォーマンスとエラスティック プールの詳細については、[エラスティック プールの使用を検討する場合](sql-database-elastic-pool-guidance.md)に関するページを参照してください。


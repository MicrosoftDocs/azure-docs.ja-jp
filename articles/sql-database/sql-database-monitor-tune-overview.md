---
title: 監視とパフォーマンスのチューニング - Azure SQL Database | Microsoft Docs
description: 評価と改善を通じて Azure SQL Database のパフォーマンスをチューニングするためのヒントを紹介します。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: ac87ce2198296b82ef5655d7d75443a0bd49df3c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57875140"
---
# <a name="monitoring-and-performance-tuning"></a>監視とパフォーマンスのチューニング

Azure SQL Database は、自動管理される柔軟なデータ サービスです。このサービスでは、使用状況の監視、リソース (CPU、メモリ、I/O) の追加または削除、ご使用のデータベースのパフォーマンスを向上させる推奨事項の検出、あるいはデータベースをご自分のワークロードに適応させパフォーマンスを自動的に最適化するなどを容易に行うことができます。

## <a name="monitoring-database-performance"></a>データベース パフォーマンスの監視

Azure での SQL データベースのパフォーマンスの監視は、選択したデータベース パフォーマンスのレベルに対するリソース使用率を監視することから始めます。 Azure SQL Database では、[パフォーマンス チューニングの推奨事項](sql-database-advisor.md)を確認することで、リソースを変更することなくクエリのパフォーマンスを改善および最適化する機会を見極めることができます。 インデックスの不足や、最適化が不完全なクエリは、データベース パフォーマンスが低下する一般的な原因です。 このようなチューニングの推奨事項を適用すると、ワークロードのパフォーマンスを向上させることができます。 識別されたすべての推奨事項を適用し、それによってデータベースのパフォーマンスが向上することを確認することで、Azure SQL Database で[クエリのパフォーマンスを自動的に最適化](sql-database-automatic-tuning.md)することもできます。

データベースのパフォーマンスの監視とトラブルシューティングには、次のオプションを使用できます。

- [Azure Portal](https://portal.azure.com) で、**[SQL データベース]** をクリックし、データベースを選択してから、監視グラフを使用して上限に近づいているリソースを見つけます。 DTU 消費は、既定で表示されます。 **[編集]** をクリックして、表示される時間の範囲と値を変更します。
- [Query Performance Insight](sql-database-query-performance.md) を使用して、最もリソースを消費しているクエリを特定します。
- [SQL Database Advisor](sql-database-advisor-portal.md) を使用して、インデックスの作成と削除、クエリのパラメーター化、およびスキーマの問題の修正に関する推奨事項を表示します。
- データベース パフォーマンスの自動監視には、[Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) を使用します。 パフォーマンスの問題が検出されたら、問題の詳細と根本原因分析 (RCA) が記載された診断ログが生成されます。 可能な場合は、パフォーマンス改善の推奨事項も提供されます。
- [自動チューニングを有効にします](sql-database-automatic-tuning-enable.md)。これにより、Azure SQL Database では、特定されたパフォーマンスの問題を自動的に修正できるようになります。
- [動的管理ビュー (DMV)](sql-database-monitoring-with-dmvs.md)、[拡張イベント](sql-database-xevent-db-diff-from-svr.md)、および[クエリ ストア](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)を使用して、パフォーマンスの問題のトラブルシューティングをより詳細に行います。

> [!TIP]
> 上の 1 つまたは複数の方法を使用してパフォーマンスの問題を識別した後、Azure SQL Database のパフォーマンスを向上させるために使用できる手法を見つけるには、[パフォーマンス ガイダンス](sql-database-performance-guidance.md)を参照してください。

## <a name="monitor-databases-using-the-azure-portal"></a>Azure ポータルを使用したデータベースの監視

[Azure portal](https://portal.azure.com/) で、ご使用のデータベースを選択し、**[監視]** グラフをクリックすることによって個々のデータベースの使用を監視できます。 これにより、**[メトリック]** ウィンドウが表示されます。**[グラフの編集]** ボタンをクリックすると、内容を編集できます。 次のメトリックを追加します。

- CPU の割合
- DTU の割合
- データ IO の割合
- データベース サイズの割合

これらのメトリックを追加したら、これらを **[監視]** グラフに引き続き表示し、**[メトリック]** ウィンドウにはさらに詳細な情報を表示できます。 4 つのメトリックはいずれも、データベースの **DTU** を基準とする平均使用率を示しています。 サービス レベルの詳細については、[DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)と[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)に関する記事を参照してください。  

![データベース パフォーマンスのサービス階層の監視](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

また、パフォーマンス メトリックに対してアラートを構成することができます。 **[メトリック]** ウィンドウの **[アラートの追加]** ボタンをクリックします。 ウィザードの指示に従ってアラートを構成します。 メトリックが特定のしきい値を超えた場合、またはメトリックが特定のしきい値を下回った場合に警告するオプションがあります。

たとえば、データベースのワークロードが増加する見込みの場合、データベースのいずれかのパフォーマンス メトリックが 80% に達すると電子メールのアラートを送信するように構成できます。 これは、次に大きいコンピューティング サイズにいつ切り替える必要があるかを見つけるための早期警告として使用できます。

下位のコンピューティング サイズにダウングレードできるかどうかを判断するために、パフォーマンス メトリックを利用することもできます。 たとえば、Standard S2 データベースを使用していて、すべてのパフォーマンス メトリックは、どの時点でもデータベースの平均的な使用率が 10% を超えないとします。 この場合、データベースは Standard S1 で快適に動作します。 ただし、下位のコンピューティング サイズへの移行を決定する前に、急上昇や変動するワークロードに注意してください。

## <a name="troubleshoot-performance-issues"></a>パフォーマンスに関する問題のトラブルシューティング

パフォーマンスの問題を診断および解決するには、まず各アクティブなクエリの状態、および各ワークロードの状態に関連するパフォーマンスの問題の原因となる条件を理解します。 Azure SQL Database のパフォーマンスを向上させるには、アプリケーションからの各アクティブなクエリ要求が実行状態または待機状態のどちらかにあることを理解します。 Azure SQL Database でパフォーマンスの問題をトラブルシューティングする場合は、この記事を参照してパフォーマンスの問題を診断および解決するときに常に次の図を念頭においてください。

![ワークロードの状態](./media/sql-database-monitor-tune-overview/workload-states.png)

パフォーマンスの問題があるワークロードの場合、そのパフォーマンスの問題は、CPU 競合 (**実行に関連する**条件) のためか、または個々のクエリが何かを待機している (**待機に関連する**条件) ためである可能性があります。

## <a name="running-related-performance-issues"></a>実行に関連するパフォーマンスの問題

一般的なガイドラインとして、CPU 使用率が常に 80% 以上である場合は、実行に関連するパフォーマンスの問題が発生しています。 実行に関連する問題が発生している場合は、不十分な CPU リソースによって発生しているか、または次のいずれかの条件に関連している可能性があります。

- 実行中のクエリが多すぎる
- コンパイル中のクエリが多すぎる
- 1 つ以上の実行クエリが最適でないクエリ プランを使用している

実行に関連するパフォーマンスの問題が発生していることを特定した場合、次の目標は 1 つまたは複数の方法を使用して正確な問題を識別することです。 実行に関連する問題を識別するための最も一般的な方法は次のとおりです。

- [Azure Portal](#monitor-databases-using-the-azure-portal) を使用して CPU 使用率 (%) を監視します。
- 次の[動的管理ビュー](sql-database-monitoring-with-dmvs.md)を使用します。

  - [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) は、Azure SQL Database データベースの CPU、I/O、およびメモリ使用量を返します。 データベースにアクティビティがない場合でも、15 秒ごとに 1 つの行が存在します。 履歴データは 1 時間保持されます。
  - [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) は、Azure SQL Database の CPU 使用率とストレージ データを返します。 データは 5 分間隔で収集され、集計されます。

> [!IMPORTANT]
> これらの DMV を使用して CPU 使用率の問題をトラブルシューティングするための一連の T-SQL クエリについては、[CPU パフォーマンスの問題の識別](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues)に関するページを参照してください。

### <a name="troubleshoot-queries-with-parameter-sensitive-query-execution-plan-issues"></a>パラメーター依存クエリ実行プランの問題があるクエリをトラブルシューティングする

パラメーター依存プラン (PSP) の問題とは、クエリ オプティマイザーが特定のパラメーター値 (または値のセット) に対してのみ最適なクエリ実行プランを生成し、キャッシュされたプランが連続実行で使用されるパラメーター値に対して最適ではないシナリオのことです。 その場合、最適ではないプランによりクエリ パフォーマンスの問題が発生し、ワークロード全体のスループットが低下する可能性があります。

問題を軽減するために使用される回避策が複数あり、それぞれに関連するトレードオフと欠点があります。

- 各クエリ実行で [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) クエリ ヒントを使用します。 この回避策では、プランの品質の向上と引き換えに、コンパイル時間が長くなって CPU 使用率が増加します。 高スループットを必要とするワークロードでは、`RECOMPILE` オプションを使用できないことがよくあります。
- [OPTION (OPTIMIZE FOR…)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) クエリ ヒントを使用して、実際のパラメーター値を、可能性のあるほとんどのパラメーター値に対して十分良好なプランを生成する一般的なパラメーター値でオーバーライドします。   このオプションを使用するには、最適なパラメーター値および関連するプランの特性をよく理解している必要があります。
- [OPTION (OPTIMIZE FOR UNKNOWN)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) クエリ ヒントを使用して、密度ベクトルの平均を使用する代わりに実際のパラメーター値をオーバーライドします。 これを行うもう 1 つの方法としては、受け取ったパラメーター値をローカル変数にキャプチャし、述語内ではパラメーター自体を使用する代わりにローカル変数を使用します。 平均密度は、この特定の修正で "*十分によい*" ものでなければなりません。
- [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) クエリ ヒントを使用して、パラメーター スニッフィング全体を無効にします。
- [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) クエリ ヒントを使用して、キャッシュを使用している間は再コンパイルが行われないようにします。 この回避策では、"*十分によい*" 一般的なプランがキャッシュ内に既にあることを前提としています。 よいプランが破棄されて新しい悪いプランがコンパイルされる可能性を減らすため、統計の自動更新を無効にすることもできます。
- [USE PLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) クエリ ヒントを明示的に使用して、プランを強制します (明示的に指定することで、クエリ ストアを使用して特定のプランを設定することで、または[自動チューニング](sql-database-automatic-tuning.md)を有効にすることで)。
- 1 つのプロシージャを、それぞれが条件付きロジックと関連するパラメーター値に基づいて使用できる入れ子になったプロシージャのセットに置き換えます。
- 静的なプロシージャ定義の代わりに、動的文字列の実行を作成します。

この種の問題の解決方法について詳しくは、以下をご覧ください。

- こちらの[パラメーターのにおい](https://blogs.msdn.microsoft.com/queryoptteam/20../../i-smell-a-parameter/)に関するブログ投稿
- こちらの[パラメーター スニッフィング問題と回避策](https://blogs.msdn.microsoft.com/turgays/20../../parameter-sniffing-problem-and-possible-workarounds/)に関するブログ投稿
- こちらの[象とマウスのパラメーター スニッフィング](https://www.brentozar.com/archive/2013/06/the-elephant-and-the-mouse-or-parameter-sniffing-in-sql-server/)に関するブログ投稿
- こちらの、[パラメーター化クエリに対する動的 SQL とプランの品質](https://blogs.msdn.microsoft.com/conor_cunningham_msft/20../../conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)に関するブログ投稿

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>不適切なパラメーター化が原因のコンパイル アクティビティのトラブルシューティング

クエリにリテラルがある場合は、データベース エンジンで自動的にステートメントのパラメーター化が選択されるか、またはユーザーが明示的にそれをパラメーター化してコンパイルの数を減らすことができます。 使用するパターンが同じでもリテラル値が異なるクエリのコンパイル回数が多いと、CPU の使用率が高くなる可能性があります。 同様に、ユーザーが継続的にリテラルを含むクエリを部分的にだけパラメーター化した場合、データベース エンジンではそれ以上パラメーター化されません。  部分的にパラメーター化されたクエリの例を次に示します。

```sql
select * from t1 join t2 on t1.c1=t2.c1
where t1.c1=@p1 and t2.c2='961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

前の例では、`t1.c1` は `@p1` を受け取りますが、`t2.c2` は引き続きリテラルとして GUID を受け取ります。 この場合、`c2` の値を変更すると、クエリは異なるクエリとして扱われて、新しいコンパイルが発生します。 前の例でコンパイルを減らすための解決策は、GUID もパラメーター化することです。

次のクエリでは、クエリ ハッシュ別にクエリの数を示して、クエリが適切にパラメーター化されているかどうかを判断します。

```sql
   SELECT  TOP 10  
      q.query_hash
      , count (distinct p.query_id ) AS number_of_distinct_query_ids
      , min(qt.query_sql_text) AS sampled_query_text
   FROM sys.query_store_query_text AS qt
      JOIN sys.query_store_query AS q
         ON qt.query_text_id = q.query_text_id
      JOIN sys.query_store_plan AS p 
         ON q.query_id = p.query_id
      JOIN sys.query_store_runtime_stats AS rs 
         ON rs.plan_id = p.plan_id
      JOIN sys.query_store_runtime_stats_interval AS rsi
         ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
   WHERE
      rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
      AND query_parameterization_type_desc IN ('User', 'None')
   GROUP BY q.query_hash
   ORDER BY count (distinct p.query_id) DESC
```

### <a name="resolve-problem-queries-or-provide-more-resources"></a>問題のあるクエリを解決するか、または提供するリソースを増やす

問題を識別したら、問題が発生しているクエリを調整するか、または CPU の要件を吸収するために Azure SQL Database の容量を増やすようにコンピューティング サイズまたはサービス レベルをアップグレードすることができます。 単一データベースのリソースのスケーリングについては、「[Azure SQL Database で単一データベースのリソースをスケーリングする](sql-database-single-database-scale.md)」を参照してください。また、エラスティック プールのリソースのスケーリングについては、「[Azure SQL Database でエラスティック プールのリソースをスケーリングする](sql-database-elastic-pool-scale.md)」を参照してください。 マネージド インスタンスのスケーリングについては、「[インスタンス レベルのリソース制限](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits)」を参照してください。

### <a name="determine-if-running-issues-due-to-increase-workload-volume"></a>ワークロードのボリュームの増加が原因である実行の問題かどうかを判断する

アプリケーションのトラフィックとワークロードの増加が CPU 使用率増加の原因である可能性がありますが、この問題を適切に診断するには注意が必要です。 CPU 使用率が高いシナリオでは、以下の質問に答えて、CPU 使用率の増加が本当にワークロードのボリュームの変更によるものかどうかを判断します。

1. アプリケーションからのクエリが、高 CPU の問題の原因ですか。
2. CPU 消費量上位の (識別可能な) クエリについて:

   - 同じクエリに複数の実行プランが関連付けられていたかどうかを確認します。 そうである場合は、その理由を判断します。
   - 同じ実行プランがあるクエリについて、実行時間が一定で、実行回数が増加したかどうかを確認します。 そうである場合は、ワークロードの増加がパフォーマンスの問題の原因である可能性があります。

まとめると、クエリ実行プランの実行方法は異ならなかったが、実行回数と共に CPU 使用率が増加した場合、ワークロードの増加に関連するパフォーマンスの問題が存在する可能性があります。

CPU の問題をもたらしているワークロード ボリュームの変更があるという結論を下すことは、簡単ではない場合があります。   検討するべき要素: 

- **リソースの使用状況が変化した**

  たとえば、長時間にわたり CPU 使用率が 80% に増加したようなシナリオを検討します。  ワークロードのボリュームの変化を意味するのは、CPU 使用率だけではありません。  クエリ実行プランの回帰や、データの分布の変化も、アプリケーションがまったく同じワークロードを実行しているのにリソースの使用量が増える原因になる可能性があります。

- **新しいクエリが発生した**

   アプリケーションでは、異なるときに新しいクエリのセットが発生する可能性があります。

- **要求の数が増加または減少した**

   このシナリオは、ワークロードの最も明白な尺度です。 クエリの数は、リソース使用率の増加に常に対応しているとは限りません。 それでもこのメトリックは、他の要因が変更されていないとすると、やはり重要なシグナルです。

## <a name="waiting-related-performance-issues"></a>待機に関連するパフォーマンスの問題

高い CPU 使用率や、実行に関連するパフォーマンスの問題に直面していないことが確信できたら、待機に関連するパフォーマンスの問題に直面しています。 つまり、CPU が他の何らかのリソースを待機しているために CPU リソースが効率的に使用されていません。 この場合、次のステップは CPU リソースが何を待機しているかを識別することです。 最上位の待機の種類カテゴリを示すための最も一般的な方法は次のとおりです。

- [クエリ ストア](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)は、一定期間にわたるクエリごとの待機の統計を提供します。 クエリ ストアでは、待機の種類は待機カテゴリに結合されます。 待機カテゴリから待機の種類へのマッピングは、[sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table) で使用できます。
- [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) は、操作中に実行されたスレッドによって発生したすべての待機に関する情報を返します。 この集計ビューを使用して、Azure SQL Database のほか、特定のクエリやバッチに関するパフォーマンスの問題を診断できます。
- [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) は、何らかのリソースを待機しているタスクの待機キューに関する情報を返します。

高 CPU のシナリオでは、次の 2 つの理由で、クエリ ストアおよび待機の統計が CPU 使用率を反映しないことがあります。

- CPU 消費量の高いクエリがまだ実行中の可能性があり、クエリが完了していない
- フェールオーバーが発生したときに、CPU 消費量の高いクエリが実行されていた

クエリ ストアと待機の統計を追跡している動的管理ビューには、正常に完了したクエリとタイムアウトしたクエリの結果のみが表示され、現在実行中のステートメントのデータは (完了するまで) 表示されません。  動的管理ビュー [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) では、現在実行中のクエリとそれに関連するワーカーの時間を追跡することができます。

前の図に示すように、最も一般的な待機は次のとおりです。

- ロック (ブロック)
- I/O
- `tempdb` に関連する競合
- メモリ許可待機

> [!IMPORTANT]
> これらの DMV を使用して次の待機に関連する問題をトラブルシューティングするための一連の T-SQL クエリについては、次を参照してください。
>
> - [I/O パフォーマンスの問題を識別する](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [`tempdb` のパフォーマンスの問題を識別する](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [メモリ許可待機を識別する](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)

## <a name="improving-database-performance-with-more-resources"></a>リソースを増やしてデータベースのパフォーマンスを向上させる

最後に、データベースのパフォーマンス向上につながるすぐに実施可能な方法がない場合は、Azure SQL Database で使用できるリソースの量を変更することができます。 単一データベースの[DTU サービス レベル](sql-database-service-tiers-dtu.md)を変更するか、エラスティック プールの eDTU を増やすことにより、いつでもリソース割り当てを増やすことができます。 あるいは、[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)を使用した場合は、サービス レベルを変更することも、データベースへの割り当てリソースを増やすこともできます。

1. 単一データベースの場合は、[サービス レベルの変更](sql-database-single-database-scale.md)や[コンピューティング リソース](sql-database-single-database-scale.md)の変更をオンデマンドで行うことで、データベースのパフォーマンスを改善できます。
2. 複数のデータベースの場合は、リソースを自動的にスケーリングするための [エラスティック プール](sql-database-elastic-pool-guidance.md) の使用を検討してください。

## <a name="tune-and-refactor-application-or-database-code"></a>アプリケーションまたはデータベース コードをチューニングおよびリファクタリングする

データベースの使用、インデックスの変更、計画の強制的な適用、データベースをワークロードに手動で適合させるためのヒントの使用をより最適に行うために、アプリケーション コードを変更することができます。 手動によるチューニングとコードの書き直しに関するガイダンスとヒントについては、[パフォーマンス ガイダンスのトピック](sql-database-performance-guidance.md)を参照してください。

## <a name="next-steps"></a>次の手順

- Azure SQL Database で自動チューニングを有効にし、自動チューニング機能でワークロードを完全に管理できるようにするには、「[Enable automatic tuning (自動チューニングの有効化)](sql-database-automatic-tuning-enable.md)」を参照してください。
- 手動チューニングを使用するには、[Azure Portal のチューニングに関する推奨事項](sql-database-advisor-portal.md)を確認したうえで、クエリのパフォーマンスを向上させる推奨事項を手動で適用してください。
- [Azure SQL Database のサービス レベル](sql-database-performance-guidance.md)を変更して、データベースで使用可能なリソースを変更します。

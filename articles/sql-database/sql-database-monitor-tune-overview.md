---
title: 監視とパフォーマンスのチューニング - Azure SQL Database | Microsoft Docs
description: 評価と改善を通じて Azure SQL Database のパフォーマンスをチューニングするためのヒントを紹介します。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: 83ff39e9f3b7f95256466c74011e55ebdc22a7a9
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910523"
---
# <a name="monitoring-and-performance-tuning"></a>監視とパフォーマンスのチューニング

Azure SQL Database には、簡単に使用状況を監視し、リソース (CPU、メモリ、I/O など) を追加または削除し、潜在的な問題のトラブルシューティングを行い、データベースのパフォーマンス向上を可能にする推奨を行うためのツールと方法が用意されています。 Azure SQL Database の機能を使用すると、データベースの問題を自動的に修正できます。 

自動チューニングを使用すると、データベースをワークロードに合わせて調整し、パフォーマンスを自動的に最適化することができます。 ただし、トラブルシューティングが必要になる場合がある個別の問題がいくつかあります。 この記事では、パフォーマンスの問題のトラブルシューティングに使用できるいくつかのベスト プラクティスとツールについて説明します。

データベースが問題なく実行されるようにするには、次のことを行う必要があります。
- [データベースのパフォーマンスの監視](#monitor-database-performance)を行って、データベースに割り当てられているリソースでワークロードを処理できることを確認します。 データベースがリソース制限に達している場合は、次のことを検討してください。
   - リソース消費量が上位のクエリを特定して最適化する。
   - [サービス レベルをアップグレードする](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources)ことで、さらにリソースを追加する。
- [パフォーマンスの問題のトラブルシューティング](#troubleshoot-performance-problems)を行って、潜在的な問題が発生した原因を特定し、問題の根本原因を特定します。 根本原因を特定したら、問題を解決するための手順を実行します。

## <a name="monitor-database-performance"></a>データベース パフォーマンスの監視

Azure での SQL データベースのパフォーマンスの監視は、選択したデータベース パフォーマンスのレベルに対して使用されるリソースを監視することから始めます。 以下のリソースを監視します。
 - **CPU 使用率**:データベースが長時間にわたって 100% の CPU 使用率に達しているかどうかを確認します。 CPU 使用率が高い場合は、最も多くのコンピューティング能力を使用するクエリを特定し、調整する必要があることを示している可能性があります。 CPU 使用率が高いことは、データベースまたはインスタンスを上位のサービス レベルにアップグレードする必要があることを示している可能性もあります。 
 - **待機統計**:クエリが待機している時間を調べるには、[sys.dm_os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) を使用します。 クエリの待機は、リソース、キュー待機、または外部待機で発生する場合があります。 
 - **IO 使用率**:データベースが、基盤となるストレージの IO 制限に達しているかどうかを確認します。
 - **メモリ使用量**:データベースまたはインスタンスで使用可能なメモリの量は仮想コアの数に比例します。 メモリがワークロードに対して十分であることを確認します。 ページの予測保持期間は、ページがメモリから削除されるまでの時間を示すことができるパラメーターの 1 つです。

Azure SQL Database サービスには、潜在的なパフォーマンスの問題をトラブルシューティングして修正するのに役立つツールとリソースが含まれています。 [パフォーマンス チューニングの推奨事項](sql-database-advisor.md)を確認することで、リソースを変更することなくクエリのパフォーマンスを改善および最適化する機会を見極めることができます。 

インデックスの不足や、最適化が不完全なクエリは、データベース パフォーマンスが低下する一般的な原因です。 チューニングの推奨事項を適用すると、ワークロードのパフォーマンスを向上させることができます。 識別されたすべての推奨事項を適用することで、Azure SQL データベースで[クエリのパフォーマンスを自動的に最適化](sql-database-automatic-tuning.md)することもできます。 次に、推奨事項によってデータベースのパフォーマンスが向上していることを確認します。

> [!NOTE]
> インデックスは、単一データベースとエラスティック プールでのみ使用できます。 インデックスはマネージド インスタンスでは使用できません。

データベースのパフォーマンスを監視およびトラブルシューティングするには、次のオプションから選択します。

- [Azure portal](https://portal.azure.com) で、 **[SQL データベース]** を選択し、データベースを選択します。 **[監視]** グラフで、最大使用率に近づいているリソースを探します。 DTU 消費は、既定で表示されます。 **[編集]** を選択して、表示される時間の範囲と値を変更します。
- SQL Server Management Studio などのツールには、[パフォーマンス ダッシュボード](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard)などの多くの有用なレポートが用意されています。 これらのレポートを使用して、リソース使用量を監視し、リソース消費量が上位のクエリを特定します。 [クエリ ストア](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed)を使用すると、パフォーマンスが低下したクエリを識別できます。
- [Azure portal](https://portal.azure.com) で、[Query Performance Insight](sql-database-query-performance.md) を使用して、リソースの大半を使用しているクエリを特定します。 この機能は、単一データベースとエラスティック プールでのみ使用できます。
- [SQL Database Advisor](sql-database-advisor-portal.md) を使用して、インデックスの作成と削除、クエリのパラメーター化、およびスキーマの問題の修正に役立つ推奨事項を表示します。 この機能は、単一データベースとエラスティック プールでのみ使用できます。
- [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) を使用して、データベースのパフォーマンスを自動的に監視します。 パフォーマンスの問題が検出されると、診断ログが生成されます。 ログには、問題の詳細と根本原因分析 (RCA) が記載されています。 可能な場合は、パフォーマンス改善の推奨事項が提供されます。
- [自動チューニングを有効にします](sql-database-automatic-tuning-enable.md)。これにより、Azure SQL データベースでは、パフォーマンスの問題を自動的に修正できるようになります。
- [動的管理ビュー (DMV)](sql-database-monitoring-with-dmvs.md)、[拡張イベント](sql-database-xevent-db-diff-from-svr.md)、および[クエリ ストア](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)を使用すると、パフォーマンスの問題のより詳細なトラブルシューティングに役立ちます。

> [!TIP]
> パフォーマンスの問題を特定したら、[パフォーマンスのガイダンス](sql-database-performance-guidance.md)に関する記事を確認して、Azure SQL Database のパフォーマンスを向上させる手法を見つけます。

## <a name="troubleshoot-performance-problems"></a>パフォーマンスの問題をトラブルシューティングする

パフォーマンスの問題を診断および解決するには、まず各アクティブなクエリの状態、および各ワークロードの状態に関連するパフォーマンスの問題の原因となる条件を確認します。 Azure SQL Database のパフォーマンスを向上させるためには、アプリケーションからのアクティブな各クエリ要求は実行状態か待機状態のいずれかであることを理解する必要があります。 Azure SQL Database のパフォーマンスの問題をトラブルシューティングする際は、次の図を念頭に置いてください。

![ワークロードの状態](./media/sql-database-monitor-tune-overview/workload-states.png)

ワークロードのパフォーマンスの問題は、CPU 競合 (*実行に関連する*条件) または何かを待機している個々のクエリ (*待機に関連する*条件) が原因である可能性があります。

実行に関連する問題の原因として、次のことが考えられます。
- **コンパイルの問題**:SQL クエリ オプティマイザーでは、古い統計や、処理される行数または必要なメモリの不正確な推定のために、最適ではないプランが生成される場合があります。 クエリが以前はもっと速く実行されたこと、または他のインスタンス (マネージド インスタンスまたは SQL Server インスタンス) では速く実行されたことがわかっている場合は、実際の実行プランと比較し、違う点を確認します。 より適切なプランを取得するためには、クエリ ヒントを適用したり、統計やインデックスをリビルドしたりしてみてください。 Azure SQL Database で自動プラン修正を有効にして、これらの問題を自動的に軽減します。
- **実行の問題**:クエリ プランが最適な場合は、ログ書き込みスループットなど、データベースのリソース制限に達していることが考えられます。 または、リビルドの必要があるフラグメント化されたインデックスが使用されている可能性があります。 同一のリソースを必要とする多数の同時実行クエリでも、実行の問題が発生する可能性があります。 *待機に関連する*問題は、通常は実行の問題に関連しています。これは、効率的に実行されていないクエリが何らかのリソースを待機している可能性があるためです。

待機に関連する問題の原因として、次のことが考えられます。
- **ブロック**:あるクエリがデータベースのオブジェクトに対するロックを保持している間に、他のクエリが同じオブジェクトにアクセスしようとしています。 ブロックしているクエリは、DMV や監視ツールを使って識別できます。
- **IO の問題**:ページがデータ ファイルまたはログ ファイルに書き込まれるのをクエリが待機している可能性があります。 この場合は、DMV で `INSTANCE_LOG_RATE_GOVERNOR`、`WRITE_LOG`、または `PAGEIOLATCH_*` の待機統計を確認します。
- **TempDB の問題**:ワークロードで一時テーブルが使用されていたり、プランで TempDB 溢れが発生したりしている場合、クエリには TempDB のスループットに問題がある可能性があります。 
- **メモリ関連の問題**:ワークロードに十分なメモリがない場合、ページの予測保持期間が低下するか、クエリが取得するメモリが必要なメモリよりも少なくなる可能性があります。 場合によっては、クエリ オプティマイザーの組み込みインテリジェンスによってメモリ関連の問題は修正されます。
 
以降のセクションでは、いくつかの種類の問題を特定してトラブルシューティングする方法について説明します。

## <a name="performance-problems-related-to-running"></a>実行に関連するパフォーマンスの問題

一般的なガイドラインとして、CPU 使用率が常に 80% 以上である場合、パフォーマンスの問題は実行に関連しています。 実行に関連する問題は、CPU リソースの不足によって発生する場合があります。 または、次のいずれかの条件に関連している可能性があります。

- 実行中のクエリが多すぎる
- コンパイル中のクエリが多すぎる
- 1 つ以上の実行クエリが最適でないクエリ プランを使用している

実行に関連するパフォーマンスの問題を見つけた場合、目標は 1 つまたは複数の方法を使用して正確な問題を識別することです。 これらの方法は、実行に関連する問題を識別するための最も一般的な方法です。

- [Azure portal](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal) を使用して CPU 使用率 (%) を監視します。
- 次の [DMV](sql-database-monitoring-with-dmvs.md) を使用します。

  - [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV は、SQL データベースの CPU、I/O、およびメモリ使用量を返します。 データベースにアクティビティがない場合でも、15 秒の間隔ごとに 1 つの行が存在します。 履歴データは 1 時間保持されます。
  - [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV は、Azure SQL Database の CPU 使用率とストレージ データを返します。 データは 5 分間隔で収集され、集計されます。

> [!IMPORTANT]
> sys.dm_db_resource_stats および sys.resource_stats DMV を使用した T-SQL クエリの CPU 使用率問題のトラブルシューティングを行うには、[CPU パフォーマンスの問題の識別](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues)に関するページを参照してください。

### <a name="ParamSniffing"></a> PSP の問題があるクエリ

パラメーター依存プラン (PSP) の問題が発生するのは、クエリ オプティマイザーが特定のパラメーター値 (または値のセット) に対してのみ最適なクエリ実行プランを生成し、キャッシュされたプランが連続実行で使用されるパラメーター値に対して最適ではない場合です。 最適ではないプランでは、クエリ パフォーマンスの問題が発生し、ワークロード全体のスループットが低下する可能性があります。 

パラメーター スニッフィングとクエリ処理の詳細については、「[クエリ処理アーキテクチャ ガイド](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)」を参照してください。

いくつかの回避策を使用して、PSP の問題を軽減できます。 それぞれの回避策には、関連するトレードオフと欠点があります。

- 各クエリ実行で [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) クエリ ヒントを使用します。 この回避策では、プランの品質の向上と引き換えに、コンパイル時間が長くなって CPU 使用率が増加します。 高スループットを必要とするワークロードでは、`RECOMPILE` オプションを使用できないことがよくあります。
- [OPTION (OPTIMIZE FOR…)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) クエリ ヒントを使用して、実際のパラメーター値を、可能性のあるほとんどのパラメーター値に対して十分良好なプランを生成する一般的なパラメーター値でオーバーライドします。 このオプションを使用するには、最適なパラメーター値および関連するプランの特性をよく理解している必要があります。
- [OPTION (OPTIMIZE FOR UNKNOWN)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) クエリ ヒントを使用して、実際のパラメーター値をオーバーライドし、代わりに密度ベクトルの平均を使用します。 また、受け取ったパラメーター値をローカル変数に取り込み、述語内ではパラメーター自体を使用する代わりにローカル変数を使用することによって、これを行うこともできます。 この修正では、平均密度は "*十分によい*" ものでなければなりません。
- [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) クエリ ヒントを使用して、パラメーター スニッフィング全体を無効にします。
- [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) クエリ ヒントを使用して、キャッシュ内の再コンパイルが行われないようにします。 この回避策では、"十分によい" 一般的なプランがキャッシュ内に既にあることを前提としています。 よいプランが破棄されて新しい悪いプランがコンパイルされる可能性を減らすため、統計の自動更新を無効にすることもできます。
- クエリを書き直してクエリ テキストにヒントを追加することによって [USE PLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) クエリ ヒントを明示的に使用することで、プランを強制します。 または、クエリ ストアを使用するか、[自動チューニング](sql-database-automatic-tuning.md)を有効にして、特定のプランを設定します。
- 1 つのプロシージャを、それぞれが条件付きロジックと関連するパラメーター値に基づいて使用できる入れ子になったプロシージャのセットに置き換えます。
- 静的なプロシージャ定義の代わりに、動的文字列の実行を作成します。

PSP 問題の解決方法について詳しくは、これらのブログ記事をご覧ください。

- 「[パラメーターのスニッフィング](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/)」
- 「[パラメーター化クエリに対する Conor、動的 SQL、プロシージャ、プランの品質の比較](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)」
- 「[SQL Server での SQL クエリの最適化技法:パラメーター スニッフィング](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)」

### <a name="compile-activity-caused-by-improper-parameterization"></a>不適切なパラメーター化によって発生するコンパイル アクティビティ

クエリにリテラルがある場合は、データベース エンジンで自動的にステートメントがパラメーター化されるか、またはユーザーが明示的にステートメントをパラメーター化してコンパイルの数を減らします。 使用するパターンが同じでもリテラル値が異なるクエリのコンパイル回数が多いと、CPU の使用率が高くなる可能性があります。 同様に、ユーザーが継続的にリテラルを含むクエリを部分的にだけパラメーター化した場合、データベース エンジンではクエリはそれ以上パラメーター化されません。  

部分的にパラメーター化されたクエリの例を次に示します。

```sql
SELECT * 
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

この例では、`t1.c1` は `@p1` を受け取りますが、`t2.c2` は引き続きリテラルとして GUID を受け取ります。 この場合、`c2` の値を変更すると、クエリは異なるクエリとして扱われて、新しいコンパイルが発生します。 この例でコンパイルを減らすためには、GUID もパラメーター化します。

次のクエリでは、クエリ ハッシュ別にクエリの数を示して、クエリが適切にパラメーター化されているか判断します。

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

### <a name="factors-that-affect-query-plan-changes"></a>クエリ プランの変更に影響する要因

クエリ実行プランが再コンパイルされると、元々キャッシュされていたプランとは異なるクエリ プランが生成される可能性があります。 元の既存のプランが自動的に再コンパイルされるのには、次のようなさまざまな理由があります。
- スキーマの変更がクエリによって参照された。
- テーブルのデータ変更がクエリによって参照された。 
- クエリ コンテキストのオプションが変更された。

コンパイルされたプランは、次のようなさまざまな理由でキャッシュから取り出される場合があります。

- インスタンスの再起動。
- データベース スコープ構成の変更。
- メモリ不足。
- キャッシュをクリアする明示的な要求。

RECOMPILE ヒントを使用した場合、プランはキャッシュされません。

再コンパイル (またはキャッシュの削除後に新たにコンパイル) を実行しても、元々あったものと同じクエリ実行プランが生成される可能性があります。 プランが以前のプランまたは元のプランから変更された場合、次のような説明が考えられます。

- **物理的設計の変更**:たとえば、新しく作成されたインデックスは、クエリの要件により効果的に対応します。 この新しいインデックスは、クエリ実行の最初のバージョン用に元々選択されていたデータ構造を使用するよりも、この新しいインデックスを使用した方がより最適であるとクエリ オプティマイザーが判断した場合、新しいコンパイルで使用される可能性があります。  参照されているオブジェクトに物理的変更が行われた場合、コンパイル時に新しいプランが選択されることがあります。

- **サーバー リソースの違い**:あるシステムのプランが別のシステムのプランと異なる場合、利用可能なプロセッサの数などのリソースの可用性が、生成されるプランに影響を与える可能性があります。  たとえば、1 つのシステムでプロセッサの数が多い場合は、並列プランが選択される可能性があります。 

- **異なる統計**:参照されているオブジェクトに関連する統計情報が変更されていたり、元のシステムの統計と実質的に異なる場合があります。  統計が変更され再コンパイルが発生すると、クエリ オプティマイザーは、変更された時点からの統計を使用します。 変更された統計のデータの分布と頻度は、元のコンパイルのものとは異なる可能性があります。  これらの変更は、カーディナリティ推定を作成するために使用されます。 (*カーディナリティ推定*は、論理クエリ ツリーを経由してフローすると予想される行数です。)カーディナリティ推定が変更されると、異なる物理演算子、および関連する操作順序の選択が必要になる場合があります。  統計に対する変更が小さくても、クエリ実行プランが変更される可能性があります。

- **データベースの互換性レベルまたはカーディナリティ推定機能のバージョンが変更された**:データベースの互換性レベルの変更により、新しい戦略や機能が有効になり、それによって、異なるクエリ実行プランが作成される可能性があります。  データベースの互換性レベルのほかに、無効または有効になったトレース フラグ 4199 またはデータベース スコープ構成 QUERY_OPTIMIZER_HOTFIXES の変更された状態が、コンパイル時のクエリ実行プランの選択に影響する可能性があります。  トレース フラグ 9481 (レガシ CE を強制) と 2312 (既定の CE を強制) もプランに影響を与えます。 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>問題のあるクエリを解決するか、または提供するリソースを増やす

問題を識別したら、問題が発生しているクエリを調整するか、または CPU の要件を吸収するために SQL データベースの容量を増やすようにコンピューティング サイズまたはサービス レベルをアップグレードすることができます。 

詳細については、「[Azure SQL Database で単一データベースのリソースをスケーリングする](sql-database-single-database-scale.md)」および「[Azure SQL Database でエラスティック プールのリソースをスケーリングする](sql-database-elastic-pool-scale.md)」を参照してください。 マネージド インスタンスのスケーリングについては、「[インスタンス レベルのリソース制限](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits)」を参照してください。

### <a name="performance-problems-caused-by-increased-workload-volume"></a>ワークロード ボリュームの増加が原因で発生したパフォーマンスの問題

アプリケーション トラフィックおよびワークロード ボリュームの増加が原因で、CPU 使用率が増加することがあります。 しかし、この問題を正しく診断するには注意が必要です。 CPU の使用率が高い問題が発生した場合は、これらの質問に答えて、増加の原因がワークロード ボリュームの変化であるかどうかを判断します。

- アプリケーションからのクエリが、CPU の使用率が高い問題の原因ですか?
- CPU 消費量上位の識別可能なクエリについて:

   - 同じクエリに複数の実行プランが関連していましたか? そうである場合、それはなぜですか?
   - 同じ実行プランを持つクエリの場合、実行時間は一貫していましたか? 実行回数は増加しましたか? その場合、ワークロードの増加がパフォーマンスの問題の原因になっている可能性があります。

まとめると、クエリ実行プランの実行方法は異ならなかったが、実行回数と共に CPU 使用率が増加した場合、パフォーマンスの問題はワークロードの増加に関連する可能性があります。

CPU の問題を引き起こしているワークロード ボリュームの変化を特定することは、必ずしも容易ではありません。 次の点を考慮します。 

- **リソースの使用状況の変化**:たとえば、長時間にわたり CPU 使用率が 80% に増加したようなシナリオを検討します。  ワークロードのボリュームの変化を意味するのは、CPU 使用率だけではありません。 クエリ実行プランの回帰や、データの分布の変化も、アプリケーションがまったく同じワークロードを実行しているのにリソースの使用量が増える原因になる可能性があります。

- **新しいクエリの発生**:アプリケーションでは、異なるタイミングで新しいクエリのセットが発生する可能性があります。

- **要求数の増加または減少**:このシナリオは、ワークロードの最も明白な尺度です。 クエリの数は、リソース使用率の増加に常に対応しているとは限りません。 それでもこのメトリックは、他の要因が変更されていないとすると、やはり重要なシグナルです。

## <a name="waiting-related-performance-problems"></a>待機に関連するパフォーマンスの問題 

パフォーマンスの問題が、高い CPU 使用率にも実行にも関係していないことが確実である場合、問題は待機に関係しています。 つまり、CPU が他の何らかのリソースを待機しているために CPU リソースが効率的に使用されていません。 この場合、CPU リソースが何を待機しているかを識別してください。 

これらの方法は、通常、待機の種類の上位カテゴリを表示するために使用されます。

- [クエリ ストア](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)を使用して、一定期間の各クエリの待機統計を検索します。 クエリ ストアでは、待機の種類は待機カテゴリに結合されます。 待機カテゴリから待機の種類へのマッピングは、[sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table) にあります。
- [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) を使用して、操作中に実行されたスレッドによって発生したすべての待機に関する情報を返します。 この集計ビューを使用して、Azure SQL Database のほか、特定のクエリやバッチに関するパフォーマンスの問題を診断できます。
- [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) を使用して、何らかのリソースを待機しているタスクのキューに関する情報を返します。

CPU の使用率が高いシナリオでは、次の場合にクエリ ストアと待機の統計に CPU 使用率が反映されない可能性があります。

- CPU 消費量の高いクエリがまだ実行中である。
- フェールオーバーが発生したときに、CPU 消費量の高いクエリが実行されていた。

クエリ ストアと待機の統計を追跡する DMV は、正常に完了したクエリとタイムアウトしたクエリのみの結果を表示します。 ステートメントが終了するまで、現在実行中のステートメントのデータは表示されません。 動的管理ビュー [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) を使用して、現在実行中のクエリとそれに関連するワーカーの時間を追跡します。

この記事の冒頭付近にあるグラフには、最も一般的な待機は以下であることが示されています。

- ロック (ブロック)
- I/O
- TempDB に関連する競合
- メモリ許可待機

> [!IMPORTANT]
> DMV を使用して待機に関連する問題のトラブルシューティングを行う T-SQL クエリのセットについては、以下を参照してください。
>
> - [I/O パフォーマンスの問題を識別する](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [メモリ許可待機を識別する](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox 待機およびラッチ](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improve-database-performance-with-more-resources"></a>より多くのリソースを使用してデータベースのパフォーマンスを改善する

データベースのパフォーマンス改善につながるすぐに実施可能な方法がない場合は、Azure SQL Database で使用できるリソースの量を変更することができます。 単一データベースの [DTU サービス レベル](sql-database-service-tiers-dtu.md)を変更して、より多くのリソースを割り当てます。 または、任意の時点でエラスティック プールの eDTU を増やします。 あるいは、[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)を使用している場合は、サービス レベルを変更するか、データベースへの割り当てリソースを増やします。

単一データベースの場合は、[サービス レベルまたはコンピューティング リソースの変更](sql-database-single-database-scale.md)をオンデマンドで行うことで、データベースのパフォーマンスを改善できます。 複数のデータベースの場合は、リソースを自動的にスケーリングするための [エラスティック プール](sql-database-elastic-pool-guidance.md) の使用を検討してください。

## <a name="tune-and-refactor-application-or-database-code"></a>アプリケーションまたはデータベース コードをチューニングおよびリファクタリングする

データベースのためのアプリケーション コードの最適化、インデックスの変更、プランの強制的な適用、データベースをワークロードに手動で適合させるためのヒントの活用が可能です。 コードの手動チューニングおよび書き直しについて詳しくは、[パフォーマンス チューニング ガイダンス](sql-database-performance-guidance.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

- Azure SQL Database で自動チューニングを有効にし、自動チューニング機能でワークロードを完全に管理できるようにするには、[自動チューニングの有効化](sql-database-automatic-tuning-enable.md)に関する記事を参照してください。
- 手動チューニングを使用するには、[Azure portal でのチューニングに関する推奨事項](sql-database-advisor-portal.md)に関する記事を参照してください。 クエリのパフォーマンスを改善する推奨事項を手動で適用します。
- [Azure SQL Database のサービス レベル](sql-database-performance-guidance.md)を変更して、データベースで使用可能なリソースを変更します。

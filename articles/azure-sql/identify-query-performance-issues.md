---
title: Azure SQL Database でのクエリ パフォーマンスの問題の種類
description: この記事では、Azure SQL Database でのクエリ パフォーマンスの問題の種類について確認し、これらの問題があるクエリを特定して解決する方法についても説明します
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: troubleshooting
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 1/14/2021
ms.openlocfilehash: 4d0f5404a64eae99ced0dd797954ba042b50060f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98217228"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>Azure SQL Database での検出可能なクエリ パフォーマンス ボトルネックの種類
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

パフォーマンス ボトルネックの解決を試みる際には、最初に、クエリが実行中状態または待機状態にあるときにボトルネックが発生しているかどうかを判断します。 この判断に応じて、適用される解決策が異なります。 以下の図を使用して、実行関連の問題または待機関連の問題を引き起こし得る要因を把握してください。 この記事では、各問題の種類に関して、問題と解決策を確認します。

Azure SQL Database の [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns) または SQL Server の [DMV](database/monitoring-with-dmvs.md) を使用して、これらの種類のパフォーマンス ボトルネックを検出できます。

![ワークロードの状態](./media/identify-query-performance-issues/workload-states.png)

**実行関連の問題**:実行関連の問題は、一般的に、最適化されていないクエリ プランに起因するコンパイルの問題に関連しているか、あるいは、リソースの不足または使用過多に関係がある実行の問題に関連しています。
**待機関連の問題**:通常、待機関連の問題は以下と関係があります。

- ロック (ブロック)
- I/O
- TempDB の使用状況に関連する競合
- メモリ許可待機

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>最適化されていないクエリ プランに起因するコンパイルの問題

SQL クエリ オプティマイザーによって生成された最適化されていないプランが、クエリ パフォーマンス低下の原因になっている可能性があります。 SQL クエリ オプティマイザーでは、インデックスの不足、古い統計、処理される行数の不正確な推定、または必要なメモリの不正確な推定が原因で、最適ではないプランが生成される場合があります。 クエリが以前はもっと速く実行されたこと、または他のインスタンスでは速く実行されたことがわかっている場合は、実際の実行プランと比較し、違う点を確認します。

- 次のいずれかの方法を使用して、不足しているインデックスを特定します。

  - [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#missing-index) を使用する。
  - 単一およびプールされたデータベースの [Database Advisor](database/database-advisor-implement-performance-recommendations.md)。
  - DMV。 この例では、不足しているインデックスの影響、DMV を使用して[不足しているインデックス](database/performance-guidance.md#identifying-and-adding-missing-indexes)を検出する方法、また、不足しているインデックスの推奨事項を実装した場合の影響について示します。
- より適切なプランを取得するためには、[クエリ ヒント](/sql/t-sql/queries/hints-transact-sql-query)の適用、[統計の更新](/sql/t-sql/statements/update-statistics-transact-sql)、または [インデックスの再構成](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)を試してみます。 Azure SQL Database で[自動プラン修正](../azure-sql/database/automatic-tuning-overview.md)を有効にして、これらの問題を自動的に軽減します。

  この[例](database/performance-guidance.md#query-tuning-and-hinting)では、パラメーター化されたクエリに起因する最適化されていないクエリ プランの影響、この条件を検出する方法、およびクエリ ヒントを使用して解決する方法を示します。

- データベース互換性レベルの変更と、インテリジェント クエリ処理の実装を試してみます。 SQL クエリ オプティマイザーでは、データベースの互換性レベルに応じて、異なるクエリ プランが生成される場合があります。 互換性レベルが高いほど、より[インテリジェントなクエリ処理機能](/sql/relational-databases/performance/intelligent-query-processing)が提供されます。

  - クエリ処理の詳細については、「[クエリ処理アーキテクチャ ガイド](/sql/relational-databases/query-processing-architecture-guide)」を参照してください。
  - データベースの互換性レベルを変更するため、また、互換性レベル間の違いを詳しく確認するためには、[ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)に関する記事を参照してください。
  - カーディナリティ推定について詳しく確認するには、[カーディナリティ推定](/sql/relational-databases/performance/cardinality-estimation-sql-server)に関する記事を参照してください。

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>クエリ実行プランが最適でないクエリの解決

以下のセクションでは、クエリ実行プランが最適でないクエリの解決方法について確認します。

### <a name="queries-that-have-parameter-sensitive-plan-psp-problems"></a><a name="ParamSniffing"></a> パラメーター依存プラン (PSP) の問題が発生しているクエリ

パラメーター依存プラン (PSP) の問題が発生するのは、クエリ オプティマイザーが特定のパラメーター値 (または値のセット) に対してのみ最適なクエリ実行プランを生成し、キャッシュされたプランが連続実行で使用されるパラメーター値に対して最適ではない場合です。 最適ではないプランでは、クエリ パフォーマンスの問題が発生し、ワークロード全体のスループットが低下する可能性があります。

パラメーター スニッフィングとクエリ処理の詳細については、「[クエリ処理アーキテクチャ ガイド](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)」を参照してください。

いくつかの回避策を使用して、PSP の問題を軽減できます。 それぞれの回避策には、関連するトレードオフと欠点があります。

- 各クエリ実行で [RECOMPILE](/sql/t-sql/queries/hints-transact-sql-query) クエリ ヒントを使用します。 この回避策では、プランの品質の向上と引き換えに、コンパイル時間が長くなって CPU 使用率が増加します。 高スループットを必要とするワークロードでは、`RECOMPILE` オプションを使用できないことがよくあります。
- [OPTION (OPTIMIZE FOR…)](/sql/t-sql/queries/hints-transact-sql-query) クエリ ヒントを使用して、実際のパラメーター値を、可能性のあるほとんどのパラメーター値に対して十分良好なプランを生成する一般的なパラメーター値でオーバーライドします。 このオプションを使用するには、最適なパラメーター値および関連するプランの特性をよく理解している必要があります。
- [OPTION (OPTIMIZE FOR UNKNOWN)](/sql/t-sql/queries/hints-transact-sql-query) クエリ ヒントを使用して、実際のパラメーター値をオーバーライドし、代わりに密度ベクトルの平均を使用します。 また、受け取ったパラメーター値をローカル変数に取り込み、述語内ではパラメーター自体を使用する代わりにローカル変数を使用することによって、これを行うこともできます。 この修正では、平均密度は "*十分によい*" ものでなければなりません。
- [DISABLE_PARAMETER_SNIFFING](/sql/t-sql/queries/hints-transact-sql-query) クエリ ヒントを使用して、パラメーター スニッフィング全体を無効にします。
- [KEEPFIXEDPLAN](/sql/t-sql/queries/hints-transact-sql-query) クエリ ヒントを使用して、キャッシュ内の再コンパイルが行われないようにします。 この回避策では、"十分によい" 一般的なプランがキャッシュ内に既にあることを前提としています。 よいプランが破棄されて新しい悪いプランがコンパイルされる可能性を減らすため、統計の自動更新を無効にすることもできます。
- クエリを書き直してクエリ テキストにヒントを追加することによって [USE PLAN](/sql/t-sql/queries/hints-transact-sql-query) クエリ ヒントを明示的に使用することで、プランを強制します。 または、クエリ ストアを使用するか、[自動チューニング](../azure-sql/database/automatic-tuning-overview.md)を有効にして、特定のプランを設定します。
- 1 つのプロシージャを、それぞれが条件付きロジックと関連するパラメーター値に基づいて使用できる入れ子になったプロシージャのセットに置き換えます。
- 静的なプロシージャ定義の代わりに、動的文字列の実行を作成します。

PSP 問題の解決方法について詳しくは、これらのブログ記事をご覧ください。

- 「[パラメーターのスニッフィング](/archive/blogs/queryoptteam/i-smell-a-parameter)」
- 「[パラメーター化クエリに対する Conor、動的 SQL、プロシージャ、プランの品質の比較](/archive/blogs/conor_cunningham_msft/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries)」
- 「[SQL Server での SQL クエリの最適化技法:パラメーター スニッフィング](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)」

### <a name="compile-activity-caused-by-improper-parameterization"></a>不適切なパラメーター化によって発生するコンパイル アクティビティ

クエリにリテラルがある場合は、データベース エンジンで自動的にステートメントがパラメーター化されるか、またはユーザーが明示的にステートメントをパラメーター化してコンパイルの数を減らします。 使用するパターンが同じでもリテラル値が異なるクエリのコンパイル回数が多いと、CPU の使用率が高くなる可能性があります。 同様に、ユーザーが継続的にリテラルを含むクエリを部分的にだけパラメーター化した場合、データベース エンジンではクエリはそれ以上パラメーター化されません。

部分的にパラメーター化されたクエリの例を次に示します。

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F';
```

この例では、`t1.c1` は `@p1` を受け取りますが、`t2.c2` は引き続きリテラルとして GUID を受け取ります。 この場合、`c2` の値を変更すると、クエリは異なるクエリとして扱われて、新しいコンパイルが発生します。 この例でコンパイルを減らすためには、GUID もパラメーター化します。

次のクエリでは、クエリ ハッシュ別にクエリの数を示して、クエリが適切にパラメーター化されているか判断します。

```sql
SELECT TOP 10
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
ORDER BY count (distinct p.query_id) DESC;
```

### <a name="factors-that-affect-query-plan-changes"></a>クエリ プランの変更に影響する要因

クエリ実行プランが再コンパイルされると、元々キャッシュされていたプランとは異なるクエリ プランが生成される可能性があります。 元の既存のプランが自動的に再コンパイルされるのには、次のようなさまざまな理由があります。

- スキーマでの変更がクエリによって参照されている
- テーブルのデータ変更がクエリによって参照されている
- クエリ コンテキストのオプションが変更された

コンパイルされたプランは、次のようなさまざまな理由でキャッシュから取り出される場合があります。

- インスタンスの再起動
- データベース スコープ構成の変更
- メモリ不足
- キャッシュをクリアする明示的な要求

RECOMPILE ヒントを使用した場合、プランはキャッシュされません。

再コンパイル (またはキャッシュの削除後に新たにコンパイル) を実行しても、元々あったものと同じクエリ実行プランが生成される可能性があります。 プランが以前のプランまたは元のプランから変更された場合、次のような説明が考えられます。

- **物理的設計の変更**:たとえば、新しく作成されたインデックスは、クエリの要件により効果的に対応します。 この新しいインデックスは、クエリ実行の最初のバージョン用に元々選択されていたデータ構造を使用するよりも、この新しいインデックスを使用した方がより最適であるとクエリ オプティマイザーが判断した場合、新しいコンパイルで使用される可能性があります。 参照されているオブジェクトに物理的変更が行われた場合、コンパイル時に新しいプランが選択されることがあります。

- **サーバー リソースの違い**:あるシステムのプランが別のシステムのプランと異なる場合、利用可能なプロセッサの数などのリソースの可用性が、生成されるプランに影響を与える可能性があります。 たとえば、1 つのシステムでプロセッサの数が多い場合は、並列プランが選択される可能性があります。

- **異なる統計**:参照されているオブジェクトに関連する統計情報が変更されていたり、元のシステムの統計と実質的に異なる場合があります。 統計が変更され再コンパイルが発生すると、クエリ オプティマイザーは、変更された時点からの統計を使用します。 変更された統計のデータの分布と頻度は、元のコンパイルのものとは異なる可能性があります。 これらの変更は、カーディナリティ推定を作成するために使用されます。 (*カーディナリティ推定* は、論理クエリ ツリーを経由してフローすると予想される行数です。)カーディナリティ推定が変更されると、異なる物理演算子、および関連する操作順序の選択が必要になる場合があります。 統計に対する変更が小さくても、クエリ実行プランが変更される可能性があります。

- **データベースの互換性レベルまたはカーディナリティ推定機能のバージョンが変更された**:データベースの互換性レベルの変更により、新しい戦略や機能が有効になり、それによって、異なるクエリ実行プランが作成される可能性があります。 データベースの互換性レベルのほかに、無効または有効になったトレース フラグ 4199 またはデータベース スコープ構成 QUERY_OPTIMIZER_HOTFIXES の変更された状態が、コンパイル時のクエリ実行プランの選択に影響する可能性があります。 トレース フラグ 9481 (レガシ CE を強制) と 2312 (既定の CE を強制) もプランに影響を与えます。

## <a name="resource-limits-issues"></a>リソース制限の問題

最適化されていないクエリ プランおよびインデックスの不足に関連がないクエリ パフォーマンスの低下は、一般的に、リソースの不足または使用過多に関係しています。 クエリ プランが最適な場合、クエリ (およびデータベース) は、データベース、エラスティック プール、またはマネージド インスタンスのリソース上限に達している可能性があります。 たとえば、サービス レベルのログ書き込みスループットを超過している可能性があります。

- Azure portal を使用してリソースの問題を検出する。リソースの制限が問題になっているかを確認するには、[SQL Database リソースの監視](database/monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring)に関する記事を参照してください。 単一データベースとエラスティック プールについては、[Database Advisor のパフォーマンスに関する推奨事項](database/database-advisor-implement-performance-recommendations.md)に関する記事と [Query Performance Insights ](database/query-performance-insight-use.md)に関する記事を参照してください。
- [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) を使用してリソース制限を検出する
- [DMV ](database/monitoring-with-dmvs.md)を使用してリソースの問題を検出する。

  - [sys.dm_db_resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) DMV は、データベースの CPU、I/O、およびメモリ使用量を返します。 データベースにアクティビティがない場合でも、15 秒の間隔ごとに 1 つの行が存在します。 履歴データは 1 時間保持されます。
  - [sys.resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) DMV は、Azure SQL Database の CPU 使用率とストレージ データを返します。 データは 5 分間隔で収集され、集計されます。
  - [多数の個別のクエリで、CPU が高い使用率で累積的に消費されている](database/monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)

この問題をリソース不足として識別した場合は、リソースをアップグレードして、CPU 要件を満たすようにデータベースの容量を増やすことができます。 詳細については、「[Azure SQL Database で単一データベースのリソースをスケーリングする](database/single-database-scale.md)」および「[Azure SQL Database でエラスティック プールのリソースをスケーリングする](database/elastic-pool-scale.md)」を参照してください。 マネージド インスタンスのスケーリングについては、[サービス レベルのリソース制限](managed-instance/resource-limits.md#service-tier-characteristics)に関するセクションを参照してください

## <a name="performance-problems-caused-by-increased-workload-volume"></a>ワークロード ボリュームの増加が原因で発生したパフォーマンスの問題

アプリケーション トラフィックおよびワークロード ボリュームの増加が原因で、CPU 使用率が増加することがあります。 しかし、この問題を正しく診断するには注意が必要です。 CPU の使用率が高い問題が発生した場合は、これらの質問に答えて、増加の原因がワークロード ボリュームの変化であるかどうかを判断します。

- アプリケーションからのクエリが、CPU の使用率が高い問題の原因ですか?
- [CPU 消費量が上位の特定可能なクエリ](database/monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past)について、以下を確認します。

  - 同じクエリに複数の実行プランが関連していましたか? そうである場合、それはなぜですか?
  - 同じ実行プランを持つクエリの場合、実行時間は一貫していましたか? 実行回数は増加しましたか? その場合、ワークロードの増加がパフォーマンスの問題の原因になっている可能性があります。

まとめると、クエリ実行プランの実行方法は異ならなかったが、実行回数と共に CPU 使用率が増加した場合、パフォーマンスの問題はワークロードの増加に関連する可能性があります。

CPU の問題を引き起こしているワークロード ボリュームの変化を特定することは、必ずしも容易ではありません。 次の点を考慮します。

- **リソースの使用状況の変化**:たとえば、長時間にわたり CPU 使用率が 80% に増加したようなシナリオを検討します。 ワークロードのボリュームの変化を意味するのは、CPU 使用率だけではありません。 クエリ実行プランの回帰や、データの分布の変化も、アプリケーションがまったく同じワークロードを実行しているのにリソースの使用量が増える原因になる可能性があります。

- **新しいクエリの発生**:アプリケーションでは、異なるタイミングで新しいクエリのセットが発生する可能性があります。

- **要求数の増加または減少**:このシナリオは、ワークロードの最も明白な尺度です。 クエリの数は、リソース使用率の増加に常に対応しているとは限りません。 それでもこのメトリックは、他の要因が変更されていないとすると、やはり重要なシグナルです。

Intelligent Insights を使用して[ワークロードの増加](database/intelligent-insights-troubleshoot-performance.md#workload-increase)と[プランの回帰](database/intelligent-insights-troubleshoot-performance.md#plan-regression)を検出します。

## <a name="waiting-related-problems"></a>待機関連の問題

最適化されていないプランと、実行の問題に関係している "*待機関連の問題*" を取り除いたら、一般的には、パフォーマンスの問題としてクエリが何らかのリソースを待機している可能性があります。 待機に関連する問題の原因として、次のことが考えられます。

- **ブロック**:

  あるクエリがデータベースのオブジェクトに対するロックを保持している間に、他のクエリが同じオブジェクトにアクセスしようとしています。 ブロックしているクエリは、[DMV](database/monitoring-with-dmvs.md#monitoring-blocked-queries) または [Intelligent Insights ](database/intelligent-insights-troubleshoot-performance.md#locking)を使って識別できます。 詳細については、「[Azure SQL のブロックの問題を理解して解決する](database/understand-resolve-blocking.md)」を参照してください。
- **IO の問題**

  ページがデータ ファイルまたはログ ファイルに書き込まれるのをクエリが待機している可能性があります。 この場合は、DMV で `INSTANCE_LOG_RATE_GOVERNOR`、`WRITE_LOG`、または `PAGEIOLATCH_*` の待機統計を確認します。 DMV を使用した「[IO パフォーマンスに関する問題の特定](database/monitoring-with-dmvs.md#identify-io-performance-issues)」を参照してください。
- **TempDB の問題**

  ワークロードで一時テーブルが使用されていたり、プランで TempDB 溢れが発生したりしている場合、クエリには TempDB のスループットに問題がある可能性があります。 DMV を使用した[TempDB に関する問題の特定](database/monitoring-with-dmvs.md#identify-tempdb-performance-issues)に関する記事を参照してください。
- **メモリ関連の問題**

  ワークロードに十分なメモリがない場合、ページの予測保持期間が低下するか、クエリが取得するメモリが必要なメモリよりも少なくなる可能性があります。 場合によっては、クエリ オプティマイザーの組み込みインテリジェンスによってメモリ関連の問題は修正されます。 DMV を使用した[メモリ許可に関する問題の特定](database/monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)に関する記事を参照してください。

### <a name="methods-to-show-top-wait-categories"></a>上位の待機カテゴリを表示する方法

これらの方法は、通常、待機の種類の上位カテゴリを表示するために使用されます。

- Intelligent Insights を使用して、[待機の増加](database/intelligent-insights-troubleshoot-performance.md#increased-wait-statistic)に起因してパフォーマンスが低下しているクエリを特定します。
- [クエリ ストア](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)を使用して、一定期間の各クエリの待機統計を検索します。 クエリ ストアでは、待機の種類は待機カテゴリに結合されます。 待機カテゴリから待機の種類へのマッピングは、[sys.query_store_wait_stats](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table) にあります。
- [sys.dm_db_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) を使用して、クエリの操作中に実行されたスレッドによって発生したすべての待機に関する情報を返します。 この集計ビューを使用して、Azure SQL Database のほか、特定のクエリやバッチに関するパフォーマンスの問題を診断できます。 クエリの待機は、リソース、キュー待機、または外部待機で発生する場合があります。
- [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) を使用して、何らかのリソースを待機しているタスクのキューに関する情報を返します。

CPU の使用率が高いシナリオでは、次の場合にクエリ ストアと待機の統計に CPU 使用率が反映されない可能性があります。

- CPU 消費量の高いクエリがまだ実行中である。
- フェールオーバーが発生したときに、CPU 消費量の高いクエリが実行されていた。

クエリ ストアと待機の統計を追跡する DMV は、正常に完了したクエリとタイムアウトしたクエリのみの結果を表示します。 ステートメントが終了するまで、現在実行中のステートメントのデータは表示されません。 動的管理ビュー [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) を使用して、現在実行中のクエリとそれに関連するワーカーの時間を追跡します。

> [!TIP]
> その他のツール:
>
> - [TigerToolbox 待機およびラッチ](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="next-steps"></a>次のステップ

[SQL Database の監視とチューニングの概要](database/monitor-tune-overview.md)
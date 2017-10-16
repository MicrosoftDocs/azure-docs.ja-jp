---
title: "Intelligent Insights を使用した Azure SQL Database のパフォーマンスに関する問題のトラブルシューティング | Microsoft Docs"
description: "Intelligent Insights は Azure SQL Database のパフォーマンスに関する問題のトラブルシューティングに役立ちます。”"
services: sql-database
documentationcenter: 
author: danimir
manager: drasumic
editor: carlrab
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 09/25/2017
ms.author: v-daljep
ms.openlocfilehash: edd8c526a681c9cd5226ede6110f21c3362aaef6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>Intelligent Insights を使用した Azure SQL Database のパフォーマンスに関する問題のトラブルシューティング

このページでは、[Intelligent Insights](sql-database-intelligent-insights.md) のデータベース パフォーマンス診断ログを通じて検出された、Azure SQL Database のパフォーマンスに関する問題について説明します。 この診断ログを、[Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)、[Azure イベント ハブ](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)、[Azure Storage](sql-database-metrics-diag-logging.md#stream-into-azure-storage)、または DevOps のカスタム アラートおよびレポート機能を提供するサード パーティ製ソリューションに送信できます。

> [!NOTE]
> Intelligent Insights を使用して Azure SQL Database のパフォーマンスに関する問題を解決するクイック ガイドについては、このドキュメントの「[推奨されるトラブルシューティングのフロー](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow)」のフローチャートをご覧ください。
>

## <a name="detectable-database-performance-patterns"></a>データベースの検出可能なパフォーマンス パターン

Intelligent Insights は、クエリ実行の待機時間、エラー、タイムアウトに基づいて Azure SQL Database のパフォーマンスに関する問題を自動的に検出します。検出されたパフォーマンス パターンは診断ログに出力されます。 検出可能なパフォーマンス パターンの概要を次の表に示します。

| 検出可能なパフォーマンス パターン | 出力される詳細 |
| :------------------- | ------------------- |
| [リソースの上限に到達](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | 使用可能なリソース (DTU) の消費量、データベースのワーカー スレッド、または監視対象サブスクリプションで使用可能なデータベース ログイン セッション数が上限に達し、Azure SQL Database のパフォーマンスの問題を引き起こしています。 |
| [ワークロードの増加](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | Azure SQL Database のパフォーマンス問題の原因となる、データベース上でのワークロードの増加、またはワークロードの継続的な累積が検出されました。 |
| [メモリ不足](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | メモリ許可を要求する worker が統計的に長時間メモリの割り当てを待機している、またはメモリ許可を要求する worker の増加が累積し、Azure SQL Database のパフォーマンスに影響を与えています。 |
| [ロック](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | Azure SQL Database のパフォーマンスに影響を与える、データベースの過剰なロックが検出されました。 |
| [MAXDOP の増加](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | 並列処理の最大限度オプション (MAXDOP) が変更されて、クエリの実行効率に影響を与えています。  |
| [ページラッチの競合](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Azure SQL Database のパフォーマンスに影響を与える、ページラッチの競合が検出されました。 複数のスレッドがメモリ内の同じデータ バッファー ページに同時にアクセスしようとしたため、待機時間が延びて Azure SQL Database のパフォーマンスに影響を与えています。 |
| [インデックスの不足](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | Azure SQL Database のパフォーマンスに影響を与える、インデックスの不足の問題が検出されました。 |
| [新しいクエリ](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | Azure SQL Database の全体的なパフォーマンスに影響を与える、新しいクエリが検出されました。  |
| [異常な待機の統計](sql-database-intelligent-insights-troubleshoot-performance.md#unusual-wait-statistic) | Azure SQL Database のパフォーマンスに影響を与える、データベースの異常な待機時間が検出されました。 |
| [TempDB の競合](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | 複数のスレッドが同じ TempDB リソースにアクセスしようとして、Azure SQL Database のパフォーマンスに影響を与えるボトルネックが発生しています。 |
| [エラスティック プールの DTU の不足](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | エラスティック プールで使用できる eDTU が不足し、Azure SQL Database のパフォーマンスに影響を与えています。 |
| [プランの回帰](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | Azure SQL Database のパフォーマンスに影響を与える、新しいプランまたは既存プランのワークロードの変更が検出されました。 |
| [データベース スコープの構成値の変更](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | データベースの構成の変更が Azure SQL Database のパフォーマンスに影響を与えています。 |
| [処理速度が遅いクライアント](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | Azure SQL Database のパフォーマンスに影響を与える、Azure SQL Database からの出力を十分な速度で処理できない、処理速度が遅いアプリケーション クライアントが検出されました。 |
| [価格レベルのダウングレード](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | 価格レベルのダウングレード アクションによって使用可能なリソースが減少し、Azure SQL Database のパフォーマンスに影響を与えています。 |

> [!TIP]
> [Azure SQL Database の自動チューニング](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-automatic-tuning)を有効にすると、Azure SQL Database のパフォーマンスを継続的に最適化できます。これは Azure SQL に組み込まれたインテリジェンスの独自機能で、お使いの Azure SQL データベースの継続的な監視、インデックスの自動調整、およびクエリ実行プランの修正の適用が可能です。
>

次のセクションでは、上記の検出可能なパフォーマンス パターンについてさらに詳しく説明します。

## <a name="reaching-resource-limits"></a>リソースの上限に到達

### <a name="what-is-happening"></a>状況

この検出可能なパフォーマンス パターンは、使用可能なリソースの上限、worker の上限、およびセッションの上限への到達に関連するパフォーマンスの問題を組み合わせたものです。 このパフォーマンスの問題が検出されると、診断ログの説明フィールドに、パフォーマンスの問題がリソース、worker、セッションの上限のいずれに関連しているかが示されます。

Azure SQL Database のリソースは通常 [DTU リソース](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-what-is-a-dtu)と呼ばれ、CPU および I/O (データおよびトランザクション ログ I/O) リソースを組み合わせたメジャーで構成されます。 リソースの上限への到達パターンが認識されるのは、検出されたクエリ パフォーマンスの低下が、測定されるリソース上限のいずれかに到達したことによって生じている場合です。

セッション上限のリソースは、Azure SQL Database に同時にログインできる数を示します。 このパフォーマンス パターンは、Azure SQL Databases に接続しているアプリケーションが、データベースに同時にログインできる数に到達した場合に認識されます。 データベースで利用できる数よりも多くのセッションをアプリケーションが使用しようとすると、クエリのパフォーマンスが影響を受けます。

利用可能な worker は DTU の使用量としてカウントされないため、worker の上限に到達するというのは、ことはリソース上限への到達の中でも特殊な場合です。 データベースで worker の上限に到達すると、リソース固有の待機時間が上昇するため、クエリのパフォーマンス低下につながります。

### <a name="troubleshooting"></a>トラブルシューティング

診断ログには、パフォーマンスとリソース使用率に影響を与えているクエリのクエリ ハッシュが出力されます。 データベースのワークロードを最適化するための出発点として、この情報を使用できるかもしれません。 具体的には、パフォーマンスの低下に影響を与えるクエリの最適化、インデックスの追加、より均等なワークロードの配分によるアプリケーションの最適化を検討できます。 ワークロードの削減や最適化が難しい場合は、Azure SQL Database サブスクリプションの価格レベルを上げて、使用可能なリソースの量を増やすことを検討できます。

使用可能なセッションの上限に到達した場合は、データベースへのログイン数を減らすという観点から、アプリケーションの最適化を検討するとよいでしょう。 アプリケーションからデータベースにログインする数を減らすことが難しい場合は、データベースの価格レベルを上げるか、お使いのデータベースを分割して複数のデータベースに移行し、ワークロードの配分を分散させることを検討できます。

セッションの上限に対応するためのその他の推奨事項については、「[How to deal with the limits of Azure SQL Database maximum logins](https://blogs.technet.microsoft.com/latam/2015/06/01/how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/) (Azure SQL Database の最大ログイン数の上限に対応する方法)」をご覧ください。 お客様のサブスクリプション レベルで使用可能なリソースの制限を確認するには、「[Azure SQL Database のリソース制限](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-resource-limits)」をご覧ください。

## <a name="workload-increase"></a>ワークロードの増加

### <a name="what-is-happening"></a>状況

このパフォーマンス パターンは、ワークロードの増加、またはより深刻なワークロードの累積によって生じる問題を表します。

いくつかの指標を組み合わせることで検出されます。 測定された基本的な指標によって、過去のワークロード ベースラインとの比較により、ワークロードの増加が検出されます。 その他の形式の検出は、クエリのパフォーマンスに影響を与えるほど大きな、アクティブなワーカー スレッド数の増加の測定に基づいています。

より深刻なのは、Azure SQL Database でワークロードを処理できないことにより、ワークロードが継続的に累積する場合です。 その場合、ワークロード サイズが増大し続けてワークロードの累積状態が続きます。 この状態では、ワークロードが実行されるまでの待機時間が増加し、極めて重大なデータベースのパフォーマンスの問題が生じます。 この問題は、中止されたワーカー スレッド数の増加を監視することで検出されます。 

### <a name="troubleshooting"></a>トラブルシューティング

診断ログには、実行回数が増加したクエリの数、およびワークロードの増加に最も影響を与えているクエリのクエリ ハッシュが出力されます。 特にワークロード増加の最大の要因として特定されたクエリに関して、ワークロード最適化の出発点としてこの情報を使用できます。

データベースにワークロードをより均等に配分することも検討できるかもしれません。 パフォーマンスに影響を与えるクエリを最適化し、インデックスを追加することを検討するのもよいでしょう。 検討すべきもうひとつの方法は、複数のデータベース間にワークロードを配分することです。 これが難しい場合は、Azure SQL Database サブスクリプションの価格レベルを上げて、使用可能なリソースの量を増やすことを検討できます。

## <a name="memory-pressure"></a>メモリ不足

### <a name="what-is-happening"></a>状況

このパフォーマンス パターンは、過去 7 日間のパフォーマンス ベースラインとの比較により、メモリ不足によって生じた現在のデータベース パフォーマンスの低下、またはより深刻なメモリの停滞状態を表します。

メモリ不足は、Azure SQL Database でメモリ許可を要求するワーカー スレッド数が非常に多いという、パフォーマンスの状態を表します。 これにより、メモリ使用率が高い状態が生じて、メモリを要求するすべてのワーカーに Azure SQL Database で効率的にメモリを割り当てることが困難になります。 この問題の最も一般的な原因のひとつは、Azure SQL Database で使用可能なメモリの量に関連しています。またワーカー スレッドの増加とメモリ不足を引き起こすワークロードの増加にも関連があります。

メモリ不足のより深刻なケースは、メモリを解放するクエリよりも、メモリ許可を要求するワーカー スレッド数の方が多いことを示す、メモリの停滞状態です。 エンジンは効率的にメモリを割り当てて要求を満たすことができないため、メモリ許可を要求するワーカー スレッド数が増加し続けます (つまり停滞状態になる)。 メモリの停滞状態は、最も重大なデータベース パフォーマンスの問題のひとつです。

### <a name="troubleshooting"></a>トラブルシューティング

診断ログには、メモリ使用量が高くなっている最大の要因としてマーク付けされたクラーク (つまりワーカー スレッド) のメモリ オブジェクトの保存詳細と、関連するタイムスタンプが出力されます。 これはトラブルシューティングのときに基本情報として使用できます。 

メモリ使用量の高さの要因であるクラークに関連するクエリを、最適化または削除することを検討できます。 また、使用する予定のないデータにクエリを実行していないかを確認できます。 クエリでは常に WHERE 句を使用することをお勧めします。 さらに、データをスキャンするのではなく、非クラスター化インデックスを作成してデータを検索することをお勧めします。

ワークロードの削減、最適化、または複数のデータベースでの分散を検討するのもよいでしょう。 検討すべきもうひとつの方法は、複数のデータベース間にワークロードを配分することです。 これが難しい場合は、Azure SQL Database サブスクリプションの価格レベルを上げて、データベースで使用可能なメモリ リソースの量を増やすことを検討できます。

トラブルシューティングのその他の推奨事項については、「[Memory Grants Meditation: The mysterious SQL Server memory consumer with Many Names (メモリ許可に関する考察：さまざまな名前を持つ、SQL Server の不可解なメモリ コンシューマー)](https://blogs.msdn.microsoft.com/sqlmeditation/2013/01/01/memory-meditation-the-mysterious-sql-server-memory-consumer-with-many-names/)」をご覧ください。

## <a name="locking"></a>ロック

### <a name="what-is-happening"></a>状況

このパフォーマンス パターンは、過去 7 日間のパフォーマンス ベースラインとの比較により、データベースの過剰なロックが検出された、現在のデータベース パフォーマンスの低下を表します。 

最新の RDBMS では、マルチスレッド化されたシステムを実装するために、ロックが不可欠です。そこでは、複数の worker を同時に実行し、可能な場合はデータベースでトランザクションを並列処理することにより、パフォーマンスが最大化されます。 簡単に言うと、この場合のロックは、1 つのトランザクションのみが必要かつ他のリソースのトランザクションと競合しない行、ページ、表、ファイルに排他的にアクセスできる、組み込みのアクセス メカニズムを指します。 リソースの使用をロックしたトランザクションでリソースの使用が完了すると、これらのリソースのロックが解除されて、他のトランザクションが必要なリソースにアクセスできるようになります。 ロックについて詳しくは、「[データベース エンジンのロック](https://msdn.microsoft.com/library/ms190615.aspx)」をご覧ください。

SQL エンジンで実行されたトランザクションが、使用がロックされたリソースにアクセスするために長時間待機している場合は、この待機時間がワークロード実行のパフォーマンス低下に影響します。 

### <a name="troubleshooting"></a>トラブルシューティング

診断ログには、トラブルシューティングのときに基本情報として使用できる、ロックの詳細が出力されます。 報告されたブロッキング クエリ、つまりロックのパフォーマンス低下を引き起こすクエリを分析して、削除するのもよいでしょう。 場合によっては、ブロッキング クエリを最適化できることもあります。
問題を緩和する最も簡単で安全な方法は、トランザクションを常に短くして、最もコストの高いクエリのロック フットプリントを低減させることです。 大規模なオペレーションを小規模なオペレーションに分割することを検討してみてもよいでしょう。 クエリを可能な限り効率的にすることで、クエリのロック フットプリントを低減させることをお勧めします。 デッドロックの可能性を高め、データベースの全体のパフォーマンスに悪影響を及ぼしてしまう大規模なスキャンを減らすことをご検討ください。 ロックの原因として特定されたクエリについては、新しいインデックスを作成したり、既存のインデックスに列を追加したりして、テーブル スキャンを回避することを検討できます。 その他の推奨事項については、「[SQL Server でロックのエスカレーションが原因で発生するブロッキング問題を解決する方法](https://support.microsoft.com/en-us/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in)」をご覧ください。

## <a name="increased-maxdop"></a>MAXDOP の増加

### <a name="what-is-happening"></a>状況

この検出可能なパフォーマンス パターンは、選択されたクエリの実行プランが必要以上に並列処理されている状態を表します。 Azure SQL Database のクエリ オプティマイザーは、可能な場合はクエリの実行同時を決定して処理を高速化することにより、ワークロードのパフォーマンスを向上させます。 ただし、同じクエリを少数の並列 worker、または場合によっては 1 つのワーカー スレッドで処理するのに比べて、クエリを処理する並列 worker が結果を同期してマージするために、互いにより多くの時間を待機に費やしている場合があります。

エキスパート システムによって、ベースライン期間と比較した現在のデータベースのパフォーマンスが分析され、クエリの実行プランが必要以上に並列処理されていることが原因で、以前にも実行されていたクエリの実行速度が低下しているかどうかが判定されます。

同じクエリを並列実行するために使用される CPU コア数の制御には、Azure SQL Database のサーバー構成オプション MAXDOP が使用されます。 

### <a name="troubleshooting"></a>トラブルシューティング

診断ログには、必要以上に並列処理されているために実行時間が増加しているクエリに関連したクエリ ハッシュが出力されます。 また、CXP 待機時間も出力されます。 この時間は、1 つのオーガナイザーまたはコーディネーター スレッド (thread 0) が、結果をマージして先に進む前に、他のすべてのスレッドが終了するのを待機している時間を表します。 さらに、診断ログには、パフォーマンスの低いクエリが実行で待機していた全体の待機時間も出力されます。 これは問題をトラブルシューティングするときの基本情報となります。

まず、複雑なクエリを最適化または簡略化できるかを検討するとよいでしょう。 長いバッチ ジョブを細かく分割することをお勧めします。 また、クエリをサポートするためにインデックスが作成されていることを確認します。 パフォーマンスが低いとフラグを付けられた特定のクエリに対して、MAXDOP (並列処理の最大限度) を手動で適用することも検討できます。 この操作は T-SQL を使用して構成します。「[max degree of parallelism サーバー構成オプションの構成](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option)」をご覧ください。

サーバー構成オプション MAXDOP を既定値のゼロ (0) に設定すると、Azure SQL Database は、使用可能なすべての論理 CPU コアを 1 つのクエリを実行するスレッドの並列処理に使用できます。 MAXDOP を 1 に設定すると、1 つのクエリ実行で使用できるコアは 1 つのみとなり、実際には並列処理が無効になります。 場合によっては、データベースで使用できるコア数、および診断ログの情報に応じて、MAXDOP オプションを調整し、クエリの並列実行に使用できるコア数をそれぞれのケースで問題を解決する値に設定できるかもしれません。

## <a name="pagelatch-contention"></a>ページラッチの競合

### <a name="what-is-happening"></a>状況

このパフォーマンス パターンは、過去 7 日間のワークロード ベースラインとの比較により、ページラッチの競合によって、現在のデータベースのワークロード パフォーマンスが低下していることを表します。

ラッチとは、マルチスレッドを可能にし、インデックス、データ ページ、およびその他の内部構造を含むメモリ内の構造の一貫性を保持するために Azure SQL Database で使用される、軽量の同期メカニズムです。

Azure SQL Database ではさまざまな種類のラッチを使用できます。 簡略化のためには、バッファー ラッチを使用してバッファー プールのメモリ内のページを保護します。またバッファー プールにまだ読み込まれていないページの保護には I/O ラッチを使用します。 バッファー プールのページにデータを書き込んだり、ページからデータを読み取ったりする場合、ワーカー スレッドはまずそのページのバッファー ラッチを取得する必要があります。 メモリ内のバッファー プールで使用できないページにワーカー スレッドがアクセスを試みると、より深刻なパフォーマンスの低下を示す、記憶域から必要な情報を読み込むための I/O 要求が行われます。

ページ ラッチの競合は、複数のスレッドが同じメモリ内の構造で同時にラッチを取得しようとする場合に発生し、クエリ実行の待機時間が増加します。 記憶域からデータにアクセスする必要があるページラッチの I/O の競合では、この待機時間がさらに増加し、ワークロードのパフォーマンスに大きく影響します。 ページ ラッチの競合は、スレッドが互いに待機し、複数の CPU システムのリソースにおいて競合する、最も一般的なシナリオです。

### <a name="troubleshooting"></a>トラブルシューティング

診断ログには、問題解決のための基本情報として使用できる、ページラッチ競合の詳細が出力されます。

ページラッチは Azure SQL Database の内部制御メカニズムであるため、使用のタイミングは自動的に決定されます。 スキーマ デザインを含むアプリケーションの決定は、ラッチの決定論的なビヘイビアーにより、ページラッチの動作に影響を与えます。
ラッチの競合を処理する方法のひとつは、連続したインデックス キーを連番でないキーに置き換えて、インデックスの範囲に挿入を均等に配置することです。 これは通常、ワークロードを比例的に配分する先頭列をインデックスに設けることによって実行します。 検討すべきもうひとつの方法は、テーブル パーティションです。 パーティション テーブルの計算列でハッシュ パーティション分割のスキーマを作成することは、ラッチの過剰な競合を軽減するための一般的な方法です。 ページラッチの I/O の競合については、このパフォーマンスの問題の軽減に役立つインデックスの導入がお勧めです。 詳細については、「[Diagnosing and Resolving Latch Contention on SQL Server (SQL Server でのラッチの競合の診断と対応)](http://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf)」(PDF のダウンロード) をご覧ください。

## <a name="missing-index"></a>インデックスの不足

### <a name="what-is-happening"></a>状況

このパフォーマンス パターンは、過去 7 日間のベースラインとの比較により、インデックスの不足によって、現在のデータベースのワークロード パフォーマンスが低下していることを表します。

インデックスは、クエリのパフォーマンスを高速化するために使用されます。 アクセスまたはスキャンする必要があるデータセットのページ数を削減して、テーブル データへのすばやいアクセスを提供します。

インデックスの作成がパフォーマンスの向上に役立つと考えられる、パフォーマンス低下の原因となるクエリがこの検出によって特定されます。

### <a name="troubleshooting"></a>トラブルシューティング

診断ログには、ワークロードのパフォーマンスに影響を与えることが認められるクエリのクエリ ハッシュが出力されます。 このようなクエリに関しては、インデックスの作成を検討できるかもしれません。 これらのクエリを最適化するか、不要な場合は削除することも検討できます。 使用しないデータへのクエリは実行しないことをお勧めします。

> [!TIP]
> Azure では組み込みのインテリジェンスによって、お使いのデータベースに最適なインデックスを自動管理します。
>
> Azure SQL Database のパフォーマンスを継続的に最適化するために、[Azure SQL Database の自動チューニング](sql-database-automatic-tuning.md)を有効にすることをお勧めします。Azure SQL に組み込まれたインテリジェンスの独自の機能により、お使いの Azure SQL データベースが継続的に監視され、データベースで使用するインデックスが自動的に調整および作成されます。
>

## <a name="new-query"></a>[新しいクエリ]

### <a name="what-is-happening"></a>状況

この検出可能なパフォーマンス パターンは、過去 7 日間のパフォーマンス ベースラインとの比較により、パフォーマンスが低く、ワークロードのパフォーマンスに影響を与える新しいクエリが検出されたことを表します。

優れたパフォーマンスのクエリを作成することは、困難なタスクです。 クエリの作成について詳しくは、[SQL クエリの作成](https://msdn.microsoft.com/library/bb264565.aspx)に関する記事をご覧ください。既存のクエリのパフォーマンスを最適化する方法については「[クエリのチューニング](https://msdn.microsoft.com/library/ms176005.aspx)」をご覧ください。

### <a name="troubleshooting"></a>トラブルシューティング

診断ログには、CPU 使用量が最も高い、最大 2 つの新しいクエリの情報 (クエリ ハッシュを含む) が出力されます。 検出されたクエリはワークロードのパフォーマンスに影響を与えているため、クエリの最適化を検討するとよいでしょう。 使用しないデータは取得しないのが最善です。 クエリでは WHERE 句を使用することが推奨されます。 複雑なクエリを簡略化し、クエリを細かく分割することもお勧めします。 大きなバッチ クエリを小さなバッチ クエリに分割することも検討に値します。 通常、このパフォーマンスの問題を軽減するには、新しいクエリにインデックスを導入することをお勧めします。

[Azure SQL Database Query Performance Insight](sql-database-query-performance.md) の使用をご検討ください。

## <a name="unusual-wait-statistic"></a>異常な待機の統計

### <a name="what-is-happening"></a>状況

この検出可能なパフォーマンス パターンは、過去 7 日間のワークロード ベースラインとの比較してパフォーマンスの低いクエリが検出された場合の、ワークロードのパフォーマンスの低下を表します。

この場合、システムでは、パフォーマンスの低いクエリはその他の検出可能なパフォーマンスの標準カテゴリには分類されません。ただし、回帰の原因となっている待機の統計が検出されているため、&#8220;*異常な待機の統計情報*&#8221; を含むクエリ (回帰の原因となっている、異常な待機の統計が検出されている) とみなされます。 

### <a name="troubleshooting"></a>トラブルシューティング

診断ログには、異常な待機時間の詳細、影響を受けたクエリのクエリ ハッシュ、および待機時間に関する情報が出力されます。

この場合、システムでは、パフォーマンスの低いクエリの根本原因が特定されないため、パフォーマンスの低いクエリに関する診断情報は、手動でトラブルシューティングを行うための第一歩として役立ちます。 このようなクエリのパフォーマンスを最適化することを検討できます。 通常、使用しないデータはフェッチせず、複雑なクエリは細かく分割することをお勧めします。 クエリのパフォーマンスの最適化について詳しくは、「[クエリのチューニング](https://msdn.microsoft.com/library/ms176005.aspx)」をご覧ください。

## <a name="tempdb-contention"></a>TempDB の競合

### <a name="what-is-happening"></a>状況

この検出可能なパフォーマンス パターンは、TempDB リソースへのアクセスを試みるスレッドのボトルネックが存在するデータベース パフォーマンスの状態を表します (この状態は I/O に関連していません)。 このパフォーマンスの問題の一般的なシナリオは、小さな TempDB テーブルを作成して使用し、次いで削除する、同時実行される数百のクエリです (つまり TempDB テーブル)。 過去 7 日間のパフォーマンス ベースラインとの比較により、同じ TempDB テーブルを使用する同時実行クエリ数の増加において統計的有意性が認められ、データベースのパフォーマンスに影響を与えていることがシステムで検出されます。

### <a name="troubleshooting"></a>トラブルシューティング

診断ログには、パフォーマンスの問題を解決するための出発点として役立つ、TempDB 競合の詳細が出力されます。 このような競合を軽減し、ワークロード全体のスループットを向上するために取り組むべきことがいくつかあります。 一時テーブルの使用をやめることができるかもしれません。 また、メモリ最適化テーブルの使用を検討するのもよいでしょう。詳しくは、「[メモリ最適化テーブルの概要](https://docs.microsoft.com/en-us/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables)」をご覧ください。 

## <a name="elastic-pool-dtu-shortage"></a>エラスティック プールの DTU の不足

### <a name="what-is-happening"></a>状況

この検出可能なパフォーマンス パターンは、お客様のサブスクリプションのエラスティック プールで使用可能な DTU が不足しているため、過去 7 日間のベースラインと比較して、現在のデータベースのワークロード パフォーマンスが低下していることを表します。 

Azure SQL Database のリソースは通常 [DTU リソース](sql-database-what-is-a-dtu.md)と呼ばれ、CPU および I/O (データおよびトランザクション ログ I/O) リソースを組み合わせたメジャーで構成されます。 [Azure エラスティック プールのリソース](sql-database-elastic-pool.md)は、複数のデータベース間でスケーリングのために共有される、使用可能な eDTU リソースのプールとして使用されます。 お使いのエラスティック プールで使用可能な eDTU リソースが、プール内のすべてのデータベースをサポートするのに十分でない場合は、エラスティック プールの DTU の不足というパフォーマンスの問題がシステムで検出されます。

### <a name="troubleshooting"></a>トラブルシューティング

診断ログには、エラスティック プールに関する情報が出力され、DTU を最も多く消費しているデータベースが一覧表示されるとともに、消費量が最も多いデータベースによるプールの DTU 使用率が提供されます。

このパフォーマンスの状態は、エラスティック プール内で同じプールの eDTU を使用する複数のデータベースに関連しているため、トラブルシューティングの手順では、DTU を最も多く消費するデータベースにフォーカスする必要があります。 このようなデータベースでの使用量が最も多いクエリの最適化も含めて、消費量が最も多いデータベースのワークロードを削減することを検討します。 また、使用しないデータにクエリを実行していないかを確認することもできます。 そのほかの方法としては、DTU を最も多く消費するデータベースを使用するアプリケーションの最適化と、複数のデータベース間でのワークロードの再分配があります。

DTU を最も多く消費するデータベースで、現在のワークロードを削除したり最適化したりすることが難しい場合は、エラスティック プールの価格レベルを上げることを検討できます。 これにより、エラスティック プールで使用可能な DTU を増やすことができます。

## <a name="plan-regression"></a>プランの回帰

### <a name="what-is-happening"></a>状況

この検出可能なパフォーマンス パターンは、最適化されていないクエリ実行プランの使用が Azure SQL Database で始まっていることを表します。 通常、最適化されていないプランはクエリ実行回数の増加の原因となり、現在およびその他のクエリの待機時間の増加につながります。

Azure SQL Database では、クエリ実行のコストが最も低いクエリ実行プランが決定されます。 ただし、クエリの種類とワークロードが変更されると、既存のプランが効率的でなくなったり、Azure SQL Database で最適な評価が行われなかったりする場合があります。 修正については、クエリの実行プランは手動で適用できます。

この検出可能なパフォーマンス パターンでは、プラン回帰の 3 つの異なるケース (新しいプランへの回帰、古いプランへの回帰、ワークロードが変更された既存のプランへの回帰) が組み合わされています。 発生した特定のプラン回帰の種類は、診断ログの &#8220;*詳細*&#8221; プロパティに表示されます。

新しいプランへの回帰の状態は、古いプランほど効率的ではない、新しいクエリ実行プランの実行が Azure SQL Database で開始されている状態を表します。 古いプランへの回帰の状態は、Azure SQL Database で、新しくより効率的なプランから、新しいプランほど効率的ではない古いプランの使用に切り替わった状態を表します。 ワークロードが変更された既存のプランへの回帰は、古いプランと新しいプランの両方が継続的に交互に行われつつ、パフォーマンスの低いプランへの比重が高まっている状態を表します。

プランの回帰について詳しくは、「[What is plan regression in SQL server (SQL server のプランの回帰とは)](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/06/09/what-is-plan-regression-in-sql-server/)」をご覧ください。 

### <a name="troubleshooting"></a>トラブルシューティング

診断ログには、この状態のパフォーマンスの問題を解決するための基本情報として使用できる、クエリ ハッシュ、適切なプランの ID、不適切なプランの ID、およびクエリ ID が出力されます。

提供されたクエリ ハッシュを使用して特定できるクエリに対して、どのプランのパフォーマンスが優れているかを分析します。 クエリに対してパフォーマンスが優れているプランを見極めたら、手動で適用できます。 詳しくは、「[How SQL Server prevents plan regressions (SQL Server がプランの回帰を回避するしくみ)](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/04/25/you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/)」をご覧ください。

> [!TIP]
> Azure では組み込みのインテリジェンスによって、お使いのデータベースに最適なクエリ実行プランを自動管理します。
>
> Azure SQL Database のパフォーマンスを継続的に最適化するために、[Azure SQL Database の自動チューニング](sql-database-automatic-tuning.md)を有効にすることをお勧めします。Azure SQL に組み込まれたインテリジェンスの独自の機能により、お使いの Azure SQL データベースが継続的に監視され、データベースで使用する最適なクエリ実行プラン自動的に調整および作成されます。
>

## <a name="database-scoped-configuration-value-change"></a>データベース スコープの構成値の変更

### <a name="what-is-happening"></a>状況

この検出可能なパフォーマンス パターンは、データベース スコープの構成が変更されたことで、過去 7 日間のワークロードの動作と比較して、パフォーマンスの低下が検出された状態を表します。 これは、データベース スコープの構成に行われた最近の変更が、データベースのパフォーマンスに有益ではないと考えられることを示します。

データベース スコープの構成の変更は、個々のデータベースごとに設定できます。 この構成は、データベースの個々のパフォーマンスを最適化するために、それぞれのケースに応じて使用します。 個別のデータベースごとに、MAXDOP、LEGACY_CARDINALITY_ESTIMATION、PARAMETER_SNIFFING、QUERY_OPTIMIZER_HOTFIXES、CLEAR PROCEDURE_CACHE のオプションを構成できます。

### <a name="troubleshooting"></a>トラブルシューティング

診断ログには、過去 7 日間のワークロードの動作と比較して、パフォーマンスの低下が生じる原因となった、最近行われたデータベース スコープの構成の変更が出力されます。 構成の変更を元の値に戻すか、または求められるパフォーマンス レベルに到達するまで値を調整することを検討します。 満足のいくパフォーマンスを達成している同様のデータベースから、データベース スコープの構成値をコピーすることも検討します。 パフォーマンスのトラブルシューティングが難しい場合は、Azure SQL Database の既定値に戻して、この基準値から微調整を行うことを検討できます。

データベース スコープの構成の最適化と、構成の変更に使用する T-SQL 構文について詳しくは、「[Alter database scoped configuration (Transact-SQL) (データベース スコープの構成の変更 (TRANSACT-SQL))](https://msdn.microsoft.com/en-us/library/mt629158.aspx)」をご覧ください。

## <a name="slow-client"></a>処理速度が低いクライアント

### <a name="what-is-happening"></a>状況

この検出可能なパフォーマンス パターンは、Azure SQL Database を使用するクライアントが、データベースで結果が送信されるのに必要な速度で、データベースからの出力データを処理できない状態を表します。 Azure SQL Database はバッファーで実行されたクエリの結果を考慮しないため、続行する前に速度を下げて、送信されたクエリの出力をクライアントが処理するまで待機します。 この状態は、Azure SQL Database からの出力データを使用するクライアントに対して、十分な速度でデータを送信できない低速なネットワークに関連する場合もあります。

過去 7 日間のワークロードの動作と比較して、パフォーマンスの低下が検出された場合にのみ、この状態が生成されます。 これによって、過去のパフォーマンスの動作と比較して、統計的に有意なパフォーマンスの低下が認められる場合にのみ、このパフォーマンスの問題が検出されることが保証されます。

### <a name="troubleshooting"></a>トラブルシューティング

この検出可能なパフォーマンス パターンは、クライアント側のアプリケーション、またはクライアント側のネットワークで、トラブルシューティングが必要な状態を表します。 診断ログには、過去 2 時間以内にクライアントによる処理を最も長い時間待機したクエリのクエリ ハッシュと待機時間が出力されます。 これらは、トラブルシューティングのときに基本情報として使用できます。

これらのクエリの使用に関して、アプリケーションのパフォーマンスを最適化することを検討します。 発生する可能性のあるネットワーク待機時間の問題について考えてみることもできます。 パフォーマンス低下の問題は、過去 7 日間のパフォーマンス ベースラインの変化に基づいているため、最近、このパフォーマンスの低下の原因となるような、アプリケーションの変更やネットワークの状態の変化がなかったかを調べることができます。 

## <a name="pricing-tier-downgrade"></a>価格レベルのダウングレード

### <a name="what-is-happening"></a>状況

この検出可能なパフォーマンス パターンは、お客様の Azure SQL Database の価格レベルがダウングレードされたことを表します。 データベースで使用できるリソース (DTU) の減少により、過去 7 日間のベースラインと比較して、現在のデータベースのパフォーマンスが低下していることがシステムで検出されています。

このほかに、お客様の Azure SQL Database サブスクリプションの価格レベルがダウングレードされた後、短期間内に上位レベルにアップグレードされた可能性もあります。 これは一時的なパフォーマンスの低下の検出を意味し、診断ログの詳細セクションには価格レベルのダウングレードとアップグレードとして出力されます。

### <a name="troubleshooting"></a>トラブルシューティング

価格レベルを下げたことにより Azure SQL Database で使用できる DTU が減少しても、パフォーマンスに満足している場合は、現時点で何らかの対応をする必要はありません。 価格レベルを下げた後に Azure SQL Database のパフォーマンスに満足できない場合は、データベースのワークロードを削減するか、価格レベルを上位に引き上げることを検討します。

## <a name="recommended-troubleshooting-flow"></a>推奨されるトラブルシューティングのフロー

Intelligent Insights を使用してパフォーマンスの問題を解決するための推奨される方法については、次のフローチャートに従ってください。

Azure SQL Analytics に移動して、Azure Portal から Intelligent Insights にアクセスします。 受信したパフォーマンス アラートを検索して、クリックします。 検出ページで現在の状況を確認します。 問題の根本原因の解析、クエリ テキスト、クエリの時間の傾向、およびインシデントの展開に関する情報をよく読みます。 パフォーマンスの問題の軽減に関する Intelligent Insights の推奨事項を使用して、問題を解決します。 

[![トラブルシューティング フローチャート](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> このフローチャートの PDF バージョンをダウンロードするには、フローチャートをクリックします。

Intelligent Insights では、パフォーマンスの問題の根本原因の解析に通常 1 時間かかります。 Intelligent Insights で問題を検索できない (通常これは、問題が発生してから 1 時間未満のためです) ものの、この問題が重要な場合は、クエリ データ ストア (QDS) を使用して、パフォーマンスの問題を手動で特定します。 詳細については、「[クエリ ストアを使用したパフォーマンスの監視](https://docs.microsoft.com/en-us/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)」をご覧ください。

## <a name="next-steps"></a>次のステップ
- [Intelligent Insights](sql-database-intelligent-insights.md) の概念の習得
- [Intelligent Insights Azure SQL Database パフォーマンス診断ログ](sql-database-intelligent-insights-use-diagnostics-log.md)の使用
- [Azure SQL Analytics を使用した Azure SQL Database](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql) の監視
- [Azure リソースからのログ データの収集と使用](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)の習得

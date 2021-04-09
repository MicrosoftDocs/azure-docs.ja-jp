---
title: 自動バキュームを最適化する - Azure Database for PostgreSQL - Single Server
description: この記事では、Azure Database for PostgreSQL - Single Server 上で自動バキュームを最適化する方法について説明します
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 07/09/2020
ms.openlocfilehash: a94afc1ab970c2cd3f509c86efba4e455d46fd13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96012563"
---
# <a name="optimize-autovacuum-on-an-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - Single Server 上で自動バキュームを最適化する

この記事では、Azure Database for PostgreSQL サーバー上で自動バキュームを効率的に最適化する方法について説明します。

## <a name="overview-of-autovacuum"></a>自動バキュームの概要

PostgreSQL は、MVCC (MultiVersion Concurrency Control) を使用して、データベースの同時実行の強化を実現しています。 すべての更新は結果として挿入と削除になり、すべての削除は結果として、削除対象のためにソフト マークされている行になります。 ソフトマークによって、後で消去される使用不能タプルが識別されます。 このようなタスクを実行するために、PostgreSQL ではバキューム ジョブを実行します。

バキューム ジョブは、手動または自動でトリガーできます。 データベースで更新や削除の操作が大量に発生している場合、使用不能タプルの数は多くなります。 データベースがアイドル状態の場合、使用不能タプルの数は少なくなります。 データベースの負荷が高くなると、バキュームを頻繁に実行する必要があります。このような場合、バキューム ジョブの "*手動*" 実行は不便になります。

自動バキュームは、構成することが可能で、チューニングによるメリットがあります。 PostgreSQL 出荷時の既定値は、どのような種類のデバイスでも製品が確実に機能するように設定されています。 たとえば、Raspberry Pi などのデバイスです。 最適な構成値は、次の条件によって変わります。

- SKU やストレージ サイズなど、使用可能な合計リソース。
- リソースの使用状況。
- 個々のオブジェクトの特性。

## <a name="autovacuum-benefits"></a>自動バキュームのメリット

バキュームを随時実行しないと、使用不能タプルの蓄積によって、以下のような結果が生じる可能性があります。

- データベースやテーブルの拡大など、データの肥大化。
- 最適とはいえないインデックスの増大。
- I/O の増加。

## <a name="monitor-bloat-with-autovacuum-queries"></a>自動バキューム クエリによる肥大化を監視する

次のサンプル クエリは、XYZ という名前のテーブルで、使用不能タプルとライブ タプルの数を識別するために設計されています。

```sql
SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;
```

## <a name="autovacuum-configurations"></a>自動バキュームの構成

自動バキュームを制御する構成パラメーターは、次の 2 つの重要な問いに基づいています。

- いつ開始する必要があるか。
- 開始後にどれだけ消去する必要があるか。

ここでは、この問いに基づいて更新することができる自動バキューム構成パラメーターの一部と、いくつかのガイダンスを示します。

パラメーター|説明|既定値
---|---|---
autovacuum_vacuum_threshold|任意の 1 つのテーブルでバキューム操作をトリガーするために必要な、更新または削除されたタプルの最小数を指定します。 既定値は 50 タプルです。 このパラメーターは、postgresql.conf ファイルまたはサーバーのコマンド ラインでのみ設定します。 個々のテーブルの設定をオーバーライドするには、テーブル ストレージのパラメーターを変更します。|50
autovacuum_vacuum_scale_factor|バキューム操作をトリガーするかどうかを決定する際に、autovacuum_vacuum_threshold に追加するテーブル サイズの割合を指定します。 既定値は 0.2 (テーブル サイズの 20%) です。 このパラメーターは、postgresql.conf ファイルまたはサーバーのコマンド ラインでのみ設定します。 個々のテーブルの設定をオーバーライドするには、テーブル ストレージのパラメーターを変更します。|0.2
autovacuum_vacuum_cost_limit|自動バキューム操作で使用されるコストの上限値を指定します。 -1 (既定値) が指定されている場合は、通常の vacuum_cost_limit 値が使用されます。 複数の worker がある場合、この値は実行中の自動バキューム worker 間で均等に配分されます。 各 worker での上限の合計は、この変数の値を超えません。 このパラメーターは、postgresql.conf ファイルまたはサーバーのコマンド ラインでのみ設定します。 個々のテーブルの設定をオーバーライドするには、テーブル ストレージのパラメーターを変更します。|-1
autovacuum_vacuum_cost_delay|自動バキューム操作で使用されるコストの遅延値を指定します。 -1 が指定された場合は、通常の vacuum_cost_delay 値が使用されます。 既定値は 20 ミリ秒です。 このパラメーターは、postgresql.conf ファイルまたはサーバーのコマンド ラインでのみ設定します。 個々のテーブルの設定をオーバーライドするには、テーブル ストレージのパラメーターを変更します。|20 ミリ秒
autovacuum_naptime | 特定のデータベースに対する自動バキューム実行の間隔の最小遅延を指定します。 各ラウンドで、デーモンがデータベースを調査し、そのデータベースのテーブルに対して、必要に応じて VACUUM コマンドと ANALYZE コマンドを発行します。 遅延は秒単位で計測されます。 このパラメーターは、postgresql.conf ファイルまたはサーバーのコマンド ラインでのみ設定します。| 15 s
autovacuum_max_workers | 任意の時点で実行できる (自動バキューム ランチャー以外の) 自動バキューム プロセスの最大数を指定します。 既定値は 3 です。 このパラメーターは、サーバー起動時にのみ設定します。|3

個々のテーブルの設定をオーバーライドするには、テーブル ストレージのパラメーターを変更します。

## <a name="autovacuum-cost"></a>自動バキュームのコスト

バキューム操作実行時の "コスト" を次に示します。

- バキューム実行対象のデータ ページはロックされます。
- バキューム ジョブの実行中にはコンピューティングとメモリが使用されます。

そのため、バキューム ジョブの実行頻度を極端に上げたり下げたりしないでください。 バキューム ジョブはワークロードに応じて調整する必要があります。 それぞれにトレードオフがあるため、すべての自動バキューム パラメーターの変更をテストしてください。

## <a name="autovacuum-start-trigger"></a>自動バキュームの開始トリガー

自動バキュームは、使用不能タプルの数が、autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples を超えるとトリガーされます。 この reltuples は定数です。

自動バキュームからのクリーンアップは、データベースの負荷に合わせて実行する必要があります。 そうしないと、ストレージが不足し、クエリが全般的に遅くなる可能性があります。 時間の経過と共に調整を進めて、バキューム操作で使用不能タプルをクリーンアップする速度が、使用不能タプルが作られる速度と等しくなる必要があります。

更新と削除の数が多いデータベースでは、使用不能タプルが多くなり、より多くの領域が必要です。 一般に、更新と削除の数が多いデータベースでは、autovacuum_vacuum_scale_factor の値と、autovacuum_vacuum_threshold の値を小さくする価値があります。 値を小さくすると、使用不能タプルが長時間にわたって蓄積されません。 小さなデータベースでは、バキュームが必要な緊急度が低いため、どちらのパラメーターにもより大きな値を使用できます。 頻繁にバキュームすると、コンピューティングとメモリのコストがかかります。

既定の割合である 20% は、使用不能タプルの割合が低いテーブルで適切に機能します。 使用不能タプルの割合が高いテーブルでは適切に機能しません。 たとえば、20 GB のテーブルの場合、この倍率では 4 GB の使用不能タプルになります。 1 TB のテーブルの場合、200 GB の使用不能タプルになります。

PostgreSQL では、テーブル レベルまたはインスタンス レベルでこれらのパラメーターを設定できます。 Azure Database for PostgreSQL では、現在、これらのパラメーターはテーブル レベルでのみ設定できます。

## <a name="estimate-the-cost-of-autovacuum"></a>自動バキュームのコストを見積もる

自動バキュームの実行は "コスト高" で、バキューム操作の実行時間を制御するためのパラメーターがあります。 以下のパラメーターが、バキューム実行コストの見積りの際に役立ちます。

- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

バキューム プロセスは、物理ページを読み取り、使用不能タプルがないか調べます。 shared_buffers 内のページはそれぞれ、コスト 1 (vacuum_cost_page_hit) であると見なされます。 その他のページはすべて、使用不能タプルが存在する場合はコスト 20 (vacuum_cost_page_dirty)、使用不能タプルが存在しない場合はコスト 10 (vacuum_cost_page_miss) と見なされます。 プロセスが autovacuum_vacuum_cost_limit を超過するとバキューム操作は停止されます。

上限に達すると、プロセスは autovacuum_vacuum_cost_delay パラメーターで指定された期間の間、スリープ状態になり、その後再度開始されます。 上限に達しない場合、自動バキュームは、autovacuum_nap_time パラメーターで指定された値の後に開始されます。

要約すると、autovacuum_vacuum_cost_delay パラメーターと autovacuum_vacuum_cost_limit パラメーターによって、単位時間あたりにクリーンアップが許可されるデータ量が制御されます。 既定値は、ほとんどの価格レベルに対して小さすぎることに注意してください。 これらのパラメーターの最適値は、価格レベルに左右されるので、それに応じて構成する必要があります。

autovacuum_max_workers パラメーターは、同時に実行できる自動バキューム プロセスの最大数を決定します。

PostgreSQL では、テーブル レベルまたはインスタンス レベルでこれらのパラメーターを設定できます。 Azure Database for PostgreSQL では、現在、これらのパラメーターはテーブル レベルでのみ設定できます。

## <a name="optimize-autovacuum-per-table"></a>テーブルごとに自動バキュームを最適化する

前述のすべての構成パラメーターは、テーブルごとに構成できます。 次に例を示します。

```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

自動バキュームは、テーブルごとの同期プロセスです。 テーブルにある使用不能タプルの割合が増えるにつれて、自動バキュームを行う "コスト" が高くなります。 更新と削除の頻度が高いテーブルは、複数のテーブルに分割できます。 テーブルを分割することで、自動バキュームを並列化し、1 つのテーブルで自動バキュームを完了するための "コスト" を削減できます。 並列の自動バキューム worker の数を増やし、worker が豊富にスケジュールされるようにすることもできます。

## <a name="next-steps"></a>次のステップ

自動バキュームの使用方法と調整方法の詳細については、以下の PostgreSQL ドキュメントを参照してください。

- [第 18 章、サーバー構成](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
- [第 24 章、日常のデータベース メンテナンス タスク](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)

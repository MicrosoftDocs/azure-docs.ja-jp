---
title: Synapse SQL のレコメンデーション
description: Synapse SQL のレコメンデーションとその生成方法について説明します
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 06/26/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e4564005e3b9cc9673cc20596d4114d102174b9e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85482855"
---
# <a name="synapse-sql-recommendations"></a>Synapse SQL のレコメンデーション

この記事では、Azure Advisor によって提供される Synapse SQL のレコメンデーションについて説明します。  

Synapse SQL からは、データ ウェアハウスのワークロードのパフォーマンスを常に最適化するためのレコメンデーションが提供されます。 レコメンデーションは [Azure Advisor](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) と強固に統合され、[Azure portal](https://aka.ms/Azureadvisor) 内でベスト プラクティスを直接提供します。 Synapse SQL を使うと、1 日の間にアクティブなワークロードのテレメトリが収集され、レコメンデーションが提示されます。 以下にサポートされるレコメンデーションのシナリオの概要と、推奨されるアクションを適用する方法を示します。

[レコメンデーションは今すぐ確認](https://aka.ms/Azureadvisor)することができます。 

## <a name="data-skew"></a>データ スキュー

データ スキューは、ワークロードの実行時に余分なデータ移動やリソースのボトルネックを引き起こす可能性があります。 次のドキュメントでは、データ スキューを特定し、最適な配布キーを選択することでその発生を回避する方法について説明します。

- [スキューを特定して削除する](sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="no-or-outdated-statistics"></a>統計情報がない、または統計情報が古い

最適でない統計情報は、SQL のクエリ オプティマイザーが最適でないクエリ計画を作成する原因となるため、クエリ パフォーマンスに深刻な影響を及ぼす可能性があります。 次のドキュメントでは、統計情報の作成や更新に関するベスト プラクティスについて説明します。

- [テーブル統計情報の作成と更新](sql-data-warehouse-tables-statistics.md)

これらのレコメンデーションによって影響を受けるテーブルの一覧を表示するには、次の [T-SQL スクリプト](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables)を実行します。 Advisor は、同じ T-SQL スクリプトを継続的に実行してこれらのレコメンデーションを生成します。

## <a name="replicate-tables"></a>テーブルのレプリケート

レプリケート テーブルのレコメンデーションについて、Advisor は、以下の物理的特性に基づいてテーブルの候補を検出します。

- レプリケート テーブルのサイズ
- 列の数
- テーブルの分散の種類
- パーティションの数

Advisor は、テーブル アクセスの頻度、返された平均行数、データ ウェアハウスのサイズとアクティビティに関連するしきい値などのワークロード ベースのヒューリスティックを継続的に活用し、品質の高いレコメンデーションが生成されるようにしています。

以下のセクションでは、レプリケート テーブルの各レコメンデーションについて Azure portal 上で見つけることができる、ワークロードベースのヒューリスティックについて説明します。

- [Scan avg]\(スキャン平均値) - 各テーブル アクセスでテーブルから返された行数の割合の、過去 7 日間にわたる平均値
- Frequent read, no update(頻繁な読み取り、更新なし - アクセス アクティビティを示しているが過去 7 日間にテーブルは更新されていないことを示します
- [Read/update ratio]\(読み取り/更新の比率) - 過去 7 日間にわたる、テーブルがアクセスされた頻度とテーブルが更新された回数との比率
- [Activity]\(アクティビティ) - アクセスのアクティビティに基づいて、使用状況を測定します。 このアクティビティでは、テーブル アクセス アクティビティを、過去 7 日間のデータ ウェアハウス間にわたる平均テーブル アクセス アクティビティと比較します。

現在、Advisor に一度に表示されるのは、最上位のアクティビティを優先順位付けするクラスター化列ストア インデックスを含め、最大 4 つのレプリケート テーブルの候補のみです。

> [!IMPORTANT]
> レプリケート テーブルのレコメンデーションは、完全に試験済みの状態ではなく、アカウント データの移動操作は考慮されていません。 これをヒューリスティックとして追加することには取り組んでいますが、それまでは、レコメンデーションの適用後、実際のワークロードを必ず検証する必要があります。 レプリケート テーブルに関する詳細については、次の[ドキュメント](design-guidance-for-replicated-tables.md#what-is-a-replicated-table)を参照してください。


## <a name="adaptive-gen2-cache-utilization"></a>アダプティブ (Gen2) キャッシュ使用率
大規模なワーキング セットがある場合は、キャッシュ ヒット率が低くなり、キャッシュ使用率が高くなる場合があります。 このシナリオでは、キャッシュ容量を増やすためにスケールアップして、ワークロードを再実行する必要があります。 詳細については、次の[ドキュメント](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-how-to-monitor-cache)を参照してください。 

## <a name="tempdb-contention"></a>Tempdb の競合

Tempdb の競合が高い場合、クエリのパフォーマンスが低下する可能性があります。  Tempdb の競合は、ユーザー定義の一時テーブルを経由するか、または大量のデータ移動が発生した場合に、生じる可能性があります。 このシナリオに対しては、tempdb の割り当てを拡張し、[リソース クラスとワークロード管理を構成](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management)してクエリにより多くのメモリを提供することができます。 

## <a name="data-loading-misconfiguration"></a>データ読み込みの構成の誤り

待機時間を最小限に抑えるには、SQL プールと同じリージョン内にあるストレージ アカウントから常にデータを読み込む必要があります。 [高スループットのデータ インジェストに関する COPY ステートメント](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)を使用し、ストレージ アカウント内にあるステージング済みファイルを分割してスループットを最大化します。 COPY ステートメントを使用できない場合は、スループットを向上させるために、バッチ サイズが大きい SqlBulkCopy API または bcp を使用できます。 データ読み込みに関するその他のガイダンスについては、次の[ドキュメント](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/guidance-for-loading-data)を参照してください。 

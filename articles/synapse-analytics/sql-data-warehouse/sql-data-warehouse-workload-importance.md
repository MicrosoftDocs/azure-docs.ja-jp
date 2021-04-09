---
title: ワークロードの重要度
description: Azure Synapse Analytics の専用 SQL プール クエリの重要度を設定するためのガイダンスです。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 06d1957d182f2cabc336afcfc47a790442a3cb9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678408"
---
# <a name="azure-synapse-analytics-workload-importance"></a>Azure Synapse Analytics ワークロードの重要度

この記事では、ワークロードの重要度が Azure Synapse の専用 SQL プールの要求の実行順序に与える影響について説明します。

## <a name="importance"></a>重要度

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

ビジネス ニーズでは、データ ウェアハウスのワークロードを他よりも重要視することが求められる場合があります。  会計期間が終了する前にミッション クリティカルな販売データが読み込まれるというシナリオを検討します。  気象データなど、その他のソースのデータ読み込には、厳密な SLA はありません。 売上データのロード要求の重要度を高く設定し、気象データのロード要求の重要度を低く設定すると、売上データのロードがリソースに最初にアクセスしてより速く完了します。

## <a name="importance-levels"></a>重要度のレベル

重要度のレベルには、low、below_normal、normal、above_normal、high の 5 つがあります。  重要度が設定されない要求には、既定のレベルである normal が割り当てられます。 同じ重要度レベルが設定された要求については、現時点で存在しているスケジュール動作と同じになります。

## <a name="importance-scenarios"></a>重要度のシナリオ

上記で説明した売上データと気象データに関する重要度の基本的なシナリオのほかに、さらに、データ処理およびクエリのニーズを満たす場合にワークロードの重要度が役に立つというシナリオもあります。

### <a name="locking"></a>ロック

読み取りおよび書き込みアクティビティ用のロックへのアクセスは、自然な競合の 1 つの領域です。 [パーティションの切り替え](sql-data-warehouse-tables-partition.md)や[オブジェクトの名前変更](/sql/t-sql/statements/rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)などのアクティビティには、管理者特権でのロックが必要です。  ワークロードの重要度が設定されていなくても、Azure Synapse の専用 SQL プールによってスループットが最適化されます。 スループットの最適化とは、実行中の要求とキューに置かれた要求が同じロック ニーズを持ち、リソースが利用可能な場合、キューに置かれた要求は、要求キューに先に到達した、より高いロック ニーズを持つ要求をバイパスする可能性があることを意味しています。 より高いロック ニーズを持つ要求にワークロードの重要度が適用されると、 重要度の高い方の要求が、重要度の低い方の要求よりも先に実行されます。

次の例を確認してください。

- Q1 はアクティブに実行されていて、SalesFact からデータを選択しています。
- Q2 はキューに置かれ、Q1 が完了するのを待機しています。  それは午前 9 時に送信され、SalesFact への新しいデータのパーティション切り替えを試みています。
- Q3 は午前 9 時 01 分に送信され、SalesFact からデータを選択することを必要としています。

Q2 と Q3 が同じ重要度を持ち、Q1 がまだ実行中である場合、Q3 は実行を開始します。 Q2 は引き続き SalesFact に対する排他的なロックを待機します。  Q2 の重要度が Q3 より高い場合、Q3 は Q2 が終了するのを待って実行を開始する必要があります。

### <a name="non-uniform-requests"></a>均一でない要求

クエリ要求を満たすのに重要度を役立てることができるというもう 1 つのシナリオは、リソース クラスが異なる要求を送信する場合に適用されます。  既に述べたように、同じ重要度の下では、Azure Synapse の専用 SQL プールによってスループットが最適化されます。 混在したサイズ要求 (Smallrc または mediumrc など) がキューに置かれると、使用可能なリソース内で収まる最も早く到着した要求が専用 SQL プールによって選択されます。 ワークロードの重要度が適用されると、重要度の最も高い要求が次にスケジュールされます。
  
DW500c に対する次の例について考えてみてください。

- Q1、Q2、Q3、および Q4 は、smallrc クエリを実行しています。
- Q5 は、午前 9 時に mediumrc リソース クラスを使用して送信されます。
- Q6 は、午前 9 時 01 分に smallrc リソース クラスを使用して送信されます。

Q5 は mediumrc であるために、2 つの同時実行スロットが必要になります。 Q5 は、2 つの実行中のクエリが完了するまで待機する必要があります。  ただし、実行中のクエリ (Q1 から Q4 まで) のいずれかが完了すると、クエリを実行するリソースが存在するため Q6 がすぐにスケジュールされます。  Q5 の重要度は Q6 よりも高いため、Q6 は Q5 が実行されるのを待って、実行を開始する必要があります。

## <a name="next-steps"></a>次のステップ

- 分類子の作成の詳細については、「[CREATE WORKLOAD CLASSIFIER (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)」を参照してください。  
- ワークロードの分類の詳細については、[ワークロードの分類](sql-data-warehouse-workload-classification.md)に関するページを参照してください。  
- ワークロード分類子の作成方法については、[ワークロード分類子の作成](quickstart-create-a-workload-classifier-tsql.md)に関するクイック スタートを参照してください。
- [ワークロードの重要度の構成](sql-data-warehouse-how-to-configure-workload-importance.md)と [Workload Management の管理と監視](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)に関するハウツー記事を参照してください。
- クエリと割り当てられている重要度を確認するには、「[sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)」を参照してください。

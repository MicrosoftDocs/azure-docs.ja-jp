---
title: Azure SQL Data Warehouse のワークロードの重要度 | Microsoft Docs
description: Azure SQL Data Warehouse でクエリの重要度を設定するためのガイダンスです。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 2a78f342d7e4b14700224bb63598f41ca95322a5
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595420"
---
# <a name="azure-sql-data-warehouse-workload-importance"></a>Azure SQL Data Warehouse のワークロードの重要度

この記事では、ワークロードの重要度が、SQL Data Warehouse の要求の実行順序にどのような影響を与えるかについて説明します。

## <a name="importance"></a>重要度

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

ビジネス ニーズでは、データ ウェアハウスのワークロードを他よりも重要視することが求められる場合があります。  会計期間が終了する前にミッション クリティカルな販売データが読み込まれるというシナリオを検討します。  気象データなど、その他のソースのデータ読み込には、厳密な SLA はありません。   売上データの読み込み要求に高い重要度を設定し、気象データの読み込み要求に低い重要度を設定すると、売上データの読み込みが確実にリソースに対するアクセス権を最初に取得し、より速く完了することになります。

## <a name="importance-levels"></a>重要度のレベル

重要度のレベルには、low、below_normal、normal、above_normal、high の 5 つがあります。  重要度が設定されない要求には、既定のレベルである normal が割り当てられます。  同じ重要度レベルが設定された要求については、現時点で存在しているスケジュール動作と同じになります。

## <a name="importance-scenarios"></a>重要度のシナリオ

上記で説明した売上データと気象データに関する重要度の基本的なシナリオのほかに、さらに、データ処理およびクエリのニーズを満たす場合にワークロードの重要度が役に立つというシナリオもあります。

### <a name="locking"></a>ロック

読み取りおよび書き込みアクティビティ用のロックへのアクセスは、自然な競合の 1 つの領域です。  [パーティションの切り替え](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition)や[オブジェクトの名前変更](/sql/t-sql/statements/rename-transact-sql)などのアクティビティには、管理者特権でのロックが必要です。  ワークロードの重要度が設定されていなくても、SQL Data Warehouse ではスループットの最適化が行われます。  スループットの最適化とは、実行中の要求とキューに置かれた要求が同じロック ニーズを持ち、リソースが利用可能な場合、キューに置かれた要求は、要求キューに先に到達した、より高いロック ニーズを持つ要求をバイパスする可能性があることを意味しています。  より高いロック ニーズを持つ要求にワークロードの重要度が適用されると、 重要度の高い方の要求が、重要度の低い方の要求よりも先に実行されます。

次の例を考えてみます。

Q1 はアクティブに実行されていて、SalesFact からデータを選択しています。
Q2 はキューに置かれ、Q1 が完了するのを待機しています。  それは午前 9 時に送信され、SalesFact への新しいデータのパーティション切り替えを試みています。
Q3 は午前 9 時 01 分に送信され、SalesFact からデータを選択することを必要としています。

Q2 と Q3 が同じ重要度を持ち、Q1 がまだ実行中である場合、Q3 は実行を開始します。 Q2 は引き続き SalesFact に対する排他的なロックを待機します。  Q2 の重要度が Q3 より高い場合、Q3 は Q2 が終了するのを待って実行を開始する必要があります。

### <a name="non-uniform-requests"></a>均一でない要求

クエリ要求を満たすのに重要度を役立てることができるというもう 1 つのシナリオは、リソース クラスが異なる要求を送信する場合に適用されます。  既に述べたように、同じ重要度の下では、SQL Data Warehouse のスループットが最適化されます。  混在したサイズ要求 (Smallrc または mediumrc など) がキューに置かれると、使用可能なリソース内で収まる最も早く到着した要求が SQL Data Warehouse によって選択されます。  ワークロードの重要度が適用されると、重要度の最も高い要求が次にスケジュールされます。
  
DW500c に対する次の例について考えてみてください。

Q1、Q2、Q3、および Q4 は、smallrc クエリを実行しています。
Q5 は、午前 9 時に mediumrc リソース クラスを使用して送信されます。
Q6 は、午前 9 時 01 分に smallrc リソース クラスを使用して送信されます。

Q5 は mediumrc であるために、2 つの同時実行スロットが必要になります。  Q5 は、2 つの実行中のクエリが完了するまで待機する必要があります。  ただし、実行中のクエリ (Q1 から Q4 まで) のいずれかが完了すると、クエリを実行するリソースが存在するため Q6 がすぐにスケジュールされます。  Q5 の重要度は Q6 よりも高いため、Q6 は Q5 が実行されるのを待って、実行を開始する必要があります。

## <a name="next-steps"></a>次の手順

- 分類子の作成の詳細については、「[CREATE WORKLOAD CLASSIFIER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql)」を参照してください。  
- SQL Data Warehouse のワークロード分類の詳細については、[ワークロードの分類](sql-data-warehouse-workload-classification.md)に関する記事を参照してください。  
- ワークロード分類子の作成方法については、[ワークロード分類子の作成](quickstart-create-a-workload-classifier-tsql.md)に関するクイック スタートを参照してください。
- [ワークロードの重要度の構成](sql-data-warehouse-how-to-configure-workload-importance.md)と [Workload Management の管理と監視](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)に関するハウツー記事を参照してください。
- クエリと割り当てられている重要度を確認するには、「[sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)」を参照してください。

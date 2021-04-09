---
title: 専用 SQL プールのワークロード分類
description: Azure Synapse Analytics で分類を使用して、専用 SQL プールのクエリのコンカレンシー、重要度、コンピューティング リソースを管理するためのガイダンスです。
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
ms.openlocfilehash: 7cd3619aa60f1bd8ac13ff767857b44348989285
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678425"
---
# <a name="workload-classification-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics の専用 SQL プールのワークロード分類

この記事では、Azure Synapse の専用 SQL プールを使用してワークロード グループと重要度を受信要求に割り当てるための、ワークロード分類プロセスについて説明します。

## <a name="classification"></a>分類

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

ワークロード管理の分類では、[リソース クラス](resource-classes-for-workload-management.md#what-are-resource-classes)と[重要度](sql-data-warehouse-workload-importance.md)を割り当てて、ワークロード ポリシーを要求に適用することができます。

データ ウェアハウスのワークロードを分類するにはさまざまな方法がありますが、最も簡単で一般的な分類は読み込みとクエリです。 データを読み込むには、INSERT、UPDATE、DELETE ステートメントを使用します。  データをクエリするには、SELECT を使用します。 データ ウェアハウス ソリューションには、多くの場合、より高いリソース クラスにはより多くのリソースを割り当てるなど、読み込みアクティビティに関するワークロード ポリシーがあります。 クエリには、読み込みアクティビティに比べて重要度を低くするなど、異なるワークロード ポリシーを適用できます。

読み込みとクエリのワークロードを下位分類することもできます。 下位分類を使用すると、ワークロードをより細かく制御できます。 たとえば、クエリ ワークロードがキューブの更新、ダッシュボードのクエリ、アドホック クエリで構成されているとします。 これらのクエリ ワークロードをそれぞれ異なるリソース クラスや重要度の設定で分類できます。 読み込みも、下位分類の利点を受けることができます。 大規模な変換を大規模なリソース クラスに割り当てることができます。 より高い重要度を使用して、気象データやソーシャル データのフィードよりも先に主要な販売データを読み込むことができます。

リソースを必要としないか、実行に影響を及ぼす重要度を必要としない一部のステートメントは分類されません。  DBCC コマンド、BEGIN、COMMIT、および ROLLBACK TRANSACTION ステートメントは分類されません。

## <a name="classification-process"></a>分類プロセス

現在、専用 SQL プールでの分類は、[sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) を使用して、対応するリソース クラスが割り当てられたロールにユーザーを割り当てることで実現されます。 1 つのリソース クラスへのログインを超えて要求を特徴付けることは、この機能によって制限されます。 より高度な分類方法として、[CREATE WORKLOAD CLASSIFIER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 構文を利用できるようになりました。  専用 SQL プール ユーザーは、この構文で `workload_group` パラメーターを使用して、要求に重要度を割り当て、割り当てるシステム リソース量を指定できます。

> [!NOTE]
> 分類は要求単位で評価されます。 1 つのセッション内の複数の要求を別々に分類できます。

## <a name="classification-weighting"></a>分類の重み付け

分類プロセスの一環として、どのワークロード グループを割り当てるかを判断するために重み付けが用意されています。  重み付けは次のようになります。

|分類子パラメーター |Weight   |
|---------------------|---------|
|MEMBERNAME:USER      |64       |
|MEMBERNAME:ROLE      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

`membername` パラメーターは必須です。  ただし、指定された membername がデータベース ロールではなくデータベース ユーザーである場合、ユーザーの重み付けの方が高くなるため、その分類子が選択されます。

ユーザーがさまざまなリソース クラスが割り当てられた、または複数の分類子が一致する複数のロールのメンバーである場合、そのユーザーには最上位のリソース クラスが割り当てられます。  この動作は、既存のリソース クラス割り当て動作と整合します。

## <a name="system-classifiers"></a>システム分類子

ワークロード分類には、システム ワークロード分類子が用意されています。 システム分類子は、既存のリソース クラス ロールのメンバーシップを通常の重要度でリソース クラスのリソース割り当てにマップします。 システム分類子は削除できません。 システム分類子を表示するには、次のクエリを実行します。

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>リソース クラス割り当てと分類子の混在

自動的に作成されるシステム分類子を利用すると、ワークロード分類に簡単に移行できます。 リソース クラス ロールのマッピングと分類の優先順位を併用すると、重要度を使用して新しい分類子を作成し始めるときに誤分類が起きやすくなります。

以下のシナリオについて考えてみます。

- 既存のデータ ウェアハウスで、データベース ユーザー DBAUser に largerc リソース クラス ロールが割り当てられています。 このリソース クラス割り当ては、sp_addrolemember を使用して行われました。
- データ ウェアハウスのワークロード管理が更新されました。
- 新しい分類構文をテストするため、(DBAUser がそのメンバーである) データベース ロール DBARole を mediumrc と高い重要度にマッピングする分類子が作成されました。
- DBAUser がログインしてクエリを実行すると、クエリは largerc に割り当てられます。 これは、ロールのメンバーシップよりもユーザーが優先されるためです。

誤分類のトラブルシューティングを簡単にするため、ワークロード分類子を作成するときは、リソース クラス ロールのマッピングを削除することをお勧めします。  次のコードは、既存のリソース クラス ロールのメンバーシップを返します。  対応するリソース クラスから返されたメンバー名ごとに、[sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) を実行します。

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember '[Resource Class]', membername
```

## <a name="next-steps"></a>次のステップ

- 分類子の作成の詳細については、「[CREATE WORKLOAD CLASSIFIER (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)」を参照してください。  
- ワークロード分類子の作成方法については、[ワークロード分類子の作成](quickstart-create-a-workload-classifier-tsql.md)に関するクイック スタートを参照してください。
- [ワークロードの重要度の構成](sql-data-warehouse-how-to-configure-workload-importance.md)と [Workload Management の管理と監視](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)に関するハウツー記事を参照してください。
- クエリと割り当てられている重要度を確認するには、「[sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)」を参照してください。

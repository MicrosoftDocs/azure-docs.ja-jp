---
title: 専用 SQL プールのワークロードの重要度を構成する
description: Azure Synapse Analytics で要求レベルの重要度を設定する方法について説明します。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 05/15/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 38fb842cf90c110266f53b79a9ab2ef6157025b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98681287"
---
# <a name="configure-workload-importance-in-dedicated-sql-pool-for-azure-synapse-analytics"></a>Azure Synapse Analytics の専用 SQL プールにおけるワークロードの重要度を構成する

Azure Synapse の専用 SQL プールで重要度を設定すると、クエリのスケジュール設定に影響を与えることができます。 重要度の高いクエリが、重要度の低いクエリよりも先に実行されるようスケジュールされます。 重要度をクエリに割り当てるには、ワークロード分類子を作成する必要があります。

## <a name="create-a-workload-classifier-with-importance"></a>重要度を使用したワークロード分類子を作成する

データ ウェアハウスのシナリオでは、多くの場合、ビジー状態のシステムでクエリをすばやく実行する必要があるユーザーがいます。  このユーザーは、レポートを実行する必要がある会社の経営陣や、アドホック クエリを実行するアナリストである場合があります。 重要度を割り当てるには、ワークロード分類子を作成することで重要度をクエリに割り当てます。  次の例では [create workload classifier](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 構文を使用して、2 つの分類子を作成しています。 `Membername` には単一のユーザーまたはグループを指定できます。  既存の専用 SQL プールのユーザーを確認するには、次のコマンドを実行します。

```sql
Select name from sys.sysusers
```

重要度の高いユーザー向けのワークロード分類子を作成するには、次のコマンドを実行します。

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = above_normal);
```

重要度の低いアドホック クエリを実行するユーザー向けのワークロード分類子を作成するには、次のコマンドを実行します。  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = below_normal);
```

## <a name="next-steps"></a>次の手順

- ワークロード管理の詳細については、[ワークロード分類](sql-data-warehouse-workload-classification.md)に関するページを参照してください
- 重要度の詳細については、[ワークロードの重要度](sql-data-warehouse-workload-importance.md)に関するページを参照してください

> [!div class="nextstepaction"]
> [ワークロードの重要度の管理と監視に移動](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)

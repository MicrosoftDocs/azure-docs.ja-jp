---
title: "Azure SQL Data Warehouse のコンピューティング能力の管理 (概要) | Microsoft Docs"
description: "Azure SQL Data Warehouse のパフォーマンス スケールアウト機能。 DWU を調整してスケールアウトする方法や、コンピューティング リソースを一時停止および再開してコストを節約する方法を説明します。"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: e13a82b0-abfe-429f-ac3c-f2b6789a70c6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 03/22/2017
ms.author: elbutter
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: f4a79413bc5e660504b4b6b48fcf496fb0f08ade
ms.lasthandoff: 03/29/2017


---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-overview"></a>Azure SQL Data Warehouse のコンピューティング能力の管理 (概要)
> [!div class="op_single_selector"]
> * [概要](sql-data-warehouse-manage-compute-overview.md)
> * [ポータル](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST ()](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>

SQL Data Warehouse のアーキテクチャではストレージとコンピューティングを分離して、それぞれを個別にスケーリングできます。 結果として、データの量とは無関係に、パフォーマンスの需要を満たすようにコンピューティングをスケーリングできます。 このアーキテクチャでは、当然、コンピューティングとストレージに対する[課金][billed]は別々に行われます。 

この概要では、SQL Data Warehouse でのスケールアウトのしくみについて説明するほか、SQL Data Warehouse の機能を一時停止、再開、およびスケーリングする方法について説明します。 [Data Warehouse ユニット (DWU)][data warehouse units (DWUs)] に関するページを参照して、DWU とパフォーマンスの関係を確認してください。 

## <a name="how-compute-management-operations-work-in-sql-data-warehouse"></a>SQL Data Warehouse でのコンピューティング管理操作のしくみ
SQL Data Warehouse のアーキテクチャは、制御ノード、コンピューティング ノード、および 60 のディストリビューションに分散されたストレージ層で構成されます。 

SQL Data Warehouse の通常のアクティブ セッション中、システムのヘッド ノードは、メタデータを管理し、分散されたクエリ オプティマイザーを格納します。 このヘッド ノードの下には、コンピューティング ノードとストレージ層があります。 DWU が 400 のシステムには、1 つのヘッド ノードと 4 つのコンピューティング ノードのほか、60 のディストリビューションから成るストレージ層があります。 

スケール操作または一時停止操作を実行すると、システムは、最初にすべての受信クエリを中止し、次にトランザクションをロールバックして一貫性のある状態を確保します。 スケール操作の場合、このトランザクションのロールバックが完了した後でのみスケーリングが行われます。 スケールアップ操作の場合、システムによって追加で必要な数のコンピューティング ノードがプロビジョニングされた後、それらのコンピューティング ノードをストレージ層に再接続する処理が開始されます。 スケールダウン操作の場合、不要なノードが解放された後、残りのコンピューティング ノードが自動的に適切な数のディストリビューションに再接続します。 一時停止操作では、すべてのコンピューティング ノードが解放され、さまざまなメタデータ操作が実行された後、最終的にシステムが安定した状態になります。

| DWU  | \#コンピューティング ノードの数 | \#ノードあたりのディストリビューションの数 |
| ---- | ------------------ | ---------------------------- |
| 100  | 1                  | 60                           |
| 200  | 2                  | 30                           |
| 300  | 3                  | 20                           |
| 400  | 4                  | 15                           |
| 500  | 5                  | 12                           |
| 600  | 6                  | 10                           |
| 1,000 | 10                 | 6                            |
| 1200 | 12                 | 5                            |
| 1500 | 15                 | 4                            |
| 2000 | 20                 | 3                            |
| 3000 | 30                 | 2                            |
| 6000 | 60                 | 1                            |

コンピューティングを管理するための 3 つの主要な機能は次のとおりです。

1. 一時停止
2. 再開
3. スケール

これらの各操作は、完了までに数分かかる場合があります。 スケーリング、一時停止、再開を自動的に行う場合は、別のアクションに進む前に特定の操作が完了していることを確認するロジックを実装することをお勧めします。 

さまざまなエンドポイントを通じてデータベースの状態を確認することで、このような操作の自動化を適切に実装できます。 ポータルでは、操作の完了通知とデータベースの現在の状態が提供されます。ただし、プログラムを使って状態をチェックすることはできません。 

>  [!NOTE]
>
>  コンピューティング管理機能はすべてのエンドポイントで使用できるわけではありません。
>
>  

|              | 一時停止/再開 | スケール | データベース状態の確認 |
| ------------ | ------------ | ----- | -------------------- |
| Azure ポータル | はい          | はい   | **いいえ**               |
| PowerShell   | はい          | あり   | はい                  |
| REST API     | はい          | あり   | はい                  |
| T-SQL        | **いいえ**       | はい   | はい                  |



<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>コンピューティングのスケーリング

SQL Data Warehouse のパフォーマンスは、CPU、メモリ、I/O 帯域幅などのコンピューティング リソースの抽象的な尺度である [Data Warehouse ユニット (DWU)][data warehouse units (DWUs)] で測定されます。 システムのパフォーマンスをスケーリングする必要のあるユーザーは、ポータル、T-SQL、REST API などのさまざまな手段を使用してパフォーマンスを向上させることができます。 

### <a name="how-do-i-scale-compute"></a>コンピューティングをスケーリングする方法
SQL Data Warehouse のコンピューティング能力を管理するには、DWU 設定を変更します。 パフォーマンスは、特定の操作のために DWU を追加するのに応じて[線形][linearly]に向上します。  Microsoft では、システムをスケールアップまたはスケールダウンしたときにパフォーマンスが顕著に変化するような DWU プランを提供しています。 

DWU を調整するのには、これらの各方法をどれでも使用できます。

* [Azure Portal を使用したコンピューティング能力のスケーリング][Scale compute power with Azure portal]
* [PowerShell を使用したコンピューティング能力のスケーリング][Scale compute power with PowerShell]
* [REST API を使用したコンピューティング能力のスケーリング][Scale compute power with REST APIs]
* [TSQL を使用したコンピューティング能力のスケーリング][Scale compute power with TSQL]

### <a name="how-many-dwus-should-i-use"></a>必要な DWU の数

理想的な DWU 値を把握するには、データ読み込みの後で、拡大/縮小を行い、いくつかのクエリを実行してください。 スケーリングは簡単に行えるので、1 時間以内でさまざまなパフォーマンス レベルを試すことができます。 

> [!Note] 
> SQL Data Warehouse は、大量のデータを処理するように設計されています。 特に大規模な DWU での実際のスケーリング能力を確認する場合は、サイズが 1 TB に近いか 1 TB を超える大きなデータ セットを使用できます。

ワークロードに最適な DWU を特定する際の推奨事項:

1. 開発中のデータ ウェアハウスの場合は、DWU が小さなパフォーマンス レベルを選択することから始めます。  手始めとしては、DW400 または DW200 が適しています。
2. アプリケーションのパフォーマンスを監視し、選択した DWU の数に対するパフォーマンスの変化を観察します。
3. 線形スケールを想定して、要件に対して最適なパフォーマンス レベルに到達するために、パフォーマンスをどの程度上下する必要があるかを判別します。
4. どのくらい速くまたは遅くワークロードを実行するかに応じて、DWU の数を増減します。 
5. ビジネス要件に応じた最適なパフォーマンス レベルに到達するまで調整を行います。

> [!NOTE]
>
> コンピューティング ノード間で作業を分割することができる場合、クエリのパフォーマンスを引き上げるには、並列処理を増やすしかありません。 スケーリングしてもパフォーマンスが変わらない場合は、パフォーマンス チューニングに関する記事を参照して、データの分散が不均等でないか、または大量のデータ移動が行われていないかを確認してください。 

### <a name="when-should-i-scale-dwus"></a>DWU をスケーリングするタイミング
DWU のスケーリングに伴い、次の重要なシナリオに変化が生じます。

1. スキャン、集計、CTAS ステートメントのためのシステムのパフォーマンスの線形の変化
2. PolyBase を使用して読み込むときのリーダーとライターの数の拡大
3. 同時クエリと同時実行スロットの最大数

DWU をスケーリングするタイミングを特定するための推奨事項:

1. 大量データの読み込みまたは変換操作を実行する前に、データが短時間で使用可能になるように DWU をスケールアップします。
2. 営業時間のピーク時は、より多くの同時実行クエリに対応できるようにスケーリングします。 

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>コンピューティングの一時停止
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

データベースを一時停止するには、次のいずれかの方法を使用できます。

* [Azure Portal を使用したコンピューティングの一時停止][Pause compute with Azure portal]
* [PowerShell を使用したコンピューティングの一時停止][Pause compute with PowerShell]
* [REST API を使用したコンピューティングの一時停止][Pause compute with REST APIs]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>コンピューティングの再開
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

データベースを再開するには、次のいずれかの方法を使用できます。

* [Azure Portal を使用したコンピューティングの再開][Resume compute with Azure portal]
* [PowerShell を使用したコンピューティングの再開][Resume compute with PowerShell]
* [REST API を使用したコンピューティングの再開][Resume compute with REST APIs]

<a name="check-compute-bk"></a>

## <a name="check-database-state"></a>データベース状態の確認 

データベースを再開するには、次のいずれかの方法を使用できます。

- [T-SQL を使用したデータベース状態の確認][Check database state with T-SQL]
- [PowerShell を使用したデータベース状態の確認][Check database state with PowerShell]
- [REST API を使用したデータベース状態の確認][Check database state with REST APIs]

## <a name="permissions"></a>アクセス許可

データベースをスケーリングするには、[ALTER DATABASE][ALTER DATABASE] に関するページで説明されているアクセス許可が必要です。  一時停止と再開には、[SQL DB の共同作成者][SQL DB Contributor]のアクセス許可、具体的には Microsoft.Sql/servers/databases/action が必要です。

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>次のステップ
その他の主要なパフォーマンスの概念を理解するために、次の記事を参照してください。

* [ワークロードと同時実行の管理][ワークロードと同時実行の管理]
* [テーブル設計の概要][Table design overview]
* [テーブルの分散][Table distribution]
* [テーブルのインデックス作成][Table indexing]
* [テーブルのパーティション分割][Table partitioning]
* [テーブルの統計][Table statistics]
* [ベスト プラクティス][Best practices]

<!--Image reference-->

<!--Article references-->
[data warehouse units (DWUs)]: ./sql-data-warehouse-overview-what-is.md#predictable-and-scalable-performance-with-data-warehouse-units
[billed]: https://azure.microsoft.com/en-us/pricing/details/sql-data-warehouse/
[linearly]: ./sql-data-warehouse-overview-what-is.md#predictable-and-scalable-performance-with-data-warehouse-units
[Scale compute power with Azure portal]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-power
[Scale compute power with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[Scale compute power with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[Scale compute power with TSQL]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md

[Pause compute with Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md#pause-compute-bk
[Pause compute with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[Pause compute with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[Resume compute with Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md#resume-compute-bk
[Resume compute with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[Resume compute with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

[Check database state with T-SQL]: ./sql-data-warehouse-manage-compute-tsql.md#check-database-state-and-operation-progress
[Check database state with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#check-database-state
[Check database state with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#check-database-state

[Workload and concurrency management]: ./sql-data-warehouse-develop-concurrency.md
[Table design overview]: ./sql-data-warehouse-tables-overview.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexing]: ./sql-data-warehouse-tables-index.md
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Table statistics]: ./sql-data-warehouse-tables-statistics.md
[Best practices]: ./sql-data-warehouse-best-practices.md
[development overview]: ./sql-data-warehouse-overview-develop.md

[SQL DB Contributor]: ../active-directory/role-based-access-built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/


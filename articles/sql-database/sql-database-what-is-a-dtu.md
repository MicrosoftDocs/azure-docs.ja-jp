---
title: "SQL Database: DTU とは  | Microsoft Docs"
description: "Azure SQL Database トランザクション ユニットについて説明します。"
keywords: "データベース オプション, データベース パフォーマンス"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: CarlRabeler
ms.assetid: 89e3e9ce-2eeb-4949-b40f-6fc3bf520538
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 8b22b5ac29e855f3074cab01042057f214e9e9b7


---
# <a name="explaining-database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>データベース トランザクション ユニット (DTU) とエラスティック データベース トランザクション ユニット (eDTU) の説明
この記事では、データベース トランザクション ユニット (DTU) とエラスティック データベース トランザクション ユニット (eDTU) について説明します。また、DTU または eDTU が最大数に達した場合に何が起こるかについても解説します。  

## <a name="what-are-database-transaction-units-dtus"></a>データベース トランザクション ユニット (DTU) とは
DTU とは、単一の Azure SQL データベースにおいて、[単一データベースのサービス レベル](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels)の範囲内で一定のパフォーマンス レベルで利用できることが保証されているリソースの最小単位です。 DTU は、CPU、メモリ、データの I/O、トランザクション ログの I/O の&4; つの要素を、ベンチマークとなる OLTP ワークロードを使って導き出した一定の割合で組み合わせた指標です (このとき使用する OLTP ワークロードは、現実世界で発生する典型的な負荷になるように設計されています)。 データベースのパフォーマンス レベルを上げて DTU を&2; 倍にすると、そのデータベースが利用できるリソースを&2; 倍にしたのと同じ効果が得られます。 たとえば、DTU が 1750 である Premium P11 データベースは、DTU が 5 である Basic データベースと比べ、DTU 換算で 350 倍の計算能力を持ちます。 DTU の各構成要素の割合を決める際に使用する OLTP ワークロードの裏で具体的にどのような方法論が採用されているかについては、「 [Azure SQL Database ベンチマークの概要](sql-database-benchmark-overview.md)」を参照してください。

![Intro to SQL Database: Single database DTUs by tier and level](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

[サービス レベル](sql-database-service-tiers.md) はいつでも変更することが可能で、アプリケーションのダウンタイムも最小限に留められます (通常、平均で&4; 秒未満)。 特に使用パターンが比較的予測可能である場合、多くのビジネスとアプリについては、データベースを作成し、要求に応じてパフォーマンスを調整する能力は十分です。 しかし、使用パターンが予測できない場合、コストおよびビジネス モデルを管理するのが難しくなる可能性があります。 そのような場合には、一定数の eDTU を備えたエラスティック プールを使用します。

## <a name="what-are-elastic-database-transaction-units-edtus"></a>エラスティック データベース トランザクション ユニット (eDTU) とは
eDTU とは、1 つの Azure SQL サーバー ( [エラスティック プール](sql-database-elastic-pool.md)) に存在するいくつかのデータベースが共有できるリソース (DTU) を&1; つにまとめた単位です。 エラスティック プールは、多種多様な予測できない使用パターンを持つ複数のデータベースに対するパフォーマンス目標を管理するための、簡単でコスト効率に優れたソリューションを提供します。 詳細については、 [エラスティック プールとサービス レベル](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) に関する記事を参照してください。

![Intro to SQL Database: eDTUs by tier and level](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

プールには、設定価格に合わせて、設定された eDTU 数が与えられます。 エラスティック プール内の個々のデータベースには、構成された境界内で自動スケールを行う柔軟性が与えられます。 負荷が大きい場合、データベースは需要に対応するためにより多くの eDTU を使用し、負荷の少ないデータベースでは使用を抑え、負荷がまったくないデータベースでは eDTU を使用しないようにすることができます。 データベースごとではなく、プール全体のリソースをプロビジョニングすることにより、管理タスクが簡素化され、プールの予算が予測可能になります。

データベースを停止せず、プール内のデータベースに影響を及ぼさずに、既存のプールに eDTU を追加できます。 同様に、不要になった eDTU は、いつでも既存のプールから削除できます。 プールのデータベースを増減できるほか、負荷が大きいときにデータベースが使用できる eDTU の量を制限して、他のデータベース用の eDTU を確保することもできます。 データベースのリソース使用率が低いと予測できる場合は、プールから削除して、必要と予想される量のリソースを備えた単一データベースとして構成できます。

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>ワークロードで必要とされる DTU の数を判断する方法
既に存在するオンプレミスのワークロードや、SQL Server 仮想マシンのワークロードを Azure SQL Database に移行することを検討している場合には、[DTU Calculator](http://dtucalculator.azurewebsites.net/) を使用すると、必要な DTU のおおよその数がわかります。 既存の Azure SQL Database ワークロードについては、[SQL Database Query Performance Insight](sql-database-query-performance.md) を使えばデータベース リソースの消費量 (DTU) を把握でき、ワークロードの最適化に関する深い洞察が得られます。 このほか、過去&1; 時間のリソース消費量を確認する場合には、[sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV も使用できます。 また、カタログ ビュー [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) を対象にクエリを実行する方法でも、過去 14 日間のリソース消費量を確認できます。ただし、こちらはデータの精度がやや低く、5 分間の平均となります。

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>リソースのエラスティック プールを使うメリット
プールは、多数のデータベースが一定のパターンで使用されている場合に適しています。 あるデータベースは、使用が急増することはあまりなく、平均使用量が低いパターンの特徴を持っています。 SQL Database は、既存の SQL Database サーバー内にあるデータベースのリソース使用量の履歴を自動的に評価し、Azure ポータルでのプールの適切な構成を推奨します。 詳細については、「[エラスティック プールの使用に適した状況](sql-database-elastic-pool-guidance.md)」を参照してください。

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>DTU が最大数に達した場合に起こること
選択したサービス レベル/パフォーマンス レベルで許可されている最大限度までデータベース ワークロードを実行するため、必要なリソースを提供できるようにパフォーマンス レベルが調整、制御されます。 ワークロードが CPU/データ IO/ログ IO のいずれかの上限に達した場合、許可される最大レベルでリソースを引き続き受け取りますが、クエリの待ち時間が長くなる場合があります。 上限に達してもエラーにはなりませんが、ワークロードが遅くなり、遅延が深刻になった場合はクエリのタイムアウトが発生するようになります。 許可される同時ユーザー セッション/要求 (ワーカー スレッド) の上限に達した場合は、明示的なエラーが発生します。 CPU、メモリ、データの I/O、トランザクション ログの I/O 以外のリソースに対する制限については、「 [Azure SQL Database のリソース制限](sql-database-resource-limits.md) 」を参照してください。

## <a name="next-steps"></a>次のステップ
* 単一データベースとエラスティック プールで利用できる DTU と eDTU については、[サービス レベル](sql-database-service-tiers.md)に関するページを参照してください。
* CPU、メモリ、データの I/O、トランザクション ログの I/O 以外のリソースに対する制限については、「 [Azure SQL Database のリソース制限](sql-database-resource-limits.md) 」を参照してください。
* (DTU の) 消費量を確認する場合には、「 [SQL Database Query Performance Insight](sql-database-query-performance.md) 」を参照してください。
* DTU の各構成要素の割合を決める際に使用する OLTP ワークロードの裏で具体的にどのような方法論が採用されているかについては、「 [Azure SQL Database ベンチマークの概要](sql-database-benchmark-overview.md) 」を参照してください。



<!--HONumber=Feb17_HO3-->



---
title: エラスティック プールのリソースをスケーリングする - Azure SQL Database | Microsoft Docs
description: このページでは、Azure SQL Database のエラスティック プールのリソースをスケーリングする方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 3/14/2019
ms.openlocfilehash: f73fc58abfa6cde4133bd56858b7f26bf0c3d4a3
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204812"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Azure SQL Database でエラスティック プールのリソースをスケーリングする

この記事では、Azure SQL Database でエラスティック プールとプールされたデータベースに使用できるコンピューティング リソースとストレージ リソースをスケーリングする方法について説明します。

## <a name="change-compute-resources-vcores-or-dtus"></a>コンピューティング リソース (仮想コアまたは DTU) の変更

仮想コアまたは eDTU の数を最初に選択した後は、[Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases)、[PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool)、[Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)、または [REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update) を使い、実際の状況に基づいて、エラスティック プールを動的にスケールアップまたはスケールダウンできます。

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>サービス レベルの変更またはコンピューティング サイズの再スケーリングの影響

エラスティック プールのサービス レベルとコンピューティング サイズの変更は、単一データベースと同様のパターンに従い、主に次の手順を実行するサービスが含まれます。

1. エラスティック プール用に新しいコンピューティング インスタンスを作成する  

    要求されたサービス レベルとコンピューティング サイズを持つ新しいコンピューティング インスタンスがエラスティック プール用に作成されます。 サービス レベルとコンピューティング サイズの一部の組み合わせの変更では、新しいコンピューティング インスタンスに各データベースのレプリカを作成する必要があります。これにはデータをコピーすることが含まれ、全体の待機時間に大きく影響する場合があります。 いずれにしても、この手順の間、データベースはオンラインのままで、接続は元のコンピューティング インスタンスのデータベースに引き続き向けられます。

2. 接続のルーティングを新しいコンピューティング インスタンスに切り替える

    元のコンピューティング インスタンス内のデータベースへの既存の接続が削除されます。 新しいコンピューティング インスタンス内のデータベースへの新しい接続が確立されます。 サービス レベルとコンピューティング サイズの一部の組み合わせの変更では、データベース ファイルがデタッチされ、切り替え時に再アタッチされます。  いずれにしても、切り替えにより、データベースが使用できなくなる短時間の中断が発生する場合があります。中断は通常は 30 秒未満で、多くの場合はほんの数秒です。 接続が削除されたときに長時間実行されているトランザクションがあると、中断されたトランザクションを復旧するために、この手順の所要時間が長くなる場合があります。 [高速データベースの復旧](sql-database-accelerated-database-recovery.md)により、長時間実行されているトランザクションの中断による影響を軽減できます。

> [!IMPORTANT]
> ワークフローのいずれの手順でもデータが失われることはありません。

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>サービス レベルの変更またはコンピューティング サイズの再スケーリングの待機時間

単一データベースまたはエラスティック プールのサービス レベルを変更またはコンピューティング サイズを変更する推定待機時間は、次のようにパラメーター化されます。

|サービス階層|Basic 単一データベース、</br>Standard (S0-S1)|Basic エラスティック プール、</br>Standard (S2-S12)、 </br>ハイパースケール、 </br>汎用の単一データベースまたはエラスティック プール|Premium または Business Critical の単一データベースまたはエラスティック プール|
|:---|:---|:---|:---|
|**Basic 単一データベース、</br>Standard (S0-S1)**|&bull; &nbsp;使用される領域とは関係ない一定時間の待機時間</br>&bull; &nbsp;通常は 5 分未満|&bull; &nbsp;データのコピーのために使用されるデータベース領域に比例した待機時間</br>&bull; &nbsp;通常、使用される領域の GB あたり 1 分未満|&bull; &nbsp;データのコピーのために使用されるデータベース領域に比例した待機時間</br>&bull; &nbsp;通常、使用される領域の GB あたり 1 分未満|
|**Basic エラスティック プール、</br>Standard (S2-S12)、</br>ハイパースケール、</br>汎用の単一データベースまたはエラスティック プール**|&bull; &nbsp;データのコピーのために使用されるデータベース領域に比例した待機時間</br>&bull; &nbsp;通常、使用される領域の GB あたり 1 分未満|&bull; &nbsp;使用される領域とは関係ない一定時間の待機時間</br>&bull; &nbsp;通常は 5 分未満|&bull; &nbsp;データのコピーのために使用されるデータベース領域に比例した待機時間</br>&bull; &nbsp;通常、使用される領域の GB あたり 1 分未満|
|**Premium または Business Critical の単一データベースまたはエラスティック プール**|&bull; &nbsp;データのコピーのために使用されるデータベース領域に比例した待機時間</br>&bull; &nbsp;通常、使用される領域の GB あたり 1 分未満|&bull; &nbsp;データのコピーのために使用されるデータベース領域に比例した待機時間</br>&bull; &nbsp;通常、使用される領域の GB あたり 1 分未満|&bull; &nbsp;データのコピーのために使用されるデータベース領域に比例した待機時間</br>&bull; &nbsp;通常、使用される領域の GB あたり 1 分未満|

> [!NOTE]
>
> - サービス レベルを変更する場合、またはエラスティック プールのコンピューティングを再度スケーリングする場合は、そのプール内のすべてのデータベース全体で使用される領域の合計を、推定値の計算に使用する必要があります。
> - エラスティック プールとの間でデータベースを移動する場合、エラスティック プールで使用される領域ではなく、データベースの使用領域のみが待機時間に影響します。
>
> [!TIP]
> 実行中の操作の監視については、[SQL REST API を使った操作の管理](https://docs.microsoft.com/rest/api/sql/operations/list)、[CLI を使った操作の管理](/cli/azure/sql/db/op)、[T-SQL を使った操作の管理](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)に関する各ページと、2 つの PowerShell コマンド [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) と [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity)。

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>サービス レベルを変更またはコンピューティング サイズを再スケーリングする場合の追加の考慮事項

- エラスティック プールの仮想コアまたは eDTU をダウンサイズするときは、プールで使われている領域が、ターゲットのサービス レベルとパフォーマンス レベルで許可されている最大サイズより小さい必要があります。
- エラスティック プールの仮想コアまたは eDTU を再スケーリングするとき、(1) プールの最大ストレージ サイズがターゲット プールでサポートされていて、かつ、(2) 最大ストレージ サイズがターゲット プールに付属するストレージ容量を超える場合、追加のストレージ コストが適用されます。 たとえば、最大サイズが 100 GB の 100 eDTU のStandard プールを、50 eDTU の Standard プールにダウンサイズすると、ターゲット プールは最大サイズの 100 GB をサポートしますが、付属するストレージ量は 50 GB だけなので、追加ストレージ コストがかかります。 したがって、追加ストレージ容量は 100 GB – 50 GB = 50 GB になります。 追加ストレージの価格については、「[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。 実際に使われる領域の量が付属のストレージの量より少ない場合、データベースの最大サイズを付属の量に減らすことで、この追加コストを回避できます。

### <a name="billing-during-rescaling"></a>再スケーリング時の課金

使用状況やデータベースがアクティブであったのが 1 時間未満であったことに関係なく、データベースが存在していた 1 時間単位で、その時間に使用された最上位のサービス階層とコンピューティング サイズで課金は行われます。 たとえば、Single Database を作成し、それを 5 分後に削除した場合、請求書にはデータベース時間として 1 時間の請求が表示されます。

## <a name="change-elastic-pool-storage-size"></a>エラスティック プールのストレージ サイズの変更

> [!IMPORTANT]
> 場合によっては、未使用領域を再利用できるようにデータベースを縮小する必要があります。 詳細については、「[Manage file space in Azure SQL Database](sql-database-file-space-management.md)」(Azure SQL Database でファイル領域を管理する) を参照してください。

### <a name="vcore-based-purchasing-model"></a>仮想コアベースの購入モデル

- 最大サイズの上限に達するまでストレージをプロビジョニングすることができます。

  - 標準または汎用サービス レベルのストレージの場合は、10 GB 単位でサイズを増減します
  - Premium または Business Critical レベルのストレージの場合は、250 GB 単位でサイズを増減します
- エラスティック プールのストレージは、最大サイズを増減することでプロビジョニングできます。
- エラスティック プールのストレージの料金は、ストレージ量にサービス レベルのストレージ単価を掛けて計算します。 追加ストレージの価格について詳しくは、「[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。

> [!IMPORTANT]
> 場合によっては、未使用領域を再利用できるようにデータベースを縮小する必要があります。 詳細については、「[Manage file space in Azure SQL Database](sql-database-file-space-management.md)」(Azure SQL Database でファイル領域を管理する) を参照してください。

### <a name="dtu-based-purchasing-model"></a>DTU ベースの購入モデル

- エラスティック プールの eDTU 価格には、追加コストなしで一定量のストレージが含まれます。 付属の容量を超える分のストレージについては、追加費用を払うことで、1 TB までは 250 GB 単位で、1 TB 以降は 256 GB 単位で、最大サイズ制限までプロビジョニングできます。 付属するストレージの量と最大サイズ制限については、「[エラスティック プール: ストレージ サイズとコンピューティング サイズ](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)」をご覧ください。
- エラスティック プールの追加ストレージは、[Azure Portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases)、[PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool)、[Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)、または [REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update) を使ってサイズを最大に増やすことでプロビジョニングできます。
- エラスティック プールの追加ストレージの料金は、追加ストレージ容量にサービス レベルの追加ストレージ単価を掛けて計算します。 追加ストレージの価格について詳しくは、「[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。

> [!IMPORTANT]
> 場合によっては、未使用領域を再利用できるようにデータベースを縮小する必要があります。 詳細については、「[Manage file space in Azure SQL Database](sql-database-file-space-management.md)」(Azure SQL Database でファイル領域を管理する) を参照してください。

## <a name="next-steps"></a>次の手順

全体的なリソースの制限については、[SQL Database の仮想コアペースのリソース制限 - エラスティック プール](sql-database-vcore-resource-limits-elastic-pools.md)および [SQL Database の DTU ベースのリソース制限 - エラスティック プール](sql-database-dtu-resource-limits-elastic-pools.md)に関するページを参照してください。

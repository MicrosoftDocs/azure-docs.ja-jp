---
title: 単一データベースのリソースをスケーリングする - Azure SQL Database | Microsoft Docs
description: この記事では、Azure SQL Database で単一データベースに使用できるコンピューティング リソースとストレージ リソースをスケーリングする方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 04/26/2019
ms.openlocfilehash: 311015aff5ea7020043ad8e43fd987144cdcbf52
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206750"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Azure SQL Database で単一データベースのリソースをスケーリングする

この記事では、プロビジョニングされたコンピューター レベルで単一データベースに使用できるコンピューティング リソースとストレージ リソースをスケーリングする方法について説明します。 あるいは、[サーバーレス (プレビュー) コンピューティング レベル](sql-database-serverless.md)は、コンピューティングの自動スケーリングと、使用されたコンピューティングの 1 秒あたりの請求額を提供します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

## <a name="change-compute-size-vcores-or-dtus"></a>コンピューティング サイズ (仮想コアまたは DTU) の変更

最初に仮想コア数または DTU 数を選択すると、[Azure portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server)、[Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1)、[PowerShell](/powershell/module/az.sql/set-azsqldatabase)、[Azure CLI](/cli/azure/sql/db#az-sql-db-update)、または [REST API](https://docs.microsoft.com/rest/api/sql/databases/update) を使い、実際のエクスペリエンスに基づいて、単一データベースを動的にスケールアップまたはスケールダウンできます。

次のビデオでは、サービス レベルとコンピューティング サイズを動的に変更して単一データベースで使用可能な DTU を増やす方法を示します。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

> [!IMPORTANT]
> 場合によっては、未使用領域を再利用できるようにデータベースを縮小する必要があります。 詳細については、「[Manage file space in Azure SQL Database](sql-database-file-space-management.md)」(Azure SQL Database でファイル領域を管理する) を参照してください。

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>サービス レベルの変更またはコンピューティング サイズの再スケーリングの影響

単一データベースのサービス レベルまたはコンピューティング サイズの変更には、主に次の手順を実行するサービスが含まれます。

1. データベース用に新しいコンピューティング インスタンスを作成する  

    要求されたサービス レベルとコンピューティング サイズを持つ新しいコンピューティング インスタンスがデータベース用に作成されます。 サービス レベルとコンピューティング サイズの変更の組み合わせの中には、新しいコンピューティング インスタンスにデータベースのレプリカを作成する必要があるものがあります。これにはデータのコピーも含まれ、全体の待機時間に大きく影響する場合があります。 いずれにしても、この手順の間、データベースはオンラインのままで、接続は元のコンピューティング インスタンスのデータベースに引き続き向けられます。

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

> [!TIP]
> 実行中の操作の監視については、[SQL REST API を使った操作の管理](https://docs.microsoft.com/rest/api/sql/operations/list)、[CLI を使った操作の管理](/cli/azure/sql/db/op)、[T-SQL を使った操作の管理](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)に関する各ページと、2 つの PowerShell コマンド [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) と [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity)。

### <a name="cancelling-service-tier-changes-or-compute-rescaling-operations"></a>サービス レベルの変更またはコンピューティングの再スケーリング操作の取り消し

サービス レベルの変更またはコンピューティングの再スケーリング操作を取り消すことができます。

#### <a name="azure-portal"></a>Azure ポータル

[データベースの概要] ブレードで、 **[通知]** に移動し、進行中の操作があることを示すタイルをクリックします。

![進行中の操作](media/sql-database-single-database-scale/ongoing-operations.png)

次に、 **[この操作を取り消す]** というラベルのボタンをクリックします。

![進行中の操作の取り消し](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

PowerShell コマンド プロンプトで `$ResourceGroupName`、`$ServerName`、および `$DatabaseName` を設定した後、次のコマンドを実行します。

```PowerShell
$OperationName = (az sql db op list --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --query "[?state=='InProgress'].name" --out tsv)
if(-not [string]::IsNullOrEmpty($OperationName))
    {
        (az sql db op cancel --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --name $OperationName)
        "Operation " + $OperationName + " has been canceled"
    }
    else
    {
        "No service tier change or compute rescaling operation found"
    }
```

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>サービス レベルを変更またはコンピューティング サイズを再スケーリングする場合の追加の考慮事項

- より上位のサービス レベルまたはコンピューティング サイズにアップグレードしても、より大きなサイズ (最大サイズ) を明示的に指定しない限り、データベースの最大サイズは増加しません。
- データベースをダウングレードするには、データベースで使われている領域がダウングレード後のサービス レベルとコンピューティング サイズで許可されている最大サイズより小さい必要があります。
- **Premium** から **Standard** レベルにダウングレードするときは、(1) データベースの最大サイズがターゲットのコンピューティング サイズでサポートされていて、かつ、(2) 最大サイズがターゲットのコンピューティング サイズで付属のストレージ容量を超えている場合、追加ストレージ コストが適用されます。 たとえば、最大サイズ 500 GB の P1 データベースを S3 にダウンサイズする場合、S3 がサポートする最大サイズは 500 GB であり、S3 で付属のストレージ容量は 250 GB だけなので、追加ストレージ コストが適用されます。 したがって、追加ストレージ容量は 500 GB – 250 GB = 250 GB になります。 追加ストレージの価格については、「[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。 実際に使われる領域の量が付属のストレージの量より少ない場合、データベースの最大サイズを付属の量に減らすことで、この追加コストを回避できます。
- [geo レプリケーション](sql-database-geo-replication-portal.md)が有効な状態でデータベースをアップグレードする場合、そのセカンダリ データベースを目的のサービス レベルとコンピューティング サイズにアップグレードしてから、プライマリ データベースをアップグレードします (パフォーマンスを最大にするための一般的なガイダンス)。 別のエディションにアップグレードする場合は、最初にセカンダリ データベースのアップグレードが必要です。
- [geo レプリケーション](sql-database-geo-replication-portal.md)が有効な状態でデータベースをダウングレードする場合、そのプライマリ データベースを目的のサービス レベルとコンピューティング サイズにダウングレードしてから、セカンダリ データベースをダウングレードします (パフォーマンスを最大にするための一般的なガイダンス)。 別のエディションにダウングレードする場合は、最初にプライマリ データベースのダウングレードが必要です。
- サービス階層によって、提供されている復元サービスは異なります。 **Basic** レベルにダウングレードする場合は、バックアップのリテンション期間が短くなります。 [Azure SQL Database のバックアップ](sql-database-automated-backups.md)に関する記事をご覧ください。
- データベースに対する新しいプロパティは、変更が完了するまで適用されません。

### <a name="billing-during-compute-rescaling"></a>コンピューティングの再スケーリング時の課金

使用状況やデータベースがアクティブであったのが 1 時間未満であったことに関係なく、データベースが存在していた 1 時間単位で、その時間に使用された最上位のサービス階層とコンピューティング サイズで課金は行われます。 たとえば、Single Database を作成し、それを 5 分後に削除した場合、請求書にはデータベース時間として 1 時間の請求が表示されます。

## <a name="change-storage-size"></a>ストレージ サイズの変更

### <a name="vcore-based-purchasing-model"></a>仮想コアベースの購入モデル

- 1 GB の増分を使用して最大サイズの上限に達するまでストレージをプロビジョニングすることができます。 構成可能な最小データ ストレージは 5 GB です
- 単一データベースのストレージは、[Azure Portal](https://portal.azure.com)、[Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1)、[PowerShell](/powershell/module/az.sql/set-azsqldatabase)、[Azure CLI](/cli/azure/sql/db#az-sql-db-update)、または [REST API](https://docs.microsoft.com/rest/api/sql/databases/update) を使って最大サイズを増減することでプロビジョニングできます。
- SQL Database は自動的に追加ストレージの 30% をログ ファイルに割り当て、TempDB の仮想コアごとに 32 GB を割り当てますが、384 GB を超えることはありません。 TempDB は、すべてのサービス レベルの接続されている SSD にあります。
- 単一データベースのストレージの料金は、データ ストレージとログ ストレージの容量の合計にサービス レベルのストレージ単価を掛けて計算します。 TempDB のコストは仮想コア価格に含まれています。 追加ストレージの価格について詳しくは、「[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。

> [!IMPORTANT]
> 場合によっては、未使用領域を再利用できるようにデータベースを縮小する必要があります。 詳細については、「[Manage file space in Azure SQL Database](sql-database-file-space-management.md)」(Azure SQL Database でファイル領域を管理する) を参照してください。

### <a name="dtu-based-purchasing-model"></a>DTU ベースの購入モデル

- 単一データベースの DTU 価格には、追加コストなしで一定量のストレージが含まれます。 付属の容量を超える分のストレージについては、追加費用を払うことで、1 TB までは 250 GB 単位で、1 TB 以降は 256 GB 単位で、最大サイズ制限までプロビジョニングできます。 付属するストレージ容量と最大サイズ制限については、「[単一データベース: ストレージ サイズとコンピューティング サイズ](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)」をご覧ください。
- 単一データベースの追加ストレージは、Azure portal、[Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1)、[PowerShell](/powershell/module/az.sql/set-azsqldatabase)、[Azure CLI](/cli/azure/sql/db#az-sql-db-update)、または [REST API](https://docs.microsoft.com/rest/api/sql/databases/update) を使ってサイズを最大に増やすことでプロビジョニングできます。
- 単一データベースの追加ストレージの料金は、追加ストレージ容量にサービス レベルの追加ストレージ単価を掛けて計算します。 追加ストレージの価格について詳しくは、「[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。

> [!IMPORTANT]
> 場合によっては、未使用領域を再利用できるようにデータベースを縮小する必要があります。 詳細については、「[Manage file space in Azure SQL Database](sql-database-file-space-management.md)」(Azure SQL Database でファイル領域を管理する) を参照してください。

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>最大サイズが 1 TB を超える場合の P11 と P15 の制約

現在、1 TB を超える Premium レベルのストレージは、中国東部、中国北部、ドイツ中部、ドイツ北東部、米国中西部、米国 DoD の各リージョンと、米国政府中部を除くすべてのリージョンで利用できます。 これらのリージョンでは、Premium レベルのストレージの最大容量は 1 TB です。 最大サイズが 1 TB を超える P11 および P15 データベースには、次の考慮事項と制限事項が適用されます。

- P11 または P15 のデータベースの最大サイズが 1 TB を超える値に設定されていた場合、P11 または P15 のデータベースにしか復元またはコピーできません。  その後、再スケーリング操作の時に割り当てられた領域の量が新しいコンピューティング サイズの最大サイズ制限を超えなければ、データベースを異なるコンピューティング サイズに再スケーリングすることができます。
- アクティブ geo レプリケーションのシナリオの場合:
  - geo レプリケーションのリレーションシップの設定:プライマリ データベースが P11 または P15 の場合は、セカンダリも P11 または P15 である必要があります。つまり、下位のコンピューティング サイズは、1 TB を超えるサイズをサポートできないため、セカンダリとして拒否されます。
  - geo レプリケーションのリレーションシップでのプライマリ データベースのアップグレード:プライマリ データベースで最大サイズを 1 TB 超に変更すると、セカンダリ データベースでも同じ変更がトリガーされます。 プライマリに対する変更を有効にするには、両方のアップグレードが正常に完了する必要があります。 1 TB を超えるオプションに関するリージョンの制限が適用されます。 1 TB 超をサポートしていないリージョンにセカンダリが存在する場合、プライマリはアップグレードされません。
- 1 TB を超える P11/P15 データベースの読み込みに Import/Export サービスを使うことはサポートされていません。 SqlPackage.exe を使用して、データを[インポート](sql-database-import.md)および[エクスポート](sql-database-export.md)してください。

## <a name="next-steps"></a>次の手順

全体的なリソースの制限については、[SQL Database の仮想コアペースのリソース制限 - 単一データベース](sql-database-vcore-resource-limits-single-databases.md)および [SQL Database の DTU ベースのリソース制限 - エラスティック プール](sql-database-dtu-resource-limits-single-databases.md)に関するページを参照してください。

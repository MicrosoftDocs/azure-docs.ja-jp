---
title: 単一データベースのリソースをスケーリングする
description: この記事では、Azure SQL Database で単一データベースに使用できるコンピューティング リソースとストレージ リソースをスケーリングする方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1, references_regions
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 02/22/2021
ms.openlocfilehash: ce8d4bf36524e3e7e7b3b8c974aa189fa000d845
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773251"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Azure SQL Database で単一データベースのリソースをスケーリングする

この記事では、プロビジョニングされたコンピューター レベルで Azure SQL Database 用に使用できるコンピューティング リソースとストレージ リソースをスケーリングする方法について説明します。 また、[サーバーレス コンピューティング レベル](serverless-tier-overview.md)は、コンピューティングの自動スケーリングと、使用されたコンピューティングの 1 秒あたりの請求額を提供します。

最初に仮想コア数または DTU 数を選択すると、[Azure portal](single-database-manage.md#the-azure-portal)、[Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#examples-1)、[PowerShell](/powershell/module/az.sql/set-azsqldatabase)、[Azure CLI](/cli/azure/sql/db#az-sql-db-update)、または [REST API](/rest/api/sql/databases/update) を使い、実際のエクスペリエンスに基づいて、単一データベースを動的にスケールアップまたはスケールダウンできます。

次のビデオでは、サービス レベルとコンピューティング サイズを動的に変更して単一データベースで使用可能な DTU を増やす方法を示します。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> 場合によっては、未使用領域を再利用できるようにデータベースを縮小する必要があります。 詳細については、「[Manage file space in Azure SQL Database](file-space-manage.md)」(Azure SQL Database でファイル領域を管理する) を参照してください。

## <a name="impact"></a>影響

サービス レベルまたはコンピューティング サイズの変更には、主に次の手順を実行するサービスが含まれます。

1. データベース用に新しいコンピューティング インスタンスを作成する 

    要求されたサービス レベルとコンピューティング サイズを持つ新しいコンピューティング インスタンスが作成されます。 サービス レベルとコンピューティング サイズの変更の組み合わせの中には、新しいコンピューティング インスタンスにデータベースのレプリカを作成する必要があるものがあります。これにはデータのコピーも含まれ、全体の待機時間に大きく影響する場合があります。 いずれにしても、この手順の間、データベースはオンラインのままで、接続は元のコンピューティング インスタンスのデータベースに引き続き向けられます。

2. 接続のルーティングを新しいコンピューティング インスタンスに切り替えます。

    元のコンピューティング インスタンス内のデータベースへの既存の接続が削除されます。 新しいコンピューティング インスタンス内のデータベースへの新しい接続が確立されます。 サービス レベルとコンピューティング サイズの一部の組み合わせの変更では、データベース ファイルがデタッチされ、切り替え時に再アタッチされます。  いずれにしても、切り替えにより、データベースが使用できなくなる短時間の中断が発生する場合があります。中断は通常は 30 秒未満で、多くの場合はほんの数秒です。 接続が削除されたときに長時間実行されているトランザクションがあると、切断されたトランザクションを復旧するために、この手順の所要時間が長くなる場合があります。 [高速データベースの復旧](../accelerated-database-recovery.md)により、長時間実行されているトランザクションの中断による影響を軽減できます。

> [!IMPORTANT]
> ワークフローのいずれの手順でもデータが失われることはありません。 サービス レベルの変更中に Azure SQL Database を使用するアプリケーションとコンポーネントに、何らかの[再試行ロジック](troubleshoot-common-connectivity-issues.md)を実装したことを確認してください。

## <a name="latency"></a>Latency

サービス レベルの変更、単一データベースまたはエラスティック プールのコンピューティング サイズのスケーリング、エラスティック プールとの間のデータベースの移動、またはエラスティック プール間でのデータベースの移動に伴う推定待ち時間は、次のようにパラメーター化されます。

|サービス階層|Basic 単一データベース、</br>Standard (S0-S1)|Basic エラスティック プール、</br>Standard (S2-S12)、 </br>汎用の単一データベースまたはエラスティック プール|Premium または Business Critical の単一データベースまたはエラスティック プール|ハイパースケール
|:---|:---|:---|:---|:---|
|**Basic 単一データベース、</br>Standard (S0-S1)**|&bull; &nbsp;使用される領域とは関係ない一定時間の待機時間</br>&bull; &nbsp;通常は 5 分未満|&bull; &nbsp;データのコピーのために使用されるデータベース領域に比例した待機時間</br>&bull; &nbsp;通常、使用される領域の GB あたり 1 分未満|&bull; &nbsp;データのコピーのために使用されるデータベース領域に比例した待機時間</br>&bull; &nbsp;通常、使用される領域の GB あたり 1 分未満|&bull; &nbsp;データのコピーのために使用されるデータベース領域に比例した待機時間</br>&bull; &nbsp;通常、使用される領域の GB あたり 1 分未満|
|**Basic エラスティック プール、</br>Standard (S2-S12)、</br>汎用の単一データベースまたはエラスティック プール**|&bull; &nbsp;データのコピーのために使用されるデータベース領域に比例した待機時間</br>&bull; &nbsp;通常、使用される領域の GB あたり 1 分未満|&bull; &nbsp;単一データベースの場合は、使用される領域とは関係ない一定時間の待機時間</br>&bull; &nbsp;単一データベースの場合は、通常 5 分未満</br>&bull; &nbsp;エラスティック プールの場合は、データベースの数に比例|&bull; &nbsp;データのコピーのために使用されるデータベース領域に比例した待機時間</br>&bull; &nbsp;通常、使用される領域の GB あたり 1 分未満|&bull; &nbsp;データのコピーのために使用されるデータベース領域に比例した待機時間</br>&bull; &nbsp;通常、使用される領域の GB あたり 1 分未満|
|**Premium または Business Critical の単一データベースまたはエラスティック プール**|&bull; &nbsp;データのコピーのために使用されるデータベース領域に比例した待機時間</br>&bull; &nbsp;通常、使用される領域の GB あたり 1 分未満|&bull; &nbsp;データのコピーのために使用されるデータベース領域に比例した待機時間</br>&bull; &nbsp;通常、使用される領域の GB あたり 1 分未満|&bull; &nbsp;データのコピーのために使用されるデータベース領域に比例した待機時間</br>&bull; &nbsp;通常、使用される領域の GB あたり 1 分未満|&bull; &nbsp;データのコピーのために使用されるデータベース領域に比例した待機時間</br>&bull; &nbsp;通常、使用される領域の GB あたり 1 分未満|
|**Hyperscale**|該当なし|N/A|該当なし|&bull; &nbsp;使用される領域とは関係ない一定時間の待機時間</br>&bull; &nbsp;通常は 2 分未満|

> [!NOTE]
> さらに、Standard (S2-S12) データベースと General Purpose データベースでは、データベースで Premium ファイル共有 ([PFS](../../storage/files/storage-files-introduction.md)) ストレージが使用されている場合、エラスティック プールとの間、またはエラスティック プール間でデータベースを移動するための待ち時間は、データベース サイズに比例します。
>
> データベースで PFS ストレージが使用されているかどうかを確認するには、データベースのコンテキストで次のクエリを実行します。 AccountType 列の値が `PremiumFileStorage` または `PremiumFileStorage-ZRS` の場合、データベースでは PFS ストレージが使用されています。
 
```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!TIP]
> 実行中の操作の監視については、[SQL REST API を使った操作の管理](/rest/api/sql/operations/list)、[CLI を使った操作の管理](/cli/azure/sql/db/op)、[T-SQL を使った操作の管理](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)に関する各ページと、2 つの PowerShell コマンド [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) と [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity)。

## <a name="cancelling-changes"></a>変更の取り消し

サービス レベルの変更またはコンピューティングの再スケーリング操作を取り消すことができます。

### <a name="the-azure-portal"></a>Azure ポータル

[データベースの概要] ブレードで、 **[通知]** に移動し、進行中の操作があることを示すタイルをクリックします。

![進行中の操作](./media/single-database-scale/ongoing-operations.png)

次に、 **[この操作を取り消す]** というラベルのボタンをクリックします。

![進行中の操作の取り消し](./media/single-database-scale/cancel-ongoing-operation.png)

### <a name="powershell"></a>PowerShell

PowerShell コマンド プロンプトで `$resourceGroupName`、`$serverName`、および `$databaseName` を設定した後、次のコマンドを実行します。

```azurecli
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

## <a name="additional-considerations"></a>その他の注意点

- より上位のサービス レベルまたはコンピューティング サイズにアップグレードしても、より大きなサイズ (最大サイズ) を明示的に指定しない限り、データベースの最大サイズは増加しません。
- データベースをダウングレードするには、データベースで使われている領域がダウングレード後のサービス レベルとコンピューティング サイズで許可されている最大サイズより小さい必要があります。
- **Premium** から **Standard** レベルにダウングレードするときは、(1) データベースの最大サイズがターゲットのコンピューティング サイズでサポートされていて、かつ、(2) 最大サイズがターゲットのコンピューティング サイズで付属のストレージ容量を超えている場合、追加ストレージ コストが適用されます。 たとえば、最大サイズ 500 GB の P1 データベースを S3 にダウンサイズする場合、S3 がサポートする最大サイズは 1 TB であり、S3 で付属のストレージ容量は 250 GB だけなので、追加ストレージ コストが適用されます。 したがって、追加ストレージ容量は 500 GB – 250 GB = 250 GB になります。 追加ストレージの価格については、「[Azure SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」を参照してください。 実際に使われる領域の量が付属のストレージの量より少ない場合、データベースの最大サイズを付属の量に減らすことで、この追加コストを回避できます。
- [geo レプリケーション](active-geo-replication-configure-portal.md)が有効な状態でデータベースをアップグレードする場合、そのセカンダリ データベースを目的のサービス レベルとコンピューティング サイズにアップグレードしてから、プライマリ データベースをアップグレードします (パフォーマンスを最大にするための一般的なガイダンス)。 別のエディションにアップグレードするには、セカンダリ データベースを先にアップグレードする必要があります。
- [geo レプリケーション](active-geo-replication-configure-portal.md)が有効な状態でデータベースをダウングレードする場合、そのプライマリ データベースを目的のサービス レベルとコンピューティング サイズにダウングレードしてから、セカンダリ データベースをダウングレードします (パフォーマンスを最大にするための一般的なガイダンス)。 別のエディションにダウングレードするには、プライマリ データベースを先にダウングレードする必要があります。
- サービス階層によって、提供されている復元サービスは異なります。 **Basic** レベルにダウングレードする場合は、バックアップのリテンション期間が短くなります。 [Azure SQL Database のバックアップ](automated-backups-overview.md)に関する記事をご覧ください。
- データベースに対する新しいプロパティは、変更が完了するまで適用されません。
- サービス レベルを変更するときにデータベースをスケーリングする ( 「[待機時間](#latency)」を参照) 必要がある場合は、スケール操作と並列のリソース使用率が高いと、スケーリング時間が長くなる可能性があります。 [高速データベース復旧 (ADR)](/sql/relational-databases/accelerated-database-recovery-concepts) では、長期にわたるトランザクションのロールバックが遅延の主な原因になることはありませんが、並列のリソース使用率が高いと、特に、コンピューティングのサイズが小さい場合に、スケーリングのために残されるコンピューティング、ストレージ、ネットワーク帯域幅のリソースが減ってしまうことがあります。

## <a name="billing"></a>課金

使用状況や、データベースのアクティブな期間が 1 時間未満だったかどうかには関係なく、データベースが存在する 1 時間ごとに、その 1 時間の間に適用された最も高いサービス レベル + コンピューティング サイズを使用して課金されます。 たとえば、Single Database を作成し、それを 5 分後に削除した場合、請求書にはデータベース時間として 1 時間の請求が表示されます。

## <a name="change-storage-size"></a>ストレージ サイズの変更

### <a name="vcore-based-purchasing-model"></a>仮想コアベースの購入モデル

- ストレージは、1 GB の増分値を使用して、データ ストレージの最大サイズの上限までプロビジョニングできます。 構成可能な最小データ ストレージは 1 GB です。 リソース制限のドキュメント ページにて、[単一データベース](resource-limits-vcore-single-databases.md)および[エラスティック プール](resource-limits-vcore-elastic-pools.md)の各サービス目標におけるデータ ストレージの最大サイズの制限をご確認ください。
- 単一データベースのストレージは、[Azure portal](https://portal.azure.com)、[Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#examples-1)、[PowerShell](/powershell/module/az.sql/set-azsqldatabase)、[Azure CLI](/cli/azure/sql/db#az-sql-db-update)、または [REST API](/rest/api/sql/databases/update) を使って最大サイズを増減してプロビジョニングできます。 バイトで指定する最大サイズの値は、1 GB (1073741824 バイト) の倍数である必要があります。
- データベースのデータ ファイルに格納できるデータ量は、データ ストレージに構成されている最大サイズによって制限されます。 Azure SQL Database では、この記憶域のほかに 30% さらに多いストレージを、トランザクション ログで使用するために自動的に割り当てます。
- Azure SQL Database では、`tempdb` データベースの仮想コアごとに 32 GB が自動的に割り当てられます。 `tempdb` は、すべてのサービス レベルのローカル SSD ストレージにあります。
- 単一データベースまたはエラスティック プールのストレージ料金は、データ ストレージとトランザクション ログ ストレージの容量の合計にサービス レベルのストレージ ユニットを掛けて計算します。 `tempdb` のコストは価格に含まれています。 ストレージの価格について詳しくは、「[Azure SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」を参照してください。

> [!IMPORTANT]
> 場合によっては、未使用領域を再利用できるようにデータベースを縮小する必要があります。 詳細については、「[Manage file space in Azure SQL Database](file-space-manage.md)」(Azure SQL Database でファイル領域を管理する) を参照してください。

### <a name="dtu-based-purchasing-model"></a>DTU ベースの購入モデル

- 単一データベースの DTU 価格には、追加コストなしで一定量のストレージが含まれます。 付属の容量を超える分のストレージについては、追加費用を払うことで、1 TB までは 250 GB 単位で、1 TB 以降は 256 GB 単位で、最大サイズ制限までプロビジョニングできます。 付属するストレージ容量と最大サイズ制限については、「[単一データベース: ストレージ サイズとコンピューティング サイズ](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes)」をご覧ください。
- 単一データベースの追加ストレージは、Azure portal、[Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#examples-1)、[PowerShell](/powershell/module/az.sql/set-azsqldatabase)、[Azure CLI](/cli/azure/sql/db#az-sql-db-update)、または [REST API](/rest/api/sql/databases/update) を使ってサイズを最大に増やすことでプロビジョニングできます。
- 単一データベースの追加ストレージの料金は、追加ストレージ容量にサービス レベルの追加ストレージ単価を掛けて計算します。 追加ストレージの価格について詳しくは、「[Azure SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」を参照してください。

> [!IMPORTANT]
> 場合によっては、未使用領域を再利用できるようにデータベースを縮小する必要があります。 詳細については、「[Manage file space in Azure SQL Database](file-space-manage.md)」(Azure SQL Database でファイル領域を管理する) を参照してください。

### <a name="geo-replicated-database"></a>Geo レプリケートされたデータベース

レプリケートされたセカンダリ データベースのデータベース サイズを変更するには、プライマリ データベースのサイズを変更します。 この変更がセカンダリ データベースでもレプリケートされて実装されます。

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>最大サイズが 1 TB を超える場合の P11 と P15 の制約

現在、1 TB を超える Premium レベルのストレージは、中国東部、中国北部、ドイツ中部、ドイツ北東部、を除くすべてのリージョンで利用できます。 これらのリージョンでは、Premium レベルのストレージの最大容量は 1 TB です。 最大サイズが 1 TB を超える P11 および P15 データベースには、次の考慮事項と制限事項が適用されます。

- P11 または P15 のデータベースの最大サイズが 1 TB を超える値に設定されていた場合、P11 または P15 のデータベースにしか復元またはコピーできません。  その後、再スケーリング操作の時点で割り当てられた領域の量が新しいコンピューティング サイズの最大サイズの上限を超えなければ、データベースを異なるコンピューティング サイズに再スケーリングできます。
- アクティブ geo レプリケーションのシナリオの場合:
  - geo レプリケーションのリレーションシップの設定:プライマリ データベースが P11 または P15 である場合は、セカンダリも P11 または P15 である必要があります。 コンピューティング サイズが小さい場合は、1 TB を超える領域をサポートできないため、セカンダリとして拒否されます。
  - geo レプリケーションのリレーションシップでのプライマリ データベースのアップグレード:プライマリ データベースで最大サイズを 1 TB 超に変更すると、セカンダリ データベースでも同じ変更がトリガーされます。 プライマリに対する変更を有効にするには、両方のアップグレードが正常に完了する必要があります。 1 TB を超えるオプションに関するリージョンの制限が適用されます。 セカンダリが 1 TB を超える領域をサポートしないリージョンにある場合、プライマリはアップグレードされません。
- 1 TB を超える P11/P15 データベースの読み込みに Import/Export サービスを使用することはサポートされていません。 SqlPackage.exe を使用して、データを[インポート](database-import.md)および[エクスポート](database-export.md)してください。

## <a name="next-steps"></a>次のステップ

全体的なリソースの制限については、[Azure SQL Database の仮想コアベースのリソース制限 - 単一データベース](resource-limits-vcore-single-databases.md)および [Azure SQL Database の DTU ベースのリソース制限 - 単一データベース](resource-limits-dtu-single-databases.md)に関するページを参照してください。

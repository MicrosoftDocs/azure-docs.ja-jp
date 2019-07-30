---
title: バックアップから Azure SQL データベースを復元する | Microsoft Docs
description: Azure SQL Database を特定の時点 (最長 35 日間) にロール バックすることができる、ポイントインタイム リストアについて説明します。
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 04/30/2019
ms.openlocfilehash: 80d01a360a2f80749bd7fbe7a9aadb9dda1189c6
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706986"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>データベースの自動バックアップを使用した Azure SQL データベースの復旧

既定で、SQL Database のバックアップは Geo レプリケートされている Blob ストレージ (RA-GRS) に格納されます。 [自動データベース バックアップ](sql-database-automated-backups.md)を使用したデータベースの復旧には、次のオプションを使用できます。

- 保有期間内の特定の時点に復旧された、同じ SQL Database サーバー上に新しいデータベースを作成する。
- 削除済みデータベースの削除時点に復旧された、同じ SQL Database サーバー上にデータベースを作成する。
- 最新のバックアップの時点に復旧された、同じリージョンの SQL Database サーバー上に新しいデータベースを作成する。
- 最後にレプリケートされたバックアップの時点に復旧された、任意の他のリージョンの SQL Database サーバー上に新しいデータベースを作成する。

[長期保有期間のバックアップ](sql-database-long-term-retention.md)を構成している場合、任意の SQL Database サーバー上に任意の LTR バックアップから新しいデータベースを作成することもできます。

> [!IMPORTANT]
> 復元中に既存のデータベースを上書きすることはできません。

Standard または Premium サービス レベルを使用している場合、復元されたデータベースでは、次の条件下で、追加のストレージ コストが生じます。

- データベースの最大サイズが 500 GB より大きい場合に、P11–P15 を S4-S12 または P1–P6 に復元する。
- データベースの最大サイズが 250 GB より大きい場合に、P1–P6 を S4-S12 に復元する。

復元されたデータベースの最大サイズがターゲット データベースのサービス レベルとパフォーマンス レベルに含まれているストレージの使用量を超えると、追加のコストが発生します。 含まれている使用量を超えてプロビジョニングされた追加のストレージには追加料金がかかります。 追加ストレージの価格について詳しくは、「[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。 実際に使用される容量が含まれているストレージの使用量より少ない場合、データベースの最大サイズを含まれている量に設定することで、この追加コストを回避できます。

> [!NOTE]
> [データベースのコピー](sql-database-copy.md)を作成するときは、[自動データベース バックアップ](sql-database-automated-backups.md)が使われます。

## <a name="recovery-time"></a>復旧時間

データベースの自動バックアップを使用してデータベースを復元する復旧時間は、さまざまな要因によって影響を受けます。

- データベースのサイズ
- データベースのコンピューティング サイズ
- 関連するトランザクション ログ数
- 復元時点に復旧するために再生の必要があるアクティビティ量
- 別のリージョンへの復元の場合は、ネットワーク帯域幅
- ターゲット リージョンで処理される、同時実行される復元要求の数

大規模なデータベースや、非常にアクティブなデータベースの場合、復元に数時間かかることがあります。 あるリージョンでの停止が長引いた場合、多数の geo リストア要求が他のリージョンによって処理されることがあります。 多数の要求がある場合、そのリージョンでのデータベースの復旧時間が長くなる可能性があります。 ほとんどのデータベースの復元は、12 時間未満で完了します。

単一サブスクリプションには、同時リストア要求の数に制限があります。  これらの制限は、ポイントインタイム リストア、geo リストア、および長期保有バックアップからの復元の任意の組み合わせに適用されます。

| | **処理される同時要求の最大数** | **送信される同時要求の最大数** |
| :--- | --: | --: |
|単一のデータベース (サブスクリプションごと)|10|60|
|エラスティック プール (プールごと)|4|200|
||||

現在、サーバー全体を復元するための組み込みの方法はありません。 このタスクを達成する 1 つの方法として、たとえば、[Azure SQL Database:Full Server Recovery](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) スクリプトがあります。

> [!IMPORTANT]
> 自動バックアップを使って復旧するには、サブスクリプションにおける SQL Server の共同作成者ロールのメンバーまたはサブスクリプション所有者である必要があります。[RBAC の組み込みのロール](../role-based-access-control/built-in-roles.md)に関するページをご覧ください。 復旧には、Azure ポータル、PowerShell、または REST API を使用できます。 Transact-SQL は使用できません。

## <a name="point-in-time-restore"></a>ポイントインタイム リストア

Azure portal、[PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)、または [REST API](https://docs.microsoft.com/rest/api/sql/databases) を使用して、スタンドアロンのデータベース、プールされたデータベース、またはインスタンス データベースを以前の時点に復元できます。 要求では、復元されるデータベースに対して任意のサービス レベルまたはコンピューティング サイズを指定できます。 データベースを復元するサーバーに十分なリソースがあることを確認します。 完了すると、新しいデータベースが元のデータベースと同じサーバー上に作成されます。 復元されたデータベースに対しては、サービス レベルとコンピューティング サイズに基づいて通常の料金で課金されます。 データベースの復元が完了するまで、料金は発生しません。

通常、以前の時点へのデータベースの復元は、復旧の目的で行います。 復元されたデータベースは、元のデータベースの代わりとして扱うことも、元のデータベースを更新するためのソース データとして使用することもできます。

- **データベースの置換**

  復元されたデータベースを元のデータベースの代わりとして使用する場合は、元のデータベースのコンピューティング サイズとサービス レベルを指定する必要があります。 次に、T-SQL の [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) コマンドを使用して、元のデータベース名を変更し、復元したデータベースに元の名前を付けます。

- **データの復旧**

  ユーザー エラーまたはアプリケーション エラーからの復旧のために、復元されたデータベースからデータを取得する場合は、復元されたデータベースからデータを抽出して元のデータベースに適用するデータ復旧スクリプトを作成して実行する必要があります。 復元操作が完了するまでに時間がかかる可能性がありますが、復元プロセスを通して、復元しているデータベースがデータベース一覧に表示されます。 復元中にデータベースを削除すると、復元操作は取り消され、復元が完了していないデータベースに対しては課金されません。

Azure portal を使用して単一のデータベース、プールされたデータベース、またはインスタンス データベースを特定の時点に復旧するには、データベースのページを開き、ツールバーの **[復元]** をクリックします。

![ポイントインタイム リストア](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

> [!IMPORTANT]
> データベースをプログラムでバックアップから復元するには、「[自動バックアップを使用したプログラム実行の復旧](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)」を参照してください。

## <a name="deleted-database-restore"></a>削除されたデータベースの復元

Azure portal、[PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)、または [REST (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) を使用して、同じ SQL Database サーバーに、削除されたデータベースを、削除時点またはそれ以前の時点に復元できます。 [PowerShell を使用して Managed Instance 上の削除されたデータベースを復元](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../recreate-dropped-database-on-azure-sql-managed-instance)できます。 

> [!TIP]
> 削除されたデータベースの復元方法を示すサンプル PowerShell スクリプトについては、[PowerShell を使用した SQL データベースの復元](scripts/sql-database-restore-database-powershell.md)に関するページを参照してください。
> [!IMPORTANT]
> Azure SQL Database サーバー インスタンスを削除すると、そのデータベースもすべて削除されます。これを回復することはできません。 現時点では、削除されたサーバーを復元するためのサポートはありません。

### <a name="deleted-database-restore-using-the-azure-portal"></a>Azure portal を使用した削除済みデータベースの復元

Azure portal を使用して削除されたデータベースを復旧するには、サーバーのページを開き、[操作] 領域で **[削除済みデータベース]** をクリックします。

![削除されたデータベースの復元 1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)

![削除されたデータベースの復元 2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

> [!IMPORTANT]
> 削除されたデータベースをプログラムで復元するには、「[自動バックアップを使用したプログラム実行の復旧](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)」を参照してください。

## <a name="geo-restore"></a>geo リストア

geo レプリケートされた最新のバックアップから任意の Azure リージョン内の任意のサーバーで SQL データベースを復元することができます。 geo リストアでは、geo レプリケートされたバックアップをソースとして使用します。 これは、データベースまたはデータセンターが停止してアクセスできない場合でも要求できます。

geo リストアは、ホスティング リージョンでのインシデントが原因でデータベースが利用できない場合の既定の復旧オプションです。 データベースは他の任意のリージョンのサーバーに復元できます。 バックアップが取得される時刻と、別のリージョンの Azure BLOB にその差分バックアップが geo レプリケートされる時刻には時間差があります。 その結果、復元されたデータベースは、元のデータベースより最大 1 時間遅れることがあります。 次の図は、別のリージョン内の利用可能な最新のバックアップからのデータベースの復元を示しています。

![地理リストア](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> geo リストアの実行方法を示すサンプル PowerShell スクリプトについては、[PowerShell を使用した SQL データベースの復元](scripts/sql-database-restore-database-powershell.md)に関するページを参照してください。

geo セカンダリでのポイントインタイム リストアは、現在はサポートされていません。 ポイントインタイム リストアは、プライマリ データベースでのみ実行できます。 geo リストアを使用して障害から復旧する方法の詳細については、[障害からの復旧](sql-database-disaster-recovery.md)に関するページを参照してください。

> [!IMPORTANT]
> geo リストアは、SQL Database で使用できる最も基本的なディザスター リカバリー ソリューションです。 geo リストアは、自動的に作成される geo レプリケートされたバックアップに依存し、その RPO は 1 時間、推定復旧時間は最大 12 時間です。 地域的な停止後は需要が急激に増加する可能性があるため、目的のデータベースを復元する容量がターゲット リージョンに確保される保証はありません。 比較的小規模なデータベースを使用する、ビジネスに不可欠ではないアプリケーションでは、geo リストアが適切なディザスター リカバリー ソリューションです。 大規模なデータベースを使用し、事業継続性を確保する必要があるビジネスに不可欠なアプリケーションの場合は、[自動フェールオーバー グループ](sql-database-auto-failover-group.md)を使用する必要があります。 大幅に低い RPO と RTO が実現され、容量が常に保証されます。 ビジネスを継続するための選択の詳細については、[ビジネス継続性の概要](sql-database-business-continuity.md)に関するページを参照してください。

### <a name="geo-restore-using-the-azure-portal"></a>Azure portal を使用した geo リストア

Azure Portal を使用して、データベースをその [DTU ベースのモデル リテンション期間](sql-database-service-tiers-dtu.md)中または[仮想コアベースのモデル リテンション期間](sql-database-service-tiers-vcore.md)中に geo リストアするには、[SQL データベース] ページを開き、 **[追加]** をクリックします。 **[ソースの選択]** テキスト ボックスで、 **[バックアップ]** を選択します。 任意のリージョン内のサーバー上で復旧を実行するバックアップを指定します。

> [!Note]
> Azure portal を使用した Geo 復元は、マネージド インスタンスでは利用できません。 代わりに、PowerShell を使用してください。

## <a name="programmatically-performing-recovery-using-automated-backups"></a>自動バックアップを使用したプログラム実行の復旧

前に説明したように、データベースの復旧は、Azure Portal のほかに、Azure PowerShell または REST API を使用してプログラムで実行できます。 次の表では、使用できるコマンド セットについて説明します。

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

- スタンドアロン データベースまたはプールされたデータベースを復元するには、「[Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase)」を参照してください。

  | コマンドレット | 説明 |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |1 つまたは複数のデータベースを取得します。 |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | 復元する削除済みデータベースを取得します。 |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |データベースの geo 冗長バックアップを取得します。 |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |SQL Database を復元します。 |

  > [!TIP]
  > データベースのポイントインタイム リストアの実行方法を示すサンプル PowerShell スクリプトについては、[PowerShell を使用した SQL データベースの復元](scripts/sql-database-restore-database-powershell.md)に関するページを参照してください。

- マネージド インスタンス データベースを復元するには、「[Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)」を参照してください。

  | コマンドレット | 説明 |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |1 つまたは複数のマネージド インスタンスを取得します。 |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | インスタンス データベースを取得します。 |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |インスタンス データベースを復元します。 |

### <a name="rest-api"></a>REST API

REST API を使用して単一のデータベースまたはプールされたデータベースを復元するには:

| API | 説明 |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |データベースを復元します |
| [Get Create or Update Database Status](https://docs.microsoft.com/rest/api/sql/operations) |復元操作中にステータスを返します |

### <a name="azure-cli"></a>Azure CLI

- Azure CLI を使用して単一のデータベースまたはプールされたデータベースを復元するには、「[az sql db restore](/cli/azure/sql/db#az-sql-db-restore)」を参照してください。
- Azure CLI を使用してマネージド インスタンスを復元するには、「[az sql midb restore](/cli/azure/sql/midb#az-sql-midb-restore)」を参照してください。

## <a name="summary"></a>まとめ

自動バックアップは、ユーザー エラーやアプリケーション エラー、偶発的なデータベースの削除、および長期間にわたる障害からデータベースを保護します。 この組み込みの機能は、すべてのサービス レベルとコンピューティング サイズで使用できます。

## <a name="next-steps"></a>次の手順

- ビジネス継続性の概要およびシナリオについては、 [ビジネス継続性の概要](sql-database-business-continuity.md)に関する記事を参照してください。
- Azure SQL Database 自動バックアップの詳細については、「 [SQL Database 自動バックアップ](sql-database-automated-backups.md)」を参照してください
- 長期のリテンション期間については、[長期のリテンション期間](sql-database-long-term-retention.md)に関する記事をご覧ください。
- より迅速な復旧オプションについては、[アクティブ geo レプリケーション](sql-database-active-geo-replication.md)または[自動フェールオーバー グループ](sql-database-auto-failover-group.md)に関する記事を参照してください。

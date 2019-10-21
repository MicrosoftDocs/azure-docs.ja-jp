---
title: バックアップから Azure SQL データベースを復元する | Microsoft Docs
description: Azure SQL データベースを最大 35 日前までにロールバックすることができる、ポイントインタイム リストアについて説明します。
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 09/26/2019
ms.openlocfilehash: f316f77d0f4ca3132a2ae77d807e2dd66ba62a43
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71846346"
---
# <a name="recover-an-azure-sql-database-by-using-automated-database-backups"></a>自動データベース バックアップを使用して Azure SQL データベースを復旧する

既定では、Azure SQL Database のバックアップは geo レプリケートされた Blob Storage に格納されます。 [自動データベース バックアップ](sql-database-automated-backups.md)を使用したデータベースの復旧には、次のオプションを使用できます。 次のようにすることができます。

- 同じ SQL Database サーバー上に、保有期間内の特定の時点に復旧された新しいデータベースを作成します。
- 同じ SQL Database サーバー上に、削除されたデータベースの削除時点に復旧されたデータベースを作成します。
- 同じリージョン内の任意の SQL Database サーバー上に、最新のバックアップの時点に復旧された新しいデータベースを作成します。
- 任意の他のリージョン内の任意の SQL Database サーバー上に、最新のレプリケートされたバックアップの時点に復旧された新しいデータベースを作成します。

[バックアップの長期保有](sql-database-long-term-retention.md)を構成している場合、任意の長期保有バックアップから任意の SQL Database サーバー上に新しいデータベースを作成することもできます。

> [!IMPORTANT]
> 復元中に既存のデータベースを上書きすることはできません。

Standard または Premium のサービス レベルを使用している場合、データベースの復元によって追加のストレージ コストが発生する可能性があります。 復元されたデータベースの最大サイズが、ターゲット データベースのサービス レベルとパフォーマンス レベルに含まれるストレージの量を超えると、追加のコストが発生します。 追加ストレージの価格について詳しくは、「[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。 実際に使用される容量が、含まれるストレージの量より少ない場合、データベースの最大サイズを含まれる量に設定することで、この追加コストを回避できます。

> [!NOTE]
> [データベースのコピー](sql-database-copy.md)を作成するときは、[自動データベース バックアップ](sql-database-automated-backups.md)を使用します。

## <a name="recovery-time"></a>復旧時間

自動データベース バックアップを使用してデータベースを復元する復旧時間は、さまざまな要因によって影響を受けます。

- データベースのサイズ。
- データベースのコンピューティング サイズ。
- 関連するトランザクション ログの数。
- 復元時点に復旧するために再生する必要があるアクティビティの量。
- 別のリージョンへの復元の場合は、ネットワーク帯域幅。
- ターゲット リージョンで処理される、同時実行される復元要求の数

大規模なデータベースや、非常に活動の激しいデータベースの場合、復元に数時間かかることがあります。 あるリージョンでの停止が長引いた場合、他のリージョンによって処理される geo リストア要求の数が多くなることがあります。 多くの要求がある場合、そのリージョンでのデータベースの復旧時間が長くなる可能性があります。 ほとんどのデータベースの復元は、12 時間未満で完了します。

単一サブスクリプションには、同時リストア要求の数に制限があります。  これらの制限は、ポイントインタイム リストア、geo リストア、および長期保有バックアップからの復元の任意の組み合わせに適用されます。

| | **処理される同時要求の最大数** | **送信される同時要求の最大数** |
| :--- | --: | --: |
|単一のデータベース (サブスクリプションごと)|10|60|
|エラスティック プール (プールごと)|4|200|
||||

サーバー全体を復元するための組み込みの方法はありません。 このタスクを実行する方法の例については、「[Azure SQL Database: サーバーの完全復旧](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666)」を参照してください。

> [!IMPORTANT]
> 自動バックアップを使って復旧するには、サブスクリプションにおける SQL Server の共同作成者ロールのメンバーまたはサブスクリプション所有者である必要があります。 詳細については、[RBAC: 組み込みのロール](../role-based-access-control/built-in-roles.md)に関するページをご覧ください。 復旧には、Azure portal、PowerShell、または REST API を使用できます。 Transact-SQL は使用できません。

## <a name="point-in-time-restore"></a>ポイントインタイム リストア

Azure portal、[PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)、または [REST API](https://docs.microsoft.com/rest/api/sql/databases) を使用して、スタンドアロンのデータベース、プールされたデータベース、またはインスタンス データベースを、以前の時点に復元できます。 要求では、復元されるデータベースに対して任意のサービス レベルまたはコンピューティング サイズを指定できます。 データベースを復元するサーバーに十分なリソースを確保します。 完了すると、元のデータベースと同じサーバー上に新しいデータベースが作成されます。 復元されたデータベースでは、サービス レベルとコンピューティング サイズに基づいて通常料金が発生します。 データベースの復元が完了するまでは、料金は発生しません。

通常、以前の時点へのデータベースの復元は、復旧の目的で行います。 復元されたデータベースは、元のデータベースの代わりとして扱うことも、元のデータベースを更新するためのソース データとして使用することもできます。

- **データベースの置換**

  復元されたデータベースを元のデータベースの代わりとして使用する場合は、元のデータベースのコンピューティング サイズとサービス レベルを指定する必要があります。 次に、T-SQL の [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) コマンドを使用して、元のデータベース名を変更し、復元されたデータベースに元の名前を付けます。

- **データの復旧**

  ユーザー エラーまたはアプリケーション エラーからの復旧のために、復元されたデータベースからデータを取得する場合は、復元されたデータベースからデータを抽出して元のデータベースに適用するデータ復旧スクリプトを作成して実行する必要があります。 復元操作が完了するまでに時間がかかる可能性がありますが、復元プロセスを通して、復元しているデータベースがデータベース一覧に表示されます。 復元中にデータベースを削除すると、復元操作は取り消され、復元が完了していないデータベースに対しては課金されません。
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Azure portal を使用したポイントインタイム リストア

Azure portal で復元するデータベースの概要ブレードから、単一の SQL データベースまたはインスタンス データベースを特定の時点に復旧できます。

#### <a name="single-azure-sql-database"></a>単一の Azure SQL データベース

Azure portal を使用して単一データベースまたはプールされたデータベースを特定の時点に復旧するには、データベースの概要ページを開き、ツール バーの **[復元]** を選択します。 バックアップ ソースを選択し、新しいデータベースを作成する特定の時点のバックアップ ポイントを選択します。 

  ![データベース復元オプションのスクリーンショット](./media/sql-database-recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>マネージド インスタンスのデータベース

Azure portal を使用してマネージド インスタンス データベースを特定の時点に復旧するには、データベースの概要ページを開き、ツール バーの **[復元]** を選択します。 新しいデータベースを作成する特定の時点のバックアップ ポイントを選択します。 

  ![データベース復元オプションのスクリーンショット](./media/sql-database-recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> データベースをプログラムでバックアップから復元するには、「[自動バックアップを使用したプログラム実行の復旧](sql-database-recovery-using-backups.md)」を参照してください。

## <a name="deleted-database-restore"></a>削除されたデータベースの復元

削除されたデータベースを、削除時点またはそれ以前の時点の状態で、同じ SQL Database サーバー上または同じマネージド インスタンス上に復元できます。 Azure portal、[PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)、または [REST (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) を使用して、これを行うことができます。 削除されたデータベースを復元するには、バックアップから新しいデータベースを作成します。

> [!IMPORTANT]
> Azure SQL Database サーバーまたはマネージド インスタンスを削除すると、そのデータベースもすべて削除され、復旧することはできません。 削除されたサーバーまたはマネージド インスタンスは復元できません。

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>削除されたデータベースの Azure portal を使用した復元

削除されたデータベースを、Azure portal を使用してサーバーおよびインスタンス リソースから復元します。

#### <a name="single-azure-sql-database"></a>単一の Azure SQL データベース

Azure portal を使用して、削除された単一データベースまたはプールされたデータベースを復旧するには、サーバーの概要ページを開き、 **[削除されたデータベース]** を選択します。 復元する削除されたデータベースを選択し、バックアップから復元されたデータを使用して作成される新しいデータベースの名前を入力します。

  ![削除されたデータベースのオプションのスクリーンショット](./media/sql-database-recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>マネージド インスタンスのデータベース

Azure portal を使用して、マネージド インスタンス上の削除されたデータベースを復元することはできません。 この目的には PowerShell を使用できます。 

### <a name="deleted-database-restore-by-using-powershell"></a>削除されたデータベースの PowerShell を使用した復元

PowerShell を使用して、Azure SQL Database とマネージド インスタンスの削除されたデータベースを復元するには、以下のサンプル スクリプトを使用します。

#### <a name="single-azure-sql-database"></a>単一の Azure SQL データベース

削除された Azure SQL データベースの復元方法を示すサンプル PowerShell スクリプトについては、[PowerShell を使用した SQL データベースの復元](scripts/sql-database-restore-database-powershell.md)に関するページをご覧ください。

#### <a name="managed-instance-database"></a>マネージド インスタンスのデータベース

削除されたインスタンス データベースの復元方法を示す PowerShell スクリプトのサンプルについては、[PowerShell を使用したマネージド インスタンス上の削除されたデータベースの復元](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../recreate-dropped-database-on-azure-sql-managed-instance)に関するページをご覧ください。 

> [!TIP]
> 削除されたデータベースをプログラムで復元するには、「[自動バックアップを使用したプログラム実行の復旧](sql-database-recovery-using-backups.md)」を参照してください。

## <a name="geo-restore"></a>geo リストア

geo レプリケートされた最新のバックアップから任意の Azure リージョン内の任意のサーバーで SQL データベースを復元することができます。 geo リストアでは、geo レプリケートされたバックアップをソースとして使用します。 データベースまたはデータセンターが停止してアクセスできない場合でも、geo リストアを要求できます。

geo リストアは、ホスティング リージョンでのインシデントが原因でデータベースが利用できない場合の既定の復旧オプションです。 データベースは他の任意のリージョンのサーバーに復元できます。 バックアップが取得される時刻と、別のリージョンの Azure BLOB にその差分バックアップが geo レプリケートされる時刻には時間差があります。 その結果、復元されたデータベースは、元のデータベースより最大 1 時間遅れることがあります。 次の図では、別のリージョン内の利用可能な最新のバックアップからのデータベースの復元を示します。

![geo リストアの図](./media/sql-database-geo-restore/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Azure portal を使用した geo リストア

Azure portal から、新しい単一データベースまたはマネージド インスタンス データベースを作成し、使用可能な geo リストア バックアップを選択します。 新しく作成されたデータベースに、geo リストアされたバックアップ データが格納されます。

#### <a name="single-azure-sql-database"></a>単一の Azure SQL データベース

選択したリージョンとサーバーの単一の SQL データベースを Azure portal から geo リストアするには、次の手順のようにします。

1. **ダッシュボード**から、 **[追加]**  >  **[SQL データベースの作成]** を選択します。 **[基本]** タブで、必要な情報を入力します。
2. **[追加設定]** を選択します。
3. **[既存のデータを使用します]** で **[バックアップ]** を選択します。
4. **[バックアップ]** で、使用可能な geo リストア バックアップの一覧からバックアップを選択します。

    ![[SQL データベースの作成] のオプションのスクリーンショット](./media/sql-database-recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

新しいデータベースを作成するプロセスを完了します。 単一の Azure SQL データベースを作成すると、復元された geo リストア バックアップが格納されます。

#### <a name="managed-instance-database"></a>マネージド インスタンスのデータベース

Azure portal を使用して、マネージド インスタンス データベースを任意のリージョンの既存のマネージド インスタンスに geo リストアするには、復元するデータベースが存在するマネージド インスタンスを選択します。 次の手順に従います。

1. **[新しいデータベース]** を選択します。
2. 目的のデータベース名を入力します。
3. **[既存のデータを使用します]** で **[バックアップ]** を選択します。
4. 使用可能な geo リストア バックアップの一覧からバックアップを選択します。

    ![[新しいデータベース] のオプションのスクリーンショット](./media/sql-database-recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

新しいデータベースを作成するプロセスを完了します。 インスタンス データベースを作成すると、復元された geo リストア バックアップが格納されます。

### <a name="geo-restore-by-using-powershell"></a>PowerShell を使用した geo リストア

#### <a name="single-azure-sql-database"></a>単一の Azure SQL データベース

単一の SQL データベースに対して geo リストアを実行する方法を示す PowerShell スクリプトについては、「[PowerShell を使用して Azure SQL 単一データベースの以前の時点に復元する](scripts/sql-database-restore-database-powershell.md)」を参照してください。

#### <a name="managed-instance-database"></a>マネージド インスタンスのデータベース

マネージド インスタンス データベースに対して geo リストアを実行する方法を示す PowerShell スクリプトについては、「[PowerShell を使用して Managed Instance データベースを別の geo リージョンに復元する](scripts/sql-managed-instance-restore-geo-backup.md)」を参照してください。

### <a name="geo-restore-considerations"></a>geo リストアに関する考慮事項

geo セカンダリ データベースでは、ポイントインタイム リストアを実行できません。 できるのはプライマリ データベースだけです。 geo リストアを使用して障害から復旧する方法の詳細については、[障害からの復旧](sql-database-disaster-recovery.md)に関するページを参照してください。

> [!IMPORTANT]
> geo リストアは、SQL Database で使用できる最も基本的なディザスター リカバリー ソリューションです。 それは、自動的に作成される geo レプリケートされたバックアップに依存し、目標復旧時点 (RPO) は 1 時間、推定復旧時間は最大 12 時間です。 リージョンの停止後は、需要が急激に増加する可能性があるため、目的のデータベースを復元する容量がターゲット リージョンに確保される保証はありません。 アプリケーションで使用されているデータベースが比較的小さく、アプリケーションがビジネス クリティカルではない場合は、geo リストアが適切なディザスター リカバリー ソリューションです。 大規模なデータベースを使用し、事業継続性を確保する必要があるビジネス クリティカルなアプリケーションの場合は、[自動フェールオーバー グループ](sql-database-auto-failover-group.md)を使用する必要があります。 大幅に低い RPO と目標復旧時間が実現され、容量が常に保証されます。 ビジネスを継続するための選択の詳細については、[ビジネス継続性の概要](sql-database-business-continuity.md)に関するページを参照してください。

## <a name="programmatically-performing-recovery-by-using-automated-backups"></a>自動バックアップを使用したプログラム実行の復旧

Azure PowerShell または REST API を復旧に使用することもできます。 次の表では、使用できるコマンド セットについて説明します。

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数はかなりの程度まで同じです。

#### <a name="single-azure-sql-database"></a>単一の Azure SQL データベース

スタンドアロン データベースまたはプールされたデータベースを復元するには、「[Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase)」を参照してください。

  | コマンドレット | 説明 |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |1 つまたは複数のデータベースを取得します。 |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | 復元する削除済みデータベースを取得します。 |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |データベースの geo 冗長バックアップを取得します。 |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |SQL Database を復元します。 |

  > [!TIP]
  > データベースのポイントインタイム リストアの実行方法を示すサンプル PowerShell スクリプトについては、[PowerShell を使用した SQL データベースの復元](scripts/sql-database-restore-database-powershell.md)に関するページを参照してください。

#### <a name="managed-instance-database"></a>マネージド インスタンスのデータベース

マネージド インスタンス データベースを復元するには、「[Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)」を参照してください。

  | コマンドレット | 説明 |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |1 つまたは複数のマネージド インスタンスを取得します。 |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | インスタンス データベースを取得します。 |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |インスタンス データベースを復元します。 |

### <a name="rest-api"></a>REST API

REST API を使用して単一データベースまたはプールされたデータベースを復元するには:

| API | 説明 |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |データベースを復元します。 |
| [Get Create or Update Database Status](https://docs.microsoft.com/rest/api/sql/operations) |復元操作中にステータスを返します。 |

### <a name="azure-cli"></a>Azure CLI

#### <a name="single-azure-sql-database"></a>単一の Azure SQL データベース

Azure CLI を使用して単一データベースまたはプールされたデータベースを復元するには、「[az sql db restore](/cli/azure/sql/db#az-sql-db-restore)」を参照してください。

#### <a name="managed-instance-database"></a>マネージド インスタンスのデータベース

Azure CLI を使用してマネージド インスタンス データベースを復元するには、「[az sql db restore](/cli/azure/sql/midb#az-sql-midb-restore)」を参照してください。

## <a name="summary"></a>まとめ

自動バックアップは、ユーザー エラーやアプリケーション エラー、偶発的なデータベースの削除、および長期間にわたる障害からデータベースを保護します。 この組み込みの機能は、すべてのサービス レベルとコンピューティング サイズで使用できます。

## <a name="next-steps"></a>次の手順

- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [SQL Database 自動バックアップ](sql-database-automated-backups.md)
- [長期保存](sql-database-long-term-retention.md)
- より迅速な復旧オプションについては、[アクティブ geo レプリケーション](sql-database-active-geo-replication.md)または[自動フェールオーバー グループ](sql-database-auto-failover-group.md)に関する記事を参照してください。

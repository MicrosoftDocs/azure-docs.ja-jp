---
title: バックアップからデータベースを復元する
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure SQL Database のデータベースまたは Azure SQL Managed Instance のインスタンスを最大 35 日前にロールバックすることができる、ポイントインタイム リストアについて説明します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein, danil
ms.date: 11/13/2020
ms.openlocfilehash: 0c3db3b3f22f9f2639012068924708537f9ada77
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98795334"
---
# <a name="recover-using-automated-database-backups---azure-sql-database--sql-managed-instance"></a>自動データベース バックアップを使用して復旧する - Azure SQL Database および SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[自動データベース バックアップ](automated-backups-overview.md)を使用したデータベースの復旧には、次のオプションを使用できます。 次のようにすることができます。

- 同じサーバー上に、保有期間内の特定の時点に復旧された新しいデータベースを作成する。
- 同じサーバー上に、削除済みデータベースの削除時に復旧されたデータベースを作成する。
- 同じリージョンのサーバー上に、最新のバックアップの時点に復旧された新しいデータベースを作成する。
- 任意の他のリージョンのサーバー上に、最後にレプリケートされたバックアップの時点に復旧された新しいデータベースを作成する。

[バックアップの長期保有](long-term-retention-overview.md)を構成している場合、任意の長期保有バックアップから任意のサーバー上に新しいデータベースを作成することもできます。

> [!IMPORTANT]
> 復元中に既存のデータベースを上書きすることはできません。

Standard または Premium のサービス レベルを使用している場合、データベースの復元によって追加のストレージ コストが発生する可能性があります。 復元されたデータベースの最大サイズが、ターゲット データベースのサービス レベルとパフォーマンス レベルに含まれるストレージの量を超えると、追加のコストが発生します。 追加ストレージの価格について詳しくは、「[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。 実際に使用される容量が、含まれるストレージの量より少ない場合、データベースの最大サイズを含まれる量に設定することで、この追加コストを回避できます。

## <a name="recovery-time"></a>復旧時間

自動データベース バックアップを使用してデータベースを復元する復旧時間は、さまざまな要因によって影響を受けます。

- データベースのサイズ。
- データベースのコンピューティング サイズ。
- 関連するトランザクション ログの数。
- 復元時点に復旧するために再生する必要があるアクティビティの量。
- 別のリージョンへの復元の場合は、ネットワーク帯域幅。
- ターゲット リージョンで処理される、同時実行される復元要求の数

大規模なデータベースや、非常に活動の激しいデータベースの場合、復元に数時間かかることがあります。 リージョン内で長時間にわたる障害が発生した場合は、ディザスター リカバリーのために、多数の geo リストア要求が開始される可能性があります。 多数の要求がある場合、個々のデータベースでの復旧時間が長くなる可能性があります。 ほとんどのデータベースの復元は、12 時間未満で完了します。

単一サブスクリプションには、同時リストア要求の数に制限があります。 これらの制限は、ポイントインタイム リストア、geo リストア、および長期保有バックアップからの復元の任意の組み合わせに適用されます。

| **デプロイ オプション** | **処理される同時要求の最大数** | **送信される同時要求の最大数** |
| :--- | --: | --: |
|**単一データベース (サブスクリプションごと)**|30|100|
|**エラスティック プール (プールごと)**|4|2000|


サーバー全体を復元するための組み込みの方法はありません。 このタスクを実行する方法の例については、「[Azure SQL Database: サーバーの完全復旧](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666)」を参照してください。

> [!IMPORTANT]
> 自動バックアップを使って復旧するには、サブスクリプション内の SQL Server 共同作成者ロールまたは SQL Managed Instance 共同作成者ロール (復旧のターゲットによって決まります) のメンバーであるか、サブスクリプション所有者である必要があります。 詳細については、[Azure RBAC: 組み込みのロール](../../role-based-access-control/built-in-roles.md)に関するページをご覧ください。 復旧には、Azure portal、PowerShell、または REST API を使用できます。 Transact-SQL は使用できません。

## <a name="point-in-time-restore"></a>ポイントインタイム リストア

Azure portal、[PowerShell](/powershell/module/az.sql/restore-azsqldatabase)、または [REST API](/rest/api/sql/databases/createorupdate#creates-a-database-from-pointintimerestore.) を使用して、スタンドアロンのデータベース、プールされたデータベース、またはインスタンス データベースを、以前の時点に復元できます。 要求では、復元されるデータベースに対して任意のサービス レベルまたはコンピューティング サイズを指定できます。 データベースを復元するサーバーに十分なリソースを確保します。 

完了すると、元のデータベースと同じサーバー上に新しいデータベースが作成されます。 復元されたデータベースでは、サービス レベルとコンピューティング サイズに基づいて通常料金が発生します。 データベースの復元が完了するまでは、料金は発生しません。

通常、以前の時点へのデータベースの復元は、復旧の目的で行います。 復元されたデータベースは、元のデータベースの代わりとして扱うことも、元のデータベースを更新するためのデータ ソースとして使用することもできます。

- **データベースの置換**

  復元されたデータベースを元のデータベースの代わりとして使用する場合は、元のデータベースのコンピューティング サイズとサービス レベルを指定する必要があります。 次に、T-SQL の [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) コマンドを使用して、元のデータベース名を変更し、復元されたデータベースに元の名前を付けます。

- **データの復旧**

  ユーザー エラーまたはアプリケーション エラーから復旧するために、復元されたデータベースからデータを取得する場合は、復元されたデータベースからデータを抽出して元のデータベースに適用するデータ復旧スクリプトを作成して実行する必要があります。 復元操作が完了するまでに時間がかかる可能性がありますが、復元プロセスを通して、復元しているデータベースがデータベース一覧に表示されます。 復元中にデータベースを削除すると、復元操作は取り消され、復元が完了していないデータベースに対しては課金されません。
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Azure portal を使用したポイントインタイム リストア

Azure portal で復元するデータベースの概要ブレードから、単一のインスタンス データベースを特定の時点に復旧できます。

#### <a name="sql-database"></a>SQL Database

Azure portal を使用してデータベースを特定の時点に復旧するには、データベースの概要ページを開き、ツール バーの **[復元]** を選択します。 バックアップ ソースを選択し、新しいデータベースを作成する特定の時点のバックアップ ポイントを選択します。

  ![SQL Database のデータベース復元オプションのスクリーンショット。](./media/recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Azure portal を使用してマネージド インスタンス データベースを特定の時点に復旧するには、データベースの概要ページを開き、ツール バーの **[復元]** を選択します。 新しいデータベースを作成する特定の時点のバックアップ ポイントを選択します。

  ![SQL Managed Instance のデータベース復元オプションのスクリーンショット。](./media/recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> データベースをプログラムでバックアップから復元するには、[自動バックアップを使用したプログラムによる復旧](recovery-using-backups.md)に関するページを参照してください。

## <a name="deleted-database-restore"></a>削除されたデータベースの復元

削除されたデータベースを、削除時点またはそれ以前の時点の状態で、同じサーバー上または同じマネージド インスタンス上に復元できます。 Azure portal、[PowerShell](/powershell/module/az.sql/restore-azsqldatabase)、または [REST (createMode=Restore)](/rest/api/sql/databases/createorupdate) を使用して、これを行うことができます。 削除されたデータベースを復元するには、バックアップから新しいデータベースを作成します。

> [!IMPORTANT]
> サーバーまたはマネージド インスタンスを削除すると、そのデータベースもすべて削除され、復旧することはできなくなります。 削除されたサーバーまたはマネージド インスタンスは復元できません。

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>削除されたデータベースの Azure portal を使用した復元

削除されたデータベースの復元は、Azure portal で、サーバーまたはマネージド インスタンス リソースから実行します。

> [!TIP]
> 最近削除されたデータベースが Azure portal の **[削除されたデータベース]** ページに表示されるには、数分かかることがあります。削除されたデータベースを [プログラムを使用して](#programmatic-recovery-using-automated-backups)表示する場合も同様です。

#### <a name="sql-database"></a>SQL Database

Azure portal を使用して削除済みデータベースを削除時刻まで復旧するには、サーバーの概要ページを開き、 **[削除済みデータベース]** を選択します。 復元する削除されたデータベースを選択し、バックアップから復元されたデータを使用して作成される新しいデータベースの名前を入力します。

  ![削除されたデータベースの復元のスクリーンショット](./media/recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Azure portal を使用して、マネージド データベースを復旧するには、マネージド インスタンスの概要ページを開き、 **[削除されたデータベース]** を選択します。 復元する削除されたデータベースを選択し、バックアップから復元されたデータを使用して作成される新しいデータベースの名前を入力します。

  ![削除された Azure SQL Managed Instance データベースの復元のスクリーンショット](./media/recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>削除されたデータベースの PowerShell を使用した復元

PowerShell を使用して、SQL Database または SQL Managed Instance の削除されたデータベースを復元するには、以下のサンプル スクリプトを使用します。

#### <a name="sql-database"></a>SQL Database

Azure SQL Database の削除されたデータベースの復元方法を示すサンプル PowerShell スクリプトについては、[PowerShell を使用したデータベースの復元](scripts/restore-database-powershell.md)に関するページを参照してください。

#### <a name="sql-managed-instance"></a>SQL Managed Instance

削除されたインスタンス データベースの復元方法を示す PowerShell スクリプトのサンプルについては、[PowerShell を使用した削除されたデータベースの復元](../managed-instance/point-in-time-restore.md#restore-a-deleted-database)に関するページをご覧ください。

> [!TIP]
> 削除されたデータベースをプログラムで復元するには、「[自動バックアップを使用したプログラム実行の復旧](recovery-using-backups.md)」を参照してください。

## <a name="geo-restore"></a>geo リストア

> [!IMPORTANT]
> geo リストアは、geo 冗長[バックアップ ストレージ](automated-backups-overview.md#backup-storage-redundancy)を使用して構成された SQL データベースまたはマネージド インスタンスでのみ利用できます。

最新の geo レプリケートされたバックアップから、任意の Azure リージョンの任意の SQL Database サーバーにデータベースを復元するか、任意のマネージド インスタンスにインスタンス データベースを復元できます。 geo リストアでは、geo レプリケートされたバックアップをソースとして使用します。 データベースまたはデータセンターが停止してアクセスできない場合でも、geo リストアを要求できます。

geo リストアは、ホスティング リージョンでのインシデントが原因でデータベースが利用できない場合の既定の復旧オプションです。 データベースは他の任意のリージョンのサーバーに復元できます。 バックアップが取得される時刻と、別のリージョンの Azure BLOB にその差分バックアップが geo レプリケートされる時刻には時間差があります。 その結果、復元されたデータベースは、元のデータベースより最大 1 時間遅れることがあります。 次の図では、別のリージョン内の利用可能な最新のバックアップからのデータベースの復元を示します。

![geo リストアの図](./media/recovery-using-backups/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Azure portal を使用した geo リストア

Azure portal から、新しい単一データベースまたはマネージド インスタンス データベースを作成し、使用可能な geo リストア バックアップを選択します。 新しく作成されたデータベースに、geo リストアされたバックアップ データが格納されます。

#### <a name="sql-database"></a>SQL Database

選択したリージョンとサーバーの単一データベースを Azure portal から geo リストアするには、次の手順に従います。

1. **ダッシュボード** から、 **[追加]**  >  **[SQL データベースの作成]** を選択します。 **[基本]** タブで、必要な情報を入力します。
2. **[追加設定]** を選択します。
3. **[既存のデータを使用します]** で **[バックアップ]** を選択します。
4. **[バックアップ]** で、使用可能な geo リストア バックアップの一覧からバックアップを選択します。

    ![[SQL データベースの作成] のオプションのスクリーンショット](./media/recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

新しいデータベースをバックアップから作成するプロセスを完了します。 Azure SQL Database でデータベースを作成すると、復元された geo リストア バックアップが格納されます。

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Azure portal を使用して、マネージド インスタンス データベースを任意のリージョンの既存のマネージド インスタンスに geo リストアするには、復元するデータベースが存在するマネージド インスタンスを選択します。 次の手順に従います。

1. **[新しいデータベース]** を選択します。
2. 目的のデータベース名を入力します。
3. **[既存のデータを使用します]** で **[バックアップ]** を選択します。
4. 使用可能な geo リストア バックアップの一覧からバックアップを選択します。

    ![[新しいデータベース] のオプションのスクリーンショット](./media/recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

新しいデータベースを作成するプロセスを完了します。 インスタンス データベースを作成すると、復元された geo リストア バックアップが格納されます。

### <a name="geo-restore-by-using-powershell"></a>PowerShell を使用した geo リストア

#### <a name="sql-database"></a>SQL Database

単一データベースに対して geo リストアを実行する方法を示す PowerShell スクリプトについては、[PowerShell を使用して以前の時点に単一データベースを復元する](scripts/restore-database-powershell.md)方法に関するページを参照してください。

#### <a name="sql-managed-instance"></a>SQL Managed Instance

マネージド インスタンス データベースに対して geo リストアを実行する方法を示す PowerShell スクリプトについては、「[PowerShell を使用して Managed Instance データベースを別の geo リージョンに復元する](../managed-instance/scripts/restore-geo-backup.md)」を参照してください。

### <a name="geo-restore-considerations"></a>geo リストアに関する考慮事項

geo セカンダリ データベースでは、ポイントインタイム リストアを実行できません。 これを実行できるのは、プライマリ データベースだけです。 geo リストアを使用して障害から復旧する方法の詳細については、[障害からの復旧](../../key-vault/general/disaster-recovery-guidance.md)に関するページを参照してください。

> [!IMPORTANT]
> geo リストアは、SQL Database と SQL Managed Instance で使用できる最も基本的なディザスター リカバリー ソリューションです。 これは、自動的に作成される geo レプリケートされたバックアップに依存し、目標復旧時点 (RPO) は最大 1 時間、推定復旧時間は最大 12 時間です。 リージョンの停止後は、需要が急激に増加する可能性があるため、目的のデータベースを復元する容量がターゲット リージョンに確保される保証はありません。 アプリケーションで使用されているデータベースが比較的小さく、アプリケーションがビジネスにとって重要でなければ、geo リストアは適切なディザスター リカバリー ソリューションです。 
>
> 大規模なデータベースを必要とし、事業継続性を保証する必要があるビジネス上不可欠なアプリケーションの場合は、[自動フェールオーバー グループ](auto-failover-group-overview.md)を使用します。 大幅に低い RPO と目標復旧時間が実現され、容量が常に保証されます。 
>
> 事業継続に関する選択肢の詳細については、[ビジネス継続性の概要](business-continuity-high-availability-disaster-recover-hadr-overview.md)に関するページを参照してください。

## <a name="programmatic-recovery-using-automated-backups"></a>自動バックアップを使用したプログラムによる復旧

Azure PowerShell または REST API を復旧に使用することもできます。 次の表では、使用できるコマンド セットについて説明します。

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは SQL Database と SQL Managed Instance によって引き続きサポートされていますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと Azure Resource Manager モジュールのコマンドの引数は、大部分が同じです。

#### <a name="sql-database"></a>SQL Database

スタンドアロン データベースまたはプールされたデータベースを復元するには、「[Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase)」を参照してください。

  | コマンドレット | 説明 |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |1 つまたは複数のデータベースを取得します。 |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | 復元する削除済みデータベースを取得します。 |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |データベースの geo 冗長バックアップを取得します。 |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |データベースを復元します。 |

  > [!TIP]
  > データベースのポイントインタイム リストアの実行方法を示すサンプル PowerShell スクリプトについては、[PowerShell を使用したデータベースの復元](scripts/restore-database-powershell.md)に関するページを参照してください。

#### <a name="sql-managed-instance"></a>SQL Managed Instance

マネージド インスタンス データベースを復元するには、「[Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)」を参照してください。

  | コマンドレット | 説明 |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |1 つまたは複数のマネージド インスタンスを取得します。 |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | インスタンス データベースを取得します。 |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |インスタンス データベースを復元します。 |

### <a name="rest-api"></a>REST API

REST API を使用してデータベースを復元するには:

| API | 説明 |
| --- | --- |
| [REST (createMode=Recovery)](/rest/api/sql/databases) |データベースを復元します。 |
| [Get Create or Update Database Status](/rest/api/sql/operations) |復元操作中にステータスを返します。 |

### <a name="azure-cli"></a>Azure CLI

#### <a name="sql-database"></a>SQL Database

Azure CLI を使用してデータベースを復元するには、「[az sql db restore](/cli/azure/sql/db#az-sql-db-restore)」を参照してください。

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Azure CLI を使用してマネージド インスタンス データベースを復元するには、「[az sql db restore](/cli/azure/sql/midb#az-sql-midb-restore)」を参照してください。

## <a name="summary"></a>まとめ

自動バックアップは、ユーザー エラーやアプリケーション エラー、偶発的なデータベースの削除、および長期間にわたる障害からデータベースを保護します。 この組み込みの機能は、すべてのサービス レベルとコンピューティング サイズで使用できます。

## <a name="next-steps"></a>次のステップ

- [ビジネス継続性の概要](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- [SQL Database 自動バックアップ](automated-backups-overview.md)
- [長期保存](long-term-retention-overview.md)
- より迅速な復旧オプションについては、[アクティブ geo レプリケーション](active-geo-replication-overview.md)または[自動フェールオーバー グループ](auto-failover-group-overview.md)に関する記事を参照してください。

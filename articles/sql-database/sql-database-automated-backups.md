---
title: Azure SQL Database のバックアップ - 自動、geo 冗長 | Microsoft Docs
description: SQL Database は数分ごとにローカル データベースをバックアップし、Azure 読み取りアクセス geo 冗長ストレージを利用して地理的冗長性を提供します。
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
ms.date: 06/27/2019
ms.openlocfilehash: 1eeb37ce74b3e2f57588197d6bb88f59944c61cf
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67460676"
---
# <a name="automated-backups"></a>自動バックアップ

SQL Database では 7 日から 35 日間に保存されるデータベース バックアップが自動的に作成され、データ センターを使用できない場合でも、Azure の[読み取りアクセス geo 冗長ストレージ (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) を使用して確実に保存されます。 これらのバックアップは自動的に作成されます。 データの不慮の破損または削除から保護するデータベース バックアップは、ビジネス継続性およびディザスター リカバリー戦略の最も重要な部分です。 セキュリティ規則で、長期間バックアップ (最長 10 年間) を利用できる必要がある場合は、Single Database と Elastic Pool で[長期保有](sql-database-long-term-retention.md)を構成できます。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>SQL Database バックアップとは

SQL Database は SQL Server 技術を利用して、[完全バックアップ](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server)を毎週、[差分バックアップ](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server)を 12 時間ごと、そして[トランザクション ログ バックアップ](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server)を 5 分から 10 分ごとに作成します。 バックアップは [RA-GRS ストレージ BLOB](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) に格納され、この BLOB はデータ センターの停止に対する保護のために[ペアのデータ センター](../best-practices-availability-paired-regions.md)にレプリケートされます。 データベースを復元するとき、どのバックアップを復元する必要があるかをサービスが判定します (完全、差分、トランザクション ログ)。

これらのバックアップを使用して、以下を行うことができます。

- **保持期間内の過去の特定の時点に既存のデータベースを復元する**。その際、Azure portal、Azure PowerShell、Azure CLI、または REST API を使用します。 Single Database および Elastic Pool では、この操作により、元のデータベースと同じサーバーに、新しいデータベースが作成されます。 Managed Instance では、この操作により、同じサブスクリプションに、データベースのコピーか、同じ Managed Instance または異なる Managed Instance のコピーを作成できます。
  - **[バックアップ保有期間の変更](#how-to-change-the-pitr-backup-retention-period)** 。7 から 35 日までの値を使用して、ご自身のバックアップ ポリシーを構成できます。
  - **長期アイテム保持ポリシーの変更 (最大 10 年)** 。[Azure portal](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies) または [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-configure-long-term-retention-policies-and-restore-backups) を使用して、Single Database および Elastic Pool のポリシーを変更できます。
- **削除したデータベースを、削除された時点に復元する**。また、保持期間内の任意の時点に復元することもできます。 削除されたデータベースは、元のデータベースが作成された論理サーバーまたは Managed Instance にのみ復元できます。
- **別の地理的リージョンにデータベースを復元する**。 geo リストアにより、サーバーやデータベースにアクセスできないときに、地理的な障害から復旧できます。 世界中のどこでも、あらゆる既存のサーバーで新しいデータベースを作成します。
- **特定の長期バックアップからデータベースを復元する**。データベースが長期アイテム保持ポリシー (LTR) で構成されている場合に、Single Database または Elastic Pool で復元できます。 LTR により、[Azure portal](sql-database-long-term-backup-retention-configure.md#view-backups-and-restore-from-a-backup-using-azure-portal) または [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-configure-long-term-retention-policies-and-restore-backups) を使用して、コンプライアンスの要求を満たすため、またはアプリケーションの古いバージョンを実行するために、古いバージョンのデータベースを復元できます。 詳細については、「[長期保存](sql-database-long-term-retention.md)」をご覧ください。
- 復元を実行するには、[バックアップからのデータベースの復元](sql-database-recovery-using-backups.md)に関する記事を参照してください。

> [!NOTE]
> Azure Storage の " *レプリケーション* " という用語は、ある場所から別の場所にファイルをコピーすることを表します。 SQL の *データベース レプリケーション* は、複数のセカンダリ データベースとプライマリ データベースとの同期を保つことを意味します。

次の例を使用して、これらの操作のいくつかを試すことができます。

| | Azure ポータル | Azure PowerShell |
|---|---|---|
| バックアップ保有期間を変更する | [Single Database](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-the-azure-portal) <br/> [Managed Instance](sql-database-automated-backups.md#change-pitr-for-a-managed-instance) | [Single Database](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[Managed Instance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| 長期のバックアップ保有期間を変更する | [1 つのデータベース](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Managed Instance - N/A  | [Single Database](sql-database-long-term-backup-retention-configure.md#use-powershell-to-configure-long-term-retention-policies-and-restore-backups)<br/>Managed Instance - N/A  |
| 特定の時点からデータベースを復元する | [1 つのデータベース](sql-database-recovery-using-backups.md#point-in-time-restore) | [1 つのデータベース](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Managed Instance](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| 削除済みデータベースの復元 | [1 つのデータベース](sql-database-recovery-using-backups.md#deleted-database-restore-using-the-azure-portal) | [1 つのデータベース](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Managed Instance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Azure Blob Storage からデータベースを復元する | Single Database - N/A <br/>Managed Instance - N/A  | Single Database - N/A <br/>[Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="how-long-are-backups-kept"></a>バックアップの保持期間

各 SQL Database には、7 日～ 35 日まで (購入モデルとサービス レベルによって異なります) の既定のバックアップの保持期間があります。 SQL Database サーバー上のデータベースに対するバックアップの保持期間は更新することができます。 詳細については、[バックアップのリテンション期間の変更](#how-to-change-the-pitr-backup-retention-period)に関するセクションを参照してください。

データベースを削除した場合、SQL Database はオンライン データベースの場合と同じようにバックアップを保持します。 たとえば、7 日間のリテンション期間のある基本的なデータベースを削除すると、4 日間保存されているバックアップはさらに 3 日間保存されます。

最大の保有期間より長くバックアップを保持する必要がある場合は、データベースに 1 つまたは複数のより長い保有期間を追加するようにバックアップのプロパティを変更できます。 詳細については、「[長期保存](sql-database-long-term-retention.md)」をご覧ください。

> [!IMPORTANT]
> SQL Database をホストする Azure SQL サーバーを削除すると、サーバーに属するすべてのエラスティック プールとデータベースも削除され、復元できなくなります。 削除されたサーバーを復元することはできません。 長期保有を構成した場合、LTR を使用したデータベースのバックアップは削除されず、これらのデータベースを復元することができます。

### <a name="default-backup-retention-period"></a>既定のバックアップの保有期間

#### <a name="dtu-based-purchasing-model"></a>DTU ベースの購入モデル

DTU ベースの購入モデルを使用して作成されたデータベースのリテンション期間は、サービス レベルに依存します。

- Basic サービスレベルの場合、**1** 週間です。
- Standard サービスレベルの場合、**5** 週間です。
- Premium サービスレベルの場合、**5** 週間です。

#### <a name="vcore-based-purchasing-model"></a>仮想コアベースの購入モデル

[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)を使用している場合、既定のバックアップの保有期間は **7** 日間です (単一、プール、およびインスタンス データベースの場合)。 すべての Azure SQL データベース (単一、プール、およびインスタンス データベース) の場合、[バックアップの保有期間を最大 35 日に変更](#how-to-change-the-pitr-backup-retention-period)できます。

> [!WARNING]
> 現在の保持期間を短縮した場合、新しい保持期間より古いすべての既存のバックアップは使用できなくなります。 現在のリテンション期間を延長した場合、SQL Database は、より長いリテンション期間に達するまでに、既存のバックアップを保持します。

## <a name="how-often-do-backups-happen"></a>バックアップが行われる頻度

### <a name="backups-for-point-in-time-restore"></a>ポイントインタイム リストアのバックアップ

SQL Database では、完全バックアップ、差分バックアップ、トランザクション ログ バックアップを自動的に作成して、ポイントインタイム リストア (PITR) のセルフ サービスをサポートします。 完全データベース バックアップは毎週、差分データベース バックアップは一般的に 12 時間ごとに、トランザクション ログ バックアップは通常、5 - 10 分ごとに作成されます。頻度は、コンピューティング サイズとデータベース アクティビティの量に基づきます。 初回の完全バックアップは、データベースの作成直後にスケジュールされます。 通常この操作は 30 分以内に終了しますが、データベースのサイズが大きい場合はそれ以上かかることがあります。 たとえば、復元されたデータベースまたはデータベースのコピーでは、初期バックアップに時間がかかります。 初回の完全バックアップ以降のバックアップは、すべて自動的にスケジュールされ、バックグラウンドで自動的に管理されます。 データベースのバックアップの正確なタイミングは、全体的なシステムのワークロードのバランスを図りながら SQL Database サービスによって決定されます。 バックアップ ジョブを変更または無効化することはできません。 

PITR バックアップは、geo 冗長であり、[Azure Storage のリージョン間レプリケーション](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)によって保護されます。

詳細については、「[ポイントインタイム リストア](sql-database-recovery-using-backups.md#point-in-time-restore)」をご覧ください。

### <a name="backups-for-long-term-retention"></a>長期保有のためのバックアップ

単一データベースとプールされたデータベースには、Azure Blob Storage に対する最大 10 年の完全バックアップの長期保有 (LTR) を構成するオプションが用意されています。 LTR ポリシーが有効になっている場合、週次の完全バックアップは自動的に別の RA-GRS ストレージ コンテナーにコピーされます。 さまざまなコンプライアンス要件を満たすために、毎週、毎月、毎年のバックアップに対して異なったリテンション期間を選択することができます。 ストレージの使用量は、選択したバックアップの頻度とリテンション期間によって異なります。 LTR ストレージのコストは、[LTR 料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=sql-database)を使用して見積もることができます。

PITR と同じように、LTR バックアップは、geo 冗長であり、[Azure Storage のリージョン間レプリケーション](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)によって保護されます。

詳細については、「[Long-term backup retention](sql-database-long-term-retention.md)」(長期バックアップ リテンション) をご覧ください。

## <a name="storage-costs"></a>ストレージ コスト
既定ではデータベースの 7 日分の自動バックアップが、RA-GRS Standard Blob Storage にコピーされます。 このストレージは、毎週の完全バックアップ、毎日の差分バックアップ、5 分ごとにコピーされるトランザクション ログ バックアップによって使用されます。 トランザクション ログのサイズは、データベースの変化率によって異なります。 データベース サイズの 100% に等しい最小ストレージ量は、追加料金なしで提供されます。 100% を超えるバックアップ ストレージの使用については、月あたりの GB 数で請求されます。

ストレージの価格について詳しくは、[価格](https://azure.microsoft.com/pricing/details/sql-database/single/)のページをご覧ください。 

## <a name="are-backups-encrypted"></a>バックアップの暗号化

データベースが TDE で暗号化されている場合、LTR バックアップを含むバックアップは保存中に自動的に暗号化されます。 Azure SQL データベースに対して TDE が有効になっているとき、バックアップも暗号化されます。 新しい Azure SQL データベースはすべて、既定で TDE が有効になった状態で構成されます。 TDE に関する詳細については、「[Azure SQL Database での Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)」をご覧ください。

## <a name="how-does-microsoft-ensure-backup-integrity"></a>Microsoft はどのようにバックアップの整合性を保証しているか

Azure SQL Database のエンジニアリング チームは、サービス全体でデータベースの自動データベース バックアップの復元の自動テストを継続的に行っています。 復元時に、データベースは DBCC CHECKDB を使用した整合性チェックも受けます。 整合性チェック中に問題が見つかると、エンジニアリング チームにアラートが送信されます。 Azure SQL Database におけるデータ整合性の詳細については、「[Data Integrity in Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)」 (Azure SQL Database でのデータ整合性) を参照してください。

## <a name="how-do-automated-backups-impact-compliance"></a>自動バックアップによるコンプライアンスへの影響

PITR リテンション期間が 35 日間である DTU ベースのサービス レベルから仮想コア ベースのサービス レベルにデータベースを移行した場合、アプリケーションのデータ リカバリ ポリシーが損なわれないように、PITR リテンション期間が保持されます。 既定のリテンション期間では、お客様のコンプライアンス要件を満たしていない場合は、PowerShell または REST API を使用して PITR リテンション期間を変更できます。 詳細については、[バックアップのリテンション期間の変更](#how-to-change-the-pitr-backup-retention-period)に関するセクションを参照してください。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>PITR のバックアップの保有期間を変更する方法

既定の PITR バックアップ保持期間は、Azure portal、PowerShell、または REST API を使用して変更できます。 サポートされる値は7、14、21、28、または 35 日間です。 次の例では、PITR リテンション期間を 28 日間に変更する方法を示します。

> [!NOTE]
> これらの API は PITR リテンション期間にのみ影響を与えます。 データベースの LTR を構成した場合、それには影響ありません。 LTR の保持期間を変更する方法については、[長期保有](sql-database-long-term-retention.md)に関するページを参照してください。

### <a name="change-pitr-backup-retention-period-using-the-azure-portal"></a>Azure portal を使用して PITR のバックアップの保有期間を変更する

Azure portal を使用して PITR バックアップ保有期間を変更するには、portal 内で保持期間を変更するサーバー オブジェクトに移動し、変更するサーバー オブジェクトに基づいて適切なオプションを選択します。

#### <a name="change-pitr-for-a-sql-database-server"></a>SQL Database サーバーの PITR の変更

![Azure portal の PITR の変更](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="change-pitr-for-a-managed-instance"></a>マネージド インスタンスの PITR の変更

![Azure portal の PITR の変更](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

### <a name="change-pitr-backup-retention-period-using-powershell"></a>PowerShell を使用して PITR バックアップ リテンション期間を変更する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>REST API を使用して PITR リテンション期間を変更する

#### <a name="sample-request"></a>要求のサンプル

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>要求本文

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>応答のサンプル

状態コード:200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

詳細については、[バックアップの保有期間の REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

- データの不慮の破損または削除から保護するデータベース バックアップは、ビジネス継続性およびディザスター リカバリー戦略の最も重要な部分です。 その他の Azure SQL Database ビジネス継続性ソリューションの概要については、[ビジネス継続性の概要](sql-database-business-continuity.md)に関する記事を参照してください。
- Azure Portal を使用して特定の時点に復元する方法については、[Azure Portal を使用したデータベースのポイントインタイム リストア](sql-database-recovery-using-backups.md)に関するページをご覧ください。
- PowerShell を使用して特定の時点に復元する方法については、[PowerShell を使用したデータベースのポイントインタイム リストア](scripts/sql-database-restore-database-powershell.md)に関するページをご覧ください。
- Azure Blob Storage に長期保存される自動バックアップを Azure portal を使用して構成、管理、復元する方法については、[Azure portal を使用した長期バックアップ保存の管理](sql-database-long-term-backup-retention-configure.md)に関する記事を参照してください。
- Azure Blob Storage に長期保有される自動バックアップを PowerShell を使用して構成、管理、復元する方法については、[PowerShell を使用した長期バックアップ保有の管理](sql-database-long-term-backup-retention-configure.md)に関する記事を参照してください。

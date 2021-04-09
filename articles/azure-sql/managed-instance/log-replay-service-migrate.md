---
title: Log Replay Service を使用して SQL Managed Instance にデータベースを移行する
description: Log Replay Service を使用して SQL Server から SQL Managed Instance にデータベースを移行する方法について説明します
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 03/01/2021
ms.openlocfilehash: 0bc00aea67fa2f71599ee62e657e1ca1b0627681
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199851"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-by-using-log-replay-service-preview"></a>Log Replay Service (プレビュー) を使用して SQL Server から SQL Managed Instance にデータベースを移行する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

この記事では、現在パブリック プレビュー中の Log Replay Service (LRS) を使用して、Azure SQL Server (2008 から 2019 まで) から SQL Managed Instance へのデータベースの移行を手動で構成する方法について説明します。 LRS は、SQL Server ログ配布テクノロジに基づいた SQL Managed Instance に対応したクラウド サービスです。 

[Azure Database Migration Service](/azure/dms/tutorial-sql-server-to-managed-instance) と LRS の基盤に使用される移行テクノロジはどちらも同じで、使用される API も同じです。 LRS をリリースすることで、オンプレミスの SQL Server と SQL Managed Instance 間で複雑なカスタム移行とハイブリッド アーキテクチャをさらに有効にすることができます。

## <a name="when-to-use-log-replay-service"></a>Log Replay Service を使用する場合

移行に Azure Database Migration Service を使用できない場合、PowerShell、Azure CLI コマンドレット、または API で LRS を直接使用して、SQL Managed Instance へのデータベースの移行を手動で構築およびオーケストレーションできます。 

次の場合に LRS の使用を検討してください。
- データベース移行プロジェクトでより高度な制御が必要。
- 移行カットオーバーで許容されるダウンタイムの範囲が少ない。
- 使用環境に Database Migration Service の実行可能ファイルをインストールできない。
- Database Migration Service の実行可能ファイルに、データベースのバックアップへのファイル アクセス権がない。
- ホスト OS にアクセスできない、または管理者特権がない。
- 使用環境から Azure にネットワーク ポートを開くことができない。
- バックアップが、`TO URL` オプションを通じて Azure Blob Storage に直接保存されている。
- 差分バックアップを使用する必要がある。

> [!NOTE]
> Database Migration Service を使用して、SQL Server から SQL Managed Instance へのデータベースの移行を自動化することをお勧めします。 このサービスでは、ログ配布が `NORECOVERY` モードに設定されている同じ LRS クラウド サービスがバックエンドで使用されています。 ご自身のシナリオが Database Migration Service によって完全にはサポートされていない場合は、LRS を手動で使用して移行をオーケストレーションすることを検討してください。

## <a name="how-it-works"></a>しくみ

LRS を使用してクラウドにデータベースを移行するカスタム ソリューションを構築するには、このセクションで後述する図と表に示されているオーケストレーション手順を実行する必要があります。

この移行は、SQL Server でデータベースの完全バックアップを `CHECKSUM` を有効にして作成する手順と、バックアップ ファイルを Azure Blob Storage にコピーする手順で構成されます。 LRS は、バックアップ ファイルを Blob Storage から SQL Managed Instance に復元するのに使用されます。 Blob Storage は、SQL Server と SQL Managed Instance の中間ストレージです。

完全バックアップが復元された後は、LRS によって Blob Storage で新しい差分またはログ バックアップの追加が監視されます。 これらの新しいファイルは、LRS によって自動的に復元されます。 このサービスを使用すると、SQL Managed Instance で復元中のバックアップ ファイルの進行状況を監視したり、必要に応じてプロセスを停止したりすることができます。

LRS では、バックアップ ファイル固有の名前付け規則は必要ありません。 Blob Storage に配置されるすべてのファイルがスキャンされ、ファイル ヘッダーのみを読み取ってバックアップ チェーンが構築されます。 データベースは、移行プロセス中は "復元中" 状態になります。 データベースは [NORECOVERY](/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) モードで復元され、移行プロセスが完全に完了するまで読み取りまたは書き込みに使用することはできません。 

複数のデータベースを移行する場合は、次のことを行う必要があります。
 
- 各データベースのバックアップを Blob Storage の個別のフォルダーに配置します。
- データベースごとに LRS を個別に開始します。
- Blob Storage のフォルダーごとに個別のパスを指定します。 

LRS は "*オートコンプリート*" または "*連続*" モードで開始できます。 オートコンプリート モードで開始した場合は、指定した最後のバックアップ ファイルが復元されると、移行が自動的に終了します。 LRS を連続モードで開始した場合は、新しいバックアップ ファイルが追加されると、サービスによって連続的に復元され、移行は手動カットオーバーのみで終了します。 

手動カットオーバーは、ログ末尾の最後のバックアップが取得され、SQL Managed Instance で復元済みとして表示された後に実行することをお勧めします。 最後のカットオーバー手順が終わると、データベースがオンラインになり、SQL Managed Instance で読み取りと書き込みができるようになります。

オートコンプリートによって自動で、またはカットオーバーによって手動で LRS が停止されると、SQL Managed Instance でオンラインにされたデータベースの復元プロセスは再開できなくなります。 オートコンプリートまたはカットオーバーによって移行が完了した後に、追加のバックアップ ファイルを復元するには、データベースを削除する必要があります。 さらに、LRS を再起動して、バックアップ チェーン全体の復元を最初から行う必要もあります。

:::image type="content" source="./media/log-replay-service-migrate/log-replay-service-conceptual.png" alt-text="SQL Managed Instance に対して行われる Log Replay Service によるオーケストレーション手順を示す図。" border="false":::
    
| 操作 | 説明 |
| :----------------------------- | :------------------------- |
| **1.データベース バックアップを SQL Server から Blob Storage にコピーします**。 | [Azcopy](/azure/storage/common/storage-use-azcopy-v10) または [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使用して、SQL Server の完全バックアップ、差分バックアップ、ログ バックアップを Blob Storage コンテナーにコピーします。 <br /><br />任意のファイル名を使用します。 LRS では固有のファイル名前付け規則は必要ありません。<br /><br />複数のデータベースを移行する場合は、データベースごとに個別のフォルダーが必要です。 |
| **2.クラウドで LRS を開始します**。 | サービスの再起動は、コマンドレット: PowerShell ([start-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay)) または Azure CLI ([az_sql_midb_log_replay_start cmdlets](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)) のいずれかを使用して実行できます。 <br /><br /> Blob Storage 上のバックアップ フォルダーを指す各データベースで、LRS を個別に開始します。 <br /><br /> サービスを開始すると、Blob Storage コンテナーからバックアップが取得され、復元が SQL Managed Instance で開始されます。<br /><br /> LRS を連続モードで開始した場合は、最初にアップロードされたすべてのバックアップが復元された後、サービスにより、フォルダーにアップロードされる新しいファイルが監視されます。 サービスが停止されるまで、ログ シーケンス番号 (LSN) チェーンに基づいてログが連続して適用されます。 |
| **2.1.操作の進行状況を監視します**。 | 復元操作の進行状況は、コマンドレット: PowerShell ([get-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay)) または Azure CLI ([az_sql_midb_log_replay_show cmdlets](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)) のいずれかを使用して監視できます。 |
| **2.2.必要に応じて、操作を停止します**。 | 移行プロセスを停止する必要がある場合は、コマンドレット: PowerShell ([stop-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay)) または Azure CLI ([az_sql_midb_log_replay_stop](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop)) のいずれかを使用できます。 <br /><br /> 操作を停止すると、SQL Managed Instance で復元しようとしているデータベースが削除されます。 操作を停止した後に、データベースの LRS を再開することはできません。 移行プロセスを最初からやり直す必要があります。 |
| **3.準備ができたら、クラウドにカットオーバーします**。 | アプリケーションとワークロードを停止します。 ログ末尾の最後のバックアップを取得し、Azure Blob Storage にアップロードします。<br /><br /> カットオーバーを完了するには、コマンドレット: PowerShell ([complete-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay)) または Azure CLI ([az_sql_midb_log_replay_complete](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete)) のいずれかを使用して LRS `complete` 操作を開始します。 この操作により LRS が停止し、データベースがオンラインになって、SQL Managed Instance で読み取りと書き込みを使用できるようになります。<br /><br /> SQL Server から SQL Managed Instance を指すようにアプリケーションの接続文字列を再設定します。 |

## <a name="requirements-for-getting-started"></a>作業を開始するための要件

### <a name="sql-server-side"></a>SQL Server 側
- SQL Server (2008 から 2019 まで)
- データベースの完全バックアップ (1 つまたは複数のファイル)
- 差分バックアップ (1 つまたは複数のファイル)
- ログ バックアップ (トランザクション ログ ファイル用に分割されていない)
- バックアップの `CHECKSUM` が有効になっている (必須)

### <a name="azure-side"></a>Azure 側
- PowerShell Az.SQL モジュール バージョン 2.16.0 以降 ([インストール](https://www.powershellgallery.com/packages/Az.Sql/)、または [Azure Cloud Shell](/azure/cloud-shell/) 経由でアクセス)
- Azure CLI バージョン 2.19.0 以降 ([インストール](/cli/azure/install-azure-cli))
- プロビジョニングされた Azure Blob Storage コンテナー
- BLOB ストレージ コンテナーに対して読み取りとリストのアクセス許可が付与された Shared Access Signature (SAS) セキュリティ トークンが生成されている

### <a name="migration-of-multiple-databases"></a>複数のデータベースの移行
異なるデータベースのバックアップ ファイルは、Blob Storage 上の個別のフォルダーに配置する必要があります。

LRS は、Blob Storage 上の適切なフォルダーを指すデータベースごとに個別に開始します。 LRS は、1 つのマネージド インスタンスごとに最大 100 個の同時復元プロセスをサポートできます。

### <a name="azure-rbac-permissions"></a>Azure RBAC アクセス許可
指定したクライアントを介して LRS を実行するには、次のいずれかの Azure ロールが必要です。
- サブスクリプションの所有者ロール
- [Managed Instance 共同作成者](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)ロール
- 次のアクセス許可を持つカスタム ロール: `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>ベスト プラクティス

推奨するベスト プラクティスを次に示します。
- [Data Migration Assistant](/sql/dma/dma-overview) を実行して、データベースを SQL Managed Instance に移行する準備ができていることを確認します。 
- 1 つのファイルを使用するのではなく、完全バックアップと差分バックアップを複数のファイルに分割します。
- バックアップの圧縮を有効にします。
- リリースされる最新のコマンドレットに常に更新されるため、Cloud Shell を使用してスクリプトを実行します。
- LRS を開始してから 47 時間以内に移行を完了するように計画します。 これは、システム管理されているソフトウェア パッチのインストールが行われない猶予期間です。

> [!IMPORTANT]
> - 移行プロセスが完了するまで、LRS で復元中のデータベースを使用することはできません。 
> - 移行中のデータベースに対する読み取り専用アクセスは、LRS ではサポートされていません。
> - LRS では復元プロセスの再開がサポートされていないため、移行が完了すると移行プロセスが終了します。

## <a name="steps-to-execute"></a>実行する手順

### <a name="make-backups-of-sql-server"></a>SQL Server のバックアップを作成する

次のいずれかのオプションを使用して、SQL Server のバックアップを作成できます。

- お使いの環境で Blob Storage への直接バックアップが制限されている場合は、ローカル ディスク ストレージにバックアップしてから、ファイルを Azure Blob Storage にアップロードします。
- お使いの環境とセキュリティ手順により許可されている場合は、T-SQL の `TO URL` オプションを使用して Blob Storage に直接バックアップします。 

完全復旧モードに移行するデータベースを、ログ バックアップを許可するように設定します。

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

ローカル ストレージでデータベースの完全バックアップ、差分バックアップ、ログ バックアップを手動で作成するには、以下の T-SQL サンプル スクリプトを使用します。 `CHECKSUM` オプションは LRS に必須であるため、これが有効になっていることを確認します。

```SQL
-- Example of how to make a full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example of how to make a differential database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff.bak'
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to the local disk
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log.trn'
WITH COMPRESSION, CHECKSUM
GO
```

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

Azure Blob Storage は、SQL Server と SQL Managed Instance 間のバックアップ ファイルの中間ストレージとして使用されます。 ストレージ アカウント内に新しいストレージ アカウントと BLOB コンテナーを作成するには、次の手順を行います。

1. [ストレージ アカウントの作成](../../storage/common/storage-account-create.md?tabs=azure-portal)。
2. ストレージ アカウント内に [BLOB コンテナーを作成します](../../storage/blobs/storage-quickstart-blobs-portal.md)。

### <a name="copy-backups-from-sql-server-to-blob-storage"></a>SQL Server のバックアップを Blob Storage にコピーする

LRS を使用したマネージド インスタンスへのデータベースの移行では、次の方法を使用して Blob Storage にバックアップをアップロードできます。
- SQL Server ネイティブの [BACKUP TO URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) 機能を使用する
- [Azcopy](/azure/storage/common/storage-use-azcopy-v10) または [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer) を使用して、バックアップを BLOB コンテナーにアップロードする
- Azure portal で Storage Explorer を使用する

### <a name="make-backups-from-sql-server-directly-to-blob-storage"></a>SQL Server のバックアップを Blob Storage に直接作成する
SQL Server のネイティブの [BACKUP TO URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) が企業およびネットワーク ポリシーで許可されている場合は、代わりにこれを使用して SQL Server のバックアップを Blob Storage に直接作成できます。 このオプションを実行できる場合は、ローカル ストレージにバックアップを作成して Blob Storage にアップロードする必要はありません。

この操作では、まず最初の手順として Blob Storage の SAS 認証トークンを生成し、そのトークンを SQL Server にインポートする必要があります。 2 番目の手順で、T-SQL の `TO URL` オプションを使用してバックアップを作成します。 すべてのバックアップが、`CHEKSUM` オプションが有効な状態で作成されていることを確認します。

参考のために、Blob Storage へのバックアップを作成するサンプル コードを以下に示します。 この例には、SAS トークンをインポートする手順は含まれていません。 SAS トークンを生成して SQL Server にインポートする方法など、詳細な手順については、[SQL Server での Azure Blob Storage の使用](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage)に関する記事をご覧ください。 

```SQL
-- Example of how to make a full database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO
-- Example of how to make a differential database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_diff.bak'  
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to a URL
BACKUP LOG [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_log.trn'  
WITH COMPRESSION, CHECKSUM
```

### <a name="generate-a-blob-storage-sas-authentication-token-for-lrs"></a>Blob Storage の LRS 用の SAS 認証トークンを生成する

Azure Blob Storage は、SQL Server と SQL Managed Instance 間のバックアップ ファイルの中間ストレージとして使用されます。 LRS の場合は、リストと読み取りのアクセス許可のみが付与された SAS 認証トークンを生成する必要があります。 このトークンを使用することで、LRS から、Blob Storage にアクセスし、バックアップ ファイルを使用して SQL Managed Instance に復元することができます。 

トークンを生成するには、次の手順を実行します。

1. Azure portal から Storage Explorer を開きます。
2. **[BLOB コンテナー]** を展開します。
3. BLOB コンテナーを右クリックして、 **[Shared Access Signature の取得]** を選択します。

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-01.png" alt-text="S A S 認証トークンを生成するための選択内容を示すスクリーンショット。":::

4. トークンの有効期限までの期間を選択します。 移行期間中はトークンが確実に有効であるようにします。
5. トークンのタイム ゾーン (UTC または現地時間) を選択します。
    
   > [!IMPORTANT]
   > トークンとご利用のマネージド インスタンスのタイム ゾーンが一致していない場合があります。 タイム ゾーンを考慮した適切な有効期間を SAS トークンに確実に設定するようにします。 可能であれば、タイム ゾーンを、移行予定時間枠のより早い時間とより遅い時間に設定します。
6. **読み取り** と **リスト** のアクセス許可のみを選択します。

   > [!IMPORTANT]
   > 他のアクセス許可は選択しないでください。 行うと、LRS は起動しません。 このセキュリティ要件は仕様です。
7. **[作成]** を選択します。

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-02.png" alt-text="[作成] ボタンと、[S A S トークンの有効期限]、[タイム ゾーン]、および [アクセス許可] の選択内容を示すスクリーンショット。":::

指定した有効期限を持つ SAS 認証が生成されます。 次のスクリーンショットに示すように、URI バージョンのトークンが必要です。

:::image type="content" source="./media/log-replay-service-migrate/lrs-generated-uri-token.png" alt-text="U R I バージョンの S A S トークンを示すスクリーンショット。":::

### <a name="copy-parameters-from-the-sas-token"></a>SAS トークンからパラメーターをコピーする

SAS トークンを使用して LRS を開始する前に、その構造を理解しておく必要があります。 生成された SAS トークンの URI は、次の例に示すように、疑問符 (`?`) で区切られた 2 つの部分で構成されています。

:::image type="content" source="./media/log-replay-service-migrate/lrs-token-structure.png" alt-text="Log Replay Service で生成された S A S トークンの U R I の例。" border="false":::

`https://` から疑問符 (`?`) までの前半部分は、LRS への入力として指定する `StorageContainerURI` パラメーターに使用します。 これにより、データベース バックアップ ファイルが格納されているフォルダーの情報が LRS に提供されます。

疑問符 (`?`) の後から文字列の終わりまでの後半部分は、`StorageContainerSasToken` パラメーターです。 これが実際の署名付き認証トークンで、指定した期間有効です。 この部分は、必ずしも例のように `sp=` で始まる必要はありません。 実際のケースでは異なる場合があります。

次のようにパラメーターをコピーします。

1. トークンの前半部分、つまり `https://` から疑問符 (`?`) までをコピーします。 これは、LRS を開始するための PowerShell または Azure CLI の `StorageContainerUri` パラメーターとして使用します。

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png" alt-text="トークンの前半部分をコピーする方法を示すスクリーンショット。":::

2. トークンの後半部分、つまり疑問符 (`?`) から文字列の最後までをコピーします。 これは、LRS を開始するための PowerShell または Azure CLI の `StorageContainerSasToken` パラメーターとして使用します。

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png" alt-text="トークンの後半部分をコピーする方法を示すスクリーンショット。":::
   
> [!NOTE]
> トークンのどちらの部分をコピーする際も、疑問符は含めないようにしてください。

### <a name="log-in-to-azure-and-select-a-subscription"></a>Azure にログインしてサブスクリプションを選択する

次の PowerShell コマンドレットを使用して Azure にログインします。

```powershell
Login-AzAccount
```

次の PowerShell コマンドレットを使用して、マネージド インスタンスが存在する適切なサブスクリプションを選択します。

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>移行を開始する

移行を開始するには、LRS を起動します。 サービスはオートコンプリートまたは連続モードで開始できます。 

オートコンプリート モードを使用すると、指定した最後のバックアップ ファイルが復元されると、移行が自動的に終了します。 このオプションでは、start コマンドに最後のバックアップ ファイルのファイル名を指定する必要があります。 

連続モードを使用すると、追加される新しいバックアップ ファイルがサービスによって継続的に復元されます。 移行は手動カットオーバーのみで終了します。 

### <a name="start-lrs-in-autocomplete-mode"></a>LRS をオートコンプリート モードで開始する

LRS をオートコンプリート モードで開始するには、次の PowerShell または Azure CLI コマンドを使用します。 `-LastBackupName` パラメーターを使用して、最後のバックアップ ファイル名を指定します。 指定された最後のバックアップ ファイルを復元すると、サービスにより、カットオーバーが自動的に開始されます。

PowerShell を使用して LRS をオートコンプリート モードで開始する例を次に示します。

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoCompleteRestore `
    -LastBackupName "last_backup.bak"
```

Azure CLI を使用して LRS をオートコンプリート モードで開始する例を次に示します。

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>LRS を連続モードで開始する

PowerShell を使用して LRS を連続モードで開始する例を次に示します。

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Azure CLI を使用して LRS を連続モードで開始する例を次に示します。

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

PowerShell および CLI クライアントで LRS を連続モードで開始すると、同期されます。 これは、ジョブ開始の成否が API 応答により報告されるまでクライアントが待機することを意味します。 

この待機中、制御は、コマンドによってコマンド プロンプトに戻されません。 移行操作のスクリプトを実行していて、LRS start コマンドで、スクリプトの残りを続行するためにすぐに制御を戻す必要がある場合は、`-AsJob` スイッチを使用して PowerShell をバックグラウンド ジョブとして実行できます。 次に例を示します。

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

バックグラウンド ジョブを開始すると、ジョブが終了するまでに時間がかかる場合でも、ジョブ オブジェクトがすぐに返されます。 ジョブの実行中は、中断されることなく引き続きセッションで作業できます。 PowerShell をバックグラウンド ジョブとして実行する方法の詳細については、[PowerShell Start-Job](/powershell/module/microsoft.powershell.core/start-job#description) に関するドキュメントを参照してください。

同様に、Linux 上で Azure CLI コマンドをバックグラウンド プロセスとして開始するには、LRS start コマンドの最後にアンパサンド (`&`) 記号を使用します。

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> LRS を開始すると、システムで管理されているソフトウェア パッチが 47 時間停止されます。 この時間枠を過ぎると、自動化された次のソフトウェア パッチによって、LRS が自動的に停止されます。 その場合、移行を再開することはできないため、最初からやり直す必要があります。 

## <a name="monitor-the-migration-progress"></a>移行の進行状況を監視する

PowerShell を使用して移行の進行状況を監視するには、次のコマンドを使用します。

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Azure CLI を使用して移行の進行状況を監視するには、次のコマンドを使用します。

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>移行を停止する

移行を停止する必要がある場合は、次のコマンドレットを使用します。 移行を停止すると、SQL Managed Instance で復元しているデータベースが削除されるため、移行を再開することはできません。

PowerShell を使用して移行プロセスを停止するには、次のコマンドを使用します。

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Azure CLI を使用して移行プロセスを停止するには、次のコマンドを使用します。

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>移行を完了する (連続モード)

LRS を連続モードで開始した場合、すべてのバックアップが確実に復元されたら、カットオーバーを開始すると移行が完了します。 カットオーバー後、データベースが移行され、読み取りと書き込みのアクセスの準備が整います。

PowerShell を使用して LRS 連続モードで移行プロセスを完了するには、次のコマンドを使用します。

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Azure CLI を使用して LRS 連続モードで移行プロセスを完了するには、次のコマンドを使用します。

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="functional-limitations"></a>機能制限

LRS の機能制限は次のとおりです。
- 復元中のデータベースを、移行プロセス中の読み取り専用アクセスに使用することはできません。
- LRS を開始すると、システムで管理されているソフトウェア パッチが 47 時間ブロックされます。 この時間枠が終了した後、次のソフトウェア更新プログラムにより、LRS が停止されます。 その後、LRS を最初からやり直す必要があります。
- LRS では、SQL Server のデータベースを `CHECKSUM` オプションを有効にしてバックアップする必要があります。
- LRS によって使用される SAS トークンは Azure Blob Storage コンテナー全体に対して生成される必要があり、読み取りとリストのアクセス許可のみ付与されている必要があります。
- 異なるデータベースのバックアップ ファイルは、Blob Storage 上の個別のフォルダーに配置する必要があります。
- LRS は、Blob Storage 上のバックアップ ファイルがある個別のフォルダーを指すデータベースごとに個別に開始する必要があります。
- LRS は、1 つのマネージド インスタンスごとに最大 100 個の同時復元プロセスをサポートできます。

## <a name="troubleshooting"></a>トラブルシューティング

LRS を開始したら、監視コマンドレット (`get-azsqlinstancedatabaselogreplay` または `az_sql_midb_log_replay_show`) を使用して、操作の状態を確認します。 しばらくしても LRS が開始できず、エラーが発生する場合は、最も一般的な問題をご確認ください。

- SQL Managed Instance の既存のデータベースの名前が、SQL Server から移行しようとしているものの名前と同じになっていますか? この競合を解決するには、一方のデータベースの名前を変更します。
- SQL Server のデータベース バックアップを、`CHECKSUM` オプションを使用して作成しましたか?
- SAS トークンに付与されているアクセス許可は、LRS の読み取りとリストのみですか?
- コピーした LRS の SAS トークンは、疑問符 (`?`) の後の `sv=2020-02-10...` から始まる内容でしたか? 
- 移行の開始と完了の時間枠に、SAS トークンの有効期間が適用されていますか? SQL Managed Instance と SAS トークンに使用されるタイム ゾーンが異なることにより、不一致が発生するおそれがあります。 時間枠のトークンの有効期間を現在の日付より前および後に拡張して、SAS トークンを再生成してみてください。
- データベース名、リソース グループ名、マネージド インスタンス名のスペルは正しいですか?
- LRS をオートコンプリート モードで開始した場合、最後のバックアップ ファイルに有効なファイル名を指定しましたか?

## <a name="next-steps"></a>次のステップ
- [SQL Server から SQL Managed Instance に移行する](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md)方法について学ぶ。
- [SQL Server と SQL Managed Instance の相違点](transact-sql-tsql-differences-sql-server.md)について学ぶ。
- [Azure に移行するワークロードのコストとサイズ設定のベスト プラクティス](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)について学ぶ。

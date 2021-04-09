---
title: データベースのバックアップと復元 - Azure SQL Edge
description: Azure SQL Edge のバックアップと復元の機能について説明します。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 114be810ea50f984c3211291691b4c4dd45ac2c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395242"
---
# <a name="back-up-and-restore-databases-in-azure-sql-edge"></a>Azure SQL Edge でのデータベースのバックアップと復元 

Azure SQL Edge は、最新バージョンの Microsoft SQL データベース エンジンの上に構築されています。 コンテナーで実行される SQL Server on Linux と SQL Server で使用できるデータベースのバックアップおよび復元機能と同様の機能が提供されます。 バックアップおよび復元コンポーネントにより、Azure SQL Edge データベースに格納されるデータを保護するために不可欠な保護機能が提供されます。 

致命的なデータ損失のリスクを最小限に抑えるために、データベースを定期的にバックアップして、データの変更を定期的に保持する必要があります。 十分に計画されたバックアップおよび復元戦略は、さまざまな障害が原因で発生するデータ損失からデータベースを保護します。 一連のバックアップの復元とデータベースの回復を実行することでご自分の戦略をテストして、災害に効率的に対応するための準備を整えてください。

バックアップが重要である理由の詳細については、「[SQL Server データベースのバックアップと復元](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases/)」をご覧ください。

Azure SQL Edge では、ローカル ストレージと Azure BLOB の両方を、バックアップと復元の対象として使用できます。 詳細については、「[Azure Blob Storage を使用した SQL Server のバックアップと復元](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service/)」と「[SQL Server の URL へのバックアップ](/sql/relational-databases/backup-restore/sql-server-backup-to-url)」をご覧ください。

## <a name="back-up-a-database-in-azure-sql-edge"></a>Azure SQL Edge でデータベースをバックアップする

Azure SQL Edge では、SQL Server でサポートされているのと同じバックアップの種類がサポートされています。 完全な一覧については、「[バックアップの概要](/sql/relational-databases/backup-restore/backup-overview-sql-server/)」を参照してください。

> [!IMPORTANT] 
> Azure SQL Edge で作成されたデータベースでは、既定で単純復旧モデルが使用されます。 そのため、これらのデータベースでログ バックアップを実行することはできません。 これを行う必要がある場合は、データベース復旧モデルを完全復旧モデルに変更する管理者が必要です。 SQL Server でサポートされている復旧モデルの完全な一覧については、「[復旧モデルの概要](/sql/relational-databases/backup-restore/recovery-models-sql-server#RMov)」を参照してください。

### <a name="back-up-to-local-disk"></a>ローカル ディスクにバックアップする

次の例では、`BACKUP DATABASE` Transact-SQL コマンドを使用して、コンテナー内にデータベースのバックアップを作成します。 この例のために、バックアップ ファイルを格納するための *backup* という名前の新しいフォルダーを作成します。

1. バックアップ用のフォルダーを作成します。 Azure SQL Edge コンテナーが実行されているホスト上でこのコマンドを実行します。 次のコマンドの **<AzureSQLEdge_Container_Name>** を、ご自分のデプロイ内の Azure SQL Edge コンテナーの名前に置き換えてください。

    ```bash
    sudo docker exec -it <AzureSQLEdge_Container_Name> mkdir /var/opt/mssql/backup
    ```

2. SQL Server Management Studio (SSMS) または Azure Data Studio を使用して、Azure SQL Edge インスタンスに接続します。 `BACKUP DATABASE` コマンドを実行して、ユーザー データベースのバックアップを作成します。 次の例では、*IronOreSilicaPrediction* データベースのバックアップを作成します。

    ```sql
    BACKUP DATABASE [IronOreSilicaPrediction] 
    TO DISK = N'/var/opt/mssql/backup/IronOrePredictDB.bak' 
    WITH NOFORMAT, NOINIT,  NAME = N'IronOreSilicaPrediction-Full Database Backup', 
    SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  STATS = 10
    GO
    ```

3. このコマンドを実行し、データベースのバックアップに成功した場合は、SSMS または Azure Data Studio の結果セクションに次のようなメッセージが表示されます。

    ```txt
    10 percent processed.
    20 percent processed.
    30 percent processed.
    40 percent processed.
    50 percent processed.
    60 percent processed.
    70 percent processed.
    80 percent processed.
    90 percent processed.
    100 percent processed.
    Processed 51648 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction_log' on file 1.
    BACKUP DATABASE successfully processed 51650 pages in 3.588 seconds (112.461 MB/sec).

    Completion time: 2020-04-09T23:54:48.4957691-07:00
    ```

### <a name="back-up-to-url"></a>URL にバックアップする

Azure SQL Edge では、ページ BLOB とブロック BLOB の両方へのバックアップがサポートされています。 詳細については、「[ブロック BLOB とページ BLOB へのバックアップ](/sql/relational-databases/backup-restore/sql-server-backup-to-url#blockbloborpageblob)」を参照してください。 次の例では、データベース *IronOreSilicaPrediction* がブロック BLOB にバックアップされます。 

1. ブロック BLOB へのバックアップを構成するには、まず Azure SQL Edge 上に SQL Server 資格情報を作成するために使用できる Shared Access Signature (SAS) トークンを生成します。 このスクリプトでは、保存されているアクセス ポリシーに関連付けられた SAS が作成されます。 詳細については、[共有アクセス署名のパート 1 のSAS モデルの説明](../storage/common/storage-sas-overview.md)に関するページをご覧ください。 スクリプトはまた、SQL Server に対する資格情報を作成するのに必要な T-SQL コマンドも書き込みます。 次のスクリプトは、ストレージ アカウントを持つ Azure サブスクリプションとバックアップ用のストレージ コンテナーを既にお持ちであることを前提としています。

    ```PowerShell
    # Define global variables for the script  
    $subscriptionName='<your subscription name>'   # the name of subscription name you will use
    $resourcegroupName = '<your resource group name>' # the name of resource group you will use
    $storageAccountName= '<your storage account name>' # the storage account name you will use for backups
    $containerName= '<your storage container name>'  # the storage container name to which you will attach the SAS policy with its SAS token  
    $policyName = 'SASPolicy' # the name of the SAS policy  

    # adds an authenticated Azure account for use in the session
    Login-AzAccount

    # set the tenant, subscription and environment for use in the rest of
    Select-AzSubscription -Subscription $subscriptionName

    # Generate the SAS token
    $sa = Get-AzStorageAccount -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storagekey = Get-AzStorageAccountKey -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storagekey[0].Value
    $cbc = Get-AzStorageContainer -Name $containerName -Context $storageContext
    $policy = New-AzStorageContainerStoredAccessPolicy -Container $containerName -Policy $policyName -Context $storageContext -ExpiryTime $(Get-Date).ToUniversalTime().AddYears(10) -Permission "rwld"
    $sas = New-AzStorageContainerSASToken -Policy $policyName -Context $storageContext -Container $containerName
    Write-Host 'Shared Access Signature= '$($sas.Substring(1))''

    # Outputs the Transact SQL to the clipboard and to the screen to create the credential using the Shared Access Signature  
    Write-Host 'Credential T-SQL'  
    $tSql = "CREATE CREDENTIAL [{0}] WITH IDENTITY='Shared Access Signature', SECRET='{1}'" -f $cbc.CloudBlobContainer.Uri.AbsoluteUri,$sas.Substring(1)
    $tSql | clip  
    Write-Host $tSql
    ```

    スクリプトが正常に実行されたら、`CREATE CREDENTIAL` コマンドをクエリ ツールにコピーします。 次に、SQL Server のインスタンスに接続し、コマンドを実行して SAS を使用する資格情報を作成します。

2. SSMS または Azure Data Studio を使用して Azure SQL Edge インスタンスに接続し、前の手順のコマンドを使用して資格情報を作成します。 `CREATE CREDENTIAL` コマンドを、前の手順の実際の出力に置き換えてください。

    ```sql
    IF NOT EXISTS  
    (SELECT * FROM sys.credentials
    WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
    CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
       WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
       SECRET = '<SAS_TOKEN>';
    ```

3. 次のコマンドを実行すると、*IronOreSilicaPrediction* のバックアップが Azure ストレージ コンテナーに作成されます。

    ```sql
    BACKUP DATABASE IronOreSilicaPrediction
    TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/IronOreSilicaPrediction.bak'
    With MAXTRANSFERSIZE = 4194304,BLOCKSIZE=65536;  
    GO
    ```

## <a name="restore-a-database-in-azure-sql-edge"></a>Azure SQL Edge でデータベースを復元する

Azure SQL Edge では、ローカル ディスク、ネットワークの場所、または Azure BLOB ストレージ アカウントから復元できます。 SQL Server での復元と回復の詳細については、「[復元と復旧の概要](/sql/relational-databases/backup-restore/restore-and-recovery-overview-sql-server)」を参照してください。 SQL Server での単純復旧モデルの概要については、「[データベースの全体復元 (単純復旧モデル)](/sql/relational-databases/backup-restore/complete-database-restores-simple-recovery-model)」を参照してください。

> [!IMPORTANT] 
> Azure SQL Edge で作成されたデータベースは、Microsoft SQL Server または Azure SQL のインスタンス上で復元できません。 また、Microsoft SQL Server または Azure SQL で作成されたデータベースは、Azure SQL Edge 上で復元できますが、Azure SQL Edge でサポートされていない機能がデータベースに含まれていないことが条件です。 

### <a name="restore-from-a-local-disk"></a>ローカル ディスクから復元する

この例では、前の例で作成した *IronOreSilicaPrediction* バックアップを使用します。 次に、別の名前の新しいデータベースとして復元します。

1. データベースのバックアップ ファイルがコンテナー内にまだ存在していない場合は、次のコマンドを使用してコンテナー内にファイルをコピーできます。 次の例は、バックアップ ファイルがローカル ホスト上に存在し、*sql1* という名前の Azure SQL Edge コンテナー内の /var/opt/mssql/backup フォルダーにコピーされることを前提としています。

    ```bash
    sudo docker cp IronOrePredictDB.bak sql1:/var/opt/mssql/backup
    ```

2. SSMS または Azure Data Studio を使用して Azure SQL Edge インスタンスに接続し、復元コマンドを実行します。 次の例では、**IronOrePredictDB.bak** が復元され、新しいデータベース **IronOreSilicaPrediction_2** が作成されます。

    ```sql
    Restore FilelistOnly from disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'

    Restore Database IronOreSilicaPrediction_2
    From disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'
    WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.mdf',
    MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.ldf'
    ```

3. 復元コマンドを実行し、復元操作が正常に完了すると、出力ウィンドウに次のようなメッセージが表示されます。 

    ```txt
    Processed 51648 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction_log' on file 1.
    RESTORE DATABASE successfully processed 51650 pages in 6.543 seconds (61.670 MB/sec).

    Completion time: 2020-04-13T23:49:21.1600986-07:00
    ```

### <a name="restore-from-url"></a>URL から復元

Azure SQL Edge では、Azure Storage アカウントからのデータベースの復元もサポートされています。 復元は、ブロック BLOB またはページ BLOB のいずれかのバックアップから実行できます。 次の例では、ブロック BLOB のデータベース バックアップ ファイル *IronOreSilicaPrediction_2020_04_16.bak* が復元され、データベース *IronOreSilicaPrediction_3* が作成されます。

```sql
RESTORE DATABASE IronOreSilicaPrediction_3
FROM URL = 'https://mystorageaccount.blob.core.windows.net/mysecondcontainer/IronOreSilicaPrediction_2020_04_16.bak'
WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.mdf', 
MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.ldf',
STATS = 10;
```
---
title: SQL Server から Azure Arc 対応 SQL Managed Instance にデータベースを移行する
description: SQL Server から Azure Arc 対応 SQL Managed Instance にデータベースを移行する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 86563b0a44bade2cedaf76af3c247821756111fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90931498"
---
# <a name="migrate-sql-server-to-azure-arc-enabled-sql-managed-instance"></a>移行する:SQL Server から Azure Arc 対応 SQL Managed Instance

このシナリオでは、2 つの異なるバックアップ方法と復元方法を使用して、Azure Arc で SQL Server インスタンスから Azure SQL Managed Instance にデータベースを移行する手順について説明します。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="use-azure-blob-storage"></a>Azure Blob Storage を使用する 

Azure Arc 対応 SQL Managed Instance に移行する場合は、Azure Blob Storage を使用します。

この方法では、バックアップしてから復元できる一時的な保存場所として Azure Blob Storage が使用されます。

### <a name="prerequisites"></a>前提条件

- [Azure Data Studio をインストールする](install-client-tools.md)
- [Azure Storage Explorer をインストールする](https://azure.microsoft.com/features/storage-explorer/)
- Azure サブスクリプション

### <a name="step-1-provision-azure-blob-storage"></a>手順 1:Azure Blob Storage をプロビジョニングする

1. [Azure Blob Storage アカウントの作成](../../storage/blobs/storage-blob-create-account-block-blob.md?tabs=azure-portal)に関するページで説明されている手順に従います。
1. Azure Storage Explorer を起動する
1. [Azure にサインインして](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure)、前の手順で作成した Blob Storage にアクセスします。
1. Blob Storage アカウントを右クリックし、 **[Blob コンテナーの作成]** を選択して、バックアップ ファイルが格納される新しいコンテナーを作成します。

### <a name="step-2-get-storage-blob-credentials"></a>手順 2:Storage Blob の資格情報を取得する

1. Microsoft Azure Storage で、作成された Blob コンテナーを右クリックし、 **[共有アクセス署名の取得]** を選択します。

1. **[読み取り]** 、 **[書き込み]** 、および **[一覧表示]** を選択します。

1. **[作成]**

   この画面から URI とクエリ文字列をメモしておきます。 後の手順で必要になります。 **[コピー]** ボタンをクリックして、メモ帳や OneNote などに保存します。

1. **[共有アクセス署名]** ウィンドウを閉じます。

### <a name="step-3-backup-database-file-to-azure-blob-storage"></a>手順 3:データベース ファイルを Azure Blob Storage にバックアップする

この手順では、ソース SQL Server に接続し、SQL Managed Instance - Azure Arc に移行するデータベースのバックアップ ファイルを作成します。

1. Azure Data Studio を起動する
1. SQL Managed Instance - Azure Arc に移行するデータベースが存在する SQL Server インスタンスに接続します。
1. データベースを右クリックし、 **[新しいクエリ]** を選択します。
1. 次の形式でクエリを準備し、前の手順で共有したアクセス署名の情報を使用して `<...>` で指定されたプレースホルダーを置き換えます。  値を置き換えたら、クエリを実行します。

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
      SECRET = '<SAS_TOKEN>';  
   ```

1. 同様に、次のように **BACKUP DATABASE** コマンドを準備し、Blob コンテナーにバックアップ ファイルを作成します。  値を置き換えたら、クエリを実行します。

   ```sql
   BACKUP DATABASE <database name> TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>'
   ```

1. Azure Storage Explorer を開き、前の手順で作成したバックアップ ファイルが Blob コンテナーに表示されていることを確認します。

### <a name="step-4-restore-the-database-from-azure-blob-storage-to-sql-managed-instance---azure-arc"></a>手順 4:Azure Blob Storage から SQL Managed Instance - Azure Arc にデータベースを復元する

1. Azure Data Studio からログインし、SQL Managed Instance - Azure Arc に接続します。
1. **[システム データベース]** を展開し、**マスター** データベースを右クリックして、 **[新しいクエリ]** を選択します。
1. クエリ エディター ウィンドウで、前の手順と同じクエリを準備して実行し、資格情報を作成します。

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
     SECRET = '<SAS_TOKEN>';  
   ```

1. 次のコマンドを準備して実行し、バックアップ ファイルが読み取り可能であり、変更されていないことを確認します。

   ```console
   RESTORE FILELISTONLY FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>.bak'
   ```

1. 次のように **RESTORE DATABASE** コマンドを準備して実行し、SQL Managed Instance - Azure Arc のデータベースにバックアップ ファイルを復元します。

   ```sql
   RESTORE DATABASE <database name> FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>'
   WITH MOVE 'Test' to '/var/opt/mssql/data/<file name>.mdf'
   ,MOVE 'Test_log' to '/var/opt/mssql/data/<file name>.ldf'
   ,RECOVERY  
   ,REPLACE  
   ,STATS = 5;  
   GO
   ```

ここで、URL へのバックアップについて説明します。

[URL ドキュメントにバックアップする](/sql/relational-databases/backup-restore/sql-server-backup-to-url)

[SQL Server Management Studio (SSMS) を使用して URL にバックアップする](/sql/relational-databases/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service)

-------

## <a name="method-2-copy-the-backup-file-into-an-azure-sql-managed-instance---azure-arc-pod-using-kubectl"></a>方法 2:kubectl を使用して Azure SQL Managed Instance - Azure Arc ポッドにバックアップ ファイルをコピーする

この方法では、任意の方法を使用して作成したバックアップ ファイルを取得し、それを Azure SQL Managed Instance ポッドのローカル ストレージにコピーして、Windows または Linux の一般的なファイル システムと同様に、そこから復元できるようにする方法について説明します。 このシナリオでは、`kubectl cp` コマンドを使用して、ファイルをある場所からポッドのファイル システムにコピーします。

### <a name="prerequisites"></a>前提条件

- Azure Arc データ サービスがデプロイされている Kubernetes クラスターを示すように kubectl をインストールして構成する
- Azure Data Studio または SQL Server Management Server などのツールがインストールされ、バックアップ ファイルを作成する SQL Server に接続されているか、ローカル ファイル システムに既存の .bak ファイルが既に作成されている。

### <a name="step-1-backup-the-database-if-you-havent-already"></a>手順 1:まだデータベースをバックアップしていない場合はバックアップする

ディスクへの一般的な SQL Server バックアップと同様に、SQL Server データベースをローカル ファイル パスにバックアップします。

 ```sql
BACKUP DATABASE Test
TO DISK = 'c:\tmp\test.bak'
WITH FORMAT, MEDIANAME = 'Test’ ;
GO
```

### <a name="step-2-copy-the-backup-file-into-the-pods-file-system"></a>手順 2:バックアップ ファイルをポッドのファイル システムにコピーする

SQL インスタンスがデプロイされているポッドの名前を検索します。 一般に、「`pod/<sqlinstancename>-0`」のような名前です。

次のように実行して、すべてのポッドのリストを取得します。

 ```console
kubectl get pods -n <namespace of data controller>
```

例:

ローカル ストレージからクラスター内の SQL ポッドにバックアップ ファイルをコピーします。

 ```console
kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>

#Example:
kubectl cp C:\Backupfiles\test.bak sqlinstance1-0:var/opt/mssql/data/test.bak -n arc
```

### <a name="step-3-restore-the-database"></a>手順 3:データベースを復元する

RESTORE DATABASE コマンドを準備して実行し、Azure SQL Managed Instance - Azure Arc にバックアップ ファイルを復元します。

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```

例:

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/test.bak'
WITH MOVE 'test' to '/var/opt/mssql/data/test.mdf'  
,MOVE 'test' to '/var/opt/mssql/data/test_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```


## <a name="next-steps"></a>次のステップ

[Azure Arc 対応 SQL Managed Instance の機能の詳細を確認する](managed-instance-features.md)

[データ コントローラーの作成から開始する](create-data-controller.md)

[データ コントローラーが既に作成されている場合: Azure Arc 対応 SQL Managed Instance を作成する](create-sql-managed-instance.md)
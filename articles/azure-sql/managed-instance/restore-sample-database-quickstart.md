---
title: クイック スタート:バックアップを復元する (SSMS)
titleSuffix: Azure SQL Managed Instance
description: このクイックスタートでは、SQL Server Management Studio (SSMS) を使用して Azure SQL Managed Instance にデータベース バックアップを復元する方法について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 12/14/2018
ms.openlocfilehash: 9b2333e38415a2c0ad50ce36c213ead711c70ab4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96928802"
---
# <a name="quickstart-restore-a-database-to-azure-sql-managed-instance-with-ssms"></a>クイック スタート:SSMS を使用して Azure SQL Managed Instance にデータベースを復元する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

このクイックスタートでは、SQL Server Management Studio (SSMS) を使用して、Azure Blob Storage から [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) にデータベース (Wide World Importers - 標準のバックアップ ファイル) を復元します。

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> Azure Database Migration Service を使用した移行の詳細については、[Database Migration Service を使用した SQL Managed Instance の移行](../../dms/tutorial-sql-server-to-managed-instance.md)に関するページを参照してください。
> 各種の移行方法の詳細については、[Azure SQL Managed Instance への SQL Server の移行](migrate-to-instance-from-sql-server.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

このクイック スタート:

- [マネージド インスタンスの作成](instance-create-quickstart.md)に関するクイックスタートのリソースを使用します。
- 最新バージョンの [SSMS](/sql/ssms/sql-server-management-studio-ssms) がインストールされている必要があります。
- SSMS を使用して SQL Managed Instance に接続する必要があります。 接続方法については、次のクイック スタートを参照してください。
  - SQL Managed Instance で[パブリック エンドポイントを有効にする](public-endpoint-configure.md) - これは、このチュートリアルで推奨される方法です。
  - [Azure VM から SQL Managed Instance に接続する](connect-vm-instance-configure.md)。
  - [オンプレミスから SQL Managed Instance へのポイント対サイト接続を構成する](point-to-site-p2s-configure.md)。

> [!NOTE]
> Azure Blob Storage と [Shared Access Signature (SAS) キー](../../storage/common/storage-sas-overview.md)を使用した SQL Server データベースのバックアップと復元の詳細については、[SQL Server Backup to URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017) に関するページを参照してください。

## <a name="restore-from-a-backup-file"></a>バックアップ ファイルから復元する

SQL Server Management Studio で、次の手順に従って、Wide World Importers データベースを SQL Managed Instance に復元します。 データベース バックアップ ファイルは、事前構成済みの Azure Blob Storage アカウントに格納されています。

1. SSMS を開き、お使いのマネージド インスタンスに接続します。
2. **オブジェクト エクスプローラー** で、マネージド インスタンスを右クリックし、 **[新しいクエリ]** を選択して新しいクエリ ウィンドウを開きます。
3. 次の SQL スクリプトを実行します。このスクリプトでは、事前構成済みのストレージ アカウントと SAS キーを使用して、マネージド インスタンスに[資格情報を作成](/sql/t-sql/statements/create-credential-transact-sql)します。
 
   > [!IMPORTANT]
   > `CREDENTIAL` はコンテナーのパスと一致し、`https` で始まる必要があり、末尾にスラッシュを含めることはできません。 `IDENTITY` は、`SHARED ACCESS SIGNATURE` である必要があります。 `SECRET` は Shared Access Signature トークンである必要があり、先頭に `?` を含めることはできません。

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```

    ![資格情報を作成する](./media/restore-sample-database-quickstart/credential.png)

4. 認証情報を確認するために、次のスクリプトを実行します。このスクリプトでは、[コンテナー](https://azure.microsoft.com/services/container-instances/)の URL を使用してバックアップ ファイルの一覧を取得します。

   ```sql
   RESTORE FILELISTONLY FROM URL =
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![ファイル一覧](./media/restore-sample-database-quickstart/file-list.png)

5. 次のスクリプトを実行して、Wide World Importers データベースを復元します。

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![オブジェクト エクスプローラーで実行され、成功メッセージが表示されたスクリプトを示すスクリーンショット。](./media/restore-sample-database-quickstart/restore.png)

6. 次のスクリプトを実行して、復元の状態を追跡します。

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time
   FROM sys.dm_exec_requests r
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')
   ```

7. 復元が完了したら、オブジェクト エクスプローラー上でデータベースを表示します。 データベースの復元が完了したことを確認するには、[sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) ビューを使用できます。

> [!NOTE]
> データベースの復元操作は非同期であり、再試行可能です。 接続が切断されるか、タイムアウトが発生した場合に、SQL Server Management Studio にエラーが生じる可能性があります。 Azure SQL Database では、バックグラウンドでのデータベースの復元を試行し続けます。[sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) および [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) ビューを使用して、復元の進行状況を追跡できます。
> 復元プロセスの一部のフェーズでは、システム ビューに実際のデータベース名ではなく、一意の識別子が表示されます。 `RESTORE` ステートメントの動作の違いについては、[こちら](./transact-sql-tsql-differences-sql-server.md#restore-statement)で確認してください。

## <a name="next-steps"></a>次のステップ

- 手順 5 で、データベースの復元がメッセージ ID 22003 で終了した場合は、バックアップ チェックサムを含む新しいバックアップ ファイルを作成し、復元を再度実行します。 「[バックアップ中または復元中にバックアップ チェックサムを有効または無効にする](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server)」を参照してください。
- URL へのバックアップのトラブルシューティングについては、「[SQL Server Backup to URL に関するベスト プラクティスとトラブルシューティング](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting)」を参照してください。
- アプリの接続オプションの概要については、[SQL Managed Instance へのアプリケーションの接続](connect-application-instance.md)に関するページを参照してください。
- 任意のツールまたは言語を使用してクエリを実行する方法については、[Azure SQL Database の接続とクエリに関するクイック スタート](../database/connect-query-content-reference-guide.md)を参照してください。

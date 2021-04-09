---
title: マネージド インスタンス間でのレプリケーションの構成
titleSuffix: Azure SQL Managed Instance
description: このチュートリアルでは、Azure SQL Managed Instance パブリッシャー/ディストリビューターと SQL Managed Instance サブスクライバー間で、トランザクション レプリケーションを構成する方法について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 47ea5ea73c581313f90791ca6d7892ebad3f666b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101690687"
---
# <a name="tutorial-configure-replication-between-two-managed-instances"></a>チュートリアル:2 つのマネージド インスタンス間でのレプリケーションの構成

[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

トランザクション レプリケーションを使用すると、1 つのデータベースから SQL Server または [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) でホストされている別のデータベースにデータをレプリケートできます。 SQL Managed Instance には、レプリケーション トポロジのパブリッシャー、ディストリビューター、またはサブスクライバーを指定できます。 使用可能な構成については、[トランザクション レプリケーションの構成](replication-transactional-overview.md#common-configurations)に関する記事をご覧ください。 

トランザクション レプリケーションは、現在、SQL Managed Instance でのパブリック プレビュー段階にあります。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> - レプリケーション パブリッシャーおよびディストリビューターとしてマネージド インスタンスを構成する。
> - レプリケーション サブスクライバーとしてマネージド インスタンスを構成する。

![2 つのマネージド インスタンス間でのレプリケート](./media/replication-between-two-instances-configure-tutorial/sqlmi-sqlmi-repl.png)

このチュートリアルは経験豊富なユーザーを対象としたもので、ユーザーは Azure でのマネージド インスタンスと SQL Server VM の両方のデプロイと接続について理解していることを前提としています。 


> [!NOTE]
> - この記事では、Azure SQL Managed Instance での[トランザクション レプリケーション](/sql/relational-databases/replication/transactional/transactional-replication)の使用方法について説明します。 これは、個々のインスタンスを完全に読み取れるレプリカを作成する Azure SQL Managed Instance の機能である、[フェールオーバー グループ](../database/auto-failover-group-overview.md)とは無関係です。 [フェールオーバー グループを使用してトランザクション レプリケーション](replication-transactional-overview.md#with-failover-groups)を構成する場合は、追加の考慮事項があります。



## <a name="requirements"></a>必要条件

SQL Managed Instance をパブリッシャー、ディストリビューター、またはその両方として機能するよう構成するには、以下の要件があります。

- パブリッシャー マネージド インスタンスがディストリビューターおよびサブスクライバーと同じ仮想ネットワーク上にあるか、[VPN ゲートウェイ](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)が 3 つのエンティティすべての仮想ネットワーク間に構成されています。 
- 接続では、レプリケーション参加者間で SQL 認証を使用します。
- レプリケーションの作業ディレクトリとしての Azure ストレージ アカウント共有。
- Azure ファイル共有にアクセスするために、マネージド インスタンスの NSG のセキュリティ規則でポート 445 (TCP 送信) を開く必要があります。  「`failed to connect to azure storage <storage account name> with os error 53`」のエラーが発生する場合、適切な SQL Managed Instance サブネットの NSG にアウトバウンド規則を追加する必要があります。

## <a name="1---create-a-resource-group"></a>1 - リソース グループを作成する

[Azure portal](https://portal.azure.com) を使用して `SQLMI-Repl` という名前のリソース グループを作成します。  

## <a name="2---create-managed-instances"></a>2 - マネージド インスタンスを作成する

[Azure portal](https://portal.azure.com) を使用して、同じ仮想ネットワークおよびサブネット上に 2 つの[SQL Managed Instance](instance-create-quickstart.md) を作成します。 たとえば、2 つのマネージド インスタンスに次の名前を指定します。

- `sql-mi-pub` (ランダム化のためのいくつかの文字を付加する)
- `sql-mi-sub` (ランダム化のためのいくつかの文字を付加する)

また、ご自分のマネージド インスタンスに[接続するように Azure VM を構成する](connect-vm-instance-configure.md)必要もあります。 

## <a name="3---create-an-azure-storage-account"></a>3 ‐ Azure ストレージ アカウントを作成する

作業ディレクトリ用に [Azure ストレージ アカウント](../../storage/common/storage-account-create.md#create-a-storage-account)を作成し、そのストレージ アカウント内に[ファイル共有](../../storage/files/storage-how-to-create-file-share.md)を作成します。 

`\\storage-account-name.file.core.windows.net\file-share-name` の形式のファイル共有パスをコピーします。

例: `\\replstorage.file.core.windows.net\replshare`

`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net` の形式のストレージ アクセス キーをコピーします。

例: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

詳細については、「[ストレージ アカウントのアクセス キーの管理](../../storage/common/storage-account-keys-manage.md)」を参照してください。 

## <a name="4---create-a-publisher-database"></a>4 - パブリッシャー データベースを作成する

SQL Server Management Studio を使用して `sql-mi-pub` マネージド インスタンスに接続し、次の Transact-SQL (T-SQL) コードを実行してパブリッシャー データベースを作成します。

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_PUB]
GO

USE [ReplTran_PUB]
GO
CREATE TABLE ReplTest (
  ID INT NOT NULL PRIMARY KEY,
  c1 VARCHAR(100) NOT NULL,
  dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO


USE [ReplTran_PUB]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="5---create-a-subscriber-database"></a>5 - サブスクライバー データベースを作成する

SQL Server Management Studio を使用して `sql-mi-sub` マネージド インスタンスに接続し、次の Transact-SQL (T-SQL) コードを実行して空のサブスクライバー データベースを作成します。

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_SUB]
GO

USE [ReplTran_SUB]
GO
CREATE TABLE ReplTest (
  ID INT NOT NULL PRIMARY KEY,
  c1 VARCHAR(100) NOT NULL,
  dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO
```

## <a name="6---configure-distribution"></a>6 - ディストリビューションを構成する

SQL Server Management Studio を使用して `sql-mi-pub` マネージド インスタンスに接続し、次の T-SQL コードを実行してディストリビューション データベースを構成します。

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 - ディストリビューターを使用するようにパブリッシャーを構成する

お使いのパブリッシャー SQL Managed Instance の `sql-mi-pub` 上で、クエリの実行を [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) モードに変更し、次のコードを実行して新しいディストリビューターをお使いのパブリッシャーに登録します。

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
-- example: file_storage "\\replstorage.file.core.windows.net\replshare"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"
-- example: file_storage_key "DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net"

USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)'; -- Remove this parameter for on-premises publishers
```

   > [!NOTE]
   > file_storage パラメーターには、円記号 (`\`) のみを使用してください。 スラッシュ (`/`) を使用すると、ファイル共有への接続時にエラーが発生する可能性があります。

このスクリプトは、マネージド インスタンス上にローカル パブリッシャーを構成し、リンク サーバーを追加し、その SQL Server エージェント用に一連のジョブを作成します。

## <a name="8---create-publication-and-subscriber"></a>8 - パブリケーションとサブスクライバーを作成する

[SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) モードを使用して、次の T-SQL スクリプトを実行してデータベースのレプリケーションを有効にし、パブリッシャー、ディストリビューター、およびサブスクライバー間のレプリケーションを構成します。

```sql
-- Set variables
:setvar username sourceLogin
:setvar password sourcePassword
:setvar source_db ReplTran_PUB
:setvar publication_name PublishData
:setvar object ReplTest
:setvar schema dbo
:setvar target_server "sql-mi-sub.wdec33262scj9dr27.database.windows.net"
:setvar target_username targetLogin
:setvar target_password targetPassword
:setvar target_db ReplTran_SUB

-- Enable replication for your source database
USE [$(source_db)]
EXEC sp_replicationdboption
  @dbname = N'$(source_db)',
  @optname = N'publish',
  @value = N'true';

-- Create your publication
EXEC sp_addpublication
  @publication = N'$(publication_name)',
  @status = N'active';


-- Configure your log reader agent
EXEC sp_changelogreader_agent
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the publication snapshot
EXEC sp_addpublication_snapshot
  @publication = N'$(publication_name)',
  @frequency_type = 1,
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the ReplTest table to the publication
EXEC sp_addarticle
  @publication = N'$(publication_name)',
  @type = N'logbased',
  @article = N'$(object)',
  @source_object = N'$(object)',
  @source_owner = N'$(schema)';

-- Add the subscriber
EXEC sp_addsubscription
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @destination_db = N'$(target_db)',
  @subscription_type = N'Push';

-- Create the push subscription agent
EXEC sp_addpushsubscription_agent
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @subscriber_db = N'$(target_db)',
  @subscriber_security_mode = 0,
  @subscriber_login = N'$(target_username)',
  @subscriber_password = N'$(target_password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9 - エージェントのパラメーターを変更する

現在、Azure SQL Managed Instance では、レプリケーション エージェントとの接続でバックエンドの問題がいくつか発生しています。 この問題には現在対応していますが、レプリケーション エージェントのログイン タイムアウト値を増やすことで解決することができます。

パブリッシャー上で次の T-SQL コマンドを実行してログイン タイムアウトを増やします。

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

必要に応じて、次の T-SQL コマンドをもう一度実行して、ログイン タイムアウトを既定値に戻します。

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

これらの変更を適用するために 3 つのエージェントすべてを再起動します。

## <a name="10---test-replication"></a>10 - テスト レプリケーション

レプリケーションが構成されたら、パブリッシャーに新しい項目を挿入し、変更がサブスクライバーに反映されることを確認することでテストできます。

次の T-SQL スニペットを実行してサブスクライバー上に行を表示します。

```sql
select * from dbo.ReplTest
```

次の T-SQL スニペットを実行してパブリッシャーに追加の行を挿入し、サブスクライバー上でその行をもう一度確認します。

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

パブリケーションをドロップするには、次の T-SQL コマンドを実行します。

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

データベースからレプリケーション オプションを削除するには、次の T-SQL コマンドを実行します。

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

パブリッシングとディストリビューションを無効にするには、次の T-SQL コマンドを実行します。

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

[リソース グループから SQL Managed Instance リソースを削除](../../azure-resource-manager/management/manage-resources-portal.md#delete-resources)してからリソース グループ `SQLMI-Repl` を削除することで、ご自分の Azure リソースをクリーンアップできます。 

## <a name="next-steps"></a>次のステップ

[Azure SQL Managed Instance とのトランザクション レプリケーション](replication-transactional-overview.md)の詳細、または [SQL Managed Instance パブリッシャー/ディストリビューターと、Azure VM サブスクライバー上の SQL](replication-two-instances-and-sql-server-configure-tutorial.md) 間でのレプリケーションの構成方法についても学習してください。
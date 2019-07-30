---
title: Azure SQL Database マネージド インスタンス データベースにレプリケーションを構成する | Microsoft Docs
description: Azure SQL Database マネージド インスタンス データベースにトランザクション レプリケーションを構成する方法を説明します
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: e4d056aacf8f3969b645747e2303574f3fea3bda
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357118"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Azure SQL Database マネージド インスタンス データベースにレプリケーションを構成する

トランザクション レプリケーションを使用すると、SQL Server データベースや別のインスタンス データベースから、Azure SQL Database マネージド インスタンス データベースにデータをレプリケートできます。 

トランザクション レプリケーションを使用して、Azure SQL Database マネージド インスタンスのインスタンス データベースで行われた変更を以下にプッシュすることもできます。

- SQL Server データベース。
- Azure SQL Database 内の単一データベース。
- Azure SQL Database エラスティック プール内のプールされたデータベース。
 
トランザクション レプリケーションは、[Azure SQL Database マネージド インスタンス](sql-database-managed-instance.md)でのパブリック プレビュー段階にあります。 マネージド インスタンスでは、パブリッシャー、ディストリビューター、サブスクライバーの各データベースをホストできます。 使用可能な構成については、[トランザクション レプリケーションの構成](sql-database-managed-instance-transactional-replication.md#common-configurations)に関する記事をご覧ください。

  > [!NOTE]
  > この記事の目的は、リソース グループの作成を始め、Azure Database マネージド インスタンスを使用したレプリケーションの構成の始めから終わりまでユーザーにガイドすることです。 マネージド インスタンスが既にデプロイされている場合は、「[手順 4](#4---create-a-publisher-database)」に進んでパブリッシャー データベースを作成します。また、既にパブリッシャーとサブスクライバー データベースがあり、レプリケーションの構成を開始する準備ができている場合は「[手順 6](#6---configure-distribution)」に進みます。  

## <a name="requirements"></a>必要条件

パブリッシャー、ディストリビューター、またはその両方として機能するようにマネージド インスタンスを構成するには、以下の要件があります。

- マネージ インスタンスが、geo レプリケーションのリレーションシップに現在参加していない。
- パブリッシャー マネージド インスタンスがディストリビューターおよびサブスクライバーと同じ仮想ネットワーク上にあるか、[vNet ピアリング](../virtual-network/tutorial-connect-virtual-networks-powershell.md)が 3 つのエンティティすべての仮想ネットワーク間に確立されている。 
- 接続では、レプリケーション参加者間で SQL 認証を使用します。
- レプリケーション作業ディレクトリの Azure ストレージ アカウント共有。
- Azure ファイル共有にアクセスするために、マネージド インスタンスの NSG のセキュリティ規則でポート 445 (TCP 送信) を開く必要があります。 


 > [!NOTE]
 > Azure SQL Database 内の単一データベースとプールされたデータベースは、サブスクライバーとしてのみ使用できます。 


## <a name="features"></a>機能

サポート:

- Azure SQL Database 内にある SQL Server のオンプレミスおよびマネージド インスタンスの、トランザクション レプリケーションとスナップショット レプリケーションの組み合わせ。
- サブスクライバーには、オンプレミス SQL Server データベース、Azure SQL Database 内の単一データベース/マネージド インスタンス、または Azure SQL Database エラスティック プール内のプールされたデータベースを使用できます。
- 一方向または双方向のレプリケーション。

次の機能は、Azure SQL Database 内のマネージド インスタンスではサポートされません。

- [更新可能なサブスクリプション](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication)。
- トランザクション レプリケーションが構成されている場合、[アクティブ geo レプリケーション](sql-database-active-geo-replication.md)と[自動フェールオーバー グループ](sql-database-auto-failover-group.md)は使用しないでください。
 
## <a name="1---create-a-resource-group"></a>1 - リソース グループを作成する

[Azure portal](https://portal.azure.com) を使用して `SQLMI-Repl` という名前のリソース グループを作成します。  

## <a name="2---create-managed-instances"></a>2 - マネージド インスタンスを作成する

[Azure portal](https://portal.azure.com) を使用して、同じ仮想ネットワークおよびサブネット上に 2 つの[マネージド インスタンス](sql-database-managed-instance-create-tutorial-portal.md)を作成します。 2 つのマネージド インスタンスには、次のように名前を付けます。

- `sql-mi-pub`
- `sql-mi-sub`

また、Azure SQL Database マネージド インスタンスに[接続するように Azure VM を構成する](sql-database-managed-instance-configure-vm.md)必要もあります。 

## <a name="3---create-azure-storage-account"></a>3 - Azure Storage アカウントを作成する

作業ディレクトリ用に [Azure Storage アカウント](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account)を作成し、そのストレージ アカウント内に[ファイル共有](../storage/files/storage-how-to-create-file-share.md)を作成します。 

`\\storage-account-name.file.core.windows.net\file-share-name` の形式のファイル共有パスをコピーします。

`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net` の形式のストレージ アクセス キーをコピーします。

 詳細については、「 [ストレージ アクセス キーの表示とコピー](../storage/common/storage-account-manage.md#access-keys)」を参照してください。 

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

パブリッシャー マネージド インスタンス `sql-mi-pub` 上で、クエリの実行を [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) モードに変更し、次のコードを実行して新しいディストリビューターをパブリッシャーに登録します。 

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"


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

このスクリプトで、マネージド インスタンス上にローカル パブリッシャーを構成し、リンク サーバーを追加し、SQL Server エージェント用の一連のジョブを作成します。 

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


-- Configure your log reaer agent
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
  @job_login = N'$(target_username)',
  @job_password = N'$(target_password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9 - エージェントのパラメーターを変更する

現在、Azure SQL Database マネージド インスタンスには、レプリケーション エージェントとの接続に関するバックエンドの問題がいくつか発生しています。 この問題は現在対処中ですが、レプリケーション エージェントのログイン タイムアウト値を増やす回避策があります。 

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

## <a name="clean-up-resources"></a>リソースのクリーンアップ

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

[リソース グループからマネージド インスタンス リソースを削除](../azure-resource-manager/manage-resources-portal.md#delete-resources)してからリソース グループ `SQLMI-Repl` を削除することで、Azure リソースをクリーンアップできます。 

   
## <a name="see-also"></a>関連項目

- [トランザクション レプリケーション](sql-database-managed-instance-transactional-replication.md)
- [マネージド インスタンスとは](sql-database-managed-instance.md)

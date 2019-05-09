---
title: Azure SQL Database マネージド インスタンス データベースにレプリケーションを構成する| Microsoft Docs
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
ms.openlocfilehash: b20a119a69ac796bc9ea85083d335f0a7d2fdf2d
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417957"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Azure SQL Database マネージド インスタンス データベースにレプリケーションを構成する

トランザクション レプリケーションを使用すると、SQL Server データベースや別のインスタンス データベースから、Azure SQL Database マネージド インスタンス データベースにデータをレプリケートできます。 また、トランザクション レプリケーションでは、Azure SQL Database マネージド インスタンス内のインスタンス データベースで行った変更を、SQL Server データベース、Azure SQL Database 内の単一データベース、Azure SQL Database エラスティック プール内のプールされたデータベースにプッシュすることもできます。 トランザクション レプリケーションは、[Azure SQL Database マネージド インスタンス](sql-database-managed-instance.md)でのパブリック プレビュー段階にあります。 マネージド インスタンスでは、パブリッシャー、ディストリビューター、サブスクライバーの各データベースをホストできます。 使用可能な構成については、[トランザクション レプリケーションの構成](sql-database-managed-instance-transactional-replication.md#common-configurations)に関する記事をご覧ください。

## <a name="requirements"></a>必要条件

マネージド インスタンスをパブリッシャーまたはディストリビューターとして機能するように構成するには、次のことが要件となります。

- マネージ インスタンスが、geo レプリケーションのリレーションシップに現在参加していない。

   >[!NOTE]
   >Azure SQL Database 内の単一データベースとプールされたデータベースは、サブスクライバーとしてのみ使用できます。

- すべてのマネージド インスタンスが、同じ vNet 上に配置されている。

- 接続では、レプリケーション参加者間で SQL 認証を使用します。

- レプリケーション作業ディレクトリの Azure ストレージ アカウント共有。

- Azure ファイル共有にアクセスするために、マネージド インスタンス サブネットのセキュリティ規則でポート 445 (TCP 送信) を開く必要があります

## <a name="features"></a>機能

サポート:

- Azure SQL Database 内にある SQL Server のオンプレミスおよびマネージド インスタンスの、トランザクション レプリケーションとスナップショット レプリケーションの組み合わせ。
- サブスクライバーには、オンプレミス SQL Server データベース、Azure SQL Database 内の単一データベース/マネージド インスタンス、または Azure SQL Database エラスティック プール内のプールされたデータベースを使用できます。
- 一方向または双方向のレプリケーション。

次の機能は、Azure SQL Database 内のマネージド インスタンスではサポートされません。

- 更新可能なサブスクリプション。
- トランザクション レプリケーションが構成されている場合、[アクティブ geo レプリケーション](sql-database-active-geo-replication.md)と[自動フェールオーバー グループ](sql-database-auto-failover-group.md)は使用しないでください。

## <a name="configure-publishing-and-distribution-example"></a>パブリッシングとディストリビューションの構成例

1. ポータルで [Azure SQL Database マネージド インスタンスを作成](sql-database-managed-instance-create-tutorial-portal.md)します。
2. 作業ディレクトリの [Azure ストレージ アカウントを作成](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account)します。

   ストレージ キーを必ずコピーしてください。 [ストレージ アクセス キーの表示とコピー](../storage/common/storage-account-manage.md#access-keys
)に関する記事をご覧ください。
3. パブリッシャーのインスタンス データベースを作成します。

   下記のサンプル スクリプトでは、`<Publishing_DB>` をこのインスタンス データベースの名前に置き換えます。

4. ディストリビューターの SQL 認証を使用するデータベース ユーザーを作成します。 セキュリティで保護されたパスワードを使用します。

   下記のサンプル スクリプトでは、`<SQL_USER>` と `<PASSWORD>` にこの SQL Server アカウントのデータベース ユーザーとパスワードを使用します。

5. [SQL Database Managed Instance に接続](sql-database-connect-query-ssms.md)します。

6. 次のクエリを実行して、ディストリビューターとディストリビューション データベースを追加します。

   ```sql
   USE [master]
   GO
   EXEC sp_adddistributor @distributor = @@ServerName;
   EXEC sp_adddistributiondb @database = N'distribution';
   ```

7. 指定したディストリビューション データベースを使用するようにパブリッシャーを構成するには、次のクエリを更新して実行します。

   `<SQL_USER>` と `<PASSWORD>` を、SQL Server アカウントとパスワードに置き換えます。

   `\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>` をストレージ アカウントの値に置き換えます。  

   `<STORAGE_CONNECTION_STRING>` を、Microsoft Azure ストレージ アカウントの **[アクセス キー]** タブの接続文字列に置き換えます。

   次のクエリを更新したら、クエリを実行します。

   ```sql
   USE [master]
   EXEC sp_adddistpublisher @publisher = @@ServerName,
                @distribution_db = N'distribution',
                @security_mode = 0,
                @login = N'<SQL_USER>',
                @password = N'<PASSWORD>',
                @working_directory = N'\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>',
                @storage_connection_string = N'<STORAGE_CONNECTION_STRING>';
   GO
   ```

8. レプリケーションのパブリッシャーを構成します。

    次のクエリで、`<Publishing_DB>` をパブリッシャー データベースの名前に置き換えます。

    `<Publication_Name>` をパブリケーションの名前に置き換えます。

    `<SQL_USER>` と `<PASSWORD>` を、SQL Server アカウントとパスワードに置き換えます。

    クエリを更新したら、クエリを実行してパブリケーションを作成します。

   ```sql
   USE [<Publishing_DB>]
   EXEC sp_replicationdboption @dbname = N'<Publishing_DB>',
                @optname = N'publish',
                @value = N'true';

   EXEC sp_addpublication @publication = N'<Publication_Name>',
                @status = N'active';

   EXEC sp_changelogreader_agent @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>';

   EXEC sp_addpublication_snapshot @publication = N'<Publication_Name>',
                @frequency_type = 1,
                @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   ```

9. アーティクル、サブスクリプション、プッシュ サブスクリプション エージェントを追加します。

   これらのオブジェクトを追加するには、次のスクリプトを更新します。

   - `<Object_Name>` をパブリケーション オブジェクトの名前に置き換えます。
   - `<Object_Schema>` をソース スキーマの名前に置き換えます。
   - 山かっこ (`<>`) 内のその他のパラメーターは、前のスクリプトの値と一致するように置き換えます。

   ```sql
   EXEC sp_addarticle @publication = N'<Publication_Name>',
                @type = N'logbased',
                @article = N'<Object_Name>',
                @source_object = N'<Object_Name>',
                @source_owner = N'<Object_Schema>'

   EXEC sp_addsubscription @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @destination_db = N'<Subscribing_DB>',
                @subscription_type = N'Push'

   EXEC sp_addpushsubscription_agent @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @subscriber_db = N'<Subscribing_DB>',
                @subscriber_security_mode = 0,
                @subscriber_login = N'<SQL_USER>',
                @subscriber_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   GO
   ```
   
## <a name="see-also"></a>関連項目

- [トランザクション レプリケーション](sql-database-managed-instance-transactional-replication.md)
- [マネージド インスタンスとは](sql-database-managed-instance.md)

---
title: トランザクション レプリケーション
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance (プレビュー) で SQL Server トランザクション レプリケーションを使用するについて説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein
ms.date: 04/20/2020
ms.openlocfilehash: 3e4b4fc3d4a6c9529c7c0ac0daef8a28173e0bf3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99225345"
---
# <a name="transactional-replication-with-azure-sql-managed-instance-preview"></a>Azure SQL Managed Instance (プレビュー) でのトランザクション レプリケーション
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

トランザクション レプリケーションは、Azure SQL Managed Instance または SQL Server インスタンスのテーブルからリモート データベースに配置されているテーブルにデータをレプリケートする、Azure SQL Managed Instance と SQL Server の機能です。 この機能を使用すると、さまざまなデータベース内の複数のテーブルを同期させることができます。 

トランザクション レプリケーションは、現在、SQL Managed Instance でのパブリック プレビュー段階にあります。 

## <a name="overview"></a>概要

トランザクション レプリケーションを使用して、Azure SQL Managed Instance で行われた変更を以下にプッシュすることができます。

- オンプレミスまたは Azure VM 上の SQL Server データベース
- Azure SQL Database 内のデータベース
- Azure SQL Managed Instance 内のインスタンス データベース

  > [!NOTE]
  > Azure SQL Managed Instance のすべての機能を使用するには、最新バージョンの [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) および [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt) を使用している必要があります。

### <a name="components"></a>Components

トランザクション レプリケーションの主要なコンポーネントは、次の図の **パブリッシャー**、**ディストリビューター**、**サブスクライバー** です。  

![SQL Database を使用したレプリケーション](./media/replication-transactional-overview/replication-to-sql-database.png)

| Role | Azure SQL データベース | Azure SQL Managed Instance |
| :----| :------------- | :--------------- |
| **発行元** | いいえ | はい |
| **ディストリビューター** | いいえ | はい|
| **プル サブスクライバー** | いいえ | はい|
| **プッシュ サブスクライバー**| はい | はい|
| &nbsp; | &nbsp; | &nbsp; |

**パブリッシャー** により、ディストリビューターに更新プログラムが送信され、一部のテーブル (アーティクル) に加えられた変更が発行されます。 パブリッシャーには、Azure SQL Managed Instance または SQL Server インスタンスを指定できます。

**ディストリビューター** では、パブリッシャーからアーティクル内の変更が収集されてサブスクライバーに配布されます。 ディストリビューターには、Azure SQL Managed Instance または SQL Server インスタンス (パブリッシャーのバージョン以上の任意のバージョン) のいずれかを指定できます。

**サブスクライバー** によって、パブリッシャーに対して加えられた変更が受け取られます。 プッシュ サブスクライバーとプル サブスクライバーには、SQL Server インスタンスと Azure SQL Managed Instance の両方を指定できます。ただし、ディストリビューターが Azure SQL Managed Instance であり、サブスクライバーがそうではない場合は、プル サブスクリプションはサポートされません。 Azure SQL Database 内のデータベースは、プッシュ サブスクライバーにしか指定できません。

Azure SQL Managed Instance では、次のバージョンの SQL Server をサブスクライバーに指定できます。

- SQL Server 2016 以降
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) または [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) または [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

   > [!NOTE]
   >
   > - Azure でのオブジェクトへの発行をサポートしていないその他のバージョンの SQL Server では、[データの再発行](/sql/relational-databases/replication/republish-data)方法を利用して新しいバージョンの SQL Server にデータを移動することができます。
   > - 以前のバージョンを使用してレプリケーションを構成しようとすると、エラー番号 MSSQL_REPL20084 (プロセスはサブスクライバーに接続できませんでした) および MSSQ_REPL40532 (ログインによって要求されたサーバー \<name> を開くことができません。 ログインに失敗しました) のエラーが発生する可能性があります。

### <a name="types-of-replication"></a>レプリケーションの種類

さまざまな[レプリケーションの種類](/sql/relational-databases/replication/types-of-replication)があります。

| レプリケーション | Azure SQL データベース | Azure SQL Managed Instance |
| :----| :------------- | :--------------- |
| [**標準トランザクション**](/sql/relational-databases/replication/transactional/transactional-replication) | はい (サブスクライバーとしてのみ) | はい |
| [**スナップショット**](/sql/relational-databases/replication/snapshot-replication) | はい (サブスクライバーとしてのみ) | はい|
| [**マージ レプリケーション**](/sql/relational-databases/replication/merge/merge-replication) | いいえ | いいえ|
| [**ピア ツー ピア**](/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | いいえ | いいえ|
| [**双方向**](/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | いいえ | はい|
| [**更新可能なサブスクリプション**](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | いいえ | いいえ|
| &nbsp; | &nbsp; | &nbsp; |

### <a name="supportability-matrix"></a>サポータビリティ マトリックス

  Azure SQL Managed Instance のトランザクション レプリケーションのサポータビリティ マトリックスは、SQL Server のものと同じです。
  
| **発行元**   | **ディストリビューター** | **サブスクライバー** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="when-to-use"></a>使用する場合

トランザクション レプリケーションは次のシナリオで役立ちます。

- データベース内の 1 つ以上のテーブルに対して加えられた変更を発行して、変更がサブスクライブされている 1 つ以上の SQL Server インスタンスまたは Azure SQL Database に配布する。
- いくつかの分散データベースは同時状態を保持します。
- 変更を継続的に発行して、データベースを 1 つの SQL Server インスタンスまたは Azure SQL Managed Instance から別のデータベースに移行する。

### <a name="compare-data-sync-with-transactional-replication"></a>データ同期とトランザクション レプリケーションの比較

| カテゴリ | データ同期 | トランザクション レプリケーション |
|---|---|---|
| 長所 | - アクティブ/アクティブのサポート<br/>- オンプレミスと Azure SQL Database 間で双方向 | - 待ち時間の短縮<br/>- トランザクションの整合性<br/>- 移行後に既存のトポロジの再利用 |
| 短所 | - トランザクションの整合性なし<br/>- パフォーマンスへの影響が大きい | - Azure SQL Database からは発行できない <br/>- 高いメンテナンス コスト |

## <a name="common-configurations"></a>一般的な構成

一般に、パブリッシャーとディストリビューターは、クラウドかオンプレミスのいずれかに存在する必要があります。 次の構成がサポートされています。

### <a name="publisher-with-local-distributor-on-sql-managed-instance"></a>SQL Managed Instance 上のパブリッシャーとローカルのディストリビューター

![パブリッシャーおよびディストリビューターとしての 1 つのインスタンス](./media/replication-transactional-overview/01-single-instance-asdbmi-pubdist.png)

パブリッシャーとディストリビューターは、1 つの SQL Managed Instance 内に構成され、別の SQL Managed Instance、SQL Database、または SQL Server インスタンスに変更を配布します。

### <a name="publisher-with-remote-distributor-on-sql-managed-instance"></a>SQL Managed Instance 上のパブリッシャーとリモート ディストリビューター

この構成では、多くのソース SQL Managed Instance にサービスを提供し、Azure SQL Database、Azure SQL Managed Instance または SQL Server 上の 1 つ以上のターゲットに変更を配布できる、別の SQL Managed Instance に配置されているディストリビューターに、1 つのマネージド インスタンスにより変更が発行されます。

![パブリッシャーおよびディストリビューターの独立したインスタンス](./media/replication-transactional-overview/02-separate-instances-asdbmi-pubdist.png)

2 つのマネージド インスタンス上にパブリッシャーとディストリビューターが構成されています。 この構成には、次のようないくつかの制約があります。

- 2 つのマネージド インスタンスが同じ vNet 上にあります。
- 2 つのマネージド インスタンスが同じ場所にあります。

### <a name="on-premises-publisherdistributor-with-remote-subscriber"></a>リモートにサブスクライバーが存在するオンプレミスのパブリッシャー/ディストリビューター

![Azure SQL Database がサブスクライバー](./media/replication-transactional-overview/03-azure-sql-db-subscriber.png)

この構成では、Azure SQL Database または Azure SQL Managed Instance 内のデータベースがサブスクライバーです。 この構成では、オンプレミスから Azure への移行がサポートされます。 サブスクライバーが Azure SQL Database のデータベースである場合は、それはプッシュ モードにする必要があります。  

## <a name="requirements"></a>必要条件

- レプリケーション参加者間の接続には、SQL 認証を使用します。
- レプリケーションで使用する作業ディレクトリに Azure ストレージ アカウント共有を使用します。
- サブネット セキュリティ規則の TCP 送信ポート 445 を Azure ファイル共有へのアクセス用に開きます。
- SQL Managed Instance がパブリッシャーまたはディストリビューターで、サブスクライバーがそうではない場合は、TCP 送信ポート 1433 を開きます。 また、ポート 1433 **宛先サービス タグ** の `allow_linkedserver_outbound` の SQL Managed Instance NSG 送信セキュリティ規則を `virtualnetwork` から `internet` に変更することが必要になる場合もあります。
- パブリッシャーとディストリビューターを両方ともクラウド、または両方ともオンプレミスに配置します。
- レプリケーション参加者の仮想ネットワークが異なる場合は、仮想ネットワーク間で VPN ピアリングを構成します。

> [!NOTE]
> ディストリビューターが Azure SQL Managed Instance データベースでサブスクライバーがオンプレミスであるときに送信ネットワーク セキュリティ グループ (NSG) ポート 445 がブロックされている場合、Azure Storage ファイルに接続するときに、エラー 53 が発生する可能性があります。 [vNet NSG を更新して](../../storage/files/storage-troubleshoot-windows-file-connection-problems.md)、この問題を解決します。

## <a name="with-failover-groups"></a>フェイルオーバーグループを使う

[アクティブ geo レプリケーション](../database/active-geo-replication-overview.md)は、トランザクション レプリケーションを使用している SQL Managed Instance ではサポートされていません。 アクティブ geo レプリケーションの代わりに、[自動フェールオーバー グループ](../database/auto-failover-group-overview.md)を使用します。ただし、パブリケーションをプライマリ マネージド インスタンスから[手動で削除](transact-sql-tsql-differences-sql-server.md#replication)し、フェールオーバー後にセカンダリ SQL Managed Instance 上で再作成する必要があることにご注意ください。

geo レプリケーションが、[フェールオーバー グループ](../database/auto-failover-group-overview.md)の **パブリッシャー** または **ディストリビューター** SQL Managed Instance 上で有効化されている場合、フェールオーバーが発生した後、SQL Managed Instance 管理者が、古いプライマリ上のすべてのパブリケーションをクリーンアップして、新しいプライマリ上でそれらを再構成する必要があります。 このシナリオでは、次のアクティビティが必要です。

1. データベース上で実行されているレプリケーション ジョブがある場合は、すべて停止します。
1. パブリッシャーからサブスクリプションのメタデータを削除するには、パブリッシャー データベース上で次のスクリプトを実行します。

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```

1. サブスクライバーからサブスクリプションのメタデータを削除します。 サブスクライバー SQL Managed Instance 上のサブスクリプション データベースで、次のスクリプトを実行します。

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>',
      @publisher_db = N'<publisher database>',
      @publication = N'<name of publication>';
   ```

1. パブリッシャーからすべてのレプリケーション オブジェクトを強制的に削除するには、発行されたデータベースで次のスクリプトを実行します。

   ```sql
   EXEC sp_removedbreplication
   ```

1. 元のプライマリ SQL Managed Instance から以前のディストリビューターを強制的に削除します (ディストリビューターが以前は存在していた以前のプライマリにフェールバックする場合)。 以前のディストリビューター SQL Managed Instance のマスター データベース上で次のスクリプトを実行します。

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

Geo レプリケーションが、フェイルオーバーの **サブスクライバー** インスタンスで有効になっている場合、パブリケーションは、サブスクライバーマネージド インスタンスのフェールオーバーグループのリスナーエンドポイントに接続するように構成されている必要があります。 フェールオーバーが発生した場合、マネージド インスタンス管理者による以降のアクションは、発生したフェールオーバーの種類によって異なります：

- データ損失のないフェールオーバーの場合、フェールオーバー後もレプリケーションは機能し続けます。
- データ損失を伴うフェイルオーバーの場合、レプリケーションも機能します。 失われた変更を再度レプリケートします。
- データ損失のあるフェールオーバーで、データ損失がディストリビューション データベースの保持期間外である場合は、SQL Managed Instance 管理者がサブスクリプション データベースを再初期化する必要があります。

## <a name="next-steps"></a>次のステップ

トランザクション レプリケーションの構成の詳細については、次に関するチュートリアルを参照してください。

- [SQL Managed Instance パブリッシャーとサブスクライバーの間でのレプリケーションの構成](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [SQL Managed Instance パブリッシャー、SQL Managed Instance ディストリビューター、SQL Server サブスクライバー間でのレプリケーションの構成](../managed-instance/replication-two-instances-and-sql-server-configure-tutorial.md)
- [パブリケーションを作成します](/sql/relational-databases/replication/publish/create-a-publication)。
- [プッシュ サブスクリプションの作成](/sql/relational-databases/replication/create-a-push-subscription): サーバー名をサブスクライバーとして (たとえば `N'azuresqldbdns.database.windows.net` を使用し、Azure SQL Database 名のデータベースを宛先データベース (たとえば、**Adventureworks** として使用します。 )

## <a name="see-also"></a>関連項目  

- [SQL Managed Instance とフェールオーバー グループのレプリケーション](transact-sql-tsql-differences-sql-server.md#replication)
- [SQL Database へのレプリケーション](../database/replication-to-sql-database.md)
- [マネージド インスタンスへのレプリケーション](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [パブリケーションを作成する](/sql/relational-databases/replication/publish/create-a-publication)
- [プッシュ サブスクリプションを作成する](/sql/relational-databases/replication/create-a-push-subscription/)
- [レプリケーションの種類](/sql/relational-databases/replication/types-of-replication)
- [監視 (レプリケーション)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [サブスクリプションを初期化する](/sql/relational-databases/replication/initialize-a-subscription)

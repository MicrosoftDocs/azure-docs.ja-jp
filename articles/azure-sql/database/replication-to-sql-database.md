---
title: Azure SQL Server の Azure SQL Database へのレプリケーション
description: Azure SQL Database のデータベースは、一方向のトランザクションまたはスナップショット レプリケーション トポロジのプッシュ サブスクライバーとして構成できます。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 6ff1d485ab4c0662ae8a9d754ce67b1446b76fcc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780956"
---
# <a name="replication-to-azure-sql-database"></a>Azure SQL Database へのレプリケーション
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database は、一方向のトランザクションまたはスナップショット レプリケーション トポロジのプッシュ サブスクライバーとして構成できます。

> [!NOTE]
> この記事では、Azure SQL Database での[のトランザクション レプリケーション](/sql/relational-databases/replication/transactional/transactional-replication)の使用方法について説明します。 これは、個々のデータベースの完全な読み取り可能レプリカを作成するための Azure SQL Database の機能である、[アクティブ geo レプリケーション](./active-geo-replication-overview.md)とは無関係です。

## <a name="supported-configurations"></a>サポートされている構成
  
- Azure SQL Database は、SQL Server パブリッシャーとディストリビューターのプッシュ サブスクライバーとしてのみ構成できます。  
- パブリッシャーまたはディストリビューターとして機能する SQL Server インスタンスは、[オンプレミスで実行されている SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) のインスタンス、[Azure SQL Managed Instance](../managed-instance/instance-create-quickstart.md)、[またはクラウドの Azure 仮想マシンで実行されている SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md) のインスタンスのいずれかになります。 
- ディストリビューション データベースとレプリケーション エージェントは、Azure SQL Database のデータベースに配置できません。  
- [スナップショット](/sql/relational-databases/replication/snapshot-replication)および[一方向のトランザクション](/sql/relational-databases/replication/transactional/transactional-replication) レプリケーションがサポートされています。 ピア ツー ピア トランザクション レプリケーションおよびマージ レプリケーションはサポートされていません。

### <a name="versions"></a>バージョン  

Azure SQL Database のデータベースへ正常にレプリケートするには、SQL Server パブリッシャーおよびディストリビューターで、(少なくとも) 次のいずれかのバージョンが使用されている必要があります。

SQL Server データベースから Azure SQL Database への発行は、次のバージョンの SQL Server でサポートされます。

- SQL Server 2016 以降
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) または [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) または [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> サポートされていないバージョンを使用してレプリケーションを構成しようとすると、エラー番号 MSSQL_REPL20084 (プロセスで、サブスクライバーに接続できませんでした) および MSSQL_REPL40532 (このログインで要求されたサーバー \<name> を開くことができません。 ログインに失敗しました) のエラーが発生する可能性があります。  

Azure SQL Database のすべての機能を使用するには、最新バージョンの [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) および [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) を使用する必要があります。  

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

  
## <a name="remarks"></a>解説

- Azure SQL Database へのプッシュ サブスクリプションのみがサポートされています。  
- レプリケーションを構成するには、[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を使用するか、パブリッシャーで Transact-SQL ステートメントを実行します。 Azure portal を使用してレプリケーションを構成することはできません。  
- レプリケーションで Azure SQL Database に接続するために使用できるのは、SQL Server 認証ログインのみです。
- レプリケーション テーブルには主キーが必要です。  
- 既存の Azure サブスクリプションが必要です。  
- Azure SQL Database のサブスクライバーは、どのリージョンであってもかまいません。  
- SQL Server 上の単一パブリケーションで、Azure SQL Database と、SQL Server (オンプレミスの SQL Server と Azure 仮想マシンの SQL Server) の両方のサブスクライバーをサポートできます。  
- レプリケーションの管理、監視、およびトラブルシューティングは、Azure SQL Database からではなく SQL Server から実行する必要があります。  
- SQL Database については、**sp_addsubscription** で `@subscriber_type = 0` のみがサポートされています。  
- Azure SQL Database では、双方向、即時、更新可能、またはピア ツー ピアのレプリケーションはサポートされていません。

## <a name="replication-architecture"></a>レプリケーション アーキテクチャ  

![図には、異なるリージョンにある複数のサブスクライバー クラスターを含む Azure SQL Database と、パブリッシャー、Logread 実行可能ファイル、およびリモート クラスターに接続するディストリビューター実行可能ファイルを含むオンプレミスの Azure 仮想マシンがあるレプリケーション アーキテクチャが示されています。](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>シナリオ  

### <a name="typical-replication-scenario"></a>一般的なレプリケーション シナリオ  

1. SQL Server データベースで、トランザクション レプリケーション パブリケーションを作成します。  
2. SQL Server で、**新しいサブスクリプション ウィザード** または Transact-SQL ステートメントを使用して、Azure SQL Database のサブスクリプションに対するプッシュを作成します。  
3. Azure SQL Database の単一およびプールされたデータベースでは、初期データ セットはスナップショットです。このスナップショットは、スナップショット エージェントによって作成され、ディストリビューション エージェントによって配布および適用されます。 SQL Managed Instance パブリッシャーでは、データベース バックアップを使用して Azure SQL Database サブスクライバーをシードすることもできます。

### <a name="data-migration-scenario"></a>データ移行シナリオ  

1. トランザクション レプリケーションを使用して、データを、SQL Server データベースから Azure SQL Database にレプリケートします。  
2. クライアントまたは中間層アプリケーションをリダイレクトし、データベースのコピーを更新します。  
3. テーブルの SQL Server のバージョンの更新を停止し、パブリケーションを削除します。  

## <a name="limitations"></a>制限事項

次のオプションは、Azure SQL Database サブスクリプションではサポートされていません。

- ファイル グループの関連付けのコピー  
- テーブルのパーティション分割構成のコピー  
- インデックスのパーティション分割構成のコピー  
- ユーザー定義の統計情報のコピー  
- 既定のバインドのコピー  
- ルールのバインドのコピー  
- フルテキスト インデックスのコピー  
- XML XSD のコピー  
- XML インデックスのコピー  
- アクセス許可のコピー  
- 空間インデックスのコピー  
- フィルター処理済みインデックスのコピー  
- データ圧縮属性のコピー  
- スパース列属性のコピー  
- MAX データ型への filestream の変換  
- MAX データ型への hierarchyid の変換  
- MAX データ型への spatial の変換  
- 拡張プロパティのコピー  

### <a name="limitations-to-be-determined"></a>未定の制限事項

- 照合順序のコピー  
- SP のシリアル化されたトランザクションでの実行  

## <a name="examples"></a>例

パブリケーションおよびプッシュ サブスクリプションを作成します。 詳細については、次を参照してください。
  
- [パブリケーションを作成する](/sql/relational-databases/replication/publish/create-a-publication)
- [プッシュ サブスクリプションを作成する](/sql/relational-databases/replication/create-a-push-subscription/)。サーバー名をサブスクライバーとして (**N'azuresqldbdns.database.windows.net'** など)、また、Azure SQL Database 名を宛先データベースとして (**AdventureWorks** など) 使用します。  

## <a name="see-also"></a>参照  

- [トランザクション レプリケーション](../managed-instance/replication-transactional-overview.md)
- [パブリケーションを作成する](/sql/relational-databases/replication/publish/create-a-publication)
- [プッシュ サブスクリプションを作成する](/sql/relational-databases/replication/create-a-push-subscription/)
- [レプリケーションの種類](/sql/relational-databases/replication/types-of-replication)
- [監視 (レプリケーション)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [サブスクリプションを初期化する](/sql/relational-databases/replication/initialize-a-subscription)
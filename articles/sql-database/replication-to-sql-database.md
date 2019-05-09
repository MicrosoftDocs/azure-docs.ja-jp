---
title: Azure SQL Database へのレプリケーション | Microsoft Docs
description: Azure SQL Database の単一データベースおよびエラスティック プール内のデータベースを使った SQL Server レプリケーションの使用について説明します
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
ms.date: 01/25/2019
ms.openlocfilehash: b9d6569504b5352c6187afe12d903c986019c517
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57860705"
---
# <a name="replication-to-sql-database-single-and-pooled-databases"></a>SQL Database の単一およびプールされたデータベースへのレプリケーション

Azure SQL Database の [SQL Database サーバー](sql-database-servers.md)で単一またはプールされたデータベースに対して SQL Server レプリケーションを構成できます。  

## <a name="supported-configurations"></a>**サポートされている構成:**
  
- SQL Server には、オンプレミスで実行されている SQL Server のインスタンス、またはクラウドの Azure 仮想マシンで実行されている SQL Server のインスタンスを指定できます。 詳細については、[Azure Virtual Machines 上の SQL Server の概要](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/)に関するページをご覧ください。  
- Azure SQL データベースは、SQL Server パブリッシャーのプッシュ サブスクライバーである必要があります。  
- ディストリビューション データベースとレプリケーション エージェントは、Azure SQL データベースに配置できません。  
- スナップショットおよび一方向のトランザクション レプリケーションがサポートされています。 ピア ツー ピア トランザクション レプリケーションおよびマージ レプリケーションはサポートされていません。
- Azure SQL Database Managed Instance のパブリック プレビューでレプリケーションを使用できます。 マネージド インスタンスでは、パブリッシャー、ディストリビューター、およびサブスクライバー データベースをホストできます。 詳細については、「[Replication with SQL Database Managed Instance (SQL Database Managed Instance でのレプリケーション)](replication-with-sql-database-managed-instance.md)」を参照してください。

## <a name="versions"></a>バージョン  

- パブリッシャーとディストリビューターは、少なくとも次のバージョンのいずれかでなければなりません。  
- SQL Server 2017 (14.x)
- SQL Server 2016 (13.x)
- SQL Server 2014 (12.x) SP1 CU3
- SQL Server 2014 (12.x) RTM CU10
- SQL Server 2012 (11.x) SP2 CU8 または SP3
- 以前のバージョンを使用してレプリケーションを構成しようとすると、エラー番号 MSSQL_REPL20084 (プロセスはサブスクライバーに接続できませんでした) および MSSQL_REPL40532 (ログインによって要求されたサーバー \<name > を開くことができません。 ログインに失敗しました) のエラーが発生する可能性があります。  
- Azure SQL Database のすべての機能を使用するには、最新バージョンの [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) および [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) を使用する必要があります。  
  
## <a name="remarks"></a>解説

- レプリケーションを構成するには、[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) を使用するか、パブリッシャーで Transact-SQL ステートメントを実行します。 Azure portal を使用してレプリケーションを構成することはできません。  
- レプリケーションで Azure SQL データベースに接続するために使用できるのは、SQL Server 認証ログインのみです。
- レプリケーション テーブルには主キーが必要です。  
- 既存の Azure サブスクリプションが必要です。  
- Azure SQL データベースのサブスクライバーは、どのリージョンであってもかまいません。  
- SQL Server 上の単一パブリケーションで、Azure SQL Database と、SQL Server (オンプレミスの SQL Server と Azure 仮想マシンの SQL Server) の両方のサブスクライバーをサポートできます。  
- レプリケーションの管理、監視、およびトラブルシューティングは、オンプレミスの SQL Server から実行する必要があります。  
- Azure SQL Database へのプッシュ サブスクリプションのみがサポートされています。  
- SQL Database については、**sp_addsubscription** で `@subscriber_type = 0` のみがサポートされています。  
- Azure SQL Database では、双方向、即時、更新可能、またはピア ツー ピアのレプリケーションはサポートされていません。

## <a name="replication-architecture"></a>レプリケーション アーキテクチャ  

![replication-to-sql-database](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>シナリオ  

### <a name="typical-replication-scenario"></a>一般的なレプリケーション シナリオ  

1. オンプレミスの SQL Server データベースで、トランザクション レプリケーション パブリケーションを作成します。  
2. オンプレミスの SQL Server で、**新しいサブスクリプション ウィザード**または Transact-SQL ステートメントを使用して、Azure SQL Database のサブスクリプションに対するプッシュを作成します。  
3. Azure SQL Database の単一およびプールされたデータベースでは、初期データ セットはスナップショットです。このスナップショットは、スナップショット エージェントによって作成され、ディストリビューション エージェントによって配布および適用されます。 マネージド インスタンスのデータベースでは、データベース バックアップを使用してサブスクライバー データベースをシードすることもできます。

### <a name="data-migration-scenario"></a>データ移行シナリオ  

1. トランザクション レプリケーションを使用して、データを、オンプレミス SQL Server データベースから Azure SQL Database にレプリケートします。  
2. クライアントまたは中間層アプリケーションをリダイレクトして、Azure SQL データベースのコピーを更新します。  
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
- アクセス許可のコピー  

### <a name="limitations-to-be-determined"></a>未定の制限事項

- 照合順序のコピー  
- SP のシリアル化されたトランザクションでの実行  

## <a name="examples"></a>例

パブリケーションおよびプッシュ サブスクリプションを作成します。 詳細については、次を参照してください。
  
- [パブリケーションを作成する](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [プッシュ サブスクリプションを作成する](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)。Azure SQL Database サーバー名をサブスクライバーとして (**N'azuresqldbdns.database.windows.net'** など)、また、Azure SQL データベース名を宛先データベースとして (**AdventureWorks** など) 使用します。  

## <a name="see-also"></a>関連項目  

- [トランザクション レプリケーション](sql-database-managed-instance-transactional-replication.md)
- [パブリケーションを作成する](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [プッシュ サブスクリプションを作成する](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [レプリケーションの種類](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [監視 (レプリケーション)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [サブスクリプションを初期化する](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  

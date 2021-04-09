---
title: Azure SQL Edge へのレプリケーションを構成する
description: Azure SQL Edge へのレプリケーションを構成する方法について説明します。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 3b424ece9207328d87068160f78ebc78a3bd1a8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395225"
---
# <a name="configure-replication-to-azure-sql-edge"></a>Azure SQL Edge へのレプリケーションを構成する 

一方向のトランザクション レプリケーションまたはスナップショット レプリケーションのプッシュ サブスクライバーとして、Azure SQL Edge インスタンスを構成できます。 トランザクション レプリケーション構成のパブリッシャーまたはディストリビューターとしてこのインスタンスを機能させることはできません。 Azure SQL Edge では、マージ レプリケーション、ピアツーピア レプリケーション、または Oracle パブリッシングはサポートされないことに注意してください。

## <a name="supported-configurations"></a>サポートされている構成
  
- Azure SQL Edge のインスタンスは、パブリッシャーのプッシュ サブスクライバーである必要があります。
- パブリッシャーとディストリビューターは、次のいずれかが可能です。
   - オンプレミスで実行されている SQL Server のインスタンス、または Azure 仮想マシンで実行されている SQL Server のインスタンス。 詳細については、[Azure Virtual Machines 上の SQL Server の概要](../azure-sql/virtual-machines/index.yml)に関するページをご覧ください。 SQL Server インスタンスでは、SQL Server 2016 よりも大きいバージョンが使用されている必要があります。
   - Azure SQL Managed Instance のインスタンス。 SQL Managed Instance では、パブリッシャー、ディストリビューター、サブスクライバーの各データベースをホストできます。 詳細については、「[Replication with SQL Database Managed Instance (SQL Database Managed Instance でのレプリケーション)](/azure/sql-database/replication-with-sql-database-managed-instance/)」を参照してください。

- ディストリビューション データベースとレプリケーション エージェントを Azure SQL Edge のインスタンスに配置することはできません。  

> [!NOTE]
> サポートされていないバージョンを使用してレプリケーションを構成しようとすると、次の 2 つのエラーが発生することがあります。MSSQL_REPL20084 ("プロセスでサブスクライバーに接続できませんでした。") および MSSQL_REPL40532 ("ログインによって要求されたサーバー \<name> を開くことができません。 ログインに失敗しました。")  

## <a name="remarks"></a>解説

レプリケーションを構成する際には、次の要件とベスト プラクティスを理解しておくことが重要です。

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を使用してレプリケーションを構成できます。 また、SQL Server Management Studio または [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) を使用して、パブリッシャーで Transact-SQL ステートメントを実行してこれを行うこともできます。
- Azure SQL Edge のインスタンスにレプリケートするには、SQL Server 認証を使用してサインインする必要があります。
- レプリケーション テーブルには主キーが必要です。
- SQL Server 上の単一パブリケーションで、Azure SQL Edge と、SQL Server (オンプレミスおよび Azure 仮想マシンの SQL Server) の両方のサブスクライバーをサポートできます。  
- レプリケーションの管理、監視、およびトラブルシューティングは、SQL Server インスタンスから実行する必要があります。  
- Azure SQL Edge へのプッシュ サブスクリプションのみがサポートされています。  
- Azure SQL Edge のストアド プロシージャ `sp_addsubscription` では、`@subscriber_type = 0` のみがサポートされています。  
- Azure SQL Edge では、双方向、即時、更新可能、またはピアツーピア レプリケーションはサポートされていません。
- Azure SQL Edge では、SQL Server または SQL Managed Instance で使用できる機能のサブセットのみがサポートされています。 サポートされていない機能が 1 つ以上含まれているデータベース (またはデータベース内のオブジェクト) をレプリケートする試みは失敗します。 たとえば、空間データ型のオブジェクトを含むデータベースをレプリケートしようとすると、エラーが発生します。 詳しくは、「[Azure SQL Edge でサポートされる機能](features.md)」をご覧ください。

## <a name="initialize-reference-data-on-an-instance-of-azure-sql-edge"></a>Azure SQL Edge のインスタンスで参照データを初期化する

時間の経過と共に変化する参照データを使用して、インスタンスを初期化できます。 たとえば、SQL Server インスタンスで機械学習モデルのトレーニングを行った後、ご自身の Azure SQL Edge のインスタンスでそれらを更新できます。 このようなシナリオでインスタンスを初期化する方法を次に示します。

1. SQL Server データベースで、トランザクション レプリケーション パブリケーションを作成します。  
2. SQL Server インスタンスで、**新しいサブスクリプション ウィザード** または Transact-SQL ステートメントを使用して、Azure SQL Edge のサブスクリプションに対するプッシュを作成します。  
3. スナップショット エージェントによって生成され、ディストリビューション エージェントによって配布および配信されたスナップショットを使用して、Azure SQL Edge 上のレプリケートされたデータベースを初期化できます。 別の方法として、パブリッシャーからのデータベースのバックアップを使用して初期化できます。 データベースのバックアップに Azure SQL Edge でサポートされていないオブジェクトまたは機能が含まれていた場合、復元操作は失敗することを忘れないでください。

## <a name="limitations"></a>制限事項

Azure SQL Edge サブスクリプションでは、次のオプションはサポートされていません。

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
- filestream、`hierarchyid`、または空間データ型のコピー
- `hierarchyid` の MAX データ型への変換  
- MAX データ型への spatial の変換  
- 拡張プロパティのコピー  
- アクセス許可のコピー  

## <a name="examples"></a>例

パブリケーションおよびプッシュ サブスクリプションを作成します。 詳細については、次を参照してください。
  
- [パブリケーションの作成](/sql/relational-databases/replication/publish/create-a-publication)
- Azure SQL Edge サーバー名と IP (例: **myEdgeinstance、1433**) をサブスクライバーとして使用し、Azure SQL Edge インスタンスのデータベース名 (例: **AdventureWorks**) を宛先データベースとして使用することで、[プッシュ サブスクリプションを作成します](/sql/relational-databases/replication/create-a-push-subscription/)。  

## <a name="next-steps"></a>次のステップ  

- [パブリケーションの作成](/sql/relational-databases/replication/publish/create-a-publication)
- [ssSDSFull](/sql/relational-databases/replication/create-a-push-subscription/)
- [レプリケーションの種類](/sql/relational-databases/replication/types-of-replication)
- [監視 (レプリケーション)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [サブスクリプションを初期化する](/sql/relational-databases/replication/initialize-a-subscription)
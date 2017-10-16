---
title: "TSQL で SQL データ ウェアハウスを作成 |Microsoft ドキュメント"
description: "TSQL と Azure SQL データ ウェアハウスを作成する方法します。"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: a4e2e68e-aa9c-4dd3-abb0-f7df997d237a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: create
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 10d8aa2b3ab8d7d8a9b91e95ffccf03faa89d237
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-sql-data-warehouse-database-by-using-transact-sql-tsql"></a>TRANSACT-SQL (TSQL) を使用して、SQL データ ウェアハウス データベースを作成します。
> [!div class="op_single_selector"]
> * [Azure ポータル](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

この記事では、T-SQL を使用して SQL データ ウェアハウスを作成する方法を示します。

## <a name="prerequisites"></a>前提条件
開始するには、次の必要があります。

* **Azure アカウント**: を参照してください[Azure 無料評価版][ Azure Free Trial]または[MSDN の Azure クレジット][ MSDN Azure Credits]にアカウントを作成します。
* **Azure の SQL server**: [Azure ポータルで Azure SQL データベースの論理サーバーを作成する] を参照してください [Azure ポータルで Azure SQL データベースの論理サーバーの作成] [PowerShell で Azure SQL データベースの論理サーバーの作成] または [PowerShell で Azure SQL データベースの論理サーバーを作成する] 詳細についてはします。
* **リソース グループ**: Azure の SQL server と同じリソース グループを使用するかを参照してください[リソース グループを作成する方法][how to create a resource group]です。
* **T-SQL を実行するよう環境**: 使用することができます[Visual Studio][Installing Visual Studio and SSDT]、 [sqlcmd][sqlcmd]、または[SSMS] [ SSMS] T-SQL を実行します。

> [!NOTE]
> SQL データ ウェアハウスを作成すると、課金対象の新しいサービス可能性があります。  参照してください[SQL Data Warehouse 料金][ SQL Data Warehouse pricing]料金の詳細についてはします。
>
>

## <a name="create-a-database-with-visual-studio"></a>Visual Studio でデータベースを作成します。
Visual Studio に慣れていない場合は、記事を参照して[クエリ Azure SQL Data Warehouse (Visual Studio)][Query Azure SQL Data Warehouse (Visual Studio)]です。  開始、Visual Studio で SQL Server オブジェクト エクスプ ローラーを開き、SQL データ ウェアハウス データベースをホストするサーバーに接続します。  に対して次の SQL コマンドを実行して SQL データ ウェアハウスを作成するには、接続、**マスター**データベース。  このコマンドは、サービス目標の DW400 を MySqlDwDb データベースを作成し、10 TB の最大サイズに拡大するデータベースを許可します。

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## <a name="create-a-database-with-sqlcmd"></a>Sqlcmd によるデータベースを作成します。
または、コマンド プロンプトで、次を実行して、sqlcmd で同じコマンドを実行できます。

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

既定の照合順序指定されていない場合は、SQL_Latin1_General_CP1_CI_AS の照合です。  `MAXSIZE` 250 GB ~ 240 TB にすることができます。  `SERVICE_OBJECTIVE`できる DW100 ~ DW2000 [DWU][DWU]です。  すべての有効な値の一覧は、MSDN ドキュメントを参照して[CREATE DATABASE][CREATE DATABASE]です。  MAXSIZE と SERVICE_OBJECTIVE を変更することができます、 [ALTER DATABASE] [ ALTER DATABASE] T-SQL コマンド。  作成後は、データベースの照合順序を変更できません。   転送中のすべてのクエリがキャンセルされる、サービスの再起動 DWU を変更中として SERVICE_OBJECTIVE を変更すると、ときに、警告を使用してください。  MAXSIZE を変更しても、サービスは再起動しません単純なメタデータの操作だけであります。

## <a name="next-steps"></a>次のステップ
SQL データ ウェアハウスが完了することができますをプロビジョニング[サンプル データを読み込む][ load sample data]かをチェック アウトする方法[開発][develop]、[読み込む][load]、または[移行][migrate]です。

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[how to create a SQL Data Warehouse from the Azure portal]: sql-data-warehouse-get-started-provision.md
[Query Azure SQL Data Warehouse (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[migrate]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md
[load]: sql-data-warehouse-overview-load.md
[load sample data]: sql-data-warehouse-load-sample-databases.md
[Create an Azure SQL database with the Azure Portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-create-and-configure-database-powershell
[how to create a resource group]: ../azure-resource-manager/resource-group-template-deploy-portal.md#create-resource-group
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references-->
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

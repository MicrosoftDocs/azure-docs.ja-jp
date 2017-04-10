---
title: "Azure Portal での SQL Data Warehouse の作成 | Microsoft Docs"
description: "Azure ポータルで Azure SQL Data Warehouse を作成する方法を説明します。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: 552e496e-d560-419c-9996-6bbc80c521cb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: create
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 75f4792ff2aa2b73ebc3ff976887a74ce09988f3
ms.lasthandoff: 03/10/2017


---
# <a name="create-an-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse の作成
> [!div class="op_single_selector"]
> * [Azure Portal](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

このチュートリアルでは、Azure ポータルを使用して、AdventureWorksDW サンプル データベースを含む SQL Data Warehouse を作成します。

## <a name="prerequisites"></a>前提条件
開始するには、以下が必要です。

* **Azure アカウント**: アカウントを作成するには、[Azure 無料試用版][Azure Free Trial]に関するページか [MSDN Azure クレジット][MSDN Azure Credits]に関するページにアクセスしてください。
* **Azure SQL サーバー**: 詳細については、[Azure Portal での Azure SQL データベースの作成][Create an Azure SQL database in the Azure portal]に関する記事を参照してください。

> [!NOTE]
> SQL Data Warehouse を作成すると、新しい課金対象サービスを使用することになる場合があります。  詳細については、「[SQL Data Warehouse の価格][SQL Data Warehouse pricing]」を参照してください。
>
>

## <a name="create-a-sql-data-warehouse"></a>SQL Data Warehouse の作成
1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. **[+ 新規]** > **[Databases]** > **[SQL Data Warehouse]** の順にクリックします。

    ![作成](./media/sql-data-warehouse-get-started-provision/create-sample.gif)
3. **[SQL Data Warehouse]** ブレードで必要な情報を入力し、[作成] を押して作成します。

    ![データベースを作成する](./media/sql-data-warehouse-get-started-provision/create-database.png)

   * **[サーバー]**: まず、サーバーを選択することをお勧めします。  
   * **[データベース名]**: SQL Data Warehouse の参照に使用される名前です。  サーバーに対して一意にする必要があります。
   * **[パフォーマンス]**: 最初は 400 [DWU][DWU] にすることをお勧めします。 スライダーを左または右に移動して、データ ウェアハウスのパフォーマンスを調整したり、作成後にスケールアップまたはスケールダウンしたりすることができます。  DWU の詳細については、[スケーリング](sql-data-warehouse-manage-compute-overview.md)に関するドキュメントまたは[価格のページ][SQL Data Warehouse pricing]を参照してください。
   * **subscription**: この SQL Data Warehouse の課金先の [subscription] を選択します。
   * **[リソース グループ]**: [リソース グループ][Resource group]は、Azure リソースのコレクション管理のサポートを目的としたコンテナーです。 [リソース グループ](../azure-resource-manager/resource-group-overview.md)に関する詳細情報を参照してください。
   * **[ソースの選択]**: **[ソースの選択]** > **[サンプル]** をクリックします。 **[サンプルの選択]** オプションが自動的に AdventureWorksDW に設定されます。

   > [!NOTE]
   > SQL Data Warehouse の既定の照合順序は、SQL_Latin1_General_CP1_CI_AS です。 別の照合順序が必要な場合は、[T-SQL][T-SQL] を使って別の照合順序のデータベースを作成できます。
   >
   >

1. **[作成]** をクリックして SQL Data Warehouse を作成します。
2. 数分間待ちます。 データ ウェアハウスの準備が完了すると、再び [Azure Portal](https://portal.azure.com)が表示されます。 SQL Data Warehouse は、ダッシュボード上で、SQL データベースの一覧か、作成に使用したリソース グループ内に表示されます。

    ![portal view](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="next-steps"></a>次のステップ
SQL Data Warehouse の作成は以上で完了です。いつでも[接続](sql-data-warehouse-connect-overview.md)して、クエリを実行することができます。

SQL Data Warehouse へのデータの読み込みについては、 [読み込みの概要](sql-data-warehouse-overview-load.md)に関するページを参照してください。

既存のデータベースを SQL Data Warehouse に移行する場合、[移行の概要](sql-data-warehouse-overview-migrate.md)に関するページを参照するか、[移行ユーティリティ](sql-data-warehouse-migrate-migration-utility.md)を使用してください。

TRANSACT-SQL を使用して、ファイアウォール規則を構成することもできます。 詳細については、[sp_set_firewall_rule][sp_set_firewall_rule] と [sp_set_database_firewall_rule][sp_set_database_firewall_rule] に関するページを参照してください。

[ベスト プラクティス][Best practices]も良い参考になります。

<!--Article references-->
[Create an Azure SQL database in the Azure portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-create-and-configure-database-powershell
[resource groups]: ../azure-resource-manager/resource-group-template-deploy-portal.md
[Best practices]: sql-data-warehouse-best-practices.md
[DWU]: sql-data-warehouse-overview-what-is.md
[subscription]: ../azure-glossary-cloud-terminology.md#subscription
[resource group]: ../azure-glossary-cloud-terminology.md#resource-group
[T-SQL]: ./sql-data-warehouse-get-started-create-database-tsql.md

<!--MSDN references-->
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx

<!--Other Web references-->
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F


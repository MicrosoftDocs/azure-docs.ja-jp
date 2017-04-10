---
title: "PowerShell を使用して SQL Data Warehouse を作成する | Microsoft Docs"
description: "PowerShell を使用して SQL Data Warehouse を作成します。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 97434863-7938-4129-8949-5a119f5949e3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: create
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 58ba34f8f99b7cd2b6a9a199bc70d79431405100
ms.lasthandoff: 03/10/2017


---
# <a name="create-sql-data-warehouse-using-powershell"></a>PowerShell を使用して SQL Data Warehouse を作成する
> [!div class="op_single_selector"]
> * [Azure Portal](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

この記事では、PowerShell を使用して SQL Data Warehouse を作成する方法を示します。

## <a name="prerequisites"></a>前提条件
開始するには、以下が必要です。

* **Azure アカウント**: アカウントを作成するには、[Azure 無料試用版][Azure Free Trial]に関するページか [MSDN Azure クレジット][MSDN Azure Credits]に関するページにアクセスしてください。
* **Azure SQL サーバー**: 詳細については、[Azure Portal での Azure SQL データベースの作成][Create an Azure SQL database in the Azure Portal]に関する記事か、[PowerShell を使用した Azure SQL データベースの作成][Create an Azure SQL database with PowerShell]に関する記事を参照してください。
* **リソース グループ**: 使用している Azure SQL Server と同じリソース グループを使用するか、 [リソース グループの作成方法](../azure-resource-manager/resource-group-portal.md)に関するセクションを参照してください。
* **PowerShell バージョン 1.0.3 以降**: **Get-Module -ListAvailable -Name Azure** を実行することで、バージョンを確認できます。  最新バージョンは、[Microsoft Web Platform Installer][Microsoft Web Platform Installer] からインストールできます。  最新バージョンのインストールの詳細については、[Azure PowerShell のインストールと構成方法][How to install and configure Azure PowerShell]に関するページを参照してください。

> [!NOTE]
> SQL Data Warehouse を作成すると、新しい課金対象サービスを使用することになる場合があります。  料金の詳細については、「[SQL Data Warehouse の価格][SQL Data Warehouse pricing]」を参照してください。
>
>

## <a name="create-a-sql-data-warehouse"></a>SQL Data Warehouse の作成
1. Windows PowerShell を開きます。
2. このコマンドレットを実行して、Azure リソース マネージャーにログインします。

    ```Powershell
    Login-AzureRmAccount
    ```
3. 現在のセッションに使用するサブスクリプションを選択します。

    ```Powershell
    Get-AzureRmSubscription    -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```
4. データベースを作成します。 この例では、サービス目標レベルが "DW400" で名前が "mynewsqldw" のデータベースを、"mywesteuroperesgp1" という名前のリソース グループ内の "sqldwserver1" という名前のサーバーに作成します。

   ```Powershell
   New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
   ```

必要なパラメーターは以下のとおりです。

* **RequestedServiceObjectiveName**: 要求する [DWU][DWU] の量。  サポートされている値は、DW100、DW200、DW300、DW400、DW500、DW600、DW1000、DW1200、DW1500、DW2000、DW3000、および DW6000 です。
* **DatabaseName**: 作成する SQL Data Warehouse の名前。
* **ServerName**: 作成の際に使用するサーバーの名前 (V12 にする必要があります)。
* **ResourceGroupName**: 使用するリソース グループ。  サブスクリプションで使用可能なリソース グループを調べるには Get-AzureResource を使用します。
* **Edition**: SQL Data Warehouse を作成するには、"DataWarehouse" に設定する必要があります。

省略可能なパラメーターは次のとおりです。

* **CollationName**: 照合順序が指定されていない場合の既定の照合順序は SQL_Latin1_General_CP1_CI_AS です。  データベースの照合順序は変更できません。
* **MaxSizeBytes**: データベースの既定の最大サイズは 10 GB です。

パラメーターのオプションの詳細については、「[New-AzureRmSqlDatabase][New-AzureRmSqlDatabase]」と「[CREATE DATABASE (Azure SQL Data Warehouse)][Create Database (Azure SQL Data Warehouse)]」を参照してください。

## <a name="next-steps"></a>次のステップ
SQL Data Warehouse のプロビジョニングが完了すると、[サンプル データを読み込んだり][loading sample data]、[開発][develop]、[読み込み][load]、[移行][migrate]の方法を確認したりできます。

プログラムで SQL Data Warehouse を管理する方法の詳細については、[PowerShell コマンドレットと REST API][PowerShell cmdlets and REST APIs] の使用方法に関する記事を参照してください。

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[migrate]: ./sql-data-warehouse-overview-migrate.md
[develop]: ./sql-data-warehouse-overview-develop.md
[load]: ./sql-data-warehouse-load-with-bcp.md
[loading sample data]: ./sql-data-warehouse-load-sample-databases.md
[PowerShell cmdlets and REST APIs]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Create an Azure SQL database in the Azure Portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-get-started-powershell.md
[how to create a resource group]: ../azure-resource-manager/resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references-->
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Create Database (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F


---
title: クイック スタート:Azure SQL データ ウェアハウスを作成する - Azure PowerShell | Microsoft Docs
description: Azure PowerShell を使用して、SQL Database の論理サーバー、サーバーレベルのファイアウォール規則、データ ウェアハウスをすばやく作成します。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: development
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 1f800ade5c5122f0891c9122f6698b6550048c67
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479335"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>クイック スタート:Azure PowerShell を使用して Azure SQL データ ウェアハウスを作成し、クエリを実行する

Azure PowerShell を使用して Azure SQL データ ウェアハウスをすばやく作成します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

> [!NOTE]
> SQL Data Warehouse を作成すると、新しい課金対象サービスを使用することになる場合があります。  詳細については、「[SQL Data Warehouse の価格](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)」を参照してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドで Azure サブスクリプションにサインインし、画面上の指示に従います。

```powershell
Connect-AzAccount
```

使用しているサブスクリプションを確認するには、[Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) を実行します。

```powershell
Get-AzSubscription
```

既定ではない別のサブスクリプションを使用する必要がある場合は、[Set-AzContext](/powershell/module/az.accounts/set-azcontext) を実行します。

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```


## <a name="create-variables"></a>変数の作成

このクイック スタートのスクリプトで使用する変数を定義します。

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehosue"
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドで [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。 次の例では、`myResourceGroup` という名前のリソース グループを `westeurope` の場所に作成します。

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>論理サーバーの作成

[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) コマンドを使用して [Azure SQL 論理サーバー](../sql-database/sql-database-logical-servers.md)を作成します。 論理サーバーには、ひとまとめにして管理される一連のデータベースが含まれています。 次の例では、管理者ユーザー `ServerAdmin` とパスワード `ChangeYourAdminPassword1` を使用し、ランダムに名前を付けたサーバーをリソース グループ内に作成しています。 これらの定義済みの値は、必要に応じて別の値に置き換えてください。

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>サーバーのファイアウォール規則の構成

[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) コマンドを使用して [Azure SQL のサーバーレベルのファイアウォール規則](../sql-database/sql-database-firewall-configure.md)を作成します。 サーバーレベルのファイアウォール規則を作成すると、SQL Server Management Studio や SQLCMD ユーティリティのような外部アプリケーションから、SQL Data Warehouse サービスのファイアウォールを介して SQL データ ウェアハウスに接続できます。 次の例では、他の Azure リソースに対してのみファイアウォールを開放しています。 外部から接続できるようにするには、実際の環境に合わせて IP アドレスを変更してください。 すべての IP アドレスを開放するには、開始 IP アドレスとして 0.0.0.0 を、終了アドレスとして 255.255.255.255 を使用します。

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL Database と SQL Data Warehouse はポート 1433 上で通信します。 企業ネットワーク内から接続しようとしても、ポート 1433 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、会社の IT 部門によってポート 1433 が開放されない限り、Azure SQL サーバーに接続することはできません。
>


## <a name="create-a-data-warehouse"></a>データ ウェアハウスの作成
この例では、以前に定義した変数を使用してデータ ウェアハウスを作成します。  サービス目標には DW100c を指定します。これは、データ ウェアハウス用の低コストの開始点です。 

```Powershell
New-AzSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW100c" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

必要なパラメーターは以下のとおりです。

* **RequestedServiceObjectiveName**:要求する[データ ウェアハウス ユニット](what-is-a-data-warehouse-unit-dwu-cdwu.md)の量。 この量を増やすと、コンピューティング コストが増加します。 サポートされている値の一覧については、[メモリとコンカレンシーの制限](memory-and-concurrency-limits.md)に関する記事をご覧ください。
* **DatabaseName**:作成する SQL Data Warehouse の名前。
* **ServerName**:作成の際に使用するサーバーの名前。
* **ResourceGroupName**:使用するリソース グループ。 サブスクリプションで使用可能なリソース グループを調べるには Get-AzureResource を使用します。
* **Edition**:SQL Data Warehouse を作成するには、"DataWarehouse" に設定する必要があります。

省略可能なパラメーターは次のとおりです。

- **CollationName**:照合順序が指定されていない場合の既定の照合順序は SQL_Latin1_General_CP1_CI_AS です。 データベースの照合順序は変更できません。
- **MaxSizeBytes**:データベースの既定の最大サイズは 240TB です。 最大サイズでは、行ストア データは制限されます。 列指向データには無制限のストレージがあります。

パラメーター オプションの詳細については、「[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)」を参照してください。


## <a name="clean-up-resources"></a>リソースのクリーンアップ

このコレクションの他のクイック スタート チュートリアルは、このクイック スタートに基づいています。 

> [!TIP]
> 引き続きクイック スタート チュートリアルの作業を行う場合は、このクイックスタートで作成したリソースをクリーンアップしないでください。 これ以上作業を行わない場合は、次の手順に従って、このクイック スタートで作成したすべてのリソースを Azure portal で削除してください。
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>次の手順

ここではデータ ウェアハウスを作成し、ファイアウォール規則を作成し、データ ウェアハウスに接続していくつかのクエリを実行しました。 Azure SQL Data Warehouse の詳細については、データの読み込みに関するチュートリアルを参照してください。
> [!div class="nextstepaction"]
>[SQL Data Warehouse にデータを読み込む](load-data-from-azure-blob-storage-using-polybase.md)

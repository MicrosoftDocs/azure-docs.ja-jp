---
title: 'クイックスタート: Azure PowerShell で専用 SQL プール (以前の SQL DW) を作成する'
description: Azure PowerShell からサーバーレベルのファイアウォール規則を使用して、専用 SQL プール (以前の SQL DW) をすばやく作成します。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse    , devx-track-azurepowershell
ms.openlocfilehash: 18fba46e4e8de14d9b3ec43455ba2abcc4218dc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98120191"
---
# <a name="quickstart-create-a-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>クイックスタート: Azure PowerShell で専用 SQL プール (以前の SQL DW) を作成する

Azure PowerShell を使用して、Azure Synapse Analytics の専用 SQL プール (以前の SQL DW) を作成します。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

> [!IMPORTANT]
> 専用 SQL プール (以前の SQL DW) を作成すると、課金対象のサービスが新たに生じることがあります。  詳細については、「[Azure Synapse Analytics の価格](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)」を参照してください。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) コマンドで Azure サブスクリプションにサインインし、画面上の指示に従います。

```powershell
Connect-AzAccount
```

使用しているサブスクリプションを確認するには、[Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) を実行します。

```powershell
Get-AzSubscription
```

既定ではない別のサブスクリプションを使用する必要がある場合は、[Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) を実行します。

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="create-variables"></a>変数の作成

このクイック スタートのスクリプトで使用する変数を定義します。

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehouse"
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) コマンドで [Azure リソース グループ](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)を作成します。 リソース グループとは、まとめてデプロイ、管理する複数の Azure リソースのコンテナーです。 次の例では、`westeurope` の場所に `myResourceGroup` という名前のリソース グループを作成します。

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```

## <a name="create-a-server"></a>サーバーの作成

[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) コマンドを使用して[論理 SQL サーバー](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)を作成します。 サーバーには、ひとまとめにして管理される一連のデータベースが含まれています。 次の例では、管理者ユーザー `ServerAdmin` とパスワード `ChangeYourAdminPassword1` を使用し、ランダムに名前を付けたサーバーをリソース グループ内に作成しています。 これらの定義済みの値は、必要に応じて別の値に置き換えてください。

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-level-firewall-rule"></a>サーバーレベルのファイアウォール規則の構成

[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) コマンドを使用して[サーバーレベルのファイアウォール規則](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)を作成します。 サーバーレベルのファイアウォール規則を作成すると、専用 SQL プール (以前の SQL DW) に対し、SQL Server Management Studio や SQLCMD ユーティリティのような外部アプリケーションから、専用 SQL プール サービスのファイアウォールを介して接続できます。

次の例では、他の Azure リソースに対してのみファイアウォールを開放しています。 外部から接続できるようにするには、実際の環境に合わせて IP アドレスを変更してください。 すべての IP アドレスを開放するには、開始 IP アドレスとして 0.0.0.0 を、終了アドレスとして 255.255.255.255 を使用します。

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL エンドポイントの通信は、ポート 1433 で行われます。 企業ネットワーク内から接続しようとしても、ポート 1433 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、会社の IT 部門によってポート 1433 が開放されない限り、サーバーに接続することはできません。
>

## <a name="create-a-dedicated-sql-pool-formerly-sql-dw"></a>専用 SQL プール (以前の SQL DW) を作成する

次の例では、以前に定義した変数を使用して専用 SQL プール (以前の SQL DW) を作成します。  サービス目標には DW100c を指定します。これは、専用 SQL プール (以前の SQL DW) 用の低コストの開始点です。

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

* **RequestedServiceObjectiveName**:要求する [データ ウェアハウス ユニット](what-is-a-data-warehouse-unit-dwu-cdwu.md)の量。 この量を増やすと、コンピューティング コストが増加します。 サポートされている値の一覧については、[メモリとコンカレンシーの制限](memory-concurrency-limits.md)に関する記事をご覧ください。
* **DatabaseName**:作成する専用 SQL プール (以前の SQL DW) の名前。
* **ServerName**:作成の際に使用するサーバーの名前。
* **ResourceGroupName**:使用するリソース グループ。 サブスクリプションで使用可能なリソース グループを調べるには Get-AzureResource を使用します。
* **Edition**:専用 SQL プール (以前の SQL DW) を作成するには "DataWarehouse" にする必要があります。

省略可能なパラメーターは次のとおりです。

* **CollationName**:照合順序が指定されていない場合の既定の照合順序は SQL_Latin1_General_CP1_CI_AS です。 データベースの照合順序は変更できません。
* **MaxSizeBytes**:データベースの既定の最大サイズは 240TB です。 最大サイズでは、行ストア データは制限されます。 列指向データには無制限のストレージがあります。

パラメーター オプションの詳細については、「[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)」を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このコレクションの他のクイック スタート チュートリアルは、このクイック スタートに基づいています。

> [!TIP]
> 引き続きクイック スタート チュートリアルの作業を行う場合は、このクイックスタートで作成したリソースをクリーンアップしないでください。 これ以上作業を行わない場合は、次の手順に従って、このクイック スタートで作成したすべてのリソースを Azure portal で削除してください。
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>次のステップ

専用 SQL プール (以前の SQL DW) を作成し、ファイアウォール規則を作成して専用 SQL プールに接続しました。 理解をさらに深めるために、[専用 SQL プールへのデータの読み込み](./load-data-from-azure-blob-storage-using-copy.md)に関する記事に進んでください。
---
title: DNS エイリアス (PowerShell & Azure CLI)
description: PowerShell コマンドレットと Azure CLI コマンドレットを使用すると、クライアントの構成を手動で変更することなく、新しいクライアント接続を Azure 内の別の SQL サーバーにリダイレクトできます。
keywords: DNS SQL Database
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: 02cfd839ed1b75fd85553f2e5a5150cadc29ff8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92790459"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>Azure SQL Database を参照する DNS エイリアス用の PowerShell
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

この記事では、Azure SQL Database をホストする [SQL サーバー](logical-servers.md)の DNS エイリアスを管理する方法を示す PowerShell スクリプトについて説明します。

> [!NOTE]
> この記事は、Azure PowerShell Az モジュールまたは Azure CLI のいずれかを使用するように更新されています。 AzureRM モジュールはまだ使用でき、少なくとも 2020 年 12 月までは引き続きバグ修正が行われます。
>
> Az モジュールと AzureRM の互換性の詳細については、「[Azure PowerShell Az モジュールの概要](/powershell/azure/new-azureps-module-az)」を参照してください。 インストール手順については、「[Azure PowerShell をインストールする](/powershell/azure/install-az-ps)」または「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

## <a name="dns-alias-in-connection-string"></a>接続文字列内の DNS エイリアス

SQL Server Management Studio (SSMS) などのクライアントでは、[論理 SQL サーバー](logical-servers.md)に接続するために、実際のサーバー名の代わりに DNS エイリアス名を指定できます。 次のサーバー文字列の例では、4 ノード サーバー文字列の中で、ドットで区切られた 1 つ目のノードでエイリアス *any-unique-alias-name* が使用されています。

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>前提条件

この記事で紹介した PowerShell デモ スクリプトを実行する際は、次の前提条件となるものを用意する必要があります。

- Azure のサブスクリプションとアカウント。無料試用版については、[Azure の試用版](https://azure.microsoft.com/free/)に関するページを参照してください。
- 2 台のサーバー

## <a name="example"></a>例

次のコード例は、複数の変数にリテラル値を割り当てることから始まっています。

コードを実行するには、すべてのプレースホルダーの値をシステム内の実際の値と一致するように編集します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用されているコマンドレットを次に示します。

- [New-AzSqlServerDNSAlias](/powershell/module/az.Sql/New-azSqlServerDnsAlias):Azure SQL Database サービス システム内に DNS エイリアスを作成します。 このエイリアスはサーバー 1 を参照します。
- [Get-AzSqlServerDNSAlias](/powershell/module/az.Sql/Get-azSqlServerDnsAlias):サーバー 1 に割り当てられているすべてのエイリアスを取得して一覧表示します。
- [Set-AzSqlServerDNSAlias](/powershell/module/az.Sql/Set-azSqlServerDnsAlias):エイリアスによって参照されるように構成されているサーバー名を、サーバー 1 から サーバー 2 に変更します。
- [Remove-AzSqlServerDNSAlias](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias):エイリアスの名前を使用して、サーバー 2 からエイリアスを削除します。

インストールまたはアップグレードするには、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。

`Get-Module -ListAvailable Az` を *powershell\_ise.exe* で使用して、バージョンを見つけます。

```powershell
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
Connect-AzAccount -SubscriptionName $subscriptionName;
$subscriptionId = Get-AzSubscription -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
New-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName `
    -Name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
Set-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -TargetServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName `
    -SourceServerResourceGroup $resourceGroupName -SourceServerName $sqlServerName `
    -SourceServerSubscriptionId $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
Remove-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName;
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用されているコマンドを次に示します。

- [az sql server dns-alias create](/powershell/module/az.Sql/New-azSqlServerDnsAlias):サーバーの DNS エイリアスを作成します。 このエイリアスはサーバー 1 を参照します。
- [az sql server dns-alias show](/powershell/module/az.Sql/Get-azSqlServerDnsAlias):サーバー 1 に割り当てられているすべてのエイリアスを取得して一覧表示します。
- [az sql server dns-alias set](/powershell/module/az.Sql/Set-azSqlServerDnsAlias):エイリアスによって参照されるように構成されているサーバー名を、サーバー 1 から サーバー 2 に変更します。
- [az sql server dns-alias delete](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias):エイリアスの名前を使用して、サーバー 2 からエイリアスを削除します。

インストールまたはアップグレードする必要には、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」をご覧ください。

```azurecli-interactive
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
az login -SubscriptionName $subscriptionName;
$subscriptionId = az account list[0].i -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
az sql server dns-alias create –-resource-group $resourceGroupName --server $sqlServerName `
    --name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
az sql server dns-alias show –-resource-group $resourceGroupName --server $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
az sql server dns-alias set –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName `
    --original-resource-group $resourceGroupName --original-server $sqlServerName `
    --original-subscription-id $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
az sql server dns-alias show –-resource-group $resourceGroupName2 --server $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
az sql server dns-alias delete –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName;
```

* * *

## <a name="next-steps"></a>次のステップ

SQL Database の DNS エイリアス機能の詳細については、「[Azure SQL Database の DNS エイリアス](./dns-alias-overview.md)」を参照してください。
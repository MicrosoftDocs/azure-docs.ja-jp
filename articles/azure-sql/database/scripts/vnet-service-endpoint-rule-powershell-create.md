---
title: 単一およびプールされたデータベース用の VNet エンドポイントおよび規則のための PowerShell
description: PowerShell スクリプトを提供して、Azure SQL Database と Azure Synapse 用 の Vertual Service エンドポイントを作成して管理します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 04/17/2019
ms.custom: sqldbrb=1
tags: azure-synapse
ms.openlocfilehash: 76a1d3aaadcbd1b15966a84f5dd2fe876f82c43a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102177625"
---
# <a name="powershell-create-a-virtual-service-endpoint-and-vnet-rule-for-azure-sql-database"></a>PowerShell:Azure SQL Database の Virtual Service エンドポイントと VNet 規則を作成する
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

"*仮想ネットワーク規則*" は 1 つのファイアウォール セキュリティ機能であり、[Azure SQL Database](../sql-database-paas-overview.md)、エラスティック プール、または [Azure Synapse](../../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 内のデータベース用の [論理 SQL サーバー](../logical-servers.md)で、仮想ネットワーク内の特定のサブネットから送信される通信が許可されるかどうかを制御します。

> [!IMPORTANT]
> この記事は、Azure Synapse (旧称 SQL DW) を含む Azure SQL Database に適用されます。 わかりやすくするために、この記事の Azure SQL Database という用語は、Azure SQL Database または Azure Synapse に属しているデータベースにあてはまります。 関連付けられているサービス エンドポイントがないため、この記事は Azure SQL Managed Instance には適用 "*されません*"。

この記事では、次のアクションを行う PowerShell スクリプトについて説明します。

1. サブネットに Microsoft Azure *仮想サービス エンドポイント* を作成します。
2. ご利用のサーバーのファイアウォールにエンドポイントを追加し、"*仮想ネットワーク規則*" を作成します。

詳しい背景については、[Azure SQL Database の仮想サービス エンドポイント][sql-db-vnet-service-endpoint-rule-overview-735r]に関する記事を参照してください。

> [!TIP]
> Azure SQL Database 用の仮想サービス エンドポイントの "*種類名*" を評価したり、またはご利用のサブネットに追加したりすることだけが必要な場合は、次をスキップして、より [ダイレクトな PowerShell スクリプト](#a-verify-subnet-is-endpoint-ps-100)に進むことができます。

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて [`Az.Sql` コマンドレット](/powershell/module/az.sql)を対象に行われます。 古いモジュールについては、「[AzureRM.Sql](/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

## <a name="major-cmdlets"></a>主なコマンドレット

この記事では、[**New-AzSqlServerVirtualNetworkRule** コマンドレット](/powershell/module/az.sql/new-azsqlservervirtualnetworkrule)について詳しく説明します。これにより、サーバーのアクセス制御リスト (ACL) にサブネット エンドポイントが追加され、それによって規則が作成されます。

次の一覧には、**New-AzSqlServerVirtualNetworkRule** の呼び出しを準備する際に実行する必要のあるその他の "*主な*" コマンドレットのシーケンスが示されています。 この記事の [スクリプト 3 "仮想ネットワーク規則"](#a-script-30) で、これらの呼び出しが発生します。

1. [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig):サブネット オブジェクトを作成します。
2. [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):仮想ネットワークを作成して、サブネットを付与します。
3. [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-azVirtualNetworkSubnetConfig):仮想サービス エンドポイントをご利用のサブネットに割り当てます。
4. [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork):ご利用の仮想ネットワークに加えられる更新を継続します。
5. [New-AzSqlServerVirtualNetworkRule](/powershell/module/az.sql/new-azsqlservervirtualnetworkrule):ご利用のサブネットがエンドポイントになった後に、そのサブネットを仮想ネットワーク規則として、ご利用のサーバーの ACL に追加します。
   - このコマンドレットは、Azure RM PowerShell モジュール バージョン 5.1.1 以降で、パラメーター **-IgnoreMissingVNetServiceEndpoint** を提供します。

## <a name="prerequisites-for-running-powershell"></a>PowerShell を実行するための前提条件

- [Azure portal][http-azure-portal-link-ref-477t] の使用などにより、既に Azure にログインできます。
- 既に、PowerShell スクリプトを実行できます。

> [!NOTE]
> サーバーに追加する VNet/サブネットに対してサービス エンドポイントが有効になっていることを確認してください。そうでない場合、VNet ファイアウォール規則の作成は失敗します。

## <a name="one-script-divided-into-four-chunks"></a>1 つのスクリプトが 4 つのチャンクに分割されています。

このデモで使用する PowerShell スクリプトは、小さなスクリプトのシーケンスに分割されています。 この分割が、学習を容易にし、柔軟性を提供します。 示されたシーケンスでスクリプトを実行する必要があります。 すぐにスクリプトを実行する時間がない場合は、スクリプト 4 の後に、実際のテストの出力が表示されます。

<a name="a-script-10"></a>

### <a name="script-1-variables"></a>スクリプト 1: 変数

この最初の PowerShell スクリプトは、変数に値を割り当てます。 後続のスクリプトは、これらの変数によって決まります。

> [!IMPORTANT]
> 必要に応じて、このスクリプトを実行する前に、値を編集できます。 例えば、既にリソース グループがある場合は、ご利用のリソース グループ名を割り当て値として編集することもできます。
>
> ご利用のサブスクリプション名をスクリプトで編集する必要があります。

### <a name="powershell-script-1-source-code"></a>PowerShell スクリプト 1 のソース コード

```powershell
######### Script 1 ########################################
##   LOG into to your Azure account.                     ##
##   (Needed only one time per powershell.exe session.)  ##
###########################################################

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]'
if ('yes' -eq $yesno) { Connect-AzAccount }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.
$SubscriptionName = 'yourSubscriptionName'
Select-AzSubscription -SubscriptionName $SubscriptionName

$ResourceGroupName = 'RG-YourNameHere'
$Region = 'westcentralus'

$VNetName = 'myVNet'
$SubnetName = 'mySubnet'
$VNetAddressPrefix = '10.1.0.0/16'
$SubnetAddressPrefix = '10.1.1.0/24'
$VNetRuleName = 'myFirstVNetRule-ForAcl'

$SqlDbServerName = 'mysqldbserver-forvnet'
$SqlDbAdminLoginName = 'ServerAdmin'
$SqlDbAdminLoginPassword = 'ChangeYourAdminPassword1'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql'  # Official type name.

Write-Host 'Completed script 1, the "Variables".'
```

<a name="a-script-20"></a>

### <a name="script-2-prerequisites"></a>スクリプト 2: 前提条件

このスクリプトは、エンドポイント アクションがある次のスクリプトを準備します。 このスクリプトは、次の一覧の項目が存在しない場合にのみ、その項目を作成します。 既にこれらの項目が存在することを確認している場合は、スクリプト 2 をスキップできます。

- Azure リソース グループ
- 論理 SQL サーバー

### <a name="powershell-script-2-source-code"></a>PowerShell スクリプト 2 のソース コード

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists."

$gottenResourceGroup = $null
$gottenResourceGroup = Get-AzResourceGroup -Name $ResourceGroupName -ErrorAction SilentlyContinue

if ($null -eq $gottenResourceGroup) {
    Write-Host "Creating your missing Resource Group - $ResourceGroupName."
    New-AzResourceGroup -Name $ResourceGroupName -Location $Region
} else {
    Write-Host "Good, your Resource Group already exists - $ResourceGroupName."
}

$gottenResourceGroup = $null

###########################################################
## Ensure your server already exists. ##
###########################################################

Write-Host "Check whether your server already exists."

$sqlDbServer = $null
$azSqlParams = @{
    ResourceGroupName = $ResourceGroupName
    ServerName        = $SqlDbServerName
    ErrorAction       = 'SilentlyContinue'
}
$sqlDbServer = Get-AzSqlServer @azSqlParams

if ($null -eq $sqlDbServer) {
    Write-Host "Creating the missing server - $SqlDbServerName."
    Write-Host "Gather the credentials necessary to next create a server."

    $sqlAdministratorCredentials = [pscredential]::new($SqlDbAdminLoginName,(ConvertTo-SecureString -String $SqlDbAdminLoginPassword -AsPlainText -Force))

    if ($null -eq $sqlAdministratorCredentials) {
        Write-Host "ERROR, unable to create SQL administrator credentials.  Now ending."
        return
    }

    Write-Host "Create your server."

    $sqlSrvParams = @{
        ResourceGroupName           = $ResourceGroupName
        ServerName                  = $SqlDbServerName
        Location                    = $Region
        SqlAdministratorCredentials = $sqlAdministratorCredentials
    }
    New-AzSqlServer @sqlSrvParams
} else {
    Write-Host "Good, your server already exists - $SqlDbServerName."
}

$sqlAdministratorCredentials = $null
$sqlDbServer = $null

Write-Host 'Completed script 2, the "Prerequisites".'
```

<a name="a-script-30"></a>

## <a name="script-3-create-an-endpoint-and-a-rule"></a>スクリプト 3: エンドポイントと規則の作成

このスクリプトは、サブネットをもつ仮想ネットワークを作成します。 次に、このスクリプトが **Microsoft.Sql** エンドポイントの種類をご利用のサブネットに割り当てます。 最後に、スクリプトにより、アクセス制御リスト (ACL) にご利用のサブネットが追加され、それによって規則が作成されます。

### <a name="powershell-script-3-source-code"></a>PowerShell スクリプト 3 のソース コード

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network."

$subnetParams = @{
    Name            = $SubnetName
    AddressPrefix   = $SubnetAddressPrefix
    ServiceEndpoint = $ServiceEndpointTypeName_SqlDb
}
$subnet = New-AzVirtualNetworkSubnetConfig @subnetParams

Write-Host "Create a virtual network '$VNetName'.`nGive the subnet to the virtual network that we created."

$vnetParams = @{
    Name              = $VNetName
    AddressPrefix     = $VNetAddressPrefix
    Subnet            = $subnet
    ResourceGroupName = $ResourceGroupName
    Location          = $Region
}
$vnet = New-AzVirtualNetwork @vnetParams

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet."

$vnetSubParams = @{
    Name            = $SubnetName
    AddressPrefix   = $SubnetAddressPrefix
    VirtualNetwork  = $vnet
    ServiceEndpoint = $ServiceEndpointTypeName_SqlDb
}
$vnet = Set-AzVirtualNetworkSubnetConfig @vnetSubParams

Write-Host "Persist the updates made to the virtual network > subnet."

$vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet

$vnet.Subnets[0].ServiceEndpoints  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object."

$vnet = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VNetName

$subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vnet

Write-Host "Add the subnet .Id as a rule, into the ACLs for your server."

$ruleParams = @{
    ResourceGroupName      = $ResourceGroupName
    ServerName             = $SqlDbServerName
    VirtualNetworkRuleName = $VNetRuleName
    VirtualNetworkSubnetId = $subnet.Id
}
New-AzSqlServerVirtualNetworkRule @ruleParams 

Write-Host "Verify that the rule is in the SQL Database ACL."

$rule2Params = @{
    ResourceGroupName      = $ResourceGroupName
    ServerName             = $SqlDbServerName
    VirtualNetworkRuleName = $VNetRuleName
}
Get-AzSqlServerVirtualNetworkRule @rule2Params

Write-Host 'Completed script 3, the "Virtual-Network-Rule".'
```

<a name="a-script-40"></a>

## <a name="script-4-clean-up"></a>スクリプト 4: クリーンアップ

この最後のスクリプトは、デモ用に前のスクリプトで作成したリソースを削除します。 ただし、次を削除する前に、スクリプトが確認を求めます。

- 論理 SQL サーバー
- Azure リソース グループ

スクリプト 1 が完了したら、いつでもスクリプト 4 を実行できます。

### <a name="powershell-script-4-source-code"></a>PowerShell スクリプト 4 のソース コード

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL Database ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL Database ACL."

$removeParams = @{
    ResourceGroupName      = $ResourceGroupName
    ServerName             = $SqlDbServerName
    VirtualNetworkRuleName = $VNetRuleName
    ErrorAction            = 'SilentlyContinue'
}
Remove-AzSqlServerVirtualNetworkRule @removeParams

Write-Host "Delete the endpoint from the subnet."

$vnet = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VNetName

Remove-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vnet

Write-Host "Delete the virtual network (thus also deletes the subnet)."

$removeParams = @{
    Name              = $VNetName
    ResourceGroupName = $ResourceGroupName
    ErrorAction       = 'SilentlyContinue'
}
Remove-AzVirtualNetwork @removeParams

###########################################################
##   Clean-up phase B:  Conditional deletes.             ##
##                                                       ##
##   These might have already existed, so user might     ##
##   want to keep.                                       ##
##                                                       ##
##   1. Logical SQL server                        ##
##   2. Azure resource group                             ##
###########################################################

$yesno = Read-Host 'CAUTION !: Do you want to DELETE your server AND your resource group?  [yes/no]'
if ('yes' -eq $yesno) {
    Write-Host "Remove the server."

    $removeParams = @{
        ServerName        = $SqlDbServerName
        ResourceGroupName = $ResourceGroupName
        ErrorAction       = 'SilentlyContinue'
    }
    Remove-AzSqlServer @removeParams

    Write-Host "Remove the Azure Resource Group."
    
    Remove-AzResourceGroup -Name $ResourceGroupName -ErrorAction SilentlyContinue
} else {
    Write-Host "Skipped over the DELETE of SQL Database and resource group."
}

Write-Host 'Completed script 4, the "Clean-Up".'
```

<a name="a-actual-output"></a>

<a name="a-verify-subnet-is-endpoint-ps-100"></a>

## <a name="verify-your-subnet-is-an-endpoint"></a>自分のサブネットがエンドポイントであることを確認する

既に **Microsoft.Sql** という種類名が割り当てられたサブネットがある場合、それは既に仮想サービス エンドポイントであることを意味します。 [Azure portal][http-azure-portal-link-ref-477t] を使用して、エンドポイントから仮想ネットワーク規則を作成できます。

また、**Microsoft.Sql** という種類名のサブネットがあるかどうかわからない場合もあります。 次の PowerShell スクリプトを実行して、次の操作を行います。

1. サブネットに **Microsoft.Sql** という種類名があるかを確認します。
2. 種類名が指定されていない場合は、必要に応じて割り当てます。
    - 指定されていない種類名を適用する前に、スクリプトが *確認* を求めます。

### <a name="phases-of-the-script"></a>スクリプトのフェーズ

PowerShell スクリプトのフェーズは次の通りです。

1. 自分の Azure アカウントにログインします。PS セッションごとに 1 回のみ必要です。  変数を割り当てます。
2. ご利用の仮想ネットワークとサブネットを順に検索します。
3. ご利用のサブネットはエンドポイント サーバーの種類が **Microsoft.Sql** としてタグ付けされていますか?
4. ご利用のサブネットに、**Microsoft.Sql** という種類名の仮想サービス エンドポイントを追加します。

> [!IMPORTANT]
> このスクリプトを実行する前に、スクリプトの先頭近くにある $ 変数に割り当てられた値を編集する必要があります。

### <a name="direct-powershell-source-code"></a>ダイレクトな PowerShell のソース コード

この PowerShell スクリプトは、ユーザーが確認を求められたときに [はい] と応答しない限り、何も更新しません。 このスクリプトは、ご利用のサブネットに **Microsoft.Sql** という種類名を追加できます。 ただし、このスクリプトが追加を試すのは、ご利用のサブネットに種類名がない場合にのみ限ります。

```powershell
### 1. LOG into to your Azure account, needed only once per PS session.  Assign variables.
$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]'
if ('yes' -eq $yesno) { Connect-AzAccount }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName = 'yourSubscriptionName'
Select-AzSubscription -SubscriptionName "$SubscriptionName"

$ResourceGroupName = 'yourRGName'
$VNetName = 'yourVNetName'
$SubnetName = 'yourSubnetName'
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.' # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql'  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.
# Search for the virtual network.
$vnet = $null
$vnet = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VNetName

if ($vnet -eq $null) {
    Write-Host "Caution: No virtual network found by the name '$VNetName'."
    return
}

$subnet = $null
for ($nn = 0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $subnet = $vnet.Subnets[$nn]
    if ($subnet.Name -eq $SubnetName) { break }
    $subnet = $null
}

if ($null -eq $subnet) {
    Write-Host "Caution: No subnet found by the name '$SubnetName'"
    Return
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?
$endpointMsSql = $null
for ($nn = 0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++) {
    $endpointMsSql = $subnet.ServiceEndpoints[$nn]
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb) {
        $endpointMsSql
        break
    }
    $endpointMsSql = $null
}

if ($null -eq $endpointMsSql) {
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'."
    return
} else {
    Write-Host "Caution: Subnet found, but not yet tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'."

    # Ask the user for confirmation.
    $yesno = Read-Host 'Do you want the PS script to apply the endpoint type name to your subnet?  [yes/no]'
    if ('no' -eq $yesno) { return }
}

### 4. Add a Virtual Service endpoint of type name 'Microsoft.Sql', on your subnet.
$setParams = @{
    Name            = $SubnetName
    AddressPrefix   = $SubnetAddressPrefix
    VirtualNetwork  = $vnet
    ServiceEndpoint = $ServiceEndpointTypeName_SqlDb
}
$vnet = Set-AzVirtualNetworkSubnetConfig @setParams

# Persist the subnet update.
$vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet

for ($nn = 0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $vnet.Subnets[0].ServiceEndpoints # Display.
}
```

<!-- Link references: -->
[sql-db-vnet-service-endpoint-rule-overview-735r]:../vnet-service-endpoint-rule-overview.md
[http-azure-portal-link-ref-477t]: https://portal.azure.com/

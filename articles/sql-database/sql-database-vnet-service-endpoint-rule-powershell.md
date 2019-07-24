---
title: Azure SQL の単一およびプール データベース用の VNet エンドポイントおよび規則のための PowerShell | Microsoft Docs
description: PowerShell スクリプトを提供して、Azure SQL Database と SQL Data Warehouse.用 の Vertual Service エンドポイントを作成して管理します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: genemi, vanto
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 6713182003a280c1d53e904209159b55b4ad01c6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57855573"
---
# <a name="powershell--create-a-virtual-service-endpoint-and-vnet-rule-for-sql"></a>PowerShell: SQL の Virtual Service エンドポイントと VNet 規則を作成する

*仮想ネットワーク規則*は 1 つのファイアウォール セキュリティ機能であり、Azure [SQL Database](sql-database-technical-overview.md) 内の単一データベースおよびエラスティック プール用、または [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 内のデータベース用のデータベース サーバーが、仮想ネットワーク内の特定のサブネットから送信される通信を許可するかどうかを制御します。

> [!IMPORTANT]
> この記事は Azure SQL サーバーのほか、その Azure SQL サーバーに作成される SQL Database と SQL Data Warehouse の両方に当てはまります。 わかりやすいように、SQL Database という言葉で SQL Database と SQL Data Warehouse の両方を言い表します。 関連付けられているサービス エンドポイントがないため、この記事は Azure SQL Database の**マネージド インスタンス** デプロイには*適用されません*。

この記事では、次のアクションを行う PowerShell スクリプトについて説明します。

1. サブネットに Microsoft Azure *仮想サービス エンドポイント*を作成します。
2. ご利用の Azure SQL Database サーバーのファイアウォールにエンドポイントを追加し、*仮想ネットワーク規則*を作成します。

規則作成の動機については、[Azure SQL Database の仮想サービス エンドポイント][sql-db-vnet-service-endpoint-rule-overview-735r]に関する記事で説明しています。

> [!TIP]
> SQL Database 用の仮想サービス エンドポイントの*種類名*を評価したり、またはご利用のサブネットに追加したりすることだけが必要な場合は、次をスキップして、より[ダイレクトな PowerShell スクリプト](#a-verify-subnet-is-endpoint-ps-100)に進めます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

## <a name="major-cmdlets"></a>主なコマンドレット

この記事では、**New-AzSqlServerVirtualNetworkRule** コマンドレットについて詳しく説明します。これは Azure SQL Database サーバーのアクセス制御リスト (ACL) にサブネット エンドポイントを追加し、それによって規則を作成します。

次の一覧には、**New-AzSqlServerVirtualNetworkRule** の呼び出しを準備する際に実行する必要のあるその他の "*主な*" コマンドレットのシーケンスが示されています。 この記事の [スクリプト 3 "仮想ネットワーク 規則"](#a-script-30) で、これらの呼び出しが発生します。

1. [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig):サブネット オブジェクトを作成します。
2. [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork):仮想ネットワークを作成して、サブネットを付与します。
3. [Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetworkSubnetConfig):仮想サービス エンドポイントをご利用のサブネットに割り当てます。
4. [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetwork):ご利用の仮想ネットワークに加えられる更新を継続します。
5. [New-AzSqlServerVirtualNetworkRule](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlservervirtualnetworkrule):ご利用のサブネットがエンドポイントになった後に、そのサブネットを仮想ネットワーク規則として、ご利用の Azure SQL Database サーバーの ACL に追加します。
   - このコマンドレットは、Azure RM PowerShell モジュール バージョン 5.1.1 以降で、パラメーター **-IgnoreMissingVNetServiceEndpoint** を提供します。

## <a name="prerequisites-for-running-powershell"></a>PowerShell を実行するための前提条件

- [Azure Portal][http-azure-portal-link-ref-477t] を通じて、既に Azure にログインできます。
- 既に、PowerShell スクリプトを実行できます。

> [!NOTE]
> サーバーに追加する VNet/サブネットに対してサービス エンドポイントが有効になっていることを確認してください。そうでない場合、VNet ファイアウォール規則の作成は失敗します。

## <a name="one-script-divided-into-four-chunks"></a>1 つのスクリプトが 4 つのチャンクに分割されています。

このデモで使用する PowerShell スクリプトは、小さなスクリプトのシーケンスに分割されています。 この分割が、学習を容易にし、柔軟性を提供します。 示されたシーケンスでスクリプトを実行する必要があります。 すぐにスクリプトを実行する時間がない場合は、スクリプト 4 の後に、実際のテストの出力が表示されます。

<a name="a-script-10" />

### <a name="script-1-variables"></a>スクリプト 1: variables

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

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.

$SubscriptionName = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName $SubscriptionName;

$ResourceGroupName = 'RG-YourNameHere';
$Region            = 'westcentralus';

$VNetName            = 'myVNet';
$SubnetName          = 'mySubnet';
$VNetAddressPrefix   = '10.1.0.0/16';
$SubnetAddressPrefix = '10.1.1.0/24';
$VNetRuleName        = 'myFirstVNetRule-ForAcl';

$SqlDbServerName         = 'mysqldbserver-forvnet';
$SqlDbAdminLoginName     = 'ServerAdmin';
$SqlDbAdminLoginPassword = 'ChangeYourAdminPassword1';

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Official type name.

Write-Host 'Completed script 1, the "Variables".';
```

<a name="a-script-20" />

### <a name="script-2-prerequisites"></a>スクリプト 2: 前提条件

このスクリプトは、エンドポイント アクションがある次のスクリプトを準備します。 このスクリプトは、次の一覧の項目が存在しない場合にのみ、その項目を作成します。 既にこれらの項目が存在することを確認している場合は、スクリプト 2 をスキップできます。

- Azure リソース グループ
- Azure SQL Database サーバー

### <a name="powershell-script-2-source-code"></a>PowerShell スクリプト 2 のソース コード

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists.";

$gottenResourceGroup = $null;

$gottenResourceGroup = Get-AzResourceGroup `
  -Name        $ResourceGroupName `
  -ErrorAction SilentlyContinue;

if ($null -eq $gottenResourceGroup)
{
    Write-Host "Creating your missing Resource Group - $ResourceGroupName.";

    $gottenResourceGroup = New-AzResourceGroup `
      -Name $ResourceGroupName `
      -Location $Region;

    $gottenResourceGroup;
}
else { Write-Host "Good, your Resource Group already exists - $ResourceGroupName."; }

$gottenResourceGroup = $null;

###########################################################
## Ensure your Azure SQL Database server already exists. ##
###########################################################

Write-Host "Check whether your Azure SQL Database server already exists.";

$sqlDbServer = $null;

$sqlDbServer = Get-AzSqlServer `
  -ResourceGroupName $ResourceGroupName `
  -ServerName        $SqlDbServerName `
  -ErrorAction       SilentlyContinue;

if ($null -eq $sqlDbServer)
{
    Write-Host "Creating the missing Azure SQL Database server - $SqlDbServerName.";

    Write-Host "Gather the credentials necessary to next create an Azure SQL Database server.";

    $sqlAdministratorCredentials = New-Object `
      -TypeName     System.Management.Automation.PSCredential `
      -ArgumentList `
        $SqlDbAdminLoginName, `
        $(ConvertTo-SecureString `
            -String      $SqlDbAdminLoginPassword `
            -AsPlainText `
            -Force `
         );

    if ($null -eq $sqlAdministratorCredentials)
    {
        Write-Host "ERROR, unable to create SQL administrator credentials.  Now ending.";
        return;
    }

    Write-Host "Create your Azure SQL Database server.";

    $sqlDbServer = New-AzSqlServer `
      -ResourceGroupName $ResourceGroupName `
      -ServerName        $SqlDbServerName `
      -Location          $Region `
      -SqlAdministratorCredentials $sqlAdministratorCredentials;

    $sqlDbServer;
}
else { Write-Host "Good, your Azure SQL Database server already exists - $SqlDbServerName."; }

$sqlAdministratorCredentials = $null;
$sqlDbServer                 = $null;

Write-Host 'Completed script 2, the "Prerequisites".';
```

<a name="a-script-30" />

## <a name="script-3-create-an-endpoint-and-a-rule"></a>スクリプト 3: エンドポイントと規則の作成

このスクリプトは、サブネットをもつ仮想ネットワークを作成します。 次に、このスクリプトが **Microsoft.Sql** エンドポイントの種類をご利用のサブネットに割り当てます。 最後に、スクリプトは、SQL Database サーバーのアクセス制御リスト (ACL) にご利用のサブネットを追加し、それによって規則を作成します。

### <a name="powershell-script-3-source-code"></a>PowerShell スクリプト 3 のソース コード

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network.";

$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Create a virtual network '$VNetName'." `
  "  Give the subnet to the virtual network that we created.";

$vnet = New-AzVirtualNetwork `
  -Name              $VNetName `
  -AddressPrefix     $VNetAddressPrefix `
  -Subnet            $subnet `
  -ResourceGroupName $ResourceGroupName `
  -Location          $Region;

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.";

$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Persist the updates made to the virtual network > subnet.";

$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.";

$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnet.Id;

$vnetRuleObject1;

Write-Host "Verify that the rule is in the SQL DB ACL.";

$vnetRuleObject2 = Get-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName;

$vnetRuleObject2;

Write-Host 'Completed script 3, the "Virtual-Network-Rule".';
```

<a name="a-script-40" />

## <a name="script-4-clean-up"></a>スクリプト 4: クリーンアップ

この最後のスクリプトは、デモ用に前のスクリプトで作成したリソースを削除します。 ただし、次を削除する前に、スクリプトが確認を求めます。

- Azure SQL Database サーバー
- Azure リソース グループ

スクリプト 1 が完了したら、いつでもスクリプト 4 を実行できます。

### <a name="powershell-script-4-source-code"></a>PowerShell スクリプト 4 のソース コード

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL DB ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL DB ACL.";

Remove-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -ErrorAction            SilentlyContinue;

Write-Host "Delete the endpoint from the subnet.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

Remove-AzVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Delete the virtual network (thus also deletes the subnet).";

Remove-AzVirtualNetwork `
  -Name              $VNetName `
  -ResourceGroupName $ResourceGroupName `
  -ErrorAction       SilentlyContinue;

###########################################################
##   Clean-up phase B:  Conditional deletes.             ##
##                                                       ##
##   These might have already existed, so user might     ##
##   want to keep.                                       ##
##                                                       ##
##   1. Azure SQL Database server                        ##
##   2. Azure resource group                             ##
###########################################################

$yesno = Read-Host 'CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]';
if ('yes' -eq $yesno)
{
    Write-Host "Remove the Azure SQL DB server.";

    Remove-AzSqlServer `
      -ServerName        $SqlDbServerName `
      -ResourceGroupName $ResourceGroupName `
      -ErrorAction       SilentlyContinue;

    Write-Host "Remove the Azure Resource Group.";

    Remove-AzResourceGroup `
      -Name        $ResourceGroupName `
      -ErrorAction SilentlyContinue;
}
else
{
    Write-Host "Skipped over the DELETE of SQL Database and resource group.";
}

Write-Host 'Completed script 4, the "Clean-Up".';
```

<a name="a-actual-output" />

## <a name="actual-output-from-scripts-1-through-4"></a>スクリプト 1 から 4 の実際の出力

このテスト ランの出力は、省略された形式で次に表示されます。 現時点で実際に PowerShell スクリプトを実行したくない場合に、出力が役に立つ場合があります。

```cmd
[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s1-variables.ps1
Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]: yes


Environment           : AzureCloud
Account               : xx@microsoft.com
TenantId              : 11111111-1111-1111-1111-111111111111
SubscriptionId        : 22222222-2222-2222-2222-222222222222
SubscriptionName      : MySubscriptionName
CurrentStorageAccount :



[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s2-prerequisites.ps1
Check whether your Resource Group already exists.
Creating your missing Resource Group - RG-YourNameHere.


ResourceGroupName : RG-YourNameHere
Location          : westcentralus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/22222222-2222-2222-2222-222222222222/resourceGroups/RG-YourNameHere

Check whether your Azure SQL Database server already exists.
Creating the missing Azure SQL Database server - mysqldbserver-forvnet.
Gather the credentials necessary to next create an Azure SQL Database server.
Create your Azure SQL Database server.

ResourceGroupName        : RG-YourNameHere
ServerName               : mysqldbserver-forvnet
Location                 : westcentralus
SqlAdministratorLogin    : ServerAdmin
SqlAdministratorPassword :
ServerVersion            : 12.0
Tags                     :
Identity                 :

Completed script 2, the "Prerequisites".

[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s3-vnet-rule.ps1
Define a subnet 'mySubnet', to be given soon to a virtual network.
Create a virtual network 'myVNet'.   Give the subnet to the virtual network that we created.
WARNING: The output object type of this cmdlet will be modified in a future release.
Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
Persist the updates made to the virtual network > subnet.

Get the subnet object.
Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.
ProvisioningState Service       Locations
----------------- -------       ---------
Succeeded         Microsoft.Sql {westcentralus}

Verify that the rule is in the SQL DB ACL.

Completed script 3, the "Virtual-Network-Rule".

[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s4-clean-up.ps1
Delete the rule from the SQL DB ACL.

Delete the endpoint from the subnet.


Delete the virtual network (thus also deletes the subnet).
CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]: yes
Remove the Azure SQL DB server.

ResourceGroupName        : RG-YourNameHere
ServerName               : mysqldbserver-forvnet
Location                 : westcentralus
SqlAdministratorLogin    : ServerAdmin
SqlAdministratorPassword :
ServerVersion            : 12.0
Tags                     :
Identity                 :

Remove the Azure Resource Group.
True
Completed script 4, the "Clean-Up".
```

これで主な PowerShell スクリプトは終わりです。

<a name="a-verify-subnet-is-endpoint-ps-100" />

## <a name="verify-your-subnet-is-an-endpoint"></a>自分のサブネットがエンドポイントであることを確認する

既に **Microsoft.Sql** という種類名が割り当てられたサブネットがある場合、それは既に仮想サービス エンドポイントであることを意味します。 [Azure Portal][http-azure-portal-link-ref-477t] を使用して、エンドポイントから仮想ネットワーク規則を作成できます。

また、**Microsoft.Sql** という種類名のサブネットがあるかどうかわからない場合もあります。 次の PowerShell スクリプトを実行して、次の操作を行います。

1. サブネットに **Microsoft.Sql** という種類名があるかを確認します。
2. 種類名が指定されていない場合は、必要に応じて割り当てます。
    - 指定されていない種類名を適用する前に、スクリプトが*確認*を求めます。

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

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName  = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName "$SubscriptionName";

$ResourceGroupName   = 'yourRGName';
$VNetName            = 'yourVNetName';
$SubnetName          = 'yourSubnetName';
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.'; # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.

# Search for the virtual network.
$vnet = $null;
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

if ($vnet -eq $null)
{
    Write-Host "Caution: No virtual network found by the name '$VNetName'.";
    Return;
}

$subnet = $null;
for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++)
{
    $subnet = $vnet.Subnets[$nn];
    if ($subnet.Name -eq $SubnetName)
    { break; }
    $subnet = $null;
}

if ($subnet -eq $null)
{
    Write-Host "Caution: No subnet found by the name '$SubnetName'";
    Return;
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?

$endpointMsSql = $null;
for ($nn=0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++)
{
    $endpointMsSql = $subnet.ServiceEndpoints[$nn];
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb)
    {
        $endpointMsSql;
        break;
    }
    $endpointMsSql = $null;
}

if ($endpointMsSql -ne $null)
{
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";
    Return;
}
else
{
    Write-Host "Caution: Subnet found, but not yet tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";

    # Ask the user for confirmation.
    $yesno = Read-Host 'Do you want the PS script to apply the endpoint type name to your subnet?  [yes/no]';
    if ('no' -eq $yesno) { Return; }
}

### 4. Add a Virtual Service endpoint of type name 'Microsoft.Sql', on your subnet.

$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

# Persist the subnet update.
$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++)
{ $vnet.Subnets[0].ServiceEndpoints; }  # Display.
```

### <a name="actual-output"></a>実際の出力

次のブロックでは、実際のフィードバック (と表面的な編集) を表示します。

```powershell
<# Our output example (with cosmetic edits), when the subnet was already tagged:

Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]: no


Environment           : AzureCloud
Account               : xx@microsoft.com
TenantId              : 11111111-1111-1111-1111-111111111111
SubscriptionId        : 22222222-2222-2222-2222-222222222222
SubscriptionName      : MySubscriptionName
CurrentStorageAccount :


ProvisioningState : Succeeded
Service           : Microsoft.Sql
Locations         : {westcentralus}

Good: Subnet found, and is already tagged as an endpoint of type 'Microsoft.Sql'.
#>
```

<!-- Link references: -->

[sql-db-vnet-service-endpoint-rule-overview-735r]: sql-database-vnet-service-endpoint-rule-overview.md

[http-azure-portal-link-ref-477t]: https://portal.azure.com/

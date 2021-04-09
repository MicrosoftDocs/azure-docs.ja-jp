---
title: PowerShell を使用した Azure DS Domain Services の有効化 | Microsoft Docs
description: Azure AD PowerShell と Azure PowerShell を使用して Azure Active Directory Domain Services を構成して有効化する方法について説明します。
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 03/10/2021
ms.author: justinha
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8056e95b731b1818e10d7415cb813d6aba0ec7fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103149069"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>PowerShell を使用した Azure Active Directory Domain Services の有効化

Azure Active Directory Domain Services (Azure AD DS) では、Windows Server Active Directory と完全に互換性のあるマネージド ドメイン サービス (ドメイン参加、グループ ポリシー、LDAP、Kerberos 認証、NTLM 認証など) が提供されます。 ドメイン コントローラーのデプロイ、管理、パッチの適用を自分で行わなくても、これらのドメイン サービスを使用することができます。 Azure AD DS は、既存の Azure AD テナントと統合されます。 この統合により、ユーザーは、各自の会社の資格情報を使用してサインインすることができます。また管理者は、既存のグループとユーザー アカウントを使用してリソースへのアクセスをセキュリティで保護することができます。

この記事では、PowerShell を使用して Azure AD DS を有効にする方法について説明します。

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下のリソースが必要です。

* Azure PowerShell のインストールおよび構成。
    * 必要であれば、手順に従って、[Azure PowerShell モジュールをインストールし、Azure サブスクリプションに接続](/powershell/azure/install-az-ps)します。
    * 必ず [Connect-AzAccount][Connect-AzAccount] コマンドレットを使用して Azure サブスクリプションにサインインしてください。
* Azure AD PowerShell をインストールして構成します。
    * 必要であれば、手順に従って、[Azure AD PowerShell モジュールをインストールして Azure AD に接続](/powershell/azure/active-directory/install-adv2)します。
    * 必ず [Connect-AzureAD][Connect-AzureAD] コマンドレットを使用して Azure AD テナントにサインインしてください。
* Azure AD DS を有効にするには、Azure AD テナントに "*全体管理者*" 特権が必要です。
* 必要な Azure AD DS リソースを作成するためには、ご利用の Azure サブスクリプションに "*共同作成者*" 特権が必要です。

  > [!IMPORTANT]
  > **Az.ADDomainServices** PowerShell モジュールがプレビュー段階にある間は、`Install-Module` コマンドレットを使用して、これを個別にインストールする必要があります。

  ```azurepowershell-interactive
  Install-Module -Name Az.ADDomainServices
  ```

## <a name="create-required-azure-ad-resources"></a>必要な Azure AD リソースを作成する

Azure AD DS を使用するには、認証と通信を行うためのサービス プリンシパルと、マネージド ドメインで管理アクセス許可を持つユーザーを定義するための Azure AD グループが必要です。

まず、*Domain Controller Services* という名前の特定のアプリケーション ID を使用して、Azure AD サービス プリンシパルを作成します。 パブリック Azure では、この ID 値は *2565bd9d-da50-47d4-8b85-4c97f669dc36* です。 他のクラウドでは、この値は *6ba9a5d4-8456-4118-b521-9c5ca10cdf84* となります。 このアプリケーション ID は変更しないでください。

[New-AzureADServicePrincipal][New-AzureADServicePrincipal] コマンドレットを使用して Azure AD サービス プリンシパルを作成します。

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

次に、*AAD DC Administrators* という名前の Azure AD グループを作成します。 このグループに追加されたユーザーには、マネージド ドメインで管理タスクを実行するためのアクセス許可が付与されます。

まず、[Get-AzureADGroup][Get-AzureADGroup] コマンドレットを使用して *AAD DC Administrators* グループ オブジェクト ID を取得します。 *AAD DC Administrators* グループが存在しない場合は、[New-AzureADGroup][New-AzureADGroup] コマンドレットを使用して作成します。

```powershell
# First, retrieve the object ID of the 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# If the group doesn't exist, create it
if (!$GroupObjectId) {
  $GroupObjectId = New-AzureADGroup -DisplayName "AAD DC Administrators" `
    -Description "Delegated group to administer Azure AD Domain Services" `
    -SecurityEnabled $true `
    -MailEnabled $false `
    -MailNickName "AADDCAdministrators"
  }
else {
  Write-Output "Admin group already exists."
}
```

*AAD DC Administrators* グループが作成されたら、[Get-AzureADUser][Get-AzureADUser] コマンドレットを使用して、目的のユーザーのオブジェクト ID を取得し、[Add-AzureADGroupMember][Add-AzureADGroupMember] コマンドレットを使用して、そのユーザーをグループに追加します。

次の例では、UPN が `admin@contoso.onmicrosoft.com` のアカウントのユーザー オブジェクト ID です。 このユーザー アカウントを、*AAD DC Administrators* グループに追加するユーザーの UPN に置き換えます。

```powershell
# Retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-network-resources"></a>ネットワーク リソースを作成する

まず、[Register-AzResourceProvider][Register-AzResourceProvider] コマンドレットを使用して、Azure AD Domain Services リソース プロバイダーを登録します。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

次に、[New-AzResourceGroup][New-AzResourceGroup] コマンドレットを使用してリソース グループを作成します。 次の例では、リソース グループは *myResourceGroup* という名前が付けられ、*westus* リージョンに作成されます。 独自の名前と希望するリージョンを使用します。

```azurepowershell-interactive
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Azure AD Domain Services の仮想ネットワークとサブネットを作成します。 *DomainServices* 用と *Workloads* 用の 2 つのサブネットが作成されます。 Azure AD DS は、専用の *DomainServices* サブネットにデプロイされます。 このサブネットには、他のアプリケーションやワークロードをデプロイしないでください。 残りの VM には、別個の *Workloads* または他のサブネットを使用します。

[New-AzVirtualNetworkSubnetConfig][New-AzVirtualNetworkSubnetConfig] コマンドレットを使用してサブネットを作成し、次に [New-AzVirtualNetwork][New-AzVirtualNetwork] コマンドレットを使用して仮想ネットワークを作成します。

```azurepowershell-interactive
$VnetName = "myVnet"

# Create the dedicated subnet for Azure AD Domain Services.
$SubnetName = "DomainServices"
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -AddressPrefix 10.0.0.0/24

# Create an additional subnet for your own VM workloads
$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet= New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```

### <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

Azure AD DS には、マネージド ドメインに必要なポートをセキュリティで保護し、その他すべての受信トラフィックをブロックするネットワーク セキュリティ グループが必要です。 [ネットワーク セキュリティ グループ (NSG)][nsg-overview] には、Azure 仮想ネットワーク内のトラフィックへのネットワーク トラフィックを許可または拒否するルールの一覧が含まれています。 Azure AD DS では、ネットワーク セキュリティ グループは、マネージド ドメインへのアクセスをロックダウンするための追加の保護レイヤーとして機能します。 必要なポートを確認するには、「[ネットワーク セキュリティ グループと必要なポート][network-ports]」を参照してください。

次の PowerShell コマンドレットでは、[New-AzNetworkSecurityRuleConfig][New-AzNetworkSecurityRuleConfig] を使用してルールを作成し、[New-AzNetworkSecurityGroup][New-AzNetworkSecurityGroup] を使用してネットワーク セキュリティ グループを作成します。 ネットワーク セキュリティ グループとルールは、[Set-AzVirtualNetworkSubnetConfig][Set-AzVirtualNetworkSubnetConfig] コマンドレットを使用して仮想ネットワーク サブネットに関連付けられます。

```azurepowershell-interactive
$NSGName = "aaddsNSG"

# Create a rule to allow inbound TCP port 3389 traffic from Microsoft secure access workstations for troubleshooting
$nsg201 = New-AzNetworkSecurityRuleConfig -Name AllowRD `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 201 `
    -SourceAddressPrefix CorpNetSaw `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389

# Create a rule to allow TCP port 5986 traffic for PowerShell remote management
$nsg301 = New-AzNetworkSecurityRuleConfig -Name AllowPSRemoting `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 301 `
    -SourceAddressPrefix AzureActiveDirectoryDomainServices `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 5986

# Create the network security group and rules
$nsg = New-AzNetworkSecurityGroup -Name $NSGName `
    -ResourceGroupName $ResourceGroupName `
    -Location $AzureLocation `
    -SecurityRules $nsg201,$nsg301

# Get the existing virtual network resource objects and information
$vnet = Get-AzVirtualNetwork -Name $VnetName -ResourceGroupName $ResourceGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $SubnetName
$addressPrefix = $subnet.AddressPrefix

# Associate the network security group with the virtual network subnet
Set-AzVirtualNetworkSubnetConfig -Name $SubnetName `
    -VirtualNetwork $vnet `
    -AddressPrefix $addressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

## <a name="create-a-managed-domain"></a>マネージド ドメインの作成

次に、マネージド ドメインを作成しましょう。 お使いの Azure サブスクリプション ID を設定し、マネージド ドメインの名前 (*aaddscontoso.com* など) を指定します。 お使いのサブスクリプション ID は、[Get-AzSubscription][Get-AzSubscription] コマンドレットを使用して取得できます。

Availability Zones がサポートされているリージョンを選択すると、Azure AD DS リソースが、冗長性強化のために複数のゾーンに分散されます。

Availability Zones は、Azure リージョン内の一意の物理的な場所です。 それぞれのゾーンは、独立した電源、冷却手段、ネットワークを備えた 1 つまたは複数のデータセンターで構成されています。 回復性を確保するため、有効になっているリージョンにはいずれも最低 3 つのゾーンが別個に存在しています。

Azure AD DS を複数のゾーンに分散するために、ご自身で構成するものは何もありません。 Azure プラットフォームでは、ゾーンへのリソース分散が自動的に処理されます。 詳細情報および利用可能なリージョンについては、「[Azure の Availability Zones の概要][availability-zones]」を参照してください。

```azurepowershell-interactive
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Enable Azure AD Domain Services for the directory.
$replicaSetParams = @{
  Location = $AzureLocation
  SubnetId = "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"
}
$replicaSet = New-AzADDomainServiceReplicaSetObject @replicaSetParams

$domainServiceParams = @{
  Name = $ManagedDomainName
  ResourceGroupName = $ResourceGroupName
  DomainName = $ManagedDomainName
  ReplicaSet = $replicaSet
}
New-AzADDomainService @domainServiceParams
```

リソースが作成され、PowerShell プロンプトに制御が戻るまで数分かかります。 マネージド ドメインは引き続きバックグラウンドでプロビジョニングされ、デプロイが完了するまでには最大 1 時間かかる場合があります。 Azure portal では、お使いのマネージド ドメインの **[概要]** ページに、このデプロイ ステージ全体の現在の状態が表示されます。

マネージド ドメインがプロビジョニングを完了したことが Azure portal に示されたら、次のタスクを完了する必要があります。

* 仮想マシンがマネージド ドメインを検出してドメイン参加または認証を行うことができるように、仮想ネットワークの DNS 設定を更新します。
    * DNS を構成するには、ポータルでマネージド ドメインを選択します。 **[概要]** ウィンドウで、これらの DNS 設定を自動的に構成するように求められます。
* [Azure AD DS とのパスワード同期を有効にして](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)、エンド ユーザーが会社の資格情報を使用してマネージド ドメインにサインインできるようにします。

## <a name="complete-powershell-script"></a>完全な PowerShell スクリプト

次の完全な PowerShell スクリプトは、この記事に示されているすべてのタスクを結合します。 スクリプトをコピーし、`.ps1` 拡張子付きのファイルに保存します。 ローカルの PowerShell コンソール、または [Azure Cloud Shell][cloud-shell] でスクリプトを実行します。

> [!NOTE]
> Azure AD DS を有効にするには、Azure AD テナントのグローバル管理者である必要があります。 また、Azure サブスクリプションで、少なくとも *共同作成者* 権限が必要です。

```azurepowershell-interactive
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
$ResourceGroupName = "myResourceGroup"
$VnetName = "myVnet"
$AzureLocation = "westus"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"

# First, retrieve the object ID of the 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Create the delegated administration group for Azure AD Domain Services if it doesn't already exist.
if (!$GroupObjectId) {
  $GroupObjectId = New-AzureADGroup -DisplayName "AAD DC Administrators" `
    -Description "Delegated group to administer Azure AD Domain Services" `
    -SecurityEnabled $true `
    -MailEnabled $false `
    -MailNickName "AADDCAdministrators"
  }
else {
  Write-Output "Admin group already exists."
}

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq '$AaddsAdminUserUpn'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId

# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$SubnetName = "DomainServices"
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

$NSGName = "aaddsNSG"

# Create a rule to allow inbound TCP port 3389 traffic from Microsoft secure access workstations for troubleshooting
$nsg201 = New-AzNetworkSecurityRuleConfig -Name AllowRD `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 201 `
    -SourceAddressPrefix CorpNetSaw `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389

# Create a rule to allow TCP port 5986 traffic for PowerShell remote management
$nsg301 = New-AzNetworkSecurityRuleConfig -Name AllowPSRemoting `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 301 `
    -SourceAddressPrefix AzureActiveDirectoryDomainServices `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 5986

# Create the network security group and rules
$nsg = New-AzNetworkSecurityGroup -Name $NSGName `
    -ResourceGroupName $ResourceGroupName `
    -Location $AzureLocation `
    -SecurityRules $nsg201,$nsg301

# Get the existing virtual network resource objects and information
$vnet = Get-AzVirtualNetwork -Name $VnetName -ResourceGroupName $ResourceGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $SubnetName
$addressPrefix = $subnet.AddressPrefix

# Associate the network security group with the virtual network subnet
Set-AzVirtualNetworkSubnetConfig -Name $SubnetName `
    -VirtualNetwork $vnet `
    -AddressPrefix $addressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork

# Enable Azure AD Domain Services for the directory.
$replicaSetParams = @{
  Location = $AzureLocation
  SubnetId = "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"
}
$replicaSet = New-AzADDomainServiceReplicaSetObject @replicaSetParams

$domainServiceParams = @{
  Name = $ManagedDomainName
  ResourceGroupName = $ResourceGroupName
  DomainName = $ManagedDomainName
  ReplicaSet = $replicaSet
}
New-AzADDomainService @domainServiceParams
```

リソースが作成され、PowerShell プロンプトに制御が戻るまで数分かかります。 マネージド ドメインは引き続きバックグラウンドでプロビジョニングされ、デプロイが完了するまでには最大 1 時間かかる場合があります。 Azure portal では、お使いのマネージド ドメインの **[概要]** ページに、このデプロイ ステージ全体の現在の状態が表示されます。

マネージド ドメインがプロビジョニングを完了したことが Azure portal に示されたら、次のタスクを完了する必要があります。

* 仮想マシンがマネージド ドメインを検出してドメイン参加または認証を行うことができるように、仮想ネットワークの DNS 設定を更新します。
    * DNS を構成するには、ポータルでマネージド ドメインを選択します。 **[概要]** ウィンドウで、これらの DNS 設定を自動的に構成するように求められます。
* [Azure AD DS とのパスワード同期を有効にして](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)、エンド ユーザーが会社の資格情報を使用してマネージド ドメインにサインインできるようにします。

## <a name="next-steps"></a>次のステップ

動作中のマネージド ドメインを確認するために、[Windows VM のドメイン参加][windows-join]、[Secure LDAP の構成][tutorial-ldaps]、[パスワード ハッシュ同期の構成][tutorial-phs]を実行できます。

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md
[nsg-overview]: ../virtual-network/network-security-groups-overview.md
[network-ports]: network-considerations.md#network-security-groups-and-required-ports

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[New-AzVirtualNetworkSubnetConfig]: /powershell/module/Az.Network/New-AzVirtualNetworkSubnetConfig
[New-AzVirtualNetwork]: /powershell/module/Az.Network/New-AzVirtualNetwork
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: ../cloud-shell/cloud-shell-windows-users.md
[availability-zones]: ../availability-zones/az-overview.md
[New-AzNetworkSecurityRuleConfig]: /powershell/module/az.network/new-aznetworksecurityruleconfig
[New-AzNetworkSecurityGroup]: /powershell/module/az.network/new-aznetworksecuritygroup
[Set-AzVirtualNetworkSubnetConfig]: /powershell/module/az.network/set-azvirtualnetworksubnetconfig

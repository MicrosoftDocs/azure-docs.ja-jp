---
title: PowerShell を使用した Azure Active Directory Domain Services の有効化 | Microsoft Docs
description: PowerShell を使用した Azure Active Directory Domain Services の有効化
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: iainfou
ms.openlocfilehash: daddb2d13aee08fe7294ab2d7f0892bab761562b
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67472682"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>PowerShell を使用した Azure Active Directory Domain Services の有効化
この記事では、PowerShell を使用して Azure Active Directory (AD) Domain Services を有効にする方法について説明します。

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="task-1-install-the-required-powershell-modules"></a>タスク 1:必要な PowerShell モジュールをインストールする

### <a name="install-and-configure-azure-ad-powershell"></a>Azure AD PowerShell のインストールおよび構成
記事の指示に従って、 [Azure AD PowerShell モジュールをインストールして Azure AD に接続します](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)。

### <a name="install-and-configure-azure-powershell"></a>Azure PowerShell のインストールおよび構成
記事の指示に従って、 [Azure PowerShell モジュールをインストールして Azure サブスクリプションに接続します](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)。


## <a name="task-2-create-the-required-service-principal-in-your-azure-ad-directory"></a>タスク 2:Azure AD ディレクトリに必要なサービス プリンシパルを作成する
次の PowerShell コマンドを入力して Azure AD Domain Services に必要なサービス プリンシパルを Azure AD ディレクトリに作成します。
```powershell
# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

## <a name="task-3-create-and-configure-the-aad-dc-administrators-group"></a>タスク 3:"AAD DC 管理者" グループを作成して構成する
次のタスクでは、マネージド ドメインでの管理タスクを委任するために使用する管理者グループを作成します。
```powershell
# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

グループが作成されたため、数人のユーザーをグループに追加します。
```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso100.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="task-4-register-the-azure-ad-domain-services-resource-provider"></a>タスク 4:Azure AD Domain Services のリソース プロバイダーを登録する
次の PowerShell コマンドを入力して Azure AD Domain Services のリソース プロバイダーを登録します。
```powershell
# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

## <a name="task-5-create-a-resource-group"></a>タスク 5:リソース グループの作成
次の PowerShell コマンドを入力して、リソース グループを作成します。
```powershell
$ResourceGroupName = "ContosoAaddsRg"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

仮想ネットワークおよび Azure AD Domain Services のマネージド ドメインをこのリソース グループに作成できます。


## <a name="task-6-create-and-configure-the-virtual-network"></a>タスク 6:仮想ネットワークを作成して構成する
ここで、Azure AD Domain Services を有効にする仮想ネットワークを作成します。 Azure AD Domain Services の専用サブネットを作成していることを確認します。 この専用サブネットにはワークロード VM をデプロイしないでください。

次の PowerShell コマンドを入力して、Azure AD Domain Services の専用サブネットを備えた仮想ネットワークを作成します。

```powershell
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```


## <a name="task-7-provision-the-azure-ad-domain-services-managed-domain"></a>タスク 7:Azure AD Domain Services のマネージド ドメインをプロビジョニングする
次の PowerShell コマンドを入力して、ディレクトリに対して Azure AD Domain Services を有効にします。

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **マネージド ドメインをプロビジョニングした後、必ず追加の構成手順を実行してください。**
> マネージド ドメインがプロビジョニングされた後、次のタスクを実行する必要があります。
> * 仮想マシンがマネージド ドメインを検出してドメイン参加または認証を行うことができるように、仮想ネットワークの **[DNS 設定を更新](active-directory-ds-getting-started-dns.md)** します。
> * **[Azure AD Domain Services とのパスワード同期を有効](active-directory-ds-getting-started-password-sync.md)** にして、エンド ユーザーが会社の資格情報を使用してマネージド ドメインにサインインできるようにします。


## <a name="powershell-script"></a>PowerShell スクリプト
この記事に記載されたすべてのタスクを実行するために使用する PowerShell スクリプトを以下に示します。 スクリプトをコピーし、'.ps1' 拡張子付きのファイルに保存します。 PowerShell または PowerShell Integrated Scripting Environment (ISE) を使用して、スクリプトを実行します。

> [!NOTE]
> **このスクリプトを実行するために必要な権限**。Azure AD Domain Services を有効にするには、Azure AD ディレクトリのグローバル管理者である必要があります。 また、少なくとも Azure AD Domain Services を有効にする仮想ネットワークの「共同作成者」権限が必要です。
>

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso100.onmicrosoft.com"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"

# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"

# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

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

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **マネージド ドメインをプロビジョニングした後、必ず追加の構成手順を実行してください。**
> マネージド ドメインがプロビジョニングされた後、次のタスクを実行する必要があります。
> * 仮想マシンがマネージド ドメインを検出してドメイン参加または認証を行うことができるように、仮想ネットワークの DNS 設定を更新します。
> * Azure AD Domain Services とのパスワード同期を有効にして、エンド ユーザーが会社の資格情報を使用してマネージド ドメインにサインインできるようにします。

## <a name="next-steps"></a>次の手順
マネージド ドメインを作成した後は、マネージド ドメインを使用できるように、次の構成タスクを実行します。

* [仮想ネットワークの DNS サーバー設定をマネージド ドメインを指定するように更新する](active-directory-ds-getting-started-dns.md)
* [マネージド ドメインとのパスワード同期を有効にする](active-directory-ds-getting-started-password-sync.md)

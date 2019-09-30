---
title: PowerShell を使用した Azure DS Domain Services の有効化 | Microsoft Docs
description: Azure AD PowerShell と Azure PowerShell を使用して Azure Active Directory Domain Services を構成して有効化する方法について説明します。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: iainfou
ms.openlocfilehash: 163259af3797b652c9605c171447f4a7d2576c87
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842717"
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

## <a name="create-required-azure-ad-resources"></a>必要な Azure AD リソースを作成する

Azure AD DS には、サービス プリンシパルと Azure AD グループが必要です。 これらのリソースにより、Azure AD DS マネージド ドメインはデータを同期し、マネージド ドメインで管理アクセス許可を持つユーザーを定義できます。

まず、Azure AD DS が通信と自身の認証を行うための Azure AD サービス プリンシパルを作成します。 ID *2565bd9d-da50-47d4-8b85-4c97f669dc36* を持つ *Domain Controller Services* という名前の特定のアプリケーション ID が使用されます。 このアプリケーション ID は変更しないでください。

[New-AzureADServicePrincipal][New-AzureADServicePrincipal] コマンドレットを使用して Azure AD サービス プリンシパルを作成します。

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

次に、*AAD DC Administrators* という名前の Azure AD グループを作成します。 このグループに追加されたユーザーには、Azure AD DS マネージド ドメインで管理タスクを実行するためのアクセス許可が付与されます。

[New-AzureADGroup][New-AzureADGroup] コマンドレットを使用して *AAD DC Administrators* グループを作成します。

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

*AAD DC Administrators* グループが作成されたら、[Add-AzureADGroupMember][Add-AzureADGroupMember] コマンドレットを使用してグループにユーザーを追加します。 まず、[Get-AzureADGroup][Get-AzureADGroup] コマンドレットを使用して *AAD DC Administrators* グループのオブジェクト ID を取得し、次に [Get-AzureADUser][Get-AzureADUser] コマンドレットを使用して目的のユーザーのオブジェクト ID を取得します。

次の例では、UPN が `admin@contoso.onmicrosoft.com` のアカウントのユーザー オブジェクト ID です。 このユーザー アカウントを、*AAD DC Administrators* グループに追加するユーザーの UPN に置き換えます。

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-supporting-azure-resources"></a>サポートする Azure リソースの作成

まず、[Register-AzResourceProvider][Register-AzResourceProvider] コマンドレットを使用して、Azure AD Domain Services リソース プロバイダーを登録します。

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

次に、[New-AzResourceGroup][New-AzResourceGroup] コマンドレットを使用してリソース グループを作成します。 次の例では、リソース グループは *myResourceGroup* という名前が付けられ、*westus* リージョンに作成されます。 独自の名前と希望するリージョンを使用します。

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Azure AD Domain Services の仮想ネットワークとサブネットを作成します。 *DomainServices* 用と *Workloads* 用の 2 つのサブネットが作成されます。 Azure AD DS は、専用の *DomainServices* サブネットにデプロイされます。 このサブネットには、他のアプリケーションやワークロードをデプロイしないでください。 残りの VM には、別個の *Workloads* または他のサブネットを使用します。

[New-AzVirtualNetworkSubnetConfig][New-AzVirtualNetworkSubnetConfig] コマンドレットを使用してサブネットを作成し、次に [New-AzVirtualNetwork][New-AzVirtualNetwork] コマンドレットを使用して仮想ネットワークを作成します。

```powershell
$VnetName = "myVnet"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

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

## <a name="create-an-azure-ad-ds-managed-domain"></a>Azure AD DS マネージド ドメインを作成する

次に、Azure AD DS マネージド ドメインを作成しましょう。 Azure サブスクリプション ID を設定し、次にマネージド ドメインの名前 (*contoso.com* など) を指定します。 サブスクリプション ID は、[Get-AzSubscription][Get-AzSubscription] コマンドレットを使用して取得できます。

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso.com"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

リソースが作成され、PowerShell プロンプトに制御が戻るまで数分かかります。 Azure AD DS のマネージド ドメインは引き続きバックグラウンドでプロビジョニングされ、デプロイが完了するまでには最大 1 時間かかることがあります。 Azure portal で、Azure AD DS マネージド ドメインの **[概要]** ページには、このデプロイ ステージ全体の現在の状態が表示されます。

Azure AD DS マネージド ドメインがプロビジョニングを完了したことが Azure portal に示されたら、次のタスクを完了する必要があります。

* 仮想マシンがマネージド ドメインを検出してドメイン参加または認証を行うことができるように、仮想ネットワークの DNS 設定を更新します。
    * DNS を構成するには、ポータルで Azure AD DS のマネージド ドメインを選択します。 **[概要]** ウィンドウで、これらの DNS 設定を自動的に構成するように求められます。
* [Azure AD Domain Services とのパスワード同期を有効](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)にして、エンド ユーザーが会社の資格情報を使用してマネージド ドメインにサインインできるようにします。

## <a name="complete-powershell-script"></a>完全な PowerShell スクリプト

次の完全な PowerShell スクリプトは、この記事に示されているすべてのタスクを結合します。 スクリプトをコピーし、`.ps1` 拡張子付きのファイルに保存します。 ローカルの PowerShell コンソール、または [Azure Cloud Shell][cloud-shell] でスクリプトを実行します。

> [!NOTE]
> Azure AD DS を有効にするには、Azure AD テナントのグローバル管理者である必要があります。 また、Azure サブスクリプションで、少なくとも*共同作成者*権限が必要です。

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
$ResourceGroupName = "myResourceGroup"
$VnetName = "myVnet"
$AzureLocation = "westus"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso.com"

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
  -Force -Verbose
```

リソースが作成され、PowerShell プロンプトに制御が戻るまで数分かかります。 Azure AD DS のマネージド ドメインは引き続きバックグラウンドでプロビジョニングされ、デプロイが完了するまでには最大 1 時間かかることがあります。 Azure portal で、Azure AD DS マネージド ドメインの **[概要]** ページには、このデプロイ ステージ全体の現在の状態が表示されます。

Azure AD DS マネージド ドメインがプロビジョニングを完了したことが Azure portal に示されたら、次のタスクを完了する必要があります。

* 仮想マシンがマネージド ドメインを検出してドメイン参加または認証を行うことができるように、仮想ネットワークの DNS 設定を更新します。
    * DNS を構成するには、ポータルで Azure AD DS のマネージド ドメインを選択します。 **[概要]** ウィンドウで、これらの DNS 設定を自動的に構成するように求められます。
* [Azure AD Domain Services とのパスワード同期を有効](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)にして、エンド ユーザーが会社の資格情報を使用してマネージド ドメインにサインインできるようにします。

## <a name="next-steps"></a>次の手順

動作中の Azure AD DS マネージド ドメインを確認するために、[Windows VM のドメイン参加][windows-join]、[Secure LDAP の構成][tutorial-ldaps]、および[パスワード ハッシュ同期の構成][tutorial-phs]を実行できます。

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md

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
[cloud-shell]: /azure/cloud-shell/cloud-shell-windows-users

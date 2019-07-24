---
title: Windows VM のユーザー割り当てマネージド ID を使用して Azure Resource Manager にアクセスする
description: Windows VM 上でユーザー割り当てマネージド ID を使用して Azure Resource Manager にアクセスするプロセスについて説明するチュートリアル。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6fc7e13cf5ea3991f81d53edcabe0980d799cb0
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59520867"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-windows-vm-to-access-azure-resource-manager"></a>チュートリアル:Windows VM 上でユーザー割り当てマネージド ID を使用して Azure Resource Manager にアクセスする

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

このチュートリアルでは、ユーザー割り当て ID を作成して Windows 仮想マシン (VM) に割り当て、その ID を使用して Azure Resource Manager API にアクセスする方法について説明します。 管理対象サービス ID は Azure によって自動的に管理されます。 管理対象サービス ID を使用すると、コード内に資格情報を埋め込む必要なく、Azure AD の認証をサポートするサービスに認証することができます。 

学習内容は次のとおりです。

> [!div class="checklist"]
> * ユーザー割り当てマネージド ID を作成する
> * ユーザー割り当て ID を Windows VM に割り当てる
> * Azure Resource Manager でユーザー割り当て ID にリソース グループへのアクセスを許可する 
> * ユーザー割り当て ID を使用してアクセス トークンを取得し、そのアクセス トークンを使用して Azure Resource Manager を呼び出す 
> * リソース グループのプロパティを読み取る

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

- [Azure portal にサインインする](https://portal.azure.com)

- [Windows 仮想マシンを作成する](/azure/virtual-machines/windows/quick-create-portal)

- このチュートリアルの必要なリソース作成およびロール管理のステップを実行するために、お使いのアカウントには、適切な範囲 (サブスクリプションまたはリソース グループ) を対象とする "所有者" アクセス許可が必要となります。 ロールの割り当てに関するサポートが必要な場合は、「[ロールベースのアクセス制御を使用して Azure サブスクリプション リソースへのアクセスを管理する](/azure/role-based-access-control/role-assignments-portal)」を参照してください。
- [Azure PowerShell モジュールの最新バージョンをインストール](/powershell/azure/install-az-ps)します。 
- `Connect-AzAccount` を実行して、Azure との接続を作成します。
- [PowerShellGet の最新バージョン](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget)をインストールします。
- `Install-Module -Name PowerShellGet -AllowPrerelease` を実行して `PowerShellGet` モジュールのプレリリース バージョンを取得します (`Az.ManagedServiceIdentity` モジュールをインストールするには、このコマンドを実行した後に現在の PowerShell セッションから `Exit` することが必要になる場合があります)。
- `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` を実行して `Az.ManagedServiceIdentity` モジュールのプレリリース バージョンをインストールして、この記事のユーザー割り当て ID 操作を実行します。

## <a name="create-a-user-assigned-identity"></a>ユーザー割り当て ID を作成する

ユーザー割り当て ID は、スタンドアロン Azure リソースとして作成されます。 [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/get-azuserassignedidentity) を使用して、Azure 内の Azure AD テナントで、1 つ以上の Azure サービス インスタンスに割り当てることができるユーザー ID が作成されます。

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

応答には、次の例のように、作成されたユーザー割り当て ID の詳細が含まれています。 後続の手順で使用するため、ユーザー割り当て ID の `Id` と `ClientId` の値をメモしておきます。

```azurepowershell
{
Id: /subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1
ResourceGroupName : myResourceGroupVM
Name: ID1
Location: westus
TenantId: 733a8f0e-ec41-4e69-8ad8-971fc4b533f8
PrincipalId: e591178e-b785-43c8-95d2-1397559b2fb9
ClientId: af825a31-b0e0-471f-baea-96de555632f9
ClientSecretUrl: https://control-westus.identity.azure.net/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1/credentials?tid=733a8f0e-ec41-4e69-8ad8-971fc4b533f8&oid=e591178e-b785-43c8-95d2-1397559b2fb9&aid=af825a31-b0e0-471f-baea-96de555632f9
Type: Microsoft.ManagedIdentity/userAssignedIdentities
}
```

## <a name="assign-the-user-assigned-identity-to-a-windows-vm"></a>ユーザー割り当て ID を Windows VM に割り当てる

ユーザー割り当て ID は、複数の Azure リソース上のクライアントで使用できます。 単一の VM にユーザー割り当て ID を割り当てるには、次のコマンドを使用します。 `-IdentityID` パラメーターには、前の手順で返された `Id` プロパティを使用します。

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-your-user-assigned-identity-access-to-a-resource-group-in-azure-resource-manager"></a>Azure Resource Manager でユーザー割り当て ID にリソース グループへのアクセスを許可する 

Azure AD 認証をサポートするリソース API に認証するアクセス トークンを要求するためにコードで使用する ID が、Azure リソースのマネージド ID により提供されます。 このチュートリアルでは、コードは Azure Resource Manager API にアクセスします。 

コードで API にアクセスできるようにするには、事前に ID に Azure Resource Manager のリソースへのアクセスを許可する必要があります。 このケースでは、VM が含まれているリソース グループが対象になります。 使用する環境に合わせて、`<SUBSCRIPTION ID>` の値を更新します。

```azurepowershell-interactive
$spID = (Get-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

応答には、次の例のように、作成されたロールの割り当ての詳細が含まれています。

```azurepowershell
RoleAssignmentId: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM/providers/Microsoft.Authorization/roleAssignments/f9cc753d-265e-4434-ae19-0c3e2ead62ac
Scope: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM
DisplayName: ID1
SignInName:
RoleDefinitionName: Reader
RoleDefinitionId: acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId: e591178e-b785-43c8-95d2-1397559b2fb9
ObjectType: ServicePrincipal
CanDelegate: False
```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>VM の ID を使用してアクセス トークンを取得し、このトークンを使用して Resource Manager を呼び出す 

チュートリアルの残りの部分では、以前に作成した VM から作業を行います。

1. Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

2. ポータルで **[Virtual Machines]** にナビゲートして Windows 仮想マシンに移動し、**[概要]** の **[接続]** をクリックします。

3. Windows VM を作成したときに使用した**ユーザー名**と**パスワード**を入力します。

4. これで、仮想マシンを使用する**リモート デスクトップ接続**が作成されました。リモート セッションで **PowerShell** を開きます。

5. PowerShell の `Invoke-WebRequest` を使用して、Azure リソース エンドポイントのローカル マネージド ID に、Azure Resource Manager のアクセス トークンを取得するよう要求します。  `client_id` 値は、[ユーザー割り当てマネージド ID を作成](#create-a-user-assigned-identity)したときに返された値です。

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=af825a31-b0e0-471f-baea-96de555632f9&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

## <a name="read-the-properties-of-a-resource-group"></a>リソース グループのプロパティを読み取る

前の手順で取得したアクセス トークンを使用して Azure Resource Manager にアクセスし、ユーザー割り当て ID にアクセスを許可したリソース グループのプロパティを読み取ります。 `<SUBSCRIPTION ID>` は使用している環境内のサブスクリプション ID に置き換えます。

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
応答には、次の例のように、特定のリソース グループの情報が含まれています。

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/myResourceGroupVM","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Resource Manager API にアクセスするために、ユーザー割り当て ID を作成して、それを Azure 仮想マシンに添付する方法について学習しました。  Azure Resource Manager の詳細については、以下を参照してください。

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)

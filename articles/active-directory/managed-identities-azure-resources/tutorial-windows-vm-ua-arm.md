---
title: チュートリアル`:` マネージド ID を使用して Azure Resource Manager にアクセスする - Windows - Azure AD
description: Windows VM 上でユーザー割り当てマネージド ID を使用して Azure Resource Manager にアクセスするプロセスについて説明するチュートリアル。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43f926f400bf8ddd522bbb5f62509519fa09903d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101096599"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-windows-vm-to-access-azure-resource-manager"></a>チュートリアル:Windows VM 上でユーザー割り当てマネージド ID を使用して Azure Resource Manager にアクセスする

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

- [Windows 仮想マシンを作成する](../../virtual-machines/windows/quick-create-portal.md)

- このチュートリアルの必要なリソース作成およびロール管理のステップを実行するために、お使いのアカウントには、適切な範囲 (サブスクリプションまたはリソース グループ) を対象とする "所有者" アクセス許可が必要となります。 ロールの割り当てに関するサポートが必要な場合は、[Azure ロールの割り当てによる Azure サブスクリプション リソースへのアクセスの管理](../../role-based-access-control/role-assignments-portal.md)に関するページをご覧ください。

- サンプル スクリプトを実行するには、次の 2 つのオプションがあります。
    - [Azure Cloud Shell](../../cloud-shell/overview.md) を使用します。これは、コード ブロックの右上隅にある **[使ってみる]** ボタンを使用して開くことができます。
    - Azure PowerShell を使用して、スクリプトをローカルで実行します。次のセクションの説明を参照してください。

### <a name="configure-azure-powershell-locally"></a>ローカルで Azure PowerShell を構成する

この記事で、(Cloud Shell ではなく) ローカルで Azure PowerShell を使用するには、次の手順を実行します。

1. [最新バージョンの Azure PowerShell](/powershell/azure/install-az-ps) をインストールします (まだインストールしていない場合)。

1. Azure にサインインします。

    ```azurepowershell
    Connect-AzAccount
    ```

1. [PowerShellGet の最新バージョン](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget)をインストールします。

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    次の手順に備えて、このコマンドを実行した後に現在の PowerShell セッションから `Exit` する必要がある場合があります。

1. `Az.ManagedServiceIdentity` モジュールのプレリリース バージョンをインストールして、この記事のユーザー割り当てマネージド ID 操作を実行します。

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="enable"></a>有効化

ユーザー割り当て ID に基づくシナリオの場合は、次の手順を実行する必要があります。

- ID の作成
- 新たに作成された ID の割り当て

### <a name="create-identity"></a>ID の作成

このセクションでは、ユーザー割り当て ID を作成する方法を説明します。 ユーザー割り当て ID は、スタンドアロン Azure リソースとして作成されます。 [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/get-azuserassignedidentity) を使用して、Azure 内の Azure AD テナントで、1 つ以上の Azure サービス インスタンスに割り当てることができるユーザー ID が作成されます。

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

### <a name="assign-identity"></a>ID の割り当て

このセクションでは、ユーザー割り当て ID を Windows VM に割り当てる方法を説明します。 ユーザー割り当て ID は、複数の Azure リソース上のクライアントで使用できます。 単一の VM にユーザー割り当て ID を割り当てるには、次のコマンドを使用します。 `-IdentityID` パラメーターには、前の手順で返された `Id` プロパティを使用します。

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-access"></a>アクセス権の付与 

このセクションでは、Azure Resource Manager のリソース グループへのアクセスをユーザー割り当て ID に許可する方法を説明します。 Azure AD 認証をサポートするリソース API に認証するアクセス トークンを要求するためにコードで使用する ID が、Azure リソースのマネージド ID により提供されます。 このチュートリアルでは、コードは Azure Resource Manager API にアクセスします。 

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

## <a name="access-data"></a>データにアクセスする

### <a name="get-an-access-token"></a>アクセス トークンを取得する 

チュートリアルの残りの部分では、以前に作成した VM から作業を行います。

1. Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

2. ポータルで **[Virtual Machines]** にナビゲートして Windows 仮想マシンに移動し、**[概要]** の **[接続]** をクリックします。

3. Windows VM を作成したときに使用した **ユーザー名** と **パスワード** を入力します。

4. これで、仮想マシンを使用する **リモート デスクトップ接続** が作成されました。リモート セッションで **PowerShell** を開きます。

5. PowerShell の `Invoke-WebRequest` を使用して、Azure リソース エンドポイントのローカル マネージド ID に、Azure Resource Manager のアクセス トークンを取得するよう要求します。  `client_id` 値は、ユーザー割り当てマネージド ID を作成したときに返された値です。

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=af825a31-b0e0-471f-baea-96de555632f9&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

### <a name="read-properties"></a>プロパティの読み取り

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
>[Azure Resource Manager](../../azure-resource-manager/management/overview.md)

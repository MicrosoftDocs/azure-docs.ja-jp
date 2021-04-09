---
title: PowerShell を使用してマネージド ID をアプリケーション ロールに割り当てる - Azure AD
description: PowerShell を使用して、マネージド ID アクセスを別のアプリケーションのロールに割り当てる手順について説明します。
services: active-directory
documentationcenter: ''
author: johndowns
manager: ''
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: jodowns
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2d844054e3744aafa01f43ded354a5b5d9f8b4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732100"
---
# <a name="assign-a-managed-identity-access-to-an-application-role-using-powershell"></a>PowerShell を使用してマネージド ID アクセスをアプリケーション ロールに割り当てる

Azure リソースのマネージド ID により、Azure Active Directory の ID が Azure サービスに提供されます。 それらが動作するためにコード内の資格情報は必要ありません。 この ID は、Azure AD 認証をサポートするサービスへの認証を行うために、Azure サービスによって使用されます。 アプリケーション ロールによってロールベースのアクセス制御の形式が提供され、サービスで承認規則を実装できます。

この記事では、Azure AD PowerShell を使用して、別のアプリケーションによって公開されているアプリケーション ロールにマネージド ID を割り当てる方法について説明します。

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。 **[システム割り当てマネージド ID とユーザー割り当てマネージド ID の違い](overview.md#managed-identity-types)を必ず確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- サンプル スクリプトを実行するには、次の 2 つのオプションがあります。
    - [Azure Cloud Shell](../../cloud-shell/overview.md) を使用します。これは、コード ブロックの右上隅にある **[試してみる]** ボタンを使用して開くことができます。
    - 最新バージョンの [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2) をインストールすることにより、ローカル環境でスクリプトを実行します。

## <a name="assign-a-managed-identity-access-to-another-applications-app-role"></a>マネージド ID アクセスを別のアプリケーションのアプリ ロールに割り当てる

1. [Azure VM などの](qs-configure-powershell-windows-vm.md) Azure リソースでマネージド ID を有効にします。

1. マネージド ID のサービス プリンシパルのオブジェクト ID を調べます。

   **システム割り当てのマネージド ID の場合** は、Azure portal のリソースの **[ID]** ページでオブジェクト ID を確認できます。 次の PowerShell スクリプトを使用して、オブジェクト ID を調べることもできます。 ステップ 1 で作成したリソースのリソース ID が必要になります。これは、Azure portal のリソースの **[プロパティ]** ページでわかります。

    ```powershell
    $resourceIdWithManagedIdentity = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.Compute/virtualMachines/{my virtual machine name}'
    (Get-AzResource -ResourceId $resourceIdWithManagedIdentity).Identity.PrincipalId
    ```

    **ユーザー割り当てのマネージド ID の場合** は、Azure portal のリソースの **[概要]** ページで、マネージド ID のオブジェクト ID を確認できます。 次の PowerShell スクリプトを使用して、オブジェクト ID を調べることもできます。 ユーザー割り当てのマネージド ID のリソース ID が必要になります。

    ```powershell
    $userManagedIdentityResourceId = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{my managed identity name}'
    (Get-AzResource -ResourceId $userManagedIdentityResourceId).Properties.PrincipalId
    ```

1. マネージド ID による要求の送信先であるサービスを表す新しいアプリケーション登録を作成します。 マネージド ID に対してアプリ ロールの付与を公開している API またはサービスのサービス プリンシパルが Azure AD テナントに既にある場合は、このステップをスキップします。 たとえば、マネージド ID アクセスを Microsoft Graph API に付与する場合は、このステップを省略できます。

1. サービス アプリケーションのサービス プリンシパルのオブジェクト ID を調べます。 これは、Azure portal を使用して確認できます。 Azure Active Directory に移動して **[エンタープライズ アプリケーション]** ページを開き、アプリケーションを見つけて、 **[オブジェクト ID]** を調べます。 次の PowerShell スクリプトを使用して、サービス プリンシパルのオブジェクト ID を表示名で検索することもできます。

    ```powershell
    $serverServicePrincipalObjectId = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$applicationName'").ObjectId
    ```

    > [!NOTE]
    > アプリケーションの表示名は一意ではないため、取得したサービス プリンシパルが適切なアプリケーションのものであることを確認する必要があります。

1. ステップ 3 で作成したアプリケーションに[アプリ ロール](../develop/howto-add-app-roles-in-azure-ad-apps.md)を追加します。 ロールは、Azure portal または Microsoft Graph を使用して作成できます。 たとえば、次のようなアプリ ロールを追加できます。

    ```json
    {
        "allowedMemberTypes": [
            "Application"
        ],
        "displayName": "Read data from MyApi",
        "id": "0566419e-bb95-4d9d-a4f8-ed9a0f147fa6",
        "isEnabled": true,
        "description": "Allow the application to read data as itself.",
        "value": "MyApi.Read.All"
    }
    ```

1. アプリ ロールをマネージド ID に割り当てます。 アプリ ロールを割り当てるには、次の情報が必要です。
    * `managedIdentityObjectId`: マネージド ID のサービス プリンシパルのオブジェクト ID。ステップ 2 で確認しました。
    * `serverServicePrincipalObjectId`: サーバー アプリケーションのサービス プリンシパルのオブジェクト ID。ステップ 4 で確認しました。
    * `appRoleId`: サーバー アプリによって公開されるアプリ ロールの ID。ステップ 5 で生成しました。この例では、アプリ ロール ID は `0566419e-bb95-4d9d-a4f8-ed9a0f147fa6` です。
   
   次の PowerShell スクリプトを実行して、ロールの割り当てを追加します。

    ```powershell
    New-AzureADServiceAppRoleAssignment -ObjectId $managedIdentityObjectId -Id $appRoleId -PrincipalId $managedIdentityObjectId -ResourceId $serverServicePrincipalObjectId
    ```

## <a name="complete-script"></a>完全なスクリプト

このスクリプトの例は、Azure Web アプリのマネージド ID をアプリ ロールに割り当てる方法を示したものです。

```powershell
# Install the module. (You need admin on the machine.)
# Install-Module AzureAD

# Your tenant ID (in the Azure portal, under Azure Active Directory > Overview).
$tenantID = '<tenant-id>'

# The name of your web app, which has a managed identity that should be assigned to the server app's app role.
$webAppName = '<web-app-name>'
$resourceGroupName = '<resource-group-name-containing-web-app>'

# The name of the server app that exposes the app role.
$serverApplicationName = '<server-application-name>' # For example, MyApi

# The name of the app role that the managed identity should be assigned to.
$appRoleName = '<app-role-name>' # For example, MyApi.Read.All

# Look up the web app's managed identity's object ID.
$managedIdentityObjectId = (Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $webAppName).identity.principalid

Connect-AzureAD -TenantId $tenantID

# Look up the details about the server app's service principal and app role.
$serverServicePrincipal = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$serverApplicationName'")
$serverServicePrincipalObjectId = $serverServicePrincipal.ObjectId
$appRoleId = ($serverServicePrincipal.AppRoles | Where-Object {$_.Value -eq $appRoleName }).Id

# Assign the managed identity access to the app role.
New-AzureADServiceAppRoleAssignment `
    -ObjectId $managedIdentityObjectId `
    -Id $appRoleId `
    -PrincipalId $managedIdentityObjectId `
    -ResourceId $serverServicePrincipalObjectId
```

## <a name="next-steps"></a>次のステップ

- [Azure リソースのマネージド ID の概要](overview.md)
- Azure VM 上でマネージド ID を有効にするには、「[PowerShell を使用して Azure VM 上の Azure リソースのマネージド ID を構成する](qs-configure-powershell-windows-vm.md)」を参照してください。
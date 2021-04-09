---
title: Azure Event Grid の Azure AD を使用して WebHook 配信をセキュリティで保護する
description: Azure Event Grid を使用して Azure Active Directory によって保護された HTTPS エンドポイントにイベントを配信する方法について説明します
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: be38edfb4edfee5b3a3b42d7b1c5a44c9d72ceef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035447"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Azure Active Directory で保護されたエンドポイントにイベントを発行する

この記事では、Azure Active Directory を利用して、イベント サブスクリプションと webhook エンドポイント間の接続をセキュリティで保護する方法について説明します。 Azure AD アプリケーションとサービス プリンシパルの概要については、「[Microsoft ID プラットフォーム (v2.0) の概要](../active-directory/develop/v2-overview.md)」を参照してください。

この記事ではデモンストレーションのために Azure portal を使用しますが、CLI、PowerShell、または SDK を使用して機能を有効にすることもできます。


## <a name="create-an-azure-ad-application"></a>Azure AD アプリケーションを作成する

まず、保護された エンドポイント用の Azure AD アプリケーションを作成します。 [https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview ) をご覧ください。
    - デーモン アプリで呼び出されるよう、保護された API を構成します。
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Event Grid で Azure AD アプリケーションを使用できるようにします
このセクションでは、Event Grid を有効にして Azure AD アプリケーションを使用する方法を紹介します。 

> [!NOTE]
> このスクリプトを実行するには、[Azure AD アプリケーション管理者ロール](../active-directory/roles/permissions-reference.md#all-roles)のメンバーである必要があります。

### <a name="connect-to-your-azure-tenant"></a>Azure テナントに接続します。
まず、`Connect-AzureAD` コマンドを使用して Azure テナントに接続します。 

```PowerShell
# This is your Tenant Id. 
$myTenantId = "<the Tenant Id of your Azure AD Application>"
Connect-AzureAD -TenantId $myTenantId
```

### <a name="create-microsofteventgrid-service-principal"></a>Microsoft EventGrid サービス プリンシパルの作成
**Microsoft.EventGrid** のサービス プリンシパルがない場合、次のスクリプトを使用して作成します。 

```PowerShell
# This is the "Azure Event Grid" Azure Active Directory AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"
    
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")

# Create the service principal if it doesn't exist
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
} else
{
    # Create a service principal for the "Azure Event Grid" Azure AD Application and add it to the role
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
```

### <a name="create-a-role-for-your-application"></a>アプリケーションのロールの作成   
次のスクリプトを実行し、Azure AD アプリケーションのロールを作成します。 この例では、ロールの名前は **AzureEventGridSecureWebhook** です。 自分の Azure AD テナント ID を使用するように PowerShell スクリプトの `$myTenantId` を変更します。`$myAzureADApplicationObjectId` は、お使いの Azure AD アプリケーションのオブジェクト ID に変更します。

```PowerShell
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}

# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
} else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}

# print application's roles
Write-Host "My Azure AD Application's Roles: "
Write-Host $myAppRoles
```

### <a name="add-event-grid-service-principal-to-the-role"></a>Event Grid サービス プリンシパルをロールに追加する    
次に、`New-AzureADServiceAppRoleAssignment` コマンドを実行し、前の手順で作成したロールに Event Grid サービス プリンシパルを割り当てます。 

```powershell
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
```

次のコマンドを実行すると、次の手順で使用する情報が出力されます。 

```powershell    
Write-Host "My Azure AD Tenant Id: $myTenantId"
Write-Host "My Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Azure AD Application ObjectId: $($myApp.ObjectId)"
```
    
## <a name="configure-the-event-subscription"></a>イベント サブスクリプションを構成する

イベント サブスクリプションの作成フローで、エンドポイントの種類として "Web Hook" を選択します。 エンドポイント URI を指定したら、[イベント サブスクリプションの作成] ブレードの上部にある [追加機能] タブをクリックします。

![エンドポイントの種類で Webhook を選択します。](./media/secure-webhook-delivery/select-webhook.png)

[追加機能] タブで、[Use AAD authentication]\(AAD 認証を使用する\) のチェックボックスをオンにし、テナント ID とアプリケーション ID を構成します。

* スクリプトの出力から Azure AD のテナント ID をコピーし、[AAD テナント ID] フィールドに入力します。
* スクリプトの出力から Azure AD のアプリケーション ID をコピーし、[AAD Application ID]\(AAD アプリケーション ID\) フィールドに入力します。

    ![Secure Webhook アクション](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>次のステップ

* イベント配信の監視について詳しくは、「[Event Grid メッセージ配信の監視](monitor-event-delivery.md)」をご覧ください。
* 認証キーについて詳しくは、「[Event Grid のセキュリティと認証](security-authentication.md)」をご覧ください。
* Azure Event Grid サブスクリプションの作成の詳細については、[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)に関する記事を参照してください。

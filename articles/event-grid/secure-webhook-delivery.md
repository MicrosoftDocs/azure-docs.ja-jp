---
title: Azure Event Grid の Azure AD を使用して WebHook 配信をセキュリティで保護する
description: Azure Event Grid を使用して Azure Active Directory によって保護された HTTPS エンドポイントにイベントを配信する方法について説明します
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 90d06f203bc93177101a87a7a774d816b11b16f6
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460713"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Azure Active Directory で保護されたエンドポイントにイベントを発行する

この記事では、Azure Active Directory を利用して、イベント サブスクリプションと webhook エンドポイント間の接続をセキュリティで保護する方法について説明します。 Azure AD アプリケーションとサービス プリンシパルの概要については、「[Microsoft ID プラットフォーム (v2.0) の概要](../active-directory/develop/v2-overview.md)」を参照してください。

この記事ではデモンストレーションのために Azure portal を使用しますが、CLI、PowerShell、または SDK を使用して機能を有効にすることもできます。

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-azure-ad-application"></a>Azure AD アプリケーションを作成する

まず、保護された エンドポイント用の Azure AD アプリケーションを作成します。 [https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview ) をご覧ください。
    - デーモン アプリで呼び出されるよう、保護された API を構成します。
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Event Grid で Azure AD アプリケーションを使用できるようにします

Azure AD アプリケーションでロールとサービス プリンシパルを作成するには、次の PowerShell スクリプトを使用します。 Azure AD アプリケーションのテナント ID とオブジェクト ID が必要になります。

   > [!NOTE]
   > このスクリプトを実行するには、[Azure AD アプリケーション管理者ロール](../active-directory/users-groups-roles/directory-assign-admin-roles.md#available-roles)のメンバーである必要があります。
    
1. Azure AD テナント ID を使用するように、PowerShell スクリプトの $myTenantId を変更します。
1. Azure AD アプリケーションのオブジェクト ID を使用するように、PowerShell スクリプトの $myAzureADApplicationObjectId を変更します。
1. 変更したスクリプトを実行します。

```PowerShell
# This is your Tenant Id. 
$myTenantId = "<the Tenant Id of your Azure AD Application>"

Connect-AzureAD -TenantId $myTenantId
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the "Azure Event Grid" Azure Active Directory AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"
    
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
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the "Azure Event Grid" Azure AD Application and add it to the role
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
    
Write-Host "My Azure AD Tenant Id: $myTenantId"
Write-Host "My Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Azure AD Application ObjectId: $($myApp.ObjectId)"
Write-Host "My Azure AD Application's Roles: "
Write-Host $myApp.AppRoles
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

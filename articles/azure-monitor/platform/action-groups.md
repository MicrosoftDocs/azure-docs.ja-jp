---
title: Azure Portal でのアクション グループの作成および管理
description: Azure Portal でアクション グループを作成および管理する方法について説明します。
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 7/08/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 842965aa49ae4cd546fe9c107107d2a2ceebebbb
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705252"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Azure Portal でのアクション グループの作成および管理
アクション グループは、Azure サブスクリプションの所有者によって定義された通知設定のコレクションです。 Azure Monitor および Service Health のアラートでは、アクション グループを使用して、アラートがトリガーされたことをユーザーに通知します。 ユーザーの要件に応じて、さまざまなアラートで同じアクション グループを使用することも、異なるアクション グループを使用することもあります。 1 つのサブスクリプションで最大 2,000 のアクション グループを構成できます。

電子メールまたは SMS でユーザーに通知するようアクションを構成すると、ユーザーは自分がアクション グループに追加されたことを示す確認メッセージを受け取ります。

この記事では、Azure Portal でアクション グループを作成および管理する方法について説明します。

各アクションは次のプロパティで構成されます。

* **名前**: アクション グループ内の一意識別子。  
* **アクションの種類**: 実行されるアクション。 たとえば、音声通話、SMS、電子メールの送信やさまざまな種類の自動化されたアクションのトリガーなどです。 この記事の後半の種類を参照してください。
* **[詳細]** :"*アクションの種類*" によって異なる対応する詳細。

Azure Resource Manager テンプレートを使用したアクション グループの構成に関する詳細については、「[アクション グループの Resource Manager テンプレート](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)」を参照してください。

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Azure Portal を使用したアクション グループの作成

1. [Azure Portal](https://portal.azure.com) で、 **[モニター]** を選択します。 **[モニター]** ウィンドウでは、すべての監視設定とデータが 1 つのビューにまとめられています。

    ![[モニター] サービス](./media/action-groups/home-monitor.png)
    
1. **[アラート]** 、 **[アクションの管理]** の順に選択します。

    ![[アクションの管理] ボタン](./media/action-groups/manage-action-groups.png)
    
1. **[アクション グループの追加]** を選択し、フィールドに入力します。

    ![[アクション グループの追加] コマンド](./media/action-groups/add-action-group.png)
    
1. **[アクション グループ名]** ボックスおよび **[短い名前]** ボックスに名前を入力します。 短い名前は、通知がこのグループを使用して送信されるときに長い名前の代わりに使用されます。

      ![[アクション グループの追加] ダイアログ ボックス](./media/action-groups/action-group-define.png)

1. **[サブスクリプション]** ボックスには、現在のサブスクリプションが自動入力されます。 このサブスクリプションにアクション グループが保存されます。

1. アクション グループが保存される **[リソース グループ]** を選択します。

1. アクションの一覧を定義します。 アクションごとに次の内容を指定します。

    1. **[名前]** :このアクションの一意識別子を入力します。

    1. **アクションの種類**:電子メール/SMS/プッシュ/音声、ロジック アプリ、Webhook、ITSM、または Automation Runbook を選択します。

    1. **[詳細]** :アクションの種類に基づいて、電話番号、メール アドレス、Webhook の URI、Azure アプリ、ITSM 接続、または Automation Runbook を入力します。 ITSM アクションの場合は、さらに ITSM ツールで必要な **[作業項目]** および他のフィールドを指定します。
    
    1. **[共通アラート スキーマ]** : [共通アラート スキーマ](https://aka.ms/commonAlertSchemaDocs)を有効にすることを選択できます。これは、Azure Monitor のすべてのアラート サービスにわたって 1 つの拡張可能で、かつ統合されたアラート ペイロードを持つ利点を提供します。

1. **[OK]** を選択して、アクション グループを作成します。

## <a name="manage-your-action-groups"></a>アクション グループの管理

アクション グループを作成すると、 **[モニター]** ウィンドウの **[アクション グループ]** セクションに表示されます。 次の操作を行うために管理するアクション グループを選択します。

* アクションの追加、編集、または削除。
* アクション グループの削除。

## <a name="action-specific-information"></a>アクション固有の情報

> [!NOTE]
> 次の各項目の数値の制限については、[監視のためのサブスクリプション サービスの制限](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-monitor-limits)に関するセクションを参照してください。  

### <a name="azure-app-push-notifications"></a>Azure アプリのプッシュ通知
アクション グループには、限られた数の Azure アプリのアクションを保持できます。

### <a name="email"></a>Email
電子メールは、次の電子メール アドレスから送信されます。 電子メールのフィルタリングが適切に構成されていることを確認してください
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

アクション グループには、電子メールに関する限られた数のアクションを持つことができます。 [レート制限情報](./../../azure-monitor/platform/alerts-rate-limiting.md)の記事を参照してください。

### <a name="itsm"></a>ITSM
ITSM アクションには ITSM 接続が必要です。 [ITSM 接続](../../azure-monitor/platform/itsmc-overview.md)の作成方法を確認してください。

アクション グループには、限られた数の ITSM アクションを保持できます。 

### <a name="logic-app"></a>ロジック アプリ
アクション グループには、限られた数のロジック アプリのアクションを保持できます。

### <a name="function"></a>Function
アクションとして構成された Function App の関数キーは Functions API から読み込まれます。これは現在、アプリ設定 "AzureWebJobsSecretStorageType" を "files" に構成するには v2 の関数アプリが必要です。 詳細については、「[Changes to Key Management in Functions V2 (Functions V2 でのキー管理の変更)]( https://aka.ms/funcsecrets)」を参照してください。

アクション グループには、限られた数の Function アクションを保持できます。

### <a name="automation-runbook"></a>Automation Runbook
Runbook ペイロードの制限については、[Azure サブスクリプション サービスの制限](../../azure-subscription-service-limits.md)に関するページを参照してください。

アクション グループには、限られた数の Runbook アクションを保持できます。 

### <a name="sms"></a>sms
その他の重要な情報については、[レート制限情報](./../../azure-monitor/platform/alerts-rate-limiting.md)と [SMS アラート動作](../../azure-monitor/platform/alerts-sms-behavior.md)に関する各ページを参照してください。

アクション グループには、限られた数の SMS アクションを保持できます。  

### <a name="voice"></a>音声
[レート制限情報](./../../azure-monitor/platform/alerts-rate-limiting.md)の記事を参照してください。

アクション グループには、限られた数の音声アクションを保持できます。

### <a name="webhook"></a>Webhook
Webhook は、次のルールを使用して再試行されます。 Webhook の呼び出しが最大 2 回再試行されるのは、HTTP 状態コードの 408、429、503、504 が返されるか、または HTTP エンドポイントが応答しない場合です。 1 回目の再試行は 10 秒後に実行されます。 2 回目の再試行は 100 秒後に実行されます。 2 回失敗した後の 30 分間、エンドポイントはアクション グループから呼び出されません。 

発信元 IP アドレスの範囲
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

これらの IP アドレスへの変更に関する更新情報を受け取るには、アクション グループ サービスに関する情報の通知を監視するサービス正常性アラートを構成することをお勧めします。

アクション グループには、限られた数の Webhook アクションを保持できます。

#### <a name="secure-webhook"></a>Secure Webhook
**Secure Webhook 機能は現在、プレビュー段階です。**

アクション グループの Webhook アクションを使用すると、Azure Active Directory を利用して、アクション グループと、保護された Web API (Webhook エンドポイント) との間の接続をセキュリティで保護することができます。 この機能を利用するための全体的なワークフローを次に示します。 Azure AD アプリケーションとサービス プリンシパルの概要については、「[Microsoft ID プラットフォーム (v2.0) の概要](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)」を参照してください。

1. 保護された Web API 用の Azure AD アプリケーションを作成します。 https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview をご覧ください。
    - デーモン アプリで呼び出されるよう、保護された API を構成します。
    
1. アクション グループで Azure AD アプリケーションを使用できるようにします。

    > [!NOTE]
    > このスクリプトを実行するには、[Azure AD アプリケーション管理者ロール](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles)のメンバーである必要があります。
    
    - Azure AD テナント ID を使用するように、PowerShell スクリプトの Connect-AzureAD 呼び出しを変更します。
    - Azure AD アプリケーションのオブジェクト ID を使用するように、PowerShell スクリプトの変数 $myAzureADApplicationObjectId を変更します。
    - 変更したスクリプトを実行します。
    
1. アクション グループの Webhook アクションを構成します。
    - スクリプトから値 $myApp.ObjectId をコピーし、Webhook アクション定義の [アプリケーション オブジェクト ID] フィールドに入力します。
    
    ![Secure Webhook アクション](./media/action-groups/action-groups-secure-webhook.png)

##### <a name="secure-webhook-powershell-script"></a>Secure Webhook PowerShell スクリプト

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
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
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```


## <a name="next-steps"></a>次の手順
* 詳細については、「[SMS アラート動作](../../azure-monitor/platform/alerts-sms-behavior.md)」を参照してください。  
* [アクティビティ ログ アラートに対する webhook スキーマについて理解](../../azure-monitor/platform/activity-log-alerts-webhook.md)します。  
* [ITSM コネクタ](../../azure-monitor/platform/itsmc-overview.md)について学習します。
* アラートの[レート制限](../../azure-monitor/platform/alerts-rate-limiting.md)について学習します。
* [アクティビティ ログ アラートの概要](../../azure-monitor/platform/alerts-overview.md)を把握し、アラートを受信する方法について学習します。  
* [サービスの正常性通知が投稿されるたびにアラートを設定](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)する方法について学習します。

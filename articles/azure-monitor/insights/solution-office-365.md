---
title: Azure の Office 365 管理ソリューション | Microsoft Docs
description: この記事では、Azure での Office 365 ソリューションの構成と使用について詳しく説明します。  Azure Monitor で作成された Office 365 レコードの詳細な説明が含まれています。
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/01/2019
ms.author: bwren
ms.openlocfilehash: 3f4b0ad8b7aad01472a76db67f2c07e03e978e41
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673041"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Azure の Office 365 管理ソリューション (プレビュー)

![Office 365 のロゴ](media/solution-office-365/icon.png)


> [!NOTE]
> Office 365 ソリューションをインストールして構成する手段としては、この記事の各手順を使用するのではなく、[Azure Sentinel](../../sentinel/overview.md) で [Office 365 コネクタ](../../sentinel/connect-office-365.md)を有効にする方法が推奨されます。 これは、構成エクスペリエンスが向上した最新版の Office 365 ソリューションです。 Azure AD ログを接続するには、[Azure Sentinel Azure AD コネクタ](../../sentinel/connect-azure-active-directory.md)を使用するか、[Azure AD 診断設定](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)を構成します。これにより、Office 365 管理ログよりも豊富なログ データが提供されます。 
>
> [Azure Sentinel をオンボード](../../sentinel/quickstart-onboard.md)する際は、Office 365 ソリューションのインストール先となる Log Analytics ワークスペースを指定します。 コネクタを有効にすると、ワークスペースでソリューションが利用できるようになります。使い方は、既にインストールされている他の監視ソリューションとまったく同じです。
>
> Azure Government クラウドのユーザーは、この記事の手順に従って Office 365 をインストールする必要があります。政府機関向けクラウドでは、まだ Azure Sentinel が提供されていません。

Office 365 管理ソリューションでは、Azure Monitor で Office 365 環境を監視できます。

- Office 365 アカウント上でのユーザー アクティビティを監視し、使用パターンを分析したり、行動傾向を識別したりします。 たとえば、組織の外で共有されるファイルや、最も人気のある SharePoint サイトといった特定の使用シナリオを抽出することができます。
- 管理者のアクティビティを監視し、構成変更や高権限操作を追跡します。
- 不必要なユーザーの行動を検出および調査します。これは、組織のニーズに合わせてカスタマイズできます。
- 監査とコンプライアンスを実証します。 たとえば、機密ファイルに対するファイル アクセス操作を監視でき、これは監査とコンプライアンスのプロセスに役立ちます。
- 組織の Office 365 アクティビティ データに対して[ログ クエリ](../log-query/log-query-overview.md)を使用し、運用上のトラブルシューティングを実行します。


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

このソリューションをインストールして構成する前に、次のものが必要です。

- 組織の Office 365 サブスクリプション。
- グローバル管理者であるユーザー アカウントの資格情報。
- 監査データを受信するには、Office 365 サブスクリプションで[監査を構成する](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin)必要があります。  [メールボックスの監査](https://technet.microsoft.com/library/dn879651.aspx)は個別に構成されることに注意してください。  監査が構成されていない場合でも、ソリューションをインストールしてその他のデータを収集することは可能です。
 

## <a name="management-packs"></a>管理パック

このソリューションでは、[接続されている管理グループ](../platform/om-agents.md)に管理パックがインストールされることはありません。
  

## <a name="install-and-configure"></a>インストールと構成

[サブスクリプションに Office 365 ソリューション](solutions.md#install-a-monitoring-solution)を追加することで開始します。 追加されたら、Office 365 サブスクリプションへのアクセス権を付与するため、このセクションの構成手順を実行する必要があります。

### <a name="required-information"></a>必要な情報

この手順を開始する前に、次の情報を収集します。

Log Analytics ワークスペースから:

- ワークスペース名: Office 365 データが収集されるワークスペース。
- リソース グループ名: そのワークスペースを含むリソース グループ。
- Azure サブスクリプション ID: そのワークスペースを含むサブスクリプション。

Office 365 サブスクリプションから:

- ユーザー名:管理者アカウントの電子メール アドレス。
- テナント ID: Office 365 サブスクリプションの一意の ID。
- クライアント ID: Office 365 クライアントを表す 16 文字の文字列。
- クライアント シークレット: 認証に必要な暗号化された文字列。

### <a name="create-an-office-365-application-in-azure-active-directory"></a>Azure Active Directory に Office 365 アプリケーションを作成する

最初の手順では、管理ソリューションが Office 365 ソリューションへのアクセスに使用する Azure Active Directory でアプリケーションを作成します。

1. Azure Portal ([https://portal.azure.com](https://portal.azure.com/)) にログインします。
1. **[Azure Active Directory]** を選択し、 **[アプリの登録]** を選択します。
1. **[新しいアプリケーションの登録]** をクリックします。

    ![アプリの登録の追加](media/solution-office-365/add-app-registration.png)
1. アプリケーションの **[名前]** を入力し、 **[サインオン URL]** をクリックします。  名前は、わかりやすいである必要があります。  URL には `http://localhost` を使用し、 **[アプリケーションの種類]** として _[Web アプリ/API]_ を選択します。
    
    ![アプリケーションの作成](media/solution-office-365/create-application.png)
1. **[作成]** をクリックしてアプリケーションの情報を検証します。

    ![登録済みのアプリ](media/solution-office-365/registered-app.png)

### <a name="configure-application-for-office-365"></a>Office 365 のアプリケーションを構成する

1. **[設定]** をクリックし、 **[設定]** メニューを開きます。
1. **[プロパティ]** を選択します。 **[マルチテナント]** を _[はい]_ に変更します。

    ![マルチテナントの設定](media/solution-office-365/settings-multitenant.png)

1. **[設定]** メニューで、 **[必要なアクセス許可]** を選択し、 **[追加]** をクリックします。
1. **[API を選択します]** をクリックして **[Office 365 Management API]** をクリックします。 **[Office 365 Management API]** をクリックします。 **[選択]** をクリックします。

    ![API の選択](media/solution-office-365/select-api.png)

1. **[アクセス許可を選択]** で **[アプリケーションのアクセス許可]** と **[デリゲートされたアクセス許可]** の両方の以下のオプションを選択します。
   - 組織の Service Health 情報の読み取り
   - 組織のアクティビティ データの読み取り
   - 組織の活動レポートの読み取り

     ![API の選択](media/solution-office-365/select-permissions.png)

1. **[選択]** 、 **[完了]** の順にクリックします。
1. **[アクセス許可の付与]** をクリックし、確認を求められたら **[はい]** をクリックします。

    ![アクセス許可を付与する](media/solution-office-365/grant-permissions.png)

### <a name="add-a-key-for-the-application"></a>アプリケーションのキーを追加する

1. **[設定]** メニューで、 **[キー]** を選択します。
1. 新しいキーの **[説明]** と **[期間]** に入力します。
1. **[保存]** をクリックし、生成された **[値]** をコピーします。

    ![構成する](media/solution-office-365/keys.png)

### <a name="add-admin-consent"></a>管理者の同意の入力

最初に管理者アカウントを有効にするには、アプリケーションの管理者の同意を指定する必要があります。 これは PowerShell スクリプトで行うことができます。 

1. 次のスクリプトを *office365_consent.ps1* として保存します。

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,     
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId
    )
    
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $WorkspaceLocation= $Workspace.Location
    $WorkspaceLocation
    
    Function AdminConsent{
    
    $domain='login.microsoftonline.com'
    switch ($WorkspaceLocation.Replace(" ","").ToLower()) {
           "eastus"   {$OfficeAppClientId="d7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westeurope"   {$OfficeAppClientId="c9005da2-023d-40f1-a17a-2b7d91af4ede"; break}
           "southeastasia"   {$OfficeAppClientId="09c5b521-648d-4e29-81ff-7f3a71b27270"; break}
           "australiasoutheast"  {$OfficeAppClientId="f553e464-612b-480f-adb9-14fd8b6cbff8"; break}   
           "westcentralus"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break}
           "japaneast"   {$OfficeAppClientId="b07d97d3-731b-4247-93d1-755b5dae91cb"; break}
           "uksouth"   {$OfficeAppClientId="f232cf9b-e7a9-4ebb-a143-be00850cd22a"; break}
           "centralindia"   {$OfficeAppClientId="ffbd6cf4-cba8-4bea-8b08-4fb5ee2a60bd"; break}
           "canadacentral"  {$OfficeAppClientId="c2d686db-f759-43c9-ade5-9d7aeec19455"; break}
           "eastus2"  {$OfficeAppClientId="7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westus2"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break} #Need to check
           "usgovvirginia" {$OfficeAppClientId="c8b41a87-f8c5-4d10-98a4-f8c11c3933fe"; 
                             $domain='login.microsoftonline.us'; break}
           default {$OfficeAppClientId="55b65fb5-b825-43b5-8972-c8b6875867c1";
                    $domain='login.windows-ppe.net'; break} #Int
        }
    
        $domain
        Start-Process -FilePath  "https://$($domain)/common/adminconsent?client_id=$($OfficeAppClientId)&state=12345"
    }
    
    AdminConsent -ErrorAction Stop
    ```

2. 次のコマンドを実行して、スクリプトを実行します。 資格情報を求めるメッセージが 2 回表示されます。 最初に Log Analytics ワークスペースの資格情報を指定し、次に Office 365 テナントのグローバル管理者の資格情報を指定します。

    ```
    .\office365_consent.ps1 -WorkspaceName <Workspace name> -ResourceGroupName <Resource group name> -SubscriptionId <Subscription ID>
    ```

    例:

    ```
    .\office365_consent.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631- yyyyyyyyyyyy'
    ```

1. 次のようなウィンドウが表示されます。 **[Accept]\(受け入れる\)** をクリックします。
    
    ![管理者の同意](media/solution-office-365/admin-consent.png)

### <a name="subscribe-to-log-analytics-workspace"></a>Log Analytics ワークスペースへの送信

最後の手順では、Log Analytics ワークスペースにアプリケーションを送信します。 これは PowerShell スクリプトでも行うことができます。

1. 次のスクリプトを *office365_subscription.ps1* として保存します。

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeUsername,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId,
        [Parameter(Mandatory=$True)][string]$OfficeClientId,
        [Parameter(Mandatory=$True)][string]$OfficeClientSecret
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    $OfficeClientSecret =[uri]::EscapeDataString($OfficeClientSecret)
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant = $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $global:authResultARM = $authContext.AcquireToken($resourceAppIdURIARM, $clientId, $redirectUri, "Auto")
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Failure {
    $line
    $formatstring = "{0} : {1}`n{2}`n" +
                    "    + CategoryInfo          : {3}`n" +
                    "    + FullyQualifiedErrorId : {4}`n"
    $fields = $_.InvocationInfo.MyCommand.Name,
              $_.ErrorDetails.Message,
              $_.InvocationInfo.PositionMessage,
              $_.CategoryInfo.ToString(),
              $_.FullyQualifiedErrorId
    
    $formatstring -f $fields
    $_.Exception.Response
    
    $line
    break
    }
    
    Function Connection-API
    {
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    
    $line
    $connectionAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/connections/office365connection_' + $SubscriptionId + $OfficeTennantId + '?api-version=2017-04-26-preview'
    $connectionAPIUrl
    $line
    
    $xms_client_tenant_Id ='1da8f770-27f4-4351-8cb3-43ee54f14759'
    
    $BodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'clientId': '" + $OfficeClientId + "',
                                    'clientSecret': '" + $OfficeClientSecret + "',
                                    'Username': '" + $OfficeUsername   + "',
                                    'Url': 'https://$($domain)/" + $OfficeTennantId + "/oauth2/token',
                                  },
                    'etag': '*',
                    'kind': 'Connection',
                    'solution': 'Connection',
                   }"
    
    $params = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id #Prod-'1da8f770-27f4-4351-8cb3-43ee54f14759'
        'Content-Type' = 'application/json'
        }
        Body = $BodyString
        Method = 'Put'
        URI = $connectionAPIUrl
    }
    $response = Invoke-WebRequest @params 
    $response
    $line
    
    }
    
    Function Office-Subscribe-Call{
    try{
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_' + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $OfficeBodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'office365TenantID': '" + $OfficeTennantId + "',
                                    'connectionID': 'office365connection_" + $SubscriptionId + $OfficeTennantId + "',
                                    'office365AdminUsername': '" + $OfficeUsername + "',
                                    'contentTypes':'Audit.Exchange,Audit.AzureActiveDirectory,Audit.SharePoint'
                                  },
                    'etag': '*',
                    'kind': 'Office365',
                    'solution': 'Office365',
                   }"
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Body = $OfficeBodyString
        Method = 'Put'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    }
    catch{ Failure }
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Connection-API -ErrorAction Stop
    Office-Subscribe-Call -ErrorAction Stop
    ```

2. 次のコマンドを実行して、スクリプトを実行します。

    ```
    .\office365_subscription.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeUsername <OfficeUsername> -OfficeTennantID <Tenant ID> -OfficeClientId <Client ID> -OfficeClientSecret <Client secret>
    ```

    例:

    ```powershell
    .\office365_subscription.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeUsername 'admin@contoso.com' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx' -OfficeClientId 'f8f14c50-5438-4c51-8956-zzzzzzzzzzzz' -OfficeClientSecret 'y5Lrwthu6n5QgLOWlqhvKqtVUZXX0exrA2KRHmtHgQb='
    ```

### <a name="troubleshooting"></a>トラブルシューティング

アプリケーションがこのワークスペースに既にサブスクライブしている場合や、このテナントが別のワークスペースにサブスクライブしている場合は、次のエラーが表示されることがあります。

```Output
Invoke-WebRequest : {"Message":"An error has occurred."}
At C:\Users\v-tanmah\Desktop\ps scripts\office365_subscription.ps1:161 char:19
+ $officeresponse = Invoke-WebRequest @Officeparams
+                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand 
```

無効なパラメーター値を指定した場合は、次のエラーが表示される場合があります。

```Output
Select-AzSubscription : Please provide a valid tenant or a valid subscription.
At line:12 char:18
+ ... cription = (Select-AzSubscription -SubscriptionId $($Subscriptio ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzContext], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.SetAzContextCommand

```

## <a name="uninstall"></a>アンインストール

[管理ソリューションを削除する](solutions.md#remove-a-monitoring-solution)のプロセスを使用して Office 365 管理ソリューションを削除できます。 ただしこれによって、Office 365 から Azure Monitor に収集されているデータは停止されません。 Office 365 からのサブスクリプションを解除し、データの収集を停止するには、以下の手順に従います。

1. 次のスクリプトを *office365_unsubscribe.ps1* として保存します。

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $global:authResultARM = $authContext.AcquireToken($resourceAppIdURIARM, $clientId, $redirectUri, "Auto")
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. 次のコマンドを実行して、スクリプトを実行します。

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    例:

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

## <a name="data-collection"></a>データ収集

### <a name="supported-agents"></a>サポートされているエージェント

Office 365 ソリューションは、どの [Log Analytics エージェント](../platform/agent-data-sources.md)からもデータを取得しません。  Office 365 から直接データを取得します。

### <a name="collection-frequency"></a>収集の頻度

最初のデータ収集には数時間かかる場合があります。 収集が開始されると、レコードが作成されるたびに、Office 365 は [webhook 通知](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications)と詳細なデータを Azure Monitor に送信します。 このレコードは、受信した後、数分以内に Azure Monitor で使用できます。

## <a name="using-the-solution"></a>ソリューションの使用

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Log Analytics ワークスペースに Office 365 ソリューションを追加すると、ダッシュボードに **[Office 365]** タイルが追加されます。 このタイルには、ご利用の環境におけるコンピューターの数と更新プログラムの対応状態が数字とグラフで表示されます。<br><br>
![Office 365 の概要タイル](media/solution-office-365/tile.png)  

**[Office 365]** タイルをクリックして **[Office 365]** ダッシュボードを開きます。

![Office 365 ダッシュボード](media/solution-office-365/dashboard.png)  

ダッシュボードには、次の表に示した列が存在します。 それぞれの列には、特定のスコープと時間範囲について、その列の基準に該当するアラート数の上位 10 件が表示されます。 ログ検索を実行してアラート全件を取得するには、列の一番下にある [See all] \(すべて表示) をクリックするか、列ヘッダーをクリックします。

| 列 | 説明 |
|:--|:--|
| Operations | すべての監視対象 Office 365 サブスクリプションから、アクティブ ユーザーに関する情報を提供します。 時間の経過と共に発生するアクティビティの数を見ることもできます。
| Exchange | Add-Mailbox Permission、または Set-Mailbox などの Exchange Server アクティビティの内訳を示します。 |
| SharePoint | SharePoint ドキュメントに対してユーザーが実行する最上位のアクティビティを示します。 このタイルからドリル ダウンすると、ターゲット ドキュメントやこのアクティビティの場所など、これらのアクティビティの詳細が検索ページに表示されます。 たとえば、File Accessed イベントの場合、アクセスされているドキュメント、それと関連付けられたアカウント名、および IP アドレスを見ることができます。 |
| Azure Active Directory | ユーザー パスワードのリセットやログイン試行など、最上位のユーザー アクティビティが含まれます。 ドリルダウンすると、結果の状態など、これらのアクティビティの詳細を見ることができます。 これは主に、Azure Active Directory 上の不審なアクティビティを監視する場合に便利です。 |




## <a name="azure-monitor-log-records"></a>Azure Monitor のログ レコード

Azure Monitor の Log Analytics ワークスペースで Office 365 ソリューションによって作成されたすべてのレコードは、**型** が **OfficeActivity** です。  **OfficeWorkload**プロパティは、レコードが参照する Office 365 サービス (Exchange、AzureActiveDirectory、SharePoint、または OneDrive) を決定します。  **RecordType** プロパティは操作の種類を指定します。  プロパティは操作の種類ごとに異なり、次の表に示しています。

### <a name="common-properties"></a>共通のプロパティ

次のプロパティは、Office 365 のすべてのレコードに共通です。

| プロパティ | Description |
|:--- |:--- |
| Type | *OfficeActivity* |
| ClientIP | アクティビティが記録されたときに使用されたデバイスの IP アドレス。 IP アドレスは IPv4 または IPv6 アドレスの形式で表示されます。 |
| OfficeWorkload | レコードが参照する Office 365 サービス。<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Operation | ユーザーまたは管理者アクティビティの名前。  |
| OrganizationId | 組織の Office 365 テナントの GUID。 どの Office 365 サービスで発生するかにかかわらず、この値は組織に対して常に同じになります。 |
| RecordType | 実行する操作の種類。 |
| ResultStatus | (Operation プロパティで指定された) アクションが正常に終了したかどうかを示します。 値は Succeeded、PartiallySucceeded、Failed のいずれかです。 Exchange 管理者アクティビティの場合、値は True または False です。 |
| UserId | レコードがログに記録される結果になったアクションを実行したユーザーの UPN (ユーザー プリンシパル名)。たとえば、my_name@my_domain_name。 (SHAREPOINT\system や NTAUTHORITY\SYSTEM などの) システム アカウントによって実行されるアクティビティのレコードも含まれることに注意してください。 | 
| UserKey | UserId プロパティで識別されるユーザーの代替 ID。  たとえば、SharePoint、OneDrive for Business、および Exchange でユーザーによって実行されたイベントの Passport 一意識別子 (PUID) が、このプロパティの値になります。 このプロパティは、他のサービスで発生するイベントや、システム アカウントによって実行されるイベントの UserID プロパティと同じ値を指定する場合もあります。|
| UserType | 操作を実行したユーザーの種類。<br><br>[Admin]<br>Application<br>DcAdmin<br>通常<br>予約済み<br>ServicePrincipal<br>システム |


### <a name="azure-active-directory-base"></a>Azure Active Directory ベース

次のプロパティは、Azure Active Directory のすべてのレコードに共通です。

| プロパティ | 説明 |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Azure AD イベントの種類。 |
| ExtendedProperties | Azure AD イベントの拡張プロパティ。 |


### <a name="azure-active-directory-account-logon"></a>Azure Active Directory アカウント ログオン

これらのレコードは、Active Directory のユーザーがログオンを試みたときに作成されます。

| プロパティ | 説明 |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectoryAccountLogon |
| Application | アカウント ログイン イベントをトリガーするアプリケーション (Office 15 など)。 |
| Client | アカウント ログイン イベントで使用されたクライアント デバイス、デバイス OS、およびデバイス ブラウザーの詳細。 |
| LoginStatus | このプロパティは OrgIdLogon.LoginStatus に直接由来します。 アルゴリズムを変えることにより、さまざまな興味深いログオン失敗のマッピングを実行できます。 |
| UserDomain | テナント ID 情報 (TII)。 | 


### <a name="azure-active-directory"></a>Azure Active Directory

これらのレコードは、変更または追加が Azure Active Directory オブジェクトに行われたときに作成されます。

| プロパティ | 説明 |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | (Operation プロパティによって識別される) アクションの実行対象だったユーザー。 |
| Actor | アクションを実行したユーザーまたはサービス プリンシパル。 |
| ActorContextId | アクターが所属する組織の GUID。 |
| ActorIpAddress | アクターの IP アドレス。IPv4 または IPv6 アドレスの形式。 |
| InterSystemsId | Office 365 サービス内のコンポーネント間でアクションを追跡する GUID。 |
| IntraSystemId |   アクションを追跡するために Azure Active Directory によって生成される GUID。 |
| SupportTicketId | "代理操作" の状況におけるアクションのカスタマー サポート チケット ID。 |
| TargetContextId | ターゲットのユーザーが所属する組織の GUID。 |


### <a name="data-center-security"></a>データ センター セキュリティ

これらのレコードは、データ センター セキュリティの監査データから作成されます。  

| プロパティ | 説明 |
|:--- |:--- |
| EffectiveOrganization | 昇格/コマンドレットのターゲットだったテナントの名前。 |
| ElevationApprovedTime | 昇格が承認されたときのタイムスタンプ。 |
| ElevationApprover | Microsoft マネージャーの名前。 |
| ElevationDuration | 昇格がアクティブだった期間。 |
| ElevationRequestId |  昇格要求の一意識別子。 |
| ElevationRole | 昇格が要求されたロール。 |
| ElevationTime | 昇格の開始時刻。 |
| Start_Time | コマンドレット実行の開始時刻。 |


### <a name="exchange-admin"></a>Exchange 管理者

これらのレコードは、Exchange 構成が変更されたときに作成されます。

| プロパティ | 説明 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  組織内のユーザー、Microsoft のデータセンター担当者またはデータセンター サービス アカウント、委任された管理者のいずれによってコマンドレットが実行されたかを指定します。 False の値は、組織内の人物によってコマンドレットが実行されたことを示します。 True の値は、データセンター担当者、データセンター サービス アカウント、または委任管理者によってコマンドレットが実行されたことを示します。 |
| ModifiedObjectResolvedName |  コマンドレットによって変更されたオブジェクトのユーザー フレンドリ名。 コマンドレットがオブジェクトを変更する場合にのみ記録されます。 |
| OrganizationName | テナントの名前。 |
| OriginatingServer | コマンドレットの実行元だったサーバーの名前。 |
| parameters | Operations プロパティで識別されるコマンドレットと共に使用されたすべてのパラメーターの名前と値。 |


### <a name="exchange-mailbox"></a>Exchange メールボックス

これらのレコードは、変更または追加が Exchange メールボックスに行われたときに作成されます。

| プロパティ | 説明 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | ブラウザーのバージョン、Outlook のバージョン、モバイル デバイス情報など、操作を実行するために使用された電子メール クライアントに関する情報。 |
| Client_IPAddress | 操作が記録されたときに使用されたデバイスの IP アドレス。 IP アドレスは IPv4 または IPv6 アドレスの形式で表示されます。 |
| ClientMachineName | Outlook クライアントをホストするマシン名。 |
| ClientProcessName | メールボックスへのアクセスに使用された電子メール クライアント。 |
| ClientVersion | 電子メール クライアントのバージョン。 |
| InternalLogonType | 内部使用のために予約されています。 |
| Logon_Type | メールボックスにアクセスし、記録された操作を実行したユーザーの種類を示します。 |
| LogonUserDisplayName |    操作を実行したユーザーのユーザー フレンドリ名。 |
| LogonUserSid | 操作を実行したユーザーの SID。 |
| MailboxGuid | アクセスされたメールボックスの Exchange GUID。 |
| MailboxOwnerMasterAccountSid | メールボックス所有者アカウントのマスター アカウント SID。 |
| MailboxOwnerSid | メールボックス所有者の SID。 |
| MailboxOwnerUPN | アクセスされたメールボックスを所有する人物の電子メール アドレス。 |


### <a name="exchange-mailbox-audit"></a>Exchange メールボックス監査

これらのレコードは、メールボックス監査エントリが作成されるときに作成されます。

| プロパティ | 説明 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| Item | 操作が実行された対象の項目を表します。 | 
| SendAsUserMailboxGuid | その名前で電子メールを送信するためにアクセスされたメールボックスの Exchange GUID。 |
| SendAsUserSmtp | 偽装されているユーザーの SMTP アドレス。 |
| SendonBehalfOfUserMailboxGuid | 代理でメールを送信するためにアクセスされたメールボックスの Exchange GUID。 |
| SendOnBehalfOfUserSmtp | その代理でメールが送信されるユーザーの SMTP アドレス。 |


### <a name="exchange-mailbox-audit-group"></a>Exchange メールボックス監査グループ

これらのレコードは、変更または追加が Exchange グループに行われたときに作成されます。

| プロパティ | 説明 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | グループ内の各項目に関する情報。 |
| CrossMailboxOperations | 複数のメールボックスが操作に関係したかどうかを示します。 |
| DestMailboxId | CrossMailboxOperations パラメーターが True の場合にのみ設定します。 ターゲット メールボックス GUID を指定します。 |
| DestMailboxOwnerMasterAccountSid | CrossMailboxOperations パラメーターが True の場合にのみ設定します。 ターゲット メールボックス所有者のマスター アカウント SID の SID を指定します。 |
| DestMailboxOwnerSid | CrossMailboxOperations パラメーターが True の場合にのみ設定します。 ターゲット メールボックスの SID を指定します。 |
| DestMailboxOwnerUPN | CrossMailboxOperations パラメーターが True の場合にのみ設定します。 ターゲット メールボックスの所有者の UPN を指定します。 |
| DestFolder | 移動などの操作の宛先フォルダー。 |
| Folder | 項目のグループが位置しているフォルダー。 |
| Folders |     操作に関係したソース フォルダーに関する情報。たとえば、フォルダーが選択後に削除されるかどうか。 |


### <a name="sharepoint-base"></a>SharePoint ベース

これらのプロパティは、SharePoint のすべてのレコードに共通です。

| プロパティ | 説明 |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | SharePoint で発生したイベントを識別します。 値は SharePoint または ObjectModel です。 |
| ItemType | アクセスまたは変更されたオブジェクトの種類。 オブジェクトの種類の詳細については、ItemType の表を参照してください。 |
| MachineDomainInfo | デバイス同期操作に関する情報。 この情報は、要求に存在する場合にのみ報告されます。 |
| MachineId |   デバイス同期操作に関する情報。 この情報は、要求に存在する場合にのみ報告されます。 |
| Site_ | ユーザーがアクセスするファイルまたはフォルダーがあるサイトの GUID。 |
| Source_Name | 監査対象の操作をトリガーしたエンティティ。 値は SharePoint または ObjectModel です。 |
| UserAgent | ユーザーのクライアントまたはブラウザーに関する情報。 この情報は、クライアントまたはブラウザーによって提供されます。 |


### <a name="sharepoint-schema"></a>SharePoint スキーマ

これらのレコードは、SharePoint の構成変更が行われたときに作成されます。

| プロパティ | 説明 |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | カスタム イベントに関する省略可能文字列。 |
| Event_Data |  カスタム イベントに関する省略可能ペイロード。 |
| ModifiedProperties | サイトまたはサイト コレクション管理者グループのメンバーとしてユーザーを追加するなどの管理者イベントに含まれるプロパティ。 プロパティには、変更されたプロパティの名前 (サイト管理者グループなど)、変更されたプロパティの新しい値 (サイト管理者として追加されたユーザーなど)、変更されたオブジェクトの以前の値が含まれます。 |


### <a name="sharepoint-file-operations"></a>SharePoint ファイル操作

これらのレコードは、SharePoint でのファイル操作に応答して作成されます。

| プロパティ | 説明 |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | コピーまたは移動されるファイルのファイル拡張子。 このプロパティは FileCopied および FileMoved イベントについてのみ表示されます。 |
| DestinationFileName | コピーまたは移動されるファイルの名前。 このプロパティは FileCopied および FileMoved イベントについてのみ表示されます。 |
| DestinationRelativeUrl | ファイルのコピーまたは移動先フォルダーの URL。 SiteURL、DestinationRelativeURL、および DestinationFileName パラメーターの値の組み合わせは、(コピーされたファイルの完全パス名である) ObjectID プロパティの値と同じです。 このプロパティは FileCopied および FileMoved イベントについてのみ表示されます。 |
| SharingType | リソースの共有相手だったユーザーに割り当てられていた共有アクセス許可の種類。 このユーザーは UserSharedWith パラメーターによって識別されます。 |
| Site_Url | ユーザーがアクセスするファイルまたはフォルダーがあるサイトの URL。 |
| SourceFileExtension | ユーザーがアクセスしたファイルのファイル拡張子。 アクセスしたオブジェクトがフォルダーの場合、このプロパティは空です。 |
| SourceFileName |  ユーザーがアクセスしたファイルまたはフォルダーの名前。 |
| SourceRelativeUrl | ユーザーがアクセスするファイルが含まれているフォルダーの URL。 SiteURL、SourceRelativeURL、および SourceFileName パラメーターの値の組み合わせは、(ユーザーがアクセスするファイルの完全パス名である) ObjectID プロパティの値と同じです。 |
| UserSharedWith |  リソースの共有相手だったユーザー。 |




## <a name="sample-log-searches"></a>サンプル ログ検索

次の表は、このソリューションによって収集された更新レコードを探すログ検索の例です。

| Query | 説明 |
| --- | --- |
|Office 365 サブスクリプションでのすべての操作のカウント |OfficeActivity &#124; summarize count() by Operation |
|SharePoint サイトの使用率|OfficeActivity &#124; where OfficeWorkload =~ "sharepoint" &#124; summarize count() by SiteUrl \| sort by Count asc|
|ユーザーの種類別のファイル アクセス操作|search in (OfficeActivity) OfficeWorkload =~ "azureactivedirectory" and "MyTest"|
|特定のキーワードで検索する|Type=OfficeActivity OfficeWorkload=azureactivedirectory "MyTest"|
|Exchange 上の外部アクションを監視する|OfficeActivity &#124; where OfficeWorkload =~ "exchange" and ExternalAccess == true|



## <a name="next-steps"></a>次の手順

* [Azure Monitor でログ クエリ](../log-query/log-query-overview.md)を使用して、詳細な更新プログラムのデータを表示します。
* [独自のダッシュボードを作成](../learn/tutorial-logs-dashboards.md)して、お気に入りの Office 365 検索クエリを表示します。
* [アラートを作成](../platform/alerts-overview.md)して、重要な Office 365 アクティビティがあらかじめ通知されるようにします。  
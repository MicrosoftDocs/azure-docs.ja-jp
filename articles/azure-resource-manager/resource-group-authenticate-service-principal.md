---
title: "PowerShell での Azure アプリ ID の作成 | Microsoft Docs"
description: "Azure PowerShell を使用して、Azure Active Directory アプリケーションやサービス プリンシパルを作成し、ロールベースのアクセス制御によって、リソースへのアクセス権を付与する方法について説明します。 パスワードまたは証明書を使ってアプリケーションを認証する方法を示します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/28/2017
ms.author: tomfitz
ms.openlocfilehash: df5d705a4451950110c11b7d468bf7d59e5474d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-powershell-to-create-a-service-principal-to-access-resources"></a>リソースにアクセスするためのサービス プリンシパルを Azure PowerShell で作成する

リソースへのアクセスを必要とするアプリやスクリプトがある場合は、アプリの ID を設定し、アプリを独自の資格情報で認証できます。 この ID は、サービス プリンシパルと呼ばれます。 このアプローチを使用すると、以下のことを実行できます。

* ユーザー自身のアクセス許可とは異なるアクセス許可を、アプリケーション ID に割り当てることができます。 通常、こうしたアクセス許可は、アプリが行う必要があることに制限されます。
* 無人スクリプトを実行するときに、証明書を使用して認証できます。

このトピックでは、アプリケーションをその独自の資格情報と ID で実行させるために必要な設定をすべて [Azure PowerShell](/powershell/azure/overview) で行う方法を紹介しています。

## <a name="required-permissions"></a>必要なアクセス許可
このトピックを完了するには、Azure Active Directory と Azure サブスクリプションの両方で適切なアクセス許可を持っている必要があります。 具体的には、Azure Active Directory でアプリケーションを作成し、ロールにサービス プリンシパルを割り当てることができる必要があります。 

自分のアカウントに適切なアクセス許可があるかどうかを確認する最も簡単な方法は、ポータルを使用することです。 [必要なアクセス許可のチェック](resource-group-create-service-principal-portal.md#required-permissions)に関するページを参照してください。

以下の認証を行うためのセクションに進みます。

* [password](#create-service-principal-with-password)
* [自己署名証明書](#create-service-principal-with-self-signed-certificate)
* [証明機関から証明書](#create-service-principal-with-certificate-from-certificate-authority)

## <a name="powershell-commands"></a>PowerShell コマンド

サービス プリンシパルを設定するには、次を使用します。

| コマンド | Description |
| ------- | ----------- | 
| [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) | Azure Active Directory サービス プリンシパルを作成します。 |
| [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) | 指定したプリンシパルに、指定のスコープで、指定した RBAC ロールを割り当てます。 |


## <a name="create-service-principal-with-password"></a>パスワードを使用したサービス プリンシパルの作成

共同作業者ロールでサブスクリプションにサービス プリンシパルを作成するには、次を使用します。 

```powershell
Login-AzureRmAccount
$sp = New-AzureRmADServicePrincipal -DisplayName exampleapp -Password "{provide-password}"
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

この例では、新しいサービス プリンシパルが Azure Active Directory 全体に反映されるまでの時間を設けるために、20 秒間スリープします。 スクリプトの待機時間が不足している場合、"PrincipalNotFound: プリンシパル {ID} がディレクトリにありません" というエラーが表示されます。

次のスクリプトでは、既定のサブスクリプション以外のスコープを指定することができ、エラーが発生した場合は、ロールの割り当てを再試行します。

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $Password
)

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.Id
 }
 else
 {
    Set-AzureRmContext -SubscriptionId $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 
 # Create Service Principal for the AD app
 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
```

このスクリプトには、次の注意事項があります。

* 既定のサブスクリプションに ID アクセスを許可する場合は、ResourceGroup パラメーターも SubscriptionId パラメーターも指定する必要はありません。
* ResourceGroup パラメーターはロールの割り当てスコープを特定のリソース グループに制限する場合にのみ指定します。
*  この例では、共同作成者ロールにサービス プリンシパルを追加します。 その他のロールについては、「[RBAC: 組み込みのロール](../active-directory/role-based-access-built-in-roles.md)」を参照してください。
* スクリプトは、新しいサービス プリンシパルが Azure Active Directory 全体に反映されるまでの時間を設けるために、15 秒間スリープします。 スクリプトの待機時間が不足している場合、"PrincipalNotFound: プリンシパル {ID} がディレクトリにありません" というエラーが表示されます。
* サービス プリンシパルのアクセスを複数のサブスクリプションまたはリソース グループに許可する必要がある場合は、異なるスコープを指定して `New-AzureRMRoleAssignment` コマンドレットを再び実行します。


### <a name="provide-credentials-through-powershell"></a>PowerShell を使用して資格情報を渡す
次に、操作を実行するアプリケーションとしてログインする必要があります。 ユーザー名には、アプリケーションのために作成した `ApplicationId` を使用します。 パスワードには、アカウントの作成時に指定したものを使用します。 

```powershell   
$creds = Get-Credential
Login-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId {tenant-ID}
```

テナント ID は機密情報ではないため、スクリプトに直接埋め込むことができます。 テナント ID を取得する必要がある場合は、次のコマンドを使用します。

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

## <a name="create-service-principal-with-self-signed-certificate"></a>自己署名証明書を使用したサービス プリンシパルの作成

自己署名証明書と共同作業者ロールを使用してサブスクリプションにサービス プリンシパルを作成するには、次を使用します。 

```powershell
Login-AzureRmAccount
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleappScriptCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzureRMADServicePrincipal -DisplayName exampleapp -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

この例では、新しいサービス プリンシパルが Azure Active Directory 全体に反映されるまでの時間を設けるために、20 秒間スリープします。 スクリプトの待機時間が不足している場合、"PrincipalNotFound: プリンシパル {ID} がディレクトリにありません" というエラーが表示されます。

次のスクリプトでは、既定のサブスクリプション以外のスコープを指定することができ、エラーが発生した場合は、ロールの割り当てを再試行します。 Windows 10 または Windows Server 2016 上の Azure PowerShell 2.0 が必要です。

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.Id
 }
 else
 {
    Set-AzureRmContext -SubscriptionId $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleappScriptCert" -KeySpec KeyExchange
 $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
```

このスクリプトには、次の注意事項があります。

* 既定のサブスクリプションに ID アクセスを許可する場合は、ResourceGroup パラメーターも SubscriptionId パラメーターも指定する必要はありません。
* ResourceGroup パラメーターはロールの割り当てスコープを特定のリソース グループに制限する場合にのみ指定します。
* この例では、共同作成者ロールにサービス プリンシパルを追加します。 その他のロールについては、「[RBAC: 組み込みのロール](../active-directory/role-based-access-built-in-roles.md)」を参照してください。
* スクリプトは、新しいサービス プリンシパルが Azure Active Directory 全体に反映されるまでの時間を設けるために、15 秒間スリープします。 スクリプトの待機時間が不足している場合、"PrincipalNotFound: プリンシパル {ID} がディレクトリにありません" というエラーが表示されます。
* サービス プリンシパルのアクセスを複数のサブスクリプションまたはリソース グループに許可する必要がある場合は、異なるスコープを指定して `New-AzureRMRoleAssignment` コマンドレットを再び実行します。

**Windows 10 または Windows Server 2016 Technical Preview をお使いでない**場合は、Microsoft スクリプト センターから [Self-signed certificate generator](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) スクリプトをダウンロードします。 ダウンロードしたファイルを展開し、必要なコマンドレットをインポートします。

```powershell  
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```
  
このスクリプトでは、証明書を生成するために次の 2 行を置き換えます。
  
```powershell
New-SelfSignedCertificateEx  -StoreLocation CurrentUser -StoreName My -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>自動化された PowerShell スクリプトから証明書を渡す
サービス プリンシパルとしてサインインするときは常に、AD アプリのディレクトリのテナント ID を指定する必要があります。 テナントは、Azure Active Directory のインスタンスです。 所有するサブスクリプションが 1 つのみである場合は、次のコマンドを使用できます。

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertSubject,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -match $CertSubject }).Thumbprint
 Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $Thumbprint -ApplicationId $ApplicationId -TenantId $TenantId
```

アプリケーション ID とテナント ID は機密情報ではないため、スクリプトに直接埋め込むことができます。 テナント ID を取得する必要がある場合は、次のコマンドを使用します。

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

アプリケーション ID を取得する必要がある場合は、次のコマンドを使用します。

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>証明機関の証明書を使用したサービス プリンシパルの作成
証明機関から発行された証明書を使用してサービス プリンシパルを作成するには、次のスクリプトを使用します。

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources
 Set-AzureRmContext -SubscriptionId $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzureRmADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

このスクリプトには、次の注意事項があります。

* アクセスのスコープは、サブスクリプションに制限されます。
* この例では、共同作成者ロールにサービス プリンシパルを追加します。 その他のロールについては、「[RBAC: 組み込みのロール](../active-directory/role-based-access-built-in-roles.md)」を参照してください。
* スクリプトは、新しいサービス プリンシパルが Azure Active Directory 全体に反映されるまでの時間を設けるために、15 秒間スリープします。 スクリプトの待機時間が不足している場合、"PrincipalNotFound: プリンシパル {ID} がディレクトリにありません" というエラーが表示されます。
* サービス プリンシパルのアクセスを複数のサブスクリプションまたはリソース グループに許可する必要がある場合は、異なるスコープを指定して `New-AzureRMRoleAssignment` コマンドレットを再び実行します。

### <a name="provide-certificate-through-automated-powershell-script"></a>自動化された PowerShell スクリプトから証明書を渡す
サービス プリンシパルとしてサインインするときは常に、AD アプリのディレクトリのテナント ID を指定する必要があります。 テナントは、Azure Active Directory のインスタンスです。

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $Thumbprint -ApplicationId $ApplicationId -TenantId $TenantId
```

アプリケーション ID とテナント ID は機密情報ではないため、スクリプトに直接埋め込むことができます。 テナント ID を取得する必要がある場合は、次のコマンドを使用します。

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

アプリケーション ID を取得する必要がある場合は、次のコマンドを使用します。

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>資格情報の変更

セキュリティ侵害の発生または資格情報の期限切れのために AD アプリの資格情報を変更するには、[Remove-AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/remove-azurermadappcredential) コマンドレットと [New-AzureRmADAppCredential](/powershell/module/azurerm.resources/new-azurermadappcredential) コマンドレットを使用します。

アプリケーションのすべての資格情報を削除するには、次のコマンドレットを使用します。

```powershell
Remove-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -All
```

パスワードを追加するには、次のコマンドレットを使用します。

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -Password p@ssword!
```

証明書の値を追加するには、このトピックの説明に従って、自己署名証明書を作成します。 その後、次のコマンドレットを使用します。

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
```

## <a name="save-access-token-to-simplify-log-in"></a>アクセス トークンを保存してログインを簡略化する
ログインの必要があるたびにサービス プリンシパルを指定しなくても済むように、アクセス トークンを保存することができます。

後のセッションで現在のアクセス トークンを使用するために、プロファイルを保存します。
   
```powershell
Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
```
   
プロファイルを開いて中身を確かめます。 アクセス トークンが含まれていることを確認します。 手動でもう一度ログインするのではなく、プロファイルを読み込むだけで済みます。
   
```powershell
Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
```

> [!NOTE]
> アクセス トークンには有効期限があるため、保存したプロファイルを使用できるのはトークンが有効である間のみです。
>  

また、PowerShell から REST 操作を呼び出して、ログインすることもできます。 認証の応答から、その他の操作で使用するためのアクセス トークンを取得できます。 REST 操作を呼び出してアクセス トークンを取得する例については、「[Generating an Access Token (アクセス トークンの生成)](resource-manager-rest-api.md#generating-an-access-token)」を参照してください。

## <a name="debug"></a>デバッグ

サービス プリンシパルの作成時に、以下のエラーが発生することがあります。

* **"Authentication_Unauthorized"** または **"コンテキストにサブスクリプトが見つかりません"** - アカウントが Azure Active Directory でアプリを登録するために[必要なアクセス許可](#required-permissions)を持っていない場合に、このエラーが表示されます。 通常は、Azure Active Directory の管理者ユーザーのみがアプリを登録できるときに、自分のアカウントが管理者でない場合に、このエラーが発生します。管理者に連絡して、自分を管理者ロールに割り当ててもらうか、ユーザーがアプリケーションを登録できるようにしてもらいます。

* アカウントに**「'/subscriptions/{guid} ' をスコープとした 'Microsoft.Authorization/roleAssignments/write' のアクションを実行するためのアクセス権限がありません」:**このエラーは、自分のアカウントが ID にロールを割り当てるのに十分なアクセス許可を持っていない場合に表示されます。 サブスクリプション管理者に連絡して、自分をユーザー アクセス管理者ロールに追加してもらいます。

## <a name="sample-applications"></a>サンプル アプリケーション
さまざまなプラットフォームからアプリケーションとしてログインする方法については、以下を参照してください。

* [.NET](/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)
* [Java](/java/azure/java-sdk-azure-authenticate)
* [Node.JS](/nodejs/azure/node-sdk-azure-get-started?view=azure-node-2.0.0)
* [Python](/python/azure/python-sdk-azure-authenticate?view=azure-python)
* [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>次のステップ
* アプリケーションを Azure に統合してリソースを管理する詳しい手順については、「 [Azure Resource Manager API を使用した承認の開発者ガイド](resource-manager-api-authentication.md)」を参照してください。
* アプリケーションとサービス プリンシパルの詳細については、「[アプリケーションおよびサービス プリンシパル オブジェクト](../active-directory/active-directory-application-objects.md)」を参照してください。 
* Azure Active Directory 認証の詳細については、「[Azure AD の認証シナリオ](../active-directory/active-directory-authentication-scenarios.md)」をご覧ください。
* ユーザーに対して許可または拒否される場合がある使用可能なアクションの一覧については、「[Azure Resource Manager のリソース プロバイダー操作](../active-directory/role-based-access-control-resource-provider-operations.md)」を参照してください。


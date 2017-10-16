---
title: "アプリケーションに Azure Stack Key Vault のシークレットの取得を許可する | Microsoft Docs"
description: "サンプル アプリを使用して Azure Stack Key Vault を操作する"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/26/2017
ms.author: sngun
ms.openlocfilehash: 7cfb78cc5219d4adab5ceddc9d7eb8d1fc71b678
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>Key Vault に格納されているキーとシークレットを使用するサンプル アプリケーション

この記事では、Azure Stack のキー コンテナーからキーとシークレットを取得するサンプル アプリケーション (HelloKeyVault) を実行する方法を説明します。

## <a name="prerequisites"></a>前提条件 

[開発キット](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)から、または [VPN 経由で接続](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)している場合は Windows ベースの外部クライアントから、次の前提条件を実行します。

* [Azure Stack と互換性のある Azure PowerShell モジュール](azure-stack-powershell-install.md)をインストールします。  
* [Azure Stack を操作するために必要なツール](azure-stack-powershell-download.md)をダウンロードします。 

## <a name="create-and-get-the-key-vault-and-application-settings"></a>キー コンテナーの作成と取得およびアプリケーションの設定

まず、Azure Stack でキー コンテナーを作成し、Azure Active Directory (Azure AD) にアプリケーションを登録する必要があります。 キー コンテナーを作成および登録するには、Azure Portal または PowerShell を使用します。 この記事では、PowerShell を使ってこの操作を実行する方法について説明します。 この PowerShell スクリプトを実行すると、既定で Active Directory に新しいアプリケーションが作成されます。 ただし、既存のアプリケーションのいずれかを使用することもできます。 変数 `aadTenantName` と `applicationPassword` の値は必ず指定してください。 変数 `applicationPassword` の値を指定しないと、このスクリプトによりランダムなパスワードが生成されます。 

```powershell
$vaultName           = 'myVault'
$resourceGroupName   = 'myResourceGroup'
$applicationName     = 'myApp'
$location            = 'local' 

# Password for the application. If not specified, this script will generate a random password during app creation.
$applicationPassword = '' 
                         
# Function to generate a random password for the application.
Function GenerateSymmetricKey()
{
    $key = New-Object byte[](32)
    $rng = [System.Security.Cryptography.RNGCryptoServiceProvider]::Create()
    $rng.GetBytes($key)
    return [System.Convert]::ToBase64String($key)
}

Write-Host 'Please log into your Azure Stack user environment' -foregroundcolor Green

$tenantARM = "https://management.local.azurestack.external"
$aadTenantName = "PLEASE FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

# Configure the Azure Stack operator’s PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackUser" `
  -ArmEndpoint $tenantARM

Set-AzureRmEnvironment `
  -Name "AzureStackAdmin" `
  -GraphAudience "https://graph.windows.net/"

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $aadTenantName `
  -EnvironmentName AzureStackUser

# Sign in to the user portal.
Login-AzureRmAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `
  
$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey
    
# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "http://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzureRmADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzureRmADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault within that resource group.
New-AzureRmResourceGroup `
  -Name $resourceGroupName `
  -Location $location   

Write-Host "Creating vault $vaultName"
$vault = New-AzureRmKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host

``` 

次のスクリーンショットは、上のスクリプトの出力を示しています。

![アプリの構成](media/azure-stack-kv-sample-app/settingsoutput.png)

上のスクリプトによって返される **VaultUrl**、**AuthClientId**、**AuthClientSecret** の値をメモしておきます。 これらの値は、HelloKeyVault アプリケーションを実行するために使用します。

## <a name="download-and-run-the-sample-application"></a>サンプル アプリケーションのダウンロードと実行

「[Azure Key Vault client samples (Azure Key Vault クライアントのサンプル)](https://www.microsoft.com/en-us/download/details.aspx?id=45343)」ページから、キー コンテナーのサンプルをダウンロードします。 .zip ファイルの内容を自分の開発ワークステーションに抽出します。 samples フォルダーには 2 つのサンプルがあります。 このトピックでは、HellpKeyVault サンプルを使用します。 **Microsoft.Azure.KeyVault.Samples** > **samples** > **HelloKeyVault** フォルダーを参照し、HelloKeyVault アプリケーションを Visual Studio で開きます。 

HelloKeyVault\App.config ファイルを開き、<appSettings> 要素の値を先ほどのスクリプトによって返された **VaultUrl**、**AuthClientId**、**AuthClientSecret** の値に置き換えます。 App.config には既定で *AuthCertThumbprint* のプレースホルダーが含まれていますが、代わりに *AuthClientSecret* を使用することに注意してください。 設定を置き換えたら、ソリューションをリビルドし、アプリケーションを起動します。

![アプリケーション設定](media/azure-stack-kv-sample-app/appconfig.png)
 
アプリケーションは、Azure AD にサインインし、そのトークンを使用して Azure Stack でキー コンテナーを認証します。 アプリケーションは、キー コンテナーのキーとシークレットで作成、暗号化、ラップ、削除などの操作を実行します。 *encrypt*、*decrypt* などの特定のパラメーターをアプリケーションに渡して、アプリケーションがそのコンテナーに対して指定した操作のみを実行するようにすることもできます。 


## <a name="next-steps"></a>次のステップ
[Key Vault パスワードを使用して VM をデプロイする](azure-stack-kv-deploy-vm-with-secret.md)

[Key Vault 証明書を使って VM をデプロイする](azure-stack-kv-push-secret-into-vm.md)




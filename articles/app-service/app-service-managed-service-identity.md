---
title: "App Service および Azure Functions での管理対象のサービス ID | Microsoft Docs"
description: "Azure App Service および Azure Functions での管理対象のサービス ID の概念と設定に関するガイドです"
services: app-service
author: mattchenderson
manager: cfowler
editor: 
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/13/2017
ms.author: mahender
ms.translationtype: HT
ms.sourcegitcommit: a6bba6b3b924564fe7ae16fa1265dd4d93bd6b94
ms.openlocfilehash: fda9d6c12da382faed5312a677c533f24ffbd824
ms.contentlocale: ja-jp
ms.lasthandoff: 09/28/2017

---

# <a name="how-to-use-azure-managed-service-identity-public-preview-in-app-service-and-azure-functions"></a>App Service および Azure Functions で管理対象のサービス ID (パブリック プレビュー) を使用する方法

> [!NOTE] 
> 現在、App Service および Azure Functions のための管理対象のサービス ID はプレビュー段階です。

このトピックでは、App Service および Azure Functions アプリケーション用の管理対象アプリ ID を作成し、それを使って他のリソースにアクセスする方法を説明します。 アプリで Azure Active Directory の管理対象のサービス ID を使うと、他の AAD で保護されたリソース (Azure Key Vault など) に簡単にアクセスできます。 ID は Azure プラットフォームによって管理され、ユーザーがシークレットをプロビジョニングまたはローテーションする必要はありません。 管理対象のサービス ID について詳しくは、「[Managed Service Identity overview](../active-directory/msi-overview.md)」(管理対象のサービス ID の概要) をご覧ください。

## <a name="creating-an-app-with-an-identity"></a>ID を持つアプリの作成

ID を持つアプリを作成するには、アプリケーションで追加のプロパティを設定する必要があります。

### <a name="using-the-azure-portal"></a>Azure ポータルの使用

ポータルで管理対象サービス ID を設定するには、最初に通常の方法でアプリケーションを作成した後、機能を有効にします。

1. ポータルを使って通常の方法でアプリを作成します。 ポータルでアプリに移動します。

2. 関数アプリを使っている場合は、**[プラットフォーム機能]** に移動します。 他のアプリの種類の場合は、左側のナビゲーションを下にスクロールして **[設定]** グループに移動します。

3. **[Managed service identity]\(管理対象のサービス ID\)** を選びます。

4. **[Register with Azure Active Directory]\(Azure Active Directory に登録\)** を **[オン]** に切り替えます。 [ **Save**] をクリックします。

![App Service での管理対象のサービス ID](media/app-service-managed-service-identity/msi-blade.png)

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用

Azure Resource Manager テンプレートを使って、Azure リソースのデプロイを自動化できます。 App Service および Functions へのデプロイについて詳しくは、「[Automating resource deployment in App Service](../app-service/app-service-deploy-complex-application-predictably.md)」(App Service でのリソースのデプロイの自動化) および「[Azure Functions の関数アプリのリソース デプロイを自動化](../azure-functions/functions-infrastructure-as-code.md)」をご覧ください。

種類が `Microsoft.Web/sites` であるすべてのリソースは、リソース定義に次のプロパティを含めることにより、ID を使って作成できます。
```json
"identity": {
    "type": "SystemAssigned"
}    
```

これは、Azure に対してアプリケーション用の ID を作成して管理するように指示します。

たとえば、Web アプリは次のようになります。
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

作成されるサイトには、次の追加プロパティが設定されます。
```json
"identity": {
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>` と `<PRINCIPALID>` は GUID に置き換えられます。 tenantId プロパティは、アプリケーションが属している AAD テナントを示します。 principalId は、アプリケーションの新しい ID の一意識別子です。 AAD でのアプリケーションの名前は、App Service または Azure Functions のインスタンスに指定したものと同じです。

## <a name="obtaining-tokens-for-azure-resources"></a>Azure リソースのトークンの取得

アプリは、その ID を使って、AAD で保護されている他のリソース (Azure Key Vault など) へのトークンを取得できます。 これらのトークンは、アプリケーションの特定のユーザーではなく、リソースにアクセスしているアプリケーションを表します。 

> [!IMPORTANT]
> アプリケーションからのアクセスを許可するように、対象のリソースを構成することが必要な場合があります。 たとえば、Key Vault に対するトークンを要求する場合、アプリケーションの ID を含むアクセス ポリシーを追加する必要があります。 追加しないと、トークンを含めた場合でも、Key Vault の呼び出しは拒否されます。 管理対象のサービス ID のトークンをサポートしているリソースについて詳しくは、「[Azure AD 認証をサポートしている Azure サービス](../active-directory/msi-overview.md#which-azure-services-support-managed-service-identity)」をご覧ください。

App Service と Azure Functions には、トークンを取得するための簡単な REST プロトコルがあります。 .NET アプリケーションの場合は、Microsoft.Azure.Services.AppAuthentication ライブラリがこのプロトコルの抽象化を提供し、ローカル開発エクスペリエンスをサポートします。

### <a name="asal"></a>.NET 用の Microsoft.Azure.Services.AppAuthentication ライブラリの使用

.NET アプリケーションと Functions の場合、管理対象のサービス ID を使う最も簡単な方法は、Microsoft.Azure.Services.AppAuthentication パッケージを利用することです。 このライブラリを使うと、[Azure CLI 2.0](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest) または Active Directory 統合認証のユーザー アカウントを使って、開発用コンピューターでローカルにコードをテストすることもできます。 このセクションでは、ライブラリを使い始める方法を示します。

1. [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) および [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet パッケージに対する参照をアプリケーションに追加します。

2.  次のコードをアプリケーションに追加します。

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.KeyVault;
// ...
var azureServiceTokenProvider = new AzureServiceTokenProvider();
string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
// OR
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
```

Microsoft.Azure.Services.AppAuthentication およびそれによって公開される操作について詳しくは、「[App Service and KeyVault with MSI .NET sample](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)」(MSI .NET での App Service と KeyVault のサンプル) をご覧ください。

### <a name="using-the-rest-protocol"></a>REST プロトコルの使用

管理対象のサービス ID を使うアプリでは、2 つの環境変数を定義します。
- MSI_ENDPOINT
- MSI_SECRET

**MSI_ENDPOINT** は、アプリがトークンを要求できるローカル URL です。 リソースのトークンを取得するには、次のパラメーターを指定して、このエンドポイントに HTTP GET 要求を行います。

> [!div class="mx-tdBreakAll"]
> |パラメーター名|イン|Description|
> |-----|-----|-----|
> |resource|クエリ|トークンを取得する必要のあるリソースの AAD リソース URI。|
> |api-version|クエリ|使うトークン API のバージョン。 現在サポートされているバージョンは "2017-09-01" だけです。|
> |secret|ヘッダー|MSI_SECRET 環境変数の値。|


正常終了の応答である 200 OK には、JSON 本文と次のプロパティが含まれています。

> [!div class="mx-tdBreakAll"]
> |プロパティ名|Description|
> |-------------|----------|
> |access_token|要求されたアクセス トークン。 呼び出し元の Web サービスは、このトークンを使用して受信側の Web サービスに対する認証処理を行うことができます。|
> |expires_on|アクセス トークンの有効期限が切れる日時。 日時は 1970-01-01T0:0:0Z UTC から期限切れ日時までの秒数として表されます。 この値は、キャッシュされたトークンの有効期間を調べるために使用されます。|
> |resource|受信側の Web サービスのアプリケーション ID URI。|
> |token_type|トークン タイプ値を指定します。 Azure AD でサポートされるのは Bearer タイプのみです。 ベアラー トークンについて詳しくは、「[OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt)」(OAuth 2.0 承認フレームワーク: ベアラー トークンの使用法 (RFC 6750)) をご覧ください。|


この応答は、[AAD のサービス間アクセス トークン要求に対する応答](../active-directory/develop/active-directory-protocols-oauth-service-to-service.md#service-to-service-access-token-response)と同じです。

### <a name="rest-protocol-examples"></a>REST プロトコルの例
要求の例を次に示します。
```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```
応答の例は次のようになります。
```
HTTP/1.1 200 OK
Content-Type: application/json
 
{
    "access_token": "eyJ0eXAi…",
    "expires_on": "09/14/2017 00:00:00 PM +00:00",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer"
}
```

### <a name="code-examples"></a>コード例
この要求を C# で行うには:
```csharp
public static async Task<HttpResponseMessage> GetToken(string resource, string apiversion)  {
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await client.GetAsync(String.Format("{0}/?resource={1}&api-version={2}", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource, apiversion));
}
```
> [!TIP]
> .NET 言語では、この要求を自作する代わりに、[Microsoft.Azure.Services.AppAuthentication](#asal) を使うこともできます。

Node.JS の例:
```javascript
const rp = require('request-promise');
const getToken = function(resource, apiver, cb) {
    var options = {
        uri: `${process.env["MSI_ENDPOINT"]}/?resource=${resource}&api-version=${apiver}`,
        headers: {
            'Secret': process.env["MSI_SECRET"]
        }
    };
    rp(options)
        .then(cb);
}
```

PowerShell では次のとおりです。
```powershell
$apiVersion = "2017-09-01"
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=$apiVersion"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```


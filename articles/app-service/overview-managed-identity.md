---
title: マネージド ID 概要 - Azure App Service | Microsoft Docs
description: Azure App Service と Azure Functions でのマネージド ID の概念と設定に関するガイドです
services: app-service
author: mattchenderson
manager: cfowler
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/15/2019
ms.author: mahender
ms.reviewer: yevbronsh
ms.openlocfilehash: 16c65a98ca420a4b15281ee033ea7773197b5b2a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098470"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>App Service と Azure Functions でマネージド ID を使用する方法

> [!Important] 
> アプリがサブスクリプションやテナント間で移行された場合、App Service と Azure Functions でのマネージド ID は想定されたとおりに動作しません。 アプリでは、機能を無効にしてから再度有効にすることで、新しい ID を取得する必要があります。 以下の「[ID の削除](#remove)」を参照してください。 また、ダウンストリーム リソースでは、新しい ID を使用するようにアクセス ポリシーを更新する必要があります。

このトピックでは、App Service と Azure Functions アプリケーションでマネージド ID を作成し、それを使用して他のリソースにアクセスする方法を説明します。 アプリで Azure Active Directory のマネージド ID を使用すると、他の AAD で保護されたリソース (Azure Key Vault など) に簡単にアクセスできます。 ID は Azure プラットフォームによって管理され、ユーザーがシークレットをプロビジョニングまたはローテーションする必要はありません。 AAD のマネージド ID については、「[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。

アプリケーションには 2 種類の ID を付与できます。 
- **システム割り当て ID** はアプリケーションに関連付けられているため、アプリが削除されると削除されます。 アプリは 1 つのシステム割り当て ID しか持つことはできません。
- **ユーザー割り当て ID** は、アプリに割り当てることができるスタンドアロン Azure リソースです。 アプリは複数のユーザー割り当て ID を持つことができます。

## <a name="adding-a-system-assigned-identity"></a>システム割り当て ID の追加

システム割り当て ID を持つアプリを作成するには、アプリケーションで追加のプロパティを設定する必要があります。

### <a name="using-the-azure-portal"></a>Azure ポータルの使用

ポータルでマネージド ID を設定するには、最初に通常の方法でアプリケーションを作成した後、機能を有効にします。

1. ポータルを使って通常の方法でアプリを作成します。 ポータルでアプリに移動します。

2. 関数アプリを使っている場合は、 **[プラットフォーム機能]** に移動します。 他のアプリの種類の場合は、左側のナビゲーションを下にスクロールして **[設定]** グループに移動します。

3. **[マネージド ID]** を選択します。

4. **[システム割り当て済み]** タブで、 **[状態]** を **[オン]** に切り替えます。 **[Save]** をクリックします。

![App Service のマネージド ID](media/app-service-managed-service-identity/msi-blade-system.png)

### <a name="using-the-azure-cli"></a>Azure CLI の使用

Azure CLI を使用してマネージド ID を設定するには、既存のアプリケーションに対して `az webapp identity assign` コマンドを使用する必要があります。 このセクションの例を実行するためのオプションとして次の 3 つがあります。

- Azure Portal から [Azure Cloud Shell](../cloud-shell/overview.md) を使用する。
- 以下の各コード ブロックの右上隅にある [試してみる] を利用して、埋め込まれた Azure Cloud Shell シェルを使用します。
- ローカル CLI コンソールを使用する場合、[Azure CLI の最新バージョン (2.0.31 以降) をインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)します。 

次の手順では、CLI を使用して、Web アプリを作成し、ID を割り当てる方法について説明します。

1. ローカルのコンソールで Azure CLI を使用している場合は、最初に [az login](/cli/azure/reference-index#az-login) を使用して Azure にサインインします。 次のように、アプリケーションをデプロイする Azure サブスクリプションに関連付けられているアカウントを使用します。

    ```azurecli-interactive
    az login
    ```
2. CLI を使用して Web アプリケーションを作成します。 App Service で CLI を使用する方法の他の例については、[App Service の CLI のサンプル](../app-service/samples-cli.md)に関するページを参照してください。

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. `identity assign` コマンドを実行してこのアプリケーションの ID を作成します。

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Azure PowerShell の使用

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

次の手順では、Azure PowerShell を使用して、Web アプリを作成し、ID を割り当てる方法について説明します。

1. 必要に応じて、[Azure PowerShell ガイド](/powershell/azure/overview)の手順に従って Azure PowerShell をインストールし、`Login-AzAccount` を実行して、Azure との接続を作成します。

2. Azure PowerShell を使用して Web アプリケーションを作成します。 App Service で Azure PowerShell を使用する方法の他の例については、[App Service の PowerShell のサンプル](../app-service/samples-powershell.md)に関するページを参照してください。

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name myResourceGroup -Location $location
    
    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free
    
    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. `Set-AzWebApp -AssignIdentity` コマンドを実行してこのアプリケーションの ID を作成します。

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用

Azure Resource Manager テンプレートを使って、Azure リソースのデプロイを自動化できます。 App Service および Functions へのデプロイについて詳しくは、「[Automating resource deployment in App Service](../app-service/deploy-complex-application-predictably.md)」(App Service でのリソースのデプロイの自動化) および「[Azure Functions の関数アプリのリソース デプロイを自動化](../azure-functions/functions-infrastructure-as-code.md)」をご覧ください。

種類が `Microsoft.Web/sites` であるすべてのリソースは、リソース定義に次のプロパティを含めることにより、ID を使って作成できます。
```json
"identity": {
    "type": "SystemAssigned"
}    
```

> [!NOTE] 
> アプリケーションは、システム割り当て ID とユーザー割り当て ID の両方を同時に持つことができます。 この場合、`type` プロパティは `SystemAssigned,UserAssigned` になります

システム割り当てタイプを追加すると、Azure に対してアプリケーション用の ID を作成して管理するように指示されます。

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
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>` と `<PRINCIPALID>` は GUID に置き換えられます。 tenantId プロパティは、ID が属している AAD テナントを示します。 principalId は、アプリケーションの新しい ID の一意識別子です。 AAD でのサービス プリンシパルの名前は、App Service または Azure Functions のインスタンスに指定したものと同じです。


## <a name="adding-a-user-assigned-identity"></a>ユーザー割り当て ID の追加

ユーザー割り当て ID を持つアプリを作成するには、ID を作成してから、そのリソース ID をアプリ構成に追加する必要があります。

### <a name="using-the-azure-portal"></a>Azure ポータルの使用

最初に、ユーザー割り当て ID リソースを作成する必要があります。

1. [以下の手順](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)に従って、ユーザー割り当てマネージド ID リソースを作成します。

2. ポータルを使って通常の方法でアプリを作成します。 ポータルでアプリに移動します。

3. 関数アプリを使っている場合は、 **[プラットフォーム機能]** に移動します。 他のアプリの種類の場合は、左側のナビゲーションを下にスクロールして **[設定]** グループに移動します。

4. **[マネージド ID]** を選択します。

5. **[ユーザー割り当て済み]** タブで **[追加]** をクリックします。

6. 先ほど作成した ID を検索して選択します。 **[追加]** をクリックします。

![App Service のマネージド ID](media/app-service-managed-service-identity/msi-blade-user.png)

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用

Azure Resource Manager テンプレートを使って、Azure リソースのデプロイを自動化できます。 App Service および Functions へのデプロイについて詳しくは、「[Automating resource deployment in App Service](../app-service/deploy-complex-application-predictably.md)」(App Service でのリソースのデプロイの自動化) および「[Azure Functions の関数アプリのリソース デプロイを自動化](../azure-functions/functions-infrastructure-as-code.md)」をご覧ください。

種類が `Microsoft.Web/sites` であるリソースは、リソース定義に次のブロックを含めて、`<RESOURCEID>` を目的の ID のリソース ID と置き換えることで、ID を使って作成できます。
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}    
```

> [!NOTE] 
> アプリケーションは、システム割り当て ID とユーザー割り当て ID の両方を同時に持つことができます。 この場合、`type` プロパティは `SystemAssigned,UserAssigned` になります

ユーザー割り当てタイプを追加すると、Azure に対してアプリケーション用の ID を作成して管理するように指示されます。

たとえば、Web アプリは次のようになります。
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

作成されるサイトには、次の追加プロパティが設定されます。
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

`<PRINCIPALID>` と `<CLIENTID>` は GUID に置き換えられます。 principalId は、AAD の管理に使用される ID の一意識別子です。 clientId は、ランタイム呼び出し中に使用する ID を指定するために使用されるアプリケーションの新しい ID の一意識別子です。


## <a name="obtaining-tokens-for-azure-resources"></a>Azure リソースのトークンの取得

アプリは、その ID を使って、AAD で保護されている他のリソース (Azure Key Vault など) へのトークンを取得できます。 これらのトークンは、アプリケーションの特定のユーザーではなく、リソースにアクセスしているアプリケーションを表します。 

> [!IMPORTANT]
> アプリケーションからのアクセスを許可するように、対象のリソースを構成することが必要な場合があります。 たとえば、Key Vault に対するトークンを要求する場合、アプリケーションの ID を含むアクセス ポリシーを追加する必要があります。 追加しないと、トークンを含めた場合でも、Key Vault の呼び出しは拒否されます。 Azure Active Directory トークンをサポートしているリソースの詳細については、「[Azure AD 認証をサポートしている Azure サービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)」をご覧ください。

App Service と Azure Functions には、トークンを取得するための簡単な REST プロトコルがあります。 .NET アプリケーションの場合は、Microsoft.Azure.Services.AppAuthentication ライブラリがこのプロトコルの抽象化を提供し、ローカル開発エクスペリエンスをサポートします。

### <a name="asal"></a>.NET 用の Microsoft.Azure.Services.AppAuthentication ライブラリの使用

.NET アプリケーションと Functions の場合、マネージド ID を使用する最も簡単な方法は、Microsoft.Azure.Services.AppAuthentication パッケージを利用することです。 このライブラリを使うと、Visual Studio、[Azure CLI](/cli/azure)、または Active Directory 統合認証のユーザー アカウントを使って、開発用コンピューターでローカルにコードをテストすることもできます。 このライブラリでのローカル開発オプションについて詳しくは、[Microsoft.Azure.Services.AppAuthentication のリファレンス]に関するページをご覧ください。 このセクションでは、コードでライブラリを使い始める方法を示します。

1. [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) とその他の必要な NuGet パッケージに対する参照をアプリケーションに追加します。 下の例では [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) も使用されています。

2. 次のコードをアプリケーションに追加し、正しいリソースが対象となるように変更します。 この例では、Azure Key Vault と連携動作するための 2 つの方法が示されています。

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.KeyVault;
// ...
var azureServiceTokenProvider = new AzureServiceTokenProvider();
string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
// OR
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
```

Microsoft.Azure.Services.AppAuthentication およびそれによって公開される操作について詳しくは、[Microsoft.Azure.Services.AppAuthentication のリファレンス]に関するページおよび「[App Service and KeyVault with MSI .NET sample](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)」(MSI .NET での App Service と KeyVault のサンプル) をご覧ください。


### <a name="using-the-azure-sdk-for-java"></a>Azure SDK for Java を使用する

Java のアプリケーションと関数の場合、マネージド ID を利用する最も簡単な方法は、[Azure SDK for Java](https://github.com/Azure/azure-sdk-for-java) を経由する方法です。 このセクションでは、コードでライブラリを使い始める方法を示します。

1. [Azure SDK ライブラリ](https://mvnrepository.com/artifact/com.microsoft.azure/azure)の参照を追加します。 Maven プロジェクトの場合、プロジェクトの POM ファイルの `dependencies` セクションにこのスニペットを追加できます。

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure</artifactId>
    <version>1.23.0</version>
</dependency>
```

2. 認証には `AppServiceMSICredentials` オブジェクトを使用します。 この例からは、このメカニズムを使用して Azure Key Vault を操作する方法がわかります。

```java
import com.microsoft.azure.AzureEnvironment;
import com.microsoft.azure.management.Azure;
import com.microsoft.azure.management.keyvault.Vault
//...
Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
        .withSubscription(subscriptionId);
Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

```

### <a name="using-the-rest-protocol"></a>REST プロトコルの使用

マネージド ID を使用するアプリでは、2 つの環境変数を定義します。

- MSI_ENDPOINT - ローカル トークン サービスに対する URL。
- MSI_SECRET - サーバー側のリクエスト フォージェリ (SSRF) 攻撃を回避するために使用されるヘッダー。 値は、プラットフォームによってローテーションされます。

**MSI_ENDPOINT** は、アプリがトークンを要求できるローカル URL です。 リソースのトークンを取得するには、次のパラメーターを指定して、このエンドポイントに HTTP GET 要求を行います。

> |パラメーター名|場所|説明|
> |-----|-----|-----|
> |resource|Query|トークンを取得する必要のあるリソースの AAD リソース URI。 これは [Azure AD 認証をサポートしている Azure サービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)の 1 つか、その他のリソース URI になります。|
> |api-version|Query|使うトークン API のバージョン。 現在サポートされているバージョンは "2017-09-01" だけです。|
> |secret|ヘッダー|MSI_SECRET 環境変数の値。 このヘッダーは、サーバー側のリクエスト フォージェリ (SSRF) 攻撃を回避するために使用されます。|
> |clientid|Query|(省略可能) 使用するユーザー割り当て ID のID。 省略すると、システム割り当て ID が使用されます。|

正常終了の応答である 200 OK には、JSON 本文と次のプロパティが含まれています。

> |プロパティ名|説明|
> |-------------|----------|
> |access_token|要求されたアクセス トークン。 呼び出し元の Web サービスは、このトークンを使用して受信側の Web サービスに対する認証処理を行うことができます。|
> |expires_on|アクセス トークンの有効期限が切れる日時。 日時は 1970-01-01T0:0:0Z UTC から期限切れ日時までの秒数として表されます。 この値は、キャッシュされたトークンの有効期間を調べるために使用されます。|
> |resource|受信側の Web サービスのアプリケーション ID URI。|
> |token_type|トークン タイプ値を指定します。 Azure AD でサポートされるのは Bearer タイプのみです。 ベアラー トークンの詳細については、「[OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)」(OAuth 2.0 承認フレームワーク: ベアラー トークンの使用法 (RFC 6750)) をご覧ください。|

この応答は、[AAD のサービス間アクセス トークン要求に対する応答](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response)と同じです。

> [!NOTE]
> 環境変数はプロセスが初めて開始されるときに設定されます。そのため、アプリケーションのマネージド ID を有効にした後、場合によってはアプリケーションを再起動するか、アプリケーションのコードを再デプロイしないと `MSI_ENDPOINT` と `MSI_SECRET` をコードで利用できません。

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

<a name="token-csharp"></a>この要求を C# で行うには:

```csharp
public static async Task<HttpResponseMessage> GetToken(string resource, string apiversion)  {
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await client.GetAsync(String.Format("{0}/?resource={1}&api-version={2}", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource, apiversion));
}
```

> [!TIP]
> .NET 言語では、この要求を自作する代わりに、[Microsoft.Azure.Services.AppAuthentication](#asal) を使うこともできます。

<a name="token-js"></a>Node.JS の例:

```javascript
const rp = require('request-promise');
const getToken = function(resource, apiver, cb) {
    let options = {
        uri: `${process.env["MSI_ENDPOINT"]}/?resource=${resource}&api-version=${apiver}`,
        headers: {
            'Secret': process.env["MSI_SECRET"]
        }
    };
    rp(options)
        .then(cb);
}
```

<a name="token-python"></a>Python の例:

```python
import os
import requests

msi_endpoint = os.environ["MSI_ENDPOINT"]
msi_secret = os.environ["MSI_SECRET"]

def get_bearer_token(resource_uri, token_api_version):
    token_auth_uri = f"{msi_endpoint}?resource={resource_uri}&api-version={token_api_version}"
    head_msi = {'Secret':msi_secret}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

<a name="token-powershell"></a>PowerShell の例:

```powershell
$apiVersion = "2017-09-01"
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=$apiVersion"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

## <a name="remove"></a>ID の削除

システム割り当て ID は、ポータル、PowerShell、または CLI を使用して、作成時と同じ方法で機能を無効にすることで、削除できます。 ユーザー割り当て ID は個別に削除することはできません。 すべての ID を削除するには、REST/ARM テンプレート プロトコルでは、種類を "なし" に設定することでこの削除を実行します。

```json
"identity": {
    "type": "None"
}
```

この方法でシステム割り当て ID を削除すると、AAD からも削除されます。 システム割り当て ID は、アプリ リソースが削除されると、AAD からも自動的に削除されます。

> [!NOTE]
> また、単純にローカル トークン サービスを無効にする、設定可能なアプリケーション設定 WEBSITE_DISABLE_MSI もあります。 ただし、ID はその場所に残り、ツールには引き続きマネージド ID が "オン" または "有効" と表示されます。 そのため、この設定の使用はお勧めしません。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [マネージド ID を使用して SQL データベースに安全にアクセスする](app-service-web-tutorial-connect-msi.md)

[Microsoft.Azure.Services.AppAuthentication のリファレンス]: https://go.microsoft.com/fwlink/p/?linkid=862452

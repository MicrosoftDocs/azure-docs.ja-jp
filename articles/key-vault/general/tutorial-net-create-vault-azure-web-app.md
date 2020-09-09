---
title: チュートリアル - .NET で Azure Web アプリを使用して Azure Key Vault を使用する | Microsoft Docs
description: このチュートリアルでは、キー コンテナーからシークレットを読み取るように ASP.NET Core アプリケーションを構成します。
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp
ms.openlocfilehash: be18718513e8624db00d502228a3e5af6076d9d7
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89007079"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-with-net"></a>チュートリアル:マネージド ID と .NET を使用して Key Vault を Azure Web アプリに接続する

Azure Key Vault は、資格情報やその他のシークレットを安全に保管する方法を提供しますが、コードで Key Vault に対して認証を行い、それらを取得する必要があります。 [Azure リソースのマネージド ID の概要](../../active-directory/managed-identities-azure-resources/overview.md)に関するページは、Azure AD で自動的に管理されているマネージド ID を Azure サービスに付与することで、この問題を解決するうえで役立ちます。 この ID を使用して、コードに資格情報が表示されていなくても、Key Vault を含む Azure AD の認証をサポートする任意のサービスに認証することができます。

このチュートリアルでは、マネージド ID と Azure Key Vault を使用して Azure Web アプリを認証します。 この手順では、[.NET 用 Azure Key Vault v4 クライアント ライブラリ](/dotnet/api/overview/azure/key-vault?view=azure-dotnet)と [Azure CLI](/cli/azure/get-started-with-azure-cli) を使用していますが、各種の開発言語や Azure PowerShell、Azure portal を使用している場合でも、基本的な原則は同じです。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [.Net Core 3.1 SDK 以降](https://dotnet.microsoft.com/download/dotnet-core/3.1)。
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) または [Azure PowerShell](/powershell/azure/)。

## <a name="create-a-resource-group"></a>リソース グループを作成する

リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 キー コンテナーと Web アプリの配置先となるリソース グループを [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) コマンドで作成します。

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>キー コンテナーを設定する

キー コンテナーを作成し、このチュートリアルで後から使用するシークレットをそこに格納します。

キー コンテナーを作成するには、[az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) コマンドを使用します。

> [!Important]
> 各キー コンテナーには一意の名前が必要です。 次の例の <your-keyvault-name> は、お使いのキー コンテナーの名前に置き換えてください。

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

"https://&lt;your-keyvault-name&gt;.vault.azure.net/" の形式で返される `vaultUri` を書き留めます。 「[コードを更新する](#update-the-code)」の手順で使用します。

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-net-web-app"></a>.NET Web アプリを作成する

### <a name="create-a-local-app"></a>ローカル アプリを作成する

コンピューターのターミナル ウィンドウで、`akvwebapp` という名前のディレクトリを作成し、現在のディレクトリをそのディレクトリに変更します。

```bash
mkdir akvwebapp
cd akvwebapp
```

次に、[dotnet new web](/dotnet/core/tools/dotnet-new) コマンドを使用して新しい .NET Core アプリを作成します。

```bash
dotnet new web
```

アプリケーションをローカルで実行すると、アプリケーションを Azure にデプロイするとどう表示されるかを把握できます。 

```bash
dotnet run
```

Web ブラウザーを開き、`http://localhost:5000` のアプリに移動します。

ページに表示されているサンプル アプリの **Hello World** メッセージが表示されます。

### <a name="initialize-the-git-repository"></a>Git リポジトリを初期化する

ターミナル ウィンドウで **Ctrl + C** キーを押して、Web サーバーを終了します。  .NET Core プロジェクト用の Git リポジトリを初期化します。

```bash
git init
git add .
git commit -m "first commit"
```

### <a name="configure-a-deployment-user"></a>デプロイ ユーザーを構成する

FTP およびローカルの Git では、"*デプロイ ユーザー*" を使用して Azure Web アプリにデプロイできます。 デプロイ ユーザーを構成すると、すべての Azure デプロイでこのユーザーを使用できます。 アカウントレベルのデプロイのユーザー名とパスワードは、Azure サブスクリプションの資格情報とは異なります。 

デプロイ ユーザーを構成するには、[az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) コマンドを実行します。 次のガイドラインに準拠したユーザー名とパスワードを選択してください。 

- ユーザー名は、Azure 内で一意である必要があり、ローカル Git プッシュの場合は "\@" シンボルを含めることはできません。 
- パスワードは長さが 8 文字以上で、文字、数字、記号のうち 2 つを含む必要があります。 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

JSON 出力には、パスワードが `null` として表示されます。 `'Conflict'. Details: 409` エラーが発生した場合は、ユーザー名を変更します。 `'Bad Request'. Details: 400` エラーが発生した場合は、より強力なパスワードを使用します。 

Web アプリのデプロイに使用するユーザー名とパスワードを記録します。

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

Azure CLI の [az appservice plan create](/cli/azure/appservice/plan?view=azure-cli-latest) コマンドで、App Service プランを作成します。 次の例では、**Free** 価格レベルの `myAppServicePlan` という名前の App Service プランを作成します。

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

App Service プランが作成されると、Azure CLI によって、次の例のような情報が表示されます。

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>


### <a name="create-a-remote-web-app"></a>リモート Web アプリを作成する

`myAppServicePlan` App Service プランに [Azure Web アプリ](../../app-service/overview.md#app-service-on-linux)を作成します。 

> [!Important]
> Key Vault と同様、Azure Web アプリにも一意の名前を付ける必要があります。 次の例の \<your-webapp-name\> は、実際の Web アプリの名前に置き換えてください。


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

Web アプリが作成されると、Azure CLI によって次の例のような出力が表示されます。

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;ayour-webapp-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;your-webapp-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;your-webapp-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>


Git リモートの URL は `deploymentLocalGitUrl` プロパティに `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git` 形式で出力されます。 この URL は後で必要になるので保存しておいてください。

新しく作成されたアプリに移動します。 _&lt;your-webapp-name>_ は、お使いのアプリの名前に置き換えてください。

```bash
https://<your-webapp-name>.azurewebsites.net
```

新しく作成した Azure Web アプリの既定の Web ページが表示されます。

### <a name="deploy-your-local-app"></a>ローカル アプリをデプロイする

ローカルのターミナル ウィンドウに戻り、Azure リモートをローカル Git リポジトリに追加します。 *\<deploymentLocalGitUrl-from-create-step>* は、「[リモート Web アプリを作成する](#create-a-remote-web-app)」の手順で保存した Git リモートの URL に置き換えてください。

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

アプリをデプロイするために、次のコマンドで Azure リモートにプッシュします。 Git Credential Manager によって資格情報の入力を求めるメッセージが表示されたら、「[デプロイ ユーザーを構成する](#configure-a-deployment-user)」の手順で作成した資格情報を使用してください。

```bash
git push azure master
```

このコマンドの実行には、数分かかる場合があります。 実行中、次の例のような情報が表示されます。
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;your-webapp-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;your-webapp-name&gt;.scm.azurewebsites.net:443/&lt;your-webapp-name&gt;.git
   d87e6ca..d6b5447  master -> master
</pre>

Web ブラウザーを使用して、デプロイされたアプリケーションにアクセス (またはブラウザーを最新の情報に更新) します。

```bash
http://<your-webapp-name>.azurewebsites.net
```

先ほど  にアクセスしたときに表示された "Hello World!" というメッセージが表示されます。

## <a name="create-and-assign-a-managed-identity"></a>マネージド ID を作成して割り当てる

このアプリケーションの ID を作成するために、Azure CLI で [az webapp-identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) コマンドを実行します。

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

この操作からは、次の JSON スニペットが返されます。

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

キー コンテナーに対する **get** 操作と **list** 操作のアクセス許可を Web アプリに与えるため、Azure CLI の [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) コマンドに principalID を渡します。

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```


## <a name="modify-the-app-to-access-your-key-vault"></a>キー コンテナーにアクセスするようアプリを変更する

### <a name="install-the-packages"></a>パッケージのインストール

ターミナル ウィンドウから、.NET 用 Azure Key Vault クライアント ライブラリ パッケージをインストールします。

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

### <a name="update-the-code"></a>コードを更新する

akvwebapp プロジェクトから Startup.cs ファイルを見つけて開きます。 

ヘッダーに次の 2 行を追加します。

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

`app.UseEndpoints` 呼び出しの前に以下の行を追加します。URI は、実際のキー コンテナーの `vaultUri` に合わせて更新してください。 以下のコードでは、キー コンテナーに対する認証に "[DefaultAzureCredential()](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet)" が使用されています。この場合、アプリケーションのマネージド ID からのトークンが認証に使用されます。 また、キー コンテナーがスロットルされている場合の再試行にはエクスポネンシャル バックオフが使用されています。

```csharp
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
var client = new SecretClient(new Uri("https://<your-unique-key-vault-name>.vault.azure.net/"), new DefaultAzureCredential(),options);

KeyVaultSecret secret = client.GetSecret("mySecret");

string secretValue = secret.Value;
```

`await context.Response.WriteAsync("Hello World!");` という行を次のように更新します。

```csharp
await context.Response.WriteAsync(secretValue);
```

次の手順に進む前に、必ず変更内容を保存してください。

### <a name="redeploy-your-web-app"></a>Web アプリを再デプロイする

コードを更新したら、次の Git コマンドを使用して、そのコードを Azure に再デプロイすることができます。

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure master
```

## <a name="visit-your-completed-web-app"></a>完成した Web アプリにアクセスする

```bash
http://<your-webapp-name>.azurewebsites.net
```

**Hello World** が表示される前に、シークレットの値が表示されるはずです。**成功です**

## <a name="next-steps"></a>次のステップ

- 詳細については、[Azure リソースのマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) について学びます。
- [App Service のマネージド ID](../../app-service/overview-managed-identity.md?tabs=dotnet) について学びます。
- [.NET 用 Azure Key Vault クライアント ライブラリ API リファレンス](/dotnet/api/overview/azure/key-vault?view=azure-dotnet)を参照してください。
- [.NET 用 Azure Key Vault クライアント ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)を参照してください。
- [.NET 用 Azure Key Vault クライアント ライブラリ v4 の NuGet パッケージ](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)を参照してください。



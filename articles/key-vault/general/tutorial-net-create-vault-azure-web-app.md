---
title: チュートリアル - .NET の Azure Web アプリを使用して Azure Key Vault を使用する
description: このチュートリアルでは、キー コンテナーからシークレットを読み取るように ASP.NET Core アプリケーションの Azure Web アプリを構成します。
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 2960726cf687908e8e4aed9333fce490dd7ff006
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98788739"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-in-net"></a>チュートリアル:マネージド ID を使用して Key Vault を .NET の Azure Web アプリに接続する

[Azure Key Vault](./overview.md) は、資格情報やその他のシークレットをより安全に格納する方法を提供します。 ただし、それらを取得するためには、コードから Key Vault に対する認証を行う必要があります。 [Azure リソースのマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を使用し、Azure Active Directory (Azure AD) 内で自動的に管理される ID を Azure サービスに付与することで、この問題を解決することができます。 この ID を使用して、コードに資格情報が表示されていなくても、Key Vault を含む Azure AD の認証をサポートする任意のサービスに認証することができます。

このチュートリアルでは、Azure Web アプリケーションを作成して [Azure App Service](../../app-service/overview.md) にデプロイします。 [.NET 用 Azure Key Vault シークレット クライアント ライブラリ](/dotnet/api/overview/azure/key-vault)と [Azure CLI](/cli/azure/get-started-with-azure-cli) を使用して、マネージド ID を使用した Azure Key Vault に対する Azure Web アプリの認証を行います。 各種の開発言語や Azure PowerShell、Azure portal を使用する場合でも、基本的な原則は同じです。

このチュートリアルで紹介する Azure App Service Web アプリケーションとデプロイの詳細については、以下を参照してください。
- [App Service の概要](../../app-service/overview.md)
- [Azure App Service での ASP.NET Core Web アプリの作成](../../app-service/quickstart-dotnetcore.md)
- [Azure App Service へのローカル Git デプロイ](../../app-service/deploy-local-git.md)

## <a name="prerequisites"></a>[前提条件]

このチュートリアルを完了するには、次のものが必要です。

* Azure サブスクリプション。 [無料で作成できます。](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [.NET Core 3.1 SDK (以降)](https://dotnet.microsoft.com/download/dotnet-core/3.1)。
* [Git](https://www.git-scm.com/downloads) のインストール。
* [Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell](/powershell/azure/)。
* [Azure Key Vault](./overview.md)。 キー コンテナーは、[Azure portal](quick-create-portal.md)、[Azure CLI](quick-create-cli.md)、または [Azure PowerShell](quick-create-powershell.md) を使用して作成できます。
* Key Vault [シークレット](../secrets/about-secrets.md)。 シークレットは、[Azure portal](../secrets/quick-create-portal.md)、[PowerShell](../secrets/quick-create-powershell.md)、または [Azure CLI](../secrets/quick-create-cli.md) を使用して作成できます。

既に Web アプリケーションを Azure App Service にデプロイしている場合は、[キー コンテナーへの Web アプリ アクセスの構成](#create-and-assign-a-managed-identity)に関するセクションおよび [Web アプリケーション コードの変更](#modify-the-app-to-access-your-key-vault)に関するセクションにスキップできます。

## <a name="create-a-net-core-app"></a>.NET Core アプリを作成する
この手順では、ローカル .NET Core プロジェクトを設定します。

マシンのターミナル ウィンドウで、`akvwebapp` という名前のディレクトリを作成し、現在のディレクトリをそのディレクトリに変更します。

```bash
mkdir akvwebapp
cd akvwebapp
```

[dotnet new web](/dotnet/core/tools/dotnet-new) コマンドを使用して .NET Core アプリを作成します。

```bash
dotnet new web
```

アプリケーションをローカルで実行すると、アプリケーションを Azure にデプロイしたときにどう表示されるかを把握できます。

```bash
dotnet run
```

Web ブラウザーでアプリ (`http://localhost:5000`) に移動します。

正常にデプロイしたアプリ  というメッセージがサンプル アプリによってページに表示されます。

Azure 用に Web アプリケーションを作成する方法の詳細については、[Azure App Service での ASP.NET Core Web アプリの作成](../../app-service/quickstart-dotnetcore.md)に関するページを参照してください。

## <a name="deploy-the-app-to-azure"></a>Azure にアプリケーションをデプロイする

この手順では、ローカル Git を使用して、Azure App Service に .NET Core アプリケーションをデプロイします。 アプリケーションを作成してデプロイする方法について詳しくは、[Azure に ASP.NET Core Web アプリを作成する](../../app-service/quickstart-dotnetcore.md)方法に関するページを参照してください。

### <a name="configure-the-local-git-deployment"></a>ローカル Git デプロイを構成する

ターミナル ウィンドウで **Ctrl + C** キーを押して Web サーバーを閉じます。  次のコマンドを実行して、.NET Core プロジェクト用の Git リポジトリを初期化してください。

```bash
git init
git add .
git commit -m "first commit"
```

FTP およびローカルの Git を使用し、"*デプロイ ユーザー*" を使用して Azure Web アプリをデプロイできます。 デプロイ ユーザーを構成すると、すべての Azure デプロイでこのユーザーを使用できます。 アカウントレベルのデプロイのユーザー名とパスワードは、Azure サブスクリプションの資格情報とは異なります。 

デプロイ ユーザーを構成するには、[az webapp deployment user set](/cli/azure/webapp/deployment/user?#az-webapp-deployment-user-set) コマンドを実行します。 次のガイドラインに準拠したユーザー名とパスワードを選択してください。 

- ユーザー名は Azure 内で一意になっている必要があります。 ローカルの Git プッシュでは、アット マーク (@) を含めることはできません。 
- パスワードは長さが 8 文字以上で、文字、数字、記号のうち 2 つを含む必要があります。 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

JSON 出力には、パスワードが `null` として表示されます。 `'Conflict'. Details: 409` エラーが発生した場合は、ユーザー名を変更します。 `'Bad Request'. Details: 400` エラーが発生した場合は、より強力なパスワードを使用します。 

Web アプリのデプロイに使用できるよう、ユーザー名とパスワードを記録してください。

### <a name="create-a-resource-group"></a>リソース グループを作成する

リソース グループは、Azure リソースをデプロイして管理するための入れ物となる論理コンテナーです。 キー コンテナーと Web アプリの配置先となるリソース グループは、[az group create](/cli/azure/group?#az-group-create) コマンドを使用して作成します。

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

Azure CLI の [az appservice plan create](/cli/azure/appservice/plan) コマンドを使用して、[App Service プラン](../../app-service/overview-hosting-plans.md)を作成します。 次の例では、`FREE` 価格レベルの `myAppServicePlan` という名前の App Service プランを作成します。

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

App Service プランが作成されると、Azure CLI によって、次のような情報が表示されます。

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

詳細については、「[Azure で App Service プランを管理する](../../app-service/app-service-plan-manage.md)」を参照してください。

### <a name="create-a-web-app"></a>Web アプリを作成する

`myAppServicePlan` App Service プランに [Azure Web アプリ](../../app-service/overview.md)を作成します。 

> [!Important]
> キー コンテナーと同様、Azure Web アプリにも一意の名前を付ける必要があります。 次の例の `<your-webapp-name>` は、実際の Web アプリの名前に置き換えてください。


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

Web アプリが作成されると、Azure CLI によって次のような出力が表示されます。

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


Git リモートの URL は `deploymentLocalGitUrl` プロパティに `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git` 形式で出力されます。 この URL を保存します。 この情報は後で必要になります。

次のコマンドを使用して新しいアプリに移動します。 `<your-webapp-name>` は、実際のアプリの名前に置き換えてください。

```bash
https://<your-webapp-name>.azurewebsites.net
```

新しい Azure Web アプリの既定の Web ページが表示されます。

### <a name="deploy-your-local-app"></a>ローカル アプリをデプロイする

ローカル ターミナル ウィンドウで、ローカル Git リポジトリに Azure リモートを追加します。 次のコマンドの `<deploymentLocalGitUrl-from-create-step>` は、「[Web アプリを作成する](#create-a-web-app)」セクションで保存した Git リモートの URL に置き換えてください。

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Azure リモートにアプリをプッシュしてデプロイするには、以下のコマンドを使用します。 Git Credential Manager によって資格情報の入力を求めるメッセージが表示されたら、「[ローカル Git デプロイを構成する](#configure-the-local-git-deployment)」セクションで作成した資格情報を使用してください。

```bash
git push azure main
```

このコマンドの実行には数分かかることがあります。 実行中、次のような情報が表示されます。
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'main'.
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
   d87e6ca..d6b5447  main -> main
</pre>

デプロイされたアプリケーションに移動 (または最新の情報に更新) します。

```bash
http://<your-webapp-name>.azurewebsites.net
```

正常にデプロイしたアプリ  先ほど `http://localhost:5000` にアクセスしたときに表示されたメッセージが表示されます。

Git を使用した Web アプリケーションのデプロイの詳細については、「[Azure App Service へのローカル Git デプロイ](../../app-service/deploy-local-git.md)」を参照してください
 
## <a name="configure-the-web-app-to-connect-to-key-vault"></a>Key Vault に接続するように Web アプリを構成する

このセクションでは、Key Vault への Web アクセスを構成し、Key Vault からシークレットを取得するようにアプリケーション コードを更新します。

### <a name="create-and-assign-a-managed-identity"></a>マネージド ID を作成して割り当てる

このチュートリアルでは、[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を使用して Key Vault に対する認証を行います。 マネージド ID によって自動的にアプリケーションの資格情報が管理されます。

このアプリケーションの ID を作成するために、Azure CLI で [az webapp-identity assign](/cli/azure/webapp/identity?#az-webapp-identity-assign) コマンドを実行します。

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

このコマンドからは、次の JSON スニペットが返されます。

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

キー コンテナーに対する **get** 操作と **list** 操作のアクセス許可を Web アプリに与えるため、Azure CLI の [az keyvault set-policy](/cli/azure/keyvault?#az-keyvault-set-policy) コマンドに `principalId` を渡します。

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```

[Azure portal](./assign-access-policy-portal.md) または [PowerShell](./assign-access-policy-powershell.md) を使用してアクセス ポリシーを割り当てることもできます。

### <a name="modify-the-app-to-access-your-key-vault"></a>キー コンテナーにアクセスするようアプリを変更する

このチュートリアルでは、デモンストレーションとして [Azure Key Vault シークレット クライアント ライブラリ](/dotnet/api/overview/azure/security.keyvault.secrets-readme)を使用します。 [Azure Key Vault 証明書クライアント ライブラリ](/dotnet/api/overview/azure/security.keyvault.certificates-readme)や [Azure Key Vault キー クライアント ライブラリ](/dotnet/api/overview/azure/security.keyvault.keys-readme)を使用することもできます。

#### <a name="install-the-packages"></a>パッケージのインストール

ターミナル ウィンドウから、.NET 用 Azure Key Vault シークレット クライアント ライブラリ パッケージと Azure ID クライアント ライブラリ パッケージをインストールします。

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

#### <a name="update-the-code"></a>コードを更新する

akvwebapp プロジェクトから Startup.cs ファイルを見つけて開きます。 

次の行をヘッダーに追加します。

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

`app.UseEndpoints` 呼び出しの前に以下の行を追加します。URI は、実際のキー コンテナーの `vaultUri` に合わせて更新してください。 このコードでは、Key Vault に対する認証に [DefaultAzureCredential()](/dotnet/api/azure.identity.defaultazurecredential) が使用されています。この場合、マネージド ID からのトークンが認証に使用されます。 Key Vault に対する認証の詳細については、[開発者ガイド](./developers-guide.md#authenticate-to-key-vault-in-code)を参照してください。 また、このコードでは、Key Vault がスロットルされている場合の再試行にはエクスポネンシャル バックオフが使用されています。 Key Vault のトランザクション制限について詳しくは、「[Azure Key Vault のスロットル ガイダンス](./overview-throttling.md)」を参照してください。

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

KeyVaultSecret secret = client.GetSecret("<mySecret>");

string secretValue = secret.Value;
```

`await context.Response.WriteAsync("Hello World!");` という行を次のように更新します。

```csharp
await context.Response.WriteAsync(secretValue);
```

次の手順に進む前に、必ず変更内容を保存してください。

#### <a name="redeploy-your-web-app"></a>Web アプリを再デプロイする

コードを更新したら、次の Git コマンドを使用して、そのコードを Azure に再デプロイすることができます。

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure main
```

## <a name="go-to-your-completed-web-app"></a>完成した Web アプリにアクセスする

```bash
http://<your-webapp-name>.azurewebsites.net
```

"Hello World!" が表示される前に、シークレットの値が表示されるはずです。

## <a name="next-steps"></a>次の手順

- [仮想マシンにデプロイされたアプリケーションで Azure Key Vault を使用する (.NET)](./tutorial-net-virtual-machine.md)
- 詳細については、[Azure リソースのマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) について学びます。
- [開発者ガイド](./developers-guide.md)を参照する
- [キー コンテナーへのアクセスをセキュリティで保護する](./secure-your-key-vault.md)
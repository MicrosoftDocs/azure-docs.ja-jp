---
title: マネージド ID を使用して App Configuration にアクセスする
titleSuffix: Azure App Configuration
description: マネージド ID を使用して Azure App Configuration に対して認証する
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.custom: devx-track-csharp, fasttrack-edit
ms.topic: conceptual
ms.date: 2/25/2020
ms.openlocfilehash: 386a0e27c0f73f5bcd42397ed515f7561d5097fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955059"
---
# <a name="use-managed-identities-to-access-app-configuration"></a>マネージド ID を使用して App Configuration にアクセスする

Azure Active Directory [マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) によってクラウド アプリケーションのシークレット管理が簡単になります。 マネージド ID を使用すると、コードでは、それが実行される Azure サービス用に作成されたサービス プリンシパルを使用できます。 Azure Key Vault またはローカル接続文字列に格納された別の資格情報の代わりに、マネージド ID を使用します。

Azure App Configuration とその .NET Core、.NET Framework、および Java Spring のクライアント ライブラリには、マネージド ID サポートが組み込まれています。 これの使用は必須ではありませんが、マネージド ID によって、シークレットが含まれるアクセス トークンが不要になります。 コードはサービス エンドポイントのみを使用して App Configuration ストアにアクセスできます。 シークレットの流出させることなく、この URL をコードに直接埋め込むことができます。

この記事では、マネージド ID を活用して App Configuration にアクセスする方法について説明します。 これは、クイック スタートで紹介されている Web アプリに基づいています。 先に進む前に、[App Configuration を使用して ASP.NET Core アプリを作成します](./quickstart-aspnet-core-app.md)。

> [!NOTE]
> この記事では例として Azure App Service を使用しますが、同じ概念は、マネージド ID をサポートする他の Azure サービス ([Azure Kubernetes Service](../aks/use-azure-ad-pod-identity.md)、[Azure 仮想マシン](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)、[Azure Container Instances](../container-instances/container-instances-managed-identity.md) など) にも当てはまります。 ワークロードがこれらのサービスのいずれかでホストされている場合は、そのサービスのマネージド ID サポートも利用できます。

この記事では、App Configuration の Key Vault 参照と共にマネージド ID を使用する方法についても説明します。 1つのマネージド ID を使用して、Key Vault のシークレットと App Configuration の構成値の両方にシームレスにアクセスできます。 この機能を確認する場合は、[ASP.NET Core の Key Vault 参照の使用](./use-key-vault-references-dotnet-core.md)に関するページの手順を最初に完了します。

このチュートリアルの手順は、任意のコード エディターを使用して実行できます。 推奨のエディターは [Visual Studio Code](https://code.visualstudio.com/) です (Windows、macOS、および Linux プラットフォームで使用できます)。

この記事では、次の方法について説明します。

> [!div class="checklist"]
> * App Configuration へのマネージド ID アクセスを許可する。
> * App Configuration に接続する際にマネージド ID を使用するようにアプリを構成する。
> * 必要に応じて、App Configuration Key Vault 参照を使用して Key Vault に接続するときに、マネージド ID を使用するようにアプリを構成します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* [.NET Core SDK](https://www.microsoft.com/net/download/windows)。
* [構成済みの Azure Cloud Shell](../cloud-shell/quickstart.md)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>マネージド ID の追加

ポータルでマネージド ID を設定するには、最初にアプリケーションを作成した後、フィーチャーを有効にします。

1. 通常どおりに、[Azure portal](https://portal.azure.com) で App Services インスタンスを作成します。 ポータルでそのアプリに移動します。

1. 左側のウィンドウで **[設定]** グループまで下へスクロールして、 **[ID]** を選択します。

1. **[システム割り当て済み]** タブで、 **[状態]** を **[オン]** に切り替えて、 **[保存]** を選択します。

1. システムで割り当てられたマネージド ID を有効にするように求められたら、 **[はい]** を指定します。

    ![App Service でのマネージド ID の設定](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>App Configuration へのアクセスの許可

1. [Azure portal](https://portal.azure.com) で **[すべてのリソース]** を選択し、クイック スタートで作成した App Configuration を選択します。

1. **[アクセス制御 (IAM)]** を選択します。

1. **[アクセスの確認]** タブで、 **[ロールの割り当てを追加する]** カード UI の **[追加]** を選択します。

1. **[ロール]** の中から、**App Configuration データ リーダー** を選択します。 **[アクセスの割り当て先]** で、 **[システム割り当てマネージド ID]** の **[App Service]** を選択します。

1. **[サブスクリプション]** で自分の Azure サブスクリプションを選択します。 アプリの App Service リソースを選択します。

1. **[保存]** を選択します。

    ![マネージド ID の追加](./media/add-managed-identity.png)

1. 省略可能:Key Vault へのアクセスも許可する場合は、「[Key Vault アクセス ポリシーを割り当てる](../key-vault/general/assign-access-policy-portal.md)」の指示に従ってください。

## <a name="use-a-managed-identity"></a>マネージド ID の使用

1. *Azure.Identity* パッケージの参照を追加します。

    ```bash
    dotnet add package Azure.Identity
    ```

1. App Configuration ストアのエンドポイントを検索します。 この URL は、Azure portal 内のストアの **[アクセスキー]** タブにリストされます。

1. *appsettings.json* を開き、以下のスクリプトを追加します。 *\<service_endpoint>* を、角かっこも含めて、ご利用の App Configuration ストアへの URL に置き換えます。

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. *Program.cs* を開き、`Azure.Identity` および `Microsoft.Azure.Services.AppAuthentication` の名前空間への参照を追加します。

    ```csharp-interactive
    using Azure.Identity;
    ```

1. App Configuration に直接格納されている値にのみアクセスしたい場合は、`config.AddAzureAppConfiguration()` メソッド (`Microsoft.Azure.AppConfiguration.AspNetCore` パッケージにあります) を置換して `CreateWebHostBuilder` メソッドを更新します。

    > [!IMPORTANT]
    > `CreateHostBuilder` により、.NET Core 3.0 の `CreateWebHostBuilder` が置き換えられます。  お使いの環境に応じて適切な構文を選択します。

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((hostingContext, config) =>
               {
                   var settings = config.Build();
                   config.AddAzureAppConfiguration(options =>
                       options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
               })
               .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                });
            })
            .UseStartup<Startup>());
    ```
    ---

    > [!NOTE]
    > **ユーザー割り当てのマネージド ID** を使用する場合、[ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) の作成時、必ず clientId を指定してください。
    >```
    >config.AddAzureAppConfiguration(options =>
    >   options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential(<your_clientId>)));
    >```
    >[Azure リソースのマネージド ID に関する FAQ](../active-directory/managed-identities-azure-resources/known-issues.md#what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request) で説明したように、使用されるマネージド ID を解決する既定の方法があります。 この場合、将来的にありえるランタイム問題 (たとえば、新しいユーザー割り当てマネージド ID が追加された場合やシステム割り当てマネージド ID が有効になった場合) を回避する目的で、望ましい ID を指定するように Azure ID ライブラリから強制されます。 そのため、ユーザー割り当てマネージド ID が 1 つだけ定義されており、システム割り当てマネージド ID がない場合であっても、clientId を指定する必要があります。


1. App Configuration と Key Vault 参照の両方を使用するには、以下に示すように、*Program.cs* を更新します。 このコードは `ConfigureKeyVault` の一部として `SetCredential` を呼び出し、Key Vault に対する認証時に使用する資格情報を構成プロバイダーに伝えます。

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((hostingContext, config) =>
               {
                   var settings = config.Build();
                   var credentials = new ManagedIdentityCredential();

                   config.AddAzureAppConfiguration(options =>
                   {
                       options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                              .ConfigureKeyVault(kv =>
                              {
                                 kv.SetCredential(credentials);
                              });
                   });
               })
               .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    var credentials = new ManagedIdentityCredential();

                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                               .ConfigureKeyVault(kv =>
                               {
                                   kv.SetCredential(credentials);
                               });
                    });
                });
            })
            .UseStartup<Startup>());
    ```
    ---

    他の App Configuration キーと同様に Key Vault 参照にアクセスできるようになりました。 構成プロバイダーは、`ManagedIdentityCredential` を使用して Key Vault に対して認証し、値を取得します。

    > [!NOTE]
    > `ManagedIdentityCredential` が機能するのは、マネージド ID 認証をサポートするサービスの Azure 環境内のみです。 ローカル環境では機能しません。 コードをローカルと Azure の両方の環境で動作させるには、[`DefaultAzureCredential`](/dotnet/api/azure.identity.defaultazurecredential) を使用します。これは、マネージド ID を含むいくつかの認証オプションにフォールバックするからです。
    > 
    > Azure にデプロイするとき、**ユーザー割り当てマネージド ID** を `DefaultAzureCredential` と共に使用する場合、[clientId を指定してください](/dotnet/api/overview/azure/identity-readme#specifying-a-user-assigned-managed-identity-with-the-defaultazurecredential)。

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>ローカル Git からのデプロイ

Kudu ビルド サーバーを使用したアプリへのローカル Git のデプロイを有効にする最も簡単な方法は、[Azure Cloud Shell](https://shell.azure.com) を使用することです。

### <a name="configure-a-deployment-user"></a>デプロイ ユーザーを構成する

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Kudu を使用するローカル Git を有効にする
アプリのローカル Git リポジトリがない場合は、初期化する必要があります。 ローカル Git リポジトリを初期化するには、アプリのプロジェクト　ディレクトリから次のコマンドを実行します。

```cmd
git init
git add .
git commit -m "Initial version"
```

Kudu ビルド サーバーを使用したアプリへのローカル Git のデプロイを有効にするには、Cloud Shell で [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/#az-webapp-deployment-source-config-local-git) を実行します。

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

このコマンドを実行すると、次のような出力が表示されます。

```json
{
  "url": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
}
```

### <a name="deploy-your-project"></a>プロジェクトのデプロイ

_ローカル ターミナル ウィンドウ_ で、ローカル Git リポジトリに Azure リモートを追加します。 _\<url>_ を、「[Kudu でローカル Git を有効にする](#enable-local-git-with-kudu)」で取得した Git リモートの URL に置換します。

```bash
git remote add azure <url>
```

アプリをデプロイするために、次のコマンドで Azure リモートにプッシュします。 パスワードの入力を求められたら、「[デプロイ ユーザーを構成する](#configure-a-deployment-user)」で作成したパスワードを入力します。 Azure portal へのサインインに使用しているパスワードは使用しないでください。

```bash
git push azure main
```

出力には、MSBuild (ASP.NET 向け)、`npm install` (Node.js 向け)、`pip install` (Python 向け) など、ランタイム固有のオートメーションが表示される場合があります。

### <a name="browse-to-the-azure-web-app"></a>Azure Web アプリの参照

ブラウザーを使用して自分の Web アプリに移動し、コンテンツがデプロイされていることを確認します。

```bash
http://<app_name>.azurewebsites.net
```

## <a name="use-managed-identity-in-other-languages"></a>他の言語におけるマネージド ID の使用

.NET Framework 用および Java Spring 用の App Configuration プロバイダーにも、マネージド ID に対する組み込みサポートがあります。 これらのプロバイダーのいずれかを構成するとき、完全な接続文字列の代わりに、ご自分のストアの URL エンドポイントを使用できます。

たとえば、クイックスタートで作成された .NET Framework コンソールアプリを更新して、*App.config* ファイルで次の設定を指定できます。

```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" endpoint="${Endpoint}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="Endpoint" value ="Set via an environment variable - for example, dev, test, staging, or production endpoint." />
    </appSettings>
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure マネージド ID を追加して、App Configuration へのアクセスを効率化し、アプリの資格情報管理を改善しました。 App Configuration の使用方法の詳細については、Azure CLI のサンプルに進んでください。

> [!div class="nextstepaction"]
> [CLI のサンプル](./cli-samples.md)
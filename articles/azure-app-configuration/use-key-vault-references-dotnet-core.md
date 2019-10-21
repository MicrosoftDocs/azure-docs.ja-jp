---
title: ASP.NET Core アプリで Azure App Configuration の Key Vault 参照を使用するためのチュートリアル | Microsoft Docs
description: このチュートリアルでは、ASP.NET Core アプリから Azure App Configuration の Key Vault 参照を使用する方法について説明します。
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/07/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: a14cb3035c159c82df44f686da7f7b78ef942943
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035817"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>チュートリアル:ASP.NET Core アプリで Key Vault 参照を使用する

このチュートリアルでは、Azure App Configuration サービスを Azure Key Vault と共に使用する方法について説明します。 これらは補完的なサービスであり、ほとんどのアプリケーションのデプロイで併用されます。 これらをまとめて使用できるように、App Configuration では、Key Vault に格納されている値を参照するキーを作成できます。 これを行うと、App Configuration には、その値そのものではなく、Key Vault の値への URI が格納されます。 お使いのアプリケーションは、App Configuration に格納されているその他のキーと同様に、App Configuration クライアント プロバイダーを使用してこのキーの値を取得します。 このクライアント プロバイダーはそれを Key Vault 参照として認識し、Key Vault を呼び出して値を取得します。 アプリケーションの役割は、App Configuration と Key Vault の両方に正しく認証されることです。 この 2 つのサービスが直接通信することはありません。

このチュートリアルでは、自分のコードに Key Vault 参照を実装する方法について説明します。 これは、クイック スタートで紹介されている Web アプリに基づいています。 先に進む前に、[App Configuration を使用した ASP.NET Core アプリの作成](./quickstart-aspnet-core-app.md)を完了しておいてください。

このチュートリアルの手順は、任意のコード エディターを使用して実行できます。 推奨のエディターは [Visual Studio Code](https://code.visualstudio.com/) です (Windows、macOS、および Linux プラットフォームで使用できます)。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Key Vault に格納されている値を参照する App Configuration キーを作成する
> * ASP.NET Core Web アプリケーションからこのキーの値にアクセスする

## <a name="prerequisites"></a>前提条件

このチュートリアルを実行するには、[.NET Core SDK](https://dotnet.microsoft.com/download) をインストールします。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>コンテナーの作成

1. Azure Portal の左上隅にある **[リソースの作成]** オプションを選択します

    ![Key Vault の作成が完了した後の出力](./media/quickstarts/search-services.png)
2. 検索ボックスに「**Key Vault**」と入力します。
3. 結果の一覧の **[Key Vault]** を選択します。
4. Key Vault のセクションで、 **[作成]** を選択します。
5. **[キー コンテナーの作成]** セクションで、次の情報を入力します。
    - **Name**:一意の名前が必要です。 このクイックスタートでは、**Contoso-vault2** を使用します。 
    - **サブスクリプション**:サブスクリプションを選択します。
    - **[リソース グループ]** で、 **[新規作成]** を選択し、リソース グループ名を入力します。
    - **[場所]** プルダウン メニューで場所を選択します。
    - 他のオプションは既定値のままにしておきます。
6. 上記の情報を指定したら、 **[作成]** を選択します。

この時点で、お使いの Azure アカウントが、この新しいコンテナーへのアクセスが承認されている唯一のアカウントになります。

![Key Vault の作成が完了した後の出力](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Key Vault にシークレットを追加する

シークレットをコンテナーに追加するには、いくつかの追加の手順を実行する必要があります。 この場合、Key Vault の取得をテストするために使用できるメッセージを追加します。 このメッセージは **Message** という名前で、それには **Hello from Key Vault** という値が格納されます。

1. Key Vault のプロパティ ページで、 **[シークレット]** を選択します。
1. **[Generate/Import]\(生成/インポート\)** をクリックします。
1. **[シークレットの作成]** 画面で、次の値を選択します。
    - **[アップロード オプション]** :手動。
    - **Name**:Message
    - **値**: Hello from Key Vault
    - 他の値は既定値のままにしておきます。 **Create** をクリックしてください。

## <a name="add-a-key-vault-reference-to-app-config"></a>App Configuration に Key Vault 参照を追加する

1. [Azure Portal](https://portal.azure.com) にサインインします。 **[すべてのリソース]** を選択し、クイック スタートで作成したアプリ構成ストア インスタンスを選択します。

1. **[構成エクスプローラー]** をクリックします。

1. **[+ 作成]**  >  **[Key vault reference]\(Key Vault 参照\)** をクリックし、次の値を選択します。
    - **[キー]** :TestApp:Settings:KeyVaultMessage
    - **ラベル**:空白
    - **[サブスクリプション]** 、 **[リソース グループ]** 、 **[キー コンテナー]** : 前のセクションで作成したキー コンテナーに対応するオプションを選択します。
    - **[シークレット]** : 前のセクションで作成した、**Message** という名前のシークレットを選択します。

## <a name="connect-to-key-vault"></a>Key Vault に接続する

1. このチュートリアルでは、KeyVault の認証にサービス プリンシパルを使用します。 このサービス プリンシパルを作成するには、Azure CLI の [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) コマンドを使用します。

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    この操作では、一連のキーと値のペアが返されます。 

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. 次のコマンドを実行して、キー コンテナーへのアクセスをサービス プリンシパルに許可します。

        az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey

1. *clientId* と *clientSecret* のシークレットを Secrets Manager に追加します。 これらのコマンドは、 *.csproj* ファイルと同じディレクトリ内で実行する必要があります。

        dotnet user-secrets set ConnectionStrings:KeyVaultClientId <clientId-of-your-service-principal>        
        dotnet user-secrets set ConnectionStrings:KeyVaultClientSecret <clientSecret-of-your-service-principal>

> [!NOTE]
> これらの Key Vault 資格情報は、お使いのアプリケーション内でのみ使用されます。 アプリケーションは、これらの資格情報を使用して Key Vault に直接認証されます。 これらが App Configuration サービスに渡されることはありません。

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Key Vault 参照を使用するようコードを更新する

1. *Program.cs* を開き、必要なパッケージへの参照を追加します。

    ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. `config.AddAzureAppConfiguration()` メソッドを呼び出して App Configuration を使用するように、`CreateWebHostBuilder` メソッドを更新します。 `UseAzureKeyVault` オプションを含めて、Key Vault への新しい `KeyVaultClient` 参照で渡します。

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                KeyVaultClient kvClient = new KeyVaultClient(async (authority, resource, scope) =>
                {
                    var adCredential = new ClientCredential(settings["ConnectionStrings:KeyVaultClientId"], settings["ConnectionStrings:KeyVaultClientSecret"]);
                    var authenticationContext = new AuthenticationContext(authority, null);
                    return (await authenticationContext.AcquireTokenAsync(resource, adCredential)).AccessToken;
                });

                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .UseAzureKeyVault(kvClient); });
            })
            .UseStartup<Startup>();
    ```

1. App Configuration への接続を初期化するときに *KeyVaultClient* 参照を `UseAzureKeyVault` メソッドに渡した後は、通常の App Configuration キーの値にアクセスするのと同じ方法で Key Vault 参照の値にアクセスできます。 このプロセスの動作を確認するには、Views の Home ディレクトリにある *Index.cshtml* を開きます。 その内容を次のコードに置き換えます。

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    Key Vault 参照の値 *TestApp:Settings:KeyVaultMessage* には、構成値 *TestApp:Settings:Message* と同じようにアクセスできます。

## <a name="build-and-run-the-app-locally"></a>アプリをビルドしてローカルで実行する

1. .NET Core CLI を使用してアプリケーションをビルドするには、コマンド シェルで次のコマンドを実行します。

        dotnet build

2. ビルドが正常に完了したら、次のコマンドを実行して、Web アプリをローカルで実行します。

        dotnet run

3. ブラウザー ウィンドウを開いて、`http://localhost:5000` (ローカルでホストされた Web アプリの既定の URL) に移動します。

    ![クイック スタートのアプリ (ローカルで起動)](./media/key-vault-reference-launch-local.png)


## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure マネージド サービス ID を追加して、App Configuration へのアクセスを効率化し、アプリの資格情報管理を改善しました。 App Configuration の使用方法の詳細については、Azure CLI のサンプルに進んでください。

> [!div class="nextstepaction"]
> [CLI のサンプル](./cli-samples.md)

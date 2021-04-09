---
title: ASP.NET Core アプリで Azure App Configuration の Key Vault 参照を使用するためのチュートリアル | Microsoft Docs
description: このチュートリアルでは、ASP.NET Core アプリから Azure App Configuration の Key Vault 参照を使用する方法について説明します。
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/08/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 37bc7fbcd366455668d5316e45ffbf79127a49f3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99981225"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>チュートリアル:ASP.NET Core アプリで Key Vault 参照を使用する

このチュートリアルでは、Azure App Configuration サービスを Azure Key Vault と共に使用する方法について説明します。 App Configuration と Key Vault は補完的なサービスであり、ほとんどのアプリケーションのデプロイで併用されます。

これらをまとめて使用できるように、App Configuration では、Key Vault に格納されている値を参照するキーを作成できます。 App Configuration でこのようなキーを作成すると、App Configuration には、その値そのものではなく、Key Vault の値の URI が格納されます。

App Configuration に格納されているその他のキーの場合と同様、お使いのアプリケーションは App Configuration クライアント プロバイダーを使用して Key Vault 参照を取得します。 この場合、App Configuration に格納された値は、Key Vault の値を参照する URI です。 これらは、Key Vault の値でも資格情報でもありません。 クライアント プロバイダーはキーを Key Vault 参照として認識し、Key Vault を使用して値を取得します。

アプリケーションの役割は、App Configuration と Key Vault の両方に正しく認証されることです。 この 2 つのサービスが直接通信することはありません。

このチュートリアルでは、自分のコードに Key Vault 参照を実装する方法について説明します。 これは、クイック スタートで紹介されている Web アプリに基づいています。 先に進む前に、[App Configuration を使用した ASP.NET Core アプリの作成](./quickstart-aspnet-core-app.md)を完了しておいてください。

このチュートリアルの手順は、任意のコード エディターを使用して実行できます。 たとえば、[Visual Studio Code](https://code.visualstudio.com/) は、Windows、macOS、および Linux の各オペレーティング システムで使用できるクロスプラットフォーム コード エディターです。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Key Vault に格納されている値を参照する App Configuration キーを作成する。
> * ASP.NET Core Web アプリケーションからこのキーの値にアクセスする。

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、[.NET Core SDK](https://dotnet.microsoft.com/download) をインストールします。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>コンテナーの作成

1. Azure Portal の左上隅にある **[リソースの作成]** オプションを選択します。

    ![Azure portal の [リソースの作成] オプションを示すスクリーンショット。](./media/quickstarts/search-services.png)
1. 検索ボックスに「**Key Vault**」と入力します。
1. 結果リストで、左側の **[キー コンテナー]** を選択します。
1. **[キー コンテナー]** で、 **[追加]** を選択します。
1. **[キー コンテナーの作成]** の右側に、次の情報を入力します。
    - **[サブスクリプション]** を選択してサブスクリプションを選択します。
    - **[リソース グループ]** で、 **[新規作成]** を選択し、リソース グループの名前を入力します。
    - **[キー コンテナー名]** では、一意の名前が必要です。 このチュートリアルでは、「**Contoso-vault2**」と入力します。
    - **[リージョン]** ドロップダウン リストで、場所を選択します。
1. **[キー コンテナーの作成]** オプションは既定値のままにしておきます。
1. **［作成］** を選択します

この時点で、お使いの Azure アカウントが、この新しいコンテナーへのアクセスが承認されている唯一のアカウントになります。

![キー コンテナーを示すスクリーンショット。](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Key Vault にシークレットを追加する

シークレットをコンテナーに追加するには、いくつかの追加の手順を行う必要があります。 この場合、Key Vault の取得をテストするために使用できるメッセージを追加します。 このメッセージは **Message** という名前で、それには "Hello from Key Vault" という値が格納されます。

1. Key Vault のプロパティ ページで、 **[シークレット]** を選択します。
1. **[Generate/Import]\(生成/インポート\)** を選択します。
1. **[シークレットの作成]** ウィンドウで、次の値を入力します。
    - **[アップロード オプション]** :「**Manual**」と入力します。
    - **Name**:「**Message**」と入力します。
    - **値**: 「**Hello from Key Vault**」と入力します。
1. **[シークレットの作成]** の他のプロパティは既定値のままにしておきます。
1. **［作成］** を選択します

## <a name="add-a-key-vault-reference-to-app-configuration"></a>App Configuration に Key Vault 参照を追加する

1. [Azure portal](https://portal.azure.com) にサインインします。 **[すべてのリソース]** を選択し、クイック スタートで作成した App Configuration ストア インスタンスを選択します。

1. **[構成エクスプローラー]** を選択します。

1. **[+ 作成]** 、 **[キー コンテナー参照]** の順に選択し、次の値を指定します。
    - **[キー]** :**TestApp:Settings:KeyVaultMessage** を選択します。
    - **ラベル**:この値は空白のままにしておきます。
    - **[サブスクリプション]** 、 **[リソース グループ]** 、 **[キー コンテナー]** : 前のセクションで作成したキー コンテナーの値に対応する値を入力します。
    - **[シークレット]** : 前のセクションで作成した、**Message** という名前のシークレットを選択します。

## <a name="connect-to-key-vault"></a>Key Vault に接続する

1. このチュートリアルでは、Key Vault の認証にサービス プリンシパルを使用します。 このサービス プリンシパルを作成するには、Azure CLI の [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) コマンドを使用します。

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
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. 次のコマンドを実行して、サービス プリンシパルがキー コンテナーにアクセスできるようにします。

    ```cmd
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. *clientId*、*clientSecret*、*tenantId* の値を格納する環境変数を追加します。

    #### <a name="windows-command-prompt"></a>[Windows コマンド プロンプト](#tab/cmd)

    ```cmd
    setx AZURE_CLIENT_ID <clientId-of-your-service-principal>
    setx AZURE_CLIENT_SECRET <clientSecret-of-your-service-principal>
    setx AZURE_TENANT_ID <tenantId-of-your-service-principal>
    ```

    #### <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```PowerShell
    $Env:AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    $Env:AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    $Env:AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    #### <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    export AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    export AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    export AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    ---

    > [!NOTE]
    > これらの Key Vault 資格情報は、お使いのアプリケーション内でのみ使用されます。 アプリケーションは、これらの資格情報を使用して Key Vault に直接認証されます。 これらが App Configuration サービスに渡されることはありません。

1. ターミナルを再起動して、これらの新しい環境変数を読み込みます。

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Key Vault 参照を使用するようコードを更新する

1. 次のコマンドを実行して、必要な NuGet パッケージへの参照を追加します。

    ```dotnetcli
    dotnet add package Azure.Identity
    ```

1. *Program.cs* を開き、次の必須パッケージへの参照を追加します。

    ```csharp
    using Azure.Identity;
    ```

1. `config.AddAzureAppConfiguration` メソッドを呼び出して App Configuration を使用するように、`CreateWebHostBuilder` メソッドを更新します。 `ConfigureKeyVault` オプションを追加し、ご利用のキー コンテナーに正しい資格情報を渡します。

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>());
    ```

1. App Configuration への接続を初期化するときは、`ConfigureKeyVault` メソッドを呼び出して、Key Vault への接続を設定します。 初期化後、通常の App Configuration キーの値にアクセスする場合と同じ方法で、Key Vault 参照の値にアクセスできます。

    このプロセスの動作を確認するには、**Views** > **Home** フォルダーにある *Index.cshtml* を開きます。 その内容を次のコードに置き換えます。

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"]px;
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    Key Vault 参照の値 **TestApp:Settings:KeyVaultMessage** には、構成値 **TestApp:Settings:Message** と同様にアクセスできます。

## <a name="build-and-run-the-app-locally"></a>アプリをビルドしてローカルで実行する

1. .NET Core CLI を使用してアプリケーションをビルドするには、コマンド シェルで次のコマンドを実行します。

    ```dotnetcli
    dotnet build
    ```

1. ビルドが完了したら、次のコマンドを実行して、Web アプリをローカルで実行します。

    ```dotnetcli
    dotnet run
    ```

1. ブラウザー ウィンドウを開いて、`http://localhost:5000` (ローカルでホストされた Web アプリの既定の URL) に移動します。

    ![クイック スタートのアプリ (ローカルで起動)](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Key Vault に格納されている値を参照する App Configuration キーを作成しました。 App Configuration と Key Vault へのアクセスを効率化する Azure マネージド サービスの ID を追加する方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [マネージド ID の統合](./howto-integrate-azure-managed-service-identity.md)

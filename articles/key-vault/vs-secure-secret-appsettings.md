---
title: Web アプリケーション用のシークレット アプリケーション設定の安全な保存 - Azure Key Vault | Microsoft Docs
description: ASP.NET Core Key Vault Provider、User Secret、または .NET 4.7.1 構成ビルダーを使用して Azure 資格情報またはサード パーティ製 API キーなどのシークレット アプリケーション設定を安全に保存する方法
services: visualstudio
documentationcenter: ''
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: key-vault
ms.workload: web, azure
ms.tgt_pltfrm: vs-getting-started
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: cawa
ms.openlocfilehash: 79b1c740bca56982243ddc130d8747fdc955247f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58124118"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Web アプリケーションのシークレット アプリケーション設定を安全に保存する

## <a name="overview"></a>概要
この記事では、Azure アプリケーションのシークレット アプリケーション構成設定を安全に保存する方法について説明します。

従来、すべての Web アプリケーション構成設定は、Web.config などの構成ファイルに保存されます。この演習では、クラウド資格情報などのシークレット設定を、GitHub などのパブリック ソース管理システムにチェックインする方法を示します。 その一方で、ソース コードの変更と開発設定の再構成に必要なオーバーヘッドが原因で、セキュリティのベスト プラクティスに従うことが困難である可能性があります。

アプリケーション シークレット設定を安全に保存するためのツールとフレームワーク ライブラリが作成されますが、開発プロセスの安全性を確保するためソース コードの変更は行われないか最小限に抑えられます。

## <a name="aspnet-and-net-core-applications"></a>ASP.NET Core と .NET Core アプリケーション

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>ソース管理フォルダーの外部にあるシークレット設定を User Secret ストアに保存する
クイック プロトタイプを実行しているか、インターネットにアクセスできない場合は、まずソース管理フォルダーの外部にあるシークレット設定を User Secret ストアに移動します。 User Secret ストアは、ユーザー プロファイラー フォルダーの下に保存されているファイルです。そのため、シークレットはソース管理にチェックインされません。 次の図では、[User Secret](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio) がどのように機能するかを示します。

![User Secret ではシークレット設定はソース管理の外部に保持される](./media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

.NET Core コンソール アプリケーションを実行している場合は、Key Vault を使用して、シークレットを安全に保存します。

### <a name="save-secret-settings-in-azure-key-vault"></a>Azure Key Vault にシークレット設定を保存する
プロジェクトを開発しており、ソース コードを安全に共有する必要がある場合は、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) を使用します。

1. の Azure サブスクリプションで Key Vault を作成します。 UI 上のすべての必須フィールドに入力し、ブレードの下部にある *[作成]* をクリックします

    ![Azure Key Vault を作成する](./media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. 自分とチーム メンバーに Key Vault へのアクセス権を付与します。 チームが大規模な場合は、[Azure Active Directory グループ](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal)を作成し、そのセキュリティ グループに Key Vault へのアクセス権を追加します。 *[シークレットのアクセス許可]* ボックスで、*[シークレットの管理操作]* の下の *[取得]* と *[リスト]* を選択します。

    ![Key Vault アクセス ポリシーを追加する](./media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Azure Portal で、シークレットを Key Vault に追加します。 入れ子になった構成設定の場合は、':' を '--' に置き換えます。これにより、Key Vault シークレット名が有効になります。 ':' は、Key Vault シークレットの名前には使用できません。

    ![Key Vault シークレットを追加する](./media/vs-secure-secret-appsettings/add-keyvault-secret.png)

4. [Visual Studio 用のAzure サービス認証拡張機能](https://go.microsoft.com/fwlink/?linkid=862354)をインストールします。 アプリはこの拡張機能により、Visual Studio サインイン ID を使用して Key Vault にアクセスできます。

5. 次の Nuget パッケージをプロジェクトに追加します。

    ```
    Microsoft.Azure.Services.AppAuthentication
    ```
6. Program.cs ファイルに次のコードを追加します。

    ```csharp
    public static IWebHost BuildWebHost(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((ctx, builder) =>
            {
                var keyVaultEndpoint = GetKeyVaultEndpoint();
                if (!string.IsNullOrEmpty(keyVaultEndpoint))
                {
                    var azureServiceTokenProvider = new AzureServiceTokenProvider();
                    var keyVaultClient = new KeyVaultClient(
                        new KeyVaultClient.AuthenticationCallback(
                            azureServiceTokenProvider.KeyVaultTokenCallback));
                            builder.AddAzureKeyVault(
                            keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                        }
                    })
                    .UseStartup<Startup>()
                    .Build();

        private static string GetKeyVaultEndpoint() => Environment.GetEnvironmentVariable("KEYVAULT_ENDPOINT");
    ```
7. Key Vault URL を launchsettings.json ファイルに追加します。 手順 6 で追加したコードで、環境変数名 *KEYVAULT_ENDPOINT* が定義されます。

    ![プロジェクトの環境変数として Key Vault URL を追加する](./media/vs-secure-secret-appsettings/add-keyvault-url.png)

8. プロジェクトのデバッグを開始します。 これは正常に実行される必要があります。

## <a name="aspnet-and-net-applications"></a>ASP.NET と .NET のアプリケーション

.NET 4.7.1 では、Key Vault 構成ビルダーと シークレット構成ビルダーがサポートされています。これにより、コードを変更せずにソース管理フォルダーの外部にシークレットを移動できます。
続行するには、[.NET 4.7.1 をダウンロード](https://www.microsoft.com/download/details.aspx?id=56115)し、アプリケーションで古いバージョンの .NET Framework が使用されている場合はそのアプリケーションを移行します。

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>ソース管理フォルダーの外部にあるシークレット設定をシークレット ファイルに保存する
クイック プロトタイプを記述しており、Azure リソースをプロビジョニングしない場合は、このオプションを使用します。

1. プロジェクトに次の NuGet パッケージをインストールします
    ```
    Microsoft.Configuration.ConfigurationBuilders.Basic
    ```

2. 次のようなファイルを作成します。 プロジェクト フォルダーの外部の場所に保存します。

    ```xml
    <root>
        <secrets ver="1.0">
            <secret name="secret1" value="foo_one" />
            <secret name="secret2" value="foo_two" />
        </secrets>
    </root>
    ```

3. Web.config ファイルでそのシークレット ファイルを構成ビルダーとして定義します。 このセクションは *appSettings* セクションの前に配置します。

    ```xml
    <configBuilders>
        <builders>
            <add name="Secrets"
                 secretsFile="C:\Users\AppData\MyWebApplication1\secret.xml" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder,
                    Microsoft.Configuration.ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```

4. appSettings セクションで、そのシークレット構成ビルダーを使用することを指定します。 シークレット設定に、ダミーの値をもつエントリがあることを確認します。

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. アプリをデバッグします。 これは正常に実行される必要があります。

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Azure Key Vault にシークレット設定を保存する
プロジェクト用に Key Vault を構成するには、ASP.NET Core のセクションの指示に従います。

1. プロジェクトに次の NuGet パッケージをインストールします
   ```
   Microsoft.Configuration.ConfigurationBuilders.UserSecrets
   ```

2. Web.config で Key Vault 構成ビルダーを定義します。このセクションは *appSettings* セクションの前に配置します。 お客様の Key Vault がパブリック Azure 内にある場合は、*vaultName* を Key Vault 名に置き換え、管理権のあるクラウドを使用している場合は完全 URI に置き換えます。

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>
    <configBuilders>
        <builders>
            <add name="AzureKeyVault" vaultName="Test911" type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```
3. appSettings セクションでその Key Vault 構成ビルダーを使用することを指定します。 シークレット設定に、ダミーの値をもつエントリがあることを確認します。

   ```xml
   <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="secret" value="" />
   </appSettings>
   ```

4. プロジェクトのデバッグを開始します。 これは正常に実行される必要があります。

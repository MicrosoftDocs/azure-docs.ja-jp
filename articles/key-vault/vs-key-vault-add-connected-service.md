---
title: Visual Studio を使用して ASP.NET プロジェクトに Key Vault のサポートを追加する - Azure Key Vault | Microsoft Docs
description: このチュートリアルを使用すると、ASP.NET または ASP.NET Core Web アプリケーションに Key Vault のサポートを追加する方法を学習できます。
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: ghogen
ms.openlocfilehash: f84662ce17d604f1a60b6700d259d322415ae91e
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672209"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Visual Studio 接続済みサービスを使用して Web アプリケーションに Key Vault を追加する

このチュートリアルでは、Azure Key Vault を使用して Web プロジェクト用のシークレットを管理するために必要なもののすべてを、Visual Studio で簡単に追加する方法を学習します。使用するプロジェクトは、ASP.NET Core プロジェクトでも、その他の種類の ASP.NET プロジェクトでもかまいません。 Visual Studio の接続済みサービスの機能を使用することで、Azure 内の Key Vault に接続するために必要なすべての NuGet パッケージと構成設定の追加を Visual Studio に自動的に実行させることができます。 

接続済みサービスが Key Vault を有効にするためにプロジェクト内で実行する変更の詳細については、[Key Vault 接続済みサービス - ASP.NET 4.7.1 プロジェクトで何が起こるか](#how-your-aspnet-framework-project-is-modified)、または[Key Vault 接続済みサービス - ASP.NET Core プロジェクトで何が起こるか](#how-your-aspnet-core-project-is-modified)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション**。 このサブスクリプションがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。
- **Visual Studio 2019** または **Visual Studio 2017 バージョン 15.7** (**Web 開発**ワークロードもインストールされます)。 [こちら](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)でダウンロードできます。
- ASP.NET (Core ではありません) と Visual Studio 2017 の組み合わせの場合、.NET Framework 4.7.1 以降の開発ツールが必要ですが、これは既定ではインストールされません。 インストールするには、Visual Studio インストーラーを起動し、 **[変更]** を選択します。次に、 **[個々 のコンポーネント]** を選択した後、右側にある **[ASP.NET と Web 開発]** を展開し、 **[.NET Framework 4.7.1 開発ツール]** を選択します。
- ASP.NET 4.7.1 以降または ASP.NET Core 2.0 Web プロジェクトが開きます。

## <a name="add-key-vault-support-to-your-project"></a>Key Vault のサポートをプロジェクトに追加する

1. **ソリューション エクスプローラー**で、 **[追加]**  >  **[接続済みサービス]** を選択します。
   [接続済みサービス] ページが開いて、プロジェクトに追加できるサービスが表示されます。
1. 使用可能なサービスのメニューから、 **[Secure Secrets With Azure Key Vault]\(Azure Key Vault でシークレットを保護する\)** を選択します。

   ![[Secure Secrets With Azure Key Vault]\(Azure Key Vault でシークレットを保護する\) を選択する](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   Visual Studio にサインインしたときに、アカウントに関連付けられている Azure サブスクリプションがある場合は、サブスクリプションのドロップダウン リストがページに表示されます。 Visual Studio にサインインしていること、サインインしたアカウントが Azure サブスクリプションを使用するためのアカウントと同じであることを確認します。

1. 使用するサブスクリプションを選択し、新規または既存の Key Vault を選択するか、自動的に生成された名前を変更するために [編集] リンクを選択します。

   ![サブスクリプションを選択します。](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. Key Vault で使用する名前を入力します。

   ![Key Vault の名前を変更し、リソース グループを選択する](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. 既存のリソース グループを選択するか、自動的に生成された一意の名前を持つ新しいパーティションの作成を選択します。  新しいグループを別の名前で作成する場合は、[Azure Portal](https://portal.azure.com) を使用できます。その後、ページを閉じ、再起動を行ってリソース グループの一覧を再度読み込みます。
1. Key Vault を作成するリージョンを選択します。 Web アプリケーションが Azure でホストされる場合は、Web アプリケーションを最適なパフォーマンスでホストするリージョンを選択します。
1. 価格レベルを選択します。 詳細については、「[Key Vault の価格](https://azure.microsoft.com/pricing/details/key-vault/)」を参照してください。
1. [OK] を選択して、構成の選択を受け入れます。
1. **[追加]** を選択して Key Vault を作成します。 既に使用されている名前を選択した場合、作成処理は失敗することがあります。  これが発生した場合は、 **[編集]** リンクを使用して Key Vault の名前を変更し、もう一度やり直してください。

   ![接続済みサービスをプロジェクトに追加する](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. 次に、Azure 内の Key Vault にシークレットを追加します。 ポータルの適切な場所に移動するには、このキー コンテナーに格納される管理シークレット用のリンクをクリックします。 ページまたはプロジェクトが閉じている場合は、[Azure portal](https://portal.azure.com) で **[すべてのサービス]** を選択し、 **[セキュリティ]** の下の **[Key Vault]** を選択します。次に、作成したキー コンテナーを選択することで、同じ場所に移動できます。

   ![ポータルに移動する](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. 作成したキー コンテナーの [Key Vault] セクションで、 **[シークレット]** 、 **[生成/インポート]** の順に選択します。

   ![シークレットを生成/インポートする](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. 「MySecret」などのシークレットを入力し、テストとして任意の文字列値を指定した後、 **[作成]** ボタンを選択します。

   ![シークレットの作成](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. (省略可能) 別のシークレットを入力しますが、今回は「Secrets--MySecret」 という名前を付けることでカテゴリに配置します。 この構文は、"MySecret" というシークレットを含む "Secrets" というカテゴリを指定します。
 
これで、コードでシークレットにアクセスできます。 次の手順は、ASP.NET 4.7.1 または ASP.NET Core のどちらを使用しているかに応じて異なります。

## <a name="access-your-secrets-in-code"></a>コードでシークレットにアクセスする

1. [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) NuGet ライブラリと [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet ライブラリの 2 つの NuGet パッケージをインストールします。

2. Program.cs ファイルを開き、コードを次のコードで更新します。 
   ```
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

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
               }
            ).UseStartup<Startup>()
             .Build();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

3. 次に About.cshtml.cs ファイルを開き、次のコードを記述します。
   1. この using ステートメントによって Microsoft.Extensions.Configuration への参照を含めます。

       ```csharp
       using Microsoft.Extensions.Configuration
       ```

   1. このコンストラクターを追加します。

       ```csharp
       public AboutModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. OnGet メソッドを更新します。 ここに示すプレース ホルダーの値を、上記のコマンドで作成したシークレット名で更新します。

       ```csharp
       public void OnGet()
       {
           //Message = "Your application description page.";
           Message = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

[About] ページを参照して、アプリをローカルで実行します。 シークレット値が取得されるはずです。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、リソース グループを削除します。 これにより、キー コンテナーと関連リソースが削除されます。 ポータルを使用してリソース グループを削除するには:

1. ポータル上部にある検索ボックスにリソース グループの名前を入力します。 このクイック スタートで使用されているリソース グループが検索結果に表示されたら、それを選択します。
2. **[リソース グループの削除]** を選択します。
3. **[リソース グループ名を入力してください:]** ボックスにリソース グループの名前を入力し、 **[削除]** を選択します。

## <a name="how-your-aspnet-core-project-is-modified"></a>ASP.NET Core プロジェクトの変更

このセクションでは、Visual Studio を使用して Key Vault 接続済みサービスを追加したときに、ASP.NET プロジェクトに対して行われる正確な変更内容を説明します。

### <a name="added-references"></a>追加された参照

プロジェクト ファイルの .NET リファレンスと NuGet パッケージのリファレンスに影響します。

| Type | リファレンス |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files"></a>追加されるファイル

- ConnectedService.json が追加されます。このファイルには、接続済みサービス プロバイダー、バージョン、およびドキュメントへのリンクに関する情報が記録されます。

### <a name="project-file-changes"></a>プロジェクト ファイルの変更

- 接続済みサービスの ItemGroup と ConnectedServices.json ファイルが追加されます。

### <a name="launchsettingsjson-changes"></a>launchsettings.json の変更

- IIS Express プロファイルと、ご使用の Web プロジェクト名に一致するプロファイルの両方に、次の環境変数エントリが追加されます。

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure"></a>Azure での変更

- リソース グループを作成します (または、既存のものを使用します)。
- 指定したリソース グループにキー コンテナーが作成されます。

## <a name="how-your-aspnet-framework-project-is-modified"></a>ASP.NET Framework プロジェクトの変更

このセクションでは、Visual Studio を使用して Key Vault 接続済みサービスを追加したときに、ASP.NET プロジェクトに対して行われる正確な変更内容を説明します。

### <a name="added-references"></a>追加された参照

プロジェクト ファイル (.NET 参照) と `packages.config` (NuGet 参照) に影響します。

| Type | リファレンス |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files"></a>追加されるファイル

- ConnectedService.json が追加されます。このファイルは、接続済みサービス プロバイダー、バージョン、およびドキュメントへのリンクに関する情報を記録します。

### <a name="project-file-changes"></a>プロジェクト ファイルの変更

- 接続済みサービスの ItemGroup と ConnectedServices.json ファイルが追加されます。
- 「[追加された参照](#added-references)」セクションで説明されている .NET アセンブリへの参照。

### <a name="webconfig-or-appconfig-changes"></a>web.config または app.config の変更

- 次の構成エントリが追加されます。

    ```xml
    <configSections>
      <section
           name="configBuilders"
           type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" 
           restartOnExternalChanges="false"
           requirePermission="false" />
    </configSections>
    <configBuilders>
      <builders>
        <add 
             name="AzureKeyVault"
             vaultName="vaultname"
             type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral" 
             vaultUri="https://vaultname.vault.azure.net" />
      </builders>
    </configBuilders>
    ```

### <a name="changes-on-azure"></a>Azure での変更

- リソース グループを作成します (または、既存のものを使用します)。
- 指定したリソース グループにキー コンテナーが作成されます。

## <a name="next-steps"></a>次の手順

Key Vault の開発の詳細については、「[Key Vault 開発者ガイド](key-vault-developers-guide.md)」を参照してください
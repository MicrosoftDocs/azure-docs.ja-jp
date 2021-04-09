---
title: Visual Studio を使用して ASP.NET プロジェクトに Key Vault のサポートを追加する - Azure Key Vault | Microsoft Docs
description: このチュートリアルを使用すると、ASP.NET または ASP.NET Core Web アプリケーションに Key Vault のサポートを追加する方法を学習できます。
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure, devx-track-csharp
ms.topic: how-to
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 9c62534acdbfbff7fd4e718bad1f07a92c641626
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792397"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Visual Studio 接続済みサービスを使用して Web アプリケーションに Key Vault を追加する

このチュートリアルでは、Azure Key Vault を使用して Web プロジェクト用のシークレットを管理するために必要なもののすべてを、Visual Studio で簡単に追加する方法を学習します。使用するプロジェクトは、ASP.NET Core プロジェクトでも、その他の種類の ASP.NET プロジェクトでもかまいません。 Visual Studio の接続済みサービスの機能を使用することで、Azure 内の Key Vault に接続するために必要なすべての NuGet パッケージと構成設定の追加を Visual Studio に自動的に実行させることができます。

接続済みサービスが Key Vault を有効にするためにプロジェクト内で実行する変更の詳細については、[Key Vault 接続済みサービス - ASP.NET 4.7.1 プロジェクトで何が起こるか](#how-your-aspnet-framework-project-is-modified)、または[Key Vault 接続済みサービス - ASP.NET Core プロジェクトで何が起こるか](#how-your-aspnet-core-project-is-modified)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション**。 サブスクリプションをお持ちでない場合には、[無料のアカウントにサインアップ](https://azure.microsoft.com/pricing/free-trial/)してください。
- **Visual Studio 2019 バージョン 16.3** 以降。[ここからダウンロードできます](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)。


## <a name="add-key-vault-support-to-your-project"></a>Key Vault のサポートをプロジェクトに追加する

開始する前に、必ず Visual Studio にサインインしてください。 Azure サブスクリプションに使用するものと同じアカウントでサインインします。 次に、ASP.NET 4.7.1 以降か、ASP.NET Core 2.0 Web プロジェクトを開き、次の手順を行います。

1. **ソリューション エクスプローラー** で、Key Vault のサポートを追加するプロジェクトを右クリックし、 **[追加]**  >  **[接続済みサービス]**  >  **[追加]** の順に選択します。
   [接続済みサービス] ページが開いて、プロジェクトに追加できるサービスが表示されます。
1. 使用可能なサービスのメニューから、 **[Azure Key Vault]** を選択し、 **[次へ]** をクリックします。

   !["Azure Key Vault" を選択する](../media/vs-key-vault-add-connected-service/key-vault-connected-service.png)

1. 使用するサブスクリプションを選択し、既存の Key Vault を選択して、 **[完了]** をクリックします。 

   ![サブスクリプションを選択します](../media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

これで、Key Vault への接続が確立され、コードでシークレットにアクセスできるようになりました。 次の手順は、ASP.NET 4.7.1 または ASP.NET Core のどちらを使用しているかに応じて異なります。

## <a name="access-your-secrets-in-code-aspnet-core"></a>コードでシークレットにアクセスする (ASP.NET Core)

1. いずれかのページ ファイル (*Index.cshtml.cs* など) を開き、次のコードを記述します。
   1. この using ディレクティブによって `Microsoft.Extensions.Configuration` の参照を含めます。

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. 構成変数を追加します。

      ```csharp
      private static IConfiguration _configuration;
      ```

   1. このコンストラクターを追加するか、既存のコンストラクターを次のように置き換えます。

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. `OnGet` メソッドを更新します。 ここに示すプレース ホルダーの値を、上記のコマンドで作成したシークレット名で更新します。

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameStoredInKeyVault>"];
       }
       ```

   1. 実行時に値を確認するには、`ViewData["Message"]` を表示するコードを *.cshtml* ファイルに追加して、メッセージ内にシークレットを表示します。

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

アプリをローカルで実行して、キー コンテナーからシークレットが正常に取得されることを確認できます。

## <a name="access-your-secrets-aspnet"></a>シークレットにアクセスする (ASP.NET)
web.config ファイルの `appSettings` 要素のダミーの値が実行時に true 値に置き換えられるように、構成を設定できます。 これには、`ConfigurationManager.AppSettings` データ構造体を使用してアクセスできます。

1. ソリューション エクスプローラーで、プロジェクトを右クリックし、[NuGet パッケージの管理] を選択します。 [参照] タブで、[[Microsoft.Configuration.ConfigurationBuilders.Azure]](https://www.nuget.org/packages/Microsoft.Configuration.ConfigurationBuilders.Azure/) を見つけてインストールします。
 
1. web.config ファイルを開き、次のコードを記述します。
    1. `configSections` と `configBuilders` を追加します。
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
    1. appSettings タグを検索し、属性 `configBuilders="AzureKeyVault"` を追加し、行を追加します。
        ```xml
         <add key="<secretNameInYourKeyVault>" value="dummy"/>
        ```

1. 確認のために値が表示されるように、*HomeController.cs* の `About` メソッドを編集します。

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["<secretNameInYourKeyVault>"];
   }
   ```
1. デバッガーの下でアプリをローカルで実行し、**[About]\(概要\)** タブに切り替えて、キー コンテナーの値が表示されていることを確認します。

## <a name="troubleshooting"></a>トラブルシューティング

お使いのキー コンテナーを Visual Studio にログインしているものとは異なる Microsoft アカウントで実行している場合 (たとえば、キー コンテナーは職場アカウントで実行しているが、Visual Studio ではプライベート アカウントを使用している場合)、お使いの Program.cs ファイルで、Visual Studio によるキー コンテナーへのアクセスを実行できないというエラーが発生します。 この問題を解決するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) に移動してお使いのキー コンテナーを開きます。

1. **[アクセス ポリシー]**、**[アクセス ポリシーの追加]** の順に選択して、プリンシパルとしてログインしているアカウントを選択します。

1. Visual Studio で、 **[ファイル]**  >  **[アカウント設定]** の順に選択します。
**[すべてのアカウント]** セクションから **[アカウントの追加]** を選択します。 お使いのアクセス ポリシーのプリンシパルとして選択したアカウントでサインインします。

1. **[ツール]**  >  **[オプション]** の順に選択し、 **[Azure Service Authentication]\(Azure サービス認証\)** を探します。 次に、Visual Studio に追加したアカウントを選択します。

これで、ご自分のアプリケーションをデバッグするときに、お使いのキー コンテナーが配置されているアカウントに Visual Studio が接続されます。

## <a name="how-your-aspnet-core-project-is-modified"></a>ASP.NET Core プロジェクトの変更

このセクションでは、Visual Studio を使用して Key Vault 接続済みサービスを追加したときに、ASP.NET プロジェクトに対して行われる正確な変更内容を説明します。

### <a name="added-references-for-aspnet-core"></a>ASP.NET Core の参照を追加しました

プロジェクト ファイルの .NET リファレンスと NuGet パッケージのリファレンスに影響します。

| Type | リファレンス |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>ASP.NET Core のファイルを追加しました

- `ConnectedService.json` を追加しました。このファイルには、接続済みサービス プロバイダー、バージョン、ドキュメントのリンクに関する情報が記録されます。

### <a name="project-file-changes-for-aspnet-core"></a>ASP.NET Core のプロジェクト ファイル変更

- 接続済みサービスの ItemGroup と `ConnectedServices.json` ファイルを追加しました。

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>ASP.NET Core の launchsettings.json 変更

- IIS Express プロファイルと、ご使用の Web プロジェクト名に一致するプロファイルの両方に、次の環境変数エントリが追加されます。

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>ASP.NET Core のための Azure 上の変更

- リソース グループを作成します (または、既存のものを使用します)。
- 指定したリソース グループにキー コンテナーが作成されます。

## <a name="how-your-aspnet-framework-project-is-modified"></a>ASP.NET Framework プロジェクトの変更

このセクションでは、Visual Studio を使用して Key Vault 接続済みサービスを追加したときに、ASP.NET プロジェクトに対して行われる正確な変更内容を説明します。

### <a name="added-references-for-aspnet-framework"></a>ASP.NET Framework の追加される参照

プロジェクト ファイル (.NET 参照) と `packages.config` (NuGet 参照) に影響します。

| Type | リファレンス |
| --- | --- |
| .NET; NuGet | Azure.Identity |
| .NET; NuGet | Azure.Security.KeyVault.Keys |
| .NET; NuGet | Azure.Security.KeyVault.Secrets |

> [!IMPORTANT] 
> 既定では、Azure.Identity 1.1.1 がインストールされます。これは、Visual Studio 資格情報をサポートしていません。 パッケージ参照を手動で 1.2+ に更新すると、Visual Studio 資格情報を使用することができます。

### <a name="added-files-for-aspnet-framework"></a>ASP.NET Framework の追加されるファイル

- `ConnectedService.json` が追加されます。このファイルには、接続済みサービス プロバイダー、バージョン、ドキュメントのリンクに関する情報が記録されます。

### <a name="project-file-changes-for-aspnet-framework"></a>ASP.NET Framework のプロジェクト ファイル変更

- 接続済みサービスの ItemGroup と ConnectedServices.json ファイルが追加されます。
- 「[追加された参照](#added-references-for-aspnet-framework)」セクションで説明されている .NET アセンブリへの参照。

## <a name="next-steps"></a>次のステップ

このチュートリアルに従っている場合、お使いのキー コンテナーへのアクセス許可は、ご自分の Azure サブスクリプションで実行するように設定されますが、これは運用環境のシナリオには適していない可能性があります。 マネージド ID を作成して、アプリの Key Vault へのアクセスを管理できます。 [Key Vault に対して認証を行う方法](./authentication.md)および [Key Vault アクセス ポリシーの割り当て](./assign-access-policy-portal.md)に関するページを参照してください。

Key Vault の開発の詳細については、「[Key Vault 開発者ガイド](developers-guide.md)」を参照してください。
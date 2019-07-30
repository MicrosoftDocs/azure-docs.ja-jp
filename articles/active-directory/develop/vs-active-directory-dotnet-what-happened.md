---
title: Azure AD への接続時に MVC プロジェクトに行われる変更
description: Visual Studio 接続済みサービスを使用して Azure AD に接続するときに MVC プロジェクトで何が起こるかについて説明します。
services: active-directory
ms.service: active-directory
ms.subservice: develop
author: ghogen
manager: douge
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8a2b73276dd6acc99e91df768ebf613b9e70477
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320757"
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>MVC プロジェクト (Visual Studio Azure Active Directory 接続済みサービス) の変更点

> [!div class="op_single_selector"]
> - [作業の開始](vs-active-directory-dotnet-getting-started.md)
> - [変更内容](vs-active-directory-dotnet-what-happened.md)

この記事では、[Visual Studio を使用して Azure Active Directory 接続サービスを](vs-active-directory-add-connected-service.md)追加したときに、ASP.NET MVC プロジェクトに対して行われる正確な変更内容を説明します。

接続済みサービスを使用する方法については、[使用開始](vs-active-directory-dotnet-getting-started.md)に関する記事をご覧ください。

## <a name="added-references"></a>追加された参照

プロジェクト ファイル (*.NET 参照) と `packages.config` (NuGet 参照) に影響します。

| Type | リファレンス |
| --- | --- |
| .NET; NuGet | Microsoft.IdentityModel.Protocol.Extensions |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.Cookies |
| .NET; NuGet | Microsoft.Owin.Security.OpenIdConnect |
| .NET; NuGet | Owin |
| .NET        | System.IdentityModel |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |
| .NET        | System.Runtime.Serialization |

追加の参照 ( **[ディレクトリ データの読み取り]** オプションを選択した場合):

| Type | リファレンス |
| --- | --- |
| .NET; NuGet | EntityFramework |
| .NET        | EntityFramework.SqlServer (Visual Studio 2015 のみ) |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms (Visual Studio 2015 のみ) |
| .NET; NuGet | System.Spatial |

次の参照は削除されます (Visual Studio 2015 では ASP.NET 4 プロジェクトのみ)。

| Type | リファレンス |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>プロジェクト ファイルの変更

- プロパティ `IISExpressSSLPort` が個別の数に設定されます。
- プロパティ `WebProject_DirectoryAccessLevelKey` が 0 に設定されます。 **[ディレクトリ データの読み取り]** オプションを選択した場合は、1 に設定されます。
- プロパティ `IISUrl` が `https://localhost:<port>/` に設定されます。このとき `<port>` は `IISExpressSSLPort` の値と一致します。

## <a name="webconfig-or-appconfig-changes"></a>web.config または app.config の変更

- 次の構成エントリが追加されます。

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="<your selected Azure domain>" />
        <add key="ida:TenantId" value="<the Id of your selected Azure AD tenant>" />
        <add key="ida:PostLogoutRedirectUri" value="<project start page, such as https://localhost:44335>" />
    </appSettings>
    ```

- `System.IdentityModel.Tokens.Jwt` と `Microsoft.IdentityModel.Protocol.Extensions`の `<runtime><assemblyBinding>` ノードの下に `<dependentAssembly>` 要素が追加されます。

追加の変更 ( **[ディレクトリ データの読み取り]** オプションを選択した場合):

- `<appSettings>` の下に次の構成エントリが追加されます。

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- `<configuration>` の下に次の要素が追加されます。project-mdf-file と project-catalog-id の値は異なります。

    ```xml
    <configSections>
      <!-- For more information on Entity Framework configuration, visit https://go.microsoft.com/fwlink/?LinkID=237468 -->
      <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>

    <connectionStrings>
      <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\<project-mdf-file>.mdf;Initial Catalog=<project-catalog-id>;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>

    <entityFramework>
      <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
        <parameters>
          <parameter value="mssqllocaldb" />
        </parameters>
      </defaultConnectionFactory>
      <providers>
        <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
      </providers>
    </entityFramework>
    ```

- `Microsoft.Data.Services.Client`、`Microsoft.Data.Edm`、`Microsoft.Data.OData` の `<runtime><assemblyBinding>`ノードの下に `<dependentAssembly>` 要素が追加されます。

## <a name="code-changes-and-additions"></a>コードの変更と追加

- `[Authorize]` 属性が `Controllers/HomeController.cs` および他のすべての既存コントローラーに追加されます。

- Azure AD 認証のスタートアップ ロジックを含む認証スタートアップ クラス `App_Start/Startup.Auth.cs` が追加されます。 **[ディレクトリ データの読み取り]** オプションを選択した場合は、OAuth コードを受け取ってアクセス トークンと交換するコードがこのファイルにも含まれます。

- コントローラー クラス `Controllers/AccountController.cs` が追加されます。これには `SignIn` メソッドと `SignOut` メソッドが含まれます。

- 部分ビュー `Views/Shared/_LoginPartial.cshtml` が追加されます。これには、`SignIn` と `SignOut` のアクション リンクが含まれます。

- 部分ビュー `Views/Account/SignoutCallback.cshtml` が含まれます。これにはサインアウト UI の HTML が含まれます。

- クラスが既に存在する場合は、`Startup.Configuration` メソッドが `ConfigureAuth(app)` の呼び出しを含むように更新されます。それ以外の場合は、そのメソッドを呼び出す `Startup` クラスが追加されます。

- `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) または `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015) が追加されます。これらには Visual Studio が接続済みサービスの追加を追跡するために使用する情報が含まれます。

- **[ディレクトリ データの読み取り]** オプションを選択した場合は、トークンのキャッシュをサポートするために `Models/ADALTokenCache.cs` と `Models/ApplicationDbContext.cs` が追加されます。 Azure Graph API (`Controllers/UserProfileController.cs`、`Views/UserProfile/Index.cshtml`、`Views/UserProfile/Relogin.cshtml`) を使用するユーザー プロファイル情報へのアクセスについて説明するために、コント ローラーとビューも追加されました。

### <a name="file-backup-visual-studio-2015"></a>ファイル バックアップ (Visual Studio 2015)

接続済みサービスを追加するとき、Visual Studio 2015 は変更されるファイルや削除されるファイルをバックアップします。 影響を受けるすべてのファイルがフォルダー `Backup/AzureAD` に保存されます。 Visual Studio 2017 以降ではバックアップは作成されません。

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Azure での変更

- 接続済みサービスを追加するときに選択したドメインに Azure AD アプリケーションが作成されます。
- **[ディレクトリ データの読み取り]** オプションを選択した場合は、このアクセス許可を含むようにアプリが更新されます。

[Azure Active Directory の詳細を確認します](https://azure.microsoft.com/services/active-directory/)。

## <a name="next-steps"></a>次の手順

- [Azure AD の認証シナリオ](authentication-scenarios.md)
- [ASP.NET Web アプリへの "Microsoft でサインイン" の追加](quickstart-v1-aspnet-webapp.md)

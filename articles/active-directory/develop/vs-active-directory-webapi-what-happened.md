---
title: Azure AD への接続時に WebAPI プロジェクトに行われる変更
description: Visual Studio を使用して Azure AD に接続した場合の WebAPI プロジェクトの変更内容の説明
services: active-directory
author: ghogen
manager: douge
ms.service: active-directory
ms.subservice: develop
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19a36ca4b194d8031b4a263a092ecb52be74cdd0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324287"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>WebAPI プロジェクトの変更点 (Visual Studio Azure Active Directory 接続済みサービス)

> [!div class="op_single_selector"]
> - [作業の開始](vs-active-directory-webapi-getting-started.md)
> - [変更内容](vs-active-directory-webapi-what-happened.md)

この記事では、[Visual Studio を使用して Azure Active Directory 接続サービスを](vs-active-directory-add-connected-service.md)追加したときに、ASP.NET WebAPI、ASP.NET シングルページ アプリケーション、ASP.NET Azure API プロジェクトに対して行われる正確な変更内容を説明します。 Visual Studio 2015 の ASP.NET Azure モバイル サービス プロジェクトにも適用されます。

接続済みサービスを使用する方法については、[使用開始](vs-active-directory-webapi-getting-started.md)に関する記事をご覧ください。

## <a name="added-references"></a>追加された参照

プロジェクト ファイル (*.NET 参照) と `packages.config` (NuGet 参照) に影響します。

| Type | リファレンス |
| --- | --- |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.ActiveDirectory |
| .NET; NuGet | Microsoft.Owin.Security.Jwt |
| .NET; NuGet | Microsoft.Owin.Security.OAuth |
| .NET; NuGet | Owin |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |

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
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(Visual Studio 2015 のみ) |
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
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Visual Studio 2017 のみ: `<appSettings>` に次のエントリも追加されます。

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- `System.IdentityModel.Tokens.Jwt` の `<runtime><assemblyBinding>` ノードの下に `<dependentAssembly>` 要素が追加されます。

- **[ディレクトリ データの読み取り]** オプションを選択した場合、`<appSettings>` の下に次の構成エントリが追加されます。

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>コードの変更と追加

- `[Authorize]` 属性が `Controllers/ValueController.cs` および他のすべての既存コントローラーに追加されます。

- Azure AD 認証のスタートアップ ロジックを含む認証スタートアップ クラス `App_Start/Startup.Auth.cs` が追加されます。または、そのように変更されます。 **[ディレクトリ データの読み取り]** オプションを選択した場合は、OAuth コードを受け取ってアクセス トークンと交換するコードがこのファイルにも含まれます。

- (ASP.NET 4 アプリを使用する Visual Studio 2015 のみ) `App_Start/IdentityConfig.cs` が削除され、`Controllers/AccountController.cs`、`Models/IdentityModel.cs`、`Providers/ApplicationAuthProvider.cs` が追加されます。

- `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) または `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015) が追加されます。これらには Visual Studio が接続済みサービスの追加を追跡するために使用する情報が含まれます。

### <a name="file-backup-visual-studio-2015"></a>ファイル バックアップ (Visual Studio 2015)

接続済みサービスを追加するとき、Visual Studio 2015 は変更されるファイルや削除されるファイルをバックアップします。 影響を受けるすべてのファイルがフォルダー `Backup/AzureAD` に保存されます。 Visual Studio 2017 ではバックアップは作成されません。

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Azure での変更

- 接続済みサービスを追加するときに選択したドメインに Azure AD アプリケーションが作成されます。
- **[ディレクトリ データの読み取り]** オプションを選択した場合は、このアクセス許可を含むようにアプリが更新されます。

[Azure Active Directory の詳細を確認します](https://azure.microsoft.com/services/active-directory/)。

## <a name="next-steps"></a>次の手順

- [Azure AD の認証シナリオ](authentication-scenarios.md)
- [ASP.NET Web アプリへの "Microsoft でサインイン" の追加](quickstart-v1-aspnet-webapp.md)

---
title: "Azure AD への接続時に WebApi プロジェクトに行われる変更 | Microsoft Docs"
description: "Visual Studio を使用して Azure AD に接続した場合の WebApi プロジェクトの変更内容の説明"
services: active-directory
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 8ca3234e54d8736aa187195640a103ac6eb145e7
ms.lasthandoff: 03/21/2017


---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Web API プロジェクトの変更点 (Visual Studio Azure Active Directory 接続済みサービス)
> [!div class="op_single_selector"]
> * [作業の開始](vs-active-directory-webapi-getting-started.md)
> * [変更内容](vs-active-directory-webapi-what-happened.md)
> 
> 

## <a name="references-have-been-added"></a>リファレンスが追加されました
### <a name="nuget-package-references"></a>NuGet パッケージのリファレンス
* `Microsoft.Owin`
* `Microsoft.Owin.Host.SystemWeb`
* `Microsoft.Owin.Security`
* `Microsoft.Owin.Security.ActiveDirectory`
* `Microsoft.Owin.Security.Jwt`
* `Microsoft.Owin.Security.OAuth`
* `Owin`
* `System.IdentityModel.Tokens.Jwt`

### <a name="net-references"></a>.NET のリファレンス
* `Microsoft.Owin`
* `Microsoft.Owin.Host.SystemWeb`
* `Microsoft.Owin.Security`
* `Microsoft.Owin.Security.ActiveDirectory`
* `Microsoft.Owin.Security.Jwt`
* `Microsoft.Owin.Security.OAuth`
* `Owin`
* `System.IdentityModel.Tokens.Jwt`

## <a name="code-changes"></a>コードの変更
### <a name="code-files-were-added-to-your-project"></a>コード ファイルがプロジェクトに追加された
認証スタートアップ クラス **App_Start/Startup.Auth.cs** が Azure AD 認証のスタートアップ ロジックを含むプロジェクトに追加されました。

### <a name="startup-code-was-added-to-your-project"></a>スタートアップ コードがプロジェクトに追加された
既にプロジェクトに Startup クラスがある場合、**Configuration`ConfigureAuth(app)` メソッドが更新されて** 呼び出しが含まれています。 それ以外の場合は、Startup クラスがプロジェクトに追加されました。

### <a name="your-appconfig-or-webconfig-file-has-new-configuration-values"></a>app.config ファイルまたは web.config ファイルに新しい構成値が含まれる
次の構成エントリが追加されました。

```
    <appSettings>
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:Tenant" value="Your selected Azure AD Tenant" />
            <add key="ida:Audience" value="The App ID Uri from the wizard" />
    </appSettings>`
```

### <a name="an-azure-ad-app-was-created"></a>Azure AD アプリが作成された
ウィザードで選択したディレクトリに Azure AD アプリケーションが作成されました。

[Azure Active Directory の詳細を確認する](https://azure.microsoft.com/services/active-directory/)

## <a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>*[個々のユーザー アカウントの認証を無効にする]*がオンになっている場合、プロジェクトにはどのような変更が加えられますか。
NuGet パッケージのリファレンスが削除されるほか、ファイルが削除およびバックアップされます。 プロジェクトの状態によっては、追加のリファレンスやファイルを手動で削除したり、必要に応じてコードを変更しなければならない場合があります。

### <a name="nuget-package-references-removed-for-those-present"></a>削除される NuGet パッケージのリファレンス (存在する場合)
* `Microsoft.AspNet.Identity.Core`
* `Microsoft.AspNet.Identity.EntityFramework`
* `Microsoft.AspNet.Identity.Owin`

### <a name="code-files-backed-up-and-removed-for-those-present"></a>バックアップおよび削除されるコード ファイル (存在する場合)
以下の各ファイルがバックアップされ、プロジェクトから削除されます。 バックアップ ファイルは、プロジェクト ディレクトリのルートにある "Backup" フォルダーに配置されます。

* `App_Start\IdentityConfig.cs`
* `Controllers\AccountController.cs`
* `Controllers\ManageController.cs`
* `Models\IdentityModels.cs`
* `Providers\ApplicationOAuthProvider.cs`

### <a name="code-files-backed-up-for-those-present"></a>バックアップされるコード ファイル (存在する場合)
以下の各ファイルがバックアップされてから置き換えられます。 バックアップ ファイルは、プロジェクト ディレクトリのルートにある "Backup" フォルダーに配置されます。

* `Startup.cs`
* `App_Start\Startup.Auth.cs`

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>*[ディレクトリ データの読み取り]*がオンになっている場合、プロジェクトにはどのような変更が加えられますか。
### <a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>app.config または web.config にさらに変更が加えられた
次の構成エントリがさらに追加されました。

```
    <appSettings>
        <add key="ida:Password" value="Your Azure AD App's new password" />
    </appSettings>`
```

### <a name="your-azure-active-directory-app-was-updated"></a>Azure Active Directory アプリが更新された
Azure Active Directory アプリが更新され、*[ディレクトリ データの読み取り]* アクセス許可が含まれるようになりました。また、`web.config` ファイルで *ida:Password* として使用される追加のキーが作成されました。

## <a name="next-steps"></a>次のステップ
- [Azure Active Directory の詳細を確認する](https://azure.microsoft.com/services/active-directory/)



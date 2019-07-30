---
title: v1.0 アプリケーションのスコープ (Microsoft Authentication Library) | Azure
description: Microsoft Authentication Library (MSAL) を使用した v1.0 アプリケーションのスコープについて説明します。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e43bc245a5908ba1bf91e7b4bee6df2f5cfc618
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514367"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>v1.0 トークンを受け入れる Web API のスコープ

OAuth 2 アクセス許可は、開発者向け Azure AD (v1.0) Web API (リソース) アプリケーションでクライアント アプリケーションに公開するアクセス許可スコープです。 これらのアクセス許可スコープは、同意中にクライアント アプリケーションに付与できます。 [Azure Active Directory アプリケーション マニフェスト リファレンス](reference-app-manifest.md#manifest-reference)に関するページの `oauth2Permissions` についてのセクションを参照してください。

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>v1.0 アプリケーションの特定の OAuth2 アクセス許可へのアクセス権を要求するスコープ
v1.0 アプリケーション (たとえば Azure AD Graph。https: \//graph.windows.net) の特定のスコープのためにトークンを取得する場合、目的のリソース ID とそのリソースの目的の OAuth2 アクセス許可を連結して、スコープを作成する必要はありません。

たとえば、ユーザーに代わって、アプリ ID URI が `ResourceId` である v1.0 Web API にアクセスするには次のように指定します。

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Azure AD Graph API (https:\//graph.windows.net/) を使用して、MSAL.NET Azure Active Directory で読み取りと書き込みを行う場合、次のようにスコープ リストを作成します。

```csharp
string ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.windows.net/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Azure Resource Manager API (https:\//management.core.windows.net/) に対応するスコープを書き込む場合は、次のスコープを要求する必要があります (2 つのスラッシュに注意してください)。

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Azure Resource Manager API ではその対象ユーザーの要求 (aud) でスラッシュが予期されるため、2 つのスラッシュを使用する必要があり、したがって、API 名をスコープと区別するためのスラッシュがあります。

Azure AD で使用されるロジックは次のとおりです。

- v1.0 アクセス トークン (使用可能な場合のみ) を使用する ADAL (v1.0) エンドポイントの場合、aud=resource となります
- v2.0 トークンを受け入れるリソースのためにアクセス トークンを要求する MSAL (Microsoft ID プラットフォーム (v2.0) エンドポイント) の場合は、aud=resource.AppId となります
- v1.0 アクセス トークンを受け入れるリソースのためにアクセス トークンを要求する MSAL (v2.0 エンドポイント) の場合 (上記の例の場合)、Azure AD では、最後のスラッシュの前のすべてを取得し、それをリソース ID として使用することで、要求されたスコープからの目的の対象ユーザーを解析します。 そのため、https:\//database.windows.net で "https:\//database.windows.net/" の対象ユーザーを必要とする場合、https:\//database.windows.net//.default のスコープを要求する必要があります。 GitHub の問題「[#747:リソース URL の末尾のスラッシュが省略されたため、SQL 認証エラーが発生した](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)」も参照してください。

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>v1.0 アプリケーションのすべてのアクセス許可へのアクセス権を要求するスコープ
v1.0 アプリケーションのすべての静的なスコープのためにトークンを取得する場合は、API のアプリ ID URI に "default" を付加します。

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-client-credential-flow--daemon-app"></a>クライアント資格証明フロー/デーモン アプリ用に要求するスコープ
クライアント資格情報フローの場合、`/.default` スコープも渡します。 これにより、管理者がアプリケーションの登録で同意したアプリレベルのすべてのアクセス許可が、Azure AD に示されます。

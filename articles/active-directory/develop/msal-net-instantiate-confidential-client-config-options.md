---
title: オプションを使用して機密クライアント アプリをインスタンス化する (.NET 用 Microsoft Authentication Library) | Azure
description: .NET 用 Microsoft Authentication Library (MSAL.NET) を使用して、構成オプションで機密クライアント アプリケーションをインスタンス化する方法について説明します。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7814ff6b7575fedc19e63676ce3353c2a62a62b4
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154439"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>MSAL.NET を使用して、構成オプションで機密クライアント アプリケーションをインスタンス化する

この記事では、.NET 用 Microsoft Authentication Library (MSAL.NET) を使用して[機密クライアント アプリケーション](msal-client-applications.md)を初期化する方法について説明します。  アプリケーションは、設定ファイルで定義されている構成オプションを使用してインスタンス化されます。

アプリケーションを初期化する前に、まず、そのアプリケーションを[登録](quickstart-register-app.md)して、Microsoft ID プラットフォームに統合できるようにする必要があります。 登録後に、次の情報が必要な場合があります (Azure portal で検索できます)。

- クライアント ID (GUID を表す文字列)
- ID プロバイダーの URL (名前付きインスタンス) とアプリケーションのサインイン対象ユーザー。 これら 2 つのパラメーターは機関と総称されます。
- 組織専用の基幹業務アプリケーション (および名前付きシングル テナント アプリケーション) を作成している場合はテナント ID。
- アプリケーション シークレット (クライアント シークレット文字列) または機密クライアント アプリの場合は (X509Certificate2 種類の) 証明書。
- Web アプリ、および場合によってはパブリック クライアント アプリの場合 (特に、アプリでブローカーを使用する必要がある場合)、ID プロバイダーがセキュリティ トークンを使用してアプリケーションに連絡する redirectUri も設定します。

## <a name="configure-the-application-from-the-config-file"></a>構成ファイルからアプリケーションを構成する
MSAL.NET のオプションのプロパティの名前は、ASP.NET Core の `AzureADOptions` のプロパティの名前と一致するため、グルー コードを記述する必要はありません。

ASP.NET Core アプリケーションの構成は、*appsettings.json* ファイルに記載されています。

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

MSAL.NET v3.x 以降では、構成ファイルから機密クライアント アプリケーションを構成できます。

アプリケーションを構成してインスタンス化するクラスで、`ConfidentialClientApplicationOptions` オブジェクトを宣言する必要があります。  [Microsoft.Extensions.Configuration.Binder NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder)の `IConfigurationRoot.Bind()` メソッドを使って、ソース (appconfig.json ファイルを含む) から読み取られた構成を、アプリケーション オプションのインスタンスにバインドします。

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

これにより、*appsettings.json* ファイルの "AzureAD" セクションのコンテンツを、`ConfidentialClientApplicationOptions` オブジェクトの対応するプロパティにバインドできます。  次に、`ConfidentialClientApplication` オブジェクトをビルドします。

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>実行時の構成を追加する
機密クライアント アプリケーションでは、通常はユーザーごとにキャッシュが存在します。 そのため、ユーザーに関連付けられたキャッシュを取得し、それを使用することをアプリケーション ビルダーに通知する必要があります。 同様に、動的に計算されたリダイレクト URI が存在する場合があります。 この場合、コードは次のようになります。

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```


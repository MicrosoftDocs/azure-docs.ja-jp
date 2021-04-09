---
title: デスクトップ アプリを呼び出す Web API を運用環境に移行する | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出すデスクトップ アプリを運用環境に移行する方法について説明します
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 22e61ea767d781dc9da54d61143c1b2524e06e94
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584416"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>Web API を呼び出すデスクトップ アプリ:運用環境に移行する

この記事では、Web API を呼び出すデスクトップ アプリを運用環境に移行する方法について説明します。

## <a name="handle-errors-in-desktop-applications"></a>デスクトップ アプリケーションでのエラー処理

別のフローでは、コード スニペットに示されているように、サイレント フローのエラーを処理する方法を説明しました。 また、増分同意や条件付きアクセスの場合などに、対話が必要となるケースがあることも説明しました。

## <a name="have-the-user-consent-upfront-for-several-resources"></a>複数のリソースでユーザーの同意を事前に取得する

> [!NOTE]
> 複数のリソースにおける同意の事前取得は、Microsoft ID プラットフォームでは機能しますが、Azure Active Directory (Azure AD) B2C では機能しません。 Azure AD B2C では、管理者による承認のみがサポートされており、ユーザーによる承認はサポートされていません。

Microsoft ID プラットフォームでは、複数のリソースのトークンを一度に取得することはできません。 `scopes` パラメーターには、1 つリソースのみのスコープを含めることができます。 `extraScopesToConsent` パラメーターを使用して、ユーザーが複数のリソースに事前に同意するようにできます。

たとえば、2 つのリソースがあり、それぞれに 2 つのスコープがあるとします。

- `https://mytenant.onmicrosoft.com/customerapi` のスコープは `customer.read` と `customer.write` です
- `https://mytenant.onmicrosoft.com/vendorapi` のスコープは `vendor.read` と `vendor.write` です

この例では、`extraScopesToConsent` パラメーターを持つ `.WithExtraScopesToConsent` 修飾子を使用します。

次に例を示します。

### <a name="in-msalnet"></a>MSAL.NET の場合

```csharp
string[] scopesForCustomerApi = new string[]
{
  "https://mytenant.onmicrosoft.com/customerapi/customer.read",
  "https://mytenant.onmicrosoft.com/customerapi/customer.write"
};
string[] scopesForVendorApi = new string[]
{
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"
};

var accounts = await app.GetAccountsAsync();
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithAccount(accounts.FirstOrDefault())
                     .WithExtraScopesToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

### <a name="in-msal-for-ios-and-macos"></a>iOS および macOS 用の MSAL の場合

Objective-C:

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.onmicrosoft.com/customerapi/customer.read",
                                @"https://mytenant.onmicrosoft.com/customerapi/customer.write"];

NSArray *scopesForVendorApi = @[@"https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                              @"https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

Swift:

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

この呼び出しでは、最初の Web API のアクセス トークンを取得します。

2 番目の Web API を呼び出す場合は、`AcquireTokenSilent` API を呼び出します。

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>Microsoft 個人アカウントではアプリを実行するたびに再同意が必要

Microsoft 個人アカウントのユーザーの場合、承認のためのネイティブ クライアント (デスクトップまたはモバイル アプリ) の呼び出しごとに同意のプロンプトが再表示されますが、これは意図的な動作です。 ネイティブ クライアント ID は本質的に安全ではありません。これは、機密性の高いクライアント アプリケーション ID とは異なります。 機密性の高いクライアント アプリケーションは、その ID を証明するために、Microsoft ID プラットフォームとシークレットを交換します。 Microsoft ID プラットフォームは、アプリケーションが承認されるたびにユーザーに同意を求めることで、コンシューマー サービスにおけるこのような非安全性を軽減することを選択しました。

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>次のステップ

追加のサンプルを試すには、「[デスクトップおよびモバイルのパブリック クライアント アプリ](sample-v2-code.md#desktop-and-mobile-public-client-apps)」を参照してください。




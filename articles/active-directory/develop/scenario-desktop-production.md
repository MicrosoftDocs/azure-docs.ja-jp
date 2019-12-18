---
title: Web API を呼び出すデスクトップ アプリを運用環境に移行する - Microsoft ID プラットフォーム | Azure
description: Web API を呼び出すデスクトップ アプリを運用環境に移行する方法について説明します
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81a5e57d0ef37a3a2e758e42f122c8e014c94958
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919985"
---
# <a name="desktop-app-that-calls-web-apis---move-to-production"></a>Web API を呼び出すデスクトップ アプリ - 運用環境への移行

この記事では、Web API を呼び出すデスクトップ アプリを運用環境に移行する方法について詳しく説明します。

## <a name="handling-errors-in-desktop-applications"></a>デスクトップ アプリケーションでのエラー処理

別のフローでは、(コード スニペットに示されているように) サイレント フローのエラーを処理する方法を説明しました。 また、対話が必要となるケース (増分同意と条件付きアクセス) があることも説明しました。

## <a name="how-to-have--the-user-consent-upfront-for-several-resources"></a>複数のリソースでユーザーの同意を事前に取得する方法

> [!NOTE]
> 複数のリソースにおける同意の事前取得は、Microsoft ID プラットフォームでは機能しますが、Azure Active Directory (Azure AD) B2C では機能しません。 Azure AD B2C では、管理者の同意のみサポートされており、ユーザーの同意はサポートされていません。

Microsoft ID プラットフォーム (v2.0) のエンドポイントでは、複数のリソースのトークンを一度に取得することはできません。 そのため、`scopes` パラメーターには、1 つのリソースのスコープのみを含めることができます。 `extraScopesToConsent` パラメーターを使用して、ユーザーが複数のリソースに事前に同意するようにできます。

たとえば、2 つのリソースがあり、それぞれに 2 つのスコープがある場合:

- `https://mytenant.onmicrosoft.com/customerapi` - `customer.read` と `customer.write` の 2 のスコープがあります
- `https://mytenant.onmicrosoft.com/vendorapi` - `vendor.read` と `vendor.write` の 2 のスコープがあります

`extraScopesToConsent` パラメーターを持つ `.WithAdditionalPromptToConsent` 修飾子を使用する必要があります。

次に例を示します。

### <a name="in-msalnet"></a>MSAL.NET の場合

```CSharp
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
                     .WithExtraScopeToConsent(scopesForVendorApi)
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

2 番目の Web API を呼び出す必要がある場合は、`AcquireTokenSilent` API を呼び出せます。

```CSharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsenting-each-time-the-app-is-run"></a>Microsoft 個人アカウントでは、アプリを実行するたびに再同意が必要

Microsoft 個人アカウントのユーザーの場合、承認のためのネイティブ クライアント (デスクトップ/モバイル アプリ) の呼び出しごとに同意のプロンプトが再表示されますが、これは意図的な動作です。 ネイティブ クライアントの ID は本質的に安全ではありません (これは、Microsoft ID プラットフォームとシークレットを交換して身元を証明する機密クライアント アプリケーションとは対照的です)。 Microsoft ID プラットフォームは、アプリケーションが承認されるたびにユーザーに同意を求めることで、コンシューマー サービスにおけるこのような非安全性を軽減することを選択しました。

## <a name="next-steps"></a>次の手順

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

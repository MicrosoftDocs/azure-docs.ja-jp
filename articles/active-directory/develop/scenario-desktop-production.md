---
title: Web API を呼び出すデスクトップ アプリ (運用環境への移行) - Microsoft ID プラットフォーム
description: Web API を呼び出すデスクトップ アプリを構築する方法 (運用環境への移行) について説明します
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
ms.date: 04/18/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2343a416bd810792e7267b94395f953aa4f880a1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111200"
---
# <a name="desktop-app-that-calls-web-apis---move-to-production"></a>Web API を呼び出すデスクトップ アプリ - 運用環境への移行

この記事では、アプリケーションをさらに改善し、運用環境に移行するための詳細を説明します。

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

この呼び出しでは、最初の Web API のアクセス トークンを取得します。

2 番目の Web API を呼び出す必要がある場合は、以下のように呼び出せます。

```CSharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsenting-each-time-the-app-is-run"></a>Microsoft 個人アカウントでは、アプリを実行するたびに再同意が必要

Microsoft 個人アカウントのユーザーの場合、承認のためのネイティブ クライアント (デスクトップ/モバイル アプリ) の呼び出しごとに同意のプロンプトが再表示されますが、これは意図的な動作です。 ネイティブ クライアントの ID は本質的に安全ではありません (これは、Microsoft ID プラットフォームとシークレットを交換して身元を証明する機密クライアント アプリケーションとは対照的です)。 Microsoft ID プラットフォームは、アプリケーションが承認されるたびにユーザーに同意を求めることで、コンシューマー サービスにおけるこのような非安全性を軽減することを選択しました。

## <a name="next-steps"></a>次の手順

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

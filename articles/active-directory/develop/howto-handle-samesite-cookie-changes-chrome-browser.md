---
title: Chrome ブラウザーにおける SameSite Cookie の変更を処理する方法 | Azure
titleSuffix: Microsoft identity platform
description: Chrome ブラウザーにおける SameSite Cookie の変更の処理について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2020
ms.author: jmprieur
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: 30c4f054259aa7c3f2a9fdfaeeadd64f26dd9bea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94444913"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>Chrome ブラウザーにおける SameSite Cookie の変更の処理

## <a name="what-is-samesite"></a>SameSite とは

`SameSite` は、Web アプリケーションでのクロスサイト リクエスト フォージェリ (CSRF) 攻撃を防ぐために、HTTP Cookie で設定できるプロパティです。

- `SameSite` が **Lax** に設定されている場合、Cookie は同じサイト内の要求と、他のサイトからの GET 要求で送信されます。 ドメインを越えた GET 要求では送信されません。
- **Strict** の値を指定すると、同じサイト内の要求でのみ Cookie が送信されることが保証されます。

既定では、`SameSite` の値はブラウザーで設定されていません。そのため、要求で送信される Cookie に制限はありません。 アプリケーションは、要件に応じて **Lax** または **Strict** を設定して、CSRF 保護をオプトインする必要があります。

## <a name="samesite-changes-and-impact-on-authentication"></a>SameSite の変更と認証への影響

最近行われた [SameSite の標準への更新](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00)では、Lax に設定されている値が 1 つもない場合、`SameSite` の既定の動作を行うことでアプリを保護することが提案されています。 この軽減策は、 Cookie が、他のサイトから行われた GET 以外の HTTP 要求に制限されることを意味します。 また、送信される Cookie に対する制限を除去するために、**None** の値が導入されました。 これらの更新は間もなく、Chrome ブラウザーの今後のバージョンでリリースされる予定です。

Web アプリが応答モード "form_post" を使用して Microsoft ID プラットフォームで認証すると、ログイン サーバーは、トークンまたは認証コードを送信するために、HTTP POST を使用してアプリケーションに応答します。 この要求はドメイン間要求 (`login.microsoftonline.com` から自分のドメイン、たとえば `https://contoso.com/auth`) のため、お使いのアプリによって設定された Cookie は、Chrome の新しいルールに該当するようになりました。 クロスサイトのシナリオで使用する必要がある Cookie は、 *state* と *nonce* の値を保持する Cookie で、これはログイン要求でも送信されす。 セッションを保持するために Azure AD によって削除された他の Cookie があります。

Web アプリを更新しないと、この新しい動作によって認証エラーが発生します。

## <a name="mitigation-and-samples"></a>軽減策とサンプル

認証エラーが発生しないようにするために、Microsoft ID プラットフォームで認証を行う Web アプリで、Chrome ブラウザーでの実行時にクロスドメイン シナリオで使用される Cookie の `SameSite` プロパティを `None` に設定できます。
その他のブラウザー (完全な一覧については[こちら](https://www.chromium.org/updates/same-site/incompatible-clients)を参照) は、`SameSite` の以前の動作に従い、`SameSite=None` が設定されている場合は Cookie が含まれません。
そのため、複数のブラウザーでの認証をサポートするには、Web アプリで、Chrome に対してのみ `SameSite` 値を `None` に設定し、他のブラウザーに対してはこの値を空のままにする必要があります。

以下のコード サンプルで、この手法の例を示します。

# <a name="net"></a>[.NET](#tab/dotnet)

次の表は、ASP.NET と ASP.NET Core サンプルでの SameSite の変更を回避する pull request を示しています。

| サンプル | Pull request |
| ------ | ------------ |
|  [ASP.NET Core Web アプリの増分チュートリアル](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [SameSite Cookie の修正 #261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [ASP.NET MVC Web アプリのサンプル](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [SameSite Cookie の修正 #35](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [active-directory-dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [SameSite Cookie の修正 #28](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

ASP.NET と ASP.NET Core で SameSite cookie を処理する方法の詳細については、以下も参照してください。

- [ASP.NET Core での SameSite cookie の使用](/aspnet/core/security/samesite)。
- [SameSite の問題に関する ASP.NET ブログ](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="python"></a>[Python](#tab/python)

| サンプル |
| ------ |
|  [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="java"></a>[Java](#tab/java)

| サンプル | Pull request |
| ------ | ------------ |
|  [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [SameSite Cookie の修正 #24](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [SameSite Cookie の修正 #4](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>次のステップ

SameSite と Web アプリのシナリオの詳細について学習します。

- [Google Chrome の SameSite に関する FAQ](https://www.chromium.org/updates/same-site/faq)

- [Chromium の SameSite ページ](https://www.chromium.org/updates/same-site)

- [シナリオ: ユーザーをサインインさせる Web アプリ](scenario-web-app-sign-user-overview.md)
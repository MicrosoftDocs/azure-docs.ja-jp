---
title: 開発するアプリで FIDO2 キーを使用してパスワードレス認証をサポートする | Azure
titleSuffix: Microsoft identity platform
description: このデプロイ ガイドでは、開発するアプリケーションの FIDO2 セキュリティ キーを使用してパスワードレス認証をサポートする方法について説明します。
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 1/29/2021
ms.author: nichola
ms.custom: aaddev
ms.openlocfilehash: f63d7aed75b14f5f008a639d667d8806b233b9fa
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174600"
---
# <a name="support-passwordless-authentication-with-fido2-keys-in-apps-you-develop"></a>開発するアプリで FIDO2 キーを使用してパスワードレス認証をサポートする

これらの構成とベスト プラクティスは、アプリケーションのユーザーが [FIDO2 パスワードレス認証](../../active-directory/authentication/concept-authentication-passwordless.md)を利用できない一般的なシナリオを回避するのに役立ちます。

## <a name="general-best-practices"></a>全般的なベスト プラクティス

### <a name="domain-hints"></a>ドメイン ヒント

[ホーム領域検出](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md)をバイパスするには、ドメイン ヒントを使用しないでください。 この機能は、サインインの効率を向上させることを目的としていますが、フェデレーション ID プロバイダーがパスワードレス認証をサポートしていない可能性があります。

### <a name="requiring-specific-credentials"></a>特定の資格情報を要求する

SAML を使用している場合は、[RequestedAuthnContext 要素を使用して](single-sign-on-saml-protocol.md#requestauthncontext)パスワードが必要であることを指定しないでください。

RequestedAuthnContext 要素は省略可能であるため、これを解決するために SAML 認証要求から削除できます。 これは一般的なベストプラクティスです。この要素を使用すると、多要素認証などの他の認証オプションが正常に機能しなくなる可能性があります。

### <a name="using-the-most-recently-used-authentication-method"></a>最近使用した認証方法を使用する

ユーザーによって最近使用されたサインイン方法が最初に表示されます。 このため、ユーザーが最初に提示されたオプションを使用しなければならないと考えたときに混乱が生じる可能性があります。 ただし、次に示すように、[その他のサインイン方法] を選択すると、別のオプションを選択できます。

:::image type="content" source="./media/support-fido2-authentication/most-recently-used-method.png" alt-text="ユーザーが認証方法を変更できるようにするボタンを強調表示したユーザー認証エクスペリエンスの画像。":::

## <a name="platform-specific-best-practices"></a>プラットフォーム固有のベスト プラクティス

### <a name="desktop"></a>デスクトップ

認証を実装するための推奨されるオプションは、順番に次のとおりです。

- Microsoft Authentication Library (MSAL) を使用する .NET デスクトップ アプリケーションでは、Windows Authentication Manager (WAM) を使用する必要があります。 この統合とその利点については、[GitHub 上のドキュメント](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/wam)をご覧ください。
- [WebView2](/microsoft-edge/webview2/) を使用して、埋め込みブラウザーで FIDO2 をサポートします。
- システム ブラウザーを使用します。 デスクトップ プラットフォーム用の MSAL ライブラリでは、この方法を既定で使用します。 使用するブラウザーが FIDO2 認証をサポートしていることを確認するには、FIDO2 ブラウザーの互換性に関するページを参照してください。

### <a name="mobile"></a>モバイル

2021 年 2 月の時点では、FIDO2 は現在、ネイティブの iOS または Android アプリではサポートされていませんが、開発中です。

利用できるようアプリケーションを準備するため、および一般的なベスト プラクティスとして、iOS および Android アプリケーションでは、システム Web ブラウザーを使用する既定の構成で MSAL を使用する必要があります。

MSAL を使用していない場合でも、認証にはシステム Web ブラウザーを引き続き使用する必要があります。 シングル サインオンや条件付きアクセスなどの機能は、システム Web ブラウザーによって提供される共有 Web サーフェスに依存します。 つまり、[Chrome カスタム タブ](https://developer.chrome.com/docs/multidevice/android/customtabs/)が使用されたり (Android)、[Web サービスを使用してユーザーが認証 (Apple 開発者ドキュメント)](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service) されたり (iOS) するということです。

### <a name="web-and-single-page-apps"></a>Web および単一ページ アプリ

Web ブラウザーで実行されるアプリケーションに対して FIDO2 パスワードレス認証を利用できるかどうかは、ブラウザーとプラットフォームの組み合わせによって異なります。 [FIDO2 の互換性マトリックス](../authentication/fido2-compatibility.md)を参照して、ユーザー側で発生する組み合わせがサポートされているかどうかを確認できます。

## <a name="next-steps"></a>次のステップ

[Azure Active Directory のパスワードレス認証オプション](../../active-directory/authentication/concept-authentication-passwordless.md)
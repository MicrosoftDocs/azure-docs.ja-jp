---
title: 認証と承認 | Azure
titleSuffix: Microsoft identity platform
description: Microsoft ID プラットフォーム (v2.0) の認証と承認の基本について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 095bc5594cf9b6eaf0df7929ea83c25a401793e2
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86252115"
---
# <a name="authentication-vs-authorization"></a>認証と承認

この記事では、認証と承認を定義し、Microsoft ID プラットフォームを使用して、Web アプリ、Web API、または保護された Web API を呼び出すアプリのユーザーを認証および承認する方法について簡単に説明します。 よく知らない用語がある場合は、[用語集](developer-glossary.md)または基本的な概念を説明する [Microsoft ID プラットフォームの動画](identity-videos.md)をご覧ください。

## <a name="authentication"></a>認証

**認証**は、ユーザーが身元を証明するプロセスです。 認証は AuthN と短縮される場合があります。 Microsoft ID プラットフォームは、認証を処理するための [OpenID Connect](https://openid.net/connect/) プロトコルを実装しています。

## <a name="authorization"></a>承認

**承認**は、認証された利用者に対し、何かを実行する権限を付与する行為です。 アクセスが許可されるデータと、そのデータで実行できる操作を指定します。 承認は AuthZ と短縮される場合があります。 Microsoft ID プラットフォームは、承認を処理するための [OAuth 2.0](https://oauth.net/2/) プロトコルを実装しています。

## <a name="authentication-and-authorization-using-microsoft-identity-platform"></a>Microsoft ID プラットフォームを使用した認証と承認

それぞれ独自のユーザー名とパスワードの情報を保持するアプリを作成する (これにより、複数のアプリにまたがってユーザーを追加または削除する必要があるときに管理上の負担が大きくなります) 代わりに、アプリで、一元化された ID プロバイダーに責任を委任することができます。

Azure Active Directory (Azure AD) は、クラウド内の一元化された ID プロバイダーです。 認証と承認を委任することにより、ユーザーが特定の場所にいることを要求する条件付きアクセス ポリシー、[多要素認証](../authentication/concept-mfa-howitworks.md) (2 要素認証または 2FA とも呼ばれる) の使用のほか、ユーザーが 1 回サインインすると、同じ一元化されたディレクトリを共有するすべての Web アプリに自動的にサインインできるようにするなどのシナリオを実現できます。 この機能は、**シングル サインオン (SSO)** と呼ばれます。

Microsoft ID プラットフォームでは、サービスとしての ID を提供することでアプリケーション開発者による承認と認証を容易にしています。また、コーディングをすばやく開始できるように、OAuth 2.0 や OpenID Connect などの業界標準プロトコルや、さまざまなプラットフォーム向けのオープン ソース ライブラリをサポートしています。 そのため、開発者はすべての Microsoft ID にサインインし、[Microsoft Graph](https://developer.microsoft.com/graph/)、その他の Microsoft API、または開発者が作成した API を呼び出すトークンを取得できます。

Microsoft ID プラットフォームで使用されるさまざまなプロトコルの簡単な比較を次に示します。

* **OAuth と OpenID Connect**:OAuth が承認に使用され、OpenID Connect (OIDC) が認証に使用されます。 OpenID Connect は OAuth 2.0 に基づいて構築されているため、これら 2 つの間では用語とフローが似ています。 さらに、(OpenID Connect を使用して) ユーザーを認証することと、(OAuth 2.0 を使用して) ユーザーが所有する保護されたリソースにアクセスするための承認を得ることを、1 回の要求で行うこともできます。 詳細については、[OAuth 2.0 と OpenID Connect プロトコル](active-directory-v2-protocols.md)および [OpenID Connect プロトコル](v2-protocols-oidc.md)に関するページを参照してください。
* **OAuth と SAML**:OAuth が承認に使用され、SAML が認証に使用されます。 2 つのプロトコルを一緒に使用して、(SAML を使用して) ユーザーを認証し、さらに (OAuth 2.0 を使用して) 保護されたリソースにアクセスするための承認を得る方法の詳細については、「[Microsoft ID プラットフォームと OAuth 2.0 SAML ベアラー アサーション フロー](v2-saml-bearer-assertion.md)」を参照してください。
* **OpenID Connect と SAML**:OpenID Connect と SAML の両方が、ユーザーの認証に使用され、シングル サインオンを有効にするために使用されます。 SAML 認証は一般的に、Azure AD にフェデレーションされた Active Directory フェデレーション サービス (AD FS) などの ID プロバイダーと一緒に使用されるため、エンタープライズ アプリケーションで頻繁に使用されます。 OpenID Connect は一般的に、モバイル アプリ、Web サイト、Web API など、純粋にクラウド内にあるアプリに対して使用されます。

## <a name="next-steps"></a>次のステップ

認証と承認の基本に関するその他のトピックは、次のとおりです。

* 承認と認証でアクセス トークン、更新トークン、ID トークンがどのように使用されるかについては、「[セキュリティ トークン](security-tokens.md)」を参照してください。
* Microsoft ID プラットフォームと統合できるようにアプリケーションを登録するプロセスについては、[アプリケーション モデル](application-model.md)に関する記事を参照してください。
* Microsoft ID プラットフォームでの Web アプリ、デスクトップ アプリ、モバイル アプリのサインイン フローの詳細については、[アプリのサインイン フロー](app-sign-in-flow.md)に関するページを参照してください。

* Microsoft ID プラットフォームによって実装されるプロトコルの詳細については、「[Microsoft ID プラットフォームにおける OAuth 2.0 プロトコルと OpenID Connect プロトコル](active-directory-v2-protocols.md)」を参照してください。
* Microsoft ID プラットフォーム でシングル サインオンをサポートする方法の詳細については、「[シングル サインオンの SAML プロトコル](single-sign-on-saml-protocol.md)」を参照してください。
* アプリでシングル サインオンを実装するためのさまざまな方法の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

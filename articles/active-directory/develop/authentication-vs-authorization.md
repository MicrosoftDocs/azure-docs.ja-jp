---
title: 認証と承認 | Azure
titleSuffix: Microsoft identity platform
description: Microsoft ID プラットフォームの認証と承認の基本について説明します。
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
ms.openlocfilehash: 5244a8e572abb56c00d87d0bdd7e8d1291af9b9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99581909"
---
# <a name="authentication-vs-authorization"></a>認証と承認

この記事では、認証と承認を定義します。 また、Microsoft ID プラットフォームを使用して、Web アプリ、Web API、または保護された Web API を呼び出すアプリのユーザーを認証および承認する方法についても簡単に説明します。 よく知らない用語がある場合は、[用語集](developer-glossary.md)または基本的な概念を説明する [Microsoft ID プラットフォームの動画](identity-videos.md)をご覧ください。

## <a name="authentication"></a>認証

"*認証*" は、ユーザーが身元を証明するプロセスです。 *AuthN* と短縮される場合があります。 Microsoft ID プラットフォームでは、認証の処理に [OpenID Connect](https://openid.net/connect/) プロトコルが使用されます。

## <a name="authorization"></a>承認

*承認* は、認証された利用者に対し、何かを実行する権限を付与する行為です。 アクセスが許可されるデータと、そのデータで実行できる操作を指定します。 承認は *AuthZ* と短縮される場合があります。 Microsoft ID プラットフォームでは、承認の処理に [OAuth 2.0](https://oauth.net/2/) プロトコルが使用されます。

## <a name="authentication-and-authorization-using-the-microsoft-identity-platform"></a>Microsoft ID プラットフォームを使用した認証と承認

それぞれ独自のユーザー名とパスワードの情報を保持するアプリを作成した場合、複数のアプリにまたがってユーザーを追加または削除するときに管理上の負担が大きくなります。 アプリでは、代わりにその責任を一元化された ID プロバイダーに委任できます。

Azure Active Directory (Azure AD) は、クラウド内の一元化された ID プロバイダーです。 認証と承認をこれに委任することで、次のようなシナリオが可能になります。

- ユーザーが特定の場所にいることを求める条件付きアクセス ポリシー。
- [多要素認証](../authentication/concept-mfa-howitworks.md)の使用。2 要素認証または 2FA と呼ばれることもあります。
- ユーザーが一度サインインした後、同じ一元化されたディレクトリを共有するすべての Web アプリに自動的にサインインできるようにする。 この機能は、"*シングル サインオン (SSO)* " と呼ばれます。

Microsoft ID プラットフォームでは、ID をサービスとして提供することで、アプリケーション開発者を対象とする承認と認証が簡略化されます。 コーディングを迅速に開始できるように、さまざまなプラットフォーム用の業界標準プロトコルとオープンソース ライブラリがサポートされています。 これにより、開発者は、すべての Microsoft ID にサインインするアプリケーションの作成、[Microsoft Graph](https://developer.microsoft.com/graph/) を呼び出すためのトークンの取得、Microsoft API へのアクセス、または開発者が作成したその他の API へのアクセスを行うことができます。

次の動画では、Microsoft ID プラットフォームと、先進認証の基本について説明します。 

> [!VIDEO https://www.youtube.com/embed/tkQJSHFsduY]

Microsoft ID プラットフォームで使用されるプロトコルの比較を次に示します。

* **OAuth と OpenID Connect**:このプラットフォームでは、OAuth が承認に使用され、OpenID Connect (OIDC) が認証に使用されます。 OpenID Connect は OAuth 2.0 に基づいて構築されているため、これら 2 つの間では用語とフローが似ています。 さらに、(OpenID Connect を使用して) ユーザーを認証することと、(OAuth 2.0 を使用して) ユーザーが所有する保護されたリソースにアクセスするための承認を得ることを、1 回の要求で行うこともできます。 詳細については、[OAuth 2.0 と OpenID Connect プロトコル](active-directory-v2-protocols.md)および [OpenID Connect プロトコル](v2-protocols-oidc.md)に関するページを参照してください。
* **OAuth と SAML**:このプラットフォームでは、OAuth 2.0 が承認に使用され、SAML が認証に使用されます。 これらのプロトコルを一緒に使用することで、ユーザーの認証および保護されたリソースにアクセスするための承認の取得の両方を行う方法の詳細については、「[Microsoft ID プラットフォームと OAuth 2.0 SAML ベアラー アサーション フロー](./scenario-token-exchange-saml-oauth.md)」を参照してください。
* **OpenID Connect と SAML**:このプラットフォームでは OpenID Connect と SAML の両方が、ユーザーを認証し、シングル サインオンを有効にするために使用されます。 SAML 認証は一般的に、Azure AD にフェデレーションされた Active Directory フェデレーション サービス (AD FS) などの ID プロバイダーと一緒に使用されるため、エンタープライズ アプリケーションで多く使用されます。 OpenID Connect は一般的に、モバイル アプリ、Web サイト、Web API など、純粋にクラウド内にあるアプリに対して使用されます。

## <a name="next-steps"></a>次の手順

認証と承認の基本に関するその他のトピックについて:

* 承認と認証でアクセス トークン、更新トークン、ID トークンがどのように使用されるかについては、「[セキュリティ トークン](security-tokens.md)」を参照してください。
* Microsoft ID プラットフォームと統合できるようにアプリケーションを登録するプロセスについては、[アプリケーション モデル](application-model.md)に関する記事を参照してください。
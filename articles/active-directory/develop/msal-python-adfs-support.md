---
title: Azure AD FS のサポート (MSAL Python)
titleSuffix: Microsoft identity platform
description: Python 用 Microsoft Authentication Library での Active Directory フェデレーション サービス (AD FS) サポートについて説明します。
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/23/2019
ms.author: abpati
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: d847e38a6114b0e5f8d56f6e994aa47dfbe929e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99583707"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>MSAL for Python での Active Directory フェデレーション サービスのサポート

Windows Server で Active Directory フェデレーション サービス (AD FS) を使用すると、Python 用 Microsoft Authentication Library (MSAL) を使用して、アプリに OpenID Connect と OAuth 2.0 ベースの認証と承認を追加できます。 MSAL for Python ライブラリを使用すると、アプリで AD FS に対してユーザーを直接認証できます。 シナリオの詳細については、「[開発者向けの AD FS のシナリオ](/windows-server/identity/ad-fs/ad-fs-development)」をご覧ください。

通常、AD FS に対して認証を行うには、次の 2 つの方法があります。

- MSAL.Python は、それ自体が他の ID プロバイダーとフェデレーションされている Azure Active Directory と通信します。 フェデレーションは AD FS を介して行われます。 MSAL Python は、Azure AD で管理されているユーザー (管理対象ユーザー) または AD FS などの別の ID プロバイダーによって管理されているユーザー (フェデレーション ユーザー) にサインインする Azure AD に接続します。 MSAL Python は、ユーザーがフェデレーションされていることを認識していません。 単に Azure AD と通信します。 この場合に使用する[機関](msal-client-application-configuration.md#authority)は、通常の機関 (機関のホスト名 + テナント、common、または organizations) です。
- MSAL Python は、AD FS 機関と直接通信します。 これは、AD FS 2019 以降でのみサポートされます。

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>AD FS とフェデレーションされている Active Directory への接続

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>フェデレーション ユーザーのトークンを対話形式で取得する

Active Directory フェデレーション サービス (AD FS) に直接接続するか Active Directory 経由で接続するかにかかわらず、次のことが適用されます。

`acquire_token_by_authorization_code` または `acquire_token_by_device_flow` メソッドを呼び出すと、ユーザー エクスペリエンスは通常、次のようになります。

1. ユーザーは自分のアカウント ID を入力します。
2. Azure AD に "組織のに移動します" というメッセージが短く表示され、ユーザーは ID プロバイダーのサインイン ページにリダイレクトされます。 サインイン ページは、通常、組織のロゴでカスタマイズされます。

このフェデレーション シナリオでサポートされている AD FS バージョンは次のとおりです。
- Active Directory フェデレーション サービス FS v2
- Active Directory フェデレーション サービス v3 (Windows Server 2012 R2)
- Active Directory フェデレーション サービス v4 (AD FS 2016)

### <a name="acquire-a-token-via-username-and-password"></a>ユーザー名とパスワードを使用してトークンを取得する

Active Directory フェデレーション サービス (AD FS) に直接接続するか Active Directory 経由で接続するかにかかわらず、次のことが適用されます。

`acquire_token_by_username_password` を使用して、トークンを取得するときに、MSAL Python はユーザー名に基づいて ID プロバイダーと通信します。 MSAL Python は、ID プロバイダーから [SAML 1.1 トークン](reference-saml-tokens.md)を取得します。このトークンは、JSON Web トークン (JWT) を返す Azure AD に提供されます。

## <a name="connecting-directly-to-ad-fs"></a>AD FS への直接接続

AD FS に直接接続する場合、アプリケーションの構築に使用する機関は `https://somesite.contoso.com/adfs/` のようになります。

MSAL Python は、ADFS 2019 をサポートしています。

ADFS 2016 または ADFS v2 への直接接続はサポートされていません。 ADFS 2016 への直接接続が必要なシナリオをサポートする場合は、最新バージョンの ADAL Python を使用してください。 オンプレミスのシステムを ADFS 2019 にアップグレードすると、MSAL Python を使用できるようになります。

## <a name="next-steps"></a>次のステップ

- フェデレーション シナリオについては、「[ホーム領域検出ポリシーを使用して、アプリケーションの Azure Active Directory サインイン動作を構成する](../manage-apps/configure-authentication-for-federated-users-portal.md)」を参照してください。

---
title: Java 用 Microsoft Authentication Library での AD FS サポート
titleSuffix: Microsoft identity platform
description: Java 用 Microsoft Authentication Library (MSAL4j) での Active Directory フェデレーション サービス (AD FS) サポートについて説明します。
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/21/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 665cef55965f6871a654b9baceaad3e4f5d196c7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916657"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>MSAL for Java での Active Directory フェデレーション サービスのサポート

Windows Server で Active Directory フェデレーション サービス (AD FS) を使用すると、Microsoft Authentication Library for Java (MSAL for Java) アプリに OpenID Connect と OAuth 2.0 ベースの認証と承認を追加できます。 統合後、アプリでは、Azure AD 経由でフェデレーションされた AD FS でユーザーを認証できます。 各種シナリオの詳細については、「[開発者向けの AD FS のシナリオ](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-scenarios-for-developers)」をご覧ください。

MSAL for Java を使用するアプリでは、Azure Active Directory (Azure AD) と通信し、その後、AD FS とフェデレーションします。

MSAL for Java が Azure AD に接続されると、Azure AD で管理されているユーザー (管理対象ユーザー) や、AD FS など、別の ID プロバイダーで管理されているユーザー (フェデレーション ユーザー) のサインインが処理されます。 MSAL for Java では、ユーザーがフェデレーションされていることを認識されません。 Azure AD と通信するだけです。

この場合に使用する[機関](msal-client-application-configuration.md#authority)は、通常の機関 (機関のホスト名 + テナント、common、または organizations) です。

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>フェデレーション ユーザーのトークンを対話で取得する

`AuthorizationCodeParameters` または `DeviceCodeParameters` で `ConfidentialClientApplication.AcquireToken()` または `PublicClientApplication.AcquireToken()` を呼び出すときのユーザー エクスペリエンスは通常、次のようになります。

1. ユーザーは自分のアカウント ID を入力します。
2. Azure AD に "組織のページに移動します" と短い時間だけ表示された後、ユーザーは ID プロバイダーのサインイン ページにリダイレクトされます。 サインイン ページは、通常、組織のロゴでカスタマイズされます。

このフェデレーション シナリオでサポートされている AD FS バージョンは次のとおりです。
- Active Directory フェデレーション サービス FS v2
- Active Directory フェデレーション サービス v3 (Windows Server 2012 R2)
- Active Directory フェデレーション サービス v4 (AD FS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>ユーザー名とパスワードでトークンを取得する

`IntegratedWindowsAuthenticationParameters` または `UsernamePasswordParameters` と共に `ConfidentialClientApplication.AcquireToken()` または `PublicClientApplication.AcquireToken()` を使用してトークンを取得するとき、MSAL for Java では、ユーザー名に基づいて ID プロバイダーと通信します。 MSAL for Java では、ID プロバイダーから [SAML 1.1 トークン](reference-saml-tokens.md) が取得されます。このトークンはその後、Azure AD に与えられ、JSON Web トークン (JWT) が返されます。

## <a name="next-steps"></a>次の手順

フェデレーション シナリオについては、「[ホーム領域検出ポリシーを使用して、アプリケーションの Azure Active Directory サインイン動作を構成する](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)」を参照してください。
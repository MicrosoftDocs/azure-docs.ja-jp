---
title: MSAL.NET での AD FS のサポート | Azure
titleSuffix: Microsoft identity platform
description: .NET 用 Microsoft Authentication Library (MSAL.NET) での Active Directory フェデレーション サービス (AD FS) サポートについて説明します。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b5124712e4002c9a3a6740986cafc18c12dbb98
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921949"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>MSAL.NET での Active Directory フェデレーション サービスのサポート
Windows Server で Active Directory フェデレーション サービス (AD FS) を使用して、自分が開発しているアプリケーションに OpenID Connect と OAuth 2.0 ベースの認証と承認を追加できます。 その後、これらのアプリケーションで、AD FS に対して直接ユーザーを認証することができます。 詳細については、「[開発者向けの AD FS のシナリオ](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios)」をご覧ください。

.NET 用 Microsoft Authentication Library (MSAL.NET) では、AD FS に対する認証について 2 つのシナリオがサポートされています。

- MSAL.NET は、それ自体が AD FS と "*フェデレーションされている*" Azure Active Directory と通信します。
- MSAL.NET は、**ADFS** 機関と直接通信します。 これは、AD FS 2019 以降でのみサポートされます。 これが強調されているシナリオの 1 つとして、[Azure Stack](https://azure.microsoft.com/overview/azure-stack/) のサポートがあります。


## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>AD FS とフェデレーションされている Azure AD への MSAL の接続
MSAL.NET では、管理対象ユーザー (Azure AD で管理されているユーザー) またはフェデレーション ユーザー (AD FS などの別の ID プロバイダーによって管理されているユーザー) にサインインする Azure AD への接続がサポートされています。 MSAL.NET は、ユーザーがフェデレーションされていることを認識しません。 それが関係している限りでは、Azure AD と通信します。

この場合に使用する[機関](msal-client-application-configuration.md#authority)は、通常の機関 (機関のホスト名 + テナント、common、または organizations) です。

### <a name="acquiring-a-token-interactively"></a>トークンを対話形式で取得する
`AcquireTokenInteractive` メソッドを呼び出すと、ユーザー エクスペリエンスは通常、次のようになります。

1. ユーザーは自分のアカウント ID を入力します。
2. Azure AD で、"Taking you to your organization's page" (組織のページに移動します) というメッセージ が一時的に表示されます。
3. ユーザーは、ID プロバイダーのサインイン ページにリダイレクトされます。 サインイン ページは、通常、組織のロゴでカスタマイズされます。

このフェデレーション シナリオでサポートされている AD FS バージョンは、AD FS v2、AD FS v3 (Windows Server 2012 R2)、および AD FS v4 (AD FS 2016) です。

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>AcquireTokenByIntegratedAuthentication または AcquireTokenByUsernamePassword を使用してトークンを取得する
`AcquireTokenByIntegratedAuthentication` または `AcquireTokenByUsernamePassword` メソッドを使用してトークンを取得するときに、MSAL.NET はユーザー名に基づいて ID プロバイダーと通信します。  その ID プロバイダーと通信した後、MSAL.NET は [SAML 1.1 トークン](reference-saml-tokens.md)を受け取ります。  その後、MSAL.NET は、SAML トークンを Azure AD にユーザー アサーション ([On-Behalf-Of フロー](msal-authentication-flows.md#on-behalf-of)と同様のもの) として提供し、JWT を取得します。

## <a name="msal-connects-directly-to-ad-fs"></a>AD FS への MSAL の直接接続
MSAL.NET では、Open ID Connect に準拠し、PKCE とスコープを認識する AD FS 2019 への接続がサポートされています。 このサポートでは、サービス パック [KB 4490481](https://support.microsoft.com/en-us/help/4490481/windows-10-update-kb4490481) が Windows Server に適用されている必要があります。 AD FS に直接接続する場合、アプリケーションの構築に使用する機関は `https://mysite.contoso.com/adfs/` のようになります。

現時点では、以下への直接接続をサポートする計画はありません。

- AD FS 16。これには PKCE のサポートがなく、依然としてスコープではなくリソースが使用されているためです。
- AD FS v2。これは OIDC に準拠していません。

 AD FS 2016 への直接接続が必要なシナリオをサポートする必要がある場合は、最新バージョンの [Azure Active Directory 認証ライブラリ](active-directory-authentication-libraries.md#microsoft-supported-client-libraries)を使用してください。 オンプレミス システムを AD FS 2019 にアップグレードすると、MSAL.NET を使用できるようになります。

## <a name="next-steps"></a>次の手順

フェデレーション シナリオについては、「[ホーム領域検出ポリシーを使用して、アプリケーションの Azure Active Directory サインイン動作を構成する](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)」を参照してください。

---
title: Azure AD Connect:シームレス シングル サインオン | Microsoft Docs
description: このトピックでは、Azure Active Directory (Azure AD) シームレス シングル サインオンについて説明します。この機能により、企業ネットワーク内の企業のデスクトップ ユーザーに真のシングル サインオンを提供できます。
services: active-directory
keywords: Azure AD Connect とは, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/13/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeede88bfbe024a66e40270240d32488e581dd77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102517703"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory シームレス シングル サインオン

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory シームレス シングル サインオンとは

Azure Active Directory シームレス シングル サインオン (Azure AD シームレス SSO) では、ユーザーが企業ネットワークに接続される会社のデバイスを使用するときに、自動的にサインインを行います。 この機能を有効にすると、ユーザーは Azure AD にサインインするためにパスワードを入力する必要がなくなります。また、通常はユーザー名の入力も不要です。 この機能により、追加のオンプレミス コンポーネントを必要とせずに、ユーザーはクラウド ベースのアプリケーションに簡単にアクセスできるようになります。

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

シームレス SSO は、サインインの方法として、 [パスワード ハッシュ同期](how-to-connect-password-hash-synchronization.md)または[パススルー認証](how-to-connect-pta.md)のどちらとも組み合わせることができます。 シームレス SSO は、Active Directory フェデレーション サービス (ADFS) には適用でき _ません_。

![シームレス シングル サインオン](./media/how-to-connect-sso/sso1.png)

## <a name="sso-via-primary-refresh-token-vs-seamless-sso"></a>プライマリ更新トークンを介した SSO とシームレス SSO

Windows 10、Windows Server 2016、およびそれ以降のバージョンの場合は、プライマリ更新トークン (PRT) を介した SSO を使用することをお勧めします。 Windows 7 と 8.1 の場合、シームレス SSO を使用することをお勧めします。
シームレス SSO では、ユーザーのデバイスがドメインに参加している必要がありますが、これは、Windows 10 の [Azure AD 参加済みデバイス](../devices/concept-azure-ad-join.md)や [Hybrid Azure AD 参加済みデバイス](../devices/concept-azure-ad-join-hybrid.md)では使用されません。 Azure AD 参加済み、Hybrid Azure AD 参加済み、および Azure AD 登録済みデバイスでの SSO は、[プライマリ更新トークン (PRT)](../devices/concept-primary-refresh-token.md) に基づいて機能します。

Hybrid Azure AD 参加済み、Azure AD 参加済み、または個人登録済みのデバイスに対して PRT を介した SSO が機能するのは、[職場または学校アカウントを追加] を使用してデバイスが Azure AD に登録された後になります。 PRT を使用した Windows 10 での SSO のしくみについて詳しくは、次を参照してください: [プライマリ更新トークン (PRT) と Azure AD](../devices/concept-primary-refresh-token.md)


## <a name="key-benefits"></a>主な利点

- *優れたユーザー エクスペリエンス*
  - ユーザーは、オンプレミスとクラウドベースの両方のアプリケーションに自動的にサインインします。
  - ユーザーは、パスワードを繰り返し入力する必要はありません。
- *デプロイと管理が容易*
  - オンプレミスでは、この機能の動作のために追加のコンポーネントは不要です。
  - [パスワード ハッシュ同期](how-to-connect-password-hash-synchronization.md)または[パススルー認証](how-to-connect-pta.md)の、どちらのクラウド認証方法でも機能します。
  - グループ ポリシーを使用して、一部のユーザーまたはすべてのユーザーに展開できます。
  - AD FS インフラストラクチャを使用することなく、Windows 10 以外のデバイスを Azure AD に登録できます。 この機能では、バージョン 2.1 以降の [workplace-join クライアント](https://www.microsoft.com/download/details.aspx?id=53554)を使用する必要があります。

## <a name="feature-highlights"></a>機能概要

- サインインのユーザー名には、オンプレミスの既定のユーザー名 (`userPrincipalName`) または Azure AD Connect で構成された別の属性 (`Alternate ID`) を指定できます。 シームレス SSO は Kerberos チケットの `securityIdentifier` 要求を使用して Azure AD で対応するユーザー オブジェクトを検索するので、どちらを使用しても問題ありません。
- シームレス SSO は便宜的な機能です。 これが何らかの理由で失敗した場合、ユーザーのサインイン エクスペリエンスは通常の動作に戻ります。つまり、ユーザーはサインイン ページでパスワードを入力する必要があります。
- アプリケーション (たとえば、`https://myapps.microsoft.com/contoso.com`) が Azure AD サインイン要求で `domain_hint` (OpenID Connect) パラメーターや `whr` (SAML) パラメーター (テナントを識別する)、または `login_hint` パラメーター (ユーザーを識別する) を転送する場合、ユーザーはユーザー名やパスワードを入力することなく自動的にサインインします。
- アプリケーション (たとえば、`https://contoso.sharepoint.com`) がサインイン要求を、Azure AD の共通エンドポイント (つまり、`https://login.microsoftonline.com/common/<...>`) ではなく、Azure AD のテナントとして設定されているエンドポイント (つまり、`https://login.microsoftonline.com/contoso.com/<..>` または `https://login.microsoftonline.com/<tenant_ID>/<..>`) に送信する場合、ユーザーにはサイレント サインオン エクスペリエンスも提供されます。
- サインアウトがサポートされています。 そのため、ユーザーは、シームレス SSO を使用して自動的にサインインするのではなく、サインインに別の Azure AD アカウントを使用することを選択できます。
- バージョン 16.0.8730.xxxx 以降の Microsoft 365 Win32 クライアント (Outlook、Word、Excel など) は、非対話型フローを使用してサポートされています。 OneDrive の場合、サイレント サインオン エクスペリエンス用の [OneDrive サイレント構成機能](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894)をアクティブにする必要があります。
- この機能は、Azure AD Connect を使用して有効にできます。
- これは無料の機能であり、この機能を使用するために Azure AD の有料エディションは不要です。
- この機能は、Web ブラウザー ベースのクライアントと、Kerberos 認証に対応したプラットフォームおよびブラウザーで[最新の認証](/office365/enterprise/modern-auth-for-office-2013-and-2016)をサポートする Office クライアントでサポートされています。

| OS\ブラウザー |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|はい\*|はい|はい|はい\*\*\*|該当なし
|Windows 8.1|はい\*|はい*\*\*\*|はい|はい\*\*\*|該当なし
|Windows 8|はい\*|該当なし|はい|はい\*\*\*|該当なし
|Windows 7|はい\*|該当なし|はい|はい\*\*\*|該当なし
|Windows Server 2012 R2 以降|はい\*\*|該当なし|はい|はい\*\*\*|該当なし
|Mac OS X|該当なし|該当なし|はい\*\*\*|はい\*\*\*|はい\*\*\*


\*Internet Explorer バージョン 10 以降が必要です。

\*\*Internet Explorer バージョン 10 以降が必要です。 拡張保護モードを無効にする。

\*\*\*[別途構成](how-to-connect-sso-quick-start.md#browser-considerations)が必要。

\*\*\*\*Microsoft Edge バージョン 77 以降が必要です。

## <a name="next-steps"></a>次のステップ

- [**クイック スタート**](how-to-connect-sso-quick-start.md) - Azure AD シームレス SSO を動作させます。
- [**デプロイ計画**](../manage-apps/plan-sso-deployment.md) - 詳細なデプロイ計画です。
- [**技術的な詳細**](how-to-connect-sso-how-it-works.md) - この機能のしくみを確認します。
- [**よく寄せられる質問**](how-to-connect-sso-faq.md) - よく寄せられる質問と回答です。
- [**トラブルシューティング**](tshoot-connect-sso.md) - この機能に関する一般的な問題を解決する方法を確認します。
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 新しい機能の要求を提出します。

---
title: Android の証明書ベースの認証 - Azure Active Directory
description: Android デバイスでソリューションに証明書ベースの認証を構成するための対応シナリオや要件について説明する
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1001d5524fe99783cda4d5b77bdaceacc6791848
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861375"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Android での Azure Active Directory の証明書ベースの認証

Android デバイスは、次に接続する場合、証明書ベースの認証 (CBA) を使用して、そのデバイス上のクライアント証明書で Azure Active Directory に対して認証できます。

* Microsoft Outlook や Microsoft Word などの Office モバイル アプリケーション
* Exchange ActiveSync (EAS) クライアント

この機能を構成すると、モバイル デバイスで特定のメールおよび Microsoft Office アプリケーションにユーザー名とパスワードの組み合わせを入力する必要がなくなります。

このトピックでは、Office 365 Enterprise、Business、Education、US Government、China、Germany プランのテナントのユーザーのために Android デバイス上で CBA を構成するための要件およびサポートされるシナリオについて説明します。

Office 365 US Government Defense プランと Federal プランでは、この機能はプレビュー版として提供されています。

## <a name="microsoft-mobile-applications-support"></a>Microsoft モバイル アプリケーションのサポート

| アプリケーション | サポート |
| --- | --- |
| Azure Information Protection アプリ |![このアプリケーションのサポートを示すチェック マーク][1] |
| Intune ポータル サイト |![このアプリケーションのサポートを示すチェック マーク][1] |
| Microsoft Teams |![このアプリケーションのサポートを示すチェック マーク][1] |
| OneNote |![このアプリケーションのサポートを示すチェック マーク][1] |
| OneDrive |![このアプリケーションのサポートを示すチェック マーク][1] |
| Outlook |![このアプリケーションのサポートを示すチェック マーク][1] |
| Power BI |![このアプリケーションのサポートを示すチェック マーク][1] |
| Skype for Business |![このアプリケーションのサポートを示すチェック マーク][1] |
| Word/Excel/PowerPoint |![このアプリケーションのサポートを示すチェック マーク][1] |
| Yammer |![このアプリケーションのサポートを示すチェック マーク][1] |

### <a name="implementation-requirements"></a>実装の要件

デバイスの OS バージョンは、Android 5.0 (Lollipop) 以降である必要があります。

フェデレーション サーバーを構成する必要があります。

Azure Active Directory でクライアント証明書を失効させるには、ADFS トークンに次の要求が必要です。

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (クライアント証明書のシリアル番号)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (クライアント証明書の発行者の文字列)

Azure Active Directory は、ADFS トークン (またはその他の SAML トークン) で利用できる場合に、これらの要求を更新トークンに追加します。 更新トークンを検証する必要がある場合、この情報を使用して失効を確認します。

ベスト プラクティスとして、組織の ADFS エラー ページを次の情報で更新するようにしてください。

* Android に Microsoft Authenticator をインストールするための要件。
* ユーザー証明書を取得する手順

詳細については、「[AD FS サインイン ページのカスタマイズ](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn280950(v=ws.11))」を参照してください。

(先進認証が有効にされている) 一部の Office アプリでは、要求で "*prompt=login*" が Azure AD に送信されます。 既定では、Azure AD によって、ADFS への要求内の "*prompt=login*" が、"*wauth=usernamepassworduri*" (ADFS に U/P 認証を実行するよう依頼する) および "*wfresh=0*" (ADFS に SSO 状態を無視し、新しい認証を実行するよう依頼する) として変換されます。 これらのアプリに対して証明書ベースの認証を有効にするには、既定の Azure AD の動作を変更する必要があります。 フェデレーション ドメインの設定で "*PromptLoginBehavior*" を "*Disabled*" に設定します。
このタスクを実行するには、[MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings) コマンドレットを使用します。

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Exchange ActiveSync クライアントのサポート

Android 5.0 (Lollipop) 以降の特定の Exchange ActiveSync アプリケーションがサポートされています。 電子メール アプリケーションがこの機能をサポートしているかどうかを確認するには、アプリケーション開発者に問い合わせてください。

## <a name="next-steps"></a>次のステップ

ご自分の環境で証明書ベースの認証を構成しようとする場合は、[Android での証明書ベースの認証の概要](active-directory-certificate-based-authentication-get-started.md)に関するページで手順を参照してください。

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png

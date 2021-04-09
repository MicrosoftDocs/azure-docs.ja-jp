---
title: iOS での証明書ベースの認証 - Azure Active Directory
description: iOS デバイスでソリューションに Azure Active Directory の証明書ベースの認証を構成するための対応シナリオや要件について説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5f9b96fe9ee0781803bbbd86316e8783b60a6f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861325"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>iOS での Azure Active Directory の証明書ベースの認証

セキュリティを強化するために、iOS デバイスは、次のアプリケーションやサービスに接続するとき、証明書ベースの認証 (CBA) を使用して、そのデバイス上のクライアント証明書で Azure Active Directory (Azure AD) に対して認証できます。

* Microsoft Outlook や Microsoft Word などの Office モバイル アプリケーション
* Exchange ActiveSync (EAS) クライアント

証明書を使用すると、モバイル デバイスで特定のメールおよび Microsoft Office アプリケーションにユーザー名とパスワードの組み合わせを入力する必要がなくなります。

この記事では、iOS デバイスに CBA を構成するうえでの要件とサポートされるシナリオについて詳しく説明します。 iOS の CBA は、Azure パブリック クラウド、Microsoft Government Cloud、Microsoft Cloud Germany、Microsoft Azure China 21Vianet 全体で使用できます。

## <a name="microsoft-mobile-applications-support"></a>Microsoft モバイル アプリケーションのサポート

| アプリケーション | サポート |
| --- | --- |
| Azure Information Protection アプリ |![このアプリケーションのサポートを示すチェック マーク][1] |
| Intune ポータル サイト |![このアプリケーションのサポートを示すチェック マーク][1] |
| Microsoft Teams |![このアプリケーションのサポートを示すチェック マーク][1] |
| Office (モバイル) |![このアプリケーションのサポートを示すチェック マーク][1] |
| OneNote |![このアプリケーションのサポートを示すチェック マーク][1] |
| OneDrive |![このアプリケーションのサポートを示すチェック マーク][1] |
| Outlook |![このアプリケーションのサポートを示すチェック マーク][1] |
| Power BI |![このアプリケーションのサポートを示すチェック マーク][1] |
| Skype for Business |![このアプリケーションのサポートを示すチェック マーク][1] |
| Word/Excel/PowerPoint |![このアプリケーションのサポートを示すチェック マーク][1] |
| Yammer |![このアプリケーションのサポートを示すチェック マーク][1] |

## <a name="requirements"></a>必要条件

iOS で CBA を使用するうえでの要件と考慮事項は次のとおりです。

* デバイスの OS バージョンは、iOS 9 以上である必要があります。
* Microsoft Authenticator は、iOS の Office アプリケーションに必要です。
* ADFS サーバーの認証 URL を含んだ識別プリファレンスを macOS キーチェーンに作成する必要があります。 詳細については、「[Mac のキーチェーン アクセスで識別プリファレンスを作成する](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac)」を参照してください。

Active Directory フェデレーション サービス (AD FS) の要件と考慮事項は次のとおりです。

* ADFS サーバーで証明書認証を有効にし、フェデレーション認証を使用する必要があります。
* 証明書には拡張キー使用法 (EKU) を使用する必要があります。また、証明書の "*サブジェクトの別名 (NT プリンシパル名)* " にユーザーの UPN が含まれている必要があります。

## <a name="configure-adfs"></a>ADFS の構成

Azure AD でクライアント証明書を失効させるには、ADFS トークンに次の要求が必要です。 Azure AD は、ADFS トークン (またはその他の SAML トークン) で利用できる場合に、これらの要求を更新トークンに追加します。 更新トークンを検証する必要がある場合、この情報を使用して失効を確認します。

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` - クライアント証明書のシリアル番号を追加します。
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` - クライアント証明書の発行者の文字列を追加します。

また、ベスト プラクティスとして、組織の ADFS エラー ページを次の情報で更新するようにしてください。

* iOS に Microsoft Authenticator をインストールするための要件
* ユーザー証明書を取得する手順

詳細については、[AD FS サインイン ページのカスタマイズ](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn280950(v=ws.11))に関するページを参照してください。

## <a name="use-modern-authentication-with-office-apps"></a>Office アプリで先進認証を使用する

先進認証が有効にされている一部の Office アプリでは、要求で `prompt=login` が Azure AD に送信されます。 既定では、Azure AD は、ADFS への要求で `prompt=login` を `wauth=usernamepassworduri` (ADFS に U/P 認証を実行するように要求) と `wfresh=0` (ADFS に SSO 状態を無視して、新しい認証を実行するように要求) に変換します。 これらのアプリに対して証明書ベースの認証を有効にするには、既定の Azure AD の動作を変更します。

既定の動作は、フェデレーション ドメインの設定で "*PromptLoginBehavior*" を "*無効*" に設定することによって更新します。 次の例のように、[MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings) コマンドレットを使用してこのタスクを実行できます。

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>Exchange ActiveSync クライアントのサポート

iOS 9 以降では、ネイティブの iOS メール クライアントがサポートされます。 その他の Exchange ActiveSync アプリケーションについては、この機能のサポート状況をアプリケーションの開発者にお問い合わせください。

## <a name="next-steps"></a>次のステップ

ご自分の環境で証明書ベースの認証を構成しようとする場合は、[証明書ベースの認証の概要](active-directory-certificate-based-authentication-get-started.md)に関するページで手順を参照してください。

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png

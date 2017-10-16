---
title: "iOS での Azure Active Directory の証明書ベースの認証 | Microsoft Docs"
description: "iOS デバイスでソリューションに証明書ベースの認証を構成するための対応シナリオや要件について説明する"
services: active-directory
author: MarkusVi
documentationcenter: na
manager: femila
ms.assetid: 26a6fc54-0153-44fb-b970-9b432c99e9f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: af9d0c7ba9c1a3026cc042872e1ab773eb3c4c8e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>iOS での Azure Active Directory の証明書ベースの認証

証明書ベースの認証 (CBA) では、Exchange Online アカウントを次に接続する場合、Azure Active Directory と、Windows、Android または iOS デバイス上のクライアント証明書を使用して認証できます。 

* Microsoft Outlook や Microsoft Word などの Office モバイル アプリケーション   
* Exchange ActiveSync (EAS) クライアント 

この機能を構成すると、モバイル デバイスで特定のメールおよび Microsoft Office アプリケーションにユーザー名とパスワードの組み合わせを入力する必要がなくなります。 

このトピックでは、Office 365 Enterprise、Business、Education、US Government、China、Germany プランのテナントのユーザーのために iOS (Android) デバイス上で CBA を構成するための要件およびサポートされるシナリオについて説明します。

Office 365 US Government Defense プランと Federal プランでは、この機能はプレビュー版として提供されています。




## <a name="office-mobile-applications-support"></a>Office モバイル アプリケーションのサポート

| アプリケーション | サポート |
| --- | --- |
| Azure Information Protection アプリ |![○][1] |
| Microsoft Teams |![○][1] |
| OneNote |![○][1] |
| OneDrive |![○][1] |
| Outlook |![○][1] |
| Power BI |![○][1] |
| Skype for Business |![○][1] |
| Word/Excel/PowerPoint |![○][1] |
| Yammer |![○][1] |


## <a name="requirements"></a>必要条件 

デバイスの OS バージョンは、iOS 9 以上である必要があります。 

フェデレーション サーバーを構成する必要があります。  

Microsoft Authenticator は、iOS の Office アプリケーションに必要です。  

Azure Active Directory でクライアント証明書を失効させるには、ADFS トークンに次の要求が必要です。  

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`  
  (クライアント証明書のシリアル番号) 
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`  
  (クライアント証明書の発行者の文字列) 

Azure Active Directory は、ADFS トークン (またはその他の SAML トークン) で利用できる場合に、これらの要求を更新トークンに追加します。 更新トークンを検証する必要がある場合、この情報を使用して失効を確認します。 

ベスト プラクティスとして、次を参照して、ADFS エラー ページを更新することをお勧めします。

* iOS に Microsoft Authenticator をインストールするための要件
* ユーザー証明書を取得する手順 

詳細については、「 [AD FS サインイン ページのカスタマイズ](https://technet.microsoft.com/library/dn280950.aspx)」を参照してください。

(先進認証が有効になった) 一部の Office アプリは、要求で *prompt=login* を Azure AD に送信します。 既定では、Azure AD は、ADFS への要求でこれを *wauth=usernamepassworduri* (ADFS に U/P 認証を実行するように要求) と *wfresh=0* (ADFS に SSO 状態を無視して、新しい認証を実行するように要求) に変換します。 これらのアプリに対して証明書ベースの認証を有効にするには、既定の Azure AD の動作を変更する必要があります。 フェデレーション ドメイン設定の *PromptLoginBehavior* を '*無効*' に設定するだけです。 このタスクを実行するには、[MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) コマンドレットを使用します。

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`
  

## <a name="exchange-activesync-clients-support"></a>Exchange ActiveSync クライアントのサポート
iOS 9 以降では、ネイティブの iOS メール クライアントがサポートされます。 その他の Exchange ActiveSync アプリケーションについては、この機能のサポート状況をアプリケーションの開発者にお問い合わせください。  


## <a name="next-steps"></a>次のステップ

ご自分の環境で証明書ベースの認証を構成しようとする場合は、[Android での証明書ベースの認証の概要](active-directory-certificate-based-authentication-get-started.md)に関するページで手順を参照してください。


<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png

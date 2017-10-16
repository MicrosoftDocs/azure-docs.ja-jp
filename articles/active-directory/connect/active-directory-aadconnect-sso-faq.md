---
title: "Azure AD Connect: シームレス シングル サインオン - よく寄せられる質問 | Microsoft Docs"
description: "Azure Active Directory シームレス シングル サインオンに関してよく寄せられる質問への回答を示します。"
services: active-directory
keywords: "Azure AD Connect とは, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: f603c4f0305184bfefe23a02b07cef134c83e678
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory シームレス シングル サインオン: よく寄せられる質問

この記事では、Azure Active Directory シームレス シングル サインオン (シームレス SSO) に関してよく寄せられる質問に回答します。 最新のコンテンツを常にチェックしてください。

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>シームレス SSO は、どのようなサインイン方法と動作しますか。

シームレス SSO は、サインインの方法として、[パスワード ハッシュ同期](active-directory-aadconnectsync-implement-password-synchronization.md)または[パススルー認証](active-directory-aadconnect-pass-through-authentication.md)のどちらとも組み合わせることができます。 ただし、この機能は、Active Directory フェデレーション サービス (AD FS) で使用できません。

## <a name="is-seamless-sso-a-free-feature"></a>シームレス SSO は無料の機能ですか。

シームレス SSO は無料の機能です。この機能を使用するために Azure AD の有料エディションは不要です。 機能が一般公開されても、無料のままです。

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>どのアプリケーションがシームレス SSO の `domain_hint` または `login_hint` 機能を利用していますか。

これらのパラメーターを送信するおよび送信しないアプリケーションの一覧を現在作成中です。 ご自分のアプリケーション用にご興味がある場合、コメント セクションからお伝えください。

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>シームレス SSO で、ユーザー名として `userPrincipalName` の代わりに `Alternate ID` をサポートしていますか。

はい。 [こちら](active-directory-aadconnect-get-started-custom.md)で示されているように、Azure AD Connect で構成されている場合、シームレス SSO はユーザー名として `Alternate ID` をサポートしています。 すべての Office 365 アプリケーションで `Alternate ID` をサポートしているわけではありません。 サポートの説明については、それぞれのアプリケーションのドキュメントを参照してください。

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Azure AD に、AD FS を使用せず非 Windows 10 デバイスを登録したいです。 代わりにシームレス SSO を使用できますか。

はい、このシナリオでは[ワークプレース ジョイン クライアント](https://www.microsoft.com/download/details.aspx?id=53554)のバージョン 2.1 以降が必要です。

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>`AZUREADSSOACC` コンピューター アカウントの Kerberos の復号化キーをロール オーバーするにはどうすればよいですか。

オンプレミスの AD フォレストで作成した `AZUREADSSOACC` コンピューター アカウント (Azure AD を表します) の Kerberos の復号化キーを頻繁にロール オーバーすることが重要です。

>[!IMPORTANT]
>少なくとも 30 日ごとに Kerberos の復号化キーをロールオーバーすることを強くお勧めします。

Azure AD Connect が実行されているオンプレミス サーバーで次の手順を実行します。

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>手順 1. シームレス SSO が有効になっている AD フォレストのリストの取得

1. 最初に、[Microsoft Online Services サインイン アシスタント](http://go.microsoft.com/fwlink/?LinkID=286152)をダウンロードしてインストールします。
2. 次に、 [64-bit Azure Active Directory Module for Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297)をダウンロードしてインストールします。
3. `%programfiles%\Microsoft Azure Active Directory Connect` フォルダーに移動します。
4. 以下のコマンドを使用して、Seamless SSO PowerShell モジュールをインポートします。`Import-Module .\AzureADSSO.psd1`
5. PowerShell を管理者として実行します。 PowerShell で、`New-AzureADSSOAuthenticationContext` を呼び出します。 このコマンドでは、テナントのグローバル管理者の資格情報を入力するポップアップが表示されます。
6. `Get-AzureADSSOStatus` を呼び出します。 このコマンドでは、この機能が有効になっている AD フォレストのリスト ("ドメイン" リストを参照) が表示されます。

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>手順 2. Kerberos の復号化キーが設定された各 AD フォレストでキーを更新します。

1. `$creds = Get-Credential` を呼び出します。 メッセージが表示されたら、目的の AD フォレストのドメイン管理者の資格情報を入力します。
2. `Update-AzureADSSOForest -OnPremCredentials $creds` を呼び出します。 このコマンドは、この特定の AD フォレスト内で `AZUREADSSOACC` コンピューター アカウントの Kerberos 復号化キーを更新し、Azure AD 内でこのキーを更新します。
3. 機能が有効に設定されている AD フォレストごとに、上記の手順を繰り返します。

>[!IMPORTANT]
>`Update-AzureADSSOForest` コマンドは、"_複数回実行しない_" でください。 複数回実行すると、ユーザーの Kerberos チケットの期限が切れてオンプレミスの Active Directory によって再発行されるまで、この機能は動作を停止します。

## <a name="how-can-i-disable-seamless-sso"></a>シームレス SSO はどのように無効にできますか。

シームレス SSO は、Azure AD Connect を使用して無効にできます。

Azure AD Connect を実行し、[Change user sign-in page] \(ユーザー サインイン ページの変更) を選択して [次へ] をクリックします。 [シングル サインオンを有効にする] チェック ボックスをオフにします。 ウィザードの手順を続行します。 ウィザードの完了後、シームレス SSO はテナントで無効になります。

ただし、画面に次のようなメッセージが表示されます。

「シングル サインオンは現在無効ですが、クリーンアップを完了するために実行できる追加の手動手順があります。 詳細情報」

プロセスを完了するには、Azure AD Connect が実行されているオンプレミス サーバーで次の手動の手順を実行します。

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>手順 1. シームレス SSO が有効になっている AD フォレストのリストの取得

1. 最初に、[Microsoft Online Services サインイン アシスタント](http://go.microsoft.com/fwlink/?LinkID=286152)をダウンロードしてインストールします。
2. 次に、 [64-bit Azure Active Directory Module for Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297)をダウンロードしてインストールします。
3. `%programfiles%\Microsoft Azure Active Directory Connect` フォルダーに移動します。
4. 以下のコマンドを使用して、Seamless SSO PowerShell モジュールをインポートします。`Import-Module .\AzureADSSO.psd1`
5. PowerShell を管理者として実行します。 PowerShell で、`New-AzureADSSOAuthenticationContext` を呼び出します。 このコマンドでは、テナントのグローバル管理者の資格情報を入力するポップアップが表示されます。
6. `Get-AzureADSSOStatus` を呼び出します。 このコマンドでは、この機能が有効になっている AD フォレストのリスト ("ドメイン" リストを参照) が表示されます。

### <a name="step-2-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>手順 2. 表示されている各 AD フォレストから `AZUREADSSOACCT` コンピューター アカウントを手動で削除します。

## <a name="next-steps"></a>次のステップ

- [**クイック スタート**](active-directory-aadconnect-sso-quick-start.md) - Azure AD シームレス SSO を動作させます。
- [**技術的な詳細**](active-directory-aadconnect-sso-how-it-works.md) - この機能のしくみを確認します。
- [**トラブルシューティング**](active-directory-aadconnect-troubleshoot-sso.md) - 機能に関する一般的な問題を解決する方法を確認します。
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 新しい機能の要求を提出します。

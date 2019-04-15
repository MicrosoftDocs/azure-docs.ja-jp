---
title: Azure AD Connect:シームレス シングル サインオン - よく寄せられる質問 | Microsoft Docs
description: Azure Active Directory シームレス シングル サインオンに関してよく寄せられる質問への回答を示します。
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
ms.topic: conceptual
ms.date: 11/14/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 709fb3be37850be37d6378652921ce26f4ff15fe
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804379"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory シームレス シングル サインオン:よく寄せられる質問

この記事では、Azure Active Directory シームレス シングル サインオン (シームレス SSO) に関してよく寄せられる質問に回答します。 最新のコンテンツを常にチェックしてください。

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>シームレス SSO は、どのようなサインイン方法と動作しますか。

シームレス SSO は、サインインの方法として、[パスワード ハッシュ同期](how-to-connect-password-hash-synchronization.md)または[パススルー認証](how-to-connect-pta.md)のどちらとも組み合わせることができます。 ただし、この機能は、Active Directory フェデレーション サービス (AD FS) で使用できません。

## <a name="is-seamless-sso-a-free-feature"></a>シームレス SSO は無料の機能ですか。

シームレス SSO は無料の機能です。この機能を使用するために Azure AD の有料エディションは不要です。

## <a name="is-seamless-sso-available-in-the-microsoft-azure-germany-cloudhttpswwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>シームレス SSO は [Microsoft Azure Germany クラウド](https://www.microsoft.de/cloud-deutschland)および [Microsoft Azure Government クラウド](https://azure.microsoft.com/features/gov/)で使用できますか。

いいえ。 シームレス SSO は、Azure AD のワールドワイド インスタンスでのみご利用いただけます。

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>どのアプリケーションがシームレス SSO の `domain_hint` または `login_hint` 機能を利用していますか。

Azure AD にこれらのパラメーターを送信でき、その結果、シームレス SSO を使用するサイレント サインオン エクスペリエンスをユーザーに提供する (つまり、ユーザーが自分のユーザー名屋パスワードを入力する必要がない) アプリケーションの一部を以下にリストします。

| アプリケーション名 | 使用するアプリケーションの URL |
| -- | -- |
| アクセス パネル | https:\//myapps.microsoft.com/contoso.com |
| Web 上の Outlook | https:\//outlook.office365.com/contoso.com |
| Office 365 ポータル | https:\//portal.office.com?domain_hint=contoso.com、https:\//www.office.com?domain_hint=contoso.com |

さらに、アプリケーションによるサインイン要求の送信先が、Azure AD の共通エンドポイント (https:\//login.microsoftonline.com/common/<...>) ではなく、テナントとして設定されている Azure AD のエンドポイント (https:\//login.microsoftonline.com/contoso.com/<..> または https:\//login.microsoftonline.com/<tenant_ID>/<..>) の場合、サイレント サインオン エクスペリエンスがユーザーに提供されます。 これらの種類のサインイン要求を行うアプリケーションの一部を以下にリストします。

| アプリケーション名 | 使用するアプリケーションの URL |
| -- | -- |
| SharePoint Online | https:\//contoso.sharepoint.com |
| Azure ポータル | https:\//portal.azure.com/contoso.com |

テナントの適切なアプリケーションの URL を取得するには、上記の表の "contoso.com" をご利用のドメイン名で置き換えます。

他のアプリケーションでサイレント サインオン エクスペリエンスを使用する場合は、フィードバック セクションからお知らせください。

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>シームレス SSO で、ユーザー名として `userPrincipalName` の代わりに `Alternate ID` をサポートしていますか。

はい。 [こちら](how-to-connect-install-custom.md)で示されているように、Azure AD Connect で構成されている場合、シームレス SSO はユーザー名として `Alternate ID` をサポートしています。 すべての Office 365 アプリケーションで `Alternate ID` をサポートしているわけではありません。 サポートの説明については、それぞれのアプリケーションのドキュメントを参照してください。

## <a name="what-is-the-difference-between-the-single-sign-on-experience-provided-by-azure-ad-joinactive-directory-azureadjoin-overviewmd-and-seamless-sso"></a>[Azure AD Join](../active-directory-azureadjoin-overview.md) とシームレス SSO のシングル サインオン エクスペリエンスの違いは何ですか。

[Azure AD Join](../active-directory-azureadjoin-overview.md) の場合、SSO は、デバイスが Azure AD に登録されているユーザーに提供されます。 そのデバイスは、必ずしもドメインに参加する必要があるとは限りません。 SSO は、Kerberos ではなく、"*プライマリ更新トークン*" (*PRT*) を使用して提供されます。 Windows 10 デバイスで、最適なユーザー エクスペリエンスが実現します。 SSO は、Microsoft Edge ブラウザーで自動的に実行されます。 ブラウザー拡張機能を使用することで Chrome でも動作します。

テナントでは、Azure AD Join とシームレス SSO の両方をご利用いただけます。 この 2 つは補完的な機能です。 両方の機能が有効な場合は、Azure AD Join がシームレス SSO に優先します。

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Azure AD に、AD FS を使用せず非 Windows 10 デバイスを登録したいです。 代わりにシームレス SSO を使用できますか。

はい、このシナリオでは[ワークプレース ジョイン クライアント](https://www.microsoft.com/download/details.aspx?id=53554)のバージョン 2.1 以降が必要です。

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>`AZUREADSSOACC` コンピューター アカウントの Kerberos の復号化キーをロール オーバーするにはどうすればよいですか。

オンプレミスの AD フォレストで作成した `AZUREADSSOACC` コンピューター アカウント (Azure AD を表します) の Kerberos の復号化キーを頻繁にロール オーバーすることが重要です。

>[!IMPORTANT]
>少なくとも 30 日ごとに Kerberos の復号化キーをロールオーバーすることを強くお勧めします。

Azure AD Connect が実行されているオンプレミス サーバーで次の手順を実行します。

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>手順 1. シームレス SSO が有効になっている AD フォレストのリストの取得

1. 最初に [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview) をダウンロードしてインストールします。
2. `%programfiles%\Microsoft Azure Active Directory Connect` フォルダーに移動します。
3. 以下のコマンドを使用して、Seamless SSO PowerShell モジュールをインポートします。`Import-Module .\AzureADSSO.psd1`
4. PowerShell を管理者として実行します。 PowerShell で、`New-AzureADSSOAuthenticationContext` を呼び出します。 このコマンドでは、テナントのグローバル管理者の資格情報を入力するポップアップが表示されます。
5. `Get-AzureADSSOStatus | ConvertFrom-Json` を呼び出します。 このコマンドでは、この機能が有効になっている AD フォレストのリスト ("ドメイン" リストを参照) が表示されます。

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>手順 2. Kerberos の復号化キーが設定された各 AD フォレストでキーを更新します。

1. `$creds = Get-Credential` を呼び出します。 メッセージが表示されたら、目的の AD フォレストのドメイン管理者の資格情報を入力します。

   > [!NOTE]
   > Microsoft は、ユーザー プリンシパル名 (UPN) (johndoe@contoso.com) の形式またはドメインで修飾された sam アカウント名 (contoso \johndoe または contoso.com\johndoe) の形式で提供されている、ドメイン管理者のユーザー名を使用して目的の AD フォレストを検索します。 ドメインで修飾された sam アカウント名が使用されている場合、Microsoft はユーザー名のドメイン部分を使用して、[DNS を使用してドメイン管理者のドメイン コントローラー](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx)を検索します。 UPN が使用されている場合、Microsoft は[それをドメインで修飾された sam アカウント名に変換](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa)してから、適切なドメイン コントローラーを検索します。

2. `Update-AzureADSSOForest -OnPremCredentials $creds` を呼び出します。 このコマンドは、この特定の AD フォレスト内で `AZUREADSSOACC` コンピューター アカウントの Kerberos 復号化キーを更新し、Azure AD 内でこのキーを更新します。
3. 機能が有効に設定されている AD フォレストごとに、上記の手順を繰り返します。

>[!IMPORTANT]
>`Update-AzureADSSOForest` コマンドは、"_複数回実行しない_" でください。 複数回実行すると、ユーザーの Kerberos チケットの期限が切れてオンプレミスの Active Directory によって再発行されるまで、この機能は動作を停止します。

## <a name="how-can-i-disable-seamless-sso"></a>シームレス SSO はどのように無効にできますか。

### <a name="step-1-disable-the-feature-on-your-tenant"></a>手順 1. テナントで機能を無効にする

#### <a name="option-a-disable-using-azure-ad-connect"></a>オプション A: Azure AD Connect を使用して無効にする

1. Azure AD Connect を実行し、**[Change user sign-in page]\(ユーザー サインイン ページの変更\)** を選択して **[次へ]** をクリックします。
2. **[シングル サインオンを有効にする]** チェック ボックスをオフにします。 ウィザードの手順を続行します。

ウィザードの完了後、シームレス SSO はテナントで無効になります。 ただし、画面に次のようなメッセージが表示されます。

「シングル サインオンは現在無効ですが、クリーンアップを完了するために実行できる追加の手動手順があります。 詳細情報」

クリーンアップ プロセスを完了するには、Azure AD Connect が実行されているオンプレミス サーバーで手順 2 と 3 を手順を実行します。

#### <a name="option-b-disable-using-powershell"></a>オプション B: PowerShell を使用して無効にする

Azure AD Connect が実行されているオンプレミス サーバーで次の手順を実行します。

1. 最初に [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview) をダウンロードしてインストールします。
2. `%programfiles%\Microsoft Azure Active Directory Connect` フォルダーに移動します。
3. 以下のコマンドを使用して、Seamless SSO PowerShell モジュールをインポートします。`Import-Module .\AzureADSSO.psd1`
4. PowerShell を管理者として実行します。 PowerShell で、`New-AzureADSSOAuthenticationContext` を呼び出します。 このコマンドでは、テナントのグローバル管理者の資格情報を入力するポップアップが表示されます。
5. `Enable-AzureADSSO -Enable $false` を呼び出します。

>[!IMPORTANT]
>PowerShell を使用してシームレス SSO を無効にしても、Azure AD Connect での状態は変更されません。 シームレス SSO は、**[ユーザー サインインの変更]** ページに有効と表示されます。

### <a name="step-2-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>手順 2. シームレス SSO が有効になっている AD フォレストのリストの取得

Azure AD Connect を使用してシームレス SSO を無効にした場合は、以下のタスクの 1 から 4 を実行します。 PowerShell を使ってシームレス SSO を無効にした場合は、次のタスク 5 に進んでください。

1. 最初に [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview) をダウンロードしてインストールします。
2. `%programfiles%\Microsoft Azure Active Directory Connect` フォルダーに移動します。
3. 以下のコマンドを使用して、Seamless SSO PowerShell モジュールをインポートします。`Import-Module .\AzureADSSO.psd1`
4. PowerShell を管理者として実行します。 PowerShell で、`New-AzureADSSOAuthenticationContext` を呼び出します。 このコマンドでは、テナントのグローバル管理者の資格情報を入力するポップアップが表示されます。
5. `Get-AzureADSSOStatus | ConvertFrom-Json` を呼び出します。 このコマンドでは、この機能が有効になっている AD フォレストのリスト ("ドメイン" リストを参照) が表示されます。

### <a name="step-3-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>手順 3. 表示されている各 AD フォレストから `AZUREADSSOACCT` コンピューター アカウントを手動で削除します。

## <a name="next-steps"></a>次の手順

- [**クイック スタート**](how-to-connect-sso-quick-start.md) - Azure AD シームレス SSO を動作させます。
- [**技術的な詳細**](how-to-connect-sso-how-it-works.md) - この機能のしくみを確認します。
- [**トラブルシューティング**](tshoot-connect-sso.md) - この機能に関する一般的な問題を解決する方法を確認します。
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 新しい機能の要求を提出します。

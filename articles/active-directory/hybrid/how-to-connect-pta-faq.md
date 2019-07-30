---
title: Azure AD Connect:パススルー認証 - よく寄せられる質問 | Microsoft Docs
description: Azure Active Directory パススルー認証に関してよく寄せられる質問への回答です。
services: active-directory
keywords: Azure AD Connect パススルー認証, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd41bdfb90e5e8428a747832c10fe23a2cac4560
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109400"
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Azure Active Directory パススルー認証:よく寄せられる質問

この記事では、Azure Active Directory (Azure AD) パススルー認証に関してよく寄せられる質問に回答します。 最新のコンテンツを常にチェックしてください。

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Azure AD へのサインイン方法として、パススルー認証、パスワード ハッシュ同期、Active Directory フェデレーション サービス (AD FS) のうちどれを選択すればよいですか。

Azure AD の各種サインイン方法の比較および組織に合った適切なサインイン方法の選び方については、[こちらのガイド](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)をご覧ください。

## <a name="is-pass-through-authentication-a-free-feature"></a>パススルー認証は無料の機能ですか。

パススルー認証は、無料の機能です。 この機能を使用するために Azure AD の有料エディションは不要です。

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloudhttpswwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>パススルー認証は [Microsoft Azure Germany クラウド](https://www.microsoft.de/cloud-deutschland)および [Microsoft Azure Government クラウド](https://azure.microsoft.com/features/gov/)で使用できますか。

いいえ。 パススルー認証は、世界中の Azure AD のインスタンスでのみ使用できます。

## <a name="does-conditional-accessactive-directory-conditional-access-azure-portalmd-work-with-pass-through-authentication"></a>[条件付きアクセス](../active-directory-conditional-access-azure-portal.md)は、パススルー認証と連携しますか。

はい。 Microsoft Azure Multi-Factor Authentication を含め、すべての条件付きアクセス機能がパススルー認証と連携します。

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>パススルー認証では、"userPrincipalName" の代わりに "Alternate ID" がユーザー名としてサポートされますか。

はい。パススルー認証では、Azure AD Connect で構成されている場合はユーザー名として `Alternate ID` をサポートします。 前提条件として、Azure AD Connect ではオンプレミスの Active Directory の `UserPrincipalName` 属性を Azure AD に同期する必要があります。 詳細については、「[Azure AD Connect のカスタム インストール](how-to-connect-install-custom.md)」をご覧ください。 すべての Office 365 アプリケーションで `Alternate ID` をサポートしているわけではありません。 サポートの説明については、それぞれのアプリケーションのドキュメントを参照してください。

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>パスワード ハッシュ同期は、パススルー認証のフォールバックとして機能しますか。

いいえ。 パススルー認証は、パスワード ハッシュ同期に自動的にフェールオーバー _されません_。 ユーザーのサインイン エラーを回避するには、[高可用性](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)向けにパススルー認証を構成する必要があります。

## <a name="what-happens-when-i-switch-from-password-hash-synchronization-to-pass-through-authentication"></a>パスワード ハッシュ同期からパススルー認証に切り替えるとどうなりますか。

Azure AD Connect を使用して、サインイン方法をパスワード ハッシュ同期からパススルー認証に切り替えると、パススルー認証がマネージド ドメイン内でのユーザーのプライマリ サインイン方法になります。 パスワード ハッシュ同期によって以前同期されていたすべてのユーザーのパスワード ハッシュは、Azure AD に格納されたままになることに注意してください。

## <a name="can-i-install-an-azure-ad-application-proxymanage-appsapplication-proxymd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>[Azure AD アプリケーション プロキシ](../manage-apps/application-proxy.md) コネクタを、パススルー認証エージェントと同じサーバーにインストールできますか。

はい。 この構成は、パススルー認証エージェント (バージョン 1.5.193.0 以降) のブランド名変更バージョンでサポートされます。

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>どのバージョンの Azure AD Connect とパススルー認証エージェントが必要ですか。

この機能を動作させるには、Azure AD Connect については 1.1.750.0 以降、パススルー認証エージェントについては 1.5.193.0 以降が必要です。 すべてのソフトウェアを、Windows Server 2012 R2 以降が搭載されたサーバーにインストールする必要があります。

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>ユーザーのパスワードの有効期限が切れている場合に、パススルー認証を使用してサインインしようとすると、どうなりますか。

特定のユーザーに対して[パスワード ライトバック](../authentication/concept-sspr-writeback.md)を構成済みの場合、パススルー認証を使用してサインインしたユーザーは、パスワードを変更またはリセットできます。 パスワードはオンプレミス Active Directory に想定どおりに書き戻されます。

特定のユーザーにパスワード ライトバックが構成されていない場合、またはユーザーに有効な Azure AD ライセンスが割り当てられていない場合、ユーザーはクラウドでパスワードを更新できません。 自身のパスワードの有効期限が切れた場合でも、そのパスワードを更新することはできません。 代わりにユーザーには、"このサイトでパスワードを変更することをお客様の組織が許可していません。 組織によって推奨されている方法でパスワードを更新するか、サポートが必要な場合は管理者にお問い合わせください。" というメッセージが表示されます。 ユーザーまたは管理者は、オンプレミスの Active Directory でパスワードをリセットする必要があります。

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>パススルー認証を使用することで、どのようにブルート フォース パスワード攻撃から保護できますか。

[スマート ロックアウトに関する情報をお読みください](../authentication/howto-password-smart-lockout.md)。

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>パススルー認証エージェントはポート 80 およびポート 443 で何をやり取りしますか。

- 認証エージェントは、すべての機能操作について、ポート 443 で HTTPS 要求を行います。
- 認証エージェントは、SSL 証明書失効リスト (CRL) をダウンロードするために、ポート 80 で HTTP 要求を行います。

     >[!NOTE]
     >この機能に必要なポートの数は、最近の更新で減らされています。 以前のバージョンの Azure AD Connect または認証エージェントを使用している場合は、5671、8080、9090、9091、9350、9352、および 10100 - 10120 の各ポートも開いておいてください。

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>パススルー認証エージェントは、送信 Web プロキシ サーバーで通信できますか。

はい。 Web プロキシ自動発見 (WPAD) がオンプレミス環境で有効になっている場合、認証エージェントは、ネットワーク上の Web プロキシ サーバーを自動的に検索し、使用しようとします。

環境内に WPAD がない場合は、(次に示す) プロキシ情報を追加して、パススルー認証エージェントに Azure AD との通信を許可できます。
- サーバー上でパススルー認証エージェントをインストールする前に、Internet Explorer 上でプロキシ情報を構成します。 これにより、認証エージェントのインストールを完了できますが、管理者ポータルには引き続き "**非アクティブ**" として表示されます。
- サーバー上で、"C:\Program Files\Microsoft Azure AD Connect Authentication Agent" に移動します。
- "AzureADConnectAuthenticationAgentService" 構成ファイルを編集し、次の行を追加します ("http\://contosoproxy.com:8080" は実際のプロキシ アドレスに置き換えます)。

```
   <system.net>
      <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy
            usesystemdefault="true"
            proxyaddress="http://contosoproxy.com:8080"
            bypassonlocal="true"
         />
     </defaultProxy>
   </system.net>
```

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>同じサーバーに複数のパススルー認証エージェントをインストールできますか。

いいえ。1 つのサーバーにインストールできるパススルー認証エージェントは 1 つだけです。 高可用性向けにパススルー認証を構成する必要がある場合は、[こちらの手順に従ってください](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)。

## <a name="do-i-have-to-manually-renew-certificates-used-by-pass-through-authentication-agents"></a>パススルー認証エージェントで使用される証明書は手動で更新する必要がありますか。

各パススルー認証エージェントと Azure AD の間の通信は、証明書ベースの認証を使用して保護されます。 これらの[証明書は、Azure AD によって数か月ごとに自動的に更新されます](how-to-connect-pta-security-deep-dive.md#operational-security-of -the-authentication-agents)。 これらの証明書を手動で更新する必要はありません。 必要に応じて、有効期限が切れた古い証明書をクリーンアップできます。

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>パススルー認証エージェントを削除するにはどうすればよいですか。

パススルー認証エージェントが実行されている間はアクティブなままであり、ユーザーのサインイン要求を継続的に処理します。 認証エージェントをアンインストールするには、 **[コントロール パネル]、[プログラム]、[プログラムと機能]** の順に選択し、**Microsoft Azure AD Connect 認証エージェント**と **Microsoft Azure AD Connect Agent Updater** プログラムの両方をアンインストールします。

上記の手順を完了した後、[Azure Active Directory 管理センター](https://aad.portal.azure.com)の [パススルー認証] ブレードを確認すると、認証エージェントは**非アクティブ**と表示されます。 これは "_予期されること_" です。 認証エージェントは数日後に自動的に一覧から削除されます。

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>既に AD FS を使用して、Azure AD にサインインしています。 これをパススルー認証に切り替えるには、どうすればよいですか。

AD FS (または他のフェデレーション テクノロジ) からパススルー認証に移行する場合は、[こちら](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true)に公開されている詳細なデプロイ ガイドに従うよう強くお勧めします。

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>複数フォレスト Active Directory 環境でパススルー認証を使用できますか。

はい。 ご使用の Active Directory フォレスト間にフォレストの信頼があり、名前サフィックス ルーティングが正しく構成されていれば、複数フォレスト環境がサポートされます。

## <a name="does-pass-through-authentication-provide-load-balancing-across-multiple-authentication-agents"></a>パススルー認証では、複数の認証エージェント間の負荷分散を提供しますか。

いいえ。複数のパススルー認証エージェントをインストールすることで[高可用性](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)のみが確保されます。 認証エージェント間の確定的な負荷分散は提供されません。 任意の認証エージェント (ランダム) が特定のユーザーのサインイン要求を処理できます。

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>いくつのパススルー認証エージェントをインストールする必要がありますか。

複数のパススルー認証エージェントをインストールすることで[高可用性](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)が確保されます。 ただし認証エージェント間の確定的な負荷分散は提供されません。

テナント上で想定されるサインイン要求のピーク時の負荷および平均的な負荷を検討してください。 ベンチマークとして、1 つの認証エージェントでは、標準的な 4 コア CPU、16 GB RAM サーバー上で 1 秒あたり 300 - 400 の認証を処理できます。

ネットワーク トラフィックを見積もるには、サイズ設定に関する次のガイダンスに従ってください。
- 各要求のペイロード サイズは、(0.5K + 1K * num_of_agents) バイトで、これは Azure AD から認証エージェントへのデータ量に相当します。 ここで "num_of_agents" は、テナントに登録されている認証エージェントの数を示します。
- 各応答のペイロード サイズは 1K バイトで、これは認証エージェントから Azure AD へのデータ量に相当します。

ほとんどのお客様の場合、高可用性と大容量を確保するには、合計 2 つまたは 3 つの認証エージェントがあれば十分です。 サインインの待機時間を向上させるために、認証エージェントは、ドメイン コントローラーの近くにインストールする必要があります。

>[!NOTE]
>認証エージェントの数は、テナントあたり 40 個に制限されています。

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Azure AD Connect が実行されているのとは別のサーバーに、最初のパススルー認証エージェントをインストールできますか。

いいえ、このシナリオはサポートされて "_いません_"。

## <a name="why-do-i-need-a-cloud-only-global-administrator-account-to-enable-pass-through-authentication"></a>パススルー認証を有効にするためにクラウド専用のグローバル管理者アカウントが必要なのはなぜですか。

クラウド専用のグローバル管理者アカウントを使用してパススルー認証を有効または無効にすることが推奨されます。 クラウド専用のグローバル管理者アカウントを追加する手順については、[こちら](../active-directory-users-create-azure-portal.md)をご覧ください。 このようにすることで、テナントからロックアウトされないようになります。

## <a name="how-can-i-disable-pass-through-authentication"></a>パススルー認証を無効にするには、どうすればよいですか。

Azure AD Connect ウィザードを再実行し、ユーザーのサインイン方法を、パススルー認証から別の方法に変更します。 この変更により、テナントのパススルー認証が無効になり、認証エージェントがサーバーからアンインストールされます。 他のサーバーからは、手動で認証エージェントをアンインストールする必要があります。

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>パススルー認証エージェントをアンインストールすると、どうなりますか。

サーバーからパススルー認証エージェントをアンインストールすると、そのサーバーでサインイン要求の受け入れが停止します。 テナントでユーザーのサインインが中断しないようにするには、パススルー認証エージェントをアンインストールする前に、別の認証エージェントが実行されていることを確認します。

## <a name="i-have-an-older-tenant-that-was-originally-setup-using-ad-fs--we-recently-migrated-to-pta-but-now-are-not-seeing-our-upn-changes-synchronizing-to-azure-ad--why-are-our-upn-changes-not-being-synchronized"></a>AD FS を使用してセットアップした古いテナントがあります。  最近 PTA に移行しましたが、UPN の変更点が Azure AD に同期されていません。  UPN の変更点はなぜ同期されていないのでしょうか。

A:次の状況においては、オンプレミスの UPN の変更点が同期されない場合があります。

- Azure AD テナントが 2015 年 6 月 15 日よりも前に作成された
- 最初に認証に AD FS を使用して、Azure AD テナントとフェデレーションした
- 管理対象ユーザーに認証として PTA を使用させるように切り替えた

これは、2015 年 6 月 15 日よりも前に作成されたテナントの既定の動作が UPN の変更点のブロックであるためです。  UPN の変更点のブロックを解除する必要がある場合は、次の PowerShell コマンドレットを実行する必要があります。  

`Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers-Enable $True`

2015 年 6 月 15 日以降に作成されたテナントでは、UPN の変更点を同期するのが既定の動作です。   



## <a name="next-steps"></a>次の手順
- [現時点での制限事項](how-to-connect-pta-current-limitations.md):サポートされているシナリオと、サポートされていないシナリオを確認します。
- [クイック スタート](how-to-connect-pta-quick-start.md): Azure AD パススルー認証を起動および実行します。
- [AD FS からパススルー認証への移行](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) - AD FS (または他のフェデレーション テクノロジ) からパススルー認証に移行するための詳細なガイドです。
- [スマート ロックアウト](../authentication/howto-password-smart-lockout.md): ユーザー アカウントを保護するようにご利用のテナント上でスマート ロックアウト機能を構成する方法について説明します。
- [技術的な詳細](how-to-connect-pta-how-it-works.md): パススルー認証機能のしくみについて説明します。
- [トラブルシューティング](tshoot-connect-pass-through-authentication.md): パススルー認証機能に関する一般的な問題を解決する方法について説明します。
- [セキュリティの詳細](how-to-connect-pta-security-deep-dive.md): パススルー認証機能に関する詳細な技術情報を取得します。
- [Azure AD シームレス SSO](how-to-connect-sso.md): この補完的な機能の詳細について説明します。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Azure Active Directory フォーラムを使用して、新しい機能の要求を行います。


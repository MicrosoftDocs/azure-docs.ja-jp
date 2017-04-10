---
title: "概要: Azure AD でのパスワード管理 | Microsoft Docs"
description: "自分のパスワードのリセットを可能にし、パスワード リセットの前提条件を学習するほか、パスワード ライトバックを有効化してオンプレミスの Active Directory パスワードを管理します。"
services: active-directory
keywords: "Active Directory パスワード管理, パスワード管理, Azure AD パスワードのリセット"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: c2c46637ccccd01c1c3056d6a25ef605cfd68f2d
ms.lasthandoff: 03/29/2017


---
# <a name="getting-started-with-password-management"></a>パスワード管理の概要
> [!IMPORTANT]
> **サインインに問題がありますか?** その場合は、 [自分のパスワードを変更してリセットする方法をここから参照してください](active-directory-passwords-update-your-own-password.md#reset-your-password)。
>
>

ユーザーは、簡単な手順を実行するだけで、自分のクラウドの Azure Active Directory パスワードまたはオンプレミス Active Directory パスワードを管理できるようになります。 いくつかの簡単な前提条件を満たせば、すぐに組織全体のパスワードの変更とリセットが実行できるようになります。 この記事では、次の概念を説明します。

* [**開始する前に読む必要がある、お客様からの重要なヒント**](#top-tips-from-our-customers-to-read-before-you-begin)
 * [**重要なヒント: ドキュメント ナビゲーション** - 目次とブラウザーの検索機能を使用して、回答を検索する](#top-tip-documentation-navigation---use-our-table-of-contents-and-your-browsers-find-feature-to-find-answers)
 * [**ヒント 1: ライセンス** - ライセンスの要件を確実に把握する](#tip-1-licensing---make-sure-you-understand-the-licensing-requirements)
 * [**ヒント 2: テスト** - 管理者ではなく、エンド ユーザーとテストを実施し、少数のユーザーによる試験運用を行う](#tip-2-testing---test-with-an-end-user-not-an-administrator-and-pilot-with-a-small-set-of-users)
 * [**ヒント 3: デプロイ** - ユーザーが登録する必要のないように、ユーザーのデータを事前に設定する](#tip-3-deployment---pre-populate-data-for-your-users-so-they-dont-have-to-register)
 * [**ヒント 4: デプロイ** - パスワード リセットを使用して、一時パスワードを連絡する必要性を取り除く](#tip-4-deployment---use-password-reset-to-obviate-the-need-to-communicate-temporary-passwords)
 * [**ヒント 5: ライトバック** - パスワード ライトバックのトラブルシューティングを行う際は、AAD Connect マシン上のアプリケーション イベント ログを調べる](#tip-5-writeback---look-at-the-application-event-log-on-your-aad-connect-machine-to-troubleshoot-password-writeback)
 * [**ヒント 6: ライトバック** - パスワード ライトバック用の適切なアクセス許可、ファイアウォール規則、および接続設定が有効になっていることを確認する](#tip-6-writeback---ensure-you-enable-the-correct-permissions-firewall-rules-and-connection-settings-for-password-writeback)
 * [**ヒント 7: レポート** - 誰が登録しているかやパスワードをリセットしているかは Azure AD SSPR 監査ログで確認する](#tip-7-reporting---see-who-is-registering-or-resetting-passwords-with-the-azure-ad-sspr-audit-logs)
 * [**ヒント 8: トラブルシューティング** - さまざまな問題を解決する際は、トラブルシューティング ガイドと FAQ を参照する](#tip-8-troubleshoot---read-our-troubleshooting-guide-and-faq-to-solve-many-issues)
 * [**ヒント 9: トラブルシューティング** - それでも解決しない場合は、サポートに必要な情報を提供する](#tip-9-troubleshoot---if-you-still-need-help-include-enough-information-for-us-to-assist-you)
* [**Azure Active Directory パスワードのユーザーによるリセットを有効にする方法**](#enable-users-to-reset-their-azure-ad-passwords)
 * [セルフサービスのパスワード リセットの前提条件](#prerequisites)
 * [手順 1: パスワードのリセット ポリシーを構成する](#step-1-configure-password-reset-policy)
 * [手順 2: テスト ユーザー用の連絡先データを追加する](#step-2-add-contact-data-for-your-test-user)
 * [手順 3: ユーザーとしてパスワードをリセットする](#step-3-reset-your-azure-ad-password-as-a-user)
* [**オンプレミス Active Directory パスワードのユーザーによるリセットまたは変更を有効にする方法**](#enable-users-to-reset-or-change-their-ad-passwords)
 * [パスワード ライトバックの前提条件](#writeback-prerequisites)
 * [手順 1: Azure AD Connect の最新バージョンをダウンロードする](#step-1-download-the-latest-version-of-azure-ad-connect)
 * [手順 2: UI または PowerShell を使用して Azure AD Connect でパスワード ライトバックを有効にしてから確認する](#step-2-enable-password-writeback-in-azure-ad-connect)
 * [手順 3: ファイアウォールを構成する](#step-3-configure-your-firewall)
 * [手順 4: 適切な権限を設定する](#step-4-set-up-the-appropriate-active-directory-permissions)
 * [手順 5: ユーザーとして AD パスワードをリセットしてから確認する](#step-5-reset-your-ad-password-as-a-user)

## <a name="top-tips-from-our-customers-to-read-before-you-begin"></a>開始する前に読む必要がある、お客様からの重要なヒント
お客様の組織にパスワード管理をデプロイする際に役立つと判断した重要なヒントをいくつか次に紹介します。

* [**重要なヒント: ドキュメント ナビゲーション** - 目次とブラウザーの検索機能を使用して、回答を検索する](#top-tip-documentation-navigation---use-our-table-of-contents-and-your-browsers-find-feature-to-find-answers)
* [**ヒント 1: ライセンス** - ライセンスの要件を確実に把握する](#tip-1-licensing---make-sure-you-understand-the-licensing-requirements)
* [**ヒント 2: テスト** - 管理者ではなく、エンド ユーザーとテストを実施し、少数のユーザーによる試験運用を行う](#tip-2-testing---test-with-an-end-user-not-an-administrator-and-pilot-with-a-small-set-of-users)
* [**ヒント 3: デプロイ** - ユーザーが登録する必要のないように、ユーザーのデータを事前に設定する](#tip-3-deployment---pre-populate-data-for-your-users-so-they-dont-have-to-register)
* [**ヒント 4: デプロイ** - パスワード リセットを使用して、一時パスワードを連絡する必要性を取り除く](#tip-4-deployment---use-password-reset-to-obviate-the-need-to-communicate-temporary-passwords)
* [**ヒント 5: ライトバック** - パスワード ライトバックのトラブルシューティングを行う際は、AAD Connect マシン上のアプリケーション イベント ログを調べる](#tip-5-writeback---look-at-the-application-event-log-on-your-aad-connect-machine-to-troubleshoot-password-writeback)
* [**ヒント 6: ライトバック** - パスワード ライトバック用の適切なアクセス許可、ファイアウォール規則、および接続設定が有効になっていることを確認する](#tip-6-writeback---ensure-you-enable-the-correct-permissions-firewall-rules-and-connection-settings-for-password-writeback)
* [**ヒント 7: レポート** - 誰が登録しているかやパスワードをリセットしているかは Azure AD SSPR 監査ログで確認する](#tip-7-reporting---see-who-is-registering-or-resetting-passwords-with-the-azure-ad-sspr-audit-logs)
* [**ヒント 8: トラブルシューティング** - さまざまな問題を解決する際は、トラブルシューティング ガイドと FAQ を参照する](#tip-8-troubleshoot---read-our-troubleshooting-guide-and-faq-to-solve-many-issues)
* [**ヒント 9: トラブルシューティング** - それでも解決しない場合は、サポートに必要な情報を提供する](#tip-9-troubleshoot---if-you-still-need-help-include-enough-information-for-us-to-assist-you)

### <a name="top-tip-documentation-navigation---use-our-table-of-contents-and-your-browsers-find-feature-to-find-answers"></a>重要なヒント: ドキュメント ナビゲーション - 目次とブラウザーの検索機能を使用して、回答を検索する
どのドキュメントを使用する場合でも、Microsoft では、学習を目的としている管理者にとって興味深い項目へのクイックリンクをすべて目次に表示するよう努めています。 

以下の目次を確認してください。 
* [Azure AD のパスワード リセット: ドキュメントの目次](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)

### <a name="tip-1-licensing---make-sure-you-understand-the-licensing-requirements"></a>ヒント 1: ライセンス - ライセンスの要件を確実に把握する
Azure AD のパスワード リセットが機能するには、組織で少なくとも 1 つのライセンスが割り当てられている必要があります。 パスワード リセット機能自体については、ユーザー数によるライセンスを設けていません。ただし、ユーザーにライセンスを割り当てずにこの機能を使用した場合は、Microsoft ライセンス契約に準拠していないと見なされ、それらのユーザーにライセンスを割り当てる必要があります。

パスワード リセットを利用するために必要なライセンスを理解するのに役立つドキュメントをいくつか紹介します。
* [パスワード リセットのライセンスに関する全般的な情報](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-customize#what-customization-options-are-available)
* [パスワード リセットのライセンスに関する機能別の情報](https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability)
* [パスワード ライトバックでサポートされているシナリオ](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#scenarios-supported-for-password-writeback)

### <a name="tip-2-testing---test-with-an-end-user-not-an-administrator-and-pilot-with-a-small-set-of-users"></a>ヒント 2: テスト - 管理者ではなく、エンド ユーザーとテストを実施し、少数のユーザーによる試験運用を行う
管理者とテストする場合は、以下に示す管理者のパスワード リセット ポリシーが適用されます。  そのため、エンド ユーザー用に構成したポリシーの期待した結果は確認できません。

管理 UX で構成したポリシーは、管理者ではなく、エンド ユーザーにのみ適用されます。 Microsoft では、組織の安全性を確実に維持するために、管理者に対して既定の強力なパスワード リセット ポリシーを適用します。このポリシーは、エンド ユーザーに対して設定したポリシーとは異なる場合があります。

#### <a name="administrator-password-reset-policy"></a>管理者のパスワード リセット ポリシー
* **適用対象** - すべての管理者ロール (グローバル管理者、ヘルプデスク管理者、パスワード管理者など)
* **ワン ゲート ポリシーが適用される場合**
 * 試用版の作成を開始してから最初の 30 日間、**または**
 * バニティ ドメインが存在しない、**かつ** Azure AD Connect が ID を同期していない
 * **_必要条件_**: 認証用電子メール、連絡用電子メール アドレス、認証用電話、携帯電話、または会社電話のうちのいずれか **1 つ**の値が存在すること
* **ツー ゲート ポリシーが適用される場合** 
 * 試用版の最初の 30 日間が経過した後、**または**
 * バニティ ドメインが存在する、**または** 
 * Azure AD Connect がオンプレミス環境から ID を同期できるようにしている
 * _**必要条件**_: 認証用電子メール、連絡用電子メール アドレス、認証用電話、携帯電話、または会社電話のうちのいずれか **2 つ**の値が存在すること

### <a name="tip-3-deployment---pre-populate-data-for-your-users-so-they-dont-have-to-register"></a>ヒント 3: デプロイ - ユーザーが登録する必要のないように、ユーザーのデータを事前に設定する
パスワード リセット機能を使用するうえで、ユーザーによる登録が必要ないことを知らない人がたくさんいます。  ユーザーの電話または電子メールのプロパティを事前に設定しておくと、組織全体にパスワード リセットをすぐに展開できます。ユーザーは何もする必要が**ありません**。

API、PowerShell、または Azure AD Connect を使用してこれを行う方法については、次のドキュメントを参照してください。
* [ユーザーによる登録を必要としないパスワード リセットのデプロイ](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#deploying-password-reset-without-requiring-end-user-registration)
* [パスワードのリセットで使用されるデータ](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-data-is-used-by-password-reset)

### <a name="tip-4-deployment---use-password-reset-to-obviate-the-need-to-communicate-temporary-passwords"></a>ヒント 4: デプロイ - パスワード リセットを使用して、一時パスワードを連絡する必要性を取り除く
これは、ヒント 3 に関連しています。 ユーザーをパスワード リセット用に事前に構成したら、新入社員が入社した場合のシナリオについて考えてみます。 新入社員に一時パスワードを連絡する代わりに、[Azure AD パスワード リセット ポータル](https://passwordreset.microsoftonline.com)に案内して、パスワードをリセットしてもらうことができます。

ユーザーは、[Windows 10 Azure AD ドメイン参加デバイス](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy)を使用している場合、Windows 10 の標準のサインイン画面から直接リセットし、新しい PC にアクセスすることもできます。管理者は何もする必要がありません。

API、PowerShell、または Azure AD Connect を使用してこれを行う方法については、次のドキュメントを参照してください。 このデータを事前に設定すると、ユーザーにパスワードをリセットしてもらうことで、ユーザーは自分のアカウントにすぐアクセスできるようになります。
* [ユーザーによる登録を必要としないパスワード リセットのデプロイ](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#deploying-password-reset-without-requiring-end-user-registration)
* [パスワードのリセットで使用されるデータ](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-data-is-used-by-password-reset)

### <a name="tip-5-writeback---look-at-the-application-event-log-on-your-aad-connect-machine-to-troubleshoot-password-writeback"></a>ヒント 5: ライトバック - パスワード ライトバックのトラブルシューティングを行う際は、AAD Connect マシン上のアプリケーション イベント ログを調べる
Azure AD Connect アプリケーション イベント ログには、パスワード ライトバック サービスに関連して発生したほとんどのイベントに関するリアルタイムの豊富なログ情報が含まれています。 このログにアクセスするには、次の手順に従います。

1. **Azure AD Connect** マシンにサインインします。
2. **[スタート]** ボタンを押し、「**イベント ビューアー**」と入力して、**Windows イベント ビューアー**を開きます。
3. **アプリケーション** イベント ログを開きます。
4. **PasswordResetService** または **ADSync** のイベントを探して、発生している可能性のある問題の詳細を確認します。

このログに表示されるイベントの完全な一覧と、パスワード ライトバックのトラブルシューティング ガイダンスについては、次を参照してください。
* [トラブルシューティング: パスワード ライトバック](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback)
* [ライトバックのイベント ログのエラー コード](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
* [トラブルシューティング: パスワード ライトバックの接続](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity)
* [ライトバックのデプロイ - 手順 3: ファイアウォールを構成する](#step-3-configure-your-firewall)
* [ライトバックのデプロイ - 手順 4: 適切な権限を設定する](#step-4-set-up-the-appropriate-active-directory-permissions)

### <a name="tip-6-writeback---ensure-you-enable-the-correct-permissions-firewall-rules-and-connection-settings-for-password-writeback"></a>ヒント 6: ライトバック - パスワード ライトバック用の適切なアクセス許可、ファイアウォール規則、および接続設定が有効になっていることを確認する
ライトバックが正常に機能するためには、以下を確認する必要があります。

1. パスワード ライトバックを使用するユーザーに対して適切な **Active Directory のアクセス許可**が設定されており、ユーザーが AD で自分のパスワードおよびアカウントのロック解除フラグを変更する権限を持っている
2. パスワード ライトバック サービスが送信接続を使用して外部と安全に通信できるように適切な**ファイアウォール ポート**が開いている
3. Service Bus など、主要なパスワード リセット サービスの URL に対して適切な**ファイアウォール例外**が設定されている
4. **プロキシとファイアウォールでアイドル状態の送信接続が強制終了されない** (10 分以上をお勧めします)

パスワード ライトバック用のアクセス許可とファイアウォール規則の構成に関するトラブルシューティング ガイダンスおよび固有のガイドラインの完全な一覧については、次を参照してください。
* [トラブルシューティング: パスワード ライトバック](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback)
* [ライトバックのイベント ログのエラー コード](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
* [トラブルシューティング: パスワード ライトバックの接続](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity)
* [ライトバックのデプロイ - 手順 3: ファイアウォールを構成する](#step-3-configure-your-firewall)
* [ライトバックのデプロイ - 手順 4: 適切な権限を設定する](#step-4-set-up-the-appropriate-active-directory-permissions)

### <a name="tip-7-reporting---see-who-is-registering-or-resetting-passwords-with-the-azure-ad-sspr-audit-logs"></a>ヒント 7: レポート - 誰が登録しているかやパスワードをリセットしているかは Azure AD SSPR 監査ログで確認する 
パスワード リセットがデプロイされて機能したら、次のステップとして、動作していることを確認し、それでも登録が必要なユーザー、リセットした場合にユーザーが直面する一般的な問題、将来の投資収益率を分析します。

Azure AD のパスワード リセット監査ログを使用すると、これ以外にもさまざまなことを Azure Portal、PowerBI、Azure AD Reporting Events API、または PowerShell から実行できます。  これらのレポート機能を使用する方法の詳細については、次を参照してください。
* [パスワード管理レポートの概要](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#overview-of-password-management-reports)
* [Azure Portal でパスワード管理レポートを表示する方法](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#how-to-view-password-management-reports)
* [Azure Portal でのセルフ サービスのパスワード管理アクティビティの種類](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#self-service-password-management-activity-types-in-the-new-azure-portal)
* [Azure AD レポートおよびイベント API からパスワード管理イベントを取得する方法](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api)
* [PowerShell を使用してパスワード リセット登録イベントをすばやくダウンロードする方法](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#how-to-download-password-reset-registration-events-quickly-with-powershell)

### <a name="tip-8-troubleshoot---read-our-troubleshooting-guide-and-faq-to-solve-many-issues"></a>ヒント 8: トラブルシューティング - さまざまな問題を解決する際は、トラブルシューティング ガイドと FAQ を参照する
パスワード リセットには、豊富なトラブルシューティング ガイダンスと FAQ が用意されています。 質問がある場合は、以下のリンク先で答えを見つけることができます。

さらに、[Azure Portal](https://portal.azure.com) の **[サポートとトラブルシューティング]** ブレードを使用して、(左側のナビゲーション ウィンドウの **[Azure Active Directory]** -> **[ユーザーとグループ]** -> **[パスワードのリセット]** -> **[サポートとトラブルシューティング]** にあるパスワード管理の管理者ユーザー エクスペリエンスから直接) 豊富なトラブルシューティング コンテンツにアクセスすることもできます。

パスワード リセットのトラブルシューティング ガイダンスと FAQ へのリンク:
* [パスワード管理のトラブルシューティング](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot)
* [パスワード管理に関する FAQ](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-faq)

### <a name="tip-9-troubleshoot---if-you-still-need-help-include-enough-information-for-us-to-assist-you"></a>ヒント 9: トラブルシューティング - それでも解決しない場合は、サポートに必要な情報を提供する
それでもトラブルシューティングのサポートが必要な場合は、心配は要りません。 サポート ケースを開くか、アカウント管理チームに連絡して、Microsoft に直接問い合わせてもらうことができます。 お気軽にお問い合わせください。

ただし、迅速に解決するために、問い合わせる前に、**以下で求められている情報がすべて集まっていることを確認**してください。
* [ヘルプが必要な場合に含める情報](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#information-to-include-when-you-need-help)

#### <a name="ways-to-provide-password-reset-feedback"></a>パスワード リセットのフィードバックを提供する方法
* [機能に関する要求またはトラブルシューティング - Azure AD MSDN フォーラムに投稿する](https://social.msdn.microsoft.com/Forums/azure/home?forum=WindowsAzureAD)
* [機能に関する要求またはトラブルシューティング - StackOverflow に投稿する](http://stackoverflow.com/questions/tagged/azure-active-directory)
* [機能に関する要求またはトラブルシューティング - @azuread! でツイートする](https://twitter.com/azuread)
* [機能に関する要求のみ - UserVoice に書き込む](https://feedback.azure.com/forums/169401-azure-active-directory)

## <a name="enable-users-to-reset-their-azure-ad-passwords"></a>ユーザーによる Azure AD パスワードのリセットを有効にする
このセクションでは、AAD クラウド ディレクトリに対してセルフサービスのパスワード リセットを有効化し、セルフサービスのパスワード リセットが実行できるようにユーザーを登録し、最後にユーザーとしてセルフサービスのパスワード リセットのテストを実行する方法について説明します。

* [セルフサービスのパスワード リセットの前提条件](#prerequisites)
* [手順 1: パスワードのリセット ポリシーを構成する](#step-1-configure-password-reset-policy)
* [手順 2: テスト ユーザー用の連絡先データを追加する](#step-2-add-contact-data-for-your-test-user)
* [手順 3: ユーザーとしてパスワードをリセットする](#step-3-reset-your-azure-ad-password-as-a-user)

### <a name="prerequisites"></a>前提条件
セルフサービスのパスワード リセットを有効にして使用するには、次の前提条件を満たす必要があります。

* AAD テナントを作成する。 詳細については、 [Azure AD の使用](https://azure.microsoft.com/trial/get-started-active-directory/)
* Azure サブスクリプションを取得する。 詳細については、 [What is an Azure AD tenant? (Azure AD テナントとは)](active-directory-administer.md#what-is-an-azure-ad-tenant)を参照してください。
* AAD テナントを Azure サブスクリプションに関連付ける。 詳細については、 [Azure サブスクリプションを Azure AD に関連付ける方法](https://msdn.microsoft.com/library/azure/dn629581.aspx)を参照してください。
* Azure AD Premium、Azure AD Basic にアップグレードするか、または O365 有料ライセンスを使用します。 詳細については、 [Azure Active Directory のエディション](https://azure.microsoft.com/pricing/details/active-directory/)を参照してください。

  > [!NOTE]
  > クラウド ユーザーのセルフサービス パスワード リセットを有効にするには、Azure AD Premium、Azure AD Basic、または有料 O365 ライセンスにアップグレードする必要があります。  オンプレミスのユーザーのセルフサービスのパスワード リセットを有効にするには、Azure AD Premium にアップグレードする必要があります。 詳細については、 [Azure Active Directory のエディション](https://azure.microsoft.com/pricing/details/active-directory/)を参照してください。 この情報には、Azure AD Premium または Basic にサインアップする方法、ライセンス プランをアクティブ化して Azure AD アクセスをアクティブ化する方法、および管理者とユーザーのアカウントにアクセス権を割り当てる方法の詳細が含まれます。
  >
  >
* AAD ディレクトリで、管理者アカウントとユーザー アカウントを少なくとも 1 つずつ作成する。
* AAD Premium、Basic、または O365 有料ライセンスを、作成済みの管理者およびユーザーのアカウントに割り当てる。

### <a name="step-1-configure-password-reset-policy"></a>手順 1: パスワードのリセット ポリシーを構成する
ユーザー パスワードのリセット ポリシーを構成するには、以下の手順を実行します。

1. 任意のブラウザーを開いて、[Azure クラシック ポータル](https://manage.windowsazure.com)に移動します。
2. [Azure クラシック ポータル](https://manage.windowsazure.com)の左側のナビゲーション バーで **Active Directory 拡張機能**を選択します。

   ![Azure AD でのパスワード管理][001]
3. [ **ディレクトリ** ] タブで、Wingtip Toys など、ユーザー パスワードのリセット ポリシーを構成するディレクトリをクリックします。

    ![][002]
4. [ **構成** ] タブをクリックします。

   ![][003]

5. **[構成]** タブで、下へスクロールして **[ユーザー パスワードのリセット ポリシー]** セクションを表示します。  ここでは、特定のディレクトリについて、ユーザー パスワードのリセット ポリシーのあらゆる側面を構成できます。 "*[構成] タブが表示されない場合は、Azure Active Directory Premium または Basic にサインアップ済みであることと、この機能の構成を行う管理者アカウントに__ライセンスを割り当て済み__であることを確認してください。*"  

   > [!NOTE]
   > **設定したポリシーが適用されるのは組織内のエンド ユーザーのみで、管理者には適用されません**。 セキュリティ上の理由から、管理者のパスワード リセット ポリシーの管理はマイクロソフトが行います。 現在の管理者用ポリシーでは、携帯電話および電子メール アドレスという 2 つのチャレンジが要求されます。

   ![][004]
6. ユーザー パスワードのリセット ポリシーを構成するには、**[パスワードのリセットが有効になっているユーザー]** トグルを **[はい]** に設定します。  この操作を行うと、組織のディレクトリでのこの機能の動作を構成するためのさまざまなコントロールが追加表示されます。  組織のニーズに合わせて自由にパスワード リセットをカスタマイズしてください。  パスワード リセット ポリシーの各コントロールの機能の詳細については、 [Customize: Azure AD Password Management (カスタマイズ: Azure AD でのパスワード管理)](active-directory-passwords-customize.md)を参照してください。

   ![][005]
7. テナントのユーザー パスワード リセット ポリシーの構成が完了したら、画面の一番下にある [ **保存** ] ボタンをクリックします。

   > [!NOTE]
   > 最も複雑な状況でこの機能がどのように動作するかを確認できるよう、ユーザー パスワードのリセット ポリシーのチャレンジは 2 つにすることをお勧めします。
   >
   >

   ![][006]

   > [!NOTE]
   > **設定したポリシーが適用されるのは組織内のエンド ユーザーのみで、管理者には適用されません**。 セキュリティ上の理由から、管理者のパスワード リセット ポリシーの管理はマイクロソフトが行います。 現在の管理者用ポリシーでは、携帯電話および電子メール アドレスという 2 つのチャレンジが要求されます。
   >
   >

### <a name="step-2-add-contact-data-for-your-test-user"></a>手順 2: テスト ユーザー用の連絡先データを追加する
組織内のユーザーがパスワードのリセットに使用するデータを指定する方法はいくつかあります。

* [Azure クラシック ポータル](https://manage.windowsazure.com)または [Office 365 管理ポータル](https://portal.microsoftonline.com)でユーザーを編集する
* AAD Connect を使用して、ユーザー プロパティをオンプレミスの Active Directory ドメインから Azure AD に同期する
* Windows PowerShell を使用してユーザー プロパティを編集する
* ユーザーに登録ポータル ( [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
* SSPR の構成オプション **[ユーザーが初めてアクセス パネルにサインインするときに登録を要求]** を **[はい]** に設定して、ユーザーがアクセス パネル ([http://myapps.microsoft.com](http://myapps.microsoft.com)) にサインインしたときにパスワード リセットの登録を求める

パスワード リセットで使用されるデータ、およびこのデータの形式の要件についての詳細は、 [What data is used by password reset? (パスワードのリセットで使用されるデータとは)](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset)を参照してください。

#### <a name="to-add-user-contact-data-via-the-user-registration-portal"></a>ユーザー登録ポータルからユーザーの連絡先データを追加するには
1. パスワード リセットの登録ポータルを使用するには、組織内のユーザーにこのページへのリンク ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)) を連絡するか、ユーザーに対して自動登録を求めるオプションをオンにする必要があります。  ユーザーがこのリンクをクリックすると、組織のアカウントを使ってサインインするよう求められます。  サインインが終わると、次のページが表示されます。

   ![][007]
2. ここでは、携帯電話、代替電子メール アドレス、またはセキュリティの質問の指定や確認ができます。  携帯電話の番号の確認画面は、以下のようになります。

   ![][008]
3. ユーザーがこの情報を指定すると、ページが更新され、情報が有効である旨が表示されます (以下の画面では、情報を非表示にしています)。  ユーザーが **[完了]** ボタンまたは **[キャンセル]** ボタンをクリックすると、アクセス パネルが表示されます。

   ![][009]
4. ユーザーがここに挙げた情報を 2 つとも確認すると、入力されたデータでプロファイルが更新されます。  この例では、 **会社電話番号** が手動で指定されたため、この番号をパスワードをリセットする際の連絡方法として利用できます。

   ![][010]

### <a name="step-3-reset-your-azure-ad-password-as-a-user"></a>手順 3: ユーザーとして Azure AD パスワードをリセットする
これでユーザーのリセット ポリシーが構成され、ユーザーの連絡先の詳細が指定できたので、このユーザーは、セルフサービスのパスワード リセットを実行できます。

#### <a name="to-perform-a-self-service-password-reset"></a>セルフサービスのパスワード リセットを実行するには
1. [**portal.microsoftonline.com**](http://portal.microsoftonline.com) などのサイトにアクセスすると、以下のようなログイン画面が表示されます。  [ **アカウントにアクセスできません** ] リンクをクリックして、パスワード リセットの UI をテストします。

   ![][011]
2. **[アカウントにアクセスできない場合]** をクリックすると新しいページが表示され、パスワードをリセットする**ユーザー ID** の入力を求められます。  テスト **ユーザー ID** と captcha を入力し、**[次へ]** をクリックします。

   ![][012]
3. この場合、ユーザーは**会社電話**、**携帯電話**、および**代替電子メール**を指定していたため、最初のチャレンジの選択肢にこれら 3 つがすべて表示されています。

   ![][013]
4. 今回は、最初に**会社電話**に**電話する**ことを選択します。  電話ベースの方法を選択した場合、パスワードをリセットするには **電話番号の確認** が求められます。  これには、悪意のある個人によって組織内のユーザーの電話番号が漏洩されることを防ぐ目的があります。

   ![][014]
5. ユーザーが電話番号を確認した後は、[通話] ウォールをクリックするとスピナーが表示され、電話が鳴ります。  ユーザーが電話を取ると、アカウントの確認のため **「#」 を押すよう** 指示するメッセージが再生されます。  # キーを押すと、ユーザーが最初のチャレンジをクリアしたことが自動的に確認され、2 番目の確認手順の UI に進みます。

   ![][015]
6. 最初のチャレンジをクリアすると、UI が自動的に更新され、クリアしたチャレンジが選択肢の一覧から除外されます。  今回は、**会社電話**を最初に使用しているため、2 番目の確認手順に利用できるチャレンジの選択肢には、**携帯電話**と**代替電子メール**のみが残っています。  [ **連絡用電子メール アドレスにメールを送信** ] オプションをクリックします。  次に [電子メール] をクリックすると、登録済みの連絡用電子メール アドレスにメールが送信されます。

   ![][016]
7. ユーザーに送信される電子メールのサンプルを次に示します。テナントのブランド化に注目してください。

   ![][017]
8. 電子メールが届くとページが更新され、電子メールに記載されている確認コードを、以下に示す入力ボックスに入力できるようになります。  コードが正しく入力されると、[次へ] ボタンが点灯し、2 番目の確認手順が完了となります。

   ![][018]
9. 組織のポリシーの要件をクリアすると、新しいパスワードを作成できるようになります。  ここで作成するパスワードは、AAD の "強力な" パスワードに関する要件 (「[Azure AD でのパスワード ポリシー](https://msdn.microsoft.com/library/azure/jj943764.aspx)」を参照) を満たすかどうかで検証されます。入力されたパスワードがこのポリシーに合致しているかどうかを示す、パスワードの強度検証コントロールが表示されます。

   ![][019]
10. 組織のポリシーに合致するパスワードを指定すると、パスワードがリセットされ、直ちに新しいパスワードでログインできるようになります。

    ![][020]

## <a name="enable-users-to-reset-or-change-their-ad-passwords"></a>ユーザーによる Azure AD パスワードのリセットまたは変更を有効にする
このセクションでは、オンプレミスの Active Directory パスワードにライトバックするためのパスワード リセットの構成方法を説明します。

* [パスワード ライトバックの前提条件](#writeback-prerequisites)
* [手順 1: Azure AD Connect の最新バージョンをダウンロードする](#step-1-download-the-latest-version-of-azure-ad-connect)
* [手順 2: UI または PowerShell を使用して Azure AD Connect でパスワード ライトバックを有効にしてから確認する](#step-2-enable-password-writeback-in-azure-ad-connect)
* [手順 3: ファイアウォールを構成する](#step-3-configure-your-firewall)
* [手順 4: 適切な権限を設定する](#step-4-set-up-the-appropriate-active-directory-permissions)
* [手順 5: ユーザーとして AD パスワードをリセットしてから確認する](#step-5-reset-your-ad-password-as-a-user)

### <a name="writeback-prerequisites"></a>ライトバックの前提条件
ライトバックを有効にして使用する前に、次の前提条件を満たす必要があります。

* Azure AD テナントで Azure AD Premium が有効になっている。  詳細については、 [Azure Active Directory のエディション](active-directory-editions.md)を参照してください。
* パスワード ライトバックを機能させるには、Azure AD Connect をプライマリ ドメイン コントローラー エミュレーターに接続する必要があります。  必要に応じて、プライマリ ドメイン コントローラーを使用するよう Azure AD Connect を構成できます。そのためには、Active Directory 同期コネクタの**プロパティ**を右クリックして **[configure directory partitions (ディレクトリ パーティションの構成)]** を選択します。 そこで **[domain controller connection settings (ドメイン コントローラーの接続の設定)]** セクションを探して、**[only use preferred domain controllers (優先ドメイン コントローラーのみを使用する)]** というチェック ボックスをオンにします。  注: 優先 DC が PDC エミュレーターでない場合も、Azure AD Connect はパスワード ライトバックのために PDC に接続されます。
* テナントでパスワード リセットが構成され、有効になっている。  詳細については、 [ユーザーによる Azure AD パスワードのリセットを有効にする](#enable-users-to-reset-their-azure-ad-passwords)
* 管理者アカウントと、この機能をテストするために使用できる Azure AD Premium ライセンスが付与されたテスト ユーザー アカウントが少なくとも 1 つずつある。  詳細については、 [Azure Active Directory のエディション](active-directory-editions.md)を参照してください。

  > [!NOTE]
  > パスワード ライトバックの有効化に使用する管理者アカウントは、クラウドの管理者アカウント (Azure AD で作成された) であり、フェデレーション アカウント (オンプレミスの AD で作成され、Azure AD に同期された) ではありません。
  >
  >
* Windows Server 2008、Windows Server 2008 R2、Windows Server 2012、または Windows Server 2012 R2 を最新のサービス パックをインストールして実行している、1 つまたは複数のマルチフォレスト AD オンプレミスのデプロイを使用している。

  > [!NOTE]
  > 古いバージョンの Windows Server 2008 または 2008 R2 を実行している場合でもこの機能を使用できますが、クラウドでローカル AD パスワード ポリシーを適用するには、 [KB 2386717 をダウンロードおよびインストール](https://support.microsoft.com/kb/2386717) する必要があります。
  >
  >
* Azure AD Connect ツールをインストールし、クラウドに同期するための AD 環境が準備されている。  詳細については、 [クラウド内のオンプレミスの ID インフラストラクチャの使用](connect/active-directory-aadconnect.md)を参照してください。

  > [!NOTE]
  > パスワード ライトバックをテストする前に、Azure AD Connect で AD と Azure AD の両方から、フル インポートと完全同期を完了していることを確認してください。
  >
  >
* Azure AD Sync または Azure AD Connect を使用している場合は、**TCP 送信ポート 443** (場合によっては **TCP 9350-9354**) を開く必要があります。  詳細については、「 [手順 3: ファイアウォールを構成する](#step-3-configure-your-firewall) 」を参照してください。 このシナリオでの DirSync の使用はサポートされなくなりました。  DirSync をまだ使用している場合は、パスワード ライトバックをデプロイする前に Azure AD Connect の最新バージョンにアップグレードしてください。

  > [!NOTE]
  > Azure AD Sync ツールまたは DirSync ツールを使用している場合、優れたエクスペリエンスと、リリースされる新しい機能を利用できるように、Azure AD Connect の最新バージョンにアップグレードすることを強くお勧めします。
  >
  >

### <a name="step-1-download-the-latest-version-of-azure-ad-connect"></a>手順 1: Azure AD Connect の最新バージョンをダウンロードする
パスワード ライトバックは、Azure AD Connect のリリース、またはバージョン番号が **1.0.0419.0911** 以降の Azure AD Sync ツールで使用できます。  アカウントが自動的にロック解除されるパスワード ライトバックが使用できるのは、Azure AD Connect のリリース、またはバージョン番号が **1.0.0485.0222** 以降の Azure AD Sync ツールです。 以前のバージョンを実行している場合は、続行する前に、少なくともこのバージョンにアップグレードしてください。 [ここをクリックすると、Azure AD Connect の最新バージョンをダウンロードできます。](connect/active-directory-aadconnect.md#install-azure-ad-connect)

#### <a name="to-check-the-version-of-azure-ad-sync"></a>Azure AD Sync のバージョンを確認するには
1. **%ProgramFiles%\Azure Active Directory Sync\** に移動します。
2. **ConfigWizard.exe** 実行可能ファイルを参照します。
3. この実行可能ファイルを右クリックして、 コンテキスト メニューから [ **プロパティ** ] オプションを選択します。
4. [ **詳細** ] タブをクリックします。
5. [ **ファイル バージョン** ] フィールドを参照します。

   ![][021]

このバージョン番号が **1.0.0419.0911** 以上の場合、または Azure AD Connect をインストールしている場合は、「[手順 2: UI または PowerShell を使用して Azure AD Connect でパスワード ライトバックを有効にしてから確認する](#step-2-enable-password-writeback-in-azure-ad-connect)」にスキップできます。

> [!NOTE]
> Azure AD Connect ツールを初めてインストールする場合は、いくつかのベスト プラクティスに従って、ディレクトリ同期の環境を準備することをお勧めします。  Azure AD Connect ツールをインストールする前に、[Office 365 管理ポータル](https://portal.microsoftonline.com)または [Azure クラシック ポータル](https://manage.windowsazure.com)でディレクトリ同期を有効にする必要があります。  詳細については、 [Managing Azure AD Connect (Azure AD Connect の管理)](active-directory-aadconnect-whats-next.md)を参照してください。
>
>

### <a name="step-2-enable-password-writeback-in-azure-ad-connect"></a>手順 2: Azure AD Connect でパスワード ライトバックを有効にする
Azure AD Connect ツールをダウンロードしたので、パスワード ライトバックを有効にする準備ができました。  次の 2 つの方法のいずれかで有効にします。  パスワード ライトバックは、Azure AD Connect のセットアップ ウィザードの [オプション機能] 画面で有効にできますし、Windows PowerShell を使用して有効にすることもできます。

#### <a name="to-enable-password-writeback-in-the-configuration-wizard"></a>構成ウィザードでパスワード ライトバックを有効にするには
1. **ディレクトリ同期コンピューター**上で、**Azure AD Connect** の構成ウィザードを開きます。
2. [ **オプション機能** ] 構成画面が表示されるまで、クリックして進みます。
3. [ **パスワード ライトバック** ] オプションをオンにします。

   ![][022]
4. ウィザードを完了します。最後のページには、パスワード ライトバックの構成の変更を含む、変更の概要が表示されます。

> [!NOTE]
> パスワード ライトバックはいつでも無効にできます。無効にするには、このウィザードを再実行してこの機能を選択解除するか、[Azure クラシック ポータル](https://manage.windowsazure.com)で、ディレクトリの **[構成]** タブの **[ユーザー パスワードのリセット ポリシー]** セクションにある **[オンプレミスのディレクトリにパスワードをライトバック]** を **[いいえ]** に設定します。  パスワード リセットのエクスペリエンスのカスタマイズの詳細については、[Azure AD でのパスワード管理のカスタマイズ](active-directory-passwords-customize.md)に関するページを参照してください。
>
>

#### <a name="to-enable-password-writeback-using-windows-powershell"></a>Windows PowerShell を使用して、パスワード ライトバックを有効にするには
1. **ディレクトリ同期コンピューター**上で、新しい**管理者特権の Windows PowerShell ウィンドウ**を開きます。
2. モジュールがまだ読み込まれていない場合は、`import-module ADSync` コマンドを入力して、Azure AD Connect コマンドレットを現在のセッションに読み込みます。
3. `Get-ADSyncConnector` コマンドレットを実行してシステム内の Azure AD コネクタの一覧を取得し、その結果を `$aadConnectorName = Get-ADSyncConnector|where-object {$_.name -like "*AAD"}` などの `$aadConnectorName` に保存します。
4. 次のコマンドレットを実行して、現在のコネクタについてライトバックの現在の状態を取得します。`Get-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName.name`
5. 次のコマンドレットを実行して、パスワード ライトバックを有効にします。 `Set-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName.name –Enable $true`

> [!NOTE]
> 資格情報を求められた場合、AzureADCredential に指定した管理者アカウントが、 **クラウドの管理者アカウント (Azure AD で作成された)**であり、フェデレーション アカウント (オンプレミスの AD で作成され、Azure AD に同期された) ではないことを確認します。
>
> [!NOTE]
> パスワード ライトバックを無効にするには、PowerShell を使用して前述の手順を繰り返すか (ただし、`$false` を渡します)、[Azure クラシック ポータル](https://manage.windowsazure.com)で、ディレクトリの **[構成]** タブの **[ユーザー パスワードのリセット ポリシー]** セクションにある **[オンプレミスのディレクトリにパスワードをライトバック]** を **[いいえ]** に設定します。
>
>

#### <a name="verify-that-the-configuration-was-successful"></a>構成が正常に完了したことを確認する
構成が正常に完了すると、[Windows PowerShell] ウィンドウに「パスワード リセット ライトバックが有効になりました。」というメッセージが表示されます。

サービスが正しくインストールされたことを確認するには、イベント ビューアーを開き、アプリケーション イベント ログに移動して、ソース **PasswordResetService** でイベント "**31005 - OnboardingEventSuccess**" が表示されているかどうかを確認します。

  ![][023]

### <a name="step-3-configure-your-firewall"></a>手順 3: ファイアウォールを構成する 
パスワード ライトバックを有効にした後は、Azure AD Connect を実行しているコンピューターが Microsoft のクラウド サービスにアクセスしてパスワード ライトバック要求を受信できるようにする必要があります。 この手順では、ネットワーク アプライアンス (プロキシ サーバー、ファイアウォールなど) の接続規則を更新し、[Microsoft が所有する一定の URL および IP アドレス](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US)に対して特定のネットワーク ポートを介して行われる送信方向の接続を許可します。 ここに示した変更作業は、Azure AD Connect ツールのバージョンに応じて異なります。 背景情報としての[パスワード ライトバックのしくみ](active-directory-passwords-learn-more.md#how-password-writeback-works)と[パスワード ライトバックのセキュリティ モデル](active-directory-passwords-learn-more.md#password-writeback-security-model)については、別のページで詳細を確認してください。

#### <a name="why-do-i-need-to-do-this"></a>必要な作業

パスワード ライトバックが正しく機能するためには、Azure AD Connect を実行するコンピューターがパスワード リセット サービスと Azure Service Bus と通信できる必要があります。

Azure AD Connect ツール **1.1.443.0** 以降の場合:

- 最新バージョンの Azure AD Connect ツールでは、以下のアドレスに対する**送信方向の HTTPS** アクセスが必要になります。
    - *passwordreset.microsoftonline.com*
    - *servicebus.windows.net*

Azure AD Connect ツールのバージョンが **1.0.8667.0** ～ **1.1.380.0** の場合:

- **オプション 1:**ポート 443 を介した送信方向の HTTPS 接続をすべて許可する (URL または IP アドレスを使用する)。
    - このオプションを使用する局面
        - 最も簡単な構成を希望する場合には、このオプションを使用します。このオプションでは、Azure データセンターの IP 範囲が時間と共に変化しても、構成を更新する必要がありません。
    - 必要な手順
        - URL または IP アドレスを使って、ポート 443 を介する送信方向の HTTPS 接続をすべて許可します。
<br><br>
- **オプション 2:** 一定の範囲の IP と URL に対する送信方向の HTTPS 接続を許可する。
    - このオプションを使用する局面
        - このオプションは、ネットワーク環境になんらかの制約がある場合や、何か他の事情により送信方向の接続を許可することが好ましくないと思われる場合に使用します。
        - この構成では、パスワード ライトバックを継続的に動作させるために週 1 回、ネットワーク アプライアンスの IP アドレスを Microsoft Azure データセンターの IP 範囲リストに記載の最新情報に更新する必要があります。 IP アドレスは XML ファイルで提供され、毎週水曜日 (太平洋標準時) に更新があり、次の週の月曜日 (太平洋標準時) に発効します。
    - 必要な手順
        - *.servicebus.windows.net に対する送信方向の HTTPS 接続をすべて許可します。
        - Microsoft Azure データセンターの IP 範囲リストに記載の IP 全部に対して HTTPS 接続を許可し、この構成を毎週更新していきます。 このリストは[こちら](https://www.microsoft.com/download/details.aspx?id=41653)からダウンロードできます。

> [!NOTE]
> 上に示した手順に従ってパスワード ライトバックを構成した際に、Azure AD Connect のイベント ログにエラーが表示されなかったものの、テスト時に接続エラーが発生する場合には、お使いの環境のネットワーク アプライアンスにより IP アドレスに対する HTTPS 接続がブロックされている可能性があります。 たとえば、*https://*.servicebus.windows.net* に対する接続は許可されているときに、その範囲内にある特定の IP アドレスへの接続がブロックされることがあります。 この問題を解決するには、ネットワーク環境の構成を変更してポート 443 を介する送信方向の HTTPS 接続を URL、IP アドレスを問わずすべて許可する (上のオプション 1 に相当) か、ネットワーク チームの協力の下で特定の IP アドレスに対する HTTPS 接続を明示的に許可する (上のオプション 2 に相当) 必要があります。

**以前のバージョンを使用している場合**

- ポート 443、9350～9354、およびポート 5671 を介した送信方向の TCP 接続を許可する
- *https://ssprsbprodncu-sb.accesscontrol.windows.net/* への送信接続をすべて許可する

> [!NOTE]
> Azure AD Connect のバージョンが 1.0.8667.0 よりも前の場合には、[最新バージョンの Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) にアップグレードすることを強くお勧めします。最新バージョンでは、ライトバック ネットワークに関する数々の機能が向上しており、構成を簡単に済ませることができます。

ネットワーク アプライアンスの構成が終わったら、Azure AD Connect ツールを実行しているコンピューターを再起動してください。

#### <a name="idle-connections-on-azure-ad-connect-114430-and-up"></a>Azure AD Connect (1.1.443.0 以降) でのアイドル接続
Azure AD Connect ツールでは、接続が現在も有効であることを確認するために、ping または keepalive が ServiceBus エンドポイントに送信されます。 接続の強制終了がツールであまりにも多く検出される場合は、エンドポイントへの ping の送信頻度が自動的に上がります。 最短の "ping の送信間隔" は 60 秒あたり 1 ping ですが、**プロキシやファイアウォールではアイドル接続を 2 ～ 3 分以上維持できるようにすることを強くお勧めします。** \*これより前のバージョンでは、4 分以上が推奨されます。

### <a name="step-4-set-up-the-appropriate-active-directory-permissions"></a>手順 4: Active Directory の適切な権限を設定する
パスワードをリセットするユーザーを含むすべてのフォレストについて、構成ウィザードでそのフォレストに対して (初期構成中に) 指定されたアカウントが X である場合、X には、そのフォレスト内の各ドメインのルート オブジェクトまたは SSPR の対象に含めるユーザー OU に対して、`lockoutTime` の **[パスワードのリセット]**、**[パスワードの変更]**、**[書き込みアクセス許可]** や `pwdLastSet` の **[書き込みアクセス許可]** など、拡張権限を付与する必要があります。  リセットのアクセス許可をドメインのルートに付与することが許容されないために、一連の特定のユーザー オブジェクトのみに限定する場合は、後者のオプションを使用できます。 この権限は、すべてのユーザー オブジェクトにより継承されるものとしてマークされます。  

上記のどのアカウントが参照されるか明らかでない場合は、Azure Active Directory Connect の構成 UI を開き、 **[ソリューションの確認]** オプションをクリックします。  以下のスクリーンショットで、アクセス許可を追加する必要があるアカウントには赤色の下線が表示されています。

**<font color="red">システムの各フォレストでドメインごとに、このアクセス許可を必ず設定します。そうしないと、パスワード ライトバックは正しく機能しません。</font>**

  ![][032]

  これらの権限を設定すると、パスワード管理が、フォレストに含まれるユーザー アカウントからではなく、各フォレストのMA サービス アカウントから可能になります。 これらの権限を割り当てないと、ライトバックが正常に構成されているように思われる場合でも、クラウドからオンプレミスのパスワードを管理しようとするとエラーが発生します。 ここでは、[ **Active Directory ユーザーとコンピューター** ] 管理スナップインを使用した権限の設定方法の手順を詳しく説明します。

> [!NOTE]
> ディレクトリ内のすべてのオブジェクトにこれらの権限をレプリケートするには、最大 1 時間かかることがあります。
>
>

#### <a name="to-set-up-the-right-permissions-for-writeback-to-occur"></a>ライトバックを行うために適切な権限を設定するには
1. 適切なドメインの管理権限を持つアカウントで [ **Active Directory ユーザーとコンピューター** ] を開きます。
2. **[表示メニュー]** オプションで、**[高度な機能]** がオンになっていることを確認します。
3. 左側のパネルで、ドメインのルートを表すオブジェクトを右クリックします。
4. [ **セキュリティ** ] タブをクリックします。
5. [ **詳細設定**] をクリックします。

   ![][024]
6. **[アクセス権限]** タブで **[追加]** をクリックします。

   ![][025]
7. アクセス許可するアカウント (フォレストの同期をセットアップ中に指定したものと同じアカウント) を選択します。
8. 上部のドロップダウンで、[ **下位ユーザー オブジェクト**] を選択します。
9. 表示される **[アクセス許可エントリ]** ダイアログ ボックスで、`lockoutTime` の **[パスワードのリセット]**、**[パスワードの変更]**、および **[書き込みのアクセス許可]** のチェック ボックスをオンにし、`pwdLastSet` の **[書き込みのアクセス許可]** のチェック ボックスをオンにします。

   ![][026]
   ![][027]
   ![][028]
10. 開いているすべてのダイアログ ボックスで **[適用] または [OK]** をクリックします。

### <a name="step-5-reset-your-ad-password-as-a-user"></a>手順 5: ユーザーとして AD パスワードをリセットする
これでパスワード ライトバックが有効になったので、その動作をテストできます。テストするには、アカウントがクラウド テナントと同期されているユーザーのパスワードをリセットします。

#### <a name="to-verify-password-writeback-is-working-properly"></a>パスワード ライトバックの正常な動作を確認するには
1. [http://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com) に移動するか、組織の ID ログイン画面に移動し、[ **アカウントにアクセスできない場合** ] リンクをクリックします。

   ![][029]
2. 新しいページが表示され、パスワードをリセットするユーザー ID を求められます。 テスト ユーザー ID を入力し、パスワード リセット フローを続行します。
3. パスワードをリセットすると、次のような画面が表示されます。 これは、オンプレミスのディレクトリまたはクラウド ディレクトリ、あるいは両方のディレクトリでパスワードが正常にリセットされたことを意味します。

   ![][030]
4. 操作が正常に完了したか、またはエラーが診断されたかを確認するには、**ディレクトリ同期コンピューター**に進んで **[イベント ビューアー]** を開き、**[アプリケーション イベント ログ]** に移動して、ソース **PasswordResetService** でテスト ユーザーのイベント "**31002 - PasswordResetSuccess**" が表示されているかどうかを確認します。

   ![][031]



## <a name="next-steps"></a>次のステップ
Azure AD のパスワードのリセットに関するすべてのドキュメント ページへのリンクを以下に示します。

* **サインインに問題がありますか?** その場合は、[自分のパスワードを変更してリセットする方法をここから参照してください](active-directory-passwords-update-your-own-password.md#reset-your-password)にお進みください。
* [**しくみ**](active-directory-passwords-how-it-works.md) - サービスの 6 つの異なるコンポーネントとそれぞれの機能について説明します。
* [**カスタマイズ**](active-directory-passwords-customize.md) - 組織のニーズに合わせてサービスの外観と動作をカスタマイズする方法について説明します。
* [**ベスト プラクティス**](active-directory-passwords-best-practices.md) - 組織内でのパスワードの迅速なデプロイと効果的な管理方法について説明します。
* [**洞察を得る**](active-directory-passwords-get-insights.md) - 統合レポート機能について説明します。
* [**FAQ**](active-directory-passwords-faq.md) -よく寄せられる質問の回答を得ます。
* [**トラブルシューティング**](active-directory-passwords-troubleshoot.md) - サービスに関する問題を迅速にトラブルシューティングする方法について説明します。
* [**詳細情報**](active-directory-passwords-learn-more.md) - サービスの機能の技術的な詳細を掘り下げます。

[001]: ./media/active-directory-passwords-getting-started/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-getting-started/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-getting-started/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-getting-started/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-getting-started/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-getting-started/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-getting-started/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-getting-started/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-getting-started/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-getting-started/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-getting-started/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-getting-started/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-getting-started/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-getting-started/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-getting-started/015.jpg "Image_015.jpg"
[016]: ./media/active-directory-passwords-getting-started/016.jpg "Image_016.jpg"
[017]: ./media/active-directory-passwords-getting-started/017.jpg "Image_017.jpg"
[018]: ./media/active-directory-passwords-getting-started/018.jpg "Image_018.jpg"
[019]: ./media/active-directory-passwords-getting-started/019.jpg "Image_019.jpg"
[020]: ./media/active-directory-passwords-getting-started/020.jpg "Image_020.jpg"
[021]: ./media/active-directory-passwords-getting-started/021.jpg "Image_021.jpg"
[022]: ./media/active-directory-passwords-getting-started/022.jpg "Image_022.jpg"
[023]: ./media/active-directory-passwords-getting-started/023.jpg "Image_023.jpg"
[024]: ./media/active-directory-passwords-getting-started/024.jpg "Image_024.jpg"
[025]: ./media/active-directory-passwords-getting-started/025.jpg "Image_025.jpg"
[026]: ./media/active-directory-passwords-getting-started/026.jpg "Image_026.jpg"
[027]: ./media/active-directory-passwords-getting-started/027.jpg "Image_027.jpg"
[028]: ./media/active-directory-passwords-getting-started/028.jpg "Image_028.jpg"
[029]: ./media/active-directory-passwords-getting-started/029.jpg "Image_029.jpg"
[030]: ./media/active-directory-passwords-getting-started/030.jpg "Image_030.jpg"
[031]: ./media/active-directory-passwords-getting-started/031.jpg "Image_031.jpg"
[032]: ./media/active-directory-passwords-getting-started/032.jpg "Image_032.jpg"


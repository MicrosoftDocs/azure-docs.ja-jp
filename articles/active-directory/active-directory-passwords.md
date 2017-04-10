---
title: "Azure Active Directory のパスワード リセット | Microsoft Docs"
description: "パスワードのリセット、変更、パスワード管理のレポート、ローカルのオンプレミスの Active Directory へのライトバックなど、Azure AD のパスワード管理機能の説明。"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: be6164fc-bae1-49df-af76-761329ba70a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 4904a3e21a54a2c60ee8b35a46370a9759a029f3
ms.lasthandoff: 03/28/2017


---
# <a name="azure-active-directory-password-reset-for-it-administrators"></a>IT 管理者のための Azure Active Directory のパスワード リセット
> [!IMPORTANT]
> **サインインに問題がありますか?** その場合は、[自分のパスワードを変更してリセットする方法をここから参照してください](active-directory-passwords-update-your-own-password.md#reset-your-password)にお進みください。
>
>

セルフ サービスの実現は、コストを削減し労力を節約する手段として、世界中の IT 部門が長年目指してきた大きな目標です。  実際、市場はクラウドまたはオンプレミスから、オンプレミスのグループや、パスワード、ユーザー プロファイルを管理できるようにする製品であふれています。 Azure Active Directory (Azure AD) は、現在利用可能な機能の中でも、最も使いやすく強力なセルフ サービス機能を提供することで、他の製品とは一線を画しています。

**Azure AD パスワード管理** は、定義するセキュリティ ポリシーに準拠したままで、ユーザーが任意のデバイスで、時間と場所に関係なくパスワードを管理できる一連の機能です。

## <a name="admins-learn-about-how-to-get-started-with-azure-ad-password-reset"></a>管理者: Azure AD のパスワード リセットの概要について学習する
Azure AD のパスワード リセットを行いたい、またはそれについてさらに学習したい管理者は、以下のリンクの関心のある項目をお読みください。

| トピック |  |
| --- | --- |
| サポートされるシナリオ |[Azure AD のパスワード リセットでできること](#what-is-possible-with-azure-ad-password-reset) |
| 使用する理由 |[Azure AD のパスワード リセットを使用する理由](#why-use-azure-ad-password-reset) |
| 価格と可用性 |[価格と可用性](#pricing-and-availability) |
| パスワード リセットを有効にする |[ユーザーのパスワード リセットの有効化](#enable-password-reset-for-your-users) |
| 動作をカスタマイズする |[パスワードのリセット動作のカスタマイズ](#customize-password-reset-behavior) |
| ユーザーに展開する |[パスワードのリセットを使用できるようにするためのユーザーの構成](#configure-your-users-to-use-password-reset) |
| レポートを表示する |[パスワード リセット アクティビティの統合レポートでの表示](#view-password-reset-activity-with-integrated-reports) |
| ユーザーのパスワードのリセット |[ユーザーのパスワードの管理](#manage-your-users-passwords) |
| 自分の組織のパスワード ポリシーを設定する |[パスワード ポリシーの設定](#set-password-policies) |
| 問題をトラブルシューティングする |[問題をトラブルシューティングする](#troubleshoot-a-problem) |
| FAQ |[FAQ を読む](#read-a-faq) |
| 技術的な詳細 |[技術的な詳細の理解](#understand-the-technical-details) |
| 新たにリリースされた機能 |[最新のサービス更新内容](#recent-service-updates) |
| その他のドキュメントへのリンク |[パスワードのリセットに関するドキュメントへのリンク](#links-to-password-reset-documentation) |

### <a name="what-is-possible-with-azure-ad-password-reset"></a>Azure AD のパスワード リセットでできること
ここでは、Azure AD のパスワード管理機能を使用して実行できることをいくつか示します。

* **セルフ サービスによるパスワードの変更** では、エンド ユーザーまたは管理者は、管理者またはヘルプ デスクにサポートを依頼することなく、有効期限が切れた、または有効期限が切れていないパスワードを変更できます。
* **セルフ サービスによるパスワードのリセット** では、エンド ユーザーまたは管理者は、管理者またはヘルプ デスクにサポートを依頼することなく、パスワードを自動的にリセットできます。 セルフ サービスのパスワード リセットには、Azure AD Premium または Basic が必要です。 詳細については、「Azure Active Directory のエディション」をご覧ください。
* **管理者によるパスワードのリセット** では、管理者は、 [Microsoft Azure 管理ポータル](https://manage.windowsazure.com)内からエンド ユーザーまたは別の管理者のパスワードをリセットできます。
* **パスワード管理アクティビティ レポート** では、組織内で発生したパスワードのリセットおよび登録アクティビティの詳細が管理者に提供されます。
* **パスワード ライトバック** では、クラウドからオンプレミスのパスワードを管理できるので、フェデレーション ユーザーまたはパスワード同期済みユーザーは、またはこれらのユーザーに代わって、上記のシナリオをすべて実行できます。 パスワード ライトバックには Azure AD Premium が必要です。 詳細については、「Azure AD Premium の概要」を参照してください。

### <a name="why-use-azure-ad-password-reset"></a>Azure AD のパスワード リセットを使用する理由
ここでは、Azure AD のパスワード管理機能を使用すべき理由をいくつか示します。

* **コストを削減する** -サポート部門によるパスワードのリセットは、通常、組織の IT 支出の 20% を占めます。
* **ユーザー エクスペリエンスを向上させる** -ユーザーは、パスワードを忘れるたびにヘルプデスクに電話して、時間を費やしたくはありません。
* **ヘルプデスクの負荷を低減する** -パスワード管理は、ほとんどの組織のヘルプデスクにおいて、それひとつに最も負担のかかる仕事です。
* **モビリティを高める** -ユーザーが任意の場所からパスワードをリセットできます。

### <a name="pricing-and-availability"></a>価格と可用性
Azure AD のパスワード リセットは所持しているサブスクリプションに応じて、3 つのレベルで利用できます。

* **Azure AD Free** - クラウドのみの管理者が、自分のパスワードをリセットできます
* **Azure AD Basic または O365 のすべての有料サブスクリプション** - クラウドのみのユーザーおよびクラウドのみの管理者が、自分のパスワードをリセットできます
* **Azure AD Premium** - クラウドのみ、フェデレーション、パスワード同期ユーザーを含むすべてのユーザーまたは管理者が、自分のパスワードをリセットできます ( [パスワード ライトバックの有効化が](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)必要です)

Azure AD Premium または Basic 料金の詳細については、「 [Active Directory の料金詳細](https://azure.microsoft.com/pricing/details/active-directory/) 」ページを参照してください。

## <a name="enable-password-reset-for-your-users"></a>ユーザーのパスワード リセットの有効化
| トピック |  |
| --- | --- |
| クラウド ユーザーのパスワードをリセットする方法 |[クラウドの Azure Active Directory パスワードをユーザーがリセットできるようにする](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords) |
| オンプレミスのユーザーのパスワード リセットおよび変更を有効にする方法 |[オンプレミス Active Directory パスワードをユーザーがリセットまたは変更できるようにする](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) |
| パスワードのリセットを一連の特定のユーザーに適用する方法 |[パスワードのリセットを特定のユーザーに制限する](active-directory-passwords-customize.md#restrict-access-to-password-reset) |
| クラウドのパスワードのリセットをテストする方法 |[ユーザーが Azure AD パスワードをリセットする](active-directory-passwords-getting-started.md#step-3-reset-your-azure-ad-password-as-a-user) |
| オンプレミスのパスワードのリセットをテストする方法 |[ユーザーがオンプレミスの AD パスワード ポリシーをリセットする](active-directory-passwords-getting-started.md#step-5-reset-your-ad-password-as-a-user) |
| 後でパスワード リセットを無効化する方法 |[設定: パスワードのリセットが有効になっているユーザー](active-directory-passwords-customize.md#users-enabled-for-password-reset) |

## <a name="customize-password-reset-behavior"></a>パスワードのリセット動作のカスタマイズ
| トピック |  |
| --- | --- |
| サポートされる認証方法を変更する方法 |[設定: ユーザーが使用できる認証方法](active-directory-passwords-customize.md#authentication-methods-available-to-users) |
| 必要な認証方法の数を変更する方法 |[設定: 必要な認証方法の数](active-directory-passwords-customize.md#number-of-authentication-methods-required) |
| カスタムの秘密の質問を設定する方法 |[設定: カスタムの秘密の質問](active-directory-passwords-customize.md#custom-security-questions) |
| 既定のローカライズされた秘密の質問を設定する方法 |[設定: ナレッジ ベースの秘密の質問](active-directory-passwords-customize.md#knowledge-based-security-questions) |
| 必要な秘密の質問の数を変更する方法 |[設定: 登録またはリセット用の秘密の質問の数](active-directory-passwords-customize.md#number-of-questions-required-to-register) |
| ユーザーが管理者に連絡する方法をカスタマイズする方法 |[設定: "管理者に問い合わせてください" リンクをカスタマイズする](active-directory-passwords-customize.md#customize-the-contact-your-administrator-link) |
| パスワードをリセットせずに AD アカウントのロックを解除することをユーザーに許可する方法 |[設定: ユーザーがパスワードをリセットせずに AD アカウントのロックを解除できるようにする](active-directory-passwords-customize.md#allow-users-to-unlock-accounts-without-resetting-their-password) |
| ユーザーのパスワード リセット通知を有効にする方法 |[設定: パスワードがリセットされたときにユーザーに通知する](active-directory-passwords-customize.md#notify-users-and-admins-when-their-own-password-has-been-reset) |
| 管理者のパスワード リセット通知を有効にする方法 |[設定: 管理者が自分のパスワードをリセットしたときに他の管理者に通知する](active-directory-passwords-customize.md#notify-admins-when-other-admins-reset-their-own-passwords) |
| パスワード リセットの外観をカスタマイズする方法 |[設定: 会社名、ブランド、ロゴ ](active-directory-passwords-customize.md#password-management-look-and-feel) |

## <a name="configure-your-users-to-use-password-reset"></a>パスワードのリセットを使用できるようにするためのユーザーの構成
| トピック |  |
| --- | --- |
| アカウントがパスワード リセット用に構成されているか確認する方法 |[アカウントがパスワード リセット用に構成されている状態とは](active-directory-passwords-best-practices.md#what-makes-an-account-configured) |
| パスワード リセットをユーザーに構成する方法 |[ユーザーのパスワードのリセット認証データを設定する方法](active-directory-passwords-best-practices.md#ways-to-populate-authentication-data) |
| ユーザーのデータを手動でアップロードする方法 |[自分でパスワード リセット データをアップロードする](active-directory-passwords-best-practices.md#uploading-data-yourself) |
| PowerShell を使用してユーザーのデータを読み取ったり設定したりする方法 |[ユーザーのパスワード リセット データにアクセスする](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users) |
| オンプレミスでパスワード リセット データを同期する方法 |[パスワードのリセットで使用されるデータ](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) |
| ユーザーにパスワードのリセットを登録し実行してもらうための電子メール キャンペーンの使用方法 |[電子メール ベースのパスワード リセットの展開](active-directory-passwords-best-practices.md#email-based-rollout) |
| サインイン時にユーザーに登録を強制する方法 |[パスワード リセットの登録ベースのロールアウトの適用](active-directory-passwords-customize.md#require-users-to-register-when-signing-in) |
| 登録内容の定期的な再確認をユーザーに強制する方法 |[設定: ユーザーに認証データの再確認を強制するまでの日数](active-directory-passwords-customize.md#number-of-days-before-users-must-confirm-their-contact-data) |
| パスワード リセットをエンドユーザーに連絡する際のベスト プラクティス |[ユーザーが使用する独自のパスワード ポータルの作成](active-directory-passwords-best-practices.md#creating-your-own-password-portal) |

## <a name="view-password-reset-activity-with-integrated-reports"></a>パスワード リセット アクティビティの統合レポートでの表示
| トピック |  |
| --- | --- |
| パスワード リセット レポートを確認できる場所へのアクセス方法 |[パスワード管理レポートの概要](active-directory-passwords-get-insights.md#overview-of-password-management-reports) |
| 組織でのユーザーのパスワード リセットの使用の確認方法 |[パスワード リセット アクティビティの表示](active-directory-passwords-get-insights.md#how-to-view-password-management-reports) |
| 登録ユーザー数、および登録対象を確認できる場所へのアクセス方法 |[パスワード リセット登録アクティビティの表示](active-directory-passwords-get-insights.md#how-to-view-password-management-reports) |
| API を使用したパスワード リセット レポートの取得方法 |[レポート API にアクセスする Azure AD アプリケーションの作成](active-directory-reporting-api-getting-started.md) |
| API で入手できるパスワード リセット レポート情報の種類 |[レポート API で使用できるパスワード リセットと登録イベント](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-reports-and-events-preview#SsprActivityEvent) |

## <a name="manage-your-users-passwords"></a>ユーザーのパスワードの管理
| トピック |  |
| --- | --- |
| O365 管理ポータルからユーザーのパスワードをリセットする方法 |[Office 365 でのユーザーのパスワードのリセット](https://support.office.com/article/Reset-a-user-s-password-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C) |
| PowerShell を使用してユーザーのパスワードをリセットする方法 |[Set-MsolUserPassword を使用したユーザーのパスワードのリセット](https://msdn.microsoft.com/library/azure/dn194140.aspx) |

## <a name="set-password-policies"></a>パスワード ポリシーの設定
| トピック |  |
| --- | --- |
| Office 365 から組織のパスワードの有効期限ポリシーを設定する方法 |[パスワードの有効期限のポリシーの設定](https://support.office.com/article/Set-a-user-s-password-expiration-policy-0f54736f-eb22-414c-8273-498a0918678f) |
| PowerShell で特定のユーザーのパスワードが期限切れにならないように設定する方法 |[特定のユーザーのパスワードが期限切れにならないようにする PowerShell での設定](https://support.office.com/article/Set-an-individual-user-s-password-to-never-expire-f493e3af-e1d8-4668-9211-230c245a0466) |
| ユーザーのパスワードが期限切れにならないように設定されていることを PowerShell で確認する方法 |[PowerShell を使用した個々のユーザーのパスワード有効期限の状態の確認](https://support.office.com/article/Set-an-individual-user-s-password-to-never-expire-f493e3af-e1d8-4668-9211-230c245a0466#__toc378845827) |

## <a name="troubleshoot-a-problem"></a>問題をトラブルシューティングする
| トピック |  |
| --- | --- |
| ヘルプが必要である場合にサポートに提供する情報 |[ヘルプが必要な場合に含める情報](active-directory-passwords-troubleshoot.md#information-to-include-when-you-need-help) |
| パスワードのリセットの問題を解決する方法 |[パスワード リセット ポータルのトラブルシューティング](active-directory-passwords-troubleshoot.md#troubleshoot-the-password-reset-portal) |
| パスワード ライトバックの問題を解決する方法 |[パスワード ライトバックのトラブルシューティング](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| パスワード ライトバックの接続の問題を解決する方法 |[パスワード ライトバックの接続のトラブルシューティング](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) |
| パスワードのリセット構成の問題を解決する方法 |[Microsoft Azure 管理ポータルでのパスワード リセット構成のトラブルシューティング](active-directory-passwords-troubleshoot.md#troubleshoot-password-reset-configuration-in-the-azure-management-portal) |
| パスワード リセット レポートの問題を解決する方法 |[Microsoft Azure 管理ポータルでのパスワード管理レポートのトラブルシューティング](active-directory-passwords-troubleshoot.md#troubleshoot-password-management-reports-in-the-azure-management-portal) |
| パスワード リセット登録の問題を解決する方法 |[パスワード リセット登録ポータルのトラブルシューティング](active-directory-passwords-troubleshoot.md#troubleshoot-the-password-reset-registration-portal) |
| パスワード ライトバックのイベント ログのエラー コード |[パスワード ライトバックのイベント ログのエラー コード](active-directory-passwords-troubleshoot.md#password-writeback-event-log-error-codes) |

## <a name="read-a-faq"></a>FAQ を読む
| トピック |  |
| --- | --- |
| パスワード リセット登録に関する FAQ を参照する |[パスワード リセット登録の FAQ](active-directory-passwords-faq.md#password-reset-registration) |
| パスワード リセットに関する FAQ を参照する |[パスワード リセットの FAQ](active-directory-passwords-faq.md#password-reset) |
| パスワード リセット レポートに関する FAQ を参照する |[パスワード管理レポートの FAQ](active-directory-passwords-faq.md#password-management-reports) |
| パスワード ライトバックに関する FAQ を参照する |[パスワード ライトバックの FAQ](active-directory-passwords-faq.md#password-writeback) |

## <a name="understand-the-technical-details"></a>技術的な詳細の理解
| トピック |  |
| --- | --- |
| パスワード ライトバックについて学習する |[パスワード ライトバックの概要](active-directory-passwords-learn-more.md#password-writeback-overview) |
| パスワード ライトバックのしくみについて学習する |[パスワード ライトバックのしくみ](active-directory-passwords-learn-more.md#how-password-writeback-works) |
| パスワード ライトバックがサポートするシナリオを学習する |[パスワード ライトバックでサポートされているシナリオ](active-directory-passwords-learn-more.md#scenarios-supported-for-password-writeback) |
| パスワード ライトバックのセキュリティ保護のしくみについて学習する |[パスワード ライトバックのセキュリティ モデル](active-directory-passwords-learn-more.md#password-writeback-security-model) |
| パスワード リセット ポータルのしくみを学習する |[パスワード リセット ポータルのしくみ](active-directory-passwords-learn-more.md#how-does-the-password-reset-portal-work) |
| パスワード リセットで使用されるデータについて学習する |[パスワードのリセットで使用されるデータ](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) |

## <a name="recent-service-updates"></a>最新のサービス更新内容
#### <a name="enforce-password-reset-registration-at-sign-in-to-office-365-apps---november-2015"></a>Office 365 アプリへのサインイン時にパスワード リセット登録を実施する - 2015 年 11 月
* [強制登録](active-directory-passwords-customize.md#require-users-to-register-when-signing-in) 機能を有効にすることにより、職場または学校のアカウントを使用してサインインした任意の場所で登録を求めることができるようになりました。  これにより、多くの組織がパスワード リセットを使用開始する速度が大幅に上昇します。  この新しい機能により、大規模な組織がわずか 2 週間で利用開始したこともありました。

#### <a name="support-for-unlocking-on-premises-ad-accounts-without-resetting-a-password---november-2015"></a>パスワードをリセットすることなくオンプレミスの AD アカウントのロックを解除する機能をサポート - 2015 年 11 月
* (リセットせずに) ロックのみを解除できることは、今日のヘルプデスクにとっては大きな推進力となります。  実際、多くの組織で、アカウントのロック解除にパスワードのリセット予算を最大 70% 使用しています。  Azure AD のパスワード リセットでは、この需要に対応するために、ユーザーがオンプレミスの AD アカウントのロックを解除できる (パスワード リセットからは独立した) 機能を有効にできるようになりました。  これをオンにするには、「[設定: ユーザーがパスワードをリセットせずにオンプレミスの AD アカウントのロックを解除できるようにする](active-directory-passwords-customize.md#allow-users-to-unlock-accounts-without-resetting-their-password)」を参照してください。

#### <a name="usability-updates-to-registration-page---october-2015"></a>登録ページでの使いやすさの更新 - 2015 年 10 月
* ユーザーは既に登録されたデータがある場合、電子メールを送信したり電話をかけたりしなくても、[問題なし] をクリックするだけでデータを更新できるようになりました。

#### <a name="improved-reliability-of-password-writeback---september-2015"></a>パスワード ライトバックの信頼性が向上 - 2015 年 9 月
* Azure AD Connect の 9 月リリースの時点で、パスワード ライトバック エージェントは接続と、より堅牢な追加のフェールオーバー機能とを積極的に再試行するようになります。

#### <a name="api-for-retrieving-password-reset-reporting-data---august-2015"></a>パスワード リセット レポート データを取得するための API - 2015 年 8 月
* パスワード リセット レポートの背後にあるデータを [Azure AD レポートおよびイベント API](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent)で直接取得できるようになりました。

#### <a name="support-for-azure-ad-password-reset-during-cloud-domain-join---august-2015"></a>クラウド ドメインへの参加中に Azure AD のパスワード リセットをサポート - 2015 年 8 月
* クラウド ユーザーは、クラウド ドメインへの参加のためのオンボード作業中に Windows 10 サインイン画面から自身のパスワードをリセットできるようになりました。  この機能は Windows 10 のサインイン画面ではまだ公開されていません。

#### <a name="enforce-password-reset-registration-at-sign-in-to-azure-and-federated-apps---july-2015"></a>Azure およびフェデレーション アプリへのサインイン時にパスワード リセット登録を強制 - 2015 年 7 月
* myapps.microsoft.com へのサインイン時の登録の実施に加えて、Microsoft Azure 管理ポータルおよび任意のフェデレーション シングル サインオン アプリケーションへのサインイン時の登録の実施もサポートするようになりました。

#### <a name="security-question-localization-support---may-2015"></a>セキュリティに関する質問のローカライズ サポート - 2015 年 5 月
* パスワード リセットに対してセキュリティの質問を構成する場合に、完全な O365 言語セットでローカライズされた定義済みのセキュリティの質問を選択できるようになりました。

#### <a name="account-unlock-support-during-password-reset---june-2015"></a>パスワード リセット中のアカウントのロック解除をサポート - 2015 年 6 月
* パスワード ライトバックを使用している場合、アカウントがロックされている状態でパスワードをリセットすると、Active Directory アカウントが自動的にロック解除されます。

#### <a name="branded-self-service-password-reset-sspr-registration---april-2015"></a>セルフサービスのパスワード リセット (SSPR) 登録のブランド化 - 2015 年 4 月
* パスワードのリセットの登録ページは、会社のロゴでブランド化されるようになりました。

#### <a name="security-questions---march-2015"></a>セキュリティの質問 - 2015 年 3 月
* セキュリティの質問を、一般に公開しました。

#### <a name="account-unlock---march-2015"></a>アカウントのロックの解除 - 2015 年 3 月
* パスワードのリセットが発生した場合に、ユーザーがロックを解除できるようになりました。

## <a name="coming-soon"></a>近日対応予定
現在作業中の優れた機能の一部を次に示します。

**サインイン時に登録済みデータを更新するようユーザーに喚起する機能** - 開発中

* 現在、myapps.microsoft.com へのアクセス時に登録済みデータを更新するようユーザーに喚起する機能をサポートしていますが、すべてのサインインでこれを行う機能を開発中です。

## <a name="next-steps"></a>次のステップ
Azure AD のパスワード リセットに関するすべてのドキュメント ページへのリンクを以下に示します。

* **サインインに問題がありますか?** その場合は、[自分のパスワードを変更してリセットする方法をここから参照してください](active-directory-passwords-update-your-own-password.md#reset-your-password)にお進みください。
* [**しくみ**](active-directory-passwords-how-it-works.md) - サービスの 6 つの異なるコンポーネントとそれぞれの機能について説明します。
* [**概要**](active-directory-passwords-getting-started.md) - ユーザーによるクラウドまたはオンプレミスのパスワードのリセットと変更を許可する方法について説明します。
* [**カスタマイズ**](active-directory-passwords-customize.md) - 組織のニーズに合わせてサービスの外観と動作をカスタマイズする方法について説明します。
* [**ベスト プラクティス**](active-directory-passwords-best-practices.md) - 組織内でのパスワードの迅速なデプロイと効果的な管理方法について説明します。
* [**洞察を得る**](active-directory-passwords-get-insights.md) - 統合レポート機能について説明します。
* [**FAQ**](active-directory-passwords-faq.md) -よく寄せられる質問の回答を得ます。
* [**トラブルシューティング**](active-directory-passwords-troubleshoot.md) - サービスに関する問題を迅速にトラブルシューティングする方法について説明します。
* [**詳細情報**](active-directory-passwords-learn-more.md) - サービスの機能の技術的な詳細を掘り下げます。


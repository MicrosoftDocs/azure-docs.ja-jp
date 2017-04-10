---
title: "Azure Active Directory B2B コラボレーションの FAQ | Microsoft Docs"
description: "Azure Active Directory B2B コラボレーションに関してよく寄せられる質問"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 03/18/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 995f185f99c80809a5c4c2925b8d594b1d5568ff
ms.lasthandoff: 03/21/2017


---

# <a name="azure-active-directory-b2b-collaboration-frequently-asked-questions-faq"></a>Azure Active Directory B2B コラボレーションに関してよく寄せられる質問 (FAQ)

よく寄せられる質問は、新しい関心を反映するために定期的に更新されます。

### <a name="is-this-functionality-available-in-the-azure-classic-portal"></a>この機能は Azure クラシック ポータルで使用できますか。
この Azure AD B2B コラボレーションのパブリック プレビューの更新における新機能は、[Azure Portal](https://portal.azure.com) と新しいアクセス パネルを通じてのみ使用できます。 試してみる

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>B2B コラボレーションのユーザーは、SharePoint Online と OneDrive にアクセスできますか。
B2B コラボレーションのゲスト ユーザーはディレクトリに入ります。 それらのユーザーを、SharePoint Online と OneDrive サイトへのアクセス権を持つグループに追加できます。また、SharePoint Online のユーザー選択ウィンドウで直接指定することもできます。 これらはゲスト ユーザーであるため、SharePoint Online サイトは外部共有が有効になっている必要があります。

### <a name="is-the-csv-upload-mechanism-still-supported"></a>CSV のアップロード メカニズムは、まだサポートされていますか。
はい。 付属の PowerShell のサンプルを参照してください。

### <a name="how-can-i-customize-my-invitation-emails"></a>招待の電子メールは、どのようにカスタマイズできますか。
B2B 招待 API を使用して、招待元プロセスのほぼすべてをカスタマイズすることができます。

### <a name="can-the-invited-external-user-leave-the-organization-to-which-he-was-invited"></a>招待された外部ユーザーは、招待元の組織から離脱することができますか。
現在、このパブリック プレビューの更新ではできません。

### <a name="now-that-multi-factor-authentication-mfa-is-available-for-guest-users-can-they-also-reset-their-mfa-method"></a>ゲスト ユーザーに対して多要素認証 (MFA) を利用できるようになりましたが、MFA 方法をリセットすることもできますか。
はい、通常のユーザーと同じようにすることができます。

### <a name="which-organization-is-responsible-for-mfa-licenses"></a>MFA ライセンスは、どちらの組織が責任を負いますか。
招待する側の組織が、MFA を担当し、実行します。 そのため、招待する側の組織に、MFA を行う B2B ユーザーのための十分なライセンスを確保する責任があります。

### <a name="what-if-my-partner-org-already-has-mfa-set-up-can-we-trust-their-mfa-and-not-use-our-mfa"></a>パートナー組織が既に MFA をセットアップしている場合はどうすればよいですか。 相手の MFA を信頼し、自社の MFA は使用しなくてもよいですか。
このパブリック プレビューの更新では対応していませんが、今後のリリースではサポートされる予定です。 それがリリースされたら、自社 (招待する側の組織) の MFA から除外する特定のパートナーを選択できるようになります。

### <a name="how-can-i-achieve-delayed-invitations"></a>招待の遅延は、どのようにして行うことができますか。
一部の組織は、B2B コラボレーション ユーザーを追加し、プロビジョニングを必要とするアプリケーションにプロビジョニングしてから、招待を送信しようとします。 そのような場合は、B2B コラボレーションの招待 API を使用して、オンボード ワークフローをカスタマイズすることができます。

### <a name="can-guest-users-and-contacts-co-exist"></a>ゲスト ユーザーと連絡先は共存できますか。
組織によっては、外部コラボレーターがグローバル アドレス一覧に表示されたり、電子メールの作成時にメール アドレス候補として表示されたりするように、外部コラボレーターを表す連絡先を追加してある場合があります。 そこへこれらの同じコラボレーターを B2B コラボレーション ユーザーとしてディレクトリに追加した場合にどうなるかを疑問に思うかもしれません。 将来のリリースでは、B2B コラボレーション ユーザーと連絡先オブジェクトが会社のディレクトリ内で共存できるようになります。 発表があるまで、しばらくお待ちください。

### <a name="can-i-make-my-guest-users-limited-admins"></a>ゲスト ユーザーを制限付き管理者にすることができますか。
そして、 組織でそうする必要がある場合、その方法については、[ロールへのゲスト ユーザーの追加](active-directory-users-assign-role-azure-portal.md)に関するページを参照してください。

### <a name="does-azure-ad-b2b-collaboration-support-permitting-b2b-users-to-access-the-azure-portal"></a>Azure AD B2B コラボレーションでは、B2B ユーザーによる Azure Portal へのアクセスの許可がサポートされていますか。
B2B コラボレーション ユーザーは、制限付き管理者ロールまたは全体管理者ロールに割り当てられない限り、Azure Portal にアクセスする必要がありません。 割り当てられた場合は、ポータルにアクセスすることができます。 これらのロールではないゲスト ユーザーがポータルにアクセスした場合、エクスペリエンスの特定の部分にアクセスできることがあります。これは、前のセクションで説明したように、ゲスト ユーザー ロールにディレクトリの特定のアクセス許可があるためです。

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>ゲスト ユーザーに対して Azure Portal へのアクセスをブロックできますか。
はい。 ただし、このポリシーを構成する際に、誤ってメンバーと管理者のアクセスをブロックしないように注意してください。
ゲスト ユーザーによる [Azure Portal](https://portal.azure.com) へのアクセスは、Windows Azure Service Management API の条件付きアクセス ポリシーを通じて、次の 3 つの手順でブロックできます。
1. **[すべてのユーザー]** グループを、メンバーのみが含まれるように変更します。![](media/active-directory-b2b-faq/modify-all-users-group.png)
2. ゲスト ユーザーを含む動的グループを作成します。![](media/active-directory-b2b-faq/group-with-guest-users.png)
3. 次のビデオで示されているように、ゲスト ユーザーによるポータルへのアクセスをブロックする条件付きアクセス ポリシーをセットアップします。

  >[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player]

### <a name="what-is-the-timeline-by-which-azure-ad-b2b-collaboration-will-start-support-for-mfa-and-consumer-email-accounts"></a>Azure AD B2B コラボレーションが MFA とコンシューマー電子メール アカウントのサポートを開始する予定はどうなっていますか。
MFA とコンシューマー電子メール アカウントの両方が、このパブリック プレビューの更新でサポートされるようになりました。

### <a name="what-is-the-ga-timeline-for-azure-ad-b2b"></a>Azure AD B2B の GA の予定はどうなっていますか。
いつ実施するかは、現在の機能セットに対するお客様からのフィードバックによって決まります。

### <a name="is-there-a-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Azure AD B2B コラボレーション ユーザーのパスワード リセットをサポートする計画はありますか。
はい。これらの両方が、B2B コラボレーション (ゲスト) ユーザーに対してサポートされています。

### <a name="is-it-also-enabled-for-users-in-a-viral-tenant"></a>バイラル テナントのユーザーに対しても有効ですか。
現時点では連携しません。

### <a name="does-microsoft-crm-provide-online-support-to-azure-ad-b2b-collaboration"></a>Microsoft CRM は、Azure AD B2B コラボレーションをオンラインでサポートしていますか。
CRM は、一般公開後、Azure AD B2B コラボレーションをサポートします。

### <a name="are-b2b-collaboration-guest-users-visible-in-sharepoint-onlineonedrive-people-picker"></a>B2B コラボレーションのゲスト ユーザーは、SharePoint Online/OneDrive のユーザー選択ウィンドウに表示されますか。
 
はい。 ただし、SharePoint Online のユーザー選択ウィンドウでの既存のゲスト ユーザーの検索は、従来の動作と一致させるために、既定ではオフになっています。 これは、テナントとサイト コレクション レベルで 'ShowPeoplePickerSuggestionsForGuestUsers' 設定を使用することで有効にできます。 この設定は、Set-SPOTenant コマンドレットと Set-SPOSite コマンドレットを使用して設定できます。これにより、メンバーは、ディレクトリ内のすべての既存のゲスト ユーザーを検索することができます。 テナントのスコープの変更は、既にプロビジョニングされている SharePoint Online サイトには影響しません。

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>新しく作成した B2B コラボレーション ユーザー用の初期パスワードの有効期間はどうなっていますか。
Azure AD には、文字、パスワードの強度、およびアカウント ロックアウトに関する要件の固定セットがあり、これはすべての Azure AD クラウド ユーザー アカウントにも同様に適用されます。 クラウド ユーザー アカウントは、他の ID プロバイダー (Microsoft アカウント、Facebook、ADFS など) や他のクラウド テナント (B2B コラボレーションの場合) とフェデレーションされないアカウントです。 フェデレーション アカウントの場合、パスワード ポリシーは、オンプレミス テナントのポリシーとユーザーの Microsoft アカウント設定に依存します。

### <a name="applications-want-to-differentiate-their-experience-between-a-tenant-user-and-a-guest-user-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-right-model-for-this"></a>アプリケーションのエクスペリエンスをテナント ユーザーとゲスト ユーザーで区別したいと思っています。 これを行うための標準的なガイダンスはありますか。 このモデルに適した ID プロバイダー要求は存在しますか。
 
「[Azure Active Directory B2B コラボレーション ユーザーのプロパティ](active-directory-b2b-user-properties.md)」で説明しているように、ゲスト ユーザーは、認証用に任意の ID プロバイダーを使用することができます。 このため、判断を行うための適切なプロパティは UserType です。 現在、UserType 要求はトークンには含まれていません。 アプリケーションは、Graph API を使用してディレクトリでユーザーのクエリを実行することで、ユーザーの UserType を取得する必要があります。

### <a name="where-can-find-a-b2b-collaboration-community-to-share-solutions-and-submit-ideas"></a>ソリューションを共有し、アイデアを提供するための B2B コラボレーション コミュニティにはどこからアクセスできますか。

B2B コラボレーションを向上させる方法に関するフィードバックは絶えず受け付けています。 マイクロソフトはお客様に、ディスカッションや、ユーザー シナリオ、ベスト プラクティス、また Azure AD B2B コラボレーションに関するご意見の共有への参加を [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b) で招待しています。
 
また、[B2B Collaboration Ideas](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas) サイトでも、ご意見の送信や、今後の機能への投票を行うことができます。


### <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-admin-add-users.md)
* [インフォメーション ワーカーが B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-iw-add-users.md)
* [B2B コラボレーションの招待メールの要素](active-directory-b2b-invitation-email.md)
* [B2B コラボレーションの招待の利用](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B コラボレーションのライセンス](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B コラボレーションのトラブルシューティング](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B コラボレーションの API とカスタマイズ](active-directory-b2b-api.md)
* [B2B コラボレーション ユーザーの多要素認証](active-directory-b2b-mfa-instructions.md)
* [招待を使用せずに B2B コラボレーション ユーザーを追加する](active-directory-b2b-add-user-without-invite.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)


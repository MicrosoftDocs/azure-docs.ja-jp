---
title: 'Azure Active Directory B2C: FAQ | Microsoft Docs'
description: "Azure Active Directory B2C についてよく寄せられる質問"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: ed33c2ca-76d0-442a-abb1-8b7b7bb92d6a
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 351149296a6d7dfa801b295ec21fc04215c7b051
ms.openlocfilehash: ac2730935d206ddf9079395384d46a43fdd740cb


---
# <a name="azure-active-directory-b2c-faqs"></a>Azure Active Directory B2C: FAQ
このページには、Azure Active Directory (Azure AD) B2C に関してよく寄せられる質問への回答が記載されています。 常に最新情報をチェックしてください。

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>既存の従業員ベースの Azure AD テナントで Azure AD B2C 機能を使用できますか。
現在のところ、既存の Azure AD テナントで Azure AD B2C 機能を有効にすることはできません。 Azure AD B2C 機能を使用するための別個のテナントを作成し、お客様を管理することをお勧めします。

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Azure AD B2C を使用してソーシャル ログイン (Facebook および Google+) を Office 365 に提供することはできますか。
Azure AD B2C は Microsoft Office 365 と共に使用することはできません。 一般に、SaaS アプリ (Office 365、Salesforce、Workday など) に認証を提供するために使用することはできません。 Azure AD B2C は、コンシューマー向けの Web およびモバイル アプリケーションの ID とアクセスの管理を提供するだけであり、従業員またはパートナーのシナリオには適用されません。

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Azure AD B2C のローカル アカウントとは何ですか。 それらは、Azure AD の職場または学校アカウントとはどのような点が異なるのですか。
Azure AD テナントでは、テナント内のすべてのユーザー (既存の Microsoft アカウントを持つユーザーを除く) は、`<xyz>@<tenant domain>` という形式の電子メール アドレスを使用してサインインします。`<tenant domain>` はテナント内のいずれかの有効なドメイン、または初期 `<...>.onmicrosoft.com` ドメインです。 この種類のアカウントは、職場または学校アカウントです。

Azure AD B2C テナントでは、ユーザーは大部分のアプリに任意の電子メール アドレス (joe@comcast.net,、bob@gmail.com,、sarah@contoso.com,、jim@live.com) など) を使用してサインインします。 この種類のアカウントはローカル アカウントです。 現在、ローカル アカウントとして任意のユーザー名 (単なるプレーン文字列) もサポートしています (joe、bob、sarah、jim など)。 Azure AD B2C サービスでは、これらの&2; 種類のローカル アカウントのいずれかを選択できます。

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>現在サポートされているソーシャル ID プロバイダーはどれですか。 将来サポートする予定のプロバイダーはどれですか。
現在、Facebook、Google+、LinkedIn、および Amazon をサポートしています。 お客様のご要望に基づいて、他の人気のあるソーシャル ID プロバイダーのサポートを追加する予定です。

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>さまざまなソーシャル ID プロバイダーからお客様に関する情報をさらに収集するためにスコープを構成できますか。
いいえ。ただし、この機能は検討中です。 サポートされている一連のソーシャル ID プロバイダーに使用されている、既定のスコープは次のとおりです。

* Facebook: 電子メール
* Google +: 電子メール
* Microsoft アカウント: openid 電子メール プロファイル
* Amazon: プロファイル
* LinkedIn: r_emailaddress、r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>使用しているアプリケーションを Azure AD B2C で機能させるには、Azure で実行する必要がありますか。
いいえ。アプリケーションは任意の場所でホストできます (クラウドまたはオンプレミス)。 Azure AD B2C とやり取りするために必要なのは、パブリックにアクセス可能なエンドポイントで HTTP 要求を送受信する機能のみです。

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>複数の Azure AD B2C テナントがあります。 Azure Portal でこれらのテナントを管理するにはどうすればよいですか。
Azure Portal には、Azure AD B2C テナントごとに独自の B2C 機能ブレードがあります。 Azure Portal で特定のテナントの B2C 機能ブレードに移動する方法については、「 [Azure Active Directory B2C: アプリケーションを登録する](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) 」を参照してください。 ほとんどのブラウザーでは、Azure Portal で Azure AD B2C ディレクトリを切り替えると、B2C 機能ブレードは開いたままにはなりません。

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Azure AD B2C によって送信される検証電子メールをカスタマイズするにはどうすればいいですか (コンテンツおよび "From:" フィールド)。
検証電子メールの内容をカスタマイズするには、 [会社のブランド化機能](../active-directory/active-directory-add-company-branding.md) を使用します。 具体的には、電子メールの次の&2; つの要素をカスタマイズできます。

* **バナー ロゴ**: 右下に表示されます。
* **背景色**: 上部に表示されます。
  
    ![カスタマイズされた検証電子メールのスクリーンショット](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

電子メールの署名には、最初に B2C テナントを作成したときに指定した B2C テナントの名前が含まれます。 名前は次の手順を使用して変更できます。

* サブスクリプション管理者として [Azure クラシック ポータル](https://manage.windowsazure.com/) にサインインします。
* B2C テナントに移動します。
* **[構成]** タブをクリックします。
* **[ディレクトリのプロパティ]** セクションの **[名前]** フィールドを変更します。
* ページの下部にある **[保存]** をクリックします。

現在、電子メールの送信元フィールドを変更する方法はありません。 この機能や検証電子メールの本文全体をカスタマイズすることに興味がある場合は、 [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/15334335-fully-customizable-verification-emails)でその機能に投票してください。

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>既存のユーザー名、パスワード、およびプロファイルを自分のデータベースから Azure AD B2C に移行するにはどのようにすればいいですか。
Azure AD Graph API を使用して、移行ツールを作成できます。 詳細については、 [Graph API サンプル](active-directory-b2c-devquickstarts-graph-dotnet.md) に関するページを参照してください。 将来、さまざまな移行オプションとツールをすぐに使用できる状態で提供する予定です。

### <a name="what-password-policy-is-used-for-local-accounts-in-azure-ad-b2c"></a>Azure AD B2C のローカル アカウントに使用されるパスワード ポリシーはどのようなものですか。
Azure AD B2C のローカル アカウントのパスワード ポリシーは Azure AD のポリシーに基づいています。 Azure AD B2C のサインアップ、サインアップまたはサインイン、パスワード リセットの各ポリシーでは、"強力な" パスワード強度を使用しており、いずれのパスワードにも有効期限がありません。 詳細については、 [Azure AD のパスワード ポリシー](https://msdn.microsoft.com/library/azure/jj943764.aspx) に関するページを参照してください。

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Azure AD Connect を使用して、自分のオンプレミス Active Directory に保存されているお客様の ID を Azure AD B2C に移行できますか。
いいえ。Azure AD Connect は Azure AD B2C と連携するようには設計されていません。 将来、さまざまな移行オプションとツールをすぐに使用できる状態で提供する予定です。

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>アプリで Azure AD B2C ページを iFrame 内で開くことはできますか。
いいえ。セキュリティ上の理由から、`login.microsftonline.com` ドメインで提供されている Azure AD B2C ページを iFrame 内で開くことはできません。 すべてのコンシューマー エクスペリエンスについて、常に Azure AD B2C にリダイレクトする必要があります。

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Azure AD B2C は Microsoft Dynamics のような CRM システムと連携しますか。
現時点では連携しません。 これらのシステムとの統合を検討中です。

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Azure AD B2C はオンプレミスの SharePoint 2016 以前と連携しますか。
現時点では連携しません。 Azure AD B2C では、ポータルおよび E コマース アプリケーションがオンプレミスの SharePoint のニーズに基づいて構築した SAML 1.1 トークンはサポートされません。 Azure AD B2C は、SharePoint 外部パートナー共有のシナリオには適していません。この記事ではなく、[Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx) に関する記事を参照してください。

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>外部 ID を管理するために Azure AD B2C または B2B を使用する必要がありますか。
外部 ID のシナリオに適した機能を使用する方法の詳細については、 [外部 ID](../active-directory/active-directory-b2b-compare-external-identities.md) に関するこの記事を参照してください。

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Azure AD B2C ではどのようなレポート機能と監査機能が提供されますか。 それは Azure AD Premium の機能と同じですか。
いいえ。Azure AD B2C では Azure AD Premium と同じレポート セットはサポートされていません。 Azure AD B2C では、基本レポート API および監査 API が間もなくリリースされる予定です。

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Azure AD B2C で提供されているページの UI をローカライズできますか。 どの言語がサポートされていますか。
現在、Azure AD B2C は英語のみに最適化されています。 可能な限り早急にローカライズ機能を展開する予定です。

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>Azure AD B2C によって提供されているサインアップおよびサインイン ページで独自の URL を使用できますか。 たとえば、URL を login.microsoftonline.com から login.contoso.com に変更できますか。
現時点では連携しません。 この機能は検討中です。 また、Azure クラシック ポータルからテナントの **[ドメイン]** タブでドメインを検証しても、変更は行われません。

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Azure AD B2C テナントを削除する方法はありますか。
Azure AD B2C テナントを削除するには、次の手順に従います。

* この手順に従って、Azure Portal で [B2C 機能ブレードに移動](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) します。
* **[アプリケーション]**、**[ID プロバイダー]**、**[すべてのポリシー]** の各ブレードで、そのすべてのエントリを削除します。
* 次に、サブスクリプション管理者として [Azure クラシック ポータル](https://manage.windowsazure.com/)にサインインします  (これは、Azure へのサインアップに使用したものと同じ職場/学校アカウント、または同じ Microsoft アカウントです)。
* 左側の Active Directory 拡張機能に移動し、B2C テナントをクリックします。
* **[Users]** タブをクリックします。
* 各ユーザーを順に選択します。ただし、現在サインインに使用しているユーザー (つまりサブスクリプション管理者) は除きます。 ページ下部の **[削除]** をクリックし、確認プロンプトに **[はい]** をクリックします。
* **[アプリケーション]** タブをクリックします。
* **[表示]** ドロップダウン フィールドで **[自分の会社が所有するアプリケーション]** を選択し、チェック マークをクリックします。
* 以下に示すように **b2c-extensions-app** というアプリケーションが表示されます。 ページ下部の **[削除]** をクリックし、確認プロンプトに **[はい]** をクリックします。
* Active Directory 拡張機能にもう一度移動し、B2C テナントをクリックします。
* ページの下部にある **[削除]** をクリックします。 画面に表示される指示に従って、処理を完了します。

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Enterprise Mobility Suite の一部として Azure AD B2C を取得できますか。
いいえ。Azure AD B2C は従量課金の Azure サービスであり、Enterprise Mobility Suite には含まれていません。

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Azure AD B2C に関する問題を報告するにはどのようにすればいいですか。
[Azure Active Directory B2C のサポート要求の提出](active-directory-b2c-support.md)に関するページを参照してください。

## <a name="more-information"></a>詳細情報
現在の [サービスの制限事項および制約事項](active-directory-b2c-limitations.md)を確認することもできます。




<!--HONumber=Jan17_HO4-->



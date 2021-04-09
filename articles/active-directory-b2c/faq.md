---
title: Azure Active Directory B2C についてよく寄せられる質問 (FAQ)
description: Azure Active Directory B2C についてよく寄せられる質問への回答。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d84c5fff41a7f00405c29ab4b4921ed2e28bdfde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580098"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C:よく寄せられる質問 (FAQ)

このページには、Azure Active Directory B2C (Azure AD B2C) に関してよく寄せられる質問への回答が記載されています。 常に最新情報をチェックしてください。

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>Azure Portal で Azure AD B2C の拡張機能にアクセスできないのはなぜですか。

Azure AD 拡張機能が動作しない一般的な理由は 2 つあります。 Azure AD B2C では、ディレクトリのユーザー ロールがグローバル管理者である必要があります。 アクセス権限が必要だと思われる場合は、管理者に問い合わせてください。 グローバル管理者権限がある場合、ユーザーが Azure Active Directory ディレクトリではなく Azure AD B2C ディレクトリにいることを確認してください。 [Azure AD B2C テナントの作成](tutorial-create-tenant.md)に関する手順を確認してください。

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>既存の従業員ベースの Azure AD テナントで Azure AD B2C 機能を使用できますか。

Azure AD と Azure AD B2C は、別個の製品であるため、同じテナントで共存させることはできません。 Azure AD テナントは、組織を表します。 Azure AD B2C テナントは、証明書利用者アプリケーションで使用される ID のコレクションを表します。 **[Azure AD B2C] > [ID プロバイダー]** で **[新しい OpenID Connect プロバイダー]** を追加するか、またはカスタム ポリシーを使用して、Azure AD B2C で Azure AD にフェデレーションして、組織の従業員の認証を許可することができます。

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-microsoft-365"></a>Azure AD B2C を使用してソーシャル ログイン (Facebook および Google+) を Microsoft 365 に提供することはできますか?

Azure AD B2C は、Microsoft 365 のユーザーの認証に使用できません。 Azure AD は、SaaS アプリへの従業員のアクセスを管理するための Microsoft のソリューションであり、その目的で設計された機能 (ライセンス付与や条件付きアクセスなど) を備えています。 Azure AD B2C には、Web およびモバイル アプリケーションを構築するための ID およびアクセスの管理プラットフォームが用意されています。 Azure AD テナントにフェデレーションするように Azure AD B2C を構成すると、Azure AD テナントでは、Azure AD B2C に依存するアプリケーションへの従業員のアクセスが管理されます。

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Azure AD B2C のローカル アカウントとは何ですか。 それらは、Azure AD の職場または学校アカウントとはどのような点が異なるのですか。

Azure AD テナントでは、テナントに属するユーザーは、`<xyz>@<tenant domain>` の形式の電子メール アドレスを使用してサインインします。 `<tenant domain>` は、テナントの確認済みドメインの 1 つ、または初期の `<...>.onmicrosoft.com` ドメインです。 この種類のアカウントは、職場または学校アカウントです。

Azure AD B2C テナントでは、ユーザーは大部分のアプリに任意のメール アドレス (joe@comcast.net、bob@gmail.com、sarah@contoso.com、jim@live.com など) を使用してサインインします。 この種類のアカウントはローカル アカウントです。 ローカル アカウントとして任意のユーザー名もサポートしています (joe、bob、sarah、jim など)。 Azure Portal で Azure AD B2C の ID プロバイダーを構成する場合、この 2 種類のローカル アカウントのいずれかを選択できます。 ご利用の Azure AD B2C テナントで、 **[ID プロバイダー]** 、 **[ローカル アカウント]** 、 **[ユーザー名]** の順に選択します。

アプリケーションのユーザー アカウントは、サインアップ ユーザー フローによって、サインアップもしくはサインイン ユーザー フローによって、Microsoft Graph API によって、または Azure portal を通じて作成できます。

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>現在サポートされているソーシャル ID プロバイダーはどれですか。 将来サポートする予定のプロバイダーはどれですか。

現在のところ、Amazon、Facebook、GitHub (プレビュー)、Google、LinkedIn、Microsoft アカウント (MSA)、QQ (プレビュー)、Twitter、WeChat (プレビュー)、Weibo (プレビュー) など、いくつかのソーシャル ID プロバイダーをサポートしています。 お客様からご要望があれば、他の人気のあるソーシャル ID プロバイダーへのサポート追加を検討します。

Azure AD B2C も[カスタム ポリシー](custom-policy-overview.md)をサポートしています。 カスタム ポリシーを使用すると、[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) または SAML をサポートする任意の ID プロバイダーに対して独自のポリシーを作成できます。 [カスタム ポリシー スターター パック](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)を使ってカスタム ポリシーの作成をお試しください。

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>さまざまなソーシャル ID プロバイダーからお客様に関する情報をさらに収集するためにスコープを構成できますか。

いいえ。 サポートされている一連のソーシャル ID プロバイダーに使用されている、既定のスコープは次のとおりです。

* Facebook: 電子メール
* Google +: 電子メール
* Microsoft アカウント: openid 電子メール プロファイル
* Amazon: プロファイル
* LinkedIn: r_emailaddress、r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>使用しているアプリケーションを Azure AD B2C で機能させるには、Azure で実行する必要がありますか。

いいえ。アプリケーションは任意の場所でホストできます (クラウドまたはオンプレミス)。 Azure AD B2C とやり取りするために必要なのは、パブリックにアクセス可能なエンドポイントで HTTP 要求を送受信する機能のみです。

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>複数の Azure AD B2C テナントがあります。 Azure Portal でこれらのテナントを管理するにはどうすればよいですか。

Azure ポータルの左側にあるメニューの [Azure AD B2C] を開く前に、管理するディレクトリに切り替える必要があります。 Azure ポータルの右上にある自分の ID をクリックしてディレクトリを切り替えた後、表示されるドロップダウンからディレクトリを選択します。

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Azure AD B2C によって送信される検証電子メールをカスタマイズするにはどうすればいいですか (コンテンツおよび "From:" フィールド)。

検証電子メールの内容をカスタマイズするには、 [会社のブランド化機能](../active-directory/fundamentals/customize-branding.md) を使用します。 具体的には、電子メールの次の 2 つの要素をカスタマイズできます。

* **バナー ロゴ**:右下に表示されます。
* **背景色**:上部に表示されます。

    ![カスタマイズされた確認メールのスクリーンショット](./media/faq/company-branded-verification-email.png)

電子メールの署名には、最初に Azure AD B2C テナントを作成したときに指定した Azure AD B2C テナントの名前が含まれます。 名前は次の手順を使用して変更できます。

1. [Azure Portal](https://portal.azure.com/) にグローバル管理者としてサインインします。
1. **[Azure Active Directory]** ブレードを開きます。
1. **[プロパティ]** タブをクリックします。
1. **[名前]** フィールドを変更します。
1. ページの上部にある **[保存]** をクリックします。

現在、電子メールの "From:" フィールドを変更することはできません。

> [!TIP]
> Azure AD B2C の[カスタム ポリシー](custom-policy-overview.md)では、ユーザーに送信される電子メール Azure AD B2C (電子メールの "From:" フィールドを含む) をカスタマイズできます。 カスタム メール確認では、[Mailjet](custom-email-mailjet.md)、[SendGrid](custom-email-sendgrid.md)、[SparkPost](https://sparkpost.com) といったサードパーティの電子メール プロバイダーを使用する必要があります。

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>既存のユーザー名、パスワード、およびプロファイルを自分のデータベースから Azure AD B2C に移行するにはどのようにすればいいですか。

Microsoft Graph API を使用して、移行ツールを作成できます。 詳細については[ユーザーの移行ガイド](user-migration.md)を参照してください。

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>Azure AD B2C のローカル アカウントに使用されるパスワード ユーザー フローはどのようなものですか。

Azure AD B2C のローカル アカウントのパスワード ユーザー フローは Azure AD のポリシーに基づいています。 Azure AD B2C のサインアップ、サインアップまたはサインイン、パスワード リセットの各ユーザー フローでは、"強力な" パスワード強度を使用しており、いずれのパスワードにも有効期限がありません。 詳細については、「[Azure Active Directory のパスワード ポリシーと制限](../active-directory/authentication/concept-sspr-policy.md)」を参照してください。

アカウントのロックアウトとパスワードについては、「[Azure Active Directory B2C: 脅威の管理](threat-management.md)」を参照してください。

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Azure AD Connect を使用して、自分のオンプレミス Active Directory に保存されているお客様の ID を Azure AD B2C に移行できますか。

いいえ。Azure AD Connect は Azure AD B2C と連携するようには設計されていません。 ユーザーの移行には、[Microsoft Graph API](microsoft-graph-operations.md) を使用することを検討してください。 詳細については[ユーザーの移行ガイド](user-migration.md)を参照してください。

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>アプリで Azure AD B2C ページを iFrame 内で開くことはできますか。

この機能はパブリック プレビュー段階にあります。 詳細については、「[埋め込みサインイン エクスペリエンス](https://docs.microsoft.com/azure/active-directory-b2c/embedded-login)」を参照してください。

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Azure AD B2C は Microsoft Dynamics のような CRM システムと連携しますか。

Microsoft Dynamics 365 ポータルとの統合を使用できます。 [Azure AD B2C を使用して認証するための Dynamics 365 ポータルの構成](/dynamics365/customer-engagement/portals/azure-ad-b2c)に関する記事を参照してください。

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Azure AD B2C はオンプレミスの SharePoint 2016 以前と連携しますか。

Azure AD B2C は、SharePoint 外部パートナー共有のシナリオには適していません。この記事ではなく、[Azure AD B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/) に関する記事を参照してください。

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>外部 ID を管理するために Azure AD B2C または B2B を使用する必要がありますか。

外部 ID のシナリオに適した機能を適用する方法の詳細については、 [Azure AD での B2B コラボレーションおよび B2C の比較](../active-directory/external-identities/compare-with-b2c.md)に関する記事を参照してください。

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Azure AD B2C ではどのようなレポート機能と監査機能が提供されますか。 それは Azure AD Premium の機能と同じですか。

いいえ。Azure AD B2C では Azure AD Premium と同じレポート セットはサポートされていません。 ただし、多くの共通点があります。

* **サインイン レポート** は、各サインインの記録を、詳細は除外して提供します。
* **監査レポート** には、管理アクティビティとアプリケーション アクティビティの両方が含まれます。
* **使用状況レポート** には、ユーザーの数、ログインの回数、MFA のボリュームが含まれます。

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Azure AD B2C で提供されているページの UI をローカライズできますか。 どの言語がサポートされていますか。

「[言語のカスタマイズ](language-customization.md)」をご覧ください。 Microsoft では、36 言語の翻訳を提供しおり、お客様は、ニーズに合わせて任意の文字列をオーバーライドすることができます。

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-contosob2clogincom-to-logincontosocom"></a>Azure AD B2C によって提供されているサインアップおよびサインイン ページで独自の URL を使用できますか。 たとえば、URL を contoso.b2clogin.com から login.contoso.com に変更できますか。

この機能は、パブリック プレビューで使用できます。 詳細については、[Azure AD B2C カスタムドメインに関するページ](https://docs.microsoft.com/azure/active-directory-b2c/custom-domain?pivots=b2c-user-flow)を参照してください。

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Azure AD B2C テナントを削除する方法はありますか。

Azure AD B2C テナントを削除するには、次の手順に従います。

新しい統一的な **アプリの登録** エクスペリエンスと、従来の **アプリケーション (レガシ)** エクスペリエンスのいずれかを利用できます。 [この新しいエクスペリエンスの詳細を参照してください](./app-registrations-training-guide.md)。

#### <a name="app-registrations"></a>[アプリの登録](#tab/app-reg-ga/)

1. *サブスクリプション管理者* として [Azure portal](https://portal.azure.com/) にサインインします。 Azure へのサインアップに使用したものと同じ職場または学校アカウント、または同じ Microsoft アカウントを使用します。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. Azure AD B2C テナント内のすべての **ユーザー フロー (ポリシー)** を削除します。
1. Azure AD B2C テナントの **ID プロバイダー** をすべて削除します。
1. **[アプリの登録]** 、 **[すべてのアプリケーション]** タブの順に選択します。
1. 登録したすべてのアプリケーションを削除します。
1. **b2c-extensions-app** を削除します。
1. **[管理]** にある **[ユーザー]** を選択します。
1. 各ユーザーを順に選択します (ただし、現在サインインに使用している *サブスクリプション管理者* ユーザーは除きます)。 ページ下部の **[削除]** を選択し、確認を求められたら **[はい]** を選択します。
1. 左側のメニューで、 **[Azure Active Directory]** を選択します。
1. **[管理]** の下で、 **[プロパティ]** を選択します。
1. **[Azure リソースのアクセス管理]** の下で **[はい]** を選択した後、 **[保存]** を選択します。
1. Azure portal からサインアウトした後に、もう一度サインインして、ご自分のアクセス権を更新します。
1. 左側のメニューで、 **[Azure Active Directory]** を選択します。
1. **[概要]** ページで **[テナントの削除]** を選択します。 画面の指示に従って、プロセスを完了します。

#### <a name="applications-legacy"></a>[アプリケーション (レガシ)](#tab/applications-legacy/)

1. *サブスクリプション管理者* として [Azure portal](https://portal.azure.com/) にサインインします。 Azure へのサインアップに使用したものと同じ職場または学校アカウント、または同じ Microsoft アカウントを使用します。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. ご使用の Azure AD B2C テナントの **ユーザー フロー (ポリシー)** をすべて削除します。
1. ご使用の Azure AD B2C テナントに登録したすべての **アプリケーション (レガシ)** を削除します。
1. 左側のメニューで、 **[Azure Active Directory]** を選択します。
1. **[管理]** にある **[ユーザー]** を選択します。
1. 各ユーザーを順に選択します (ただし、現在サインインに使用している *サブスクリプション管理者* ユーザーは除きます)。 ページ下部の **[削除]** を選択し、確認を求められたら **[はい]** を選択します。
1. **[管理]** の **[アプリの登録]** を選択します。
1. **[アプリケーションをすべて表示]** を選択します。
1. **b2c-extensions-app** という名前のアプリケーションを選択し、 **[削除]** を選択した後、確認を求められたら **[はい]** を選択します。
1. **[管理]** の下で **[ユーザー設定]** を選択します。
1. 表示される場合は、 **[LinkedIn アカウント接続]** の下で **[いいえ]** を選択した後、 **[保存]** を選択します。
1. **[管理]** の下で、 **[プロパティ]** を選択します。
1. **[Azure リソースのアクセス管理]** の下で **[はい]** を選択した後、 **[保存]** を選択します。
1. Azure portal からサインアウトした後に、もう一度サインインして、ご自分のアクセス権を更新します。
1. 左側のメニューで、 **[Azure Active Directory]** を選択します。
1. **[概要]** ページで **[ディレクトリの削除]** を選択します。 画面の指示に従って、プロセスを完了します。

* * *

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Enterprise Mobility Suite の一部として Azure AD B2C を取得できますか。

いいえ。Azure AD B2C は従量課金制の Azure サービスであり、Enterprise Mobility Suite には含まれていません。

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Azure AD B2C に関する問題を報告するにはどのようにすればいいですか。

[Azure Active Directory B2C のサポート要求の提出](support-options.md)に関するページを参照してください。

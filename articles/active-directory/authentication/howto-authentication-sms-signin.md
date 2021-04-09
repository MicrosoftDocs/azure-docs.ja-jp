---
title: Azure Active Directory 用の SMS ベースのユーザー サインイン
description: SMS を使用して Azure Active Directory にサインインするようにユーザーを構成して有効にする方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rateller
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e9052502eba71f025bb6724278b7001173c5217
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491619"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory"></a>Azure Active Directory を使用して SMS ベース認証用にユーザーを構成して有効にする 

アプリケーションやサービスへのサインインを簡素化してセキュリティで保護するために、Azure Active Directory (Azure AD) には複数の認証オプションが用意されています。 SMS ベースの認証を使用すると、サインイン時にユーザーは自分のユーザー名とパスワードを指定する必要がなく、覚えておく必要すらありません。 ユーザーは、ID 管理者によってアカウントが作成された後、サインイン プロンプトで電話番号を入力できます。 ユーザーは、テキスト メッセージを介して、サインインを完了するために指定できる認証コードを受け取ります。 この認証方法を使用すると、特に第一線で働くユーザーにとっては、アプリケーションやサービスへのアクセスが簡単になります。

この記事では、Azure AD で選択されたユーザーまたはグループに対して SMS ベースの認証を有効にする方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事を完了するには、以下のリソースと特権が必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* お使いのサブスクリプションに関連付けられている Azure Active Directory テナント。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* SMS ベース認証を有効にするには、Azure AD テナントでの "*全体管理者*" 特権が必要です。
* テキスト メッセージ認証方法ポリシーで有効になっている各ユーザーは、その方法を使用しない場合でも、ライセンスを取得している必要があります。 有効な各ユーザーは、次の Azure AD、EMS、または Microsoft 365 ライセンスのいずれかを保持している必要があります。
    * [Microsoft 365 (M365) F1 または F3][m365-firstline-workers-licensing]
    * [Enterprise Mobility + Security (EMS) E3 または E5][ems-licensing] または [Microsoft 365 (M365) E3 または E5][m365-licensing]
    * [Office 365 F3][o365-f3]

## <a name="limitations"></a>制限事項

SMS ベースの認証には、次の制限事項が適用されます。

* 現在、SMS ベース認証には Azure AD Multi-Factor Authentication との互換性はありません。
* Teams を除き、SMS ベース認証には、ネイティブな Office アプリケーションとの互換性はありません。
* B2B アカウントの場合、SMS ベース認証はお勧めしません。
* フェデレーション ユーザーは、ホーム テナントでは認証されません。 クラウドでのみ認証されます。

## <a name="enable-the-sms-based-authentication-method"></a>SMS ベースの認証方法を有効にする

組織で SMS ベース認証を有効にして使用するには、次の 3 つの主要な手順があります。

* 認証方法ポリシーを有効にする。
* SMS ベースの認証方法を使用できるユーザーまたはグループを選択する。
* 各ユーザー アカウントに電話番号を割り当てる。
    * この電話番号は、Azure portal (この記事で示されている) と *[マイ スタッフ]* または *[マイ アカウント]* で割り当てることができます。

まず、Azure AD テナントの SMS ベース認証を有効にしましょう。

1. [Azure portal][azure-portal] に "*全体管理者*" としてサインインします。
1. **Azure Active Directory** を検索して選択します。
1. Azure Active Directory ウィンドウの左側にあるナビゲーション メニューから、 **[セキュリティ] > [認証方法] > [認証方法ポリシー]** を選択します。

    [![Azure portal で [認証方法ポリシー] ウィンドウを参照して選択します。](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png)](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. 利用可能な認証方法の一覧から、 **[テキスト メッセージ]** を選択します。
1. **[有効にする]** を *[はい]* に設定します。

    ![認証方法ポリシー ウィンドウでテキスト認証を有効にする](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    "*すべてのユーザー*" または "*選択されたユーザー*" およびグループに対して SMS ベース認証を有効にするように選ぶことができます。 次のセクションで、テスト ユーザーの SMS ベース認証を有効にします。

## <a name="assign-the-authentication-method-to-users-and-groups"></a>認証方法をユーザーとグループに割り当てる

Azure AD テナントで SMS ベース認証が有効になっている状態で、この認証方法の使用が許可されるようにいくつかのユーザーまたはグループを選択します。

1. テキスト メッセージ認証ポリシー ウィンドウで、 **[ターゲット]** を "*選択されたユーザー*" に設定します。
1. **[ユーザーまたはグループの追加]** を選択して、"*Contoso ユーザー*" や "*Contoso SMS ユーザー*" などのテスト ユーザーまたはグループを選択します。

    [![Azure portal で SMS ベースの認証を有効にするユーザーまたはグループを選択します。](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png)](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. ユーザーまたはグループを選択したら、更新された認証方法ポリシーの **[選択]** 、 **[保存]**  を順に選びます。

テキスト メッセージ認証方法ポリシーで有効になっている各ユーザーは、その方法を使用しない場合でも、ライセンスを取得している必要があります。 特に、大規模なユーザー グループに対して機能を有効にする場合は、認証方法ポリシーで有効にするユーザーに適切なライセンスがあることを確認してください。

## <a name="set-a-phone-number-for-user-accounts"></a>ユーザー アカウントに電話番号を設定する

これで、SMS ベース認証に対してユーザーが有効になりましたが、サインインするには、事前にユーザーの電話番号が Azure AD 上でユーザー プロファイルに関連付けられている必要があります。 ユーザーは " *[マイ アカウント]* " で [この電話番号を自分で設定](../user-help/sms-sign-in-explainer.md)することができます。または、管理者が Azure portal を使用して電話番号を割り当ててもかまいません。 電話番号は、"*全体管理者*"、"*authentication admins (認証管理者)* "、または "*privileged authentication admins (特権認証管理者)* " によって設定できます。

電話番号が SMS サインイン用に設定されている場合は、[Azure AD Multi-Factor Authentication][tutorial-azure-mfa] および[セルフサービス パスワード リセット][tutorial-sspr]にその電話番号を使用することもできます。

1. **Azure Active Directory** を検索して選択します。
1. Azure Active Directory ウィンドウの左側にあるナビゲーション メニューから、 **[ユーザー]** を選択します。
1. 前のセクションで SMS ベース認証を有効にしたユーザーを選択して ("*Contoso ユーザー*" など)、 **[認証方法]** を選択します。
1. **[認証方法の追加]** を選択し、 *[方法の選択]* ドロップダウン メニューで **[電話番号]** を選択します。

    *+1 xxxxxxxxx* など、国番号を含むユーザーの電話番号を入力します。 Azure portal では、電話番号が正しい形式であることが検証されます。

    次に、 *[電話の種類]* ドロップダウン メニューから、必要に応じて *[携帯電話]* 、 *[連絡用携帯電話]* 、または *[その他]* を選択します。

    :::image type="content" source="media/howto-authentication-sms-signin/set-user-phone-number.png" alt-text="Azure portal 上でユーザーの電話番号を SMS ベース認証に使用するように設定する":::

    電話番号はテナント内で一意にする必要があります。 複数のユーザーに同じ電話番号を使用しようとすると、エラー メッセージが表示されます。

1. 電話番号をユーザーのアカウントに適用するには、 **[追加]** を選択します。

正常にプロビジョニングされると、"*SMS サインインが有効になっている*" のチェック マークが表示されます。

## <a name="test-sms-based-sign-in"></a>SMS ベースのサインインをテストする

SMS ベースのサインインが有効になったユーザー アカウントをテストするには、次の手順を実行します。

1. [https://www.office.com][office] に新しい InPrivate または Incognito Web ブラウザー ウィンドウを開きます
1. 右上隅にある **[サインイン]** を選択します。
1. サインイン プロンプトで、前のセクションでユーザーに関連付けられている電話番号を入力し、 **[次へ]** を選択します。

    ![テスト ユーザーのサインイン プロンプトで電話番号を入力する](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. 指定された電話番号に、テキスト メッセージが送信されます。 サインイン プロセスを完了するには、テキスト メッセージに記載されている 6 桁のコードをサインイン プロンプトに入力します。

    ![テキスト メッセージを使用してユーザーの電話番号に送信された確認コードを入力する](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. これでユーザーは、ユーザー名やパスワードを入力しなくてもサインインできるようになりました。

## <a name="troubleshoot-sms-based-sign-in"></a>SMS ベースのサインインをトラブルシューティングする

次のシナリオとトラブルシューティングの手順は、SMS ベースのサインインの有効化と使用に関する問題が発生した場合に、利用できます。

### <a name="phone-number-already-set-for-a-user-account"></a>ユーザー アカウントに既に電話番号が設定されている

ユーザーが Azure AD Multi-Factor Authentication またはセルフサービス パスワード リセット (SSPR) に既に登録されている場合は、そのアカウントに関連付けられている電話番号が既に設定されています。 SMS ベースのサインインに使用できるように、この電話番号が自動的に利用可能になるわけではありません。

アカウントに電話番号が既に設定されているユーザーには、 **[マイ プロファイル]** ページ上に " *[Enable for SMS sign-in]\(SMS サインインを有効化する\)* " ボタンが表示されます。 このボタンを選択すると、アカウントが SMS ベースのサインインでの使用と、以前の Azure AD Multi-Factor Authentication または SSPR 登録に対して有効化されます。

エンドユーザー エクスペリエンスの詳細については、[電話番号での SMS サインイン ユーザー エクスペリエンス](../user-help/sms-sign-in-explainer.md)に関する記事を参照してください。

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>ユーザーのアカウントに電話番号を設定しようとするとエラーが発生する

Azure portal でユーザーのアカウントに電話番号を設定しようとするとエラーが発生する場合は、次のトラブルシューティング手順を確認してください。

1. SMS ベースのサインインに対してご自身が有効になっていることを確認します。
1. *テキスト メッセージ* 認証方法ポリシーで該当のユーザー アカウントが有効になっていることを確認します。
1. Azure portal 上で検証されるように、電話番号を適切な形式で設定していることを確認します ( *+1 4251234567* など)。
1. 電話番号がテナント内の他の場所に使用されていないことを確認します。
1. アカウントに音声番号が設定されていないことを確認します。 音声番号が設定されている場合は、削除してからもう一度電話番号の設定を試してください。

## <a name="next-steps"></a>次のステップ

Microsoft Authenticator アプリや FIDO2 セキュリティ キーなど、パスワードを使用せずに Azure AD にサインインするその他の方法については、「[Azure AD のパスワードレス認証オプション][concepts-passwordless]」を参照してください。

また、Microsoft Graph REST API ベータ版を使用して、SMS ベースのサインインを[有効][rest-enable]または[無効][rest-disable]にすることができます。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md
[rest-enable]: /graph/api/phoneauthenticationmethod-enablesmssignin?view=graph-rest-beta&tabs=http
[rest-disable]: /graph/api/phoneauthenticationmethod-disablesmssignin?view=graph-rest-beta&tabs=http

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
[o365-f1]: https://www.microsoft.com/microsoft-365/business/office-365-f1?market=af
[o365-f3]: https://www.microsoft.com/microsoft-365/business/office-365-f3?activetab=pivot%3aoverviewtab

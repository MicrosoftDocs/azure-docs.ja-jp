---
title: 'チュートリアル: Azure Active Directory と Absorb LMS の統合 | Microsoft Docs'
description: Azure Active Directory と Absorb LMS の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 268943463002ddd1dbdbf67df9587758f81f537f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646522"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>チュートリアル: Azure Active Directory と Absorb LMS の統合

このチュートリアルでは、Absorb LMS と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Absorb LMS を統合すると、次のことができます。

* Absorb LMS にアクセスできるユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントを使用して Absorb LMS に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

Absorb LMS と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Absorb LMS でのシングル サインオンが有効なサブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Absorb LMS では、**IDP** によって開始される SSO がサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-absorb-lms-from-the-gallery"></a>ギャラリーから Absorb LMS を追加する

Azure AD への Absorb LMS の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Absorb LMS を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Absorb LMS**」と入力します。
1. 結果のパネルから **[Absorb LMS]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-absorb-lms"></a>Absorb LMS の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Absorb LMS に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Absorb LMS の関連ユーザーとの間にリンク関係を確立する必要があります。

Absorb LMS に対する Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Absorb LMS の SSO の構成](#configure-absorb-lms-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Absorb LMS テスト ユーザーの作成](#create-absorb-lms-test-user)** - Absorb LMS で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Absorb LMS** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** ボタンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    **Absorb 5 - UI** を使用している場合は、次の構成を使用します。

    a. **[識別子]** ボックスに、`https://<SUBDOMAIN>.myabsorb.com/account/saml` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<SUBDOMAIN>.myabsorb.com/account/saml` の形式で URL を入力します。

    **Absorb 5 - New Learner Experience** を使用している場合は、次の構成を使用します。

    a. **[識別子]** ボックスに、`https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml` の形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[Absorb LMS クライアント サポート チーム](https://support.absorblms.com/hc/)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. 次のスクリーンショットは、既定の属性の一覧を示しています。ここで、**nameidentifier** は **user.userprincipalname** にマップされています。

    ![image](common/edit-attribute.png)

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

7. **[Absorb LMS のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Absorb LMS へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Absorb LMS]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-absorb-lms-sso"></a>Absorb LMS の SSO の構成

1. 新しい Web ブラウザー ウィンドウで、Absorb LMS 企業サイトに管理者としてサインインします。

2. 右上にある **[アカウント]** ボタンを選択します。

    ![[アカウント] ボタン](./media/absorblms-tutorial/account.png)

3. [アカウント] ウィンドウで、**[ポータルの設定]** を選択します。

    ![[ポータルの設定] リンク](./media/absorblms-tutorial/portal.png)

4. **[Manage SSO Settings]\(SSO の設定の管理\)** タブを選択します。

    ![[ユーザー] タブ](./media/absorblms-tutorial/sso.png)

5. **[Manage Single Sign-On Settings]\(シングル サインオンの設定の管理\)** ページで、次のようにします。

    ![[シングル サインオンの構成] ページ](./media/absorblms-tutorial/settings.png)

    a. **[Name]\(名前\)** テキスト ボックスに、"Azure AD Marketplace SSO" のような名前を入力します。

    b. **[Method]\(方法\)** として **[SAML]** を選択します。

    c. メモ帳で、Azure Portal からダウンロードした証明書を開きます。 **-----BEGIN CERTIFICATE-----** タグと **-----END CERTIFICATE-----** タグを削除します。 次に **[キー]** ボックスに残りのコンテンツを貼り付けます。

    d. **[モード]** ボックスで、**[Identity Provider Initiated]\(ID プロバイダー開始\)** を選択します。

    e. **[Id Property]\(ID プロパティ\)** ボックスで、Azure AD でユーザー ID として構成した属性を選択します。 たとえば、Azure AD で *nameidentifier* が選択されている場合、**[ユーザー名]** を選択します。

    f. **[Signature Type]\(署名の種類\)** として **[Sha256]** を選択します。

    g. **[ログイン URL]** ボックスに、Azure Portal のアプリケーションの **[プロパティ]** ページにある **ユーザーのアクセス URL** を貼り付けます。

    h. **[ログアウト URL]** に、Azure Portal の **[サインオンの構成]** ウィンドウからコピーした **[サインアウト URL]** 値を貼り付けます。

    i. **[Automatically Redirect]\(自動的にリダイレクトする\)** を **[On]\(オン\)** にします。

6. **[保存]** を選択します。

    ![[Only Allow SSO Login]\(SSO ログインのみを許可する\) の切り替え](./media/absorblms-tutorial/save.png)

### <a name="create-absorb-lms-test-user"></a>Absorb LMS のテスト ユーザーの作成

Azure AD ユーザーが Absorb LMS にサインインするには、そのユーザーを Absorb LMS で設定する必要があります。 Absorb LMS の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. Absorb LMS 企業サイトに管理者としてサインインします。

2. **[ユーザー]** ウィンドウで、**[ユーザー]** を選択します。

    ![[ユーザー] リンク](./media/absorblms-tutorial/users.png)

3. **[ユーザー]** タブを選択します。

    ![[新規追加] ドロップダウン リスト](./media/absorblms-tutorial/add.png)

4. **[ユーザーの追加]** ページで、次の手順に従います。

    ![[ユーザーの追加] ページ](./media/absorblms-tutorial/user.png)

    a. **[名]** ボックスに、ユーザーの名 (たとえば、**Britta**) を入力します。

    b. **[姓]** ボックスに、ユーザーの姓 (たとえば、**Simon**) を入力します。

    c. **[ユーザー名]** ボックスに、ユーザーの氏名 (たとえば、**Britta Simon**) を入力します。

    d. **[パスワード]** ボックスに、ユーザーのパスワードを入力します。

    e. **[パスワードの確認]** ボックスに、パスワードを再度入力します。

    f. **[アクティブ]** を **[アクティブ]** に切り替えます。

5. **[保存]** を選択します。

    ![[Only Allow SSO Login]\(SSO ログインのみを許可する\) の切り替え](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > 既定では、ユーザー プロビジョニングは SSO では有効になりません。 顧客がこの機能を有効にすることを望む場合は、[こちら](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning)のドキュメントで説明されているように設定する必要があります。 また、ユーザー プロビジョニングは ACS URL が `https://company.myabsorb.com/api/rest/v2/authentication/saml` の **Absorb 5 - New Learner Experience** でのみ使用できることに注意してください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Absorb LMS に自動的にサインインされます。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Absorb LMS] タイルをクリックすると、SSO を設定した Absorb LMS に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Absorb LMS を構成したら、ご自分の組織の機密データの流出と侵入をリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
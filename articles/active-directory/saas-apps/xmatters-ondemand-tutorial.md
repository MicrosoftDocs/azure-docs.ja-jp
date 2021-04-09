---
title: チュートリアル:Azure Active Directory と xMatters OnDemand の統合 | Microsoft Docs
description: Azure Active Directory と xMatters OnDemand の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2020
ms.author: jeedes
ms.openlocfilehash: 762bd1c536df0ca307149ba7c201f08f5bdfded5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735618"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>チュートリアル:Azure Active Directory と xMatters OnDemand の統合

このチュートリアルでは、xMatters OnDemand と Azure Active Directory (Azure AD) を統合する方法について説明します。
xMatters OnDemand と Azure AD の統合には、次の利点があります。

* xMatters OnDemand にアクセスするユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントを使用して xMattersxMatters OnDemand に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

xMatters OnDemand と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* xMatters OnDemand でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* xMatters OnDemand では、**IDP** によって開始される SSO がサポートされます

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>ギャラリーからの xMatters OnDemand の追加

Azure AD への xMatters OnDemand の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に xMatters OnDemand を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**xMatters OnDemand**」と入力します。
1. 結果パネルで **[xMatters OnDemand]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-xmatters-ondemand"></a>xMatters OnDemand の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、xMatters OnDemand に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと xMatters OnDemand の関連ユーザーとの間にリンク関係を確立する必要があります。

xMatters OnDemand に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
    2. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[xMatters OnDemand の SSO の構成](#configure-xmatters-ondemand-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[xMatters OnDemand のテスト ユーザーの作成](#create-xmatters-ondemand-test-user)** - xMatters OnDemand で Britta Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
3. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **xMatters OnDemand** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[識別子]** テキスト ボックスに、次のいずれかのパターンを使用して URL を入力します。

    | 識別子 |
    | ---------- |
    | `https://<companyname>.au1.xmatters.com.au/` |
    | `https://<companyname>.cs1.xmatters.com/` |
    | `https://<companyname>.xmatters.com/` |
    | `https://www.xmatters.com` |
    | `https://<companyname>.xmatters.com.au/` |

    b. **[応答 URL]** ボックスに、次のいずれかのパターンを使用して URL を入力します。

    | [応答 URL] |
    | ---------- |
    |  `https://<companyname>.au1.xmatters.com.au` |
    | `https://<companyname>.xmatters.com/sp/<instancename>` |
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>` |
    | `https://<companyname>.au1.xmatters.com.au/<instancename>` |

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[xMatters OnDemand クライアント サポート チーム](https://www.xmatters.com/company/contact-us/)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

    > [!IMPORTANT]
    > [xMatters OnDemand サポート チーム](https://www.xmatters.com/company/contact-us/)に証明書を転送する必要があります。 シングル サインオンの構成を確定するには、その前に、xMatters サポート チームによって証明書がアップロードされる必要があります。

6. **[Set up xMatters OnDemand]\(xMatters OnDemand の設定\)** セクションで、要件どおりの適切な URL をコピーします。

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

このセクションでは、B.Simon に xMatters OnDemand へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーション一覧で **[xMatters OnDemand]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。


## <a name="configure-xmatters-ondemand-sso"></a>xMatters OnDemand の SSO の構成

1. 別の Web ブラウザーのウィンドウで、XMatters OnDemand の企業サイトに管理者としてサインインします。

2. **[Admin]\(管理\)** をクリックし、 **[Company Details]\(会社の詳細\)** をクリックします。

    ![[Admin]\(管理\) ページ](./media/xmatters-ondemand-tutorial/admin.png "管理者")

3. **[SAML 構成]** ページで、次の手順を実行します。

    ![[SAML Configuration]\(SAML 構成\) セクション](./media/xmatters-ondemand-tutorial/saml-configuration.png "[SAML 構成]")

    a. **[Enable SAML]** を選択します。

    b. **[Identity Provider ID]\(ID プロバイダーの ID\)** ボックスに、Azure portal からコピーした **Azure AD 識別子** の値を貼り付けます。

    c. **[シングル サインオン URL]** テキストボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    d. **[Logout URL Redirect]\(ログアウト URL リダイレクト\)** ボックスに、Azure portal からコピーした **ログアウト URL** を貼り付けます。

    e. **[Choose File]\(ファイルの選択\)** をクリックして、Azure portal からダウンロードした **証明書 (Base64)** をアップロードします。 

    f. [Company Details] ページで、上部にある [**Save Changes**] をクリックします。

    ![会社の詳細情報](./media/xmatters-ondemand-tutorial/save-button.png "会社の詳細情報")

### <a name="create-xmatters-ondemand-test-user"></a>xMatters OnDemand のテスト ユーザーの作成

1. **XMatters OnDemand** テナントにサインインします。

2. **[Users]\(ユーザー\) アイコン** >  **[Users]\(ユーザー\)** に移動し、 **[Add Users]\(ユーザーの追加\)** をクリックします。

    ![ユーザー](./media/xmatters-ondemand-tutorial/add-user.png "ユーザー")

3. **[Add Users]\(ユーザーの追加\)** セクションで必要なフィールドに入力し、 **[Add User]\(ユーザーの追加\)** ボタンをクリックします。

    ![[ユーザーの追加]](./media/xmatters-ondemand-tutorial/add-user-2.png "ユーザーを追加する")



### <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した xMatters OnDemand に自動的にサインインされます

* Microsoft マイ アプリを使用することができます。 マイ アプリで [xMatters OnDemand] タイルをクリックすると、SSO を設定した xMatters OnDemand に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

xMatters OnDemand を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
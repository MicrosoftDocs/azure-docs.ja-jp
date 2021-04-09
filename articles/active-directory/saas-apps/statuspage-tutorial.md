---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と StatusPage の統合 | Microsoft Docs
description: Azure Active Directory と StatusPage の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: cc3ce56ecd17d627001f4925355c055afdc09d22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98729636"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-statuspage"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と StatusPage の統合

このチュートリアルでは、StatusPage と Azure Active Directory (Azure AD) を統合する方法について説明します。
StatusPage と Azure AD の統合には、次の利点があります。

* StatusPage にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで StatusPage に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

StatusPage と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* StatusPage でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* StatusPage では、**IDP** Initiated SSO がサポートされます

## <a name="adding-statuspage-from-the-gallery"></a>ギャラリーからの StatusPage の追加

Azure AD への StatusPage の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に StatusPage を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**StatusPage**」と入力します。
1. 結果パネルから **StatusPage** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-statuspage"></a>StatusPage の Azure AD SSO の構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、StatusPage で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと StatusPage 内の関連ユーザー間にリンク関係が確立されている必要があります。

StatusPage で Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[StatusPage の SSO の構成](#configure-statuspage-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[StatusPage のテスト ユーザーの作成](#create-statuspage-test-user)** - StatusPage で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **AskYourTeam** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、次の手順を実行します。

    a. **[識別子]** テキスト ボックスに、次のいずれかのパターンを使用して URL を入力します。

    | 識別子 |
    |--------------|
    | `https://<subdomain>.statuspagestaging.com/` |
    | `https://<subdomain>.statuspage.io/` |
    |

    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。

     | [応答 URL] |
    |--------------|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume` |
    | `https://<subdomain>.statuspage.io/sso/saml/consume` |
    |

    > [!NOTE]
    > シングル サインオンを構成するために必要なメタデータは、StatusPage サポート チーム ( [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io)) に連絡して入手してください。 
    >
    > a. メタデータから発行者の値をコピーし、 **[識別子]** ボックスに貼り付けます。
    >
    > b. メタデータから応答 URL をコピーし、 **[応答 URL]** ボックスに貼り付けます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[StatusPage のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、Britta Simon に StatusPage へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[StatusPage]** を選択します。

2. アプリケーションの一覧で **[StatusPage]** を選択します。

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

## <a name="configure-statuspage-sso"></a>StatusPage の SSO の構成

1. StatusPage 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[StatusPage のセットアップ]** をクリックすると、StatusPage アプリケーションに移動します。 そこから、管理者の資格情報を入力して StatusPage にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. StatusPage を手動でセットアップする場合は、別の Web ブラウザー ウィンドウで、StatusPage 企業サイトに管理者としてサインインします。

1. メイン ツール バーで、 **[Manage Account]** をクリックします。

    ![StatusPage 企業サイトから選択された [Manage Account]\(アカウントの管理\) のスクリーンショット。](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. **[Single Sign-on]** タブをクリックします。

    ![[Single Sign-on]\(シングル サインオン\) タブのスクリーンショット。](./media/statuspage-tutorial/tutorial_statuspage_07.png)

1. [SSO Setup] ページで、次の手順に従います。

    ![[S S O Setup ]\(S S O の設定\) ページを示すスクリーンショット。ここで、説明されている値を入力できます。](./media/statuspage-tutorial/tutorial_statuspage_08.png)

    ![[Save Configuration]\(構成を保存\) ボタンを示すスクリーンショット。](./media/statuspage-tutorial/tutorial_statuspage_09.png)

    a. **[SSO Target URL]\(SSO ターゲット URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    b. ダウンロードした証明書をメモ帳で開き、その内容をコピーして、 **[Certificate]** ボックスに貼り付けます。

    c. **[構成の保存]** をクリックします。

### <a name="create-statuspage-test-user"></a>StatusPage のテスト ユーザーの作成

このセクションの目的は、StatusPage で Britta Simon というユーザーを作成することです。

StatusPage では、ジャストインタイム プロビジョニングがサポートされています。 この機能は、「[Azure AD シングル サインオンの構成](#configure-azure-ad-sso)」で既に有効にしています。

**StatusPage で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. StatusPage 企業サイトに管理者としてサインオンします。

1. 上部のメニューで **[Manage Account]** をクリックします。

    ![StatusPage 企業サイトから選択された [Manage Account]\(アカウントの管理\) のスクリーンショット。](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. **[チーム メンバー]** タブをクリックします。
  
    ![[チーム メンバー] タブのスクリーンショット。](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. **[チーム メンバーの追加]** をクリックします。
  
    ![[チーム メンバーの追加] ボタンのスクリーンショット。](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. プロビジョニングする有効なユーザーの **電子メール アドレス**、**名**、**姓** を、対応するテキスト ボックスに入力します。 

    ![[ユーザーの追加] ダイアログ ボックスを示すスクリーンショット。ここで、説明されている値を入力できます。](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. **[Role]** で **[Client Administrator]** を選択します。

1. **[アカウントの作成]** をクリックします。

### <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した StatusPage に自動的にサインインされます

* Microsoft マイ アプリを使用することができます。 マイ アプリで [StatusPage] タイルをクリックすると、SSO を設定した StatusPage に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

StatusPage を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
---
title: 'チュートリアル: Azure Active Directory と iQualify LMS の統合 | Microsoft Docs'
description: Azure Active Directory と iQualify LMS の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: f2baeaf72bb77dfe9247380b8b26f3860cbfd5b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999163"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>チュートリアル: Azure Active Directory と iQualify LMS の統合

このチュートリアルでは、iQualify LMS と Azure Active Directory (Azure AD) を統合する方法について説明します。
iQualify LMS と Azure AD の統合には、次の利点があります。

* iQualify LMS にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで iQualify LMS に自動的にサインイン (シングル サインオン) されるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

iQualify LMS と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* iQualify LMS でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* iQualify LMS では、**SP と IDP** によって開始される SSO がサポートされます
* iQualify LMS では、**Just In Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-iqualify-lms-from-the-gallery"></a>ギャラリーからの iQualify LMS の追加

Azure AD への iQualify LMS の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に iQualify LMS を追加する必要があります。

**ギャラリーから iQualify LMS を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**iQualify LMS**」と入力し、結果ウィンドウで **[iQualify LMS]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの iQualify LMS](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、iQualify LMS で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと iQualify LMS 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

iQualify LMS で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[iQualify LMS のシングル サインオンの構成](#configure-iqualify-lms-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[iQualify LMS のテスト ユーザーの作成](#create-iqualify-lms-test-user)** - iQualify LMS で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

iQualify LMS で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **iQualify LMS** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![このスクリーンショットは、[基本的な SAML 構成] を示しています。ここで、識別子と応答 U R L を入力し、[保存] を選択できます。](common/idp-intiated.png)

    a. **[識別子]** ボックスに、次の形式で URL を入力します。
    | |
    |--|--|
    | 運用環境: `https://<yourorg>.iqualify.com/`|
    | テスト環境: `https://<yourorg>.iqualify.io`|

    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。
    | |
    |--|--|
    | 運用環境: `https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | テスト環境: `https://<yourorg>.iqualify.io/auth/saml2/callback` |

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![このスクリーンショットは、[追加の U R L を設定します] を示しています。ここで、サインオン U R L を入力できます。](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。
    | |
    |--|--|
    | 運用環境: `https://<yourorg>.iqualify.com/login` |
    | テスト環境: `https://<yourorg>.iqualify.io/login` |

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[iQualify LMS クライアント サポート チーム](https://www.iqualify.com/)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. iQualify LMS アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。 **[編集]** アイコンをクリックして、 **[ユーザー属性]** ダイアログを開きます。

    ![このスクリーンショットは、[編集] アイコンが選択された状態の [User Attributes]\(ユーザー属性\) を示しています。](common/edit-attribute.png)

7. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、**編集アイコン** を使用して要求を編集するか、 **[新しい要求の追加]** を使用して要求を追加することで、上の図のように SAML トークン属性を構成し、次の手順を実行します。

    | 名前 | ソース属性|
    | --- | --- |
    | email | user.userprincipalname |
    | first_name | User.givenname |
    | last_name | User.surname |
    | person_id | "あなたの属性" |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![[新しい要求の追加] オプションが備わっている [ユーザー要求] のスクリーンショット。](common/new-save-attribute.png)

    ![スクリーンショットは、説明されている値を入力できる [ユーザー要求の管理] ダイアログ ボックスを示しています。](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[保存]** をクリックします。

    > [!Note]
    > **person_id** 属性は **省略可能** です。

8. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

9. **[iQualify LMS のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-iqualify-lms-single-sign-on"></a>iQualify LMS のシングル サインオンを構成する

1. 新しく Web ブラウザー ウィンドウを開き、iQualify LMS 環境に管理者としてサインインします。

1. ログインした後、右上のアバターをクリックし、 **[Account settings]\(アカウント設定\)** をクリックします。

    ![アカウント設定](./media/iqualify-tutorial/setting1.png)

1. [Account settings]\(アカウント設定\) 領域で、左側のリボン メニューをクリックし、 **[INTEGRATIONS]\(統合\)** をクリックします。

    ![[INTEGRATIONS]\(統合\)](./media/iqualify-tutorial/setting2.png)

1. [INTEGRATIONS]\(統合\)で、**[SAML]** アイコンをクリックします。

    ![[SAML] アイコン](./media/iqualify-tutorial/setting3.png)

1. **[SAML Authentication Settings (SAML 認証設定)]** ダイアログ ボックスで、次の手順を実行します。

    ![SAML 認証設定](./media/iqualify-tutorial/setting4.png)

    a. **[SAML SINGLE SIGN-ON SERVICE URL]\(SAML シングル サインオン サービス URL\)** ボックスに、Azure AD アプリケーション構成ウィンドウからコピーした **ログイン URL** の値を貼り付けます。

    b. **[SAML LOGOUT URL]\(SAML ログアウト URL\)** ボックスに、Azure AD アプリケーション構成ウィンドウからコピーした **ログアウト URL** の値を貼り付けます。

    c. ダウンロードした証明書ファイルをメモ帳で開き、その内容をコピーして、**[Public Certificate]\(パブリック証明書\)** ボックスに貼り付けます。

    d. **[LOGIN BUTTON LABEL]\( ログイン ボタン ラベル\)** に、ログイン ページに表示するボタンの名前を入力します。

    e. **[保存]** をクリックします。

    f. **[Update]\(更新\)** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに **brittasimon@yourcompanydomain.extension** と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に iQualify LMS へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]**、**[iQualify LMS]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[iQualify LMS]** を選択します。

    ![アプリケーションの一覧の iQualify LMS リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-iqualify-lms-test-user"></a>iQualify LMS のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを iQualify LMS に作成します。 iQualify LMS では、Just-In-Time ユーザー プロビジョニングがサポートされます。この設定は既定で有効です。 このセクションでは、ユーザー側で必要な操作はありません。 iQualify LMS にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [iQualify LMS] タイルをクリックすると、iQualify LMS アプリケーションのログイン ページが表示されます。 

   ![login page](./media/iqualify-tutorial/login.png) 

**[Sign in with Azure AD]\(Azure AD でサインイン\)** ボタンをクリックすると、iQualify LMS アプリケーションに自動的にサインオンします。

アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)
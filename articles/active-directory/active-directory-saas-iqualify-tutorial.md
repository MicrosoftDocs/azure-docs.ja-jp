---
title: "チュートリアル: Azure Active Directory と iQualify LMS の統合 | Microsoft Docs"
description: "Azure Active Directory と iQualify LMS の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: jeedes
ms.openlocfilehash: cab1ce3694372c137667e0179caf8d3523147f7a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>チュートリアル: Azure Active Directory と iQualify LMS の統合

このチュートリアルでは、iQualify LMS と Azure Active Directory (Azure AD) を統合する方法について説明します。

iQualify LMS と Azure AD の統合には、次の利点があります。

- iQualify LMS にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが Azure AD アカウントで自動的に iQualify LMS にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

iQualify LMS と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- iQualify LMS でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの iQualify LMS の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-iqualify-lms-from-the-gallery"></a>ギャラリーからの iQualify LMS の追加
Azure AD への iQualify LMS の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に iQualify LMS を追加する必要があります。

**ギャラリーから iQualify LMS を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**iQualify LMS**」と入力し、結果ウィンドウで **[iQualify LMS]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの iQualify LMS](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、iQualify LMS で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する iQualify LMS ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと iQualify LMS の関連ユーザーの間で、リンク関係が確立されている必要があります。

iQualify LMS で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

iQualify LMS で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[iQualify LMS テスト ユーザーの作成](#create-an-iqualify-lms-test-user)** - iQualify LMS で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure ポータルで Azure AD のシングル サインオンを有効にし、iQualify LMS アプリケーションでシングル サインオンを構成します。

**iQualify LMS で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure ポータルの **iQualify LMS** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_samlbase.png)

3. **[iQualify LMS のドメインと URL]** セクションで、IDP 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[iQualify LMS のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_url.png)

    a. **[識別子]** ボックスに、次のパターンで URL を入力します。 
    | |
    |--|--|
    | 運用環境: `https://<yourorg>.iqualify.com/`|
    | テスト環境: `https://<yourorg>.iqualify.io`|
    
    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。 
    | |
    |--|--|
    | 運用環境: `https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | テスト環境: `https://<yourorg>.iqualify.io/auth/saml2/callback` |

4. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[iQualify LMS のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_url1.png)

    **[サインオン URL]** ボックスに、次の形式で URL を入力します。
    | |
    |--|--|
    | 運用環境: `https://<yourorg>.iqualify.com/login` |
    | テスト環境: `https://<yourorg>.iqualify.io/login` |
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[iQualify LMS クライアント サポート チーム](https://www.iqualify.com)に連絡してください。 

5. iQualify LMS アプリケーションでは、Security Assertion Markup Language (SAML) のアサーションを特定の形式で表示する必要があります。 次のスクリーンショットに示すように、iQualify LMS アプリケーション統合ページの **[ユーザー属性]** セクションで、要求を構成し、属性の値を管理します。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-iqualify-tutorial/atb.png)

6. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、以下の表に示す行ごとに、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | --- | --- |    
    | email | user.userprincipalname |
    | first_name | User.givenname |
    | last_name | User.surname |
    | person_id | "あなたの属性" | 

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-iqualify-tutorial/atb2.png)

    ![Configure Single Sign-On](./media/active-directory-saas-iqualify-tutorial/atb3.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[値]** 一覧から、その行に対して表示される値を入力します。
    
    d. **[OK]** をクリックします。

    e. 表の次の行についても "a" ～ "d" の手順を繰り返します。 

    > [!Note]
    > **person_id** 属性に対する "a" ～ "d" の手順の繰り返しは**省略可能**です。

7. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_certificate.png) 

8. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-iqualify-tutorial/tutorial_general_400.png)
    
9. **[iQualify LMS 構成]** セクションで、**[iQualify LMS の構成]** をクリックして **[サインオンの構成]** ウィンドウを開きます。 **クイック リファレンス セクション**から、**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![iQualify LMS の構成](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_configure.png) 

10.  新しく Web ブラウザー ウィンドウを開き、iQualify LMS 環境に管理者としてサインインします。

11. ログインした後、右上のアバターをクリックし、**[Account settings]\(アカウント設定\)** をクリックします。

    ![アカウント設定](./media/active-directory-saas-iqualify-tutorial/setting1.png) 
12. [Account settings]\(アカウント設定\) 領域で、左側のリボン メニューをクリックし、**[INTEGRATIONS]\(統合\)** をクリックします。
    
    ![[INTEGRATIONS]\(統合\)](./media/active-directory-saas-iqualify-tutorial/setting2.png)

13. [INTEGRATIONS]\(統合\)で、**[SAML]** アイコンをクリックします。

    ![[SAML] アイコン](./media/active-directory-saas-iqualify-tutorial/setting3.png)

14. **[SAML Authentication Settings (SAML 認証設定)]** ダイアログ ボックスで、次の手順を実行します。

    ![SAML 認証設定](./media/active-directory-saas-iqualify-tutorial/setting4.png)

    a. **[SAML SINGLE SIGN-ON SERVICE URL]\(SAML シングル サインオン サービス URL\)** ボックスに、Azure AD アプリケーション構成ウィンドウからコピーした **[SAML Single Sign-on Service URL]\(SAML シングル サインオン サービス URL\)** の値を貼り付けます。
    
    b. **[SAML LOGOUT URL]\(SAML ログアウト URL\)** ボックスに、Azure AD アプリケーション構成ウィンドウからコピーした **[Sign‑Out URL]\(サインアウト URL\)** の値を貼り付けます。
    
    c. ダウンロードした証明書ファイルをメモ帳で開き、その内容をコピーして、**[Public Certificate]\(パブリック証明書\)** ボックスに貼り付けます。
    
    d. **[LOGIN BUTTON LABEL]\( ログイン ボタン ラベル\)** に、ログイン ページに表示するボタンの名前を入力します。
    
    e. **[保存]**をクリックします。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[Update]\(更新\)** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-iqualify-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-iqualify-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-iqualify-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-iqualify-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-an-iqualify-lms-test-user"></a>IQualify LMS テスト ユーザーを作成する

このセクションでは、Britta Simon というユーザーを iQualify に作成します。 iQualify LMS では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ iQualify に存在しない場合は、iQualify LMS にアクセスしようとしたときに新しいユーザーが作成されます。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に iQualify LMS へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**iQualify LMS に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[iQualify LMS]** を選択します。

    ![アプリケーションの一覧の iQualify LMS リンク](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [iQualify LMS] タイルをクリックすると、iQualify LMS アプリケーションのログイン ページが表示されます。 

   ![login page](./media/active-directory-saas-iqualify-tutorial/login.png) 

**[Sign in with Azure AD]\(Azure AD でサインイン\)** ボタンをクリックすると、iQualify LMS アプリケーションに自動的にサインオンします。

アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_203.png


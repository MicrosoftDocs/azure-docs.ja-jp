---
title: "チュートリアル: Azure Active Directory と Absorb LMS の統合 | Microsoft Docs"
description: "Azure Active Directory と Absorb LMS の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 99523f27fe43f07081bd43f5d563e554bda4426f
ms.openlocfilehash: 3c68c3ac7d6be593476d419f8c015931b206eead
ms.contentlocale: ja-jp
ms.lasthandoff: 08/05/2017

---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>チュートリアル: Azure Active Directory と Absorb LMS の統合

このチュートリアルでは、Absorb LMS と Azure Active Directory (Azure AD) を統合する方法について説明します。

Absorb LMS と Azure AD の統合には、次の利点があります。

- Absorb LMS にアクセスするユーザーを Azure AD で管理できます。
- ユーザーが Azure AD アカウントで自動的に Absorb LMS にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、 「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Absorb LMS と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Absorb LMS でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Absorb LMS の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-absorb-lms-from-the-gallery"></a>ギャラリーからの Absorb LMS の追加
Azure AD への Absorb LMS の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Absorb LMS を追加する必要があります。

**ギャラリーから Absorb LMS を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Absorb LMS**」と入力し、結果ウィンドウで **[Absorb LMS]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Absorb LMS](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Absorb LMS で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Absorb LMS ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Absorb LMS の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Absorb LMS の **[Username]** の値として割り当てます。

Absorb LMS で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Absorb LMS テスト ユーザーの作成](#create-an-absorb-lms-test-user)** - Absorb LMS で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Absorb LMS アプリケーションでシングル サインオンを構成します。

**Absorb LMS で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Absorb LMS** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_samlbase.png)

3. **[Absorb LMS のドメインと URL]** セクションで、次の手順を実行します。

    ![[Absorb LMS のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_url.png)

    a. **[識別子]** ボックスに、`https://<subdomain>.myabsorb.com/Account/SAML` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<subdomain>.myabsorb.com/Account/SAML` のパターンを使用して URL を入力します。
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[Absorb LMS クライアント サポート チーム](https://www.absorblms.com/support)に連絡してください。 

4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_certificate.png) 

6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-absorblms-tutorial/tutorial_general_400.png)
    
7. **[Absorb LMS Configuration (Absorb LMS 構成)]** セクションで、**[Configure Absorb LMS (Absorb LMS を構成する)]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![Absorb LMS 構成](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_configure.png) 

8. 別の Web ブラウザー ウィンドウで、Absorb LMS 企業サイトに管理者としてログインします。

9. 管理インターフェイスで**アカウント アイコン**をクリックします。 

    ![[シングル サインオンの構成]](./media/active-directory-saas-absorblms-tutorial/1.png)

10. **[Portal Settings]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-absorblms-tutorial/2.png)
    
11. **[Users]** タブをクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-absorblms-tutorial/3.png)

12. シングル サインオンの各構成フィールドで、次の手順を実行します。

    ![Configure Single Sign-On](./media/active-directory-saas-absorblms-tutorial/4.png)

    a. 適切な**モード**を選択します。

    b. Azure Portal からダウンロードした証明書をメモ帳で開き、**---BEGIN CERTIFICATE---** および **---END CERTIFICATE---** タグを削除して、残りの内容を **[Key]** ボックスに貼り付けます。
    
    c. **[Id Property]** で、Azure AD でユーザー識別子として構成した該当する属性を選択します (たとえば、Azure AD で userprinciplename を選択した場合、ここでは Username を選択します)。

    d. **[Login URL]** に、Azure Portal の **[サインオンの構成]** ウィンドウでコピーした **SAML シングル サインオン サービス URL** 値を貼り付けます。

    e. **[Logout URL]** に、Azure Portal の **[サインオンの構成]** ウィンドウでコピーした**サインアウト URL** 値を貼り付けます。

13. **[Only Allow SSO Login]** を有効にします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-absorblms-tutorial/5.png)

14. **[Save]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-absorblms-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-absorblms-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![[追加] ボタン](./media/active-directory-saas-absorblms-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-absorblms-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **[作成]**をクリックします。

### <a name="create-an-absorb-lms-test-user"></a>Absorb LMS のテスト ユーザーの作成

Azure AD ユーザーが Absorb LMS にログインできるようにするには、ユーザーを Absorb LMS にプロビジョニングする必要があります。  
Absorb LMS の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Absorb LMS 企業サイトに管理者としてログインします。

2. **[Users]** タブをクリックします。

    ![[ユーザーの招待]](./media/active-directory-saas-absorblms-tutorial/absorblms_users.png)

3. **[Users]** タブの **[Users]** をクリックします。

    ![[ユーザーの招待]](./media/active-directory-saas-absorblms-tutorial/absorblms_userssub.png)

4.  **[Add New]** ドロップダウンで **[User]** を選択します。

    ![[ユーザーの招待]](./media/active-directory-saas-absorblms-tutorial/absorblms_createuser.png)

5. **[Add User]** ページで、次の手順に従います。

    ![[ユーザーの招待]](./media/active-directory-saas-absorblms-tutorial/user.png)

    a. **[First Name]** ボックスに、ユーザーの名を入力します (この例では Britta)。

    b. **[Last Name]** ボックスに、ユーザーの姓を入力します (この例では Simon)。
    
    c. **[Last Name]** ボックスに、ユーザーの姓を入力します (この例では Simon)。

    d. **[Password]** ボックスに、Britta Simon のパスワードを入力します。

    e. **[パスワードの確認]** ボックスに、同じパスワードを入力します。
    
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[ACTIVE]** を選択します。   

6. **[Save]** をクリックします。
 
### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Absorb LMS へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

**Absorb LMS に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Absorb LMS]** を選択します。

    ![アプリケーションの一覧の Absorb LMS のリンク](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Absorb LMS のタイルをクリックすると、自動的に Absorb LMS アプリケーションにサインオンします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_203.png



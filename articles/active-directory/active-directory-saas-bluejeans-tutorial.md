---
title: "チュートリアル: Azure Active Directory と BlueJeans の統合 | Microsoft Docs"
description: "Azure Active Directory と BlueJeans の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: jeedes
ms.openlocfilehash: 03bf65852b8d3cf14aebf155891a028db86e78d0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>チュートリアル: Azure Active Directory と BlueJeans の統合

このチュートリアルでは、BlueJeans と Azure Active Directory (Azure AD) を統合する方法について説明します。

BlueJeans と Azure AD の統合には、次の利点があります。

- BlueJeans にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に BlueJeans にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

BlueJeans と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- BlueJeans でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの BlueJeans の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-bluejeans-from-the-gallery"></a>ギャラリーからの BlueJeans の追加
Azure AD への BlueJeans の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に BlueJeans を追加する必要があります。

**ギャラリーから BlueJeans を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**BlueJeans**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_search.png)

5. 結果ウィンドウで **[BlueJeans]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、BlueJeans で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する BlueJeans ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと BlueJeans の関連ユーザーの間で、リンク関係が確立されている必要があります。

BlueJeans で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

BlueJeans で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[BlueJeans のテスト ユーザーの作成](#creating-a-bluejeans-test-user)** - BlueJeans で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、BlueJeans アプリケーションでシングル サインオンを構成します。

**BlueJeans で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **BlueJeans** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_samlbase.png)

3. **[BlueJeans のドメインと URL]** セクションで、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_url.png)

    a. **[サインオン URL]** ボックスに、`https://<companyname>.BlueJeans.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<companyname>.BlueJeans.com` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[BlueJeans クライアント サポート チーム](https://support.bluejeans.com/contact)に問い合わせください。 
 
4. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-bluejeans-tutorial/tutorial_general_400.png)

6. **[BlueJeans 構成]** セクションで、**[BlueJeans の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから**サインアウト URL、パスワードの変更 URL、および SAML シングル サインオン サービス URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_configure.png) 

7. 別の Web ブラウザー ウィンドウで、 **BlueJeans** 企業サイトに管理者としてログインします。

8. **[管理] \> [グループ設定] \> [セキュリティ]** の順にクリックします。
   
   ![管理](./media/active-directory-saas-bluejeans-tutorial/IC785868.png "Admin")

9. **[セキュリティ]** セクションで、次の手順を実行します。
   
   ![SAML シングル サインオン](./media/active-directory-saas-bluejeans-tutorial/IC785869.png "SAML Single Sign On")   
   
   a. **[SAML シングル サインオン]**を選択します。
  
   b. **[自動プロビジョニングの有効化]**を選択します。

10. 次の手順を実行します。

    ![証明書パス](./media/active-directory-saas-bluejeans-tutorial/IC785870.png "Certificate Path")
    
    a. **[ファイルの選択]**をクリックし、ダウンロードした証明書をアップロードします。
   
    b. **SAML シングル サインオン サービス URL** を **[Login URL]** (ログイン URL) ボックスに貼り付けます。
   
    c. **パスワードの変更 URL** を **[Password Change URL]** (パスワード変更 URL) ボックスに貼り付けます。
   
    d. **サインアウト URL** を **[Logout URL]** (ログアウト URL) ボックスに貼り付けます。

11. 次の手順を実行します。
    
    ![変更を保存](./media/active-directory-saas-bluejeans-tutorial/IC785874.png "Save Changes")
    
    a. **[User id]** (ユーザー ID) ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`」と入力します。
   
    b. **[Email]** (電子メール) ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`」と入力します。
   
    c. **[変更を保存]**をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bluejeans-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bluejeans-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bluejeans-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bluejeans-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-bluejeans-test-user"></a>BlueJeans のテスト ユーザーの作成

Azure AD ユーザーが BlueJeans にログインできるようにするには、ユーザーを BlueJeans にプロビジョニングする必要があります。  

BlueJeans の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **BlueJeans** 企業サイトに管理者としてログインします。

2. **[管理] \> [ユーザーの管理] \> [ユーザーの追加]** の順にクリックします。
   
   ![管理](./media/active-directory-saas-bluejeans-tutorial/IC785877.png "Admin")
   
   >[!IMPORTANT]
   >**[ユーザーの追加]** タブは、**[セキュリティ]** タブの **[自動プロビジョニングの有効化]** がオフになっている場合にのみ使用できます。 
   
3. **[ユーザーの追加]** セクションで、次の手順を実行します。

    ![ユーザーの追加](./media/active-directory-saas-bluejeans-tutorial/IC785886.png "Add User")
    
    a. 対応するテキスト ボックスに、プロビジョニングする有効な AAD アカウントの **BlueJeans ユーザー名**、**メール アドレス**、**BlueJeans ミーティング ID**、**モデレーター パスコード**、**フル ネーム**、**会社**を入力します。
    
    b. **[ユーザーの追加]**をクリックします。

>[!NOTE]
>BlueJeans から提供されている他の BlueJeans ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、BlueJeans へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**BlueJeans に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[BlueJeans]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [BlueJeans] タイルをクリックすると、BlueJeans アプリケーションのログイン ページが表示されます。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_203.png


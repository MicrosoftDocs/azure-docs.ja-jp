---
title: "チュートリアル: Azure Active Directory と Canvas LMS の統合 | Microsoft Docs"
description: "Azure Active Directory と Canvas LMS の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: bfed291c-a33e-410d-b919-5b965a631d45
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: jeedes
ms.openlocfilehash: 2212b7a81b66d1afd1aa78d1487b07b6d7b84129
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-canvas-lms"></a>チュートリアル: Azure Active Directory と Canvas LMS の統合

このチュートリアルでは、Canvas と Azure Active Directory (Azure AD) を統合する方法について説明します。

Canvas と Azure AD の統合には、次の利点があります。

- Azure AD で誰が Canvas にアクセスできるかを制御できます
- ユーザーが自分の Azure AD アカウントで Canvas に自動的にサインオンされる (シングル サインオン) ようにできます
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Canvas の統合を構成するには、次の項目が必要です。

- Azure AD サブスクリプション
- Canvas でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Canvas の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-canvas-from-the-gallery"></a>ギャラリーからの Canvas の追加
Canvas の Azure AD への統合を構成するには、Canvas をギャラリーから管理対象 SaaS アプリの一覧に追加する必要があります。

**ギャラリーから Canvas を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに「**Canvas**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_search.png)

5. 結果ウィンドウで **[Canvas]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" という名前のテスト ユーザーに基づいて、Canvas で Azure AD のシングル サインオンを構成およびテストします。

シングル サインオンを機能させるには、Azure AD が Azure AD のユーザーに対応する Canvas のユーザーを認識している必要があります。 つまり、Azure AD ユーザーと Canvas の関連するユーザーの間のリンク関係が確立されている必要があります。

Canvas で、Azure AD での **[ユーザー名]** の値を **[ユーザー名]** の値として割り当てることによってリンク関係を確立します。

Canvas で Azure AD のシングル サインオンを構成およびテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Canvas テスト ユーザーの作成](#creating-a-canvas-test-user)** - Azure AD でのユーザーにリンクされた、Canvas での Britta Simon の対応するユーザーを作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Canvas アプリケーションでシングル サインオンを構成します。

**Canvas で Azure AD のシングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **[Canvas]** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_samlbase.png)

3. **[Canvas Domain and URLs] \(Canvas のドメインと URL)** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_url.png)

    a. **[サインオン URL]** ボックスに、`https://<tenant-name>.instructure.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<tenant-name>.instructure.com/saml2` の形式で値を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Canvas クライアント サポート チーム](https://community.canvaslms.com/community/help)に問い合わせてください。 
 
4. **[SAML 署名証明書]** セクションで、証明書の **THUMBPRINT** 値をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_400.png)

6. **[Canvas Configuration] \(Canvas 構成)** セクションで、**[Configure Canvas] \(Canvas の構成)** をクリックして **[Configure sign-on] \(サインオンの構成)** ウィンドウを開きます。 **[クイック リファレンス] セクション**から、**パスワードの変更 URL、サインアウト URL、SAML エンティティ ID、および SAML シングル サインオン サービス URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_configure.png) 
 
7. 別の Web ブラウザー ウィンドウで、Canvas 企業サイトに管理者としてログインします。

8. **[コース] \> [管理アカウント] \> [Microsoft]** の順にクリックします。
   
    ![Canvas](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Canvas")

9. 左側のナビゲーション ウィンドウで **[認証]** を選択し、**[新しい SAML 構成を追加]** をクリックします。
   
    ![Authentication](./media/active-directory-saas-canvas-lms-tutorial/IC775991.png "Authentication")

10. [現在の統合] ページで、次の手順を実行します。
   
    ![Current Integration](./media/active-directory-saas-canvas-lms-tutorial/IC775992.png "Current Integration")

    a. **[IdP Entity ID] \(IdP エンティティ ID)** テキスト ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。

    b. **[Log On URL] \(ログオン URL)** テキスト ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    c. **[Log Out URL] \(ログアウト URL)** テキスト ボックスに、Azure Portal からコピーした**サインアウト URL** の値を貼り付けます。

    d. **[Change Password Link] \(パスワードの変更リンク)** テキスト ボックスに、Azure Portal からコピーした**パスワードの変更 URL** の値を貼り付けます。 

    e. **[Certificate Fingerprint] \(証明書のフィンガープリント)** テキスト ボックスに、Azure Portal からコピーした証明書の **THUMBPRINT** 値を貼り付けます。      
        
    f. **[ログイン属性]** の一覧で、**[NameID]** を選択します。

    g. **[識別子の形式]** の一覧で、**[emailAddress]** を選択します。

    h. **[認証設定の保存]**をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-canvas-lms-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-canvas-lms-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-canvas-lms-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-canvas-lms-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-canvas-test-user"></a>Canvas テスト ユーザーの作成

Azure AD ユーザーが Canvas にログインできるようにするには、そのユーザーを Canvas にプロビジョニングする必要があります。

Canvas の場合、ユーザー プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Canvas** テナントにログインします。

2. **[コース] \> [管理アカウント] \> [Microsoft]** の順にクリックします。
   
   ![Canvas](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Canvas")

3. **[ユーザー]**をクリックします。
   
   ![ユーザー](./media/active-directory-saas-canvas-lms-tutorial/IC775995.png "Users")

4. **[新しいユーザーの追加]**をクリックします。
   
   ![ユーザー](./media/active-directory-saas-canvas-lms-tutorial/IC775996.png "Users")

5. [新しいユーザーの追加] ダイアログ ページで、次の手順を実行します。
   
   ![ユーザーの追加](./media/active-directory-saas-canvas-lms-tutorial/IC775997.png "Add User")
   
   a. **[Full Name] \(フルネーム)** テキスト ボックスに、ユーザーの名前 (**BrittaSimon** など) を入力します。

   b. **[電子メール]** テキスト ボックスに、ユーザーの電子メール (**brittasimon@contoso.com** など) を入力します。

   c. **[ログイン]** テキスト ボックスに、ユーザーの Azure AD 電子メール アドレス (**brittasimon@contoso.com** など) を入力します。

   d. **[このアカウント作成についてユーザーに電子メールを送信]**を選択します。

   e. **[ユーザーの追加]**をクリックします。

>[!NOTE]
>Canvas から提供されている他の Canvas ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Canvas へのアクセス権を付与することによって、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を Canvas に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で、**[Canvas]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルの [Canvas] タイルをクリックすると、Canvas アプリケーションに自動的にサインオンされます。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_203.png


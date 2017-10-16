---
title: "チュートリアル: Azure Active Directory と Velpic SAML の統合 | Microsoft Docs"
description: "Azure Active Directory と Velpic SAML の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: jeedes
ms.openlocfilehash: 5325f3cca00167e6b7b687509ce43435447ad2f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-velpic-saml"></a>チュートリアル: Azure Active Directory と Velpic SAML の統合

このチュートリアルでは、Velpic SAML と Azure Active Directory (Azure AD) を統合する方法について説明します。

Velpic SAML と Azure AD の統合には、次の利点があります。

- Velpic SAML にアクセス可能なユーザーを Azure AD で管理できます。
- ユーザーが自分の Azure AD アカウントで Velpic SAML に自動的にサインオン (シングル サインオン) するように設定できます。
- 1 つの中央サイト (Microsoft Azure 管理ポータル) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Velpic SAML と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Velpic SAML でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の試用環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の試用版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Velpic SAML の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-velpic-saml-from-the-gallery"></a>ギャラリーからの Velpic SAML の追加
Azure AD への Velpic SAML の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Velpic SAML を追加する必要があります。

**ギャラリーから Velpic SAML を追加するには、次の手順を実行します。**

1. **[Microsoft Azure 管理ポータル](https://portal.azure.com)**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. ダイアログの上部にある **[追加]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Velpic SAML**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_search.png)

5. 結果ウィンドウで **[Velpic SAML]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーを基に、Velpic SAML で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Velpic SAML ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Velpic SAML の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Velpic SAML の **[Username] \(ユーザー名)** の値として割り当てます。

Velpic SAML で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Velpic SAML のテスト ユーザーの作成](#creating-a-velpic-saml-test-user)** - Velpic SAML で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Microsoft Azure 管理ポータルで Azure AD のシングル サインオンを有効にし、Velpic SAML アプリケーションでシングル サインオンを構成します。

**Velpic SAML で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Microsoft Azure 管理ポータルの **Velpic SAML** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_samlbase.png)

3. **[Velpic SAML のドメインとURL]** セクションで、次の手順を実行します。

    ![Configure Single Sign-On](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_url.png)

    a. **[サインオン URL]** テキストボックスに、「`https://<sub-domain>.velpicsaml.net`」と入力します。

    b. **[識別子]** テキストボックスに、"**シングル サインオン URL**" の値 (`https://auth.velpic.com/saml/v2/<entity-id>/login`) を貼り付けます。
    
    > [!NOTE]
    > サインオン URL は Velpic SAML チームによって提供され、識別子の値は Velpic SAML 側の SSO プラグインの構成時に入手できます。 Velpic SAML アプリケーションのページからこの値をコピーして、ここに貼り付けてください。

4. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** をクリックし、コンピューターに XML ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_400.png)

6. [Velpic SAML 構成] セクションで、[Velpic SAML の構成] をクリックして、[サインオンの構成] ウィンドウを開きます。 [クイック リファレンス] セクションから SAML エンティティ ID をコピーします。

7. 別の Web ブラウザーのウィンドウで、Velpic SAML 企業サイトに管理者としてログインします。

8. **[Manage] \(管理)** タブをクリックして **[Integration] \(統合)** セクションに移動します。ここで、**[Plugins] \(プラグイン)** をクリックして、サインイン用の新しいプラグインを作成する必要があります。

    ![プラグイン](./media/active-directory-saas-velpicsaml-tutorial/velpic_1.png)

9. **[Add plugin] \(プラグインの追加)** をクリックします。
    
    ![プラグイン](./media/active-directory-saas-velpicsaml-tutorial/velpic_2.png)

10. [Add plugin] \(プラグインの追加) ページで **[SAML]** タイルをクリックします。
    
    ![プラグイン](./media/active-directory-saas-velpicsaml-tutorial/velpic_3.png)

11. 新しい SAML プラグインの名前を入力し、**[追加]** をクリックします。

    ![プラグイン](./media/active-directory-saas-velpicsaml-tutorial/velpic_4.png)

12. 詳細を次のように入力します。

    ![プラグイン](./media/active-directory-saas-velpicsaml-tutorial/velpic_5.png)

    a. **[名前]** テキストボックスに、SAML プラグインの名前を入力します。

    b. **[発行者の URL]** テキストボックスに、Azure Portal の **[サインオンの構成]** ウィンドウからコピーした **SAML エンティティ ID** を貼り付けます。

    c. **[Provider Metadata Config] \(Provider メタデータ構成)** で、Azure Portal からダウンロードしたメタデータ XML ファイルをアップロードします。

    d. **Auto create new users \(新規ユーザーの自動作成)** チェックボックスをオンにして、SAML のジャストインタイム プロビジョニングを有効にすることもできます。 Velpic にユーザーが存在せず、このチェックボックスがオンになっていない場合は、Azure からのログインに失敗します。 このチェックボックスがオンになっている場合、ユーザーは、ログイン時に Velpic に自動的にプロビジョニングされます。 

    e. テキストボックスから**シングル サインオン URL** をコピーして、Azure Portal に貼り付けます。
    
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[保存]**をクリックします。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Microsoft Azure 管理ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-velpic-saml-test-user"></a>Velpic SAML のテスト ユーザーの作成

アプリケーションは、ジャスト イン タイムのユーザー プロビジョニングをサポートしているため、通常、この手順は必要ありません。 自動ユーザー プロビジョニングが有効でない場合は、次の手順にしたがって手動でユーザーを作成することができます。

Velpic SAML 企業サイトに管理者としてログインし、次の手順を実行します。
    
1. [Manage] \(管理) タブをクリックして [Users] \(ユーザー) セクションに移動し、[New] \(新規) をクリックしてユーザーを追加します。

    ![add user](./media/active-directory-saas-velpicsaml-tutorial/velpic_7.png)

2. **[Create New User] \(新しいユーザーの作成)** ダイアログ ページで、次の手順を実行します。

    ![ユーザー](./media/active-directory-saas-velpicsaml-tutorial/velpic_8.png)
    
    a. **[First Name] \(名)** テキストボックスに、Britta Simon の名を入力します。

    b. **[Last Name] \(姓)** テキストボックスに、Britta Simon の姓を入力します。

    c. **[User Name] \(ユーザー名)** テキストボックスに、Britta Simon のユーザーを入力します。

    d. **[Email (電子メール)]** ボックスに、Britta Simon アカウントの電子メール アドレスを入力します。

    e. その他の情報は省略可能です。必要に応じて入力してください。
    
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[保存]**をクリックします。  

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Velpic SAML へのアクセス権を付与して、Azure のシングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を Velpic SAML に割り当てるには、次の手順を実行します。**

1. Azure 管理ポータルでアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧から **[Velpic SAML]** を選択します。

    ![Configure Single Sign-On](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

1. アクセス パネルで [Velpic SAML] タイルをクリックすると、Velpic SAML アプリケーションのログイン ページが表示されます。 サインイン ページに **[Log In With Azure AD] \(Azure AD でログイン)** と表示されます。

    ![プラグイン](./media/active-directory-saas-velpicsaml-tutorial/velpic_6.png)

2. **[Log In With Azure AD] \(Azure AD でログイン)** をクリックし、Azure AD アカウントを使用して Velpic にログインします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_203.png


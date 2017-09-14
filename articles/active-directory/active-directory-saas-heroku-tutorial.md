---
title: "チュートリアル: Azure Active Directory と Heroku の統合 | Microsoft Docs"
description: "Azure Active Directory と Heroku の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d7d72ec6-4a60-4524-8634-26d8fbbcc833
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: d30605e4757b484f327a784b73f939b62ef59373
ms.contentlocale: ja-jp
ms.lasthandoff: 07/04/2017


---
# <a name="tutorial-azure-active-directory-integration-with-heroku"></a>チュートリアル: Azure Active Directory と Heroku の統合

このチュートリアルでは、Heroku と Azure Active Directory (Azure AD) を統合する方法について説明します。

Heroku と Azure AD の統合には、次の利点があります。

- Heroku にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Heroku にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Heroku と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Heroku でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Heroku を追加する
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-heroku-from-the-gallery"></a>ギャラリーから Heroku を追加する
Azure AD への Heroku の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Heroku を追加する必要があります。

**ギャラリーから Heroku を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「 **Box**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_search.png)

5. 結果ウィンドウで **[Heroku]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Heroku で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Heroku ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Heroku の関連ユーザーの間で、リンク関係が確立されている必要があります。

Heroku で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Heroku で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Heroku テスト ユーザーの作成](#creating-a-heroku-test-user)** - Heroku で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure AD のシングル サインオンを Azure Portal で有効にし、Heroku アプリケーションでシングル サインオンを構成します。

**Heroku で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Heroku** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_samlbase.png)

3. **[Heroku Domain and URLs]\(Heroku のドメインと URL\)** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_url.png)

    a. **[サインオン URL]** ボックスに、次の形式で URL を入力します。    
    `https://sso.heroku.com/saml/<company-name>/init`

    b. **[Identifier URL]\(ID URL\)** ボックスに、次の形式で URL を入力します。            
    `https://sso.heroku.com/saml/<company-name>`

    > [!NOTE]
    >これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 Heroku チームからこれらの値を取得します。これについては、この記事の以降のセクションで説明しています。 
        
4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-heroku-tutorial/tutorial_general_400.png)

6. Heroku で SSO を有効にするには、次の手順に従います。
   
    a. Heroku アカウントに管理者としてログインします。

    b. **[設定]** タブをクリックします。

    c. **[Single Sign On Page]** で、**[Upload Metadata]** をクリックします。

    d. Azure Portal からダウンロードしたメタデータ ファイルをアップロードします。

    e. セットアップが成功すると、管理者には確認のダイアログ ボックスと、エンド ユーザー用の SSO ログインの URL が表示されます。 

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[Heroku Login URL]\(Heroku ログイン URL\)** と **[Heroku Entity ID]\(Heroku エンティティ ID\)** の値をコピーして Azure Portal の **[Heroku Domain and URLs]\(Heroku のドメインと URL\)** セクションに戻り、それらの値を **[サインオン URL]** と **[識別子]** ボックスにそれぞれ貼り付けます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_52.png) 
    
8. **[次へ]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory Enterprise Applications]\(Active Directory エンタープライズ アプリケーション\)** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-heroku-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-heroku-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-heroku-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-heroku-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-a-heroku-test-user"></a>Heroku テスト ユーザーの作成

このセクションでは、Heroku で Britta Simon というユーザーを作成します。 Heroku では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ存在しない場合は、Heroku へのアクセス時に新しいユーザーが作成されます。 アカウントがプロビジョニングされると、確認の電子メールが送信されます。エンド ユーザーはそこに記載された受信確認のリンクをクリックする必要があります。

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、[Heroku クライアント サポート チーム](https://www.heroku.com/support)にお問い合わせください。
>  

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Heroku へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようします。

![ユーザーの割り当て][200] 

**Heroku に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Heroku]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Heroku] タイルをクリックすると、自動的に Heroku アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_203.png


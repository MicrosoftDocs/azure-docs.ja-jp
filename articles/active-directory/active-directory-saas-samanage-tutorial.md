---
title: "チュートリアル: Azure Active Directory と Samanage の統合 | Microsoft Docs"
description: "Azure Active Directory と Samanage の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: c54dbe407145a29a712acc3c0fb549a38ac26bed
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>チュートリアル: Azure Active Directory と Samanage の統合

このチュートリアルでは、Samanage と Azure Active Directory (Azure AD) を統合する方法について説明します。

Samanage と Azure AD の統合には、次の利点があります。

- Samanage にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Samanage にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Samanage と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Samanage でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Samanage の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-samanage-from-the-gallery"></a>ギャラリーからの Samanage の追加
Azure AD への Samanage の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Samanage を追加する必要があります。

**ギャラリーから Samanage を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Samanage**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_search.png)

5. 結果ウィンドウで **[Samanage]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Samanage で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Samanage ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Samanage の関連ユーザーの間で、リンク関係が確立されている必要があります。

Samanage で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Samanage で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Samanage のテスト ユーザーの作成](#creating-a-samanage-test-user)** - Samanage で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Samanage アプリケーションでシングル サインオンを構成します。

**Samanage で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Samanage** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_samlbase.png)

3. **[Samanage のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_url.png)

    a. **[サインオン URL]** ボックスに、`https://<Company Name>.samanage.com/saml_login/<Company Name>` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<Company Name>.samanage.com` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子に値を置き換えます。実際の値については後で説明します。 詳細については、[Samanage クライアント サポート チーム](https://www.samanage.com/support)に問い合わせてください。    
 
4. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-samanage-tutorial/tutorial_general_400.png)

6. **[Samanage 構成]** セクションで、**[Samanage の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL と SAML エンティティ ID** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_configure.png) 

7. 別の Web ブラウザーのウィンドウで、Samanage 企業サイトに管理者としてログインします。

8. **[Dashboard]** をクリックして、左のナビゲーション ウィンドウで **[Setup]** を選択します。
   
    ![Dashboard](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "Dashboard")

9. **[シングル サインオン]**をクリックします。
   
    ![シングル サインオン](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_002.png "Single Sign-On")

10. **[Login using SAML (SAML でログイン)]** セクションで、次の手順を実行します。
   
    ![SAML でログイン](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_003.png "Login using SAML")
 
    a. **[Enable Single Sign-On with SAML (SAML でのシングル サインオンを有効にする)]** をクリックします。  
 
    b. **[Identity Provider URL]\(ID プロバイダー URL\)** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。    
 
    c. **[Login URL]\(ログイン URL\)** が Azure Portal の **[Samanage のドメインと URL]** セクションの **[サインオン URL]** と一致することを確認します。
 
    d. **[Logout URL]\(ログアウト URL\)** ボックスに、Azure Portal からコピーした**サインアウト URL** の値を入力します。
 
    e. **[SAML Issuer]**\(SAML 発行者\) ボックスに、ID プロバイダーに設定されたアプリ ID URI を入力します。
 
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 Azure Portal からダウンロードした base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[Paste your Identity Provider x.509 Certificate below]\(ID プロバイダー x.509 証明書を貼り付けてください\)** ボックスに貼り付けます。
 
    g. **[Create users if they do not exist in Samanage (Samanage に存在しない場合にユーザーを作成する)]** をクリックします。
 
    h. **[Update]**をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
 
### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-samanage-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-samanage-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-samanage-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-samanage-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-a-samanage-test-user"></a>Samanage のテスト ユーザーの作成

Azure AD ユーザーが Samanage にログインできるようにするには、そのユーザーを Samanage にプロビジョニングする必要があります。  
Samanage の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Samanage 企業サイトに管理者としてログインします。

2. **[Dashboard (ダッシュボード)]** をクリックし、左のナビゲーション ウィンドウで **[Setup (セットアップ)]** を選択します。
   
    ![Setup](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "Setup")

3. **[ユーザー]** タブをクリックします。
   
    ![ユーザー](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_006.png "Users")

4. **[新しいユーザー]**をクリックします。
   
    ![New User](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_007.png "New User")

5. プロビジョニングする Azure Active Directory アカウントの **[Name]\(名前\)** と **[Email Address]\(電子メール アドレス\)** を入力し、**[Create user]\(ユーザーの作成\)** をクリックします。
   
    ![Create User](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_008.png "Create User")
   
   >[!NOTE]
   >Azure Active Directory アカウント所有者がメールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。 Samanage から提供されている他の Samanage ユーザー アカウント作成ツールまたは API を使用して、Azure Active Directory ユーザー アカウントをプロビジョニングできます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Samanage へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を Samanage に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Samanage]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Samanage] タイルをクリックすると、自動的に Samanage アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_203.png



---
title: "チュートリアル: Azure Active Directory と ITRP の統合 | Microsoft Docs"
description: "Azure Active Directory と ITRP の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: fae1c7b6b0e04c1e23123d3aee7913cb3131e645
ms.contentlocale: ja-jp
ms.lasthandoff: 07/04/2017


---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>チュートリアル: Azure Active Directory と ITRP の統合

このチュートリアルでは、ITRP と Azure Active Directory (Azure AD) を統合する方法について説明します。

ITRP と Azure AD の統合には、次の利点があります。

- ITRP にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に ITRP にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

ITRP と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- ITRP でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの ITRP の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-itrp-from-the-gallery"></a>ギャラリーからの ITRP の追加
Azure AD への ITRP の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ITRP を追加する必要があります。

**ギャラリーから ITRP を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに「**ITRP**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_search.png)

5. 結果ウィンドウで **[ITRP]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、ITRP で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する ITRP ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと ITRP の関連ユーザーの間で、リンク関係が確立されている必要があります。

ITRP で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

ITRP で Azure AD のシングル サインオンを構成してテストするには、次の一連の作業を完了させる必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[ITRP テスト ユーザーの作成](#creating-an-itrp-test-user)** - ITRP で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、ITRP アプリケーションでシングル サインオンを構成します。

**ITRP で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **ITRP** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_samlbase.png)

3. **[ITRP Domain and URLs]\(ITRP のドメインと URL\)** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_url.png)

    a. **[サインオン URL]** ボックスに、`https://<tenant-name>.itrp.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<tenant-name>.itrp.com` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[ITRP クライアント サポート チーム](https://www.itrp.com/support)に問い合わせてください。 
 
4. **[SAML 署名証明書]** セクションで、証明書の **[拇印]** の値をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-itrp-tutorial/tutorial_general_400.png)

6. **[ITRP Configuration]\(ITRP 構成\)** セクションで、**[Configure ITRP]\(ITRP を構成する\)** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス] セクション**から、**SAML シングル サインオン サービス URL と サインアウト URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_configure.png) 

7. 別の Web ブラウザー ウィンドウで、ITRP 企業サイトに管理者としてログインします。

8. 上部のツールバーで **[設定]**をクリックします。
   
    ![ITRP](./media/active-directory-saas-itrp-tutorial/ic775570.png "ITRP")

8. 左側のナビゲーション ウィンドウで、 **[シングル サインオン]**を選択します。
   
    ![シングル サインオン](./media/active-directory-saas-itrp-tutorial/ic775571.png "Single Sign-On")

9. [シングル サインオン] 構成セクションで、次の手順を実行します。
   
    ![シングル サインオン](./media/active-directory-saas-itrp-tutorial/ic775572.png "Single Sign-On")
    
    ![シングル サインオン](./media/active-directory-saas-itrp-tutorial/ic775573.png "Single Sign-On")   

    a. **[有効]**をクリックします。

    b. **[Remote Log Out URL]\(リモート ログアウト URL\)** ボックスに、Azure Portal からコピーした**サインアウト URL** の値を貼り付けます。

    c. **[SAML SSO URL]** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    d. **[Certificate Fingerprint]\(証明書のフィンガープリント\)** ボックスに、Azure Portal からコピーした証明書の **[拇印]** の値を貼り付けます。 
      
10. [ **Save**] をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-itrp-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-itrp-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-itrp-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-itrp-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-an-itrp-test-user"></a>ITRP テスト ユーザーの作成

Azure AD ユーザーが ITRP にログインできるようにするには、ユーザーを ITRP にプロビジョニングする必要があります。  

ITRP の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **ITRP** テナントにログインします。

2. 上部のツールバーの **[レコード]**をクリックします。
   
    ![管理](./media/active-directory-saas-itrp-tutorial/ic775575.png "Admin")

3. ポップアップ メニューから **[ユーザー]**を選択します。
   
    ![ユーザー](./media/active-directory-saas-itrp-tutorial/ic775587.png "People")

4. **[新しいユーザーの追加]** (“+”) をクリックします。
   
    ![管理](./media/active-directory-saas-itrp-tutorial/ic775576.png "Admin")

5. [新しいユーザーの追加] ダイアログで、次の手順を実行します。
   
    ![ユーザー](./media/active-directory-saas-itrp-tutorial/ic775577.png "User") 
      
    a. プロビジョニングする有効な AAD アカウントの**名前**と**メール**を入力します。

    b. [ **Save**] をクリックします。

>[!NOTE]
>ITRP から提供されている他の ITRP ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に ITRP へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**ITRP に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[ITRP]\(ITRP\)** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで ITRP のタイルをクリックすると、自動的に ITRP アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_203.png



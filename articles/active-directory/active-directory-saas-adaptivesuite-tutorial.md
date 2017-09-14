---
title: "チュートリアル: Azure Active Directory と Adaptive Suite の統合 | Microsoft Docs"
description: "Azure Active Directory と Adaptive Suite の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 5d7ba2f4c7d814e3aaa1bf804ddc5030380ccb2d
ms.contentlocale: ja-jp
ms.lasthandoff: 05/27/2017


---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-suite"></a>チュートリアル: Azure Active Directory と Adaptive Suite の統合

このチュートリアルでは、Adaptive Suite と Azure Active Directory (Azure AD) を統合する方法について説明します。

Adaptive Suite と Azure AD の統合には、次の利点があります。

- Adaptive Suite にアクセスするユーザーを Azure AD で管理できます
- ユーザーが自分の Azure AD アカウントで自動的に Adaptive Suite にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Adaptive Suite と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Adaptive Suite でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Adaptive Suite の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-adaptive-suite-from-the-gallery"></a>ギャラリーからの Adaptive Suite の追加
Azure AD への Adaptive Suite の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Adaptive Suite を追加する必要があります。

**ギャラリーから Adaptive Suite を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. [検索] ボックスに、「**Adaptive Suite**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_search.png)

5. 結果ウィンドウで **[Adaptive Suite]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Adaptive Suite で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Adaptive Suite ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Adaptive Suite の関連ユーザーの間で、リンク関係が確立されている必要があります。

Adaptive Suite で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Adaptive Suite で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Adaptive Suite テスト ユーザーの作成](#creating-an-adaptive-suite-test-user)** - Azure AD でのユーザーにリンクされた、Adaptive Suite での Britta Simon の対応するユーザーを作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure ポータルで Azure AD のシングル サインオンを有効にして、Adaptive Suite アプリケーションでシングル サインオンを構成します。

**Adaptive Suite で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure ポータルの **Adaptive Suite** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_samlbase.png)

3. **[Adaptive Suite のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_url.png)

    **[応答 URL]** ボックスに、`https://login.adaptiveinsights.com:443/samlsso/<unique-id>` のパターンを使用して URL を入力します。

    >[!NOTE]
    > この値は、Adaptive Suite の **[SAML SSO 設定]** ページから取得できます。
    >  

4. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_400.png)

6. **[Adaptive Suite]** セクションで、**[Adaptive Suite の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML エンティティ ID と SAML シングル サインオン サービス URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_configure.png) 

7. 別の Web ブラウザーのウィンドウで、管理者として Adaptive Suite 企業サイトにログインします。

8. **[Admin]**に移動します。
   
    ![管理者](./media/active-directory-saas-adaptivesuite-tutorial/IC805644.png "Admin")

9. **[ユーザーとロール]** セクションで、**[SAML SSO 設定の管理]** をクリックします。
   
    ![SAML SSO 設定の管理](./media/active-directory-saas-adaptivesuite-tutorial/IC805645.png "Manage SAML SSO Settings")

10. **[SAML SSO 設定]** ページで、次の手順を実行します。
   
    ![SAML SSO 設定](./media/active-directory-saas-adaptivesuite-tutorial/IC805646.png "SAML SSO Settings")

    a. **[ID プロバイダー名]** テキスト ボックスに、構成の名前を入力します。
    
    b. Azure ポータルからコピーした **SAML エンティティ ID** を **[Identity provider entity ID]\(ID プロバイダーのエンティティ ID\)** ボックスに貼り付けます。
  
    c. Azure ポータルからコピーした **SAML シングル サインオン サービス URL** を **[Identity provider SSO URL]\(ID プロバイダーの SSO URL\)** ボックスに貼り付けます。
  
    d. Azure ポータルからコピーした **SAML シングル サインオン サービス URL** を **[Custom logout URL]\(カスタム ログアウト URL\)** ボックスに貼り付けます。
  
    e. ダウンロードした証明書をアップロードするには、 **[ファイルの選択]**をクリックします。
  
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 次のように選択します。
    * **[SAML ユーザー ID]** では、**[ユーザーの Adaptive Insights ユーザー名]** を選択します。
    * **[SAML ユーザー ID の場所]** では、**[サブジェクトの NameID 内のユーザー ID ]** を選択します。
    * **[SAML NameID 形式]** では、**[電子メール アドレス]** を選択します。
    * **[SAML を有効にする]** では、**[SAML SSO を許可して、Adaptive Insights に直接ログインする]** を選択します。
    
    g. [ **Save**] をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-an-adaptive-suite-test-user"></a>Adaptive Suite のテスト ユーザーの作成

Azure AD ユーザーが Adaptive Suite にログインできるようにするには、ユーザーを Adaptive Suite にプロビジョニングする必要があります。  

* Adaptive Suite の場合、プロビジョニングは手動で実行します。

**ユーザー プロビジョニングを構成するには、次の手順に従います。** 

1. **Adaptive Suite** 企業サイトに管理者としてログインします。
2. **[Admin]**に移動します。
   
   ![管理者](./media/active-directory-saas-adaptivesuite-tutorial/IC805644.png "Admin")
3. **[ユーザーとロール]** セクションで **[ユーザーの追加]** をクリックします。
   
   ![ユーザーの追加](./media/active-directory-saas-adaptivesuite-tutorial/IC805648.png "Add User")
4. **[新しいユーザー]** セクションで、次の手順に従います。
   
   ![送信](./media/active-directory-saas-adaptivesuite-tutorial/IC805649.png "Submit")   

   a. 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory ユーザーの **[名前]**、**[ログイン]**、**[電子メール]**、**[パスワード]** を入力します。
  
   b. **[ロール]**を選択します。
  
   c. **[Submit]**をクリックします。

>[!NOTE]
>他の Adaptive Suite ユーザー アカウント作成ツールまたは Adaptive Suite から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
>  

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Adaptive Suite へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Adaptive Suite に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Adaptive Suite]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Microsoft Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで [Adaptive Suite] タイルをクリックすると、自動的に Adaptive Suite アプリケーションにサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_203.png



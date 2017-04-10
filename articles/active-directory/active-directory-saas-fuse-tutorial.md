---
title: "チュートリアル: Azure Active Directory と Fuse の統合 | Microsoft Docs"
description: "Azure Active Directory と Fuse の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 5ef34f58-863a-4b37-875c-e8efa3e18bb3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: cb7a8a505122f0b6d2e5744c31a48018c2c05351
ms.openlocfilehash: 10a51c8b272a8e8460079f8beb8a23dc40d3701d


---
# <a name="tutorial-azure-active-directory-integration-with-fuse"></a>チュートリアル: Azure Active Directory と Fuse の統合

このチュートリアルでは、Fuse と Azure Active Directory (Azure AD) を統合する方法について説明します。

Fuse と Azure AD の統合には、次の利点があります。

- Fuse にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Fuse にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure 管理ポータル) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Fuse と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Fuse でのシングル サインオンが有効なサブスクリプション


> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の評価版を入手できます。


## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの Fuse の追加
2. Azure AD シングル サインオンの構成とテスト


## <a name="adding-fuse-from-the-gallery"></a>ギャラリーからの Fuse の追加
Azure AD への Fuse の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Fuse を追加する必要があります。

**ギャラリーから Fuse を追加するには、次の手順に従います。**

1. **[Azure 管理ポータル](https://portal.azure.com)**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. ダイアログの上部にある **[追加]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「 **Fuse**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_001.png)

5. 結果ウィンドウで **[Fuse]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Fuse で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Fuse ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Fuse の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Fuse の **[Username]** の値として割り当てます。

Fuse で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Fuse テスト ユーザーの作成](#creating-a-fuse-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Fuse で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure 管理ポータルで Azure AD のシングル サインオンを有効にして、Fuse アプリケーションでシングル サインオンを構成します。

**Fuse で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure 管理ポータルの **Fuse** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure Single Sign-On](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_01.png)

3. **[Fuse のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure Single Sign-On](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_02.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[サインオン URL]** ボックスに、`https://<tenant name>.fusion-universal.com/` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<tenant name>.fusion-universal.com` の形式で URL を入力します。

    > [!NOTE] 
    > これは実際の値ではないので注意してください。 実際のサインオン URL と識別子でこれらの値を更新する必要があります。 これらの値を取得するには、[Fuse サポート チーム](mailto:support@fusion-universal.com)に問い合わせてください。 

4. **[SAML 署名証明書**] セクションで、**[新しい証明書の作成]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_03.png)   

5. **[新しい証明書の作成]**ダイアログ ボックスで、カレンダー アイコンをクリックし、**期限日**を選択します。 **[保存]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-fuse-tutorial/tutorial_general_300.png)

6. **[SAML 署名証明書]** セクションで、**[Make new certificate active (新しい証明書を有効にする)]** をクリックし、**[保存]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_04.png)

7. ポップアップ表示される **[Rollover certificate (ロール オーバー証明書)]** ウィンドウで、**[OK]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-fuse-tutorial/tutorial_general_400.png)

8. **[SAML 署名証明書]** セクションで、**[Certificate (Raw) (証明書 (Raw))]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_05.png) 

9. **[Fuse Configuration (Fuse 構成)]** セクションで、**[Configure Fuse (Fuse を構成する)]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_06.png) 

    ![[シングル サインオンの構成]](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_07.png)

10. アプリケーション用に構成された SSO を入手するために、[Fuse サポート チーム](mailto:support@fusion-universal.com)に連絡し、次のものを情報として提供します。 

    •  ダウンロードした**証明書ファイル**

    •  **SAML シングル サインオン サービス URL**

    •  **SAML エンティティ ID**

    •  **サインアウト URL**
  

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure 管理ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-fuse-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-fuse-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-fuse-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-fuse-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。 



### <a name="creating-a-fuse-test-user"></a>Fuse テスト ユーザーの作成

このセクションでは、Fuse で Britta Simon というユーザーを作成します。 [Fuse サポート チーム](mailto:support@fusion-universal.com)と連携し、Fuse プラットフォームにユーザーを追加してください。


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Fuse へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Fuse に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure 管理ポータルでアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Fuse]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_50.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** をクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** をクリックします。
    


### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Fuse] タイルをクリックすると、自動的に Fuse アプリケーションにサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_203.png


<!--HONumber=Jan17_HO4-->



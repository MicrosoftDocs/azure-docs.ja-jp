---
title: "チュートリアル: Azure Active Directory と Atlassian Cloud の統合 | Microsoft Docs"
description: "Azure Active Directory と Atlassian Cloud の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
editor: na
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6d53de158a44116058ab8241d5182414f8f306fb
ms.openlocfilehash: 243e81ecd1e1648bfd6b2ca6e6268ea1a18d2036
ms.lasthandoff: 02/16/2017


---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>チュートリアル: Azure Active Directory と Atlassian Cloud の統合

このチュートリアルでは、Atlassian Cloud と Azure Active Directory (Azure AD) を統合する方法について説明します。

Atlassian Cloud と Azure AD の統合には、次の利点があります。

- Atlassian Cloud にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Atlassian Cloud にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Atlassian Cloud と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Atlassian Cloud でのシングル サインオンが有効なサブスクリプション


>[!NOTE] 
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。


## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Atlassian Cloud の追加
2. Azure AD シングル サインオンの構成とテスト


## <a name="adding-atlassian-cloud-from-the-gallery"></a>ギャラリーからの Atlassian Cloud の追加
Azure AD への Atlassian Cloud の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Atlassian Cloud を追加する必要があります。

**ギャラリーから Atlassian Cloud を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![[アプリケーション]][2]

4. ページの下部にある **[追加]** をクリックします。

    ![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![アプリケーション][4]

6. 検索ボックスに、「**Atlassian Cloud**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_01.png)

7. 結果ウィンドウで **[Atlassian Cloud]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Atlassian Cloud で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Atlassian Cloud ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Atlassian Cloud の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Atlassian Cloud の **[Username]** の値として割り当てます。

Atlassian Cloud での Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Atlassian Cloud のテスト ユーザーの作成](#creating-Atlassian-cloud-test-user)** - Atlassian Cloud で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、Atlassian Cloud アプリケーションでシングル サインオンを構成します。


**Atlassian Cloud との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. クラシック ポータルの **Atlassian Cloud** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
     
    ![[シングル サインオンの構成]][6] 

2. **[ユーザーの Atlassian Cloud へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_03.png) 

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_04.png) 

    a. **[サインオン URL]** ボックスに、ユーザーが Atlassian Cloud アプリケーションにサインオンするために使用する URL を、次の形式で入力します。`https://<instancename>.atlassian.net`
    
    b. **[識別子]** ボックスに、URL を次のパターンで入力します。`https://id.atlassian.com/login`

    >[!NOTE] 
    >**[識別子]**の正確な値は、[Atlassian Cloud SAML 構成] 画面で取得できます。

    c. **[次へ]** をクリックします。
 
4. **[Atlassian Cloud でのシングル サインオンの構成]** ページで、次の手順を実行します。

    ![シングル サインオンの構成](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_05.png)

    a. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。

    b. **[次へ]**をクリックします。


5. アプリケーションの SSO を構成するには、管理者権限で Atlassian ポータルにログインします。

6. 左側のナビゲーションの [Authentication] セクションで、**[Domains]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)

    a. ボックスにドメイン名を入力し、**[Add domain]** をクリックします。
        
    ![[シングル サインオンの構成]](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_07.png)

    b. ドメインを検証するために、**[Verify]** をクリックします。 

    ![[シングル サインオンの構成]](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_08.png)

    c. ドメイン検証 html ファイルをダウンロードし、ドメインの Web サイトのルート フォルダーにアップロードします。次に、**[Verify domain]** をクリックします。
    
    ![Configure Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_09.png)

    d. ドメインの検証が終わると、**[Stauts]** フィールドの値が **[Verified]** になります。

    ![シングル サインオンの構成](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_10.png)

7. 左側のナビゲーション バーで、**[SAML]** をクリックします。
 
    ![シングル サインオンの構成](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

8. 新しい SAML 構成を作成し、ID プロバイダー構成を追加します。

    a. Azure AD からエンティティ ID の値をコピーし、[ID プロバイダー エンティティ ID] フィールドに貼り付けます。
    
    b. SAML SSO URL をコピーし、[ID プロバイダー SSO URL] ボックスに貼り付けます。

    c. Azure AD からダウンロードした証明書をメモ帳で開き、Begin 行と End 行以外の値をコピーし、[公開 X509 証明書] ボックスに貼り付けます。
    
    d. 設定を保存します。

    ![シングル サインオンの構成](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)
  
9. Azure AD 設定を更新し、適切な識別子 URL が設定されていることを確認します。

    a. [SAML] 画面から SP 識別 ID をコピーし、Azure AD に **[識別子]** の値として貼り付けます。

    b. サインオン URL は、Atlassian Cloud のテナント URL です。     

    ![シングル サインオンの構成](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)
    
10. クラシック ポータルで、シングル サインオンの構成確認を選択し、**[次へ]**をクリックします。
    
    ![Azure AD のシングル サインオン][10]

7. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
 
    ![Azure AD のシングル サインオン][11]


### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。


![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_09.png) 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png) 

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png) 

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。 ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_05.png) 

    a. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。

    c. ページの下部にある **[次へ]**」を参照してください。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_06.png) 

    a. **[名]** ボックスに「**Britta**」と入力します。  

    b. **[姓]** ボックスに「**Simon**」と入力します。

    c. **[表示名]** ボックスに「**Britta Simon**」と入力します。

    d. **[ロール]** 一覧で **[ユーザー]** を選択します。

    e. **[次へ]**をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_07.png) 

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_08.png) 

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[新しいパスワード]** の値を書き留めます。

    b. ページの下部にある **[完了]**」を参照してください。   



### <a name="creating-an-atlassian-cloud-test-user"></a>Atlassian Cloud のテスト ユーザーの作成


このセクションでは、Atlassian Cloud で Britta Simon というユーザーを作成します。 そのユーザーは、シングル サインオンを実行する前に Atlassian Cloud に存在することが重要です。 Atlassian Cloud インスタンスに管理者権限でログインし、次の手順を実行します。

>[!NOTE] 
>[Users (ユーザー)] セクションの **[Bulk Create (一括作成)]** ボタンをクリックして、ユーザーを作成することもできます。

1. [Site administration (サイト管理)] セクションで、**[Users (ユーザー)]** ボタンをクリックします。

    ![Atlassian Cloud ユーザーの作成](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. **[Create User (ユーザーの作成)]** ボタンをクリックして、Atlassian Cloud にユーザーを作成します。

    ![Atlassian Cloud ユーザーの作成](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. ユーザーの電子メール アドレス、ユーザー名、フルネームを入力し、アプリケーションへのアクセスを割り当てます。 

    ![Atlassian Cloud ユーザーの作成](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. **[Create user (ユーザーの作成)]** ボタンをクリックします。これで、招待状が電子メールでユーザーに送信され、招待状を受け取ったユーザーがシステムをアクティブ化します。 


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Atlassian Cloud へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Atlassian Cloud に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Atlassian Cloud]** を選択します。

    ![シングル サインオンの構成](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_50.png) 

3. 上部のメニューで **[ユーザー]**をクリックします。

    ![ユーザーの割り当て][203]

4. ユーザーの一覧で **[Britta Simon]**を選択します。

5. 下部にあるツール バーで **[割り当て]**をクリックします。

    ![ユーザーの割り当て][205]


### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Atlassian Cloud] タイルをクリックすると、自動的に Atlassian Cloud アプリケーションにサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_205.png


---
title: "チュートリアル: Azure Active Directory と Jive の統合 | Microsoft Docs"
description: "Azure Active Directory と Jive の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 9fc5659a-c116-4a1b-a601-333325a26b46
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6a01f53ce05aa8084f0a18e56714b1790cfce912
ms.openlocfilehash: 304ee87e82ce5acc4479fd16d3ac1aa340e42815
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-jive"></a>チュートリアル: Azure Active Directory と Jive の統合
このチュートリアルでは、Jive と Azure Active Directory (Azure AD) を統合する方法について説明します。

Jive と Azure AD の統合には、次の利点があります。

* Jive にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Jive にサインオン (シングル サインオン) できるようにします。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Jive と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* Jive でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
> 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの Jive の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-jive-from-the-gallery"></a>ギャラリーからの Jive の追加
Azure AD への Jive の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Jive を追加する必要があります。

**ギャラリーから Jive を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **Jive**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-jive-tutorial/tutorial_jive_01.png)
7. 結果ウィンドウで **[Jive]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-jive-tutorial/tutorial_jive_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Jive で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Jive ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Jive 内の対応しているユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Jive の **[Username]** の値として割り当てます。

Jive で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Jive テスト ユーザーの作成](#creating-a-jive-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Jive で作成します。
4. **[ユーザー プロビジョニングの構成](#configuring-user-provisioning)** - Active Directory のユーザー アカウントのプロビジョニングを Jive に対して有効にする方法を説明します。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
6. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、Jive アプリケーションでシングル サインオンを構成することです。

**Jive で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. クラシック ポータルの **Jive** アプリケーション統合のページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![Configure Single Sign-On][6] 
2. **[ユーザーの Jive へのアクセスを設定してください]** ページで、**[Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-jive-tutorial/tutorial_jive_03.png) 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-jive-tutorial/tutorial_jive_04.png) 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[サインオン URL]** ボックスに、**https://\<<顧客名>\>.jivecustom.com** のパターンを使用して、ユーザーが Jive アプリケーションへのサインオンに使用する URL を入力します。
   
    b. click **[次へ]**
4. **[Jive でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-jive-tutorial/tutorial_jive_05.png)
   
    a. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。
   
    b. ページの下部にある **[次へ]**」を参照してください。
5. Jive テナントに管理者としてサインオンします。
6. 上部のメニューで、**[SAML]**をクリックします。
   
    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-jive-tutorial/tutorial_jive_002.png)
   
    a. **[Genaral (全般)]** タブで **[Enabled (有効)]** を選択します。
   
    b. **[Save all saml settings (すべての SAML 設定を保存)]** ボタンをクリックします。
7. **[Idp Metadata (IDP メタデータ)]**タブに移動します。
   
    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-jive-tutorial/tutorial_jive_003.png)
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 ダウンロードしたメタデータ XML ファイルの内容をコピーし、**[Identity Provider (IDP) Metadata (ID プロバイダー (IDP) のメタデータ)]** ボックスに貼り付けます。
   
    b. **[Save all saml settings (すべての SAML 設定を保存)]** ボタンをクリックします。 
8. **[User Attribute Mapping (ユーザー属性のマッピング)]**タブに移動します。
   
    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-jive-tutorial/tutorial_jive_004.png)
   
    a. **[Email (電子メール)]** ボックスに、**mail** 値の属性名をコピーして貼り付けます。
   
    b. **[First Name (名)]** ボックスに、**givenname** 値の属性名をコピーして貼り付けます。
   
    c. **[Last Name (姓)]** ボックスに、**surname** 値の属性名をコピーして貼り付けます。
9. Azure AD ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   ![Azure AD Single Sign-On][10]
10. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
    ![Azure AD Single Sign-On][11]

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-jive-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-jive-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-jive-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。 ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-jive-tutorial/create_aaduser_05.png) 
   
    a. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   
    b. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
   
    c. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-jive-tutorial/create_aaduser_06.png) 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[名]** ボックスに「**Britta**」と入力します。  
   
    b. **[姓]** ボックスに「**Simon**」と入力します。
   
    c. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   
    d. **[ロール]** 一覧で **[ユーザー]** を選択します。
   
    e. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-jive-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-jive-tutorial/create_aaduser_08.png) 
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[新しいパスワード]** の値を書き留めます。
   
    b. ページの下部にある **[完了]**」を参照してください。   

### <a name="creating-a-jive-test-user"></a>Jive テスト ユーザーの作成
このセクションでは、Jive で Britta Simon というユーザーを作成します。 Jive サポート チームと連携し、Jive プラットフォームにユーザーを追加してください。

### <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成
このセクションでは、Active Directory のユーザー アカウントのプロビジョニングを Jive に対して有効にする方法を説明します。  
この手順の一環として、Jive.com からの要求に必要なユーザー セキュリティ トークンを指定する必要があります。

次のスクリーン ショットは、Azure AD の関連ダイアログの例を示しています。

![ユーザー プロビジョニングの構成](./media/active-directory-saas-jive-tutorial/IC698794.png "ユーザー プロビジョニングの構成")

#### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>ユーザー プロビジョニングを構成するには、次の手順に従います。
1. Microsoft Azure 管理ポータルの **Jive** アプリケーション統合ページで、**[ユーザー プロビジョニングの構成]** をクリックして **[ユーザー プロビジョニングの構成]** ダイアログを開きます。
2. **[自動ユーザー プロビジョニングを有効にするための Jive の資格情報を入力してください]** ページで、以下の構成設定を入力します。
   
    a. **[JIVE 管理ユーザー名]** テキストボックスに、Jive.com の**システム管理者**プロファイルが割り当てられている Jive アカウント名を入力します。
   
    b. **[JIVE 管理パスワード]** テキストボックスに、このアカウントのパスワードを入力します。
   
    c. **[JIVE テナント URL]** テキストボックスに、Jive テナント URL を入力します。
      
      > [!NOTE]
      > Jive テナント URL は、組織が Jive にログインするために使用する URL です。  
      > 通常、URL の形式は **www.\<<組織名>\>.jive.com** のようになります。
      > 
      > 
   
    d. **[検証]** をクリックして構成を確認します。

    e. **[次へ]** をクリックして、**[確認]** ページを開きます。

3. **[確認]** ページで、チェックマークをクリックして構成を保存します。

ここで、テスト アカウントを作成して 10 分間待機し、アカウントが Jive.com と同期されていることを確認します。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に Jive へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようします。

![ユーザーの割り当て][200] 

**Jive に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[Jive]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-jive-tutorial/tutorial_jive_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203]
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト
このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Jive] タイルをクリックすると、自動的に Jive アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-jive-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jive-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jive-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jive-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-jive-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-jive-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-jive-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-jive-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jive-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jive-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-jive-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-jive-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-jive-tutorial/tutorial_general_205.png


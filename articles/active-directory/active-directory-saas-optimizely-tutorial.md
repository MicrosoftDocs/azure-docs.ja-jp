---
title: "チュートリアル: Azure Active Directory と Optimizely の統合 | Microsoft Docs"
description: "Azure Active Directory と Optimizely の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 1d9aa0e5da9b0bd659b8057739c81b318023cf44
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>チュートリアル: Azure Active Directory と Optimizely の統合
このチュートリアルでは、Optimizely と Azure Active Directory (Azure AD) を統合する方法について説明します。

Optimizely と Azure AD の統合には、次の利点があります。

* Optimizely にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが各自の Azure AD アカウントで Optimizely に自動的にシングル サインオン (SSO) するように、設定が可能です。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Azure AD と Optimizely の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* **Optimizely** での SSO が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
> 


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD SSO をテストします。 

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Optimizely の追加
2. Azure AD SSO の構成とテスト

## <a name="add-optimizely-from-the-gallery"></a>ギャラリーからの Optimizely の追加
Azure AD への Optimizely の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Optimizely を追加する必要があります。

**ギャラリーから Optimizely を追加するには、次の手順を実行します。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]

6. 検索ボックスに、「 **Optimizely**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_01.png)

7. 結果ウィンドウで **[Optimizely]** を選び、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_02.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Optimizely で Azure AD の SSO を構成し、テストします。

SSO を機能させるには、Azure AD ユーザーに対応する Optimizely ユーザーが Azure AD で認識されている必要があります。 つまり、Azure AD ユーザーと Optimizely の関連ユーザーの間でリンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、Optimizely の **[Username]** の値として割り当てることで確立されます。

Optimizely で Azure AD の SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Optimizely のテスト ユーザーの作成](#creating-an-optimizely-test-user)** - Optimizely で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションの目的は、Azure クラシック ポータルで Azure AD の SSO を有効にすることと、Optimizely アプリケーションで SSO を構成することです。

Optimizely アプリケーションでは、SAML アサーションに、"email" という名前の属性を含めます。 "email" の値は、Azure AD によって認証を取得できる Optimizely 認識対象の電子メールにする必要があります。 このアプリケーションには、"email" の要求を構成してください。 

この属性の値は、アプリケーションの **[属性]** タブから管理できます。 次のスクリーンショットはその例です。 

![[シングル サインオンの構成]](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_03.png) 

**Optimizely で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure クラシック ポータルの **Optimizely** アプリケーション統合ページで、上部のメニューから **[属性]** をクリックします。
   
    ![[シングル サインオンの構成]][5]

2. [Saml トークン属性] ダイアログで、"email" 属性を追加します。
  1. **[ユーザー属性の追加]** をクリックして **[ユーザー属性の追加]** ダイアログを開きます。 
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_05.png)
  2. **[属性名]** ボックスに、属性名 "email" を入力します。
  3. **[属性値]** リストから、属性値 "userprincipalname" または Azure AD および Optimizely で認識される電子メールを含む任意の値を選択します。
  4. ページの下部にある **[完了]**」を参照してください。

3. 上部のメニューで **[クイック スタート]**をクリックします。
   
    ![[シングル サインオンの構成]][6]

4. クラシック ポータルの **Optimizely** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][7] 

5. **[ユーザーの Optimizely へのアクセスを設定してください]** ページで、**[Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_06.png)

6. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。 
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)
  1. **[サインオン URL]** ボックスに、「`https://app.optimizely.net/contoso`」と入力します。
  2. **[識別子]** ボックスに、「`urn:auth0:optimizely:contoso`」と入力します。
  3. **[次へ]**をクリックします。 

     >[!NOTE] 
     >**[サインオン URL]** と **[識別子]** の値は、実際の値の単なるプレースホルダーです。 このチュートリアルの後半で Optimizely から実際の値を取得するための手順を確認できます。
     >

1. **[Optimizely でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_08.png)
 1. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。
 2. **[シングル サインオン サービス URL]** をコピーします。

2. アプリケーション用に構成された SSO を入手するには、Optimizely アカウント マネージャーに連絡し、次の情報を提供してください。
   
  * ダウンロードした証明書 
  * シングル サインオン サービス URL
     
  電子メールに応じて、Optimizely では、[サインオン URL] \(SP によって開始された SSO) と [識別子] \(サービス プロバイダーのエンティティ ID) の値が提供されます。

3. **[アプリケーション設定の構成]** ダイアログ ページに戻り、次の手順を実行します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png) 
 1. **[サインオン URL]** ボックスに、Optimizely が提供する **SP によって開始された SSO の URL** を入力します。  
 2. **[識別子]** ボックスに、Optimizely が提供する**サービス プロバイダーのエンティティ ID** を入力します。  
 3. **[次へ]**をクリックします。

4. **[Optimizely でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![Azure AD のシングル サインオン][10] 
 1. シングル サインオンの構成確認を選択します。  
 2. **[次へ]**をクリックします。

5. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

6. 別のブラウザー ウィンドウで、Optimizely アプリケーションにサインオンします。

7. 右上隅のアカウント名をクリックし、 **[アカウント設定]**に移動します。
   
    ![Azure AD のシングル サインオン](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_09.png)

8. [アカウント] タブで、**[概要]** セクションの [シングル サインオン] にある **[Enable SSO (SSO の有効化)]** チェック ボックスをオンにします。
   
    ![Azure AD のシングル サインオン](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_10.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。

* ユーザーの一覧で **[Britta Simon]**を選択します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-optimizely-tutorial/create_aaduser_09.png) 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-optimizely-tutorial/create_aaduser_03.png) 

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-optimizely-tutorial/create_aaduser_04.png) 

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-optimizely-tutorial/create_aaduser_05.png)  
 1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。 
 2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。 
 3. **[次へ]**をクリックします。

6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-optimizely-tutorial/create_aaduser_06.png)  
 1. **[名]** ボックスに「**Britta**」と入力します。   
 2. **[姓]** ボックスに「**Simon**」と入力します。 
 3. **[表示名]** ボックスに「**Britta Simon**」と入力します。  
 4. **[ロール]** 一覧で **[ユーザー]** を選択します。 
 5. **[次へ]**をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-optimizely-tutorial/create_aaduser_07.png) 

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-optimizely-tutorial/create_aaduser_08.png)  
 1. **[新しいパスワード]** の値を書き留めます。  
 2. **[完了]** をクリックします。   

### <a name="create-an-optimizely-test-user"></a>Optimizely のテスト ユーザーの作成
このセクションでは、Optimizely で Britta Simon というユーザーを作成します。

1. ホーム ページで、**[コラボレーター]** タブを選択します。

2. **[New Collaborator (新しいコラボレーター)]** クリックし、新しいコラボレーターをプロジェクトに追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-optimizely-tutorial/create_aaduser_10.png)

3. 電子メール アドレスを入力して、役割を割り当てます。 **[招待]**をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-optimizely-tutorial/create_aaduser_11.png)

1. 電子メールの招待を受信します。 メール アドレスを使用すると、Optimizely にログインする必要があります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に Optimizely へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようします。

![ユーザーの割り当て][200] 

**Britta Simon を Optimizely に割り当てるには、次の手順を実行します。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Optimizely]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_50.png) 

3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 

4. [すべてのユーザー] の一覧で **[Britta Simon]**を選択します。

5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで [Optimizely] タイルをクリックすると、Optimizely アプリケーションに自動的にサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-optimizely-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_205.png


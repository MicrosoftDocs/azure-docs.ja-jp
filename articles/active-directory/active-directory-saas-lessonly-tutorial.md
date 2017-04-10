---
title: "チュートリアル: Azure Active Directory と Lesson.ly の統合 | Microsoft Docs"
description: "Azure Active Directory と Lesson.ly の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 8c9dc6e6-5d85-4553-8a35-c7137064b928
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a14be44b66c5e61350785ddfaf5b96125a196103
ms.openlocfilehash: 242cc7d58acb4af66d7ea5da4502db4ce2b414c6
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-lessonly"></a>チュートリアル: Azure Active Directory と Lesson.ly の統合
このチュートリアルの目的は、Lesson.ly と Azure Active Directory (Azure AD) を統合する方法を説明することです。

Lesson.ly と Azure AD の統合には、次の利点があります。

* Lesson.ly にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが各自の Azure AD アカウントで Lesson.ly に自動的にサインオン (シングル サインオン) できるようにします。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Azure AD と Lesson.ly の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* Lesson.ly でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
> 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。 

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの Lesson.ly の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-lessonly-from-the-gallery"></a>ギャラリーからの Lesson.ly の追加
Azure AD への Lesson.ly の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Lesson.ly を追加する必要があります。

**ギャラリーから Lesson.ly を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **Lesson.ly**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_01.png)
7. 結果ウィンドウで **[Lesson.ly]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Lesson.ly で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Lesson.ly ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Lesson.ly の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Lesson.ly の **[Username]** の値として割り当てます。

Lesson.ly で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Lesson.ly テスト ユーザーの作成](#creating-a-Lessonly-test-user)** - Lesson.ly で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Lesson.ly に対する認証を行えるようにする方法を説明します。

Lesson.ly アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを **SAML トークン属性** の構成に追加する必要があります。
次のスクリーンショットはその例です。

![[シングル サインオンの構成]](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_06.png) 

**Lesson.ly で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの Lesson.ly アプリケーション統合ページで、上部のメニューの **[属性]** をクリックして、**[SAML トークン属性]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_07.png) 
2. 必要な属性のマッピングを追加するには、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_08.png) 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 上記の表の各データ行で、 **[ユーザー属性の追加]**をクリックします。
   
    b. **[属性名]** ボックスに、その行に対して表示される属性名を入力します。
   
    c. **[属性値]** リストから、その行に対して表示される属性値を選択します。
   
    d. ページの下部にある **[完了]**
3. **[変更の適用]**をクリックします。
4. ブラウザーで、 **[戻る]** をクリックして、[クイック スタート] ダイアログをもう一度開きます。
5. **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![Configure Single Sign-On][6] 
6. **[ユーザーの Lesson.ly へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_03.png) 
7. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_04.png) 

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 [サインオン URL] ボックスに、ユーザーが Lessonly アプリケーションへのサインオンに使用する URL を、"**https://companyname.lesson.ly/signin**" の形式で入力します。 一般名を参照するときは、この **companyname** を実際の名前に置き換える必要があります。


1. **[Lesson.ly でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_05.png) 
   
    a. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。
   
    b. **[次へ]**をクリックします。
2. お使いのアプリケーション用に構成された SSO を取得するために、Lesson.ly のサポート チーム (dev@lessonly.com) に問い合わせます。 Lesson.ly チーム側で SSO を設定する必要があるため、ダウンロードした証明書ファイルをメールに添付して、メタデータ URL (エンティティ ID、SSO サインイン URL、およびサインアウト URL) をチームと共有してください。
3. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
   ![Azure AD のシングル サインオン][10]
4. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
   ![Azure AD のシングル サインオン][11]

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-lessonly-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-lessonly-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-lessonly-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-lessonly-tutorial/create_aaduser_05.png) 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   
    b. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
   
    c. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-lessonly-tutorial/create_aaduser_06.png) 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[名]** ボックスに「**Britta**」と入力します。  
   
    b. **[姓]** ボックスに「**Simon**」と入力します。
   
    c. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   
    d. **[ロール]** 一覧で **[ユーザー]** を選択します。
   
    e. **[次へ]**をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-lessonly-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-lessonly-tutorial/create_aaduser_08.png) 
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[新しいパスワード]** の値を書き留めます。
   
    b. **[完了]** をクリックします。   

### <a name="creating-a-lessonly-test-user"></a>Lesson.ly テスト ユーザーの作成
このセクションの目的は、Lesson.ly で Britta Simon というユーザーを作成することです。 Lesson.ly では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 存在しない Lesson.ly ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。 [Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、Lesson.ly のサポート チームにお問い合わせください。
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に Lesson.ly へのアクセスを許可することによって、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][200] 

**Britta Simon を Lesson.ly に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[Lesson.ly]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで [Lesson.ly] タイルをクリックすると、Lesson.ly アプリケーションに自動的にサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_205.png


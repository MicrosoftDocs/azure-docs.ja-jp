---
title: "チュートリアル: Azure Active Directory と Fieldglass の統合 | Microsoft Docs"
description: "Azure Active Directory と Fieldglass の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 2510195f-d5b1-4684-b3da-283fb8619df2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a14be44b66c5e61350785ddfaf5b96125a196103
ms.openlocfilehash: 6baceea55e9085556c5b1386de842b3e4393c721


---
# <a name="tutorial-azure-active-directory-integration-with-fieldglass"></a>チュートリアル: Azure Active Directory と Fieldglass の統合
このチュートリアルの目的は、Fieldglass と Azure Active Directory (Azure AD) を統合する方法を説明することです。

Fieldglass と Azure AD の統合には、次の利点があります。

* Fieldglass にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Fieldglass にサインオン (シングル サインオン) できるようにします。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Fieldglass と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* Fieldglass でのシングル サインオンが有効なサブスクリプション

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

1. ギャラリーからの Fieldglass の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-fieldglass-from-the-gallery"></a>ギャラリーからの Fieldglass の追加
Azure AD への Fieldglass の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Fieldglass を追加する必要があります。

**ギャラリーから Fieldglass を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「**Fieldglass**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-fieldglass-tutorial/tutorial_fieldglass_01.png)
7. 結果ウィンドウで **[Fieldglass]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![ギャラリーでアプリを選択する](./media/active-directory-saas-fieldglass-tutorial/tutorial_fieldglass_0001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Fieldglass で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Fieldglass ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Fieldglass の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Fieldglass の **[Username (ユーザー名)]** の値として割り当てます。

Fieldglass で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD シングル サインオンをテストします。
3. **[Fieldglass のテスト ユーザーの作成](#creating-a-fieldglass-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Fieldglass で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、Fieldglass アプリケーションでシングル サインオンを構成します。

**Fieldglass で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. クラシック ポータルの **Fieldglass** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 
2. **[ユーザーの Fieldglass へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-fieldglass-tutorial/tutorial_fieldglass_03.png) 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順を実行し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-fieldglass-tutorial/tutorial_fieldglass_04.png)
   
    a. **[識別子]** ボックスに、「`https://www.fieldglass.com`」と入力するか、`https://<company name>.fgvms.com` 形式の URL を入力します。
   
    b. **[応答 URL]** ボックスに、次の形式で URL を入力します。 
   
       - `https://<company name>.fgvms.com/<company name>`
    - `https://www.fieldglass.net/<company name>`
     
    c. **[次へ]**
     
    > [!NOTE]
    > これは実際の値ではないので注意してください。 実際の識別子と応答 URL で値を更新する必要があります。 これらの値の取得については、 [FieldGlass](http://www.fieldglass.com/solutions/support)にお問い合わせください。
    > 
    >
     
4. **[FieldGlass でのシングル サインオンの構成]** ページで、次の手順を実行し、**[次へ]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-fieldglass-tutorial/tutorial_fieldglass_05.png)
   
    a. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。
   
    b. **[次へ]**をクリックします。
5. お使いのアプリケーション用に構成された SSO を取得するには、Fieldglass サポート チームに連絡し、次のものを情報として提供します。 
   
    -**ダウンロードした証明書** ファイル
   
    - **エンティティ ID**
   
    - **シングル サインアウト サービス URL**
6. クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
7. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-fieldglass-tutorial/create_aaduser_09.png)
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-fieldglass-tutorial/create_aaduser_03.png)
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-fieldglass-tutorial/create_aaduser_04.png)
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-fieldglass-tutorial/create_aaduser_05.png)
   
    a. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   
    b. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
   
    c. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-fieldglass-tutorial/create_aaduser_06.png)
   
    a. **[名]** ボックスに「**Britta**」と入力します。  
   
    b. **[姓]** ボックスに「**Simon**」と入力します。
   
    c. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   
    d. **[ロール]** 一覧で **[ユーザー]** を選択します。
   
    e. **[次へ]**をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-fieldglass-tutorial/create_aaduser_07.png)
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-fieldglass-tutorial/create_aaduser_08.png)
   
    a. **[新しいパスワード]** の値を書き留めます。
   
    b. ページの下部にある **[完了]**」を参照してください。   

### <a name="creating-a-fieldglass-test-user"></a>Fieldglass のテスト ユーザーの作成
このセクションの目的は、Britta Simon というユーザーを Fieldglass で作成することです。Fieldglass サポート チームと連携し、Fieldglass アカウントにユーザーを追加してください。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に Fieldglass へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][200]

**Fieldglass に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201]
2. アプリケーションの一覧で **[Fieldglass]** を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-fieldglass-tutorial/tutorial_fieldglass_50.png)
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203]
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで [Fieldglass] タイルをクリックすると、自動的に Fieldglass アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO3-->



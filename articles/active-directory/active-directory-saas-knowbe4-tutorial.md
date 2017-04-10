---
title: "チュートリアル: Azure Active Directory と KnowBe4 の統合 | Microsoft Docs"
description: "Azure Active Directory と KnowBe4 の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: b80d2212-cc5f-4adb-836c-570640810c39
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ed2fc2b34ff10acc806daec84986f8db58e713c3
ms.openlocfilehash: bc7ce4867180dd9f896b894b56a02bf033ed6bbb
ms.lasthandoff: 02/17/2017


---

# <a name="tutorial-azure-active-directory-integration-with-knowbe4"></a>チュートリアル: Azure Active Directory と KnowBe4 の統合
このチュートリアルの目的は、KnowBe4 と Azure Active Directory (Azure AD) を統合する方法を説明することです。  

KnowBe4 と Azure AD の統合には、次の利点があります。

* KnowBe4 にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に KnowBe4 にサインオン (シングル サインオン) できるようにします。
* 1 つの中央サイト (Azure Active Directory ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
KnowBe4 と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* KnowBe4 でのシングル サインオン (SSO) が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。  

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

* ギャラリーから KnowBe4 を追加する
* Azure AD シングル サインオンの構成とテスト

## <a name="add-knowbe4-from-the-gallery"></a>ギャラリーから KnowBe4 を追加する
Azure AD への KnowBe4 の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に KnowBe4 を追加する必要があります。

**ギャラリーから KnowBe4 を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **KnowBe4**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_01.png)
7. 結果ウィンドウで **[KnowBe4]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、KnowBe4 で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する KnowBe4 ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと KnowBe4 の関連ユーザーの間で、リンク関係が確立されている必要があります。  

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を KnowBe4 の **[Username]** の値として割り当てます。

KnowBe4 で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[KnowBe4 テスト ユーザーの作成](#creating-a-KnowBe4-test-user)** - KnowBe4 で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、KnowBe4 アプリケーションでシングル サインオンを構成することです。

**KnowBe4 で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **KnowBe4** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![Configure Single Sign-On][6] 
2. **[ユーザーの KnowBe4 へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_03.png) 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_04.png) 

  * [サインオン URL] ボックスに、ユーザーが KnowBe4 アプリケーションへのサインオンに使用する URL を入力します。その際、「**https://\<company name\>.knowbe4.com/auth/saml/aad168.ccsctp.net)**」のパターンを使用します。

4. **[KnowBe4 でのシングル サインオンの構成]** ページで、次の手順に従います。
   
    ![Configure Single Sign-On](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_05.png) 
   
   1. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。 
   2. **[次へ]**をクリックします。
5. お使いのアプリケーション用に構成された SSO を取得するために、 [KnowBe4 のサポート チーム](mailto:support@knowbe4.com)に問い合わせます。 KnowBe4 チーム側で SSO を設定する必要があるため、ダウンロードした証明書ファイルをメールに添付して、メタデータ URL (エンティティ ID、SSO サインイン URL、およびサインアウト URL) をチームと共有してください。
6. Azure クラシック ポータルで、[single sign-on configuration confirmation] \(シングル サインオンの構成の確認) を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
7. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。  

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_05.png) 
   
   1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。   
   2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
   3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_06.png) 
   
   1. **[名]** ボックスに「**Britta**」と入力します。  
   2. **[姓]** ボックスに「**Simon**」と入力します。
   3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   4. **[ロール]** 一覧で **[ユーザー]** を選択します。
   5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_08.png) 
   
    1. **[新しいパスワード]** の値を書き留めます。
    2. **[完了]** をクリックします。   

### <a name="create-a-knowbe4-test-user"></a>KnowBe4 テスト ユーザーの作成
このセクションの目的は、KnowBe4 で Britta Simon というユーザーを作成することです。 KnowBe4 では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 存在しない KnowBe4 ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。 

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、KnowBe4 のサポート チームにお問い合わせください。
>  

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に KnowBe4 へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

    ![Assign User][200] 

**KnowBe4 に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[KnowBe4]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。  
アクセス パネルで [KnowBe4] タイルをクリックすると、自動的に KnowBe4 アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_205.png


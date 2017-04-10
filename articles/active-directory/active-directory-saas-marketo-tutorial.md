---
title: "チュートリアル: Azure Active Directory と Marketo の統合 | Microsoft Docs"
description: "Azure Active Directory と Marketo の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1a2ff324cfa65977685f6a0eee9cd398b0717519
ms.openlocfilehash: 20f3a8b006e45e3a94e95b516bca292a82c5fd03


---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>チュートリアル: Marketo と Azure Active Directory の統合
このチュートリアルでは、Marketo と Azure Active Directory (Azure AD) を統合する方法について説明します。

Marketo と Azure AD の統合には、次の利点があります。

* Marketo にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Marketo にサインオン (シングル サインオン) できるようにします。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Marketo と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* Marketo でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
> 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Marketo の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-marketo-from-the-gallery"></a>ギャラリーからの Marketo の追加
Azure AD への Marketo の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Marketo を追加する必要があります。

**ギャラリーから Marketo を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **Marketo**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_01.png)
7. 結果ウィンドウで **[Marketo]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Marketo で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Marketo ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Marketo の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Marketo の **[Username]** の値として割り当てます。

Marketo で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Marketo のテスト ユーザーの作成](#creating-a-predictix-price-reporting-test-user)** - Marketo で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、Marketo アプリケーションでシングル サインオンを構成します。

**Marketo で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. クラシック ポータルの **Marketo** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 
2. **[ユーザーの Marketo へのアクセスを設定してください]** ページで、**[Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_03.png) 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_04.png) 
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[識別子]** ボックスに、`https://saml.marketo.com/sp` のパターンを使用して URL を入力します。
   
    b. **[応答 URL]** ボックスに、`https://login.marketo.com/saml/assertion/\<munchkinid\>` のパターンを使用して URL を入力します。
   
    c. click **[次へ]**
4. **[Marketo でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_05.png)
   
    a. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。
   
    b. **[次へ]**をクリックします。
5. アプリケーションの Munchkin ID を取得するには、管理者の資格情報を使用して Marketo にログインし、次の操作を実行します。
   
    a. 管理者の資格情報を使用して Marketo アプリにログインします。
   
    b. 上部のナビゲーション ウィンドウで [管理者] ボタンをクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. 統合メニューに移動し、Munchkin リンクをクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_11.png)
   
    d. 画面に表示される Munchkin ID をコピーし、Azure AD の構成ウィザードで、応答 URL を完了します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_12.png)
6. アプリケーションで SSO を構成するには、以下の手順に従ってください。
   
    a. 管理者の資格情報を使用して Marketo アプリにログインします。
   
    b. 上部のナビゲーション ウィンドウで [管理者] ボタンをクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. 統合メニューに移動し、[シングルサインオン] をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_07.png) 
   
    d. SAML 設定を有効にするには、[編集] ボタンをクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_08.png) 
   
    e. シングル サインオン設定を**有効**にします。
   
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 Azure AD の構成ウィザードからコピーした発行者 ID を入力します。
   
    g. [エンティティ ID] ボックスに、「**http://saml.marketo.com/sp**」と入力します。
   
    h. **名前識別子要素**としてユーザー ID の場所を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > ユーザー識別子が UPN 値ではない場合、[属性] タブで値を変更します。
    > 
    > 
   
    i. Azure AD の構成ウィザードからダウンロードした証明書をアップロードします。 設定を保存します。
   
    j. ページのリダイレクト設定を編集します。
   
    k. Azure AD 構成ウィザードのログイン URL をコピーして **[ログイン URL]** ボックスに貼り付けます。
   
    l. Azure AD 構成ウィザードのログアウト URL をコピーして **[ログアウト URL]** ボックスに貼り付けます。
   
    m. [エラー URL] に Marketo インスタンス URL をコピーし、[保存] ボタンをクリックして設定を保存します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_10.png)

7. ユーザーの SSO を有効にするには、次の操作を行います。
   
    a. 管理者の資格情報を使用して Marketo アプリにログインします。
   
    b. 上部のナビゲーション ウィンドウで **[管理者]** ボタンをクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. **[セキュリティ]** メニューに移動して、**[ログイン設定]** をクリックします。 
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_13.png)
   
    d. **[SSO 必須]** オプションをオンにして、設定を保存します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_14.png)
8. クラシック ポータルで、シングル サインオンの構成確認を選択し、**[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
9. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-marketo-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-marketo-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-marketo-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。 ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-marketo-tutorial/create_aaduser_05.png) 
   
    a. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   
    b. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
   
    c. ページの下部にある **[次へ]**」を参照してください。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。 ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-marketo-tutorial/create_aaduser_06.png) 
   
    a. **[名]** ボックスに「**Britta**」と入力します。  
   
    b. **[姓]** ボックスに「**Simon**」と入力します。
   
    c. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   
    d. **[ロール]** 一覧で **[ユーザー]** を選択します。
   
    e. **[次へ]**をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-marketo-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-marketo-tutorial/create_aaduser_08.png) 
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[新しいパスワード]** の値を書き留めます。
   
    b. ページの下部にある **[完了]**」を参照してください。   

### <a name="creating-an-marketo-test-user"></a>Marketo テスト ユーザーの作成
このセクションでは、Marketo で Britta Simon というユーザーを作成します。 Marketo プラットフォームでユーザーを作成するには、次の手順に従ってください。

1. 管理者の資格情報を使用して Marketo アプリにログインします。
2. 上部のナビゲーション ウィンドウで **[管理者]** ボタンをクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
3. **[セキュリティ]** メニューに移動して、**[ユーザーと役割]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_19.png)  
4. [ユーザー] タブで **[新しいユーザーの追加]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_15.png) 
5. [新しいユーザーの追加] ウィザードで、次の情報を入力します。
   
    a. テキスト ボックスにユーザーの **[メール]** アドレスを入力します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_16.png)
   
    b. テキスト ボックスに **[名]** を入力します。
   
    c. テキスト ボックスに **[姓]** を入力します。
   
    d. [次へ] をクリックします。
6. **[アクセス許可]** タブで、ユーザーの役割を選択して [次へ] をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_17.png)
7. [送信] ボタンをクリックしてユーザーの招待を送信します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_18.png)
8. ユーザーは、電子メール通知を受け取った後、リンクをクリックしてパスワードを変更し、アカウントをアクティブ化する必要があります。 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に Marketo へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Marketo に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[Marketo]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203]
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト
このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Marketo のタイルをクリックすると、自動的に Marketo アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->



---
title: "チュートリアル: Azure Active Directory と SD Elements の統合 | Microsoft Docs"
description: "Azure Active Directory と SD Elements の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f0386307-bb3b-4810-8d4b-d0bfebda04f4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: b2eb87a88721a3fd1ecdfa573c6a6d49ed02d344
ms.openlocfilehash: 7166f4d3046f573d2f60d747edf2eefbbd0439c2
ms.lasthandoff: 02/16/2017


---
# <a name="tutorial-azure-active-directory-integration-with-sd-elements"></a>チュートリアル: Azure Active Directory と SD Elements の統合
このチュートリアルの目的は、SD Elements と Azure Active Directory (Azure AD) を統合する方法を説明することです。  
SD Elements と Azure AD の統合には、次の利点があります。

* SD Elements にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に SD Elements にサインオン (シングル サインオン) できるようにします。
* 1 つの中央サイト (Azure Active Directory ポータル) でアカウントを管理できます。 

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
SD Elements と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* SD Elements でのシングル サインオンが有効なサブスクリプション

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

1. ギャラリーから SD Elements を追加します。
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-sd-elements-from-the-gallery"></a>ギャラリーから SD Elements を追加します。
Azure AD への SD Elements の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SD Elements を追加する必要があります。

**ギャラリーから SD Elements を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]

6. 検索ボックスに、「 **SD Elements**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_01.png)

7. 結果ウィンドウで **[SD Elements]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、SD Elements で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SD Elements ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SD Elements の関連ユーザーの間で、リンク関係が確立されている必要があります。  
このリンク関係は、Azure AD の **[ユーザー名]** の値を、SD Elements の **[Username]** の値として割り当てることで確立されます。

SD Elements で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[SD Elements テスト ユーザーの作成](#creating-a-sd-elements-test-user)** - SD Elements で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、SD Elements アプリケーションでシングル サインオンを構成することです。

SD Elements アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを **SAML トークン属性** の構成に追加する必要があります。 次のスクリーンショットはその例です。

![[シングル サインオンの構成]](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_14.png) 

**SD Elements で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **SD Elements** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 

2. **[ユーザーの SD Elements へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_03.png) 

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_04.png) 

    a. **[発行元]** テキストボックスにテナントの発行元 URL を入力します。次のパターンを利用します。*https://\<テナント名\>.sdelements.com/sso/saml2/metadata*

    b. **[応答 URL]** テキストボックスにテナントの応答 URL を入力します。次のパターンを利用します。*https://\<テナント名\>.sdelements.com/sso/saml2/acs/*       

    > [!NOTE] 
    > テナントの実際の発行元 URL と応答 URL が必要な場合、 [SD Elements サポート チーム](mailto:support@sdelements.com)にお問い合わせください。

    c. **[次へ]**をクリックします。


1. **[SD Elements でのシングル サインオンの構成]** ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_05.png) 
   
    a. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。
   
    b. **[次へ]**をクリックします。
2. シングル サインオンを有効にするには、 [SD Elements サポート チーム](mailto:support@sdelements.com) に連絡し、ダウンロードした証明書ファイルを提示します。
3. 別のブラウザー ウィンドウで、管理者として SD Elements テナントにサインオンします。
4. 上部のメニューで [System] をクリックし、[Single Sign-on] をクリックします。 
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_09.png) 
5. **[Single Sign-On Settings]** ダイアログで、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_10.png) 
   
    a. **SSO 型** として **SAML** を選びます。
   
    b. Azure クラシック ポータルで、**[SD Elements でのシングル サインオンの構成]** ダイアログ ページの **[発行者の URL]** の値をコピーし、**[ID プロバイダーのエンティティ ID]** テキスト ボックスに貼り付けます。
   
    c. Azure クラシック ポータルで、**[SD Elements でのシングル サインオンの構成]** ダイアログ ページの **[シングル サインオン サービス URL]** の値をコピーし、**[ID プロバイダーのシングル サインオン サービス]** テキスト ボックスに貼り付けます。
   
    d. [ **Save**] をクリックします。
6. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
7. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]
8. 上部のメニューで、 **属性** to open the **SAML Token 属性** ダイアログを開きます。 
   
    ![[シングル サインオンの構成]][21]
9. 次の表の行ごとに次の手順を実行します。
   
   | 属性名 | 属性値 |
   | --- | --- |
   | 電子メール |User.mail |
   | firstname |User.givenname |
   | lastname |User.surname |

    a. **[ユーザー属性の追加]**をクリックします。 

    ![[シングル サインオンの構成]][23]

    b. **[属性名]** ボックスに**属性名**を入力し、**[属性値]** としてその行に表示されている属性値を選択します。

    ![[シングル サインオンの構成]][22]

    c. **[ユーザー属性の追加]**をクリックします。 

    ![[シングル サインオンの構成]][23]

1. **[変更の適用]**をクリックします。 
   
    ![[シングル サインオンの構成]][24]

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。  

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_05.png) 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   
    b. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
   
    c. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_06.png) 
   
   a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[名]** ボックスに「**Britta**」と入力します。  
   
   b. **[姓]** ボックスに「**Simon**」と入力します。
   
   c. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   
   d. **[ロール]** 一覧で **[ユーザー]** を選択します。
   
   e. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_08.png) 
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[新しいパスワード]** の値を書き留めます。
   
    b. ページの下部にある **[完了]**」を参照してください。   

### <a name="creating-a-sd-elements-test-user"></a>SD Elements テスト ユーザーの作成
このセクションの目的は、SD Elements で Britta Simon というユーザーを作成することです。 SD Elements の場合、SD Elements ユーザーは手動で作成します。

**SD Elements で Britta Simon を作成するには、次の手順に従います。**

1. Web ブラウザー ウィンドウで、管理者として SD Elements 企業サイトにサインオンします。
2. 上部のメニューで、[ユーザー管理]、[ユーザー] の順にクリックします。
   
   ![SD Elements テスト ユーザーの作成](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_11.png) 
3. [新しいユーザーの追加] をクリックします。
   
   ![SD Elements テスト ユーザーの作成](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_12.png) 
4. [新規ユーザーの追加] ダイアログで、次の手順を実行します。
   
   ![SD Elements テスト ユーザーの作成](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_13.png) 
   
   a. **[電子メール]** ボックスに、Azure AD の Britta の電子メール アドレスを入力します。
   
   b. **[名]** ボックスに「**Britta**」と入力します。
   
   c. **[姓]** ボックスに「**Simon**」と入力します。
   
   d. **[ロール]** として **[ユーザー]** を選びます。 
   
   e. **[Create User]**をクリックします。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に SD Elements へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][200] 

**SD Elements に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[SD Elements]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. **[ユーザー]** 一覧で **[Britta Simon]** を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。  
アクセス パネルで [SD Elements] タイルをクリックすると、自動的に SD Elements アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_100.png

[21]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_80.png
[22]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_82.png
[23]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_81.png
[24]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_83.png


[200]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_205.png


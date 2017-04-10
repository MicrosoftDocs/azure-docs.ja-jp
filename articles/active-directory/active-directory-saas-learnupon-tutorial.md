---
title: "チュートリアル: Azure Active Directory と Novatus の統合 | Microsoft Docs"
description: "Azure Active Directory と LearnUpon の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4a07f946d396a9263d5b00202cd5229ddc86d1be
ms.openlocfilehash: 02619192a5106a064a9f97cddac7c20bd73559e7
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>チュートリアル: Azure Active Directory と LearnUpon の統合
このチュートリアルの目的は、LearnUpon と Azure Active Directory (Azure AD) を統合する方法を説明することです。

LearnUpon と Azure AD の統合には、次の利点があります。

* LearnUpon にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に LearnUpon にシングル サインオン (SSO) できるようにします
* 1 つの中央サイト (Azure Active Directory クラシック ポータル) でアカウントを管理できます。 

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
LearnUpon と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* LearnUpon でのシングル サインオン (SSO) が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD の SSO をテストできるようにすることです。 

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの LearnUpon の追加
2. Azure AD SSO の構成とテスト

## <a name="adding-learnupon-from-the-gallery"></a>ギャラリーからの LearnUpon の追加
Azure AD への LearnUpon の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に LearnUpon を追加する必要があります。

**ギャラリーから LearnUpon を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに「 **LearnUpon**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_01.png)
7. 結果ウィンドウで **[LearnUpon]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、LearnUpon で Azure AD の SSO を構成し、テストする方法について説明することです。

SSO を機能させるには、Azure AD ユーザーに対応する LearnUpon ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと LearnUpon の関連ユーザーの間で、リンク関係が確立されている必要があります。  

このリンク関係は、Azure AD の **[ユーザー名]** の値を、LearnUpon の **[Username (ユーザー名)]** の値として割り当てることで確立されます。

LearnUpon で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[LearnUpon テスト ユーザーの作成](#creating-a-learnupon-test-user)** - LearnUpon で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成
このセクションの目的は、Azure クラシック ポータルで Azure AD の SSO を有効にすることと、LearnUpon アプリケーションでシングル サインオンを構成することです。

**LearnUpon で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **LearnUpon** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 
2. **[ユーザーの LearnUpon へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_03.png) 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![Configure Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_04.png) 
  1. **[応答 URL]** ボックスに、`https://\<companyname\>.learnupon.com/saml/consumer` という形式を使用して、Assertion Consumer Service URL を入力します。
  2. **[次へ]**をクリックします。 
4. **[LearnUpon でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_05.png)   
  1. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。 この証明書とメタデータ URL (エンティティ ID、SSO サインイン URL、サインアウト URL) は LearnUpon 側で SSO をセットアップするために必要です。
  2. **[次へ]**をクリックします。
5. 別のブラウザー インスタンスを開き、管理者アカウントを使用して LearnUpon にログインします。 
6. **[settings (設定)]** タブをクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_06.png) 
7. **[Single Sign On - SAML (シングル サインオン - SAML)]**、**[General Settings (全般設定)]** の順にクリックし、SAML 設定を構成します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_07.png) 
8. **[General Settings (全般設定)]** セクションで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_08.png)  
  1. **[Enabled]**を選択します。
  2. **[Version (バージョン)]** として **[2.0]** を選択します。
  3. **[Skip conditions (条件をスキップする)]** で **[No (いいえ)]** を選択します。
  4. **[SAML Token POST param name (SAML トークン POST パラメーター名)]** ボックスに、前述の SAML コンシューマー URL に対する POST 要求パラメーターの名前を入力します。ここには、確認と認証の対象である SAML アサーションが含まれます (**SAMLResponse** など)。
  5. **[Name Identifier Format (名前識別子形式)]** ボックスに、SAML アサーション内のユーザー ID (メール アドレス) の場所を示す値を入力します (**urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress** など)。
  6. Azure クラシック ポータルのログイン画面でアップロード済みアイコンをクリックした際のユーザーの移動先を示す値を、**[Identify Provider Location (プロバイダーの場所を特定)]** ボックスに入力します。
  7. Azure クラシック ポータルで、**[シングル サインアウト サービス URL]** をコピーし、**[Sign out URL (サインアウト URL)]** ボックスに貼り付けます。
  8. **[Manage finger prints (指紋の管理)]** をクリックし、ダウンロードした証明書の指紋をアップロードします。 
9. **[User Settings (ユーザー設定)]**をクリックし、次の手順を実行します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_11.png)  
  1. **[First Name Identifier Format (名識別子形式)]** ボックスに、SAML アサーション内のユーザーの名の場所を示す値を入力します (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname など)。 
  2. **[Last Name Identifier Format (姓識別子形式)]** ボックスに、SAML アサーション内のユーザーの姓の場所を示す値を入力します (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname など)。
7. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
8. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-learnupon-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-learnupon-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-learnupon-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-learnupon-tutorial/create_aaduser_05.png) 
  1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
  2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
  3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-learnupon-tutorial/create_aaduser_06.png) 
  1. **[名]** ボックスに「**Britta**」と入力します。   
  2. **[姓]** ボックスに「**Simon**」と入力します。
  3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
  4. **[ロール]** 一覧で **[ユーザー]** を選択します。
  5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-learnupon-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-learnupon-tutorial/create_aaduser_08.png) 
  1. **[新しいパスワード]** の値を書き留めます。 
  2. ページの下部にある **[完了]**」を参照してください。   

### <a name="create-a-learnupon-test-user"></a>LearnUpon テスト ユーザーの作成
このセクションの目的は、LearnUpon で Britta Simon というユーザーを作成することです。 LearnUpon では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 存在しない LearnUpon ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。 [Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、LearnUpon のサポート チームにお問い合わせください。 
> 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に LearnUpon へのアクセスを許可し、このユーザーが Azure の SSO を使用できるようにすることです。

![ユーザーの割り当て][200] 

**LearnUpon に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[LearnUpon]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。  

アクセス パネルで [LearnUpon] タイルをクリックすると、LearnUpon アプリケーションに自動的にサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_205.png


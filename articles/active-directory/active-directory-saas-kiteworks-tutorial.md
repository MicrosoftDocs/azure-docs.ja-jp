---
title: "チュートリアル: Azure Active Directory と Kiteworks の統合 | Microsoft Docs"
description: "Azure Active Directory と Kiteworks の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f7984aaf-ab1f-4a85-9646-a9523f5275d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: d3be35d6c7efea5e5a784ee3c0d1965cc11bfcfe
ms.openlocfilehash: f64add6d742de24d0144db44e7c3885feb7a1139
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-kiteworks"></a>チュートリアル: Azure Active Directory と Kiteworks の統合
このチュートリアルの目的は、Kiteworks と Azure Active Directory (Azure AD) を統合する方法を説明することです。 

Kiteworks と Azure AD の統合には、次の利点があります。 

* Kiteworks にアクセスする Azure AD ユーザーを制御できます。 
* ユーザーが自分の Azure AD アカウントで自動的に Kiteworks にシングル サインオン (SSO) できるようにします
* 1 つの中央サイト (Azure Active Directory ポータル) でアカウントを管理できます。 

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Kiteworks と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* Kiteworks での SSO が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。 

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD の SSO をテストできるようにすることです。  

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの Kiteworks の追加 
2. Azure AD SSO の構成とテスト

## <a name="add-kiteworks-from-the-gallery"></a>ギャラリーからの Kiteworks の追加
Azure AD への Kiteworks の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Kiteworks を追加する必要があります。

**ギャラリーから Kiteworks を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **Kiteworks**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_01.png)
7. 結果ウィンドウで **[Kiteworks]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_02.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Kiteworks で Azure AD の SSO を構成し、テストする方法について説明することです。

SSO を機能させるには、Azure AD ユーザーに対応する Kiteworks ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Kiteworks の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Kiteworks の **[Username (ユーザー名)]** の値として割り当てます。

Kiteworks で Azure AD の SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Kiteworks テスト ユーザーの作成](#creating-a-kiteworks-test-user)** - Kiteworks で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成
このセクションの目的は、Azure クラシック ポータルで Azure AD の SSO を有効にすることと、Kiteworks アプリケーションで SSO を構成することです。 

この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。 この手順に慣れていない場合は、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

Kiteworks の SSO を構成するには、登録済みのドメインが必要です。 登録済みのドメインがない場合は、Kiteworks のサポート チームにお問い合わせください。  

**Kiteworks で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Kiteworks** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![Configure Single Sign-On][6] 
2. **[ユーザーの Kiteworks へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_03.png) 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_04.png) 
  1. **[サインオン URL]** ボックスに、ユーザーが UserEcho アプリケーションへのサインオンに使用する URL (例: *https://fabrikam.kiteworks.com/*) を入力します。
  2. **[次へ]**をクリックします。
4. **[Kiteworks でのシングル サインオンの構成]** ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_05.png)   
  1. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。
  2. **[次へ]**をクリックします。
5. Kiteworks 企業サイトに管理者としてサインオンします。
6. 上部のツールバーで **[Settings]**をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_06.png) 
7. **[Authentication and Authorization (認証と承認)]** セクションで、**[SSO Setup (SSO のセットアップ)]** をクリックします。 
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_07.png) 
8. [SSO Setup] ページで、次の手順に従います。
   
    ![Configure Single Sign-On](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_09.png)   
  1. **[Authenticate via SSO (SSO を介して認証する)]** を選択します。
  2. **[Initiate AuthnRequest (AuthnRequest の開始)]** を選択します。
  3. Azure クラシック ポータルの **[Kiteworks でのシングル サインオンの構成]** ダイアログ ページで **[エンティティ ID]** の値をコピーし、**[IDP Entity ID (IDP エンティティ ID)]** ボックスに貼り付けます。 
  4. Azure クラシック ポータルの **[Kiteworks でのシングル サインオンの構成]** ダイアログ ページで **[シングル サインオン サービス URL]** の値をコピーし、**[Single Sign-On Service URL (シングル サインオン サービス URL)]** ボックスに貼り付けます。
  5. Azure クラシック ポータルの **[Kiteworks でのシングル サインオンの構成]** ダイアログ ページで **[シングル サインアウト サービス URL]** の値をコピーし、**[Single Logout Service URL (シングル サインアウト サービス URL)]** ボックスに貼り付けます。
  6. ダウンロードした証明書をメモ帳で開き、その内容をコピーして、**[RSA Public Key Certificate (RSA 公開キーの証明書)]** ボックスに貼り付けます。 
  7. **[保存]**をクリックします。
9. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。 
   
    ![Azure AD のシングル サインオン][10]
10. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_09.png)  
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_05.png)  
  1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
  2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
  3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。 
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_06.png) 
  1. **[名]** ボックスに「**Britta**」と入力します。  
  2. **[姓]** ボックスに「**Simon**」と入力します。
  3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
  4. **[ロール]** 一覧で **[ユーザー]** を選択します。
  5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_08.png) 
  1. **[新しいパスワード]** の値を書き留めます。
  2. ページの下部にある **[完了]**」を参照してください。   

### <a name="create-a-kiteworks-test-user"></a>Kiteworks テスト ユーザーの作成
このセクションの目的は、Kiteworks で Britta Simon というユーザーを作成することです。

Kiteworks では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない Kiteworks ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、Kiteworks のサポート チームにお問い合わせください。
>  

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に Kiteworks へのアクセスを許可し、このユーザーが Azure の SSO を使用できるようにすることです。

![ユーザーの割り当て][200] 

**Kiteworks に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[Kiteworks]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。  

アクセス パネルで [Kiteworks] タイルをクリックすると、自動的に Kiteworks アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_205.png








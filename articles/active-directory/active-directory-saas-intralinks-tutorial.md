---
title: "チュートリアル: Azure Active Directory と Intralinks の統合 | Microsoft Docs"
description: "Azure Active Directory と Intralinks の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 147f2bf9-166b-402e-adc4-4b19dd336883
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ed2fc2b34ff10acc806daec84986f8db58e713c3
ms.openlocfilehash: 2112f4b129ff8735955d440341174a44a6a1469c
ms.lasthandoff: 02/17/2017


---

# <a name="tutorial-azure-active-directory-integration-with-intralinks"></a>チュートリアル: Azure Active Directory と Intralinks の統合
このチュートリアルでは、Intralinks と Azure Active Directory (Azure AD) を統合する方法について説明します。

Intralinks と Azure AD の統合には、次の利点があります。

* Intralinks にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Intralinks にシングル サインオン (SSO) できるようにします。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Intralinks と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* Intralinks でのシングル サインオンが有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの Intralinks の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="add-intralinks-from-the-gallery"></a>ギャラリーからの Intralinks の追加
Azure AD への Intralinks の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Intralinks を追加する必要があります。

**ギャラリーから Intralinks を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **Intralinks**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_01.png)
7. 結果ウィンドウで **[Intralinks]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_02.png)

## <a name="configure-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Intralinks で Azure AD のシングル サインオンを構成し、テストします。

SSO を機能させるには、Azure AD ユーザーに対応する Intralinks ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Intralinks の関連ユーザーの間で、リンク関係が確立されている必要があります。

>[!NOTE]
>このリンク関係は、Azure AD の **[ユーザー名]** の値を、Intralinks の **[Username]** の値として割り当てることで確立されます。
>

Intralinks で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Intralinks テスト ユーザーの作成](#creating-an-intralinks-test-user)** - Intralinks で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、Intralinks アプリケーションでシングル サインオンを構成します。

**Intralinks で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. クラシック ポータルの **Intralinks** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![Configure Single Sign-On][6] 
2. **[ユーザーの Intralinks へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_03.png) 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_04.png) 
   
    1. **[サインオン URL]** ボックスに、次のパターンを使用して、ユーザーが Intralinks アプリケーションへのサインオンに使用する URL を入力します。**https://\<会社名\>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/\<Azure AD テナント ID\>/**
    2. **[次へ]**をクリックします。
4. **[Intralinks でのシングル サインオンの構成]** ページで、次の手順に従います。
   
    ![Configure Single Sign-On](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_05.png)
   
    1. **[メタデータのダウンロード]** をクリックし、コンピューターにファイルを保存します。
    2. **[次へ]**をクリックします。
5. 使用しているアプリケーション用に構成された SSO を入手するため、Intralinks のサポート チームに連絡し、ダウンロードしたメタデータ ファイルを添付した電子メールを送ります。
6. クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
7. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。

* ユーザーの一覧で **[Britta Simon]**を選択します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-intralinks-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-intralinks-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-intralinks-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。 ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-intralinks-tutorial/create_aaduser_05.png) 
   
    1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。 
    2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
    3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-intralinks-tutorial/create_aaduser_06.png) 
   
   1. **[名]** ボックスに「**Britta**」と入力します。  
   2. **[姓]** ボックスに「**Simon**」と入力します。
   3. **[表示名]** ボックスに「**Britta Simon**」と入力します。   
   4. **[ロール]** 一覧で **[ユーザー]** を選択します。
   5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-intralinks-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-intralinks-tutorial/create_aaduser_08.png) 
   
    1. **[新しいパスワード]** の値を書き留めます。
    2. **[完了]** をクリックします。   

### <a name="create-an-intralinks-test-user"></a>Intralinks テスト ユーザーの作成
このセクションでは、Intralinks で Britta Simon というユーザーを作成します。 Intralinks サポート チームと連携し、Intralinks プラットフォームにユーザーを追加してください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に Intralinks へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようします。

![ユーザーの割り当て][200] 

**Intralinks に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[Intralinks]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203]
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="add-intralinks-via-or-elite-application"></a>Intralinks VIA または Elite アプリケーションの追加
Intralinks では、Deal Nexus アプリケーションを除くその他すべての Intralinks アプリケーションで、同じ SSO ID プラットフォームを使用します。 Intralinks の他のアプリケーションを使用する予定の場合、最初に上述の手順に従って&1; つのプライマリ Intralinks アプリケーションに対して SSO を構成する必要があります。

その後、後述の手順に従ってテナントに別の Intralinks アプリケーションを追加し、このテナントでプライマリ アプリケーションを SSO で利用できます。 

>[!NOTE]
>この機能は、Azure AD "Premium" SKU のお客様のみ使用できます。"無料" SKU または "Basic" SKU のお客様は使用できません。
> 

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 左側のタブで、 **[カスタム]** タブをクリックします。
   
    ![Intralinks VIA または Elite アプリケーションの追加](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_51.png)
7. アプリケーションに適切な名前 (**Intralinks Elite** など) を付け、[完了] ボタンをクリックします。
8. **[シングル サインオンの構成]** ボタンをクリックします。
9. **[既存のシングル サインオン]** オプションを選択します。
   
    ![Intralinks VIA または Elite アプリケーションの追加](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_52.png)
10. 他の Intralinks アプリケーションの Intralinks チームから SP によって開始される SSO URL を取得して、次のように入力します。 
    
    ![Intralinks VIA または Elite アプリケーションの追加](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_53.png)
    
    * [サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Intralinks アプリケーションへのサインオンに使用する URL を入力します。**https://\<会社名\>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/\<AzureAD テナント ID\>/**
11. **[次へ]**をクリックします。
12. 「**[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)**」セクションの手順に従って、アプリケーションをユーザーまたはグループに割り当てます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションでは、アクセス パネルを使用して Azure AD の SSO 構成をテストします。

アクセス パネルで Intralinks のタイルをクリックすると、自動的に Intralinks アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_205.png


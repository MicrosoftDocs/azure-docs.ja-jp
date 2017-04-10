---
title: "チュートリアル: Azure Active Directory と Kronos の統合 | Microsoft Docs"
description: "Azure Active Directory と Kronos の間でシングル サインオンを構成する方法について確認します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: e28d6191-c375-43c6-b2df-22daa88d9939
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 7563c1a70f6f956b28a6d2b78f7c0d373620c0cd
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-kronos"></a>チュートリアル: Azure Active Directory と Kronos の統合
このチュートリアルでは、Kronos と Azure Active Directory (Azure AD) を統合する方法について説明します。

Kronos と Azure AD の統合には、次の利点があります。

* Kronos にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Kronos にシングル サインオン (SSO) できるようにします。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Kronos と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* **Kronos Workforce Central** での SSO が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
>  

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD SSO をテストします。 

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Kronos の追加
2. Azure AD SSO の構成とテスト

## <a name="add-kronos-from-the-gallery"></a>ギャラリーからの Kronos の追加
Azure AD への Kronos の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Kronos を追加する必要があります。

**ギャラリーから Kronos を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]

6. 検索ボックスに、「 **Kronos**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kronos-tutorial/tutorial_kronos_01.png)

7. 結果ウィンドウで **[Kronos]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kronos-tutorial/tutorial_kronos_06.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Kronos で Azure AD の SSO を構成し、テストします。

SSO を機能させるには、Azure AD ユーザーに対応する Kronos ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Kronos の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、Kronos の **[Username]** の値として割り当てることで確立されます。

Kronos で Azure AD の SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Kronos テスト ユーザーの作成](#creating-an-kronos-test-user)** - Kronos で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションでは、クラシック ポータルで Azure AD の SSO を有効にして、Kronos アプリケーションでシングル サインオンを構成します。

Kronos アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 アプリケーションにマップされる適切なユーザー ID を識別するには、まず Kronos チームと協力してください。 

また、このマッピングに使用する属性については、Kronos チームからのガイダンスに従ってください。 ユーザー ID として **"NameIdentifier"** 属性を使用することをお勧めします。 この属性の値は、アプリケーションの **[属性]** タブから管理できます。 

次のスクリーンショットはその例です。 ここでは、nameidentifier 要求を **ExtractMailPrefix** 関数とともに **userprincipalname** 属性にマッピングしています。これは一意のユーザー ID となり、SAML 応答が成功するたびに Kronos アプリケーションに送信されます。

![[シングル サインオンの構成]](./media/active-directory-saas-kronos-tutorial/tutorial_kronos_07.png) 

**Kronos で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. クラシック ポータルの **Kronos** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
     ![[シングル サインオンの構成]][6] 

2. **[ユーザーの Kronos へのアクセスを設定してください]** ページで、**[Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-kronos-tutorial/tutorial_kronos_03.png) 

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-kronos-tutorial/tutorial_kronos_04.png) 
  1. 次のパターンを使用して、ユーザーが Kronos アプリケーションへのサインオンに使用する URL を [識別子] ボックスに入力します: `https://<company name>.kronos.net/`
  2. [応答 URL] に、`https://<company name>.kronos.net/wfc/navigator/logonWithUID` の形式で URL を入力します。

1. **[Kronos でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-kronos-tutorial/tutorial_kronos_05.png) 
  1. **[メタデータのダウンロード]** をクリックし、コンピューターにファイルを保存します。 
  2. **[次へ]**をクリックします。

2. 自分のアプリケーション向けに SSO を構成する場合は、Kronos のアカウント管理者に問い合わせて、SSO を構成するための適切なチャネルについてのサポートを受けます。 ダウンロードしたメタデータ ファイルをメールに添付して送信する必要があることに注意してください。

3. クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]

4. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kronos-tutorial/create_aaduser_09.png) 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kronos-tutorial/create_aaduser_03.png) 

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kronos-tutorial/create_aaduser_04.png) 

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kronos-tutorial/create_aaduser_05.png) 
  1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。 
  2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
  3. **[次へ]**をクリックします。

6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kronos-tutorial/create_aaduser_06.png) 
  1. **[名]** ボックスに「**Britta**」と入力します。  
  2. **[姓]** ボックスに「**Simon**」と入力します。 
  3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
  4. **[ロール]** 一覧で **[ユーザー]** を選択します。
  5. **[次へ]**をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kronos-tutorial/create_aaduser_07.png) 

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kronos-tutorial/create_aaduser_08.png) 
  1. **[新しいパスワード]** の値を書き留めます。
  2. ページの下部にある **[完了]**」を参照してください。   

### <a name="create-an-kronos-test-user"></a>Kronos テスト ユーザーの作成
このセクションでは、Kronos で Britta Simon というユーザーを作成します。 Kronos アプリケーションでは、SSO を行う前に、すべてのユーザーをアプリケーションにプロビジョニングする必要があります。 

Kronos のカスタマー サポート担当者と連携し、すべてのユーザーをアプリケーションにプロビジョニングしてください。 

>[!NOTE]
>ユーザーを手動で作成する必要がある場合、またはユーザーのバッチを作成する必要がある場合は、Kronos のサポート チームにお問い合わせください。 
> 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に Kronos へのアクセスを許可することで、このユーザーが Azure SSO を使用できるようにします。

![ユーザーの割り当て][200] 

**Kronos に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Kronos]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-kronos-tutorial/tutorial_kronos_50.png) 

3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 

4. ユーザーの一覧で **[Britta Simon]**を選択します。

5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションでは、アクセス パネルを使用して Azure AD の SSO 構成をテストします。

アクセス パネルで Kronos のタイルをクリックすると、自動的に Kronos アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_205.png


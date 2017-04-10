---
title: "チュートリアル: Azure Active Directory と Proofpoint on Demand の統合 | Microsoft Docs"
description: "Azure Active Directory と Proofpoint on Demand の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 773e7f7d-ec31-411b-860d-6a6633335d43
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7cc133d6289bffbc3b7fc591104bc51ebfc67ddd
ms.openlocfilehash: 2ea87726b06a0e8ab20515031424bf4e93f45a80
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-proofpoint-on-demand"></a>チュートリアル: Azure Active Directory と Proofpoint on Demand の統合
このチュートリアルでは、Proofpoint on Demand と Azure Active Directory (Azure AD) を統合する方法について説明します。

Proofpoint on Demand と Azure AD の統合には、次の利点があります。

* Proofpoint on Demand にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Proofpoint on Demand にサインオン (シングル サインオンまたは SSO) できるようにすることが可能です。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Azure AD と Proofpoint on Demand の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* Proofpoint on Demand でのシングル サインオン (SSO) サブスクリプション

このチュートリアルの手順をテストするには、次の推奨事項に従います。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

* ギャラリーからの Proofpoint on Demand の追加。
* Azure AD シングル サインオンの構成とテスト。

## <a name="add-proofpoint-on-demand-from-the-gallery"></a>ギャラリーからの Proofpoint on Demand の追加
Azure AD への Proofpoint on Demand の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Proofpoint on Demand を追加する必要があります。

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Active Directory のアイコン][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション] メニュー項目][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![[追加] ボタン][3]
5. **[What do you want to do (実行する内容)]** ダイアログ ボックスで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加を選択][4]
6. 検索ボックスに、「**Proofpoint on Demand**」と入力します。
   
    ![「Proofpoint on Demand」と入力するボックス](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_01.png)
7. 結果ウィンドウで **[Proofpoint on Demand]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、Britta Simon というテスト ユーザーに基づいて、Proofpoint on Demand で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Proofpoint on Demand ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Proofpoint on Demand の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Proofpoint on Demand の **[Username (ユーザー名)]** の値として割り当てます。

Proofpoint on Demand で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. [Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on) - ユーザーがこの機能を使用できるようにします。
2. [Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user) - Britta Simon で Azure AD のシングル サインオンをテストします。
3. [Proofpoint on Demand テスト ユーザーの作成](#creating-a-proofpoint-ondemand-test-user) - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Proofpoint on Demand で作成します。
4. [Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user) - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. [シングル サインオンのテスト](#testing-single-sign-on) - 構成が機能することを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、Proofpoint on Demand アプリケーションでシングル サインオンを構成します。

1. クラシック ポータルの **Proofpoint on Demand** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログ ボックスを開きます。
   
    ![[シングル サインオンの構成] ボタン][6]
2. **[ユーザーの Proofpoint on Demand へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[Microsoft Azure AD のシングル サインオン] オプション ボタン](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_03.png)
3. **[アプリケーション設定の構成]** ページで、次の手順を実行します。
   
    ![ボックスに情報が入力された [アプリケーション設定の構成] ページ](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_04.png)
   1. **[サインオン URL]** ボックスに、ユーザーが Proofpoint on Demand アプリケーションへのサインオンに使用する URL を入力します。 **https://\<ホスト名\>.pphosted.com/ppssamlsp_hostname** というパターンを使用します。
   2. **[識別子]** ボックスに、**https://\<ホスト名/>.pphosted.com/ppssamlsp** というパターンで URL を入力します。
   3. **[応答 URL]** ボックスに、**https://\<ホスト名/>.pphosted.com:portnumber/v1/samlauth/samlconsumer** というパターンで URL を入力します。  
   4. **[次へ]**をクリックします。
4. **[Proofpoint on Demand でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![[Proofpoint on Demand でのシングル サインオンの構成] ページと [証明書のダウンロード] ボタン](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_05.png)
   1. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。   
   2. **[次へ]**をクリックします。
5. アプリケーション用に構成された SSO を入手するために、Proofpoint on Demand サポート チームに連絡し、次のものを情報として提供します。
   * ダウンロードした証明書
   * エンティティ ID
   * SAML SSO URL
6. クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![シングル サインオンの構成確認のチェック ボックス][10]
7. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![確認ページ][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。

![ユーザーの一覧に含まれるテスト ユーザー][20]

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Active Directory のアイコン](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_09.png)
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]** をクリックして、ユーザーの一覧を表示します。
   
    ![[ユーザー] メニュー項目](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_03.png)
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![[ユーザーの追加] ボタン](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_04.png)
5. **[このユーザーに関する情報の入力]** ページで、次の手順に従います。

    ![[このユーザーに関する情報の入力] ページ (各ボックスに情報を入力済み)](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_05.png)   
   1. **[ユーザーの種類]** で **[組織内の新しいユーザー]** を選択します。
   2. **[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。
   3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ページで、次の手順に従います。

  ![[ユーザー プロファイル] ページ (各ボックスに情報を入力済み)](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_06.png)   
   1. **[名]** ボックスに「**Britta**」と入力します。  
   2. **[姓]** ボックスに「**Simon**」と入力します。
   3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   4. **[ロール]** ボックスの一覧の **[ユーザー]** を選択します。
   5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ページで、**[作成]** をクリックします。
   
   ![一時パスワードの作成ボタン](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_07.png)
8. **[一時パスワードの取得]** ページで、次の手順に従います。
   
   ![[一時パスワードの取得] ページとパスワード情報](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_08.png)  
   1. **[新しいパスワード]** ボックスの値を書き留めます。
   2. ページの下部にある **[完了]**」を参照してください。   

### <a name="create-a-proofpoint-on-demand-test-user"></a>Proofpoint on Demand のテスト ユーザーの作成
このセクションでは、Proofpoint on Demand で Britta Simon というユーザーを作成します。 Proofpoint on Demand サポート チームと連携し、Proofpoint on Demand プラットフォームにユーザーを追加してください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に Proofpoint on Demand へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![直接方式でアクセスが有効化されたことを示すユーザー情報][200]

1. クラシック ポータルのディレクトリ ビューで、トップ メニューの **[アプリケーション]** をクリックし、アプリケーション ビューを開きます。
   
    ![[アプリケーション] メニュー項目][201]
2. アプリケーションの一覧で、**[Proofpoint on Demand]** を選択します。
   
    ![Proofpoint on Demand が選択されたアプリケーションの一覧](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_50.png)
3. 上部のメニューで **[ユーザー]** をクリックします。
   
    ![[ユーザー] メニュー項目][203]
4. ユーザーの一覧で **[Britta Simon]** を選択します。
5. 下部にあるツール バーで **[割り当て]** をクリックします。
   
    ![[割り当て] ボタン][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで **[Proofpoint on Demand]** タイルをクリックすると、Proofpoint on Demand アプリケーションに自動的にサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_205.png


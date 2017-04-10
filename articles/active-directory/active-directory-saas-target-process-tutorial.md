---
title: "チュートリアル: Azure Active Directory と TargetProcess の統合 | Microsoft Docs"
description: "Azure Active Directory と TargetProcess の間でシングル サインオンを構成する方法について確認します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 7cb91628-e758-480d-a233-7a3caaaff50d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: 7cf207e3a853359794a5988aff854cf766734f9b
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-targetprocess"></a>チュートリアル: Azure Active Directory と TargetProcess の統合
このチュートリアルの目的は、TargetProcess と Azure Active Directory (Azure AD) を統合する方法を説明することです。

TargetProcess と Azure AD の統合には、次の利点があります。 

* TargetProcess にアクセスするユーザーを Azure AD で管理できます。 
* ユーザーが自分の Azure AD アカウントで自動的に TargetProcess にシングル サインオン (SSO) できるようにします
* 1 つの中央サイト (Azure Active Directory クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Azure AD と TargetProcess の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* TargetProcess での SSO が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。 

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD の SSO をテストできるようにすることです。 

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーから TargetProcess を追加する 
2. Azure AD SSO の構成とテスト

## <a name="adding-targetprocess-from-the-gallery"></a>ギャラリーから TargetProcess を追加する
Azure AD への TargetProcess の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に TargetProcess を追加する必要があります。

**ギャラリーから TargetProcess を追加するには、次の手順を実行します。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに「 **TargetProcess**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_01.png)
7. 結果ウィンドウで **[TargetProcess]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_10.png)

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、TargetProcess で Azure AD の SSO を構成し、テストする方法について説明することです。

SSO を機能させるには、Azure AD ユーザーに対応する TargetProcess ユーザーが Azure AD で認識されている必要があります。 つまり、Azure AD ユーザーと TargetProcess の関連ユーザーの間でリンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を、TargetProcess の **[Username (ユーザー名)]** の値として割り当てます。

TargetProcess で Azure AD の SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[TargetProcess のテスト ユーザーの作成](#creating-a-targetprocess-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを TargetProcess で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成
このセクションの目的は、Azure クラシック ポータルで Azure AD の SSO を有効にすることと、TargetProcess アプリケーションでシングル サインオンを構成することです。 

この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。 この手順に慣れていない場合は、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

TargetProcess の SSO を構成するには、登録済みのドメインが必要です。 登録済みのドメインをお持ちでない場合は、TargetProcess のサポート チーム ( [support@flatterfiles.com](mailto:support@flatterfiles.com)」を参照してください。  

**TargetProcess で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure AD クラシック ポータルの **TargetProcess** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6]
2. **[ユーザーの TargetProcess へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_02.png) 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_03.png) 
  1. **[サインオン URL]** ボックスに、ユーザーが TargetProcess アプリケーションへのサインオンに使用する URL を入力します (例: *https://fabrikam.TargetProcess.com/*)。
  2. **[次へ]**をクリックします。
4. **[TargetProcess でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_04.png)   
  1. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。
  2. **[次へ]**をクリックします。
5. 管理者として TargetProcess アプリケーションにサインオンします。
6. 上部のメニューで **[セットアップ]**をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_05.png)
7. **[設定]**をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_06.png) 
8. **[Single Sign-on]**をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_07.png) 
9. [Single Sign-on] の設定ダイアログで、次の手順を実行します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_08.png)   
  1. **[Enable Single Sign-on (シングル サインオンを有効にする)]** をクリックします。
  2. Azure クラシック ポータルの **[TargetProcess でのシングル サインオンの構成]** ページで **[シングル サインオン サービス URL]** の値をコピーし、**[Sign-on URL (シングル サインオン URL)]** ボックスに貼り付けます。
  3. ダウンロードした証明書をメモ帳で開き、その内容をコピーして、**[Certificate]** ボックスに貼り付けます。
  4. **[Enable JIT Provisioning]**をクリックします。
10. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。 
   
    ![Azure AD のシングル サインオン][10]
11. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

* ユーザーの一覧で **[Britta Simon]**を選択します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_09.png)  
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_05.png)   
  1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。 
  2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
  3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。 
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_06.png) 
  1. **[名]** ボックスに「**Britta**」と入力します。  
  2. **[姓]** ボックスに「**Simon**」と入力します。
  3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
  4. **[ロール]** 一覧で **[ユーザー]** を選択します。
  5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_08.png) 
  1. **[新しいパスワード]** の値を書き留めます。
  2. ページの下部にある **[完了]**」を参照してください。   

### <a name="create-a-targetprocess-test-user"></a>TargetProcess テスト ユーザーの作成
このセクションの目的は、TargetProcess で Britta Simon というユーザーを作成することです。

TargetProcess では、Just-In-Time プロビジョニングがサポートされています。 この機能は、「 [Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)」で既に有効にしています。

このセクションでは、ユーザー側で必要な操作はありません。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に TargetProcess へのアクセスを許可し、このユーザーが Azure AD の SSO を使用できるようにすることです。

![ユーザーの割り当て][200] 

**Britta Simon を TargetProcess に割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[TargetProcess]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_09.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで [TargetProcess] タイルをクリックすると、TargetProcess アプリケーションに自動的にサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_205.png








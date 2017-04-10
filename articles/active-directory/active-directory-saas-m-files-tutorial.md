---
title: "チュートリアル: Azure Active Directory と M-Files の統合 | Microsoft Docs"
description: "Azure Active Directory と M-Files の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 4536fd49-3a65-4cff-9620-860904f726d0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4a07f946d396a9263d5b00202cd5229ddc86d1be
ms.openlocfilehash: 78f0a065b675326a9a507e0cf480bc779fd458a9
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>チュートリアル: Azure Active Directory と M-Files の統合
このチュートリアルでは、M-Files と Azure Active Directory (Azure AD) を統合する方法について説明します。

M-Files と Azure AD の統合には、次の利点があります。

* M-Files にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に M-Files にシングル サインオン (SSO) できるようにします
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
M-Files と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* **M-Files** での SSO が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの M-Files の追加
2. Azure AD SSO の構成とテスト

## <a name="adding-m-files-from-the-gallery"></a>ギャラリーからの M-Files の追加
Azure AD への M-Files の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に M-Files を追加する必要があります。

**ギャラリーから M-Files を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「**M-Files**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_01.png)
7. 結果ウィンドウで **[M-Files]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、M-Files で Azure AD の SSO を構成し、テストします。

SSO を機能させるには、Azure AD ユーザーに対応する M-Files ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと M-Files の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を M-Files の **[Username]** の値として割り当てます。 M-Files で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[M-Files テスト ユーザーの作成](#creating-a-m-file-test-user)** - M-Files で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-sso"></a>Azure AD SSO の構成
このセクションの目的は、Azure クラシック ポータルで Azure AD の SSO を有効にすることと、M-Files アプリケーションで SSO を構成することです。

**M-Files で Azure AD SSO を構成するには、次の手順に従います。**

1. クラシック ポータルの **M-Files** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![Configure Single Sign-On][7] 
2. **[ユーザーの M-Files へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_06.png)
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_07.png)
  1. [サインオン URL] ボックスに、`https://<tenant-name>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso` という形式で URL を入力します。
  2. **[次へ]**をクリックします。
4. **[M-Files でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、コンピューターにファイルを保存します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_09.png)
5. アプリケーション用に構成された SSO を入手するには、<mailto:support@m-files.com> を使用して M-Files サポート チームに連絡して、ダウンロードしたメタデータを提供してください。
   
   >[!NOTE]
   >M-File デスクトップ アプリケーション用に SSO を構成する場合は、次の手順に従います。 M-Files の Web バージョン用に SSO を構成するだけの場合は、追加の手順は必要ありません。  
   > 
6. 次の手順に従って M-File デスクトップ アプリケーションを構成し、Azure AD の SSO を有効にします。 M-Files をダウンロードするには、[M-Files のダウンロード](https://www.m-files.com/en/download-latest-version)ページに移動します。
7. **[M-Files デスクトップ設定]** ウィンドウを開きます。 **[追加]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_10.png)
8. **[Document Vault Connection Properties] \(資格情報コンテナーの接続プロパティのドキュメント化)** ウィンドウで、次の手順を実行します。
   
  ![[シングル サインオンの構成]](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_11.png)  
    **[サーバー]** セクションで、次のように値を入力します。  
 1. **[名前]** は「`<tenant-name>.cloudvault.m-files.com`」と入力します。  
 2. **[ポート番号]** は「**4466**」と入力します。 
 3. **[プロトコル]** は **[HTTPS]** を選択します。 
 4. **[認証]** フィールドで、**[特定の Windows ユーザー]** を選択します。 署名の入力を求められます。 Azure AD の資格情報を入力してください。 
 5. **[Vault on Server] \(サーバーの資格情報コンテナー)** は、サーバー上の対応する資格情報コンテナーを選択します。 
 6. **[OK]**をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-m-files-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-m-files-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-m-files-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-m-files-tutorial/create_aaduser_05.png) 
 1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。  
 2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。 
 3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-m-files-tutorial/create_aaduser_06.png)  
 1. **[名]** ボックスに「**Britta**」と入力します。   
 2. **[姓]** ボックスに「**Simon**」と入力します。 
 3. **[表示名]** ボックスに「**Britta Simon**」と入力します。 
 4. **[ロール]** 一覧で **[ユーザー]** を選択します。 
 5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-m-files-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-m-files-tutorial/create_aaduser_08.png)  
 1. **[新しいパスワード]** の値を書き留めます。 
 2. **[完了]** をクリックします。   

### <a name="create-a-m-files-test-user"></a>M-Files テスト ユーザーの作成
このセクションでは、M-Files で Britta Simon というユーザーを作成します。 M-Files でユーザーを作成する方法がわからない場合は、M-Files サポート チーム (<mailto:support@m-files.com>) に連絡してください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に M-Files へのアクセスを許可することで、このユーザーが Azure SSO を使用できるようにします。

![ユーザーの割り当て][200]

**M-Files に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[M-Files]** を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_12.png)
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. [すべてのユーザー] の一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで M-Files のタイルをクリックすると、自動的に M-Files アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-m-files-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_205.png


---
title: "チュートリアル: Azure Active Directory と TiViTz の統合 | Microsoft Docs"
description: "Azure Active Directory と TiViTz の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b97ed88f-7888-4185-be22-41d1f62cbbf1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/07/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 06debc64a44b58b8cb1fb50aa8af27f22a61d086
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-tivitz"></a>チュートリアル: Azure Active Directory と TiViTz の統合

このチュートリアルでは、TiViTz と Azure Active Directory (Azure AD) を統合する方法について説明します。

TiViTz と Azure AD の統合には、次の利点があります。

- TiViTz にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に TiViTz にシングル サインオン (SSO) できるようにします。
- 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure AD と TiViTz の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- TiViTz での SSO が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
>

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの TiViTz の追加
2. Azure AD SSO の構成とテスト


## <a name="add-tivitz-from-the-gallery"></a>ギャラリーからの TiViTz の追加
Azure AD への TiViTz の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に TiViTz を追加する必要があります。

**ギャラリーから TiViTz を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 

    ![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![[アプリケーション]][2]

4. ページの下部にある **[追加]** をクリックします。

    ![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![アプリケーション][4]

6. 検索ボックスに、「**TiViTz**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tivitz-tutorial/tutorial_tivitz_001.png)

7. 結果ウィンドウで **[TiViTz]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tivitz-tutorial/tutorial_tivitz_0001.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、TiViTz で Azure AD の SSO を構成し、テストします。

SSO を機能させるには、Azure AD ユーザーに対応する TiViTz ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと TiViTz の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を TiViTz の **[Username]** の値として割り当てます。

TiViTz で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[TiViTz のテスト ユーザーの作成](#creating-a-tivitz-test-user)** - TiViTz で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、クラシック ポータルで Azure AD の SSO を有効にして、TiViTz アプリケーションでシングル サインオンを構成します。

**TiViTz で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. クラシック ポータルの **TiViTz** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![[シングル サインオンの構成]][6]

2. **[ユーザーの TiViTz へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-tivitz-tutorial/tutorial_tivitz_02.png)

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-tivitz-tutorial/tutorial_tivitz_03.png)
 1. **[サインオン URL]** ボックスに、`https://<company name>.o365.tivitz.com/` のパターンを使用して URL を入力します。
 2. **[識別子]** ボックスに、`https://<company name>.o365.tivitz.com/` の形式で URL を入力します。
 3. **[次へ]**をクリックします。

     >[!NOTE] 
     >これは実際の値ではないので注意してください。 実際のサインオン URL と識別子でこれらの値を更新する必要があります。 これらの値を取得するには、[TiViTz サポート チーム](emaiLto:info@tivitz.com)に問い合わせてください。
     >

4. **[TiViTz でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックしてメタデータをダウンロードし、コンピューターにファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-tivitz-tutorial/tutorial_tivitz_04.png) 

5. アプリケーション用に構成された SSO を入手するには、[TiViTz サポート チーム](emaiLto:info@tivitz.com)に連絡して、ダウンロードした**メタデータ**を提供してください。

6. クラシック ポータルで、シングル サインオンの構成確認を選択し、**[次へ]**をクリックします。

    ![Azure AD のシングル サインオン][10]

7. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
  
    ![Azure AD のシングル サインオン][11]


### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tivitz-tutorial/create_aaduser_09.png) 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tivitz-tutorial/create_aaduser_03.png) 

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tivitz-tutorial/create_aaduser_04.png) 

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tivitz-tutorial/create_aaduser_05.png) 
 1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
 2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
 3. **[次へ]**をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tivitz-tutorial/create_aaduser_06.png) 
 1. **[名]** ボックスに「**Britta**」と入力します。  
 2. **[姓]** ボックスに「**Simon**」と入力します。
 3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
 4. **[ロール]** 一覧で **[ユーザー]** を選択します。
 5. **[次へ]**をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tivitz-tutorial/create_aaduser_07.png) 

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tivitz-tutorial/create_aaduser_08.png) 
 1. **[新しいパスワード]** の値を書き留めます。
 2. **[完了]** をクリックします。   

### <a name="create-a-tivitz-test-user"></a>TiViTz のテスト ユーザーの作成

このセクションの目的は、TiViTz で Britta Simon というユーザーを作成することです。 TiViTz では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 ユーザーが存在しない場合は、TiViTz へのアクセスを試みたときに、新しいユーザーが自動的に作成されます。

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、[TiViTz のサポート チーム](emaiLto:info@tivitz.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に TiViTz へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を TiViTz に割り当てるには、次の手順を実行します。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[TiViTz]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-tivitz-tutorial/tutorial_tivitz_50.png) 

3. 上部のメニューで **[ユーザー]**をクリックします。

    ![ユーザーの割り当て][203] 

4. ユーザーの一覧で **[Britta Simon]**を選択します。

5. 下部にあるツール バーで **[割り当て]**をクリックします。
    
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [TiViTz] タイルをクリックすると、TiViTz アプリケーションに自動的にサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tivitz-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tivitz-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tivitz-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tivitz-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-tivitz-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-tivitz-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-tivitz-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-tivitz-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tivitz-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tivitz-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-tivitz-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-tivitz-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-tivitz-tutorial/tutorial_general_205.png


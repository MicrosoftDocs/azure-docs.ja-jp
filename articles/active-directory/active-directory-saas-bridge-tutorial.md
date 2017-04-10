---
title: "チュートリアル: Azure Active Directory と Bridge の統合 | Microsoft Docs"
description: "Azure Active Directory と Bridge の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 3dbb6499-80c1-4d00-a0b4-e0ad5522cf0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 2250fbe0b7451d875ccdd319af9b77cb4e24fe35
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-bridge"></a>チュートリアル: Azure Active Directory と Bridge の統合

このチュートリアルでは、Bridge と Azure Active Directory (Azure AD) を統合する方法について説明します。

Bridge と Azure AD の統合には、次の利点があります。

- Bridge にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Bridge にシングル サインオン (SSO) できるようにすることが可能です。
- 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Bridge の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Bridge での SSO が有効なサブスクリプション


>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
>

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。


## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD SSO をテストします。 

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Bridge の追加
2. Azure AD SSO の構成とテスト


## <a name="add-bridge-from-the-gallery"></a>ギャラリーからの Bridge の追加
Azure AD への Bridge の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Bridge を追加する必要があります。

**ギャラリーから Bridge を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 

    ![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![[アプリケーション]][2]

4. ページの下部にある **[追加]** をクリックします。

    ![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![アプリケーション][4]

6. 検索ボックスに、「**Bridge**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bridge-tutorial/tutorial_bridge_001.png)

7. 結果ウィンドウで **[Bridge]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bridge-tutorial/tutorial_bridge_0001.png)


##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Bridge で Azure AD の SSO を構成し、テストします。

SSO を機能させるには、Azure AD ユーザーに対応する Bridge ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Bridge の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Bridge の **[Username (ユーザー名)]** の値として割り当てます。

Bridge で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Bridge テスト ユーザーの作成](#creating-a-bridge-test-user)** - Bridge で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、クラシック ポータルで Azure AD の SSO を有効にして、Bridge アプリケーションでシングル サインオンを構成します。


**Bridge で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. クラシック ポータルの **Bridge** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![[シングル サインオンの構成]][6]

2. **[ユーザーの Bridge へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-bridge-tutorial/tutorial_bridge_02.png)

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-bridge-tutorial/tutorial_bridge_03.png)
  1. **[サインオン URL]** ボックスに、`https://<company name>.bridgeapp.com` のパターンを使用して URL を入力します。
  2. **[識別子]** ボックスに、`https://<company name>.bridgeapp.com` の形式で URL を入力します。
  3. **[次へ]**をクリックします。

    >[!NOTE] 
    >これは実際の値ではないので注意してください。 実際のサインオン URL と識別子でこれらの値を更新する必要があります。 <a href="https://bridgeapp.zendesk.com/hc/en-us/requests/new">こちら</a>から Bridge へのサポート チケットを取得して、これらの値を取得できます。
    >

4. **[Bridge でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-bridge-tutorial/tutorial_bridge_04.png) 

5. アプリケーション用に構成された SSO を入手するには、<a href="https://bridgeapp.zendesk.com/hc/en-us/requests/new">こちら</a>から Bridge サポート チームに連絡し、以下の情報を提供してサポート チケット取得します。 

 *  ダウンロードした**証明書ファイル**
 *  **エンティティ ID**
 *  **シングル サインオン サービス URL**
 *  **シングル サインアウト サービス URL**

6. クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。

    ![Azure AD のシングル サインオン][10]

7. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
  
    ![Azure AD のシングル サインオン][11]


### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bridge-tutorial/create_aaduser_09.png) 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bridge-tutorial/create_aaduser_03.png) 

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bridge-tutorial/create_aaduser_04.png) 

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bridge-tutorial/create_aaduser_05.png) 
 1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
 2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
 3. **[次へ]**をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bridge-tutorial/create_aaduser_06.png) 
 1. **[名]** ボックスに「**Britta**」と入力します。  
 2. **[姓]** ボックスに「**Simon**」と入力します。
 3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
 4. **[ロール]** 一覧で **[ユーザー]** を選択します。
 5. **[次へ]**をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bridge-tutorial/create_aaduser_07.png) 

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bridge-tutorial/create_aaduser_08.png) 
 1. **[新しいパスワード]** の値を書き留めます。
 2. ページの下部にある **[完了]**」を参照してください。   

### <a name="create-a-bridge-test-user"></a>Bridge テスト ユーザーの作成

このセクションでは、Bridge で Britta Simon というユーザーを作成します。 Bridge サポート チームと協力して、プラットフォームにユーザーを作成します。 <a href="https://bridgeapp.zendesk.com/hc/en-us/requests/new">こちら</a>から Bridge へのサポート チケットを取得して、Bridge プラットフォームでユーザーを追加できます。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Bridge へのアクセスを許可することで、このユーザーが Azure SSO を使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を Bridge に割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Bridge]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-bridge-tutorial/tutorial_bridge_50.png) 

3. 上部のメニューで **[ユーザー]**をクリックします。

    ![ユーザーの割り当て][203] 

4. ユーザーの一覧で **[Britta Simon]**を選択します。

5. 下部にあるツール バーで **[割り当て]**をクリックします。
    
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD の SSO 構成をテストします。

アクセス パネルで [Bridge] タイルをクリックすると、自動的に Bridge アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bridge-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bridge-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bridge-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bridge-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-bridge-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-bridge-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-bridge-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-bridge-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bridge-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bridge-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-bridge-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-bridge-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-bridge-tutorial/tutorial_general_205.png


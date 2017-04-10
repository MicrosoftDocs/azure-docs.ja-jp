---
title: "チュートリアル: Azure Active Directory と ClickTime の統合 | Microsoft Docs"
description: "Azure Active Directory で ClickTime を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: d437b5ab-4d71-4c13-96d0-79018cebbbd4
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0837cb33bf438fb7fd9665d21d411f0170cdd393
ms.openlocfilehash: d5171cdc9048837385bfb99d553e496a9f56846e
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-clicktime"></a>チュートリアル: Azure Active Directory と ClickTime の統合
このチュートリアルでは、ClickTime と Azure Active Directory (Azure AD) を統合する方法について説明します。

ClickTime と Azure AD の統合には、次の利点があります。

* ClickTime にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで ClickTime に自動的にサインオン (シングル サインオン) できるように、設定が可能です。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
ClickTime と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* ClickTime でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
> 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの ClickTime の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-clicktime-from-the-gallery"></a>ギャラリーからの ClickTime の追加
このセクションでは、ClickTime のアプリケーション統合を有効にする方法を説明します。

### <a name="to-enable-the-application-integration-for-clicktime-perform-the-following-steps"></a>ClickTime のアプリケーション統合を有効にするには、次の手順を実行します。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-clicktime-tutorial/tic700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-clicktime-tutorial/tic700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-clicktime-tutorial/tic749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-clicktime-tutorial/tic749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**ClickTime**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-clicktime-tutorial/tic777275.png "Application Gallery")
7. 結果ウィンドウで **[ClickTime]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![ClickTime](./media/active-directory-saas-clicktime-tutorial/tic777276.png "ClickTime")

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、ClickTime で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する ClickTime ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと ClickTime の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を ClickTime の **[Username (ユーザー名)]** の値として割り当てます。

ClickTime で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[ClickTime のテスト ユーザーの作成](#creating-a-clicktime-test-user)** - ClickTime で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで ClickTime に対する認証を行うことができるようにする方法を説明します。  

> [!IMPORTANT]
> ClickTime テナントでシングル サインオンを構成できるようにするには、まず ClickTime テクニカル サポートに連絡してこの機能を有効にする必要があります。
> 
> 

**ClickTime で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **ClickTime** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの有効化](./media/active-directory-saas-clicktime-tutorial/tic777277.png "シングル サインオンの有効化")
2. **[ユーザーの ClickTime へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-clicktime-tutorial/tic777278.png "Configure single sign-on")
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-clicktime-tutorial/tic777286.png) 
   
    a. **[識別子]** ボックスに、**https://app.clicktime.com/sp/** の形式で URL を入力します。
   
    b. **[応答 URL]** ボックスに、**https://app.clicktime.com/Login/** の形式で URL を入力します。
   
    c. click **[次へ]**
4. **[ClickTime でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルをコンピューターに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-clicktime-tutorial/tic777279.png "Configure single sign-on")
5. 別の Web ブラウザー ウィンドウで、ClickTime 企業サイトに管理者としてログインします。
6. 上部にあるツールバーで **[Preferences]**、**[Security Settings]** の順にクリックします。
7. **[Single Sign-On Preferences]** 構成セクションで、次の手順を実行します。
   
   ![Security Settings](./media/active-directory-saas-clicktime-tutorial/tic777280.png "Security Settings")
   
   a.  **[Azure AD]** を指定し、シングル サインオン (SSO) を使用したサインインの **[Allow (許可)]** を選択します。
   
   b.  Azure クラシック ポータルの **[ClickTime でのシングル サインオンの構成]** ダイアログ ページで、**[シングル サインオン サービス URL]** の値をコピーし、**[Identity Provider Endpoint (ID プロバイダー エンドポイント)]** ボックスに貼り付けます。
   
   c.  base-64 でエンコードされた証明書を**メモ帳**で開き、その内容をコピーして、**[X.509 証明書]** テキストボックスに貼り付けます。
   
   d.  **[Save]**をクリックします。
8. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-clicktime-tutorial/tic777281.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成
Azure AD ユーザーが ClickTime にログインできるようにするには、そのユーザーを ClickTime にプロビジョニングする必要があります。  
ClickTime の場合、プロビジョニングは手動で行います。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順に従います。
1. **ClickTime** テナントにログインします。
2. 上部のツールバーで、**[Company]** をクリックし、**[People]** をクリックします。
   
   ![ユーザー](./media/active-directory-saas-clicktime-tutorial/tic777282.png "People")
3. **[Add Person]**をクリックします。
   
   ![Add Person](./media/active-directory-saas-clicktime-tutorial/tic777283.png "Add Person")
4. [New Person] セクションで、次の手順を実行します。
   
   ![ユーザー](./media/active-directory-saas-clicktime-tutorial/tic777284.png "People")
   
   a.  **[email address]** テキストボックスに Azure AD アカウントの電子メール アドレスを入力します。
   
   b.  **[full name]** テキストボックスに、Azure AD アカウントの名前を入力します。  
   
   > [!NOTE]
   > 必要に応じて、新しいユーザー オブジェクトの追加プロパティを設定できます。
   > 
   > 
   
   c.  **[Save]**をクリックします。

> [!NOTE]
> ClickTime から提供されている他の ClickTime ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に ClickTime へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200]

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ClickTime に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[ClickTime]**を選択します。
   
    ![Configure single sign-on](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203]
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

## <a name="testing-single-sign-on"></a>シングル サインオンのテスト
このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [ClickTime] タイルをクリックすると、ClickTime アプリケーションに自動的にサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[200]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_203.png
[205]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_205.png


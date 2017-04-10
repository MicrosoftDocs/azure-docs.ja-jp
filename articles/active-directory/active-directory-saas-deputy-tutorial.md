---
title: "チュートリアル: Azure Active Directory と Deputy の統合 | Microsoft Docs"
description: "Azure Active Directory と Deputy の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 5665c3ac-5689-4201-80fe-fcc677d4430d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 04a045f41965b093aab71e59cd9b5f328b44de84
ms.openlocfilehash: c853d61257493d73cd0f8a51a15f0389e1e83cf4
ms.lasthandoff: 02/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-deputy"></a>チュートリアル: Azure Active Directory と Deputy の統合
このチュートリアルの目的は、Deputy と Azure Active Directory (Azure AD) を統合する方法を説明することです。

Deputy と Azure AD の統合には、次の利点があります。

* Deputy にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが各自の Azure AD アカウントで Deputy に自動的にサインオン (シングル サインオン) するように、設定が可能です。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Deputy と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* Deputy でのシングル サインオン (SSO) が有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
> 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの Deputy の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-deputy-from-the-gallery"></a>ギャラリーからの Deputy の追加
Azure AD への Deputy の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Deputy を追加する必要があります。

**ギャラリーから Deputy を追加するには、次の手順を実行します。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **Deputy**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_01.png)
7. 結果ウィンドウで **[Deputy]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![ギャラリーでアプリを選択する](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_0001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Deputy で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Deputy ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Deputy の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Deputy の **[Username]** の値として割り当てます。

Deputy で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Deputy テスト ユーザーの作成](#creating-a-deputy-test-user)** - Deputy で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、Deputy アプリケーションでシングル サインオンを構成します。

**Deputy で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. クラシック ポータルの **Deputy** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 
2. **[ユーザーの Deputy へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_03.png)
3. **[アプリケーション設定の構成]** ダイアログ ページで、**IDP 開始モード**でアプリケーションを構成する場合は、次の手順を実行し、**[次へ]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_04.png)
  1. **[識別子]** ボックスに、`https://<your-subdomain>.<region>.deputy.com` のパターンを使用して URL を入力します。
  2. **[応答 URL]** ボックスに、`https://<your-subdomain>.<region>.deputy.com/exec/devapp/samlacs` のパターンを使用して URL を入力します。
  3. **[次へ]**をクリックします。
4. **[アプリケーション設定の構成]** ダイアログ ページで、**SP 開始モード**でアプリケーションを構成する場合は、**[詳細設定を表示します (オプション)]** をクリックし、**サインオン URL** を入力して、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_05.png)
   1. **[サインオン URL]** ボックスに、`https://<your-subdomain>.<region>.deputy.com` のパターンを使用して URL を入力します。
   2. **[次へ]**をクリックします。
   
   > [!NOTE]
   > Deputy リージョン サフィックスは オプションです。または次のいずれかを使用する必要があります。au | na | eu |as |la |af |an |ent-au |ent-na |ent-eu |ent-as | ent-la | ent-af | ent-an
   > 
   > 
5. **[Deputy でのシングル サインオンの構成]** ページで、次の手順を実行し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_06.png)
   1. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。
6. https://(your-subdomain).deputy.com/exec/config/system_config に移動します。 **[Security Settings (セキュリティの設定)]** に移動し、**[編集]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_004.png)
7. Azure クラシック ポータルの [Deputy でのシングル サインオンの構成] ページで、SAML SSO URL をコピーします。 
8. この **[Security Settings (セキュリティの設定)]** ページで、次の手順を実行します。
   
![[シングル サインオンの構成]](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_005.png)

   1. **ソーシャル ログイン**を有効にします。
   2. Base64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[OpenSSL Certificate (OpenSSL 証明書)]** ボックスに貼り付けます。
   3. [SAM SSO URL] ボックスに、「`https://<your subdomain>.deputy.com/exec/devapp/samlacs?dpLoginTo=<saml sso url>`」と入力します。
   4. [SAM SSO URL] ボックスで、`<your subdomain>` をサブドメインに置き換えます。
   5. [SAM SSO URL] ボックスで、`<saml sso url>` を Azure クラシック ポータルからコピーした SAML SSO URL に置き換えます。
   6. **[設定の保存]**をクリックします。
9. クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
10. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
    
    ![Azure AD のシングル サインオン][11]

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-deputy-tutorial/create_aaduser_09.png)
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-deputy-tutorial/create_aaduser_03.png)
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-deputy-tutorial/create_aaduser_04.png)
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-deputy-tutorial/create_aaduser_05.png)

   1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
   3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-deputy-tutorial/create_aaduser_06.png)
   
   1. **[名]** ボックスに「**Britta**」と入力します。  
   2. **[姓]** ボックスに「**Simon**」と入力します。
   3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   4. **[ロール]** 一覧で **[ユーザー]** を選択します。
   5. **[次へ]**をクリックします。
   
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-deputy-tutorial/create_aaduser_07.png)
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-deputy-tutorial/create_aaduser_08.png)
   
   1. **[新しいパスワード]** の値を書き留めます。
   2. **[完了]** をクリックします。   

### <a name="creating-a-deputy-test-user"></a>Deputy テスト ユーザーの作成
Azure AD ユーザーが Deputy にログインできるようにするには、そのユーザーを Deputy にプロビジョニングする必要があります。 Deputy の場合、プロビジョニングは手動で行います。

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順を実行します。
1. Deputy 企業サイトに管理者としてログインします。
2. ナビゲーション ウィンドウの上部にある **[ユーザー]**をクリックします。
   
   ![ユーザー](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_001.png "People")
3. **[ユーザーの追加]** ボタンをクリックし、**[Add a single person (1 人のユーザーの追加)]** をクリックします。
   
   ![ユーザーの追加](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_002.png "ユーザーの追加")
4. 次の手順を実行し、**[Save & Invite (保存および招待)]** をクリックします。
   
   ![New User](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_003.png "New User")
   
  1. **[名前]** ボックスに「**Britta**」および「**Simon**」と入力します。  
  2. **[電子メール]** ボックスに、プロビジョニングする Azure AD アカウントの電子メール アドレスを入力します。
  3. **[Work at (勤務先)]** ボックスに、勤務先の名前を入力します。
  4. **[Save & Invite (保存および招待)]** ボタンをクリックします。
   
   > [!NOTE]
   > AAD アカウント所有者がメールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。 Deputy から提供されている他の Deputy ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
   > 
   > 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に Deputy へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][200]

**Britta Simon を Deputy に割り当てるには、次の手順を実行します。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201]
2. アプリケーションの一覧で **[Deputy]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_50.png)
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203]
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで [Deputy] タイルをクリックすると、Deputy アプリケーションに自動的にサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_205.png


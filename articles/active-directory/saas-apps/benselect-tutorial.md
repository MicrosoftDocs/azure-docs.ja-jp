---
title: チュートリアル:Azure Active Directory と BenSelect の統合 | Microsoft Docs
description: Azure Active Directory と BenSelect の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: ffa17478-3ea1-4356-a289-545b5b9a4494
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 699afd4703efc5e8f63bb13fe1dd753a0c72594d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56209440"
---
# <a name="tutorial-azure-active-directory-integration-with-benselect"></a>チュートリアル:Azure Active Directory と BenSelect の統合

このチュートリアルでは、BenSelect と Azure Active Directory (Azure AD) を統合する方法について説明します。

BenSelect と Azure AD の統合には、次の利点があります。

- BenSelect にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に BenSelect にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

BenSelect と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- BenSelect でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの BenSelect の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-benselect-from-the-gallery"></a>ギャラリーからの BenSelect の追加
Azure AD への BenSelect の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に BenSelect を追加する必要があります。

**ギャラリーから BenSelect を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「 **BenSelect**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/benselect-tutorial/tutorial_benselect_search.png)

1. 結果ウィンドウで **[BenSelect]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/benselect-tutorial/tutorial_benselect_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" という名前のテスト ユーザーに基づいて、BenSelect で Azure AD のシングル サインオンを構成およびテストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する BenSelect ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと BenSelect の関連ユーザーの間で、リンク関係が確立されている必要があります。

BenSelect で、Azure AD での **[ユーザー名]** の値を **[ユーザー名]** の値として割り当てることによってリンク関係を確立します。

BenSelect で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[BenSelect テスト ユーザーの作成](#creating-a-benselect-test-user)** - Azure AD でのユーザーにリンクされた、BenSelect での Britta Simon の対応するユーザーを作成します。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、BenSelect アプリケーションでシングル サインオンを構成します。

**BenSelect で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **[BenSelect]** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/benselect-tutorial/tutorial_benselect_samlbase.png)

1. **[BenSelect Domain and URLs] \(BenSelect のドメインと URL)** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/benselect-tutorial/tutorial_benselect_url.png)

    **[応答 URL]** ボックスに、`https://www.benselect.com/enroll/login.aspx?Path=<tenant name>` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これは実際の値ではありません。 実際の応答 URL でこの値を更新します。 この値を取得するには、[BenSelect サポート チーム](mailto:support@selerix.com)に問い合わせてください。
 
1. **[SAML 署名証明書]** セクションで、**[Certificate(Raw)] \(証明書 (Raw))** をクリックし、証明書ファイルをコンピューターに保存します。

    ![Configure single sign-on](./media/benselect-tutorial/tutorial_benselect_certificate.png) 

1. BenSelect アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 次のスクリーンショットはその例です。

    ![Configure single sign-on](./media/benselect-tutorial/tutorial_benselect_06.png)

1. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、次の手順を実行します。

    a. **[User Identifier] \(ユーザー識別子)** ドロップダウン リストで、**[ExtractMailPrefix]** を選択します。

    b. **[Mail] \(メール)** ドロップダウン リストで、**[user.userprincipalname]** を選択します。

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/benselect-tutorial/tutorial_general_400.png)

1. **[BenSelect Configuration] \(BenSelect 構成)** セクションで、**[Configure BenSelect] \(BenSelect の構成)** をクリックして **[Configure sign-on] \(サインオンの構成)** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/benselect-tutorial/tutorial_benselect_configure.png) 

1. **BenSelect** 側にシングル サインオンを構成するには、ダウンロードされた**証明書 (Raw)**、**サインアウト URL、SAML エンティティ ID、および SAML シングル サインオン サービス URL** を [BenSelect サポート チーム](mailto:support@selerix.com)に送信する必要があります。

   >[!NOTE]
   >適切なサーバー (app2101 など) で SSO を設定するために、この統合には SHA256 アルゴリズム (SHA1 はサポート対象外) が必要であるという点を伝える必要があります。 
   
> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 埋め込みドキュメント機能の詳細については、[Azure AD の埋め込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/benselect-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/benselect-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/benselect-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/benselect-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。
 
### <a name="creating-a-benselect-test-user"></a>BenSelect テスト ユーザーの作成

このセクションの目的は、BenSelect で Britta Simon というユーザーを作成することです。 [BenSelect サポート チーム](mailto:support@selerix.com)と協力して、BenSelect アカウントでユーザーを追加します。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、BenSelect へのアクセス権を付与することによって、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**BenSelect に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[BenSelect]** を選択します。

    ![Configure single sign-on](./media/benselect-tutorial/tutorial_benselect_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD の SSO 構成をテストします。

アクセス パネルで [BenSelect] タイルをクリックすると、自動的に BenSelect アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/benselect-tutorial/tutorial_general_01.png
[2]: ./media/benselect-tutorial/tutorial_general_02.png
[3]: ./media/benselect-tutorial/tutorial_general_03.png
[4]: ./media/benselect-tutorial/tutorial_general_04.png

[100]: ./media/benselect-tutorial/tutorial_general_100.png

[200]: ./media/benselect-tutorial/tutorial_general_200.png
[201]: ./media/benselect-tutorial/tutorial_general_201.png
[202]: ./media/benselect-tutorial/tutorial_general_202.png
[203]: ./media/benselect-tutorial/tutorial_general_203.png


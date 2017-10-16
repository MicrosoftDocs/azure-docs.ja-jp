---
title: "チュートリアル: Azure Active Directory と etouches の統合 | Microsoft Docs"
description: "Azure Active Directory と etouches の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 76cccaa8-859c-4c16-9d1d-8a6496fc7520
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 3cd9e9d6aae924369065ca492b1f6380c0ddc5fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-etouches"></a>チュートリアル: Azure Active Directory と etouches の統合

このチュートリアルでは、etouches と Azure Active Directory (Azure AD) を統合する方法について説明します。

etouches と Azure AD の統合には、次の利点があります。

- etouches にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に etouches にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

etouches と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- etouches でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの etouches の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-etouches-from-the-gallery"></a>ギャラリーからの etouches の追加
Azure AD への etouches の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に etouches を追加する必要があります。

**ギャラリーから etouches を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**etouches**」と入力し、結果ウィンドウで **etouches** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の etouches](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、etouches で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する etouches ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと etouches の関連ユーザーの間で、リンク関係が確立されている必要があります。

etouches で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

etouches で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[etouches のテスト ユーザーの作成](#create-an-etouches-test-user)** - etouches で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、etouches アプリケーションでシングル サインオンを構成します。

**etouches で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **etouches** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_samlbase.png)

3. **[etouches のドメインと URL]** セクションで、次の手順に従います。

    ![[etouches のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_url.png)

    a. **[サインオン URL]** ボックスに、`https://www.eiseverywhere.com/saml/accounts/?sso&accountid=<ACCOUNTID>` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://www.eiseverywhere.com/<instance name>` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子に値を置き換えます。実際の値については後で説明します。
    > 

4. etouches アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーションの **[ユーザー属性]** から管理できます。 次のスクリーンショットはその例です。 

    ![ユーザー属性](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_attribute.png) 

5. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | ------------------- | -------------------- |
    | 電子メール | User.mail |    
    
    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![[属性の追加]](./media/active-directory-saas-etouches-tutorial/tutorial_attribute_04.png)

    ![[属性の追加] ダイアログ](./media/active-directory-saas-etouches-tutorial/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。
    
    d. **[OK]**をクリックします。 

6. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_certificate.png) 

7. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-etouches-tutorial/tutorial_general_400.png)

8. お使いのアプリケーション用に構成された SSO を取得するには、etouches アプリケーションで次の手順を実行します。 

    ![etouches の構成](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_06.png) 

    a. 管理者権限を使用して **etouches** アプリケーションにログインします。
   
    b. **[SAML 構成]** に移動します。

    c. **[General Settings]\(全般設定\)** セクションで、Azure Portal からダウンロードした証明書をメモ帳で開き、内容をコピーして、[IDP metadata]\(IDP メタデータ\) ボックスに貼り付けます。 

    d. **[Save & Stay (保存のみ)]** ボタンをクリックします。
  
    e. [SAML Metadata (SAML メタデータ)] セクションの **[Update Metadata (メタデータの更新)]** をクリックします。 

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 ページが開き、SSO が実行されます。 SSO が実行されたら、ユーザー名を設定できます。

    g. [Username]\(ユーザー名\) フィールドで、次の画像に示すように **emailaddress** を選びます。 

    h. **SP エンティティ ID** の値をコピーし、Azure Portal の **[etouches のドメインと URL]** セクションの **[識別子]** ボックスに貼り付けます。

    i. **SSO URL/ACS** の値をコピーし、Azure Portal の **[etouches のドメインと URL]** セクションの **[サインオン URL]** ボックスに貼り付けます。
   
> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-etouches-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-etouches-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![[追加] ボタン](./media/active-directory-saas-etouches-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-etouches-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="create-an-etouches-test-user"></a>etouches テスト ユーザーの作成

このセクションでは、etouches で Britta Simon というユーザーを作成します。 [etouches クライアント サポート チーム](https://www.etouches.com/event-software/support/customer-support/)と協力して、etouches プラットフォームにユーザーを追加します。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に etouches へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**etouches に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[etouches]**を選択します。

    ![アプリケーションの一覧の etouches のリンク](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト


このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで etouches のタイルをクリックすると、自動的に etouches アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_203.png


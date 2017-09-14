---
title: "チュートリアル: Azure Active Directory と New Relic の統合 | Microsoft Docs"
description: "Azure Active Directory と New Relic の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 605e85c23a849f70fcc0237361d7a891f716ca3a
ms.contentlocale: ja-jp
ms.lasthandoff: 07/04/2017


---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>チュートリアル: Azure Active Directory と New Relic の統合

このチュートリアルでは、New Relic と Azure Active Directory (Azure AD) を統合する方法について説明します。

New Relic と Azure AD の統合には、次の利点があります。

- New Relic にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に New Relic にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

New Relic と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- New Relic でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの New Relic の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-new-relic-from-the-gallery"></a>ギャラリーからの New Relic の追加
Azure AD への New Relic の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に New Relic を追加する必要があります。

**ギャラリーから New Relic を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**New Relic**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-new-relic-tutorial/tutorial_newrelic_search.png)

5. 結果ウィンドウで **[New Relic]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-new-relic-tutorial/tutorial_newrelic_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、New Relic で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する New Relic ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと New Relic の関連ユーザーの間で、リンク関係が確立されている必要があります。

New Relic で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

New Relic で Azure AD のシングル サインオンを構成してテストするには、次の一連の作業を完了させる必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[New Relic テスト ユーザーの作成](#creating-a-new-relic-test-user)** - New Relic で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、New Relic アプリケーションでシングル サインオンを構成します。

**New Relic で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **New Relic** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-new-relic-tutorial/tutorial_newrelic_samlbase.png)

3. **[New Relic Domain and URLs]\(New Relic のドメインと URL\)** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-new-relic-tutorial/tutorial_newrelic_url.png)

    **[サインオン URL]** ボックスに、`https://<subdomain>.newrelic.com` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[New Relic クライアント サポート チーム](https://support.newrelic.com/)に問い合わせてください。 
 
4. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-new-relic-tutorial/tutorial_newrelic_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-new-relic-tutorial/tutorial_general_400.png)

6. **[New Relic Configuration]\(New Relic 構成\)** セクションで、**[Configure New Relic]\(New Relic を構成する\)** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-new-relic-tutorial/tutorial_newrelic_configure.png) 

7. 別の Web ブラウザーのウィンドウで、管理者として **New Relic** 企業サイトにサインオンします。

8. 上部のメニューで **[アカウント設定]**をクリックします。
   
    ![Account Settings](./media/active-directory-saas-new-relic-tutorial/ic797036.png "Account Settings")

9. **[セキュリティと認証]** タブをクリックし、**[シングル サインオン]** タブをクリックします。
   
    ![シングル サインオン](./media/active-directory-saas-new-relic-tutorial/ic797037.png "Single Sign-On")

10. SAML ダイアログ ページで、次の手順に従います。
   
    ![SAML](./media/active-directory-saas-new-relic-tutorial/ic797038.png "SAML")
   
   a. **[ファイルの選択]** をクリックして、ダウンロードした Azure Active Directory 証明書をアップロードします。

   b. **[リモート ログイン URL]** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。
   
   c. **[Logout landing URL]\(ログアウト ランディング URL\)** ボックスに、Azure Portal からコピーした **サインアウト URL** の値を貼り付けます。

   d. **[変更を保存する]**をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-new-relic-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-new-relic-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-new-relic-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-new-relic-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-a-new-relic-test-user"></a>New Relic テスト ユーザーの作成

Azure Active Directory ユーザーが New Relic にログインできるようにするには、そのユーザーを New Relic にプロビジョニングする必要があります。 New Relic の場合、プロビジョニングは手動で行います。

**ユーザー アカウントを New Relic にプロビジョニングするには、次の手順に従います。**

1. **New Relic** 企業サイトに管理者としてログインします。

2. 上部のメニューで **[アカウント設定]**をクリックします。
   
    ![Account Settings](./media/active-directory-saas-new-relic-tutorial/ic797040.png "Account Settings")

3. **[アカウント]** ペインの左側にある **[概要]** をクリックし、次に **[ユーザーの追加]** をクリックします。
   
    ![Account Settings](./media/active-directory-saas-new-relic-tutorial/ic797041.png "Account Settings")

4. **[アクティブ ユーザー]** ダイアログで、次の手順に従います。
   
    ![アクティブ ユーザー](./media/active-directory-saas-new-relic-tutorial/ic797042.png "アクティブ ユーザー")
   
    a. **[電子メール]** テキストボックスに、プロビジョニングする有効な Azure Active Directory ユーザーの電子メール アドレスを入力します。

    b. **[ロール]** として **[ユーザー]** を選びます。

    c. **[このユーザーを追加]**をクリックします。

>[!NOTE]
>他の New Relic ユーザー アカウントの作成ツールまたは New Relic から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に New Relic へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**New Relic に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[New Relic]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-new-relic-tutorial/tutorial_newrelic_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで [New Relic] タイルをクリックすると、New Relic アプリケーションに自動的にサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_203.png



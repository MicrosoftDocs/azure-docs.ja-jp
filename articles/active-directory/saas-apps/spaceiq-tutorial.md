---
title: チュートリアル:Azure Active Directory と Slack の統合 | Microsoft Docs
description: Azure Active Directory と SpaceIQ の間でシングル サインオンを構成する方法について確認します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 5b55ae29-491f-401f-9299-d3a6b64a1b99
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6695fe4854c6d91d3d2ba671104d1b481721356c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56162273"
---
# <a name="tutorial-azure-active-directory-integration-with-spaceiq"></a>チュートリアル:Azure Active Directory と SpaceIQ の統合

このチュートリアルでは、SpaceIQ と Azure Active Directory (Azure AD) を統合する方法について説明します。

SpaceIQ と Azure AD の統合には、次の利点があります。

- SpaceIQ にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで SpaceIQ に自動的にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

SpaceIQ と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- SpaceIQ でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの SpaceIQ の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-spaceiq-from-the-gallery"></a>ギャラリーからの SpaceIQ の追加
Azure AD への SpaceIQ の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SpaceIQ を追加する必要があります。

**ギャラリーから SpaceIQ を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**SpaceIQ**」と入力し、結果ウィンドウで **[SpaceIQ]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![結果一覧の SpaceIQ](./media/spaceiq-tutorial/tutorial_spaceiq_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SpaceIQ で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SpaceIQ ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SpaceIQ の関連ユーザーの間で、リンク関係が確立されている必要があります。

SpaceIQ で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

SpaceIQ で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[SpaceIQ のテスト ユーザーの作成](#create-a-spaceiq-test-user)** - SpaceIQ で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure ポータルで Azure AD のシングル サインオンを有効にし、SpaceIQ でシングル サインオンを構成します。

**SpaceIQ で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure ポータルの **SpaceIQ** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/spaceiq-tutorial/tutorial_spaceiq_samlbase.png)

1. **[SpaceIQ のドメインと URL]** セクションで、次の手順に従います。

    ![[SpaceIQ のドメインと URL] のシングル サインオン情報](./media/spaceiq-tutorial/tutorial_spaceiq_url.png)

    a. **[識別子]** ボックスに次の URL を入力します。`https://api.spaceiq.com`

    b. **[応答 URL]** ボックスに、`https://api.spaceiq.com/saml/<instanceid>/callback` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > 実際の応答 URL と識別子に値を置き換えます。実際の値については後で説明します。
 
1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/spaceiq-tutorial/tutorial_spaceiq_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/spaceiq-tutorial/tutorial_general_400.png)

1. **[SpaceIQ の構成]** セクションで、**[SpaceIQ の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス] セクション**から **SAML エンティティ ID** をコピーします。

    ![SpaceIQ の構成](./media/spaceiq-tutorial/tutorial_spaceiq_configure.png) 

1.  新しい Web ブラウザー ウィンドウを開き、SpaceIQ 環境に管理者としてサインインします。

1. ログインしたら、右上のパズル サインをクリックし、**[Integrations]\(統合\)** をクリックします。

    ![アカウント設定](./media/spaceiq-tutorial/setting1.png) 

1. **[All PROVISIONING & SSO]\(すべてのプロビジョニングと SSO\)** で、**[Azure]** タイルをクリックして、Azure インスタンスを IDP として追加します。

    ![[SAML] アイコン](./media/spaceiq-tutorial/setting2.png)

1. **[SSO]** ダイアログ ボックスで、次の手順に従います。

    ![SAML 認証設定](./media/spaceiq-tutorial/setting3.png)

    a. **[SAML Issuer URL]\(SAML 発行者 URL\)** ボックスに、Azure AD のアプリケーション構成ウィンドウからコピーした **[SAML エンティティ ID]** の値を貼り付けます。
    
    b. **[SAML CallBack Endpoint URL (read-only)]\(SAML コールバック エンドポイント URL (読み取り専用)\)** の値をコピーし、Azure ポータルの **[SpaceIQ のドメインと URL]** セクションの **[応答 URL]** ボックスに貼り付けます。
    
    c. **[SAML Audience URI (read-only)]\(SAML オーディエンス URL (読み取り専用)\)** の値をコピーし、Azure ポータルの **[SpaceIQ のドメインと URL]** セクションの **[識別子]** ボックスに貼り付けます。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 ダウンロードした証明書ファイルをメモ帳で開き、その内容をコピーし、**[X.509 Certificate]\(X.509 証明書\)** ボックスに貼り付けます。
    
    e. **[Save]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 埋め込みドキュメント機能の詳細については、[Azure AD の埋め込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/spaceiq-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/spaceiq-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/spaceiq-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/spaceiq-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。
  
### <a name="create-a-spaceiq-test-user"></a>SpaceIQ テスト ユーザーを作成する

このセクションでは、SpaceIQ で Britta Simon というユーザーを作成します。 [SpaceIQ サポート チーム](mailto:eng@spaceiq.com)  と協力して、SpaceIQ プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SpaceIQ へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**SpaceIQ に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[SpaceIQ]** を選択します。

    ![アプリケーションの一覧の SpaceIQ のリンク](./media/spaceiq-tutorial/tutorial_spaceiq_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [SpaceIQ] タイルをクリックすると、自動的に SpaceIQ アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/spaceiq-tutorial/tutorial_general_01.png
[2]: ./media/spaceiq-tutorial/tutorial_general_02.png
[3]: ./media/spaceiq-tutorial/tutorial_general_03.png
[4]: ./media/spaceiq-tutorial/tutorial_general_04.png

[100]: ./media/spaceiq-tutorial/tutorial_general_100.png

[200]: ./media/spaceiq-tutorial/tutorial_general_200.png
[201]: ./media/spaceiq-tutorial/tutorial_general_201.png
[202]: ./media/spaceiq-tutorial/tutorial_general_202.png
[203]: ./media/spaceiq-tutorial/tutorial_general_203.png


---
title: チュートリアル:Azure Active Directory と TOPdesk - Public の統合 | Microsoft Docs
description: Azure Active Directory と TOPdesk - Public の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89412040fdea32746574d8ae5bada9c017617b80
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56184994"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>チュートリアル:Azure Active Directory と TOPdesk - Public の統合

このチュートリアルでは、TOPdesk - Public と Azure Active Directory (Azure AD) を統合する方法について説明します。

TOPdesk - Public と Azure AD の統合には、次の利点があります。

- TOPdesk - Public にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが各自の Azure AD アカウントで TOPdesk - Public に自動的にサインオン (シングル サインオン) するように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

TOPdesk - Public と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- TOPdesk - Public でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから TOPdesk - Public を追加する
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-topdesk---public-from-the-gallery"></a>ギャラリーから TOPdesk - Public を追加する
Azure AD への TOPdesk - Public の統合を構成するには、管理対象の SaaS アプリの一覧にギャラリーから TOPdesk - Public を追加する必要があります。

**ギャラリーから TOPdesk - Public を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**TOPdesk - Public**」と入力して、結果パネルで **[TOPdesk - Public]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の TOPdesk - Public](./media/topdesk-public-tutorial/tutorial_topdesk-public_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、TOPdesk - Public で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する TOPdesk - Public ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと TOPdesk - Public の関連ユーザーの間で、リンク関係が確立されている必要があります。

TOPdesk - Public で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

TOPdesk - Public で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[TOPdesk - Public のテスト ユーザーの作成](#create-a-topdesk---public-test-user)** - TOPdesk - Public で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、TOPdesk - Public アプリケーションでシングル サインオンを構成します。

**TOPdesk - Public で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **TOPdesk - Public** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/topdesk-public-tutorial/tutorial_topdesk-public_samlbase.png)

1. **[TOPdesk - Public のドメインと URL]** セクションで、次の手順を実行します。

    ![[TOPdesk - Public のドメインと URL] のシングル サインオン情報](./media/topdesk-public-tutorial/tutorial_topdesk-public_url.png)

    a. **[サインオン URL]** ボックスに、`https://<companyname>.topdesk.net` のパターンを使用して URL を入力します。
    
    b. **[識別子]** ボックスに、`https://<companyname>.topdesk.net/tas/public/login/verify` の形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<companyname>.topdesk.net/tas/public/login/saml` のパターンを使用して URL を入力します。
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 応答 URL については、このチュートリアルで後ほど説明します。 これらの値を取得するには、[TOPdesk - Public クライアント サポート チーム](https://help.topdesk.com/saas/enterprise/user/)に問い合わせてください。  

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/topdesk-public-tutorial/tutorial_topdesk-public_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/topdesk-public-tutorial/tutorial_general_400.png)
    
1. **[TOPdesk - Public 構成]** セクションで、**[TOPdesk - Public の構成]** をクリックし、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![TOPdesk - Public 構成](./media/topdesk-public-tutorial/tutorial_topdesk-public_configure.png) 

1. **TOPdesk - Public** 企業サイトに管理者としてサインオンします。

1. **[TOPdesk]** メニューで **[Settings]** をクリックします。
   
    ![設定](./media/topdesk-public-tutorial/ic790598.png "Settings")

1. **[Login Settings]** をクリックします。
   
    ![Login Settings](./media/topdesk-public-tutorial/ic790599.png "Login Settings")

1. **[Login Settings]** メニューを展開し、**[General]** をクリックします。
   
    ![General](./media/topdesk-public-tutorial/ic790600.png "General")

1. **[SAML login]** 構成セクションの **[Public]** で、次の手順に従います。
   
    ![Technical Settings](./media/topdesk-public-tutorial/ic790601.png "Technical Settings")
   
    a. **[Download]** をクリックしてパブリック メタデータ ファイルをダウンロードし、コンピューターにローカルに保存します。
   
    b. ダウンロードしたメタデータ ファイルを開いて、**AssertionConsumerService** ノードを探します。

    ![AssertionConsumerService](./media/topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. **[AssertionConsumerService]** の値をコピーし、**[TOPdesk - Public のドメインと URL]** セクションの **[応答 URL]** ボックスにこの値を貼り付けます。      
   
1. 証明書ファイルを作成するには、次の手順を実行します。
    
    ![Certificate](./media/topdesk-public-tutorial/ic790606.png "Certificate")
    
    a. Azure Portal からダウンロードしたメタデータ ファイルを開きます。
    
    b. **fed:ApplicationServiceType** の **xsi:type** を持つ **RoleDescriptor** ノードを展開します。
    
    c. **X509Certificate** ノードの値をコピーします。
    
    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 コピーした **X509Certificate** の値をコンピューター上のファイル内にローカルに保存します。

1. **[Public]** セクションで、**[Add]** をクリックします。
    
    ![SAML ログイン](./media/topdesk-public-tutorial/ic790625.png "SAML ログイン")

1. **[SAML configuration assistant]** ダイアログ ページで、次の手順を実行します。
    
    ![SAML Configuration Assistant](./media/topdesk-public-tutorial/ic790608.png "SAML Configuration Assistant")
    
    a. Azure Portal からダウンロードしたメタデータ ファイルをアップロードするには、**[Federation Metadata]** で **[Browse]** をクリックします。

    b. 証明書ファイルをアップロードするには、**[Certificate (RSA)]** で **[Browse]** をクリックします。

    c. TOPdesk サポート チームから入手したロゴのファイルをアップロードするには、**[Logo icon]** の下の **[Browse]** をクリックします。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **[User name attribute]** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」と入力します。

    e. **[Display name]** テキスト ボックスに、構成の名前を入力します。

    f. **[Save]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 埋め込みドキュメント機能の詳細については、[Azure AD の埋め込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/topdesk-public-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/topdesk-public-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/topdesk-public-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/topdesk-public-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。
 
### <a name="create-a-topdesk---public-test-user"></a>TOPdesk - Public のテスト ユーザーの作成

Azure AD ユーザーが TOPdesk - Public にログインできるようにするには、そのユーザーを TOPdesk - Public にプロビジョニングする必要があります。  
TOPdesk - Public の場合、プロビジョニングは手動で行います。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>ユーザー プロビジョニングを構成するには、次の手順に従います。
1. **TOPdesk - Public** 企業サイトに管理者としてサインオンします。

1. 上部のメニューで、**[TOPdesk]\>[New]\>[Support Files]\>[Person]** の順にクリックします。
   
    ![Person](./media/topdesk-public-tutorial/ic790628.png "Person")

1. [New Person] ダイアログで、次の手順を実行します。
   
    ![New Person](./media/topdesk-public-tutorial/ic790629.png "New Person")
   
    a. [General] タブをクリックします。

    b. **[Surname]** ボックスに、ユーザーの姓を入力します (この例では Simon)。
 
    c. アカウントの **[Site]** を選択します。
 
    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **[Save]** をクリックします。

> [!NOTE]
> 他の TOPdesk - Public ユーザー アカウントの作成ツールまたは TOPdesk - Public から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に TOPdesk - Public へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Britta Simon を TOPdesk - Public に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[TOPdesk - Public]** を選択します。

    ![アプリケーションの一覧の TOPdesk - Public のリンク](./media/topdesk-public-tutorial/tutorial_topdesk-public_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [TOPdesk - Public] のタイルをクリックすると、TOPdesk - Public アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/topdesk-public-tutorial/tutorial_general_01.png
[2]: ./media/topdesk-public-tutorial/tutorial_general_02.png
[3]: ./media/topdesk-public-tutorial/tutorial_general_03.png
[4]: ./media/topdesk-public-tutorial/tutorial_general_04.png

[100]: ./media/topdesk-public-tutorial/tutorial_general_100.png

[200]: ./media/topdesk-public-tutorial/tutorial_general_200.png
[201]: ./media/topdesk-public-tutorial/tutorial_general_201.png
[202]: ./media/topdesk-public-tutorial/tutorial_general_202.png
[203]: ./media/topdesk-public-tutorial/tutorial_general_203.png


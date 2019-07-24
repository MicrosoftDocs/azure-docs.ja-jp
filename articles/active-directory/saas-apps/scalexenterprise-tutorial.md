---
title: チュートリアル:Azure Active Directory と ScaleX Enterprise の統合 | Microsoft Docs
description: Azure Active Directory と ScaleX Enterprise の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c2379a8d-a659-45f1-87db-9ba156d83183
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64edf2aa47211c1d2a598417a7b2edc00f260075
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56208573"
---
# <a name="tutorial-azure-active-directory-integration-with-scalex-enterprise"></a>チュートリアル:Azure Active Directory と ScaleX Enterprise の統合

このチュートリアルでは、ScaleX Enterprise と Azure Active Directory (Azure AD) を統合する方法について説明します。

ScaleX Enterprise と Azure AD の統合には、次の利点があります。

- ScaleX Enterprise にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に ScaleX Enterprise にサインオン (シングル サインオン) する機能を有効にすることができます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、 「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

ScaleX Enterprise と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- ScaleX Enterprise でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの ScaleX Enterprise の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-scalex-enterprise-from-the-gallery"></a>ギャラリーからの ScaleX Enterprise の追加
Azure AD への ScaleX Enterprise の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ScaleX Enterprise を追加する必要があります。

**ギャラリーから ScaleX Enterprise を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. ダイアログの上部にある **[追加]** をクリックします。

    ![[アプリケーション]][3]

1. **検索ボックス**に、「ScaleX Enterprise」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_search.png)

1. 結果ウィンドウで **[ScaleX Enterprise]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、ScaleX Enterprise で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する ScaleX Enterprise ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと ScaleX Enterprise の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、ScaleX Enterprise の **[Username (ユーザー名)]** の値として割り当てることで確立されます。

ScaleX Enterprise で Azure AD のシングル サインオンを構成し、テストするには、次の要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[ScaleX Enterprise のテスト ユーザーの作成](#creating-a-scalex-enterprise-test-user)** - ScaleX Enterprise で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure ポータルで Azure AD のシングル サインオンを有効にして、ScaleX Enterprise アプリケーションでシングル サインオンを構成します。

**ScaleX Enterprise で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure ポータルの **ScaleX Enterprise** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択してシングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_samlbase.png)

1. **[ScaleX Enterprise のドメインと URL]** セクションで、**IDP 開始モード**でアプリケーションを構成する場合は、次の手順に従います。

    ![Configure single sign-on](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_url1.png)

    a. **[識別子]** ボックスに、`https://platform.rescale.com/saml2/<company id>/` の形式で値を入力します。

    b. **[応答 URL]** ボックスに、`https://platform.rescale.com/saml2/<company id>/acs/` のパターンを使用して URL を入力します。

1. **SP** 開始モードでアプリケーションを構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにします。

    ![Configure single sign-on](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_url2.png)

    **[サインオン URL]** ボックスに、`https://platform.rescale.com/saml2/<company id>/sso/` のパターンを使用して値を入力します。
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[ScaleX Enterprise クライアント サポート チーム](https://info.rescale.com/contact_sales)に問い合わせてください。 

1. ScaleX アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に変更する必要があります。 **[その他のすべてのユーザー属性を表示および編集する]** チェックボックスをオンにして、カスタム属性設定を開きます。

    ![Configure single sign-on](./media/scalexenterprise-tutorial/scalex_attributes.png)
    
    a. **name**  属性を右クリックし、[削除] をクリックします。

    ![Configure single sign-on](./media/scalexenterprise-tutorial/delete_attribute_name.png)

    b. **emailaddress** 属性をクリックして、[属性の編集] ウィンドウを開きます。 その値を、**user.mail** から **user.userprincipalname** に変更し、[Ok] をクリックします。

    ![Configure single sign-on](./media/scalexenterprise-tutorial/edit_email_attribute.png) 
    
1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/scalexenterprise-tutorial/tutorial_general_400.png)
    
1. **[ScaleX Enterprise の構成]** セクションで、**[ScaleX Enterprise を構成する]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML エンティティ ID** と **SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_configure.png) 

1. **ScaleX Enterprise** 側でシングル サインオンを構成するには、管理者として ScaleX Enterprise の Web サイトにログインします。

1. 右上隅のメニューを選択し、**[Contoso Administration (Contoso の管理)]** をクリックします。

    > [!NOTE] 
    > Contoso は一例です。 これは、実際の会社名でなければなりません。 

    ![Configure single sign-on](./media/scalexenterprise-tutorial/Test_Admin.png) 

1. 上部メニューから **[Integrations (統合)]** を選択し、**[Single Sign-On (シングル サインオン)]** を選択します。

    ![Configure single sign-on](./media/scalexenterprise-tutorial/admin_sso.png) 

1. 次のようにフォームの操作を実行します。

    ![Configure single sign-on](./media/scalexenterprise-tutorial/scalex_admin_save.png) 
    
    a. **[Create any user who can authenticate with SSO (SSO で認証できるユーザーを作成する)]** を選択します。

    b. **[Service Provider saml]\(サービス プロバイダー SAML\)**:値 ***urn:oasis:names:tc:SAML:2.0:nameid-format:persistent*** を貼り付けます

    c. **[Name of Identity Provider email field in ACS response]\(ACS 応答での ID プロバイダーの電子メール フィールドの名前\)**:値 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` を貼り付けます

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **[Identity Provider EntityDescriptor Entity ID]\(ID プロバイダーの EntityDescriptor エンティティ ID\):** Azure portal からコピーした **SAML エンティティ ID** 値を貼り付けます。

    e. **Identity Provider SingleSignOnService URL\(ID プロバイダーの SingleSignOnService URL\):** Azure portal からの **SAML シングル サインオン サービス URL** を貼り付けます。

    f. **[Identity Provider public X509 certificate]\(ID プロバイダーのパブリック X509 証明書\):** Azure からダウンロードした X509 証明書をメモ帳で開いて、このボックスに貼り付けます。 証明書の内容の途中に改行が含まれていないことを確認します。
    
    g. 次のチェックボックスをオンにします:**[Enabled]\(有効化\)、[Encrypt NameID]\(暗号 NameID\)、[Sign AuthnRequests]\(AuthnRequests に署名する\)**。

    h. **[Update SSO Settings (SSO 設定を更新する)]** をクリックして設定を保存します。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 埋め込みドキュメント機能の詳細については、[Azure AD の埋め込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/scalexenterprise-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/scalexenterprise-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/scalexenterprise-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/scalexenterprise-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。
 
### <a name="creating-a-scalex-enterprise-test-user"></a>ScaleX Enterprise テスト ユーザーの作成

Azure AD ユーザーが ScaleX Enterprise にログインできるようにするには、ユーザーを ScaleX Enterprise にプロビジョニングする必要があります。 ScaleX Enterprise の場合、プロビジョニングは自動化されたタスクであり、手動の手順は必要ありません。 SSO 資格情報で正しく認証できるすべてのユーザーは、ScaleX 側で自動的にプロビジョニングされます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に ScaleX Enterprise へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を ScaleX Enterprise に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で、 **[ScaleX Enterprise]** を選択します。

    ![Configure single sign-on](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [ScaleX Enterprise] タイルをクリックすると、自動的に ScaleX Enterprise アプリケーションにサインオンします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/scalexenterprise-tutorial/tutorial_general_01.png
[2]: ./media/scalexenterprise-tutorial/tutorial_general_02.png
[3]: ./media/scalexenterprise-tutorial/tutorial_general_03.png
[4]: ./media/scalexenterprise-tutorial/tutorial_general_04.png

[100]: ./media/scalexenterprise-tutorial/tutorial_general_100.png

[200]: ./media/scalexenterprise-tutorial/tutorial_general_200.png
[201]: ./media/scalexenterprise-tutorial/tutorial_general_201.png
[202]: ./media/scalexenterprise-tutorial/tutorial_general_202.png
[203]: ./media/scalexenterprise-tutorial/tutorial_general_203.png


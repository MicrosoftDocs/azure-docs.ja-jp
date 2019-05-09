---
title: チュートリアル:Azure Active Directory と Leapsome の統合 | Microsoft Docs
description: Azure Active Directory と Leapsome の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: cb523e97-add8-4289-b106-927bf1a02188
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37549cc76e1490b0758de8e296523b0e70c98dbf
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56191233"
---
# <a name="tutorial-azure-active-directory-integration-with-leapsome"></a>チュートリアル:Azure Active Directory と Leapsome の統合

このチュートリアルでは、Leapsome と Azure Active Directory (Azure AD) を統合する方法について説明します。

Leapsome と Azure AD の統合には、次の利点があります。

- Leapsome にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Leapsome にサインオン (シングル サインオン) できるようにすることが可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Leapsome と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Leapsome でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Leapsome の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-leapsome-from-the-gallery"></a>ギャラリーからの Leapsome の追加
Azure AD への Leapsome の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Leapsome を追加する必要があります。

**ギャラリーから Leapsome を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Leapsome**」と入力し、結果パネルで **Leapsome** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Leapsome](./media/leapsome-tutorial/tutorial_leapsome_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Leapsome で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Leapsome ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Leapsome の関連ユーザーの間で、リンク関係が確立されている必要があります。

Leapsome で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Leapsome テスト ユーザーの作成](#create-a-leapsome-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Leapsome で作成します。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Leapsome アプリケーションでシングル サインオンを構成します。

**Leapsome で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Leapsome** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/leapsome-tutorial/tutorial_leapsome_samlbase.png)

1. アプリケーションを **IDP** 開始モードで構成する場合は、**[Leapsome Domain and URLs]\(Leapsome のドメインと URL\)** セクションで次の手順を実行します。

    ![[Leapsome Domain and URLs]\(Leapsome のドメインと URL\) のシングル サインオン情報](./media/leapsome-tutorial/tutorial_leapsome_url.png)

    a. **[識別子]** ボックスに次の URL を入力します。`https://www.leapsome.com`

    b. **[応答 URL]** ボックスに、`https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert` のパターンを使用して URL を入力します。

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[Leapsome Domain and URLs]\(Leapsome のドメインと URL\) のシングル サインオン情報](./media/leapsome-tutorial/tutorial_leapsome_url1.png)

    **[サインオン URL]** ボックスに、`https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login` のパターンを使用して URL を入力します。
     
    > [!NOTE] 
    > 上記の [応答 URL] と [サインオン URL] の値は、実際の値ではありません。 これらの値を実際の値に置き換えます。実際の値については後で説明します。

1. Leapsome アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 次のスクリーンショットは、例を示しています。
    
    ![Configure single sign-on](./media/leapsome-tutorial/tutorial_Leapsome_attribute.png)

1. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、上の図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名 | 属性値 | 名前空間 |
    | ---------------| --------------- | --------- |   
    | firstname | User.givenname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | lastname | User.surname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | title | user.jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | picture | 社員の画像への URL | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |

    > [!Note]
    > picture 属性の値は、実際のものではありません。 実際の画像 URL でこの値を更新してください。 この値を取得するには、 [Leapsome クライアント サポート チーム](mailto:support@leapsome.com)にお問い合わせください。
    
    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure single sign-on](./media/leapsome-tutorial/tutorial_attribute_04.png)

    ![Configure single sign-on](./media/leapsome-tutorial/tutorial_attribute_05.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **[名前空間]** ボックスに、その行の名前空間 URI を入力します。
    
    e. **[OK]** をクリックします。

1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/leapsome-tutorial/tutorial_leapsome_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/leapsome-tutorial/tutorial_general_400.png)
    
1. **[Leapsome Configuration]\(Leapsome 構成\)** セクションで、**[Configure Leapsome]\(Leapsome を構成する\)** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![[Leapsome Configuration]\(Leapsome 構成\)](./media/leapsome-tutorial/tutorial_leapsome_configure.png)

1. 別の Web ブラウザー ウィンドウで、Leapsome にセキュリティ管理者としてログインします。

1. 右上にある設定ロゴをクリックし、**[Admin Settings]\(管理者設定\)** をクリックします。 

    ![Leapsome セット](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

1. 左側のメニュー バーで **[Single Sign On \(SSO\)]\(シングル サインオン \(SSO\)\)** をクリックし、**[SAML-based single sign-on \(SSO\)]\(SAML ベースのシングル サインオン \(SSO\)\)** ページで、次の手順を実行します。
    
    ![Leapsome SAML](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. **[Enable SAML-based single sign-on]\(SAML ベースのシングル サインオンを有効にする\)** を選択します。

    b. **[Login URL \(point your users here to start login\)]\(ログイン URL \(ログインをスタートする場所としてユーザーにここを案内する\)\)** の値をコピーし、Azure portal の **[Leapsome Domain and URLs]\(Leapsome のドメインと URL\)** セクションの **[サインオン URL]** ボックスに貼り付けます。

    c. **[Reply URL (receives response from your identity provider)]\(応答 URL (ID プロバイダーからの応答をここで受け取る)\)** の値をコピーし、Azure portal の **[Leapsome のドメインと URL]** セクションの **[応答 URL]** ボックスに貼り付けます。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **[SSO Login URL \(provided by identity provider\)]\(SSO ログイン URL \(ID プロバイダーから提供されたもの\)\)** ボックスに、Azure portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    e. Azure portal からダウンロードした証明書をコピーし、---BEGIN CERTIFICATE--- および ---END CERTIFICATE--- というコメントを削除して、**[Certificate \(provided by identity provider\)]\(証明書 \(ID プロバイダーから提供されたもの\)\)** ボックスに貼り付けます。

    f. **[UPDATE SSO SETTINGS]\(SSO 設定を更新する\)** をクリックします。
    
### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/leapsome-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/leapsome-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/leapsome-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/leapsome-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。
 
### <a name="create-a-leapsome-test-user"></a>Leapsome テスト ユーザーの作成

このセクションでは、Leapsome で Britta Simon というユーザーを作成します。  [Leapsome クライアント サポート チーム](mailto:support@leapsome.com) に相談して、Leapsome プラットフォームでホワイトリストに追加する必要のあるユーザーまたはドメインを追加します。 ドメインがチームによって追加された場合、ユーザーは Leapsome プラットフォームに自動的にプロビジョニングされます。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Leapsome へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Leapsome に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Leapsome]** を選択します。

    ![アプリケーションの一覧の Leapsome のリンク](./media/leapsome-tutorial/tutorial_leapsome_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Leapsome] タイルをクリックすると、自動的に Leapsome アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/leapsome-tutorial/tutorial_general_01.png
[2]: ./media/leapsome-tutorial/tutorial_general_02.png
[3]: ./media/leapsome-tutorial/tutorial_general_03.png
[4]: ./media/leapsome-tutorial/tutorial_general_04.png

[100]: ./media/leapsome-tutorial/tutorial_general_100.png

[200]: ./media/leapsome-tutorial/tutorial_general_200.png
[201]: ./media/leapsome-tutorial/tutorial_general_201.png
[202]: ./media/leapsome-tutorial/tutorial_general_202.png
[203]: ./media/leapsome-tutorial/tutorial_general_203.png


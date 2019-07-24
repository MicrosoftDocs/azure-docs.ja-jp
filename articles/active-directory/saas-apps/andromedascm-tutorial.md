---
title: チュートリアル:Azure Active Directory と Andromeda の統合 | Microsoft Docs
description: Azure Active Directory と Andromeda の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7a142c86-ca0c-4915-b1d8-124c08c3e3d8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fd26129a6ab8fb6082f9465be71eadcafa292db
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56165198"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>チュートリアル:Azure Active Directory と Andromeda の統合

このチュートリアルでは、Andromeda と Azure Active Directory (Azure AD) を統合する方法について説明します。

Andromeda と Azure AD の統合には、次の利点があります。

- Andromeda にアクセスするユーザーを Azure AD で管理できます。
- ユーザーが自分の Azure AD アカウントで自動的に Andromeda にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Andromeda と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Andromeda でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Andromeda の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-andromeda-from-the-gallery"></a>ギャラリーからの Andromeda の追加
Azure AD への Andromeda の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Andromeda を追加する必要があります。

**ギャラリーから Andromeda を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Andromeda** 」と入力し、結果パネルで **[Andromeda ]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Andromeda で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Andromeda ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Andromeda の関連ユーザーの間で、リンク関係が確立されている必要があります。

Andromeda で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Andromeda のテスト ユーザーの作成](#create-an-andromeda-test-user)** - Andromeda で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Andromeda アプリケーションでシングル サインオンを構成します。

**Andromeda で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Andromeda** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/andromedascm-tutorial/tutorial_andromedascm_samlbase.png)

3. **[Andromeda のドメインと URL]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[Andromeda のドメインと URL] のシングル サインオン情報](./media/andromedascm-tutorial/tutorial_andromedascm_url.png)

    a. **[識別子]** ボックスに、`https://<tenantURL>.ngcxpress.com/` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx` のパターンを使用して URL を入力します。

4. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[Andromeda のドメインと URL] のシングル サインオン情報](./media/andromedascm-tutorial/tutorial_andromedascm_url1.png)

    **[サインオン URL]** ボックスに、`https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx` のパターンを使用して URL を入力します。
     
    > [!NOTE] 
    > 上記の値は、実際の値ではありません。 実際の識別子、応答 URL、サインオン URL に値を置き換えます。実際の値については後で説明します。

5. Andromeda アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 次のスクリーンショットはその例です。
    
    ![シングル サインオンの構成の属性](./media/andromedascm-tutorial/tutorial_andromedascm_attribute.png)

    > [!Important]
    > これらの設定中に名前空間の定義をクリアします。
    
6. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | -------------- | -------------------- |    
    | role        | アプリ固有のロール |
    | type        | アプリの種類 |
    | company       | CompanyName    |

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値はデモ用なので、実際の組織のロールを使ってください。

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![シングル サインオンの構成の追加](./media/andromedascm-tutorial/tutorial_attribute_04.png)

    ![シングル サインオンの構成の属性の追加](./media/andromedascm-tutorial/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **[名前空間]** は空白のままにします。
    
    e. **[OK]** をクリックします。

7. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/andromedascm-tutorial/tutorial_andromedascm_certificate.png) 

8. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/andromedascm-tutorial/tutorial_general_400.png)
    
9. **[Andromeda 構成]** セクションで、**[Andromeda の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Andromeda の構成](./media/andromedascm-tutorial/tutorial_andromedascm_configure.png)

10. Andromeda 企業サイトに管理者としてサインオンします。

11. メニュー バーの上部にある **[Admin]\(管理者\)** をクリックし、**[Administration]\(管理\)** に移動します。

    ![Andromeda 管理者](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

12. ツール バーの左側の **[Interfaces]\(インターフェイス\)** セクションで、**[SAML Configuration]\(SAML の構成\)** をクリックします。

    ![Andromeda SAML](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

13. **[SAML Configuration]\(SAML の構成\)** セクション ページで、次の手順を実行します。

    ![Andromeda の構成](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. **[Enable SSO with SAML]\(SAML で SSO を有効にする\)** をオンにします。

    b. **[Andromeda Information]\(Andromeda 情報\)** セクションで、**[SP Identity]\(SP ID\)** の値をコピーして、**[Andromeda のドメインと URL]** セクションの **[識別子]** ボックスに貼り付けます。

    c. **[Consumer URL]\(コンシューマー URL\)** の値をコピーし、**[Andromeda のドメインと URL]** セクションの **[応答 URL]** ボックスに貼り付けます。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **[Logon URL]\(ログオン URL\)** の値をコピーし、**[Andromeda のドメインと URL]** セクションの **[サインオン URL]** ボックスに貼り付けます。

    e. **[SAML Identity Provider]\(SAML ID プロバイダー\)** セクションで、IDP 名を入力します。

    f. **[Single Sign On End Point]\(シングル サインオン エンドポイント\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    g. Azure Portal からダウンロードした **Base64 でエンコードされた証明書**をメモ帳で開き、**[X.509 証明書]** ボックスに貼り付けます。
    
    h. 以下の属性を対応する値にマップし、Azure AD からの SSO ログインを容易にします。 **ユーザー ID** 属性はログインに必要です。 プロビジョニングの場合は、**電子メール**、**会社**、**ユーザーの種類**、**ロール**が必要です。 このセクションでは、Azure Portal 内で定義されている値に関連付ける属性マッピング (名前と値) を定義します。

    ![Andromeda 属性マップ](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. **[Save]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 埋め込みドキュメント機能の詳細については、[Azure AD の埋め込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/andromedascm-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/andromedascm-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/andromedascm-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/andromedascm-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。
 
### <a name="create-an-andromeda-test-user"></a>Andromeda テスト ユーザーの作成

このセクションの目的は、Andromeda で Britta Simon というユーザーを作成することです。 Andromeda では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Andromeda にアクセスしようとすると、ユーザーがまだ存在しない場合は新しいユーザーが作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Andromeda クライアント サポート チーム](https://www.ngcsoftware.com/support/)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Andromeda へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Andromeda に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Andromeda]** を選択します。

    ![アプリケーションの一覧の [Andromeda] リンク](./media/andromedascm-tutorial/tutorial_andromedascm_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Andromeda のタイルをクリックすると、自動的に Andromeda アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/andromedascm-tutorial/tutorial_general_01.png
[2]: ./media/andromedascm-tutorial/tutorial_general_02.png
[3]: ./media/andromedascm-tutorial/tutorial_general_03.png
[4]: ./media/andromedascm-tutorial/tutorial_general_04.png

[100]: ./media/andromedascm-tutorial/tutorial_general_100.png

[200]: ./media/andromedascm-tutorial/tutorial_general_200.png
[201]: ./media/andromedascm-tutorial/tutorial_general_201.png
[202]: ./media/andromedascm-tutorial/tutorial_general_202.png
[203]: ./media/andromedascm-tutorial/tutorial_general_203.png

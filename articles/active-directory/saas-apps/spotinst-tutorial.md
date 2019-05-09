---
title: チュートリアル:Azure Active Directory と Spotinst の統合 | Microsoft Docs
description: Azure Active Directory と Spotinst の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2f6dbd70-c2db-4ae9-99ee-976c3090d214
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0bffdf439a192fb10fe695fbfa18e8c7abf8077
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57846686"
---
# <a name="tutorial-azure-active-directory-integration-with-spotinst"></a>チュートリアル:Azure Active Directory と Spotinst の統合

このチュートリアルでは、Spotinst と Azure Active Directory (Azure AD) を統合する方法について説明します。

Spotinst と Azure AD の統合には、次の利点があります。

- Spotinst にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Spotinst にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Spotinst と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Spotinst のシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Spotinst の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-spotinst-from-the-gallery"></a>ギャラリーからの Spotinst の追加
Azure AD への Spotinst の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Spotinst を追加する必要があります。

**ギャラリーから Spotinst を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Spotinst**」と入力し、結果パネルで **Spotinst** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Spotinst](./media/spotinst-tutorial/tutorial_spotinst_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Spotinst で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Spotinst ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Spotinst の関連ユーザーの間で、リンク関係が確立されている必要があります。

Spotinst で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Spotinst テスト ユーザーの作成](#create-a-spotinst-test-user)** - Spotinst で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Spotinst アプリケーションでシングル サインオンを構成します。

**Spotinst で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Spotinst** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/spotinst-tutorial/tutorial_spotinst_samlbase.png)

3. **[Spotinst のドメインと URL]** セクションで、IDP 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[Spotinst のドメインと URL] のシングル サインオン情報](./media/spotinst-tutorial/tutorial_spotinst_url1.png)

    a. **[詳細な URL 設定の表示]** をクリックします。

    b. **[リレー状態]** ボックスに、値 `<ID>` を入力します。

    c. **[SP]** 開始モードでアプリケーションを構成する場合は、**[サインオン URL]** テキストボックスに次の URL を入力します: `https://console.spotinst.com`

    > [!NOTE]
    > リレー状態の値は実際のものではありません。 実際のリレー状態にリレー状態値を置き換えます。実際の値については後で説明します。

4. Spotinst アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 次のスクリーンショットは、その例を示しています。

    ![Configure single sign-on](./media/spotinst-tutorial/tutorial_Spotinst_attribute.png)

5. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、上の図に示すように SAML トークン属性を構成し、次の手順を実行します。

    | 属性名 | 属性値 |
    | ---------------| --------------- |
    | 電子メール | User.mail |
    | FirstName | User.givenname |
    | LastName | User.surname |
    
    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure single sign-on](./media/spotinst-tutorial/tutorial_attribute_04.png)

    ![Configure single sign-on](./media/spotinst-tutorial/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d. **[名前空間]** は空白のままにします。

    e. **[OK]** をクリックします。

6. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/spotinst-tutorial/tutorial_spotinst_certificate.png) 

7. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/spotinst-tutorial/tutorial_general_400.png)

8. 別の Web ブラウザー ウィンドウで、セキュリティ管理者として Spotinst にログインします。

9. 画面の右上にある**ユーザー アイコン**をクリックして、**[設定]** をクリックします。

    ![Spotinst の設定](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

10. 上部の **[SECURITY]\(セキュリティ\)** タブをクリックし、**[Identity Providers]\(ID プロバイダー\)** を選択して、以下の手順を実行します。

    ![Spotinst のセキュリティ](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. インスタンスの **[Relay State]\(リレー状態\)** の値をコピーして、Azure portal の **[Spotinst のドメインと URL]** セクションの **[リレー状態]** ボックスに貼り付けます。

    b. **[BROWSE]\(参照\)** をクリックし、Azure portal からダウンロードしたメタデータ xml ファイルをアップロードします。

    c. **[保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/spotinst-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/spotinst-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/spotinst-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/spotinst-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="create-a-spotinst-test-user"></a>Spotinst テスト ユーザーを作成する

このセクションの目的は、Spotinst で Britta Simon というユーザーを作成することです。

1. **SP** 開始モードでアプリケーションを構成してある場合は、次の手順を実行します。

   a. 別の Web ブラウザー ウィンドウで、セキュリティ管理者として Spotinst にログインします。

   b. 画面の右上にある**ユーザー アイコン**をクリックして、**[設定]** をクリックします。

    ![Spotinst の設定](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. **[Users]\(ユーザー\)** をクリックして、**[ADD USER]\(ユーザーの追加\)** を選択します。

    ![Spotinst の設定](./media/spotinst-tutorial/adduser1.png)

    d. ユーザーの追加セクションで、次の手順を実行します。

    ![Spotinst の設定](./media/spotinst-tutorial/adduser2.png)

    * **[Full Name]\(フルネーム\)** ボックスに、ユーザーの氏名 (**BrittaSimon** など) を入力します。

    * **[Email]\(メール\)** ボックスに、ユーザーのメール アドレス (例: **Brittasimon\@contoso.com**) を入力します。

    * **[Organization Role, Account Role, and Accounts]\(組織ロール、アカウント ロール、アカウント\)** で組織に固有の詳細を選択します。

2. **IDP** 開始モードでアプリケーションを構成してある場合は、このセクションにアクション項目はありません。 Spotinst では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 存在しない Spotinst ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Spotinst へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Spotinst に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Spotinst]** を選択します。

    ![アプリケーションの一覧の Spotinst のリンク](./media/spotinst-tutorial/tutorial_spotinst_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Spotinst] タイルをクリックすると、自動的に Spotinst アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../active-directory-saas-access-panel-introduction.md)に関するページを参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/spotinst-tutorial/tutorial_general_01.png
[2]: ./media/spotinst-tutorial/tutorial_general_02.png
[3]: ./media/spotinst-tutorial/tutorial_general_03.png
[4]: ./media/spotinst-tutorial/tutorial_general_04.png

[100]: ./media/spotinst-tutorial/tutorial_general_100.png

[200]: ./media/spotinst-tutorial/tutorial_general_200.png
[201]: ./media/spotinst-tutorial/tutorial_general_201.png
[202]: ./media/spotinst-tutorial/tutorial_general_202.png
[203]: ./media/spotinst-tutorial/tutorial_general_203.png


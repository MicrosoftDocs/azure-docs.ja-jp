---
title: チュートリアル:Azure Active Directory と Yodeck の統合 | Microsoft Docs
description: Azure Active Directory と Yodeck の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b2c8dccb-eeb0-4f4d-a24d-8320631ce819
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7564710b282c7e2ac586980896a28c00bcb2fe80
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57872604"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>チュートリアル:Azure Active Directory と Yodeck の統合

このチュートリアルでは、Yodeck と Azure Active Directory (Azure AD) を統合する方法について説明します。

Yodeck と Azure AD の統合には、次の利点があります。

- Yodeck にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Yodeck にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Yodeck と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Yodeck でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Yodeck の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-yodeck-from-the-gallery"></a>ギャラリーからの Yodeck の追加
Azure AD への Yodeck の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Yodeck を追加する必要があります。

**ギャラリーから Yodeck を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Yodeck**」と入力し、結果パネルで **[Yodeck]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Yodeck](./media/yodeck-tutorial/tutorial_yodeck_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Yodeck で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Yodeck ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Yodeck の関連ユーザーの間で、リンク関係が確立されている必要があります。

Yodeck で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Yodeck のテスト ユーザーの作成](#create-a-yodeck-test-user)** - Yodeck で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Yodeck アプリケーションでシングル サインオンを構成します。

**Yodeck で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Yodeck** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/yodeck-tutorial/tutorial_yodeck_samlbase.png)

1. **[Yodeck Domain and URLs]\(Yodeck のドメインと URL)** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[Yodeck Domain and URLs]\(Yodeck のドメインと URL) のシングル サインオン情報](./media/yodeck-tutorial/tutorial_yodeck_url.png)

    **[識別子 (エンティティ ID)]** ボックスに `https://app.yodeck.com/api/v1/account/metadata/` という URL を入力します。

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[Yodeck Domain and URLs]\(Yodeck のドメインと URL) のシングル サインオン情報](./media/yodeck-tutorial/tutorial_yodeck_url1.png)

    **[サインオン URL]** ボックスに、URL として「`https://app.yodeck.com/login`」を入力します。

1. **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[App Federation Metadata Url]\(アプリケーション フェデレーション メタデータ URL\)** をコピーし、メモ帳に貼り付けます。

    ![証明書のダウンロードのリンク](./media/yodeck-tutorial/tutorial_yodeck_certificate.png)

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/yodeck-tutorial/tutorial_general_400.png)
    
1. 別の Web ブラウザー ウィンドウで、Yodeck 企業サイトに管理者としてログインします。

1. ページの右上隅にある **[ユーザー設定]** オプションをクリックし、**[アカウント設定]** を選択します。

    ![Yodeck の構成](./media/yodeck-tutorial/configure1.png)

1. **[SAML]** をクリックし、次の手順を実行します。

    ![Yodeck の構成](./media/yodeck-tutorial/configure2.png)

    a. **[URL からインポートする]** を選択します。

    b. **[URL]** テキスト ボックスに、Azure Portal からコピーした **[アプリのフェデレーション メタデータ URL]** 値を貼り付け、**[インポート]** をクリックします。
    
    c. **[アプリのフェデレーション メタデータ URL]** をインポートすると、残りのフィールドが自動的に設定されます。

    d. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/yodeck-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/yodeck-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/yodeck-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/yodeck-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-yodeck-test-user"></a>Yodeck テスト ユーザーの作成

Azure AD ユーザーが Yodeck にログインできるようにするには、ユーザーを Yodeck にプロビジョニングする必要があります。
Yodeck の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Yodeck 企業サイトに管理者としてログインします。

1. ページの右上隅にある **[ユーザー設定]** オプションをクリックし、**[ユーザー]** を選択します。

    ![従業員の追加](./media/yodeck-tutorial/user1.png)

1. **[+User]** をクリックして **[ユーザーの詳細]** タブを開きます。

    ![従業員の追加](./media/yodeck-tutorial/user2.png)

1. **[ユーザーの詳細]** ダイアログ ページで、次の手順を実行します。

    ![従業員の追加](./media/yodeck-tutorial/user3.png)

    a. **[名]** ボックスに、ユーザーの名を入力します (この例では **Britta**)。

    b. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。

    c. **[Email]\(メール\)** ボックスに、ユーザーのメール アドレス (例: **brittasimon\@contoso.com**) を入力します。

    d. 組織の要件に従って、適切な **[アカウントのアクセス許可]** オプションを選択します。
    
    e. **[Save]** をクリックします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Yodeck へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

**Yodeck に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201]

1. アプリケーションの一覧で **[Yodeck]** を選択します。

    ![アプリケーションの一覧の Yodeck のリンク](./media/yodeck-tutorial/tutorial_yodeck_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Yodeck] タイルをクリックすると、Yodeck アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/yodeck-tutorial/tutorial_general_01.png
[2]: ./media/yodeck-tutorial/tutorial_general_02.png
[3]: ./media/yodeck-tutorial/tutorial_general_03.png
[4]: ./media/yodeck-tutorial/tutorial_general_04.png

[100]: ./media/yodeck-tutorial/tutorial_general_100.png

[200]: ./media/yodeck-tutorial/tutorial_general_200.png
[201]: ./media/yodeck-tutorial/tutorial_general_201.png
[202]: ./media/yodeck-tutorial/tutorial_general_202.png
[203]: ./media/yodeck-tutorial/tutorial_general_203.png


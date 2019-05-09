---
title: チュートリアル:Azure Active Directory と Screencast-O-Matic の統合 | Microsoft Docs
description: Azure Active Directory と Screencast-O-Matic の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 525ad47d-5488-40e2-aeaf-ae6183745682
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 941a02ef9ab3d623d34c8dc6996040f6d9e44859
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58096845"
---
# <a name="tutorial-azure-active-directory-integration-with-screencast-o-matic"></a>チュートリアル:Azure Active Directory と Screencast-O-Matic の統合

このチュートリアルでは、Screencast-O-Matic と Azure Active Directory (Azure AD) を統合する方法について説明します。

Screencast-O-Matic と Azure AD の統合には、次の利点があります。

- Screencast-O-Matic にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Screencast-O-Matic にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Screencast-O-Matic の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Screencast-O-Matic でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Screencast-O-Matic の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-screencast-o-matic-from-the-gallery"></a>ギャラリーからの Screencast-O-Matic の追加
Azure AD への Screencast-O-Matic の統合を構成するには、ギャラリーからマネージド SaaS アプリのリストに Screencast-O-Matic を追加する必要があります。

**ギャラリーから Screencast-O-Matic を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Screencast-O-Matic**」と入力し、結果ウィンドウで **[Screencast-O-Matic]** を選び、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果リストの Screencast-O-Matic](./media/screencast-tutorial/tutorial_screencast_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Screencast-O-Matic で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Screencast-O-Matic ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Screencast-O-Matic の関連ユーザーの間で、リンク関係が確立されている必要があります。

Screencast-O-Matic で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Screencast-O-Matic テスト ユーザーの作成](#create-a-screencast-o-matic-test-user)** - Screencast-O-Matic で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、Screencast-O-Matic アプリケーションでシングル サインオンを構成します。

**Screencast-O-Matic で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure portal の **Screencast-O-Matic** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/screencast-tutorial/tutorial_screencast_samlbase.png)

3. **[Screencast-O-Matic Domain and URLs]\(Screencast-O-Matic のドメインと URL\)** セクションで、次の手順を実行します。

    ![[Screencast-O-Matic のドメインと URL] のシングル サインオン情報](./media/screencast-tutorial/tutorial_screencast_url.png)

    **[サインオン URL]** ボックスに、`https://screencast-o-matic.com/<InstanceName>` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > サインオン URL は実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得する場合は、[Screencast-O-Matic クライアント サポート チーム](mailto:support@screencast-o-matic.com)にお問い合わせください。 
 
4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/screencast-tutorial/tutorial_screencast_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/screencast-tutorial/tutorial_general_400.png)

6. 別の Web ブラウザー ウィンドウで、Screencast-O-Matic に管理者としてログインします。

7. **[サブスクリプション]** をクリックします。

    ![サブスクリプション](./media/screencast-tutorial/tutorial_screencast_sub.png)

8. **[Access page]\(アクセス ページ\)** セクションで、**[セットアップ]** をクリックします。

    ![アクセス](./media/screencast-tutorial/tutorial_screencast_setup.png)

9. **[Setup Access Page]\(アクセス ページのセットアップ\)** で、次の手順を実行します。

   * **[アクセス URL]** セクションで、指定されたテキスト ボックスにインスタンス名を入力します。

     ![アクセス](./media/screencast-tutorial/tutorial_screencast_access.png)

   * **[SAML User Restriction (optional)]\(SAML ユーザーの制限 (省略可能)\)** セクションで、**[Require Domain User]\(ドメイン ユーザーが必要\)** を選択します。

   * **[Upload IDP Metadata XML File]\(IDP メタデータ XML ファイルのアップロード\)** で、**[ファイルの選択]** をクリックして、Azure portal からダウンロードしたメタデータをアップロードします。

   * Click **OK**. 

     ![アクセス](./media/screencast-tutorial/tutorial_screencast_save.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/screencast-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/screencast-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/screencast-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/screencast-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-screencast-o-matic-test-user"></a>Screencast-O-Matic テスト ユーザーの作成

このセクションの目的は、Screencast-O-Matic で Britta Simon というユーザーを作成することです。 Screencast-O-Matic では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Screencast-O-Matic にアクセスしようとすると、ユーザーがまだ存在しない場合は新しいユーザーが作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、 [Screencast-O-Matic クライアント サポート チーム](mailto:support@screencast-o-matic.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Screencast-O-Matic へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Screencast-O-Matic に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Screencast-O-Matic]** を選択します。

    ![アプリケーションの一覧の Screencast-O-Matic のリンク](./media/screencast-tutorial/tutorial_screencast_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Screencast-O-Matic] タイルをクリックすると、自動的に Screencast-O-Matic アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/screencast-tutorial/tutorial_general_01.png
[2]: ./media/screencast-tutorial/tutorial_general_02.png
[3]: ./media/screencast-tutorial/tutorial_general_03.png
[4]: ./media/screencast-tutorial/tutorial_general_04.png

[100]: ./media/screencast-tutorial/tutorial_general_100.png

[200]: ./media/screencast-tutorial/tutorial_general_200.png
[201]: ./media/screencast-tutorial/tutorial_general_201.png
[202]: ./media/screencast-tutorial/tutorial_general_202.png
[203]: ./media/screencast-tutorial/tutorial_general_203.png


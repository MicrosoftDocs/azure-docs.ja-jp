---
title: チュートリアル:Azure Active Directory と iWellnessNow の統合 | Microsoft Docs
description: Azure Active Directory と iWellnessNow の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 24ffc841-7a77-481c-9cc4-6f8bda58fe66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02b831df98db5b9d63873a0da93e603cd7cbf308
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57456042"
---
# <a name="tutorial-azure-active-directory-integration-with-iwellnessnow"></a>チュートリアル:Azure Active Directory と iWellnessNow の統合

このチュートリアルでは、iWellnessNow と Azure Active Directory (Azure AD) を統合する方法について説明します。

iWellnessNow と Azure AD の統合には、次の利点があります。

- iWellnessNow にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで iWellnessNow に自動的にサインオン (シングル サインオン) できるように設定できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

iWellnessNow と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- iWellnessNow でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの iWellnessNow の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-iwellnessnow-from-the-gallery"></a>ギャラリーからの iWellnessNow の追加
Azure AD への iWellnessNow の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に iWellnessNow を追加する必要があります。

**ギャラリーから iWellnessNow を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**iWellnessNow**」と入力し、結果パネルで **iWellnessNow** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの iWellnessNow](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、iWellnessNow で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する iWellnessNow ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと iWellnessNow の関連ユーザーの間で、リンク関係が確立されている必要があります。

iWellnessNow で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[iWellnessNow テスト ユーザーの作成](#create-an-iwellnessnow-test-user)** - iWellnessNow で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、iWellnessNow アプリケーションでシングル サインオンを構成します。

**iWellnessNow で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure portal の **iWellnessNow** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_samlbase.png)

1. **[iWellnessNow のドメインと URL]** セクションで、**サービス プロバイダー メタデータ ファイル**があるときに **IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[iWellnessNow のドメインと URL] のシングル サインオンのアップロード](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_upload.png)

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    ![[iWellnessNow のドメインと URL] のシングル サインオンの uploadconfig](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_uploadconfig.png)

    b. **フォルダー ロゴ**をクリックしてメタデータ ファイルを選択し、**[アップロード]** をクリックします。
    
    c. **サービス プロバイダー メタデータ ファイル**のアップロードが正常に完了すると、次のように、**識別子**と**応答 URL** の値が **[iWellnessNow のドメインと URL]** セクションのテキストボックスに自動的に入力されます。

    ![[iWellnessNow のドメインと URL] のシングル サインオン情報](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_url3.png)

1. **サービス プロバイダー メタデータ ファイル**がないときに **IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[iWellnessNow のドメインと URL] のシングル サインオン情報](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_url.png)

    a. **[識別子]** ボックスに、`http://<CustomerName>.iwellnessnow.com` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<CustomerName>.iwellnessnow.com/ssologin` のパターンを使用して URL を入力します。

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[iWellnessNow のドメインと URL] のシングル サインオン情報](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_url1.png)

    **[サインオン URL]** ボックスに、`https://<CustomerName>.iwellnessnow.com/` のパターンを使用して URL を入力します。
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[iWellnessNow クライアント サポート チーム](mailto:info@iwellnessnow.com)にお問い合わせください。

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/iwellnessnow-tutorial/tutorial_general_400.png)
    
1. **iWellnessNow** 側にシングル サインオンを構成するには、ダウンロードした**メタデータ XML** を [iWellnessNow サポート チーム](mailto:info@iwellnessnow.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/iwellnessnow-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/iwellnessnow-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/iwellnessnow-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/iwellnessnow-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-an-iwellnessnow-test-user"></a>iWellnessNow テスト ユーザーの作成

このセクションでは、iWellnessNow で Britta Simon というユーザーを作成します。  [iWellnessNow サポート チーム](mailto:info@iwellnessnow.com)と連携し、iWellnessNow プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に iWellnessNow へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**iWellnessNow に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[iWellnessNow]** を選択します。

    ![アプリケーションの一覧の iWellnessNow のリンク](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [iWellnessNow] タイルをクリックすると、自動的に iWellnessNow アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/iwellnessnow-tutorial/tutorial_general_01.png
[2]: ./media/iwellnessnow-tutorial/tutorial_general_02.png
[3]: ./media/iwellnessnow-tutorial/tutorial_general_03.png
[4]: ./media/iwellnessnow-tutorial/tutorial_general_04.png

[100]: ./media/iwellnessnow-tutorial/tutorial_general_100.png

[200]: ./media/iwellnessnow-tutorial/tutorial_general_200.png
[201]: ./media/iwellnessnow-tutorial/tutorial_general_201.png
[202]: ./media/iwellnessnow-tutorial/tutorial_general_202.png
[203]: ./media/iwellnessnow-tutorial/tutorial_general_203.png


---
title: チュートリアル:Azure Active Directory と楽楽精算の統合 | Microsoft Docs
description: Azure Active Directory と楽楽精算の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6f86a8a2-4bd0-40cc-b1b4-752fce123328
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b7dee41b226cdacea1d9c7f1cf581d9f095977e
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56190774"
---
# <a name="tutorial-azure-active-directory-integration-with-settling-music"></a>チュートリアル:Azure Active Directory と楽楽精算の統合

このチュートリアルでは、楽楽精算と Azure Active Directory (Azure AD) を統合する方法について説明します。

楽楽精算と Azure AD の統合には、次の利点があります。

- 楽楽精算にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで楽楽精算に自動的にサインオン (シングル サインオン) できるように設定できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と楽楽精算の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- 楽楽精算でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの楽楽精算の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-settling-music-from-the-gallery"></a>ギャラリーからの楽楽精算の追加
Azure AD への楽楽精算の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に楽楽精算を追加する必要があります。

**ギャラリーから楽楽精算を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**楽楽精算**」と入力し、結果パネルで**楽楽精算**を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの楽楽精算](./media/settlingmusic-tutorial/tutorial_settlingmusic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、楽楽精算で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する楽楽精算ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと楽楽精算の関連ユーザーの間で、リンク関係が確立されている必要があります。

楽楽精算で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[楽楽精算テスト ユーザーの作成](#create-a-settling-music-test-user)** - 楽楽精算で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、楽楽精算アプリケーションでシングル サインオンを構成します。

**楽楽精算で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure portal の**楽楽精算**アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/settlingmusic-tutorial/tutorial_settlingmusic_samlbase.png)

1. **[楽楽精算のドメインと URL]** セクションで、次の手順に従います。

    ![[楽楽精算のドメインと URL] のシングル サインオン情報](./media/settlingmusic-tutorial/tutorial_settlingmusic_url.png)

    a. **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[楽楽精算クライアント サポート チーム](https://rakurakuseisan.jp/)にお問い合わせください。 
 
1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/settlingmusic-tutorial/tutorial_settlingmusic_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/settlingmusic-tutorial/tutorial_general_400.png)

1. **[楽楽精算構成]** セクションで、**[楽楽精算の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![楽楽精算構成](./media/settlingmusic-tutorial/tutorial_settlingmusic_configure.png) 

1. 別の Web ブラウザー ウィンドウで、セキュリティ管理者として楽楽精算にログインします。

1. ページ上部で **[管理]** タブをクリックします。

    ![楽楽精算手順 1](./media/settlingmusic-tutorial/tutorial_settlingmusic_step1.png)

1. **[システム設定]** タブをクリックします。

    ![楽楽精算手順 2](./media/settlingmusic-tutorial/tutorial_settlingmusic_step2.png)

1. **[セキュリティ]** タブに切り替えます。

    ![楽楽精算手順 3](./media/settlingmusic-tutorial/tutorial_settlingmusic_step3.png)

1. **[シングル サインオンの設定]** セクションで、次の手順に従います。

    ![楽楽精算手順 5](./media/settlingmusic-tutorial/tutorial_settlingmusic_step4.png)

    a. **[有効にする]** をクリックします。

    b. **[ID プロバイダーのログイン URL]** ボックスに、Azure portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    c. **[ID プロバイダーのログアウト URL]** ボックスに、Azure portal からコピーした **サインアウト URL** の値を貼り付けます。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **[Choose File]** をクリックして、Azure portal からダウンロードした**証明書 (Base64)** をアップロードします。

    e. **[保存]** ボタンをクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/settlingmusic-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/settlingmusic-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/settlingmusic-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/settlingmusic-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。
 
### <a name="create-a-settling-music-test-user"></a>楽楽精算テスト ユーザーの作成

このセクションでは、楽楽精算で Britta Simon というユーザーを作成します。  [楽楽精算クライアント サポート チーム](https://rakurakuseisan.jp/)と連携し、楽楽精算プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に楽楽精算へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**楽楽精算に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[楽楽精算]** を選択します。

    ![アプリケーションの一覧の楽楽精算のリンク](./media/settlingmusic-tutorial/tutorial_settlingmusic_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [楽楽精算] タイルをクリックすると、自動的に楽楽精算アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/settlingmusic-tutorial/tutorial_general_01.png
[2]: ./media/settlingmusic-tutorial/tutorial_general_02.png
[3]: ./media/settlingmusic-tutorial/tutorial_general_03.png
[4]: ./media/settlingmusic-tutorial/tutorial_general_04.png

[100]: ./media/settlingmusic-tutorial/tutorial_general_100.png

[200]: ./media/settlingmusic-tutorial/tutorial_general_200.png
[201]: ./media/settlingmusic-tutorial/tutorial_general_201.png
[202]: ./media/settlingmusic-tutorial/tutorial_general_202.png
[203]: ./media/settlingmusic-tutorial/tutorial_general_203.png


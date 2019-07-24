---
title: チュートリアル:Azure Active Directory と XaitPorter の統合 | Microsoft Docs
description: Azure Active Directory と XaitPorter の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d33c7cb7-0550-425b-882a-619a713a71b7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cd4f9e561a55c010273b48fb4232348baad3582
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56200940"
---
# <a name="tutorial-azure-active-directory-integration-with-xaitporter"></a>チュートリアル:Azure Active Directory と XaitPorter の統合

このチュートリアルでは、XaitPorter と Azure Active Directory (Azure AD) を統合する方法について説明します。

XaitPorter と Azure AD の統合には、次の利点があります。

- XaitPorter にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に XaitPorter にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

XaitPorter と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- XaitPorter でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから XaitPorter を追加する
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-xaitporter-from-the-gallery"></a>ギャラリーから XaitPorter を追加する
Azure AD への XaitPorter の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に XaitPorter を追加する必要があります。

**ギャラリーから XaitPorter を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに **「XaitPorter」** と入力し、結果ウィンドウで **[XaitPorter]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの XaitPorter](./media/xaitporter-tutorial/tutorial_xaitporter_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、XaitPorter で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する XaitPorter ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと XaitPorter の関連ユーザーの間で、リンク関係が確立されている必要があります。

XaitPorter で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

XaitPorter で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[XaitPorter のテスト ユーザーの作成](#create-a-xaitporter-test-user)** - XaitPorter で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、XaitPorter アプリケーションでシングル サインオンを構成します。

**XaitPorter で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **XaitPorter** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/xaitporter-tutorial/tutorial_xaitporter_samlbase.png)

1. **[XaitPorter のドメインと URL]** セクションで、次の手順に従います。

    ![[XaitPorter のドメインと URL] のシングル サインオン情報](./media/xaitporter-tutorial/tutorial_xaitporter_url.png)

    a. **[サインオン URL]** ボックスに、`https://<subdomain>.xaitporter.com/saml/login` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<subdomain>.xaitporter.com` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[XaitPorter クライアント サポート チーム](https://www.xait.com/support/)に問い合わせてください。
     
1. **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[App Federation Metadata Url]\(アプリケーション フェデレーション メタデータ URL\)** をコピーし、メモ帳に貼り付けます。 

    ![証明書のダウンロードのリンク](./media/xaitporter-tutorial/tutorial_xaitporter_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/xaitporter-tutorial/tutorial_general_400.png)

1. **IP アドレス**または**アプリのフェデレーション メタデータ URL** を [SmartRecruiters サポート チーム](https://www.smartrecruiters.com/about-us/contact-us/)に提供し、XaitPorter がホワイトリストを構成して、IP アドレスがお使いの XaitPorter インスタンスから到達可能にできるようにします。 

1. 別の Web ブラウザー ウィンドウで、XaitPorter 企業サイトに管理者としてログインします。

1. **[管理]** をクリックします。

    ![Configure single sign-on](./media/xaitporter-tutorial/user1.png)

1. **[システム設定]** ドロップダウン リストから、**[シングル サインオンの管理]** を選択します。

    ![Configure single sign-on](./media/xaitporter-tutorial/user2.png)

1. **[シングル サインオンの管理]** セクションで、次の手順に従います。

    ![Configure single sign-on](./media/xaitporter-tutorial/user3.png)

    a. **[シングル サインオンを有効にする]** を選択します。

    b. **[ID プロバイダーの設定]** ボックスで、Azure Portal からコピーした**アプリのフェデレーション メタデータ URL** を貼り付けて、**[フェッチ]** をクリックします。

    c. **[Enable Autocreation of Users]\(ユーザーの自動作成を有効にする\)** を選択します。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 Click **OK**.

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/xaitporter-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/xaitporter-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/xaitporter-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/xaitporter-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。
 
### <a name="create-a-xaitporter-test-user"></a>XaitPorter のテスト ユーザーを作成する

このセクションでは、XaitPorter で Britta Simon というユーザーを作成します。 XaitPorter プラットフォームでユーザーを追加するには、 [XaitPorter クライアント サポート チーム](https://www.xait.com/support/) に問い合わせてください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に XaitPorter へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**XaitPorter に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[XaitPorter]** を選択します。

    ![アプリケーションの一覧の [XaitPorter] リンク](./media/xaitporter-tutorial/tutorial_xaitporter_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [XaitPorter] タイルをクリックすると、自動的に XaitPorter アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/xaitporter-tutorial/tutorial_general_01.png
[2]: ./media/xaitporter-tutorial/tutorial_general_02.png
[3]: ./media/xaitporter-tutorial/tutorial_general_03.png
[4]: ./media/xaitporter-tutorial/tutorial_general_04.png

[100]: ./media/xaitporter-tutorial/tutorial_general_100.png

[200]: ./media/xaitporter-tutorial/tutorial_general_200.png
[201]: ./media/xaitporter-tutorial/tutorial_general_201.png
[202]: ./media/xaitporter-tutorial/tutorial_general_202.png
[203]: ./media/xaitporter-tutorial/tutorial_general_203.png


---
title: チュートリアル:Azure Active Directory と Supermood の統合 | Microsoft Docs
description: Azure Active Directory と Supermood の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: afc04efa-2eba-4e47-8ce4-b71eb293cd09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60efdd5b509045ce2398720d650894983c2cfc23
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198526"
---
# <a name="tutorial-azure-active-directory-integration-with-supermood"></a>チュートリアル:Azure Active Directory と Supermood の統合

このチュートリアルでは、Supermood と Azure Active Directory (Azure AD) を統合する方法について説明します。

Supermood と Azure AD の統合には、次の利点があります。

- Supermood にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで Supermood に自動的にサインオン (シングル サインオン) できるように設定できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Supermood と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Supermood でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Supermood の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-supermood-from-the-gallery"></a>ギャラリーからの Supermood の追加
Azure AD への Supermood の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Supermood を追加する必要があります。

**ギャラリーから Supermood を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Supermood**」と入力し、結果パネルで **Supermood** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Supermood](./media/supermood-tutorial/tutorial_supermood_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Supermood で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Supermood ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Supermood の関連ユーザーの間で、リンク関係が確立されている必要があります。

Supermood で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Supermood のテスト ユーザーの作成](#create-a-supermood-test-user)** - Supermood で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、Supermood アプリケーションでシングル サインオンを構成します。

**Supermood で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure portal の **Supermood** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/supermood-tutorial/tutorial_supermood_samlbase.png)

1. **[Supermood のドメインと URL]** セクションで、次の手順に従います。

    ![[Supermood のドメインと URL] のシングル サインオン情報](./media/supermood-tutorial/tutorial_supermood_url.png)

    a. **[詳細な URL 設定の表示]** をクリックします。

    b. **[IDP]** 開始モードでアプリケーションを構成する場合は、**[リレー状態]** テキストボックスに次の URL を入力します: `https://supermood.co/auth/sso/saml20`

    c. **[SP]** 開始モードでアプリケーションを構成する場合は、**[サインオン URL]** テキストボックスに次の URL を入力します: `https://supermood.co/app/#!/loginv2`

1. Supermood アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 次のスクリーンショットはその例です。
    
    ![Configure single sign-on](./media/supermood-tutorial/tutorial_supermood_attribute.png)

1. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、上の図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | ---------------| --------------- |    
    | firstName | User.givenname |
    | lastName | User.surname |

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure single sign-on](./media/supermood-tutorial/tutorial_attribute_04.png)

    ![Configure single sign-on](./media/supermood-tutorial/tutorial_attribute_05.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **[名前空間]** は空白のままにします。
    
    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **[OK]** をクリックします。

1. **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[App Federation Metadata Url]\(アプリケーション フェデレーション メタデータ URL\)** をコピーし、メモ帳に貼り付けます。

    ![証明書のダウンロードのリンク](./media/supermood-tutorial/tutorial_supermood_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/supermood-tutorial/tutorial_general_400.png)

1. セキュリティ管理者として、Supermood.co 管理パネルに移動します。

1. **[My account]\(マイ アカウント\)** (左下) と **[Single Sign On (SSO)]\(シングル サインオン (SSO)\)** をクリックします。

    ![証明書シングル](./media/supermood-tutorial/tutorial_supermood_single.png)
1. **[Your SAML 2.0 configurations]\(SAML 2.0 構成)\\** で **[Add an SAML 2.0 configuration for an email domain]\(電子メール ドメインの SAML 2.0 構成の追加\)** をクリックします。

    ![証明書の追加](./media/supermood-tutorial/tutorial_supermood_add.png)

1. **[Add an SAML 2.0 configuration for an email domain]\(電子メール ドメインの SAML 2.0 構成の追加\)**  セクションで、次の手順に従います。

    ![証明書 saml](./media/supermood-tutorial/tutorial_supermood_saml.png)

    a. **[email domain for this Identity provider]\(この ID プロバイダーの電子メール ドメイン\)** テキストボックスに、自分のドメインを入力します。

    b. **[Use a metadata URL]\(メタデータ URL の使用\)** テキストボックスに、Azure portal からコピーした**アプリのフェデレーション メタデータ URL** を貼り付けます。

    c. **[追加]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/supermood-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/supermood-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/supermood-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/supermood-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。
 
### <a name="create-a-supermood-test-user"></a>Supermood テスト ユーザーの作成

このセクションでは、Supermood で Britta Simon というユーザーを作成します。 Supermood では、Just-In-Time プロビジョニングがサポートされます。Just-In-Time プロビジョニングは、Supermood 側の構成中に追加されるドメインに属する電子メールを持つユーザーに対して既定で有効になります。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ存在しない場合、Supermood にアクセスしようとすると、新しいユーザーが作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、 [Supermood サポート チーム](mailto:hello@supermood.fr)にお問い合わせください。


### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Supermood へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Supermood に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Supermood]** を選択します。

    ![アプリケーションの一覧の Supermood のリンク](./media/supermood-tutorial/tutorial_supermood_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Supermood] タイルをクリックすると、自動的に Supermood アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/supermood-tutorial/tutorial_general_01.png
[2]: ./media/supermood-tutorial/tutorial_general_02.png
[3]: ./media/supermood-tutorial/tutorial_general_03.png
[4]: ./media/supermood-tutorial/tutorial_general_04.png

[100]: ./media/supermood-tutorial/tutorial_general_100.png

[200]: ./media/supermood-tutorial/tutorial_general_200.png
[201]: ./media/supermood-tutorial/tutorial_general_201.png
[202]: ./media/supermood-tutorial/tutorial_general_202.png
[203]: ./media/supermood-tutorial/tutorial_general_203.png


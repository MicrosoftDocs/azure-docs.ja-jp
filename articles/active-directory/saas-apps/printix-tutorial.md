---
title: 'チュートリアル: Azure Active Directory と Printix の統合 | Microsoft Docs'
description: Azure Active Directory と Printix の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: dfde9bbbeb7f6b349ecbdc4c2da605d39a0708da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94357880"
---
# <a name="tutorial-azure-active-directory-integration-with-printix"></a>チュートリアル: Azure Active Directory と Printix の統合

このチュートリアルでは、Printix と Azure Active Directory (Azure AD) を統合する方法について説明します。

Printix と Azure AD の統合には、次の利点があります。

- Printix にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Printix にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Printix と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Printix でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Printix を追加する
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-printix-from-the-gallery"></a>ギャラリーから Printix を追加する
Azure AD への Printix の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Printix を追加する必要があります。

**ギャラリーから Printix を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、 **[すべてのアプリケーション]** に移動します。

    ![[管理] の下で Azure portal の [エンタープライズ アプリケーション] が選択され、[すべてのアプリケーション] が選択されたことを示すスクリーンショット。][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] が選択されたことを示すスクリーンショット。][3]

1. 検索ボックスに、「 **Printix**」と入力します。

    ![[ギャラリーから追加する] ダイアログ ボックスで「Printix」を検索している状態を示すスクリーンショット。](./media/printix-tutorial/tutorial_printix_search.png)

1. 結果パネルで **[Printix]** を選択し、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![[Printix] オプションが選択されたことを示すスクリーンショット。](./media/printix-tutorial/tutorial_printix_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Printix で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Printix ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Printix の関連ユーザーの間で、リンク関係が確立されている必要があります。

Printix で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Printix で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD シングル サインオンをテストします。
1. **[Printix のテスト ユーザーの作成](#creating-a-printix-test-user)** - Printix で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Printix アプリケーションでシングル サインオンを構成します。

**Printix で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Printix** アプリケーション統合ページで、 **[シングル サインオン]** をクリックします。

    ![Azure portal の [管理] の下で [シングル サインオン] が選択されたことを示すスクリーンショット。][4]

1. **[シングル サインオン]** ダイアログで、 **[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[SAML ベースのサインオン] モードが選択されたことを示すスクリーンショット。](./media/printix-tutorial/tutorial_printix_samlbase.png)

1. **[Printix のドメインと URL]** セクションで、次の手順に従います。

    ![サインオンのための URL を指定できる [Printix のドメインと URL] セクションを示すスクリーンショット。](./media/printix-tutorial/tutorial_printix_url.png)

    **[サインオン URL]** ボックスに、`https://<subdomain>.printix.net` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Printix クライアント サポート チーム](mailto:support@printix.net)に問い合わせてください。 
 
1. **[SAML 署名証明書]** セクションで、 **[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![[SAML 署名証明書] ウィンドウを示すスクリーンショット。ここでは、証明書をダウンロードできます。](./media/printix-tutorial/tutorial_printix_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[Save]\(保存\) ボタンを示すスクリーンショット。](./media/printix-tutorial/tutorial_general_400.png)

1. Printix テナントに管理者としてサインオンします。

1. 上部のメニューで右上隅のアイコンをクリックし、 **[認証]** を選択します。
   
    ![メニューから [認証] が選択された状態を示すスクリーンショット。](./media/printix-tutorial/tutorial_printix_06.png)

1. **[セットアップ]** タブの **[Enable Azure/Office 365 authentication (Azure/Office 365 認証の有効化)]** を選択します。
   
    ![[Enable Azure/Office 365 authentication]\(Azure/Office 365 認証の有効化\) が選択できる [Printix.net] ページを示すスクリーンショット。](./media/printix-tutorial/tutorial_printix_07.png)

1. **[Azure]** タブの **[フェデレーション メタデータ ドキュメント]** ボックスにフェデレーション メタデータの URL を入力します。 

    Azure AD からダウンロードしたメタデータの XML ファイルを添付して [Printix サポート チーム](mailto:support@printix.net)に送信します。 XML ファイルはサポート チームによってアップロードされ、フェデレーション メタデータの URL が支給されます。
   
    ![フェデレーション メタデータ ドキュメントを指定できる [Printix.net] ページを示すスクリーンショット。](./media/printix-tutorial/tutorial_printix_08.png)
   
1. テストに成功した場合は、 **[Test (テスト)]** ボタンをクリックし、 **[OK]** ボタンをクリックします。
   
     **テスト** ボタンをクリックした後に Azure Active Directory ページが表示されます。 ここで「テストに成功した」とは、Azure テスト アカウントの資格情報を入力した後にメッセージ "Settings tested OK" がポップ アップ表示されるということです。 **[OK]** ボタンをクリックしてください。
   
    ![テストの結果を示すスクリーンショット。](./media/printix-tutorial/tutorial_printix_09.png)

1. **[Authentication (認証)]** ページの **[Save (保存)]** ボタンをクリックします。


> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、 **[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![作成する名前とユーザー名が表示されているスクリーンショット。](./media/printix-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、 **[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure portal の Azure A D アイコンを示すスクリーンショット。](./media/printix-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、 **[ユーザー]** ダイアログを開きます。
 
    ![[管理] メニューで [ユーザーとグループ] が選択され、[すべてのユーザー] が選択されていることを示すスクリーンショット。](./media/printix-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![説明されている値を入力できる [ユーザー] ダイアログ ボックスが表示されているスクリーンショット。](./media/printix-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の **電子メール アドレス** を入力します。

    c. **[パスワードを表示]** を選択し、 **[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-printix-test-user"></a>Printix テスト ユーザーの作成

このセクションの目的は、Printix で Britta Simon というユーザーを作成することです。 Printix では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 存在しない Printix ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。 

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[Printix のサポート チーム](mailto:support@printix.net)にお問い合わせください。
> 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Printix へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようします。

![既定のアクセス権を持つユーザーを示すスクリーンショット。][200] 

**Printix に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、 **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** をクリックします。

    ![[管理] の [エンタープライズ アプリケーション] が選択され、[すべてのアプリケーション] が選択された状態のスクリーンショット。][201] 

1. アプリケーションの一覧で **[Printix]** を選択します。

    ![[Printix] を選択できるアプリケーションの一覧を示すスクリーンショット。](./media/printix-tutorial/tutorial_printix_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[管理] メニューから [ユーザーとグループ] が選択された状態を示すスクリーンショット。][202] 

1. **[追加]** ボタンをクリックします。 次に、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] を選択できる、[追加] ボタンと [割り当ての追加] ページが表示されているスクリーンショット。][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Printix] タイルをクリックすると、自動的に Printix アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/printix-tutorial/tutorial_general_01.png
[2]: ./media/printix-tutorial/tutorial_general_02.png
[3]: ./media/printix-tutorial/tutorial_general_03.png
[4]: ./media/printix-tutorial/tutorial_general_04.png

[100]: ./media/printix-tutorial/tutorial_general_100.png

[200]: ./media/printix-tutorial/tutorial_general_200.png
[201]: ./media/printix-tutorial/tutorial_general_201.png
[202]: ./media/printix-tutorial/tutorial_general_202.png
[203]: ./media/printix-tutorial/tutorial_general_203.png


---
title: チュートリアル:Azure Active Directory と PageDNA の統合 | Microsoft Docs
description: Azure Active Directory と PageDNA の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c8765864-45f4-48c2-9d86-986a4aa431e4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c7f8c8efcad0a07a3d3a56925866b10d94f82ed
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227480"
---
# <a name="tutorial-azure-active-directory-integration-with-pagedna"></a>チュートリアル:Azure Active Directory と PageDNA の統合

このチュートリアルでは、PageDNA と Azure Active Directory (Azure AD) を統合する方法について説明します。

PageDNA と Azure AD の統合には、次の利点があります。

* Azure AD で、PageDNA にアクセスできるユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して PageDNA に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure ポータル) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

PageDNA と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
* シングル サインオンが有効な PageDNA のサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストし、PageDNA を Azure AD と統合します。

PageDNA では、次の機能をサポートしています。

* SP によって開始されるシングル サインオン (SSO)。

* Just-In-Time のユーザー プロビジョニング。

## <a name="add-pagedna-from-the-azure-marketplace"></a>Azure Marketplace からの PageDNA の追加

Azure AD への PageDNA の統合を構成するには、Azure Marketplace からマネージド SaaS アプリの一覧に PageDNA を追加する必要があります。

1. [Azure Portal](https://portal.azure.com?azure-portal=true) にサインインします。
1. 左ウィンドウで、 **[Azure Active Directory]** を選択します。

    ![[Azure Active Directory] オプション](common/select-azuread.png)

1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ウィンドウ](common/enterprise-applications.png)

1. 新しいアプリケーションを追加するには、ウィンドウの上部の **[+ 新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] オプション](common/add-new-app.png)

1. 検索ボックスに「**PageDNA**」と入力します。 検索結果で **[PageDNA]** を選択し、 **[追加]** を選択してアプリケーションを追加します。

    ![結果一覧の PageDNA](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、PageDNA で Azure AD のシングル サインオンを構成し、テストします。 シングル サインオンを機能させるには、Azure AD ユーザーと PageDNA 内の関連ユーザーとの間にリンク関係を確立する必要があります。

PageDNA で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[PageDNA シングル サインオンの構成](#configure-pagedna-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[PageDNA テスト ユーザーの作成](#create-a-pagedna-test-user)** - Britta Simon という Azure AD ユーザーにリンクされている Britta Simon というユーザーが PageDNA に存在するようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

PageDNA で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **PageDNA** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン オプションを構成する](common/select-sso.png)

1. **[シングル サインオン方式の選択]** ウィンドウで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

1. **[SAML でシングル サインオンをセットアップします]** ウィンドウで、**編集** (鉛筆アイコン) を選択して **[基本的な SAML 構成]** ウィンドウを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ウィンドウで、次の手順を実行します。

    ![[PageDNA のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    1. **[サインオン URL]** ボックスに、以下のいずれかの形式で URL を入力します。

        ||
        |--|
        | `https://stores.pagedna.com/<your site>` |
        | `https://<your domain>` |
        | `https://<your domain>/<your site>` |
        | `https://www.nationsprint.com/<your site>` |
        | |

    1. **[識別子 (エンティティ ID)]** ボックスに、以下のいずれかの形式で URL を入力します。

        ||
        |--|
        | `https://stores.pagedna.com/<your site>/saml2ep.cgi` |
        | `https://www.nationsprint.com/<your site>/saml2ep.cgi` |
        | |

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[PageDNA サポート チーム](mailto:success@pagedna.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** ウィンドウに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ウィンドウの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** を選択して特定のオプションの**証明書 (未加工)** をダウンロードし、コンピューターに保存します。

    ![証明書 (未加工) のダウンロード オプション](common/certificateraw.png)

1. **[PageDNA のセットアップ]** セクションで、必要な URL をコピーします。

   * **ログイン URL**
   * **Azure AD 識別子**
   * **ログアウト URL**

    ![構成 URL をコピーする](common/copy-configuration-urls.png)

### <a name="configure-pagedna-single-sign-on"></a>PageDNA シングル サインオンの構成

PageDNA 側でシングル サインオンを構成するには、ダウンロードした証明書 (未加工) と Azure portal からコピーした適切な URL を [PageDNA サポート チーム](mailto:success@pagedna.com)に送信する必要があります。 PageDNA チームは、SAML SSO 接続が両方の側で正しく設定されていることを確認します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で Britta Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]**    >  **[ユーザー]**  >  **[すべてのユーザー]** の順に選択します。

    ![[ユーザー] と [すべてのユーザー] オプション](common/users.png)

1. 画面の上部にある **[+ 新しいユーザー]** を選択します。

    ![[新しいユーザー] オプション](common/new-user.png)

1. **[ユーザー]** ウィンドウで、次の手順を実行します。

    ![[ユーザー] ウィンドウ](common/user-properties.png)

    1. **[名前]** ボックスに「**BrittaSimon**」と入力します。
  
    1. **[ユーザー名]** ボックスに、「**BrittaSimon\@\<yourcompanydomain>.\<extension>** 」と入力します。 たとえば、「**BrittaSimon\@contoso.com**」です。

    1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。

    1. **作成** を選択します。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon というユーザーに PageDNA へのユーザー アクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]**  >  **[PageDNA]** の順に選択します。

    ![[エンタープライズ アプリケーション] ウィンドウ](common/enterprise-applications.png)

1. アプリケーションの一覧で **[PageDNA]** を選択します。

    ![アプリケーションの一覧の PageDNA](common/all-applications.png)

1. 左側のウィンドウの **[管理]** で、 **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] オプション](common/users-groups-blade.png)

1. **[+ ユーザーの追加]** を選択し、 **[割り当ての追加]** ウィンドウで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

1. **[ユーザーとグループ]** ウィンドウの **[ユーザー]** の一覧で **[Britta Simon]** を選択し、ウィンドウの下部にある **[選択]** を選択します。

1. SAML アサーションでロール値が必要な場合は、 **[ロールの選択]** ウィンドウで、一覧からユーザーに適したロールを選択します。 ウィンドウの下部で、 **[選択]** を選択します。

1. **[割り当ての追加]** ウィンドウで **[割り当て]** を選択します。

### <a name="create-a-pagedna-test-user"></a>PageDNA テスト ユーザーの作成

これで、Britta Simon というユーザーが PageDNA に作成されました。 このユーザーを作成するために、何かをする必要はありません。 PageDNA では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 PageDNA に Britta Simon というユーザーがまだ存在しない場合は、認証後に新しく作成されます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、マイ アプリ ポータルを使用して自分の Azure AD のシングル サインオン構成をテストします。

マイ アプリ ポータルで **[PageDNA]** を選択すると、シングル サインオンを設定した PageDNA サブスクリプションに自動的にサインインするはずです。 マイアプリ ポータルの詳細については、「[マイ アプリ ポータルでアプリにアクセスして使用する](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリケーションと Azure Active Directory との統合に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


---
title: 'チュートリアル: Azure Active Directory と Five9 Plus Adapter (CTI、Contact Center Agents) の統合 | Microsoft Docs'
description: Azure Active Directory と Five9 Plus Adapter (CTI、Contact Center Agents) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/04/2019
ms.author: jeedes
ms.openlocfilehash: 68de5b11c131fe33252178ebecdeb9c3855fe239
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453435"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>チュートリアル: Azure Active Directory と Five9 Plus Adapter (CTI、Contact Center Agents) の統合

このチュートリアルでは、Five9 Plus Adapter (CTI、Contact Center Agents) と Azure Active Directory (Azure AD) を統合する方法について説明します。
Five9 Plus Adapter (CTI、Contact Center Agents) と Azure AD の統合には、次の利点があります。

* Five9 Plus Adapter (CTI、Contact Center Agents) にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで Five9 Plus Adapter (CTI、Contact Center Agents) に自動的にサインイン (シングル サインオン) できるように、設定が可能です。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Five9 Plus Adapter (CTI、Contact Center Agents) と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Five9 Plus Adapter (CTI、Contact Center Agents) でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Five9 Plus Adapter (CTI、Contact Center Agents) では、**IDP** によって開始される SSO がサポートされます。

## <a name="adding-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>ギャラリーからの Five9 Plus Adapter (CTI、Contact Center Agents) の追加

Azure AD への Five9 Plus Adapter (CTI、Contact Center Agents) の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Five9 Plus Adapter (CTI、Contact Center Agents) を追加する必要があります。

**ギャラリーから Five9 Plus Adapter (CTI、Contact Center Agents) を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「 **Five9 Plus Adapter (CTI, Contact Center Agents)**」と入力し、結果パネルから **Five9 Plus Adapter (CTI, Contact Center Agents)** を選択してから **[追加]** ボタンをクリックしてアプリケーションを追加します。

     ![結果の一覧の Five9 Plus Adapter (CTI、Contact Center Agents)](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Five9 Plus Adapter (CTI、Contact Center Agents) で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Five9 Plus Adapter (CTI、Contact Center Agents) の関連ユーザーの間で、リンク関係が確立されている必要があります。

Five9 Plus Adapter (CTI、Contact Center Agents) で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Five9 Plus Adapter (CTI、Contact Center Agents) シングル サインオンの構成](#configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Five9 Plus Adapter (CTI、Contact Center Agents) テスト ユーザーの作成](#create-five9-plus-adapter-cti-contact-center-agents-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Five9 Plus Adapter (CTI、Contact Center Agents) で作成します。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Five9 Plus Adapter (CTI、Contact Center Agents) で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/)の **Five9 Plus Adapter (CTI、Contact Center Agents)** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、次の手順を実行します。

    ![[Five9 Plus Adapter (CTI、Contact Center Agents) のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、次の形式で URL を入力します。
    
    |    環境      |       URL      |
    | :-- | :-- |
    | "Five9 Plus Adapter for Microsoft Dynamics CRM" の場合 | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | "Five9 Plus Adapter for Zendesk" の場合 | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | "Five9 Plus Adapter for Agent Desktop Toolkit" の場合 | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。

    |      環境     |      URL      |
    | :--                  | :--           |
    | "Five9 Plus Adapter for Microsoft Dynamics CRM" の場合 | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | "Five9 Plus Adapter for Zendesk" の場合 | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | "Five9 Plus Adapter for Agent Desktop Toolkit" の場合 | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[Five9 Plus Adapter (CTI、Contact Center Agents) の設定]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on"></a>Five9 Plus Adapter (CTI、Contact Center Agents) のシングル サインオンの構成

1. **Five9 Plus Adapter (CTI、Contact Center Agents)** 側にシングル サインオンを構成するには、ダウンロードされた **証明書 (Base64)** と適切な URL を [Five9 Plus Adapter (CTI、Contact Center Agents) サポート チーム](https://www.five9.com/about/contact)に送信する必要があります。 また、SSO をさらに構成するために、アダプターに従って以下の手順のようにしてください。

    a. "Five9 Plus Adapter for Agent Desktop Toolkit" 管理ガイド: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. "Five9 Plus Adapter for Microsoft Dynamics CRM" 管理ガイド: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. "Five9 Plus Adapter for Zendesk" 管理ガイド: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Five9 Plus Adapter (CTI、Contact Center Agents) へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]**、**[Five9 Plus Adapter (CTI, Contact Center Agents)]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Five9 Plus Adapter (CTI, Contact Center Agents)]** を選択します。

    ![アプリケーションの一覧の Five9 Plus Adapter (CTI、Contact Center Agents)](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Five9 Plus Adapter (CTI、Contact Center Agents) テスト ユーザーの作成

このセクションでは、Five9 Plus Adapter (CTI、Contact Center Agents) で Britta Simon というユーザーを作成します。 [Five9 Plus Adapter (CTI、Contact Center Agents) サポート チーム](https://www.five9.com/about/contact)と協力し、Five9 Plus Adapter (CTI、Contact Center Agents) プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Five9 Plus Adapter (CTI、Contact Center Agents) のタイルをクリックすると、SSO を設定した Five9 Plus Adapter (CTI、Contact Center Agents) に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)
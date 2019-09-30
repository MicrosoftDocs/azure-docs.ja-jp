---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Sugar CRM の統合 | Microsoft Docs
description: Azure Active Directory と Sugar CRM の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 150c4b458724562fc35ef97e190c898a289c6122
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71102914"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sugar-crm"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Sugar CRM の統合

このチュートリアルでは、Sugar CRM と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Sugar CRM を統合すると、次のことができます。

* Sugar CRM にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Sugar CRM に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Sugar CRM でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Sugar CRM では、**SP** によって開始される SSO がサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-sugar-crm-from-the-gallery"></a>ギャラリーからの Sugar CRM の追加

Azure AD への Sugar CRM の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Sugar CRM を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Sugar CRM**」と入力します。
1. 結果ウィンドウで **[Sugar CRM]** を選択し、アプリケーションを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-sugar-crm"></a>Sugar CRM の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Sugar CRM に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Sugar CRM の関連ユーザーの間で、リンク関係が確立されている必要があります。

Sugar CRM に対する Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Sugar CRM の SSO の構成](#configure-sugar-crm-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Sugar CRM のテスト ユーザーの作成](#create-sugar-crm-test-user)** - Sugar CRM で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Sugar CRM** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。

    | |
    |--|
    | `https://<companyname>.sugarondemand.com`|
    | `https://<companyname>.trial.sugarcrm`|

    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。

    | |
    |--|
    | `https://<companyname>.sugarondemand.com/<companyname>`|
    | `https://<companyname>.trial.sugarcrm.com/<companyname>`|
    | `https://<companyname>.trial.sugarcrm.eu/<companyname>`|

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と応答 URL でこれらの値を更新してください。 これらの値を取得するには、[Sugar CRM クライアント サポート チーム](https://support.sugarcrm.com/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Set up Sugar CRM]\(Sugar CRM のセットアップ\)** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Sugar CRM へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Sugar CRM]** を選びます。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-sugar-crm-sso"></a>Sugar CRM の SSO の構成

1. 別の Web ブラウザー ウィンドウで、Sugar CRM 企業サイトに管理者としてサインインします。

1. **[Admin]** に移動します。

    ![管理](./media/sugarcrm-tutorial/ic795888.png "Admin")

1. **[Administration]** セクションで、 **[Password Management]** をクリックします。

    ![Administration](./media/sugarcrm-tutorial/ic795889.png "Administration")

1. **[Enable SAML Authentication]** を選択します。

    ![Administration](./media/sugarcrm-tutorial/ic795890.png "Administration")

1. **[SAML Authentication]** セクションで、次の手順に従います。

    ![SAML Authentication](./media/sugarcrm-tutorial/ic795891.png "SAML Authentication")  

    a. **[ログイン URL]** ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。
  
    b. **[SLO URL]\(SLO の URL\)** ボックスに、Azure portal からコピーした**ログアウト URL** の値を貼り付けます。
  
    c. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、証明書全体を **[X.509 Certificate]** テキスト ボックスに貼り付けます。
  
    d. **[Save]** をクリックします。

### <a name="create-sugar-crm-test-user"></a>Sugar CRM テスト ユーザーの作成

Azure AD ユーザーが Sugar CRM にサインインできるようにするには、そのユーザーを Sugar CRM にプロビジョニングする必要があります。 Sugar CRM の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Sugar CRM** 企業サイトに管理者としてサインインします。

1. **[Admin]** に移動します。

    ![管理](./media/sugarcrm-tutorial/ic795888.png "Admin")

1. **[Administration]** セクションで、 **[User Management]** をクリックします。

    ![Administration](./media/sugarcrm-tutorial/ic795893.png "Administration")

1. **[Users]\>[Create New User]** の順に選択します。

    ![Create New User](./media/sugarcrm-tutorial/ic795894.png "Create New User")

1. **[User Profile]** タブで、次の手順に従います。

    ![New User](./media/sugarcrm-tutorial/ic795895.png "New User")

    * 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの**ユーザー名**、**姓**、および**メール アドレス**を入力します。
  
1. **[Status]** として、 **[Active]** を選択します。

1. [Password] タブで、次の手順に従います。

    ![New User](./media/sugarcrm-tutorial/ic795896.png "New User")

    a. 該当するテキスト ボックスにパスワードを入力します。

    b. **[Save]** をクリックします。

> [!NOTE]
> Sugar CRM から提供されている他の Sugar CRM ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Sugar CRM] タイルをクリックすると、SSO を設定した Sugar CRM に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で Sugar CRM を試す](https://aad.portal.azure.com/)


---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と SumoLogic の統合 | Microsoft Docs
description: Azure Active Directory と SumoLogic の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: 2dcc52688cabebaa6eb813e3240150ea8774e716
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521897"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sumologic"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と SumoLogic の統合

このチュートリアルでは、SumoLogic と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と SumoLogic を統合すると、次のことができます。

* SumoLogic にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して SumoLogic に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* SumoLogic でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* SumoLogic では、**IDP** initiated SSO がサポートされます

## <a name="adding-sumologic-from-the-gallery"></a>ギャラリーからの SumoLogic の追加

Azure AD への SumoLogic の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SumoLogic を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**SumoLogic**」と入力します。
1. 結果のパネルから **SumoLogic** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-sumologic"></a>SumoLogic の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、SumoLogic に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと SumoLogic の関連ユーザーとの間にリンク関係を確立する必要があります。

SumoLogic に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[SumoLogic の SSO の構成](#configure-sumologic-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[SumoLogic のテスト ユーザーの作成](#create-sumologic-test-user)** - SumoLogic で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **SumoLogic** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[SAML でシングル サインオンをセットアップします]** ページで、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、次の形式で URL を入力します。

    - `https://service.sumologic.com`
    - `https://<tenantname>.us2.sumologic.com`
    - `https://<tenantname>.us4.sumologic.com`
    - `https://<tenantname>.eu.sumologic.com`
    - `https://<tenantname>.jp.sumologic.com`
    - `https://<tenantname>.de.sumologic.com`
    - `https://<tenantname>.ca.sumologic.com`

    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。

    - `https://service.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.us2.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.us4.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.eu.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.jp.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.de.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.ca.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.au.sumologic.com/sumo/saml/consume/<tenantname>`

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[SumoLogic クライアント サポート チーム](https://www.sumologic.com/contact-us/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. SumoLogic アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、SumoLogic アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    |  名前 | ソース属性 |
    | ---------------| --------------- |
    | FirstName | User.givenname |
    | LastName | User.surname |
    | ロール | user.assignedroles |

    > [!NOTE]
    > Azure AD で **役割** を構成する方法については、[ここ](../develop/active-directory-enterprise-app-role-management.md)をクリックしてください。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[SumoLogic のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に SumoLogic へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[SumoLogic]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-sumologic-sso"></a>SumoLogic の SSO の構成

1. 別の Web ブラウザーのウィンドウで、SumoLogic 企業サイトに管理者としてサインインします。

1. **[Manage]\>[Security]** の順に選択します。

    ![管理](./media/sumologic-tutorial/ic778556.png "管理する")

1. **[SAML]** をクリックします。

    ![グローバル セキュリティ設定](./media/sumologic-tutorial/ic778557.png "グローバル セキュリティ設定")

1. **[Select a configuration or create a new one]** ボックスの一覧から **[Azure AD]** を選択し、 **[Configure]** をクリックします。

    ![[Configure SAML 2.0]\(SAML 2.0 の構成\) を示すスクリーンショット。ここで [Azure AD] を選択できます。](./media/sumologic-tutorial/ic778558.png "[Configure SAML 2.0]")

1. **[Configure SAML 2.0]** ダイアログで、次の手順に従います。

    ![[Configure SAML 2.0]\(SAML 2.0 の構成\) ダイアログ ボックスを示すスクリーンショット。ここで、説明されている値を入力できます。](./media/sumologic-tutorial/ic778559.png "[Configure SAML 2.0]")

    a. **[Configuration Name]** テキスト ボックスに、「**Azure AD**」と入力します。

    b. **[Debug Mode]** を選択します。

    c. **[Issuer]\(発行者\)** ボックスに、Azure portal からコピーした **Azure AD 識別子** の値を貼り付けます。

    d. **[Authn Request URL]\(認証要求 URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    e. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、証明書全体を **[X.509 Certificate]** テキスト ボックスに貼り付けます。

    f. **[Email Attribute]** として、 **[Use SAML subject]** を選択します。  

    g. **[SP initiated Login Configuration]** を選択します。

    h. **[Login Path]** テキスト ボックスに、「**Azure**」と入力し、 **[保存]** .をクリックします。

### <a name="create-sumologic-test-user"></a>SumoLogic のテスト ユーザーの作成

Azure AD ユーザーが SumoLogic にサインインできるようにするには、そのユーザーを SumoLogic にプロビジョニングする必要があります。 SumoLogic の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **SumoLogic** テナントにサインインします。

1. **[管理]\>[ユーザー]** の順に移動します。

    ![[管理] メニューから [ユーザー] が選択された状態を示すスクリーンショット。](./media/sumologic-tutorial/ic778561.png "ユーザー")

1. **[追加]** をクリックします。

    ![ユーザーの [追加] ボタンを示すスクリーンショット。](./media/sumologic-tutorial/ic778562.png "ユーザー")

1. **[New User]** ダイアログ ページで、次の手順に従います。

    ![[New User]\(新しいユーザー\)](./media/sumologic-tutorial/ic778563.png "[新しいユーザー]")

    a. プロビジョニングする Azure AD アカウントに関連する情報を、 **[First Name]\(名\)** 、 **[Last Name]\(姓\)** 、および **[Email]\(電子メール\)** ボックスに入力します。
  
    b. ロールを選択します。
  
    c. **[Status]** として、 **[Active]** を選択します。
  
    d. **[保存]** をクリックします。

> [!NOTE]
> 他の SumoLogic ユーザー アカウント作成ツールや、SumoLogic から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [SumoLogic] タイルをクリックすると、SSO を設定した SumoLogic に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で SumoLogic を試す](https://aad.portal.azure.com/)
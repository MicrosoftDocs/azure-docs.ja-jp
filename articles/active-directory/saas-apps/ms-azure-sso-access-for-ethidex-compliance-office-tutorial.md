---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と MS Azure SSO Access for Ethidex Compliance Office™ の統合 | Microsoft Docs
description: Azure Active Directory と MS Azure SSO Access for Ethidex Compliance Office™ の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: jeedes
ms.openlocfilehash: 7ef219ca147fe96fc65f14bbf3ba6a565adc95ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92520916"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ms-azure-sso-access-for-ethidex-compliance-office"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と MS Azure SSO Access for Ethidex Compliance Office™ の統合

このチュートリアルでは、MS Azure SSO Access for Ethidex Compliance Office™ を Azure Active Directory (Azure AD) と統合する方法について学習します。 MS Azure SSO Access for Ethidex Compliance Office™ と Azure AD を統合すると、次のことができます。

* MS Azure SSO Access for Ethidex Compliance Office™ にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して MS Azure SSO Access for Ethidex Compliance Office™ に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* MS Azure SSO Access for Ethidex Compliance Office™ でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* MS Azure SSO Access for Ethidex Compliance Office™ では、**IDP** Initiated SSO がサポートされます

## <a name="adding-ms-azure-sso-access-for-ethidex-compliance-office-from-the-gallery"></a>ギャラリーからの MS Azure SSO Access for Ethidex Compliance Office™ の追加

Azure AD への MS Azure SSO Access for Ethidex Compliance Office™ の統合を構成するには、マネージド SaaS アプリの一覧にギャラリーから MS Azure SSO Access for Ethidex Compliance Office™ を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**MS Azure SSO Access for Ethidex Compliance Office™**」と入力します。
1. 結果のパネルから **MS Azure SSO Access for Ethidex Compliance Office™** を選択し、そのアプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-ms-azure-sso-access-for-ethidex-compliance-office"></a>MS Azure SSO Access for Ethidex Compliance Office™ の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、MS Azure SSO Access for Ethidex Compliance Office™ に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと MS Azure SSO Access for Ethidex Compliance Office™ の関連ユーザーとの間にリンク関係を確立する必要があります。

MS Azure SSO Access for Ethidex Compliance Office™ に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[MS Azure SSO Access for Ethidex Compliance Office の SSO の構成](#configure-ms-azure-sso-access-for-ethidex-compliance-office-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[MS Azure SSO Access for Ethidex Compliance Office のテスト ユーザーの作成](#create-ms-azure-sso-access-for-ethidex-compliance-office-test-user)** - MS Azure SSO Access for Ethidex Compliance Office™ で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **MS Azure SSO Access for Ethidex Compliance Office™** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`com.ethidex.prod.<CLIENTID>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://www.ethidex.com/saml2/sp/acs/<CLIENTID>` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 この値を取得するには、[MS Azure SSO Access for Ethidex Compliance Office™ サポート チーム](mailto:support@ethidex.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. MS Azure SSO Access for Ethidex Compliance Office™ アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示しています。ここで、**nameidentifier** は **user.userprincipalname** にマップされています。 MS Azure SSO Access for Ethidex Compliance Office™ アプリケーションでは、**nameidentifier** が **user.mail** にマップされると想定されているため、 **[編集]** アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![image](common/edit-attribute.png)

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (未加工)]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificateraw.png)

1. **[MS Azure SSO Access for Ethidex Compliance Office™ の設定]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、MS Azure SSO Access for Ethidex Compliance Office™ へのアクセスを B.Simon に許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[MS Azure SSO Access for Ethidex Compliance Office™]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-ms-azure-sso-access-for-ethidex-compliance-office-sso"></a>MS Azure SSO Access for Ethidex Compliance Office の SSO の構成

**MS Azure SSO Access for Ethidex Compliance Office™** 側でシングル サインオンを構成するには、ダウンロードした **証明書 (未加工)** と Azure portal からコピーした適切な URL を [MS Azure SSO Access for Ethidex Compliance Office™ サポート チーム](mailto:support@ethidex.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-ms-azure-sso-access-for-ethidex-compliance-office-test-user"></a>MS Azure SSO Access for Ethidex Compliance Office のテスト ユーザーの作成

このセクションでは、MS Azure SSO Access for Ethidex Compliance Office™ で B.Simon というユーザーを作成します。 [MS Azure SSO Access for Ethidex Compliance Office™ サポート チーム](mailto:support@ethidex.com)と連携して、MS Azure SSO Access for Ethidex Compliance Office™ プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [MS Azure SSO Access for Ethidex Compliance Office™] タイルをクリックすると、SSO を設定した MS Azure SSO Access for Ethidex Compliance Office™ アプリケーションに自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で MS Azure SSO Access for Ethidex Compliance Office™ を試す](https://aad.portal.azure.com/)
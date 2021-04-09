---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Claromentis の統合 | Microsoft Docs
description: Azure Active Directory と Claromentis の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: jeedes
ms.openlocfilehash: 92b068ee9b8aaf4c462002354bbb6490f4888a80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92455903"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-claromentis"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Claromentis の統合

このチュートリアルでは、Claromentis と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Claromentis を統合すると、次のことができます。

* Claromentis にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Claromentis に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Claromentis でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Claromentis では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます
* Claromentis では、**Just In Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-claromentis-from-the-gallery"></a>ギャラリーからの Claromentis の追加

Azure AD への Claromentis の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Claromentis を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Claromentis**」と入力します。
1. 結果のパネルから **[Claromentis]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-claromentis"></a>Claromentis の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Claromentis に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Claromentis の関連ユーザーの間で、リンク関係を確立する必要があります。

Claromentis で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Claromentis SSO の構成](#configure-claromentis-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Claromentis テスト ユーザーの作成](#create-claromentis-test-user)** - Claromentis で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Claromentis** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、組織の要件に従って識別子の値を入力します。

    b. **[応答 URL]** ボックスに、`https://<customer_site_url>/custom/loginhandler/simplesaml/www/module.php/saml/sp/saml2-acs.php/claromentis` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。

    ```https
    https://<customer_site_url>/login
    https://<customer_site_url>/login?no_auto=0
    ```

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値は、実際の識別子、応答 URL、およびサインオン URL に更新します。実際の値については、このチュートリアルの後の方で説明します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Claromentis のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Claromentis へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Claromentis]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-claromentis-sso"></a>Claromentis SSO の構成

1. 別のブラウザー ウィンドウで、Claromentis Web サイトに管理者としてサインインします。

1. **アプリケーション アイコン** をクリックし、 **[Admin]\(管理者\)** を選択します。

    ![スクリーンショットは、[Admin]\(管理者\) が選択されている Claromentis Web サイトを示します。](./media/claromentis-tutorial/config1.png)

1. **[Custom Login Handler]\(カスタム ログイン ハンドラー\)** タブを選択します。

    ![スクリーンショットは、[Custom Login Handler]\(カスタム ログイン ハンドラー\) が選択されている [管理者] ページを示します。](./media/claromentis-tutorial/config2.png)

1. **[SAML Config]\(SAML 構成\)** を選択します。

    ![スクリーンショットは、SAML の構成ページを示します。](./media/claromentis-tutorial/config3.png)

1. **[SAML Config]\(SAML 構成\)** タブで **[Config]\(構成\)** セクションまで下へスクロールし、以下の手順を実行します。

    ![スクリーンショットは、この手順で説明されている情報を入力できるページの [Config]\(構成\) セクションを示します。](./media/claromentis-tutorial/config4.png)

    a. **[Technical Contact Name]\(技術部連絡先名\)** ボックスに、技術部連絡先担当者の名前を入力します。

    b. **[Technical Contact Email]\(技術部連絡先メール\)** ボックスに、技術部連絡先担当者のメール アドレスを入力します。

    c. **[Auth Admin Password]\(認証管理者パスワード\)** ボックスにパスワードを指定します。

1. **[Auth Sources]\(認証ソース\)** まで下へスクロールし、以下の手順を実行します。

    ![スクリーンショットは、この手順で説明されている情報を入力できる [Auth Sources]\(認証ソース\) セクションを示します。](./media/claromentis-tutorial/config5.png)

    a. **[IDP]** ボックスに、Azure portal からコピーした **Azure AD ID** の値を入力します。

    b. **[Entity ID]\(エンティティ ID\)** ボックスに、エンティティ ID 値を入力します。

    c. Azure portal からダウンロードした **フェデレーション メタデータ XML** ファイルをアップロードします。

    d. **[保存]** をクリックします。

1. これで、すべての URL が **[SAML Config]\(SAML 構成\)** セクションの **[Identity Provider]\(ID プロバイダー\)** セクション内に設定されたことになります。

    ![スクリーンショットは、URL が設定されている [Identity Provider]\(ID プロバイダー\) ページを示します。](./media/claromentis-tutorial/config6.png)

    a. **[Identifier (Entity ID)]\(識別子 (エンティティ ID)\)** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションにある **[識別子]** ボックスに貼り付けます。

    b. **[Reply URL]\(応答 URL\)** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションにある **[応答 URL]** ボックスに貼り付けます。

    c. **[Sign On URL]\(サインオン URL\)** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションにある **[サインオン URL]** ボックスに貼り付けます。

### <a name="create-claromentis-test-user"></a>Claromentis テスト ユーザーの作成

このセクションでは、B.Simon というユーザーを Claromentis に作成します。 Claromentis では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Claromentis にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Claromentis] タイルをクリックすると、SSO を設定した Claromentis に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Claromentis を試す](https://aad.portal.azure.com/)
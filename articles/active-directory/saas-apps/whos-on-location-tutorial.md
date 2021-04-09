---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と WhosOnLocation の統合 | Microsoft Docs
description: Azure Active Directory と WhosOnLocation の間のシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/30/2020
ms.author: jeedes
ms.openlocfilehash: d0e5134da9083e97b3977b05d601c2cfba25f5d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92636682"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-whosonlocation"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と WhosOnLocation の統合

このチュートリアルでは、WhosOnLocation と Azure Active Directory (Azure AD) を統合する方法について説明します。 WhosOnLocation を Azure AD と統合すると、次のことができます。

* WhosOnLocation にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して WhosOnLocation に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* WhosOnLocation でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* WhosOnLocation では、**SP** initiated SSO がサポートされます

* WhosOnLocation を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-whosonlocation-from-the-gallery"></a>ギャラリーからの WhosOnLocation の追加

Azure AD への WhosOnLocation の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に WhosOnLocation を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**WhosOnLocation**」と入力します。
1. 結果パネルで **[WhosOnLocation]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-whosonlocation"></a>WhosOnLocation の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、WhosOnLocation に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと WhosOnLocation の関連ユーザーとの間にリンク関係を確立する必要があります。

WhosOnLocation で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[WhosOnLocation SSO の構成](#configure-whosonlocation-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[WhosOnLocation テスト ユーザーの作成](#create-whosonlocation-test-user)** - WhosOnLocation で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **WhosOnLocation** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://login.whosonlocation.com/saml/login/<CUSTOM_ID>`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://login.whosonlocation.com/saml/metadata/<CUSTOM_ID>`

    c. **[応答 URL]** ボックスに、`https://login.whosonlocation.com/saml/acs/<CUSTOM_ID>` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際のサインオン URL、応答 URL、識別子で更新してください。 これらの値を取得するには、[WhosOnLocation クライアント サポート チーム](mailto:support@whosonlocation.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[WhosOnLocation のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に WhosOnLocation へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[WhosOnLocation]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-whosonlocation-sso"></a>WhosOnLocation SSO の構成

1. 別のブラウザー ウィンドウで、WhosOnLocation 企業サイトに管理者としてサインオンします。

2. **[Tools]\(ツール\)**  ->  **[Account]\(アカウント\)** の順にクリックします。

    ![WhosOnLocation サイトの [Tools]\(ツール\) メニューから [Account]\(アカウント\) が選択されていることを示すスクリーンショット。](./media/WhosOnLocation-tutorial/config1.png)

3. 左側のナビゲーターで、 **[Employee Access]\(従業員のアクセス\)** を選択します。

    ![[Account Profile]\(アカウントのプロファイル\) から [Employee Access]\(従業員のアクセス\) が選択されていることを示すスクリーンショット。](./media/WhosOnLocation-tutorial/config2.png)

4. 次のページで、以下の手順を実行します。

    ![[Employess Access]\(従業員のアクセス\) タブを示すスクリーンショット。ここで、ユーザー データを入力できます。](./media/WhosOnLocation-tutorial/config3.png)

    a. **[Single sign-on with SAML]\(SAML によるシングル サインオン\)** で **[Yes]\(はい\)** に変更します。

    b. **[Issuer URL]\(発行者の URL\)** ボックスに、Azure portal からコピーした **エンティティ ID** の値を貼り付けます。

    c. **[SSO Endpoint]\(SSO エンドポイント\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    d. Azure portal からダウンロードした **証明書 (Base64)** をメモ帳で開き、その内容を **[Certificate]\(証明書\)** ボックスに貼り付けます。

    e. **[Save SAML Configuration]\(SAML 構成の保存\)** をクリックします。

### <a name="create-whosonlocation-test-user"></a>WhosOnLocation テスト ユーザーの作成

このセクションでは、WhosOnLocation で B.Simon というユーザーを作成します。 [WhosOnLocation サポート チーム](mailto:support@whosonlocation.com)と連携して、WhosOnLocation プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [WhosOnLocation] タイルをクリックすると、SSO を設定した WhosOnLocation に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で WhosOnLocation を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって WhosOnLocation を保護する方法](/cloud-app-security/proxy-intro-aad)
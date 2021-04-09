---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Azure AD SAML Toolkit の統合 | Microsoft Docs
description: Azure Active Directory と Azure AD SAML Toolkit の間でシングル サインオンを構成する方法について学習します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 26618382223a87f779f95452000a39126f37efbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92675433"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-azure-ad-saml-toolkit"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Azure AD SAML Toolkit の統合

このチュートリアルでは、Azure AD SAML Toolkit と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD SAML Toolkit と Azure AD を統合すると、次のことが可能になります。

* Azure AD SAML Toolkit にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Azure AD SAML Toolkit に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Azure AD SAML Toolkit でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Azure AD SAML Toolkit では、**SP** Initiated SSO がサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>ギャラリーからの Azure AD SAML Toolkit の追加

Azure AD への Azure AD SAML Toolkit の統合を構成するには、マネージド SaaS アプリの一覧にギャラリーから Azure AD SAML Toolkit を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Azure AD SAML Toolkit**」と入力します。
1. 結果のパネルから **[Azure AD SAML Toolkit]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-azure-ad-saml-toolkit"></a>Azure AD SAML Toolkit の Azure AD SSO の構成とテスト

**B. Simon** というテスト ユーザーを使用して、Azure AD SAML Toolkit に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Azure AD SAML Toolkit の関連ユーザーとの間にリンク関係を確立する必要があります。

Azure AD SAML Toolkit に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Azure AD SAML Toolkit の SSO の構成](#configure-azure-ad-saml-toolkit-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Azure AD SAML Toolkit のテスト ユーザーの作成](#create-azure-ad-saml-toolkit-test-user)** - Azure AD SAML Toolkit で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Azure AD SAML Toolkit** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ページで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、URL として「`https://samltoolkit.azurewebsites.net/`」と入力します。

    b. **[応答 URL]** ボックスに、URL として「`https://samltoolkit.azurewebsites.net/SAML/Consume`」と入力します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (未加工)]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificateraw.png)

1. **[Set up Azure AD SAML Toolkit]\(Azure AD SAML Toolkit の設定\)** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、Azure AD SAML Toolkit へのアクセスを許可することで、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Azure AD SAML Toolkit]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-azure-ad-saml-toolkit-sso"></a>Azure AD SAML Toolkit SSO の構成

1. 新しい Web ブラウザー ウィンドウを開き、Azure AD SAML Toolkit Web サイトへの登録が済んでいない場合は、まず **[登録]** をクリックして登録します。 既に登録が済んでいる場合は、登録済みのサインイン資格情報を使用して Azure AD SAML Toolkit 企業サイトにサインインします。

    ![Azure AD SAML Toolkit の登録](./media/saml-toolkit-tutorial/register.png)

1. **[SAML Configuration]\(SAML 構成\)** をクリックします。

    ![Azure AD SAML Toolkit の SAML 構成](./media/saml-toolkit-tutorial/saml-configure.png)

1. **Create** をクリックしてください。

    ![Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/createsso.png)

1. **[SAML SSO Configuration]\(SAML SSO 構成\)** ページで、次の手順を実行します。

    ![Azure AD SAML Toolkit の SSO 構成の作成](./media/saml-toolkit-tutorial/fill-details.png)

    1. **[ログイン URL]** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    1. **[Azure AD 識別子]** ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    1. **[Logout URL]\(ログアウト URL\)** テキストボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    1. **[ファイルの選択]** をクリックして、Azure portal からダウンロードした **証明書 (未加工)** ファイルをアップロードします。

    1. **Create** をクリックしてください。

    1. SAML Toolkit SSO の構成ページでサインオン URL、識別子、および ACS URL の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションで、対応するテキスト ボックスに貼り付けます。

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Azure AD SAML Toolkit のテスト ユーザーの作成

このセクションでは、B.Simon というユーザーを Azure AD SAML Toolkit に作成します。 新しいユーザーを登録してツールでテスト ユーザーを作成し、すべてのユーザーの詳細を入力してください。 

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

1. Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる SAML Toolkit のサインオン URL にリダイレクトされます。 

2. SAML Toolkit のサインオン URL に直接移動し、そこからログイン フローを開始します。

3. Microsoft アクセス パネルを使用することができます。 アクセス パネル上で [SAML Toolkit] タイルをクリックすると、SSO を設定した SAML Toolkit に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

Azure AD SAML Toolkit を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
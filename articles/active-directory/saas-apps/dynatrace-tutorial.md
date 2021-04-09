---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Dynatrace の統合 | Microsoft Docs
description: Azure Active Directory と Dynatrace の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.openlocfilehash: 6453983ad4547139ef43af019a0dbf5ff77452c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92454490"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-dynatrace"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Dynatrace の統合

このチュートリアルでは、Dynatrace と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Dynatrace を統合すると、次のことができます。

* Dynatrace にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Dynatrace に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Dynatrace でのシングル サインオン (SSO) が有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Dynatrace では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます
* Dynatrace では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値です。 1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-dynatrace-from-the-gallery"></a>ギャラリーからの Dynatrace の追加

Azure AD への Dynatrace の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Dynatrace を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Dynatrace**」と入力します。
1. 結果のパネルから **[Dynatrace]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-dynatrace"></a>Dynatrace の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Dynatrace に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Dynatrace の関連ユーザーとの間にリンク関係を確立する必要があります。

Dynatrace に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Dynatrace SSO の構成](#configure-dynatrace-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Dynatrace のテスト ユーザーの作成](#create-dynatrace-test-user)** - Dynatrace で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Dynatrace** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションでは、アプリケーションは **IDP** 開始モードで事前に構成されており、必要な URL は既に Azure で事前に設定されています。 構成を保存するには、 **[保存]** ボタンをクリックします。

1. アプリケーションを **SP** 開始モードで構成するには、 **[追加の URL を設定します]** をクリックし、次の手順を実行します。

    **[サインオン URL]** テキスト ボックスに URL として「`https://sso.dynatrace.com/`」と入力します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を見つけます。 **[ダウンロード]** を選択して証明書をダウンロードし、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[SAML 署名証明書]** セクションで **[編集]** ボタンを選択して、 **[SAML 署名証明書]** ダイアログ ボックスを開きます。 次の手順を実行します。

    ![SAML 署名証明書の編集](common/edit-certificate.png)

    a. **[署名オプション]** の設定はあらかじめ入力されています。 組織ごとの設定を確認してください。

    b. **[保存]** をクリックします。

    ![Communifire の署名オプション](./media/dynatrace-tutorial/tutorial-dynatrace-signing-option.png)

1. **[Dynatrace のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Dynatrace へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Dynatrace]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログ ボックスの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーションにロール値が必要な場合は、 **[ロールの選択]** ダイアログ ボックスでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログ ボックスで、 **[割り当て]** ボタンをクリックします。

## <a name="configure-dynatrace-sso"></a>Dynatrace の SSO の構成

**Dynatrace** 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** ファイルと Azure portal からコピーした適切な URL を [Dynatrace](https://www.dynatrace.com/support/help/shortlink/users-sso-hub) に送信する必要があります。 Dynatrace Web サイトの指示に従って、両方の側で SAML SSO 接続を構成できます。

### <a name="create-dynatrace-test-user"></a>Dynatrace のテスト ユーザーの作成

このセクションでは、B. Simon というユーザーを Dynatrace に作成します。 Dynatrace では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Dynatrace にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Dynatrace] タイルをクリックすると、SSO を設定した Dynatrace に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Dynatrace を試す](https://aad.portal.azure.com/)
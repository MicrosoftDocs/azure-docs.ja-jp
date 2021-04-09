---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Software AG Cloud の統合 | Microsoft Docs
description: Azure Active Directory と Software AG Cloud の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/09/2020
ms.author: jeedes
ms.openlocfilehash: c2a2446da0e1ccf1b3dab88fe898b179291ddaba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98726141"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-software-ag-cloud"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Software AG Cloud の統合

このチュートリアルでは、Software AG Cloud と Azure Active Directory (Azure AD) を統合する方法について説明します。 Software AG Cloud と Azure AD を統合すると、次のことが可能になります。

* Software AG Cloud にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Software AG Cloud に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Software AG Cloud でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Software AG Cloud では、**SP** Initiated SSO がサポートされます
* Software AG Cloud では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-software-ag-cloud-from-the-gallery"></a>ギャラリーからの Software AG Cloud の追加

Azure AD への Software AG Cloud の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Software AG Cloud を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Software AG Cloud**」と入力します。
1. 結果パネルで **[Software AG Cloud]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-software-ag-cloud"></a>Software AG Cloud の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Software AG Cloud に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと Software AG Cloud の関連ユーザーとの間にリンク関係を確立する必要があります。

Software AG Cloud に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Software AG Cloud の SSO の構成](#configure-software-ag-cloud-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Software AG Cloud のテスト ユーザーの作成](#create-software-ag-cloud-test-user)** - Software AG Cloud で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Software AG Cloud** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    1. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。

        `https://*.softwareag.cloud/auth/realms/TENANT-NAME/broker/IDENTITY-PROVIDER-NAME/endpoint`

    1. **[識別子 (エンティティ ID)]** テキスト ボックスに、次のパターンで URL を入力します。

        `https://*.softwareag.cloud/auth/realms/TENANT-NAME`

        > [!NOTE]
        > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[Software AG Cloud クライアント サポート チーム](mailto:support@softwareag.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Software AG Cloud のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Software AG Cloud へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Software AG Cloud]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-software-ag-cloud-sso"></a>Software AG Cloud の SSO の構成

1. 別の Web ブラウザー ウィンドウで、Software AG Cloud Web サイトに管理者としてサインインします。

1.  **[Administration]\(管理\)** をクリックします。

    ![Software AG Cloud の管理の構成](./media/software-ag-cloud-tutorial/admin.png)

1. **[Single-sign on]\(シングル サインオン\) > [Add identity provider]\(ID プロバイダーの追加\)** に移動します。

    ![Software AG Cloud の ID プロバイダーの構成](./media/software-ag-cloud-tutorial/add-identity-provider.png)

1. 次のページで、以下の手順を実行します。

    ![Software AG Cloud の構成の手順](./media/software-ag-cloud-tutorial/saml-1.png)

    a. **[Identity provider display name]\(ID プロバイダーの表示名\)** ボックスに、名前 (例: `azure ad`) を入力します。

    b. **[Identity provider unique identifier for use in Software AG Cloud redirect URI]\(Software AG Cloud リダイレクト URI で使用する ID プロバイダーの一意識別子\)** ボックスに、ID プロバイダーの一意の名前を入力します。 **[Software AG Cloud redirect URI]\(Software AG Cloud リダイレクト URI\)** フィールドが更新され、URI が設定されます。 この URI をコピーし、それを使用して、定義されているパターンに従って Azure portal で **エンティティ ID** と他の情報を構成します。

    c. **[Identity provider configuration]\(ID プロバイダー構成\)** で **フェデレーション メタデータ XML** ファイルをインポートし、 **[Next]\(次へ\)** をクリックします。

    d. **[Configuration]\(構成\)** ページに移動し、必要に応じてフィールドに入力します。

### <a name="create-software-ag-cloud-test-user"></a>Software AG Cloud のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Software AG Cloud に作成します。 Software AG Cloud では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Software AG Cloud にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

Software AG Cloud で Microsoft Azure がプロバイダーとして構成されていることを前提として、`www.softwareag.cloud` に移動し、[Login]\(ログイン\) ボタンをクリックして、環境名を入力します。 次の画面で、[Log in with <IDP NAME>]\(<IDP 名> でログイン\) リンクをクリックし、資格情報を入力します。 認証されると、ログインされ、Software AG Cloud のホーム ページが表示されます。

## <a name="next-steps"></a>次の手順

Software AG Cloud を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
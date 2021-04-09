---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と TrendMiner の統合 | Microsoft Docs
description: Azure Active Directory と TrendMiner の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/23/2020
ms.author: jeedes
ms.openlocfilehash: 7f7e7126c482038907e5e986d0779827957cb093
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182174"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trendminer"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と TrendMiner の統合

このチュートリアルでは、TrendMiner と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と TrendMiner を統合すると、次のことが可能になります。

* TrendMiner にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して TrendMiner に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* TrendMiner でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* TrendMiner では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます

## <a name="adding-trendminer-from-the-gallery"></a>ギャラリーからの TrendMiner の追加

Azure AD への TrendMiner の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に TrendMiner を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**TrendMiner**」と入力します。
1. 結果パネルから **[TrendMiner]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-trendminer"></a>TrendMiner の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、TrendMiner に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと TrendMiner の関連ユーザーとの間にリンク関係を確立する必要があります。

TrendMiner に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[TrendMiner の SSO の構成](#configure-trendminer-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[TrendMiner のテスト ユーザーの作成](#create-trendminer-test-user)** - TrendMiner で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **TrendMiner** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://<CUSTOMER>.trendminer.cloud/security/saml/metadata` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<CUSTOMER>.trendminer.cloud/security/saml/SSO` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<CUSTOMER>.trendminer.cloud/` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[TrendMiner クライアント サポート チーム](mailto:support@trendminer.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[TrendMiner のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に TrendMiner へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[TrendMiner]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-trendminer-sso"></a>TrendMiner の SSO の構成

1. 新しい Web ブラウザー ウィンドウを開き、TrendMiner 企業サイトに管理者としてサインインします。

1. 左側のメニューで、 **[SECURITY]\(セキュリティ\) > [Identity Provider]\(ID プロバイダー\)** を選択します。

1. **[Identity Provider]\(ID プロバイダー\)** ページで、 **[SAML]** をクリックし、 **[Next step]\(次のステップ\)** をクリックします。

    ![[SAML] の選択](./media/trendminer-tutorial/saml.png)

1. **[SAML Backup]\(SAML のバックアップ\)** ページで、 **[Next step]\(次のステップ\)** をクリックします。

    ![[SAML Backup]\(SAML のバックアップ\) の選択](./media/trendminer-tutorial/saml-backup.png)

1. **[Self-Signed certificates]\(自己署名証明書\)** で、 **[Next step]\(次のステップ\)** をクリックします。

    ![[Self-Signed certificates]\(自己署名証明書\) ページ](./media/trendminer-tutorial/self-signed-certificate.png)

1. 署名キーのアップロードは **スキップ** できます。

    ![署名キーのアップロード](./media/trendminer-tutorial/signing-key.png)

1. **[SAML Configuration]\(SAML 構成\)** 画面の **[Entity base URL]\(エンティティ ベース URL\)** に、ドメイン URL (例: `https://trendminer.domain.com/`) を入力します。

1. **[Identity provider metadata]\(ID プロバイダー メタデータ\)** で、Azure portal からコピーした **Azure メタデータ ファイル** をアップロードし、 **[Next step]\(次のステップ\)** をクリックします。

    ![SAML の構成](./media/trendminer-tutorial/saml-configuration.png)

1. **[SAML User mapping]\(SAML ユーザー マッピング\)** セクションで、ログインに使用するユーザー名を入力し、 **[Finish]\(完了\)** をクリックします。

    ![[SAML User mapping]\(SAML ユーザー マッピング\)](./media/trendminer-tutorial/user-mapping.png)

### <a name="create-trendminer-test-user"></a>TrendMiner のテスト ユーザーの作成

このセクションでは、TrendMiner で Britta Simon というユーザーを作成します。 [TrendMiner サポート チーム](mailto:support@trendminer.com)と協力してして、TrendMiner プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる TrendMiner のサインオン URL にリダイレクトされます。  

* TrendMiner のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した TrendMiner に自動的にサインインされます 

また、Microsoft アクセス パネルを使用して、任意のモードでアプリケーションをテストすることもできます。 アクセス パネルで [TrendMiner] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した TrendMiner に自動的にサインインされます。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。


## <a name="next-steps"></a>次の手順

TrendMiner を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
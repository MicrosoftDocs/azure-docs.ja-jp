---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と Coda の統合 | Microsoft Docs'
description: Azure Active Directory と Coda の間にシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/23/2020
ms.author: jeedes
ms.openlocfilehash: c78744538991e337b226e49a053807ef8174d507
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92455733"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coda"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と Coda の統合

このチュートリアルでは、Coda と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Coda を統合すると、次のことができます。

* Coda にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Coda に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* GDrive 統合が無効になっている Coda でのシングル サインオン (SSO) が有効なサブスクリプション (Enterprise)。 組織の GDrive 統合が現在有効になっている場合は、[Coda サポート チーム](mailto:support@coda.io)に連絡してこれを無効にしてください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Coda では、**IDP** Initiated SSO がサポートされます

* Coda では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

* Coda を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-coda-from-the-gallery"></a>ギャラリーからの Coda の追加

Azure AD への Coda の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Coda を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Coda**」と入力します。
1. 結果のパネルから **[Coda]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-coda"></a>Coda の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Coda に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Coda の関連ユーザーとの間にリンク関係を確立する必要があります。

Coda で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Coda の SSO の構成の開始](#begin-configuration-of-coda-sso)** - Coda で SSO の構成を開始します。
1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
   * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
   * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Coda の SSO の構成](#configure-coda-sso)** - Coda でシングル サインオン設定の構成を完了します。
   * **[Coda のテスト ユーザーの作成](#create-coda-test-user)** - Coda で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="begin-configuration-of-coda-sso"></a>Coda の SSO の構成の開始

Coda で次の手順に従って開始します。

1. Coda で、 **[Organization settings]\(組織の設定\)** パネルを開きます。

   ![[Organization settings]\(組織の設定\) を開く](media/coda-tutorial/org-settings.png)

1. 組織の GDrive 統合がオフになっていることを確認します。 現在有効になっている場合は、GDrive からの移行について [Coda サポート チーム](mailto:support@coda.io)にお問い合わせください。

   ![GDrive が無効](media/coda-tutorial/gdrive-off.png)

1. **[Authenticate with SSO (SAML)]\(SSO で認証 (SAML)\)** で、 **[Configure SAML]\(SAML の構成\)** オプションを選択します。

   ![SAML 設定](media/coda-tutorial/saml-settings-link.png)

1. 後続の手順で必要になるため、 **[Entity ID]\(エンティティ ID\)** および **[SAML Response URL]\(SAML 応答 URL\)** の値をメモしておきます。

   ![Azure で使用するエンティティ ID と SAML 応答 URL](media/coda-tutorial/azure-settings.png)

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Coda** アプリケーション統合ページで、**[管理]** セクションを探して、**[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[SAML でシングル サインオンをセットアップします]** ページで、次のフィールドの値を入力します。

   a. **[Identifier]\(識別子\)** テキスト ボックスに、上の "エンティティ ID" を入力します。 これは次のパターンに従っている必要があります: `https://coda.io/samlId/<CUSTOMID>`

   b. **[応答 URL]** テキスト ボックスに、上の "SAML 応答 URL" を入力します。 これは次のパターンに従っている必要があります: `https://coda.io/login/sso/saml/<CUSTOMID>/consume`

   > [!NOTE]
   > 実際の値は上記とは異なります。Coda の [Configure SAML]\(SAML の構成\) コンソールで目的の値を確認できます。 実際の識別子と応答 URL でこれらの値を更新します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

   ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Coda のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Coda へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Coda]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

   ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-coda-sso"></a>Coda の SSO の構成

構成を完了するには、Coda の **[Configure Saml]\(SAML の構成\)** パネルに Azure Active Directory の値を入力します。

1. Coda で、 **[Organization settings]\(組織の設定\)** パネルを開きます。
1. **[Authenticate with SSO (SAML)]\(SSO で認証 (SAML)\)** で、 **[Configure SAML]\(SAML の構成\)** オプションを選択します。
1. **[SAML Provider]\(SAML プロバイダー\)** を **[Azure Active Directory]** に設定します。
1. **[Identity Provider Login URL]\(ID プロバイダーのログイン URL\)** に、Azure コンソールから取得した **ログイン URL** を貼り付けます。
1. **[Identity Provider Issuer]\(ID プロバイダーの発行者\)** に、Azure コンソールから取得した **Azure AD 識別子** を貼り付けます。
1. **[Identity Provider Public Certificate]\(ID プロバイダーの公開証明書\)** で、 **[Upload Certificate]\(証明書のアップロード\)** オプションを選択し、以前にダウンロードした証明書ファイルを選択します。
1. **[保存]** を選択します。

これで、SAML SSO 接続の設定に必要な作業が完了しました。

### <a name="create-coda-test-user"></a>Coda のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Coda に作成します。 Coda では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Coda にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Coda] タイルをクリックすると、SSO を設定した Coda に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Coda を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって Coda を保護する方法](/cloud-app-security/proxy-intro-aad)
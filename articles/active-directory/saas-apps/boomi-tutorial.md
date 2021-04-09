---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Boomi の統合 | Microsoft Docs
description: Azure Active Directory と Boomi の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: jeedes
ms.openlocfilehash: c58566c628eedd1dbc3d86ae6a142156cbf31211
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585198"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Boomi の統合

このチュートリアルでは、Boomi と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Boomi を統合すると、次のことができます。

* Boomi にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Boomi に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Boomi でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Boomi では、**IDP** Initiated SSO がサポートされます。

## <a name="add-boomi-from-the-gallery"></a>ギャラリーからの Boomi の追加

Azure AD への Boomi の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Boomi を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Boomi**」と入力します。
1. 結果のパネルから **[Boomi]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-boomi"></a>Boomi の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Boomi に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Boomi の関連ユーザーとの間にリンク関係を確立する必要があります。

Boomi に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Boomi SSO の構成](#configure-boomi-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Boomi のテスト ユーザーの作成](#create-boomi-test-user)** - Boomi で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Boomi** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **サービス プロバイダー メタデータ ファイル** を保持しており、**IDP** Initiated モードに構成したい場合は、 **[基本的な SAML 構成]** セクション上で次の手順を実行します。

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    ![メタデータ ファイルをアップロードする](common/upload-metadata.png)

    b. **フォルダー ロゴ** をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    ![メタデータ ファイルを選択する](common/browse-upload-metadata.png)

    c. メタデータ ファイルが正常にアップロードされると、**識別子** と **応答 URL** の値が、[基本的な SAML 構成] セクションに自動的に設定されます。

    d. **サインオン URL** を入力します (例: `https://platform.boomi.com/AtomSphere.html#build;accountId={your-accountId}`)。

    > [!Note]
    > **サービス プロバイダーのメタデータ ファイル** は、このチュートリアルで後述する「**Boomi SSO の構成**」セクションで取得します。 **識別子** と **応答 URL** の値が自動的に設定されない場合は、要件に応じて手動で値を入力してください。

1. Boomi アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![スクリーンショットは [User Attributes & Claims]\(ユーザー属性とクレーム\) を示しています。[Givenname]\(名\) の user.givenname や [Emailaddress]\(メール アドレス\) の user.mail など、既定値が表示されています。](common/default-attributes.png)

1. その他に、Boomi アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 |  ソース属性|
    | ---------------|  --------- |
    | FEDERATION_ID | User.mail |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Boomi のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Boomi へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Boomi]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-boomi-sso"></a>Boomi SSO の構成

1. 別の Web ブラウザー ウィンドウで、Boomi 企業サイトに管理者としてサインインします。

1. **[会社名]**、**[セットアップ]** の順に進みます。

1. **[SSO オプション]** タブをクリックして以下の手順に従います。

    ![アプリ側でのシングル サインオンの構成](./media/boomi-tutorial/import.png)

    a. **[Enable SAML Single Sign-On]\(SAML シングル サインオンの有効化)** のチェックボックスをオンにします。

    b. **[インポート]** をクリックして、Azure AD からダウンロードした証明書を **ID プロバイダー証明書** にアップロードします。

    c. **[ID プロバイダーのログイン URL]** ボックスに、Azure AD アプリケーションの構成ウィンドウの **[ログイン URL]** の値を入力します。

    d. **[Federation Id Location]** で、**[Federation Id is in FEDERATION_ID Attribute element]** オプションを選択します。

    e. **[AtomSphere MetaData URL]\(AtomSphere メタデータ URL\)** をコピーし、任意のブラウザーで **[MetaData URL]\(メタデータ URL\)** に移動して、出力をファイルに保存します。 Azure portal の **[基本的な SAML 構成]** セクションで **メタデータ URL** をアップロードします。

    f. **[保存]** ボタンをクリックします。

### <a name="create-boomi-test-user&quot;></a>Boomi のテスト ユーザーの作成

Azure AD ユーザーが Boomi にサインインできるようにするには、ユーザーを Boomi にプロビジョニングする必要があります。 Boomi の場合、プロビジョニングは手動で行います。

### <a name=&quot;to-provision-a-user-account-perform-the-following-steps&quot;></a>ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1. Boomi 企業サイトに管理者としてサインインします。

1. **[ユーザー管理]**、**[ユーザー]** の順に進みます。

    ![スクリーンショットは、[ユーザー] が選択されている [ユーザー管理] ページを示しています。](./media/boomi-tutorial/user.png &quot;ユーザー")

1. **+** アイコンをクリックすると **[Add/Maintain User Roles]\(ユーザーの役割の追加と管理)** ダイアログが開きます。

    ![スクリーンショットでは、[+] アイコンが選択されています。](./media/boomi-tutorial/add.png "ユーザー")

    ![スクリーンショットは [Add / Maintain User Roles]\(ユーザー ロールの追加および管理\) を示しています。ここでユーザーを構成します。](./media/boomi-tutorial/roles.png "ユーザー")

    a. **[ユーザーのメール アドレス]** ボックスに、ユーザーのメール アドレスを入力します (この例では B.Simon@contoso.com)。

    b. **[名]** ボックスに、ユーザーの名を入力します (この例では B)。

    c. **[姓]** ボックスに、ユーザーの姓を入力します (この例では Simon)。

    d. ユーザーの **フェデレーション ID** を入力します。 各ユーザーには、アカウント内のユーザーを一意に識別するフェデレーション ID が必要です。

    e. **標準ユーザー** の役割をユーザーに割り当てます。 シングル サインオンのアクセスのほかに、Boomi Atmosphere の通常のアクセスも付与することになるため、管理者の役割は割り当てないでください。

    f. **[OK]** をクリックします。

    > [!NOTE]
    > ユーザー パスワードは ID プロバイダーを通じて管理されるため、AtomSphere アカウントのログインに使用するパスワードを含む、「ようこそ」の通知メールはユーザーに送信されません。 Boomi から提供されている他の Boomi ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Boomi に自動的にサインインされます。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Boomi] タイルをクリックすると、SSO を設定した Boomi に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Boomi を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
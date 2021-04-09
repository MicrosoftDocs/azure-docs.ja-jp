---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Druva の統合 | Microsoft Docs
description: Azure Active Directory と Druva の間にシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: jeedes
ms.openlocfilehash: a7868d702ff3f1190d7f51e4ad7316508d453015
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92454526"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-druva"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Druva の統合

このチュートリアルでは、Druva と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Druva を統合すると、次のことができます。

* Druva にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Druva に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Druva でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Druva では、**IDP** Initiated SSO がサポートされます
* Druva SSO を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-druva-from-the-gallery"></a>ギャラリーからの Druva の追加

Azure AD への Druva の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Druva を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Druva**」と入力します。
1. 結果のパネルから **[Druva]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-druva"></a>Druva の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Druva で Azure AD の SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Druva の関連ユーザーとの間にリンク関係を確立する必要があります。

Druva に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Druva SSO の構成](#configure-druva-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Druva のテスト ユーザーの作成](#create-druva-test-user)** - Druva で B.Simon に対応するユーザーを作成し、Azure AD のそのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Druva** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子 (エンティティ ID)]** ボックスに、「`DCP-login`」という文字列値を入力します。
    
    b. **[応答 URL (Assertion Consumer Service URL)]** ボックスに、URL として「`https://cloud.druva.com/wrsaml/consume`」を入力します。

1. **[保存]** をクリックします。

1. Druva アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、Druva アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性|
    | ------------------- | -------------------- |
    | emailAddress | user.email |
    | druva_auth_token | DCP 管理コンソールから生成された SSO トークン (引用符を除く)。  次に例を示します。X-XXXXX-XXXX-S-A-M-P-L-E+TXOXKXEXNX=. Azure によって、認証トークンの周りに引用符が自動的に追加されます。 |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Druva のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Druva へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Druva]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-druva-sso"></a>Druva SSO の構成

1. 別の Web ブラウザー ウィンドウで、Druva 企業サイトに管理者としてサインインします。

1. 左上にある Druva ロゴをクリックし、 **[Druva Cloud Settings]\(Druva Cloud の設定\)** をクリックします。

    ![設定](./media/druva-tutorial/ic795091.png "設定")

1. **[Single Sign-On]\(シングル サインオン\)** タブの **[Edit]\(編集\)** をクリックします。

    ![[Edit]\(編集\) ボタンが選択されている [Access Settings - Single Sign-On]\(アクセス設定 - シングル サインオン\) タブを示すスクリーンショット。](./media/druva-tutorial/ic795092.png "[シングル サインオンの設定]")

1. **[Edit Single Sign-On Settings]\(シングル サインオン設定の編集\)** ページで、次の手順を実行します。

    ![シングル サインオンの設定](./media/druva-tutorial/ic795095.png "[シングル サインオンの設定]")

    1. **[ID Provider Login URL]\(ID プロバイダーのログイン URL\)**  テキスト ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    1. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、 **[ID プロバイダー証明書]** ボックスに貼り付けます。

       > [!NOTE]
       > 管理者に対してシングル サインオンを有効にするには、 **[Administrators log into Druva Cloud through SSO provider]\(管理者は SSO プロバイダーを介して Druva Cloud にログインする\)** および **[Allow failsafe access to Druva Cloud administrators(recommended)]\(Druva Cloud 管理者へのフェールセーフ アクセスを許可する\)** チェック ボックスをオンにします。 IdP で障害が発生した場合に DCP コンソールにアクセスする必要があるため、Druva では、 **[Failsafe for Administrators]\(管理者のフェールセーフ\)** を有効にすることを推奨しています。 また、管理者は SSO と DCP の両方のパスワードを使用して、DCP コンソールにアクセスすることもできます。

    1. **[保存]** をクリックします。 これにより、SSO を使用して Druva Cloud Platform にアクセスできるようになります。

### <a name="create-druva-test-user"></a>Druva のテスト ユーザーの作成

このセクションでは、B.Simon というユーザーを Druva に作成します。 Druva では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Druva にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Druva] タイルをクリックすると、SSO を設定した Druva に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Druva を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)
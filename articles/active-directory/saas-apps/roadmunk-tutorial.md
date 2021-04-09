---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Roadmunk の統合
description: Azure Active Directory と Roadmunk の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: a7f4682be2f7fbf308aba32768efa932f27b7a87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181708"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-roadmunk"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Roadmunk の統合

このチュートリアルでは、Roadmunk と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Roadmunk を統合すると、次のことができます。

* Roadmunk にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Roadmunk に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Roadmunk サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

Roadmunk では、"*サービス プロバイダー*" (SP) と "*ID プロバイダー*" (IDP) によって開始される SSO がサポートされます。

## <a name="add-roadmunk-from-the-gallery"></a>ギャラリーからの Roadmunk の追加

Roadmunk を Azure AD に統合するには、ギャラリーからマネージド SaaS アプリの一覧に Roadmunk を追加します。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左ウィンドウで、 **[Azure Active Directory]** を選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションの検索ボックスに「**Roadmunk**」と入力します。
1. 結果から **[Roadmunk]** を選択し、そのアプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-roadmunk"></a>Roadmunk の Azure AD SSO の構成とテスト

*B.Simon* という名前のテスト ユーザーを使用して、Roadmunk に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Roadmunk の関連ユーザーとの間にリンク関係を確立する必要があります。

Roadmunk に対して Azure AD SSO を構成してテストする方法の概要は次のとおりです。

1. [Azure AD SSO を構成](#configure-azure-ad-sso)して、その機能をユーザーが使用できるようにします。
    1. [Azure AD テスト ユーザーを作成](#create-an-azure-ad-test-user)して、B.Simon を使用することで Azure AD SSO をテストします。
    1. [Azure AD テスト ユーザーを割り当て](#assign-the-azure-ad-test-user)、B.Simon が Azure AD SSO を使用できるようにします。
1. [Roadmunk SSO を構成](#configure-roadmunk-sso)して、アプリケーション側で SSO 設定を構成します。
    1. [Roadmunk のテスト ユーザーを作成](#create-roadmunk-test-user)します。Roadmunk の B.Simon に対応するユーザーを、Azure AD の B.Simon にリンクすることができます。
1. [SSO をテスト](#test-sso)し、構成が機能することを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Roadmunk** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** のペン アイコンを選択して設定を編集します。

   ![[基本的な SAML 構成] の [編集] アイコンを示すスクリーンショット。](common/edit-urls.png)

1. SP メタデータ ファイルがあり、IDP-initiated モードで構成する場合は、 **[基本的な SAML 構成]** セクションで次の手順に従います。

    a. **[メタデータ ファイルをアップロードする]** を選択します。

    ![メタデータ ファイルをアップロードするためのリンクを示すスクリーンショット。](common/upload-metadata.png)

    b. フォルダー アイコンを選択して、「Roadmunk SSO の構成」手順の手順 4 でダウンロードしたメタデータ ファイルを選択します。 **[アップロード]** を選択します。

    ![メタデータ ファイルの選択方法を示すスクリーンショット。](common/browse-upload-metadata.png)

    メタデータ ファイルがアップロードされると、 **[基本的な SAML 構成]** セクションの **[識別子]** と **[応答 URL]** の値が自動的に設定されます。

    ![[基本的な SAML 構成] セクションを示すスクリーンショット。 [識別子] フィールドと [応答 URL] フィールドが強調表示されています。](common/idp-intiated.png)

    > [!Note]
    > **[識別子]** と **[応答 URL]** の値が自動的に設定されない場合は、手動で値を入力してください。

1. アプリケーションを SP-initiated モードで構成する場合は、 **[追加の URL を設定します]** を選択します。 **[サインオン URL]** フィールドに、「`https://login.roadmunk.com`」と入力します。

    ![SP-initiated モード用のサインオン URL を設定する場所を示すスクリーンショット。](common/metadata-upload-additional-signon.png)

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を見つけます。 次に、 **[ダウンロード]** を選択して証明書をダウンロードし、コンピューターに保存します。

    ![SAML 署名証明書のダウンロード リンクを示すスクリーンショット。](common/metadataxml.png)

1. **[Roadmunk のセットアップ]** セクションで、必要な URL をコピーします。

    ![構成 URL をコピーする場所を示すスクリーンショット。](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal でテスト ユーザーを作成します。 このユーザーに *B.Simon* という名前を付けます。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. ウィンドウの上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、`B.Simon@contoso.com` と入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Roadmunk へのアクセスを許可することで、このユーザーが Azure SSO を使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。
1. アプリケーションの一覧で **[Roadmunk]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択します。 次に、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログ ボックスで、 **[ユーザー]** 一覧から **[B.Simon]** を選択します。 その後、ダイアログ ボックスの一番下にある **[選択]** を選択します。
1. ユーザーにロールが割り当てられるようにする場合は、 **[ロールの選択]** ドロップダウンからそれを選択します。 このアプリに対してロールが設定されていない場合は、 **[既定のアクセス]** ロールが選択されます。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

## <a name="configure-roadmunk-sso"></a>Roadmunk SSO の構成

1. Roadmunk の Web サイトに管理者としてサインインします。

1. ページ下部のユーザー アイコンを選択し、 **[Account Settings]\(アカウント設定\)** を選択します。

    ![ユーザー アカウント設定を選択する場所を示すスクリーンショット。](./media/roadmunk-tutorial/account.png)

1. **[Company]\(会社\)**  >  **[Authentication Settings]\(認証の設定\)** に移動します。

1. **[Authentication Settings]\(認証の設定\)** ページで、次の手順に従います。

    ![[Authentication Settings]\(認証の設定\) ページを示すスクリーンショット。](./media/roadmunk-tutorial/saml-sso.png)

    a. **[SAML Single Sign On (SSO)]\(SAML シングル サインオン (SSO)\)** をオンにします。

    b. **[Step 1]\(手順 1\)** セクションで、メタデータ XML ファイルをアップロードするか、メタデータの URL を指定します。

    c. **[Step 2]\(手順 2\)** セクションで、Roadmunk メタデータ ファイルをダウンロードしてお使いのコンピューターに保存します。

    d. SSO を使用してサインインする場合は、 **[Step 3]\(手順 3\)** セクションで、 **[Enforce SAML Sign-In Only]\(SAML サインインのみを適用する\)** を選択します。

    e. **[保存]** を選択します。


### <a name="create-roadmunk-test-user"></a>Roadmunk のテスト ユーザーの作成

1. Roadmunk の Web サイトに管理者としてサインインします。

1. ページ下部のユーザー アイコンを選択し、 **[Account Settings]\(アカウント設定\)** を選択します。

    ![テスト ユーザーの [Account Settings]\(アカウント設定\) を開く方法を示すスクリーンショット。](./media/roadmunk-tutorial/account.png)

1. **[Users]\(ユーザー\)** タブを開き、 **[Invite User]\(ユーザーの招待\)** を選択します。

    ![[Users]\(ユーザー\) タブを示すスクリーンショット。[Invite User]\(ユーザーの招待\) ボタンが強調表示されています。 開かれているウィンドウでは、[Email]\(メール\) および [Role]\(ロール\) フィールドが強調表示されています。](./media/roadmunk-tutorial/create-user.png)

1. 表示されたフォームに必要な情報を入力し、 **[Invite]\(招待\)** を選択します。


## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD SSO の構成をテストします。

マイ アプリ ポータルで **[Roadmunk]** タイルをクリックすると、SSO を設定した Roadmunk アカウントに自動的にサインインします。 詳細については、「[マイ アプリ ポータルからアプリにサインインして開始する](../user-help/my-apps-portal-end-user-access.md)」を参照してください。

## <a name="next-steps"></a>次の手順

Roadmunk を構成した後、セッション制御を適用できます。 セッション制御により、組織の機密データの流出と侵入をリアルタイムで保護することができます。 セッション制御は、条件付きアクセスを拡張したものです。 

[Microsoft Cloud App Security を使用してセッション制御を適用する方法](/cloud-app-security/proxy-deployment-any-app)を確認してください。
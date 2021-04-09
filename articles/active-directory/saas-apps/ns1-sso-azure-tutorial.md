---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と NS1 SSO for Azure の統合 | Microsoft Docs'
description: Azure Active Directory と NS1 SSO for Azure の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.openlocfilehash: a7fad0af26d15bc77a52d55309c594937ba3be13
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92518621"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ns1-sso-for-azure"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と NS1 SSO for Azure の統合

このチュートリアルでは、NS1 SSO for Azure と Azure Active Directory (Azure AD) を統合する方法について説明します。 NS1 SSO for Azure と Azure AD を統合すると、次のことができます。

* NS1 SSO for Azure にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して NS1 SSO for Azure に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、[Azure Active Directory を使用したアプリケーション アクセスとシングル サインオンの概要](../manage-apps/what-is-single-sign-on.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* NS1 SSO for Azure でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* NS1 SSO for Azure では、SP Initiated SSO と IDP Initiated SSO がサポートされます。
* NS1 SSO for Azure を構成した後、セッション制御を適用できます。 これにより、組織の機密データを流出と侵入からリアルタイムで保護することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。


## <a name="add-ns1-sso-for-azure-from-the-gallery"></a>ギャラリーからの NS1 SSO for Azure の追加

Azure AD への NS1 SSO for Azure の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に NS1 SSO for Azure を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**NS1 SSO for Azure**」と入力します。
1. 結果のパネルから **[NS1 SSO for Azure]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-ns1-sso-for-azure"></a>NS1 SSO for Azure の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、NS1 SSO for Azure に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと NS1 SSO for Azure の関連ユーザーとの間にリンク関係を確立します。

NS1 SSO for Azure で Azure AD SSO を構成してテストする一般的な手順を次に示します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。

    a. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。

    b. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[NS1 SSO for Azure の SSO の構成](#configure-ns1-sso-for-azure-sso)** - アプリケーション側でシングル サインオン設定を構成します。

    a. **[NS1 SSO for Azure のテスト ユーザーの作成](#create-an-ns1-sso-for-azure-test-user)** - NS1 SSO for Azure で B.Simon に対応するユーザーを作成します。 このユーザーを Azure AD の対応するユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **NS1 SSO for Azure** アプリケーション統合ページで、 **[管理]** セクションを見つけて、[シングル サインオン] を選択します。 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンを選択して設定を編集します。

   ![鉛筆アイコンが強調表示された [SAML でシングル サインオンをセットアップします] ページのスクリーンショット](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、次の URL を入力します: `https://api.nsone.net/saml/metadata`。

    b. **[応答 URL]** ボックスに、次のパターンを使用する URL を入力します: `https://api.nsone.net/saml/sso/<ssoid>`。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** を選択して次の手順を実行します。

    **[サインオン URL]** ボックスに、次の URL を入力します: `https://my.nsone.net/#/login/sso`

    > [!NOTE]
    > 応答 URL は、実際の値ではありません。 応答 URL 値を実際の応答 URL で更新します。 この値を取得するには、[NS1 SSO for Azure クライアント サポート チーム](mailto:techops@nsone.net)に問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. NS1 SSO for Azure アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性とクレーム]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、鉛筆アイコンを選択して **[ユーザー属性]** ダイアログ ボックスを開きます。

    ![鉛筆アイコンが強調表示された [ユーザー属性とクレーム] セクションのスクリーンショット](./media/ns1-sso-for-azure-tutorial/attribute-edit-option.png)

1. 属性名を選択して要求を編集します。

    ![属性名が強調表示された [ユーザー属性とクレーム] セクションのスクリーンショット](./media/ns1-sso-for-azure-tutorial/attribute-claim-edit.png)

1. **[変換]** を選択します。

    ![変換が強調表示された [要求の管理] セクションのスクリーンショット](./media/ns1-sso-for-azure-tutorial/prefix-edit.png)

1. **[変換の管理]** セクションで、次の手順を実行します。

    ![さまざまなフィールドが強調表示された [変換の管理] セクションのスクリーンショット](./media/ns1-sso-for-azure-tutorial/prefix-added.png)

    1. **[Transformation]\(変換\)** として **[ExactMailPrefix()]** を選択します。

    1. **[パラメーター 1]** として **[user.userprincipalname]** を選択します。

    1. **[追加]** を選択します。

    1. **[保存]** を選択します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、コピー ボタンを選択します。 これにより、**アプリのフェデレーション メタデータ URL** がコピーされ、お使いのコンピューターに保存されます。

    ![コピー ボタンが強調表示された [SAML 署名証明書] セクションのスクリーンショット](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。

   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** フィールドに表示された値を書き留めます。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に NS1 SSO for Azure へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。
1. アプリケーションの一覧で、 **[NS1 SSO for Azure]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] が強調表示された [管理] セクションのスクリーンショット](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択します。 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] が強調表示された [ユーザーとグループ] ページのスクリーンショット](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[B.Simon]** を選択します。 次に、画面の下部にある **[選択]** ボタンを選択します。
1. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 次に、画面の下部にある **[選択]** ボタンを選択します。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

## <a name="configure-ns1-sso-for-azure-sso"></a>NS1 SSO for Azure の SSO の構成

NS1 SSO for Azure 側でシングル サインオンを構成するには、アプリのフェデレーション メタデータ URL を [NS1 SSO for Azure サポート チーム](mailto:techops@nsone.net)に送信する必要があります。 この設定が構成され、SAML SSO 接続が両側で正しく行われます。

### <a name="create-an-ns1-sso-for-azure-test-user"></a>NS1 SSO for Azure のテスト ユーザーの作成

このセクションでは、NS1 SSO for Azure で B.Simon というユーザーを作成します。 NS1 SSO for Azure サポート チームと協力して、NS1 SSO for Azure プラットフォームにユーザーを追加します。 ユーザーを作成してアクティブ化するまでは、シングル サインオンを使用できません。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [NS1 SSO for Azure] タイルを選択すると、SSO を設定した NS1 SSO for Azure に自動的にサインインします。 詳細については、「[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリケーションと Azure Active Directory との統合に関するチュートリアル](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で NS1 SSO for Azure を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)
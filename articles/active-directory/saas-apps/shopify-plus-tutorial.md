---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Shopify Plus の統合 | Microsoft Docs
description: Azure Active Directory と Shopify Plus の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 65f4963f23d97ca2e3af34febb0d5dbea652fc12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646982"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-shopify-plus"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Shopify Plus の統合

このチュートリアルでは、Shopify Plus と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Shopify Plus を統合すると、次のことができます。

* Shopify Plus にアクセスできるユーザーを Azure AD で制御します。
* ユーザーが Azure AD アカウントを使用して Shopify Plus に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Shopify Plus でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Shopify Plus では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。

## <a name="add-shopify-plus-from-the-gallery"></a>ギャラリーからの Shopify Plus の追加

Azure AD への Shopify Plus の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Shopify Plus を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Shopify Plus**」と入力します。
1. 結果のパネルから **[Shopify Plus]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-shopify-plus"></a>Shopify Plus の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Shopify Plus との Azure AD SSO を構成し、テストします。 SSO が機能するためには、Azure AD ユーザーと Shopify Plus の関連ユーザーとの間にリンク関係を確立する必要があります。

Shopify Plus に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Shopify Plus の構成](#configure-shopify-plus-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Shopify Plus のテスト ユーザーの作成](#create-shopify-plus-test-user)** - Shopify Plus で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクします。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Shopify Plus** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    **[応答 URL]** ボックスに、`https://accounts.shopify.com/saml/consume/organization/<ORGANIZATION_ID>` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** テキスト ボックスに、URL として「`https://shopify.plus/login`」と入力します。

    > [!NOTE]
    > 応答 URL 値は、実際の値ではありません。 実際の応答 URL でこの値を更新します。 この値を取得するには、[Shopify Plus クライアント サポート チーム](mailto:plus-user-management@shopify.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. Shopify Plus アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. 上記に加えて、Shopify Plus アプリケーションでは、SAML 応答でいくつかの属性が返されると想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性|
    | ---- | --------------- |
    | email | User.mail |

1. **[名前 ID]** の形式を **[永続的]** に変更します。 **[一意のユーザー識別子 (名前 ID)]** オプションを選択し、 **[名前識別子]** 形式を選択します。 このオプションに **[永続的]** を選択します。 変更を保存します。
1. **[SAML によるシングル サインオンのセットアップ]** ページの **[SAML 署名証明書]** セクションで、コピー ボタンを選択して **[アプリのフェデレーション メタデータ URL]** をコピーし、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

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

このセクションでは、Shopify Plus へのアクセスを許可することで、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Shopify Plus]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-shopify-plus-sso"></a>Shopify Plus SSO の構成

完全な手順を確認するには、[SAML 統合の設定に関する Shopify のドキュメント](https://help.shopify.com/en/manual/shopify-plus/saml)を参照してください。

**Shopify Plus** 側でシングルサインオンを構成するには、**アプリのフェデレーション メタデータ URL** を Azure Active Directory からコピーします。 次に、[組織の管理者](https://shopify.plus)にログインし、 **[Users]\(ユーザー\)**  >  **[Security]\(セキュリティ\)** に移動します。 **[Set up configuration]\(構成の設定\)** を選択し、 **[Identity provider metadata URL]\(ID プロバイダー メタデータ URL\)** にアプリのフェデレーション メタデータ URL を貼り付けます。 **[Add]\(追加\)** を選択してこの手順を完了します。

### <a name="create-shopify-plus-test-user"></a>Shopify Plus テスト ユーザーの作成

このセクションでは、Shopify Plus で B.Simon というユーザーを作成します。 **[Users]\(ユーザー\)** セクションに戻り、電子メールとアクセス許可を入力してユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="enforce-saml-authentication"></a>SAML 認証の適用

> [!NOTE]
> 個々のユーザーを使用して統合をテストしてから、広く適用することをお勧めします。

個々のユーザー:
1. Azure AD によって管理され、Shopify Plus で検証済みのメール ドメインを使用して Shopify Plus の個々のユーザーのページに移動します。
1. SAML 認証セクションで、 **[Edit]\(編集\)** を選択し、 **[Required]\(必須\)** を選択してから、 **[Save]\(保存\)** を選択します。
1. このユーザーが idP-initiated および SP-initiated フローを使用して正常にサインインできることをテストします。

メール ドメインのすべてのユーザー:
1. **[Security]\(セキュリティ\)** ページに戻ります。
1. SAML 認証設定に **[Required]\(必須\)** を選択します。 これにより、Shopify Plus 全体でそのメール ドメインを使用するすべてのユーザーに SAML が適用されます。
1. **[保存]** を選択します。

> [!IMPORTANT]
> メール ドメインのすべてのユーザーに対して SAML を有効にすると、このアプリケーションを使用するすべてのユーザーに影響します。 ユーザーは、通常のサインイン ページを使用してサインインすることはできません。 Azure Active Directory を介してのみアプリにアクセスできます。 Shopify には、ユーザーが通常のユーザー名とパスワードを使用してサインインできるバックアップ サインイン URL は用意されていません。 必要に応じて、Shopify サポートに連絡して SAML を無効にできます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Shopify Plus のサインオン URL にリダイレクトされます。  

* Shopify Plus のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Shopify Plus に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Shopify Plus] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Shopify Plus に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Shopify Plus を構成したら、組織の機密データの流出と侵入をリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
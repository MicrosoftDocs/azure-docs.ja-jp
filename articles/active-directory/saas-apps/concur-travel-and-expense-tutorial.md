---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Concur Travel and Expense の統合 | Microsoft Docs
description: Azure Active Directory と Concur Travel and Expense の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: jeedes
ms.openlocfilehash: 56eab06bd1afd18bfd4e23b9acb0b44d7bd1f80b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737034"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-concur-travel-and-expense"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Concur Travel and Expense の統合

このチュートリアルでは、Concur Travel and Expense と Azure Active Directory (Azure AD) を統合する方法について説明します。 Concur Travel and Expense と Azure AD を統合すると、次のことができます。

* Concur Travel and Expense にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Concur Travel and Expense に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Concur Travel and Expense のサブスクリプション。
* Concur ユーザー アカウントの "社内管理者" ロール。 適切なアクセス権があるかどうかは、[Concur SSO セルフサービス ツール](https://www.concursolutions.com/nui/authadmin/ssoadmin)に移動して調べることができます。 アクセス権がなかった場合は、Concur サポートまたは導入プロジェクト マネージャーに問い合わせてください。 

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、Azure AD の SSO を構成してテストします。

* Concur Travel and Expense では、**IDP Initiated SSO** と **SP Initiated SSO** がサポートされます
* Concur Travel and Expense では、運用環境と導入環境の両方で SSO のテストがサポートされます 

> [!NOTE]
> このアプリケーションの識別子は、3 つのリージョンのそれぞれについて固定された文字列値です。それらのリージョンは、米国、EMEA、および中国です。 そのため、1 つのテナントで構成できるインスタンスは、各リージョンにつき 1 つだけです。 

## <a name="adding-concur-travel-and-expense-from-the-gallery"></a>ギャラリーからの Concur Travel and Expense の追加

Azure AD への Concur Travel and Expense の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Concur Travel and Expense を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Concur Travel and Expense**」と入力します。
1. 結果のパネルから **[Concur Travel and Expense]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-concur-travel-and-expense"></a>Concur Travel and Expense の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Concur Travel and Expense に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Concur Travel and Expense の関連ユーザーとの間にリンク関係を確立する必要があります。

Concur Travel and Expense に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Concur Travel and Expense の SSO の構成](#configure-concur-travel-and-expense-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Concur Travel and Expense のテスト ユーザーの作成](#create-concur-travel-and-expense-test-user)** - Concur Travel and Expense で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Concur Travel and Expense** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションでは、アプリケーションは **IDP** 開始モードで事前に構成されており、必要な URL は既に Azure で事前に設定されています。 構成を保存するには、 **[保存]** ボタンをクリックします。

    > [!NOTE]
    > 識別子 (エンティティ ID) と応答 URL (Assertion Consumer Service URL) はリージョン固有です。 Concur エンティティのデータセンターに基づいて選択してください。 Concur エンティティのデータセンターがわからない場合は、Concur サポートにお問い合わせください。 

5. **[SAML でシングル サインオンをセットアップします]** ページで、**[ユーザー属性]** の編集 (ペン) アイコンをクリックして設定を編集します。 [一意のユーザー ID] は、Concur のユーザーのログイン ID と一致している必要があります。 通常、**user.userprincipalname** は **user.mail** に変更する必要があります。

    ![ユーザー属性の編集](common/edit-attribute.png)

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、メタデータをダウンロードしてコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

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

このセクションでは、B.Simon に Concur Travel and Expense へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Concur Travel and Expense]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-concur-travel-and-expense-sso"></a>Concur Travel and Expense の SSO の構成

1. Concur Travel and Expense 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Concur Travel and Expense のセットアップ]** をクリックすると、Concur Travel and Expense アプリケーションに移動します。 そこから、管理者の資格情報を入力して Concur Travel and Expense にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 7 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Concur Travel and Expense を手動で設定する場合は、別の Web ブラウザー ウィンドウで、ダウンロードした **フェデレーション メタデータ XML** を [Concur SSO セルフ サービス ツール](https://www.concursolutions.com/nui/authadmin/ssoadmin)にアップロードして、Concur Travel and Expense 会社サイトに管理者としてサインインする必要があります。

1. **[追加]** をクリックします。
1. IdP のカスタム名を入力します (例: "Azure AD (US)")。 
1. **[Upload XML File]\(XML ファイルのアップロード\)** をクリックし、先ほどダウンロードした **フェデレーション メタデータ XML** を添付します。
1. **[Add Metadata]\(メタデータの追加\)** をクリックして変更を保存します。

    ![Concur SSO セルフ サービス ツールのスクリーンショット](./media/concur-travel-and-expense-tutorial/add-metadata-concur-self-service-tool.png)

### <a name="create-concur-travel-and-expense-test-user"></a>Concur Travel and Expense のテスト ユーザーの作成

このセクションでは、Concur Travel and Expense で B.Simon というユーザーを作成します。 Concur サポート チームと連携し、Concur Travel and Expense プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 

> [!NOTE]
> B.Simon の Concur ログイン ID は、Azure AD における B.Simon の一意識別子と一致している必要があります。 たとえば、Azure AD における B.Simon の一意識別子が `B.Simon@contoso.com` である場合、 Concur における B.Simon のログイン ID も `B.Simon@contoso.com` である必要があります。 

## <a name="configure-concur-mobile-sso"></a>Concur Mobile の SSO の構成
Concur Mobile の SSO を有効にするには、Concur サポート チームに **ユーザーのアクセス URL** を提供する必要があります。 次の手順に従って、Azure AD から **ユーザーのアクセス URL** を入手してください。
1. **[エンタープライズ アプリケーション]** に移動します。
1. **[Concur Travel and Expense]** をクリックします。
1. **[プロパティ]** をクリックします。
1. **[ユーザーのアクセス URL]** をコピーして、その URL を Concur サポートに提供します。

> [!NOTE]
> SSO をセルフ サービスで構成するオプションは用意されていません。Concur サポート チームと連携して Mobile の SSO を有効にしてください。 

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Concur Travel and Expense のサインオン URL にリダイレクトされます。

* Concur Travel and Expense のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Concur Travel and Expense に自動的にサインインされます

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Concur Travel and Expense] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Concur Travel and Expense に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Concur Travel and Expense を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
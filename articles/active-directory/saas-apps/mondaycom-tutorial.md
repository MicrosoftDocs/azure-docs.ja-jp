---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と monday.com の統合 | Microsoft Docs
description: Azure Active Directory と monday.com の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/08/2021
ms.author: jeedes
ms.openlocfilehash: 06f4ddff9c897858eec27f97f8e45a3d656e951d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101650789"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mondaycom"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と monday.com の統合

このチュートリアルでは、monday.com と Azure Active Directory (Azure AD) を統合する方法について説明します。 monday.com と Azure AD を統合すると、次のことが可能になります。

* monday.com にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで monday.com に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* monday.com でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* monday.com では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます
* monday.com では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="add-mondaycom-from-the-gallery"></a>ギャラリーからの monday.com の追加

Azure AD への monday.com の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に monday.com を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**monday.com**」と入力します。
1. 結果のパネルから **[monday.com]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-mondaycom"></a>monday.com の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、monday.com に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと monday.com の関連ユーザーとの間にリンク関係を確立する必要があります。

monday.com に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[monday.com の SSO の構成](#configure-mondaycom-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[monday.com のテスト ユーザーの作成](#create-mondaycom-test-user)** - monday.com で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **monday.com** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **サービス プロバイダー メタデータ ファイル** を保持しており、**IDP** Initiated モードに構成したい場合は、 **[基本的な SAML 構成]** セクション上で次の手順を実行します。

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    ![メタデータ ファイルをアップロードする](common/upload-metadata.png)

    b. **フォルダー ロゴ** をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    ![メタデータ ファイルを選択する](common/browse-upload-metadata.png)

    c. メタデータ ファイルが正常にアップロードされると、**識別子** と **応答 URL** の値が、[基本的な SAML 構成] セクションに自動的に設定されます。

    > [!Note]
    > **識別子** と **応答 URL** の値が自動的に設定されない場合は、手動で値を入力してください。 **識別子** と **応答 URL** は同じであり、その値は `https://<your-domain>.monday.com/saml/saml_callback` というパターンになっています。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<YOUR_DOMAIN>.monday.com` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[monday.com クライアント サポート チーム](https://monday.com/contact-us/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. monday.com アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![スクリーンショットは [User Attributes & Claims]\(ユーザー属性とクレーム\) を示しています。[Givenname]\(名\) の user.givenname や [Emailaddress]\(メール アドレス\) の user.mail など、既定値が表示されています。](common/default-attributes.png)

1. その他に、monday.com アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性 |
    |--|--|
    | Email | User.mail |
    | FirstName | User.givenname |
    | LastName | User.surname |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Set up monday.com]\(monday.com のセットアップ\)** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に monday.com へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[monday.com]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-mondaycom-sso"></a>monday.com の SSO の構成

1. monday.com 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **マイアプリによるセキュリティで保護された Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[Setup monday.com]\(monday.com のセットアップ\)** をクリックすると、monday.com アプリケーションに移動します。 そこから、管理者の資格情報を入力して monday.com にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. monday.com を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として monday.com サイトにサインインして、次の手順を実行します。

1. ページの右上隅にある **[プロファイル]** に移動し、 **[管理者]** をクリックします。

    ![[管理者] プロファイルが選択されていることを示すスクリーンショット。](./media/mondaycom-tutorial/configuration-1.png)

1. **[セキュリティ]** を選択し、SAML の横の **[開く]** をクリックします。

    ![[セキュリティ] タブを示すスクリーンショット。[SAML] の横にある [開く] オプションが表示されています。](./media/mondaycom-tutorial/configuration-2.png)

1. 実際の IDP から、以下の詳細を入力します。

    ![[SAML プロバイダー] を示すスクリーンショット。ここでは、IDP からの情報を入力できます。](./media/mondaycom-tutorial/configuration-3.png)

    > [!NOTE]
    > 詳細については、[こちら](https://support.monday.com/hc/articles/360000460605-SAML-Single-Sign-on?abcb=34642)の記事を参照してください。

### <a name="create-mondaycom-test-user"></a>monday.com のテスト ユーザーの作成

このセクションでは、B.Simon というユーザーを monday.com に作成します。 monday.com では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ monday.com に存在しない場合は、monday.com にアクセスしようとしたときに新しいユーザーが作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる monday.com のサインオン URL にリダイレクトされます。  

* monday.com のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した monday.com に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [monday.com] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した monday.com に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

monday.com を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
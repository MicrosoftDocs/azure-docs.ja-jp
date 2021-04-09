---
title: チュートリアル:Azure Active Directory と RStudio Connect SAML Authentication の統合 | Microsoft Docs
description: Azure Active Directory と RStudio Connect SAML Authentication の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/21/2020
ms.author: jeedes
ms.openlocfilehash: 84e8c7fc1d2655ea0685ac79841a9c467bf766cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182393"
---
# <a name="tutorial-azure-active-directory-integration-with-rstudio-connect-saml-authentication"></a>チュートリアル:Azure Active Directory と RStudio Connect SAML Authentication の統合

このチュートリアルでは、RStudio Connect SAML Authentication と Azure Active Directory (Azure AD) を統合する方法について説明します。
RStudio Connect SAML Authentication と Azure AD の統合には、次の利点があります。

* RStudio Connect SAML Authentication にアクセスできるユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントを使用して RStudio Connect SAML Authentication に自動的にサインイン (シングル サインオン) できるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

RStudio Connect SAML Authentication と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* RStudio Connect SAML Authentication。 [45 日間無料評価版](https://www.rstudio.com/products/connect/)があります。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* RStudio Connect SAML Authentication では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます

* RStudio Connect SAML Authentication では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-rstudio-connect-saml-authentication-from-the-gallery"></a>ギャラリーからの RStudio Connect SAML Authentication の追加

Azure AD への RStudio Connect SAML Authentication の統合を構成するには、マネージド SaaS アプリの一覧にギャラリーから RStudio Connect SAML Authentication を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**RStudio Connect SAML Authentication**」と入力します。
1. 結果のパネルから **[RStudio Connect SAML Authentication]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-rstudio-connect-saml-authentication"></a>RStudio Connect SAML Authentication の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、RStudio Connect SAML Authentication に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと RStudio Connect SAML Authentication の関連ユーザーとの間にリンク関係を確立する必要があります。

RStudio Connect SAML Authentication に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[RStudio Connect SAML Authentication の SSO の構成](#configure-rstudio-connect-saml-authentication-sso)** - アプリケーション側でシングル サインオンを構成します。
    * **[RStudio Connect SAML Authentication のテスト ユーザーの作成](#create-rstudio-connect-saml-authentication-test-user)** - RStudio Connect SAML Authentication で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
3. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **RStudio Connect SAML Authentication** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** initiated モードで構成する場合は、次の手順を実行します。`<example.com>` は、お使いの RStudio Connect SAML Authentication のサーバー アドレスおよびポートで置き換えてください。

    ![RStudio Connect SAML Authentication のドメインと URL のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://<example.com>/__login__/saml` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<example.com>/__login__/saml/acs` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![RStudio Connect SAML Authentication のメタデータのアップロード](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、`https://<example.com>/` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 それらは、RStudio Connect SAML Authentication のサーバー アドレス (上記例の `https://example.com`) から判別されます。 問題が発生した場合は、[RStudio Connect SAML Authentication のサポート チーム](mailto:support@rstudio.com)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. RStudio Connect SAML Authentication アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングをご自分の SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示しています。ここで、**nameidentifier** は **user.userprincipalname** にマップされています。 RStudio Connect SAML Authentication アプリケーションでは、**nameidentifier** が **user.mail** にマップされると想定されているため、 **[編集]** アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![image](common/edit-attribute.png)

7. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

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

このセクションでは、B.Simon に RStudio Connect SAML Authentication へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[RStudio Connect SAML Authentication]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-rstudio-connect-saml-authentication-sso"></a>RStudio Connect SAML Authentication の SSO の構成

**RStudio Connect SAML Authentication** でシングル サインオンを構成するには、上記で使用された **[アプリのフェデレーション メタデータ URL]** と **[サーバー アドレス]** を使用する必要があります。 これは、`/etc/rstudio-connect.rstudio-connect.gcfg` にある RStudio Connect SAML Authentication 構成ファイルで行います。

これは、構成ファイルの例です。

```
[Server]
SenderEmail =

; Important! The user-facing URL of your RStudio Connect SAML Authentication server.
Address = 

[Http]
Listen = :3939

[Authentication]
Provider = saml

[SAML]
Logging = true

; Important! The URL where your IdP hosts the SAML metadata or the path to a local copy of it placed in the RStudio Connect SAML Authentication server.
IdPMetaData = 

IdPAttributeProfile = azure
SSOInitiated = IdPAndSP
```

**サーバー アドレス** を `Server.Address` 値に格納し、**アプリのフェデレーション メタデータ URL** を `SAML.IdPMetaData` 値に格納します。 このサンプル構成では、暗号化されていない HTTP 接続を使用しますが、Azure AD では暗号化された HTTPS 接続を使用する必要があることに注意してください。 RStudio Connect SAML Authentication の前面に[リバース プロキシ](https://docs.rstudio.com/connect/admin/proxy/)を使用するか、[HTTPS を直接使用](https://docs.rstudio.com/connect/admin/appendix/configuration/#HTTPS)するように RStudio Connect SAML Authentication を構成することができます。 

構成に問題がある場合は、[RStudio Connect SAML Authentication の管理ガイド](https://docs.rstudio.com/connect/admin/authentication/saml/)をお読みいただくか、ヘルプについてのメールを [RStudio のサポート チーム](mailto:support@rstudio.com)まで送信してください。

### <a name="create-rstudio-connect-saml-authentication-test-user"></a>RStudio Connect SAML Authentication のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを RStudio Connect SAML Authentication に作成します。 RStudio Connect SAML Authentication では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ RStudio Connect SAML Authentication に存在しない場合は、RStudio Connect SAML Authentication にアクセスしようとしたときに新しいユーザーが作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる RStudio Connect SAML Authentication のサインオン URL にリダイレクトされます。  

* RStudio Connect SAML Authentication のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した RStudio Connect SAML Authentication に自動的にサインインされます 

また、Microsoft アクセス パネルを使用して、任意のモードでアプリケーションをテストすることもできます。 アクセス パネルで [RStudio Connect SAML Authentication] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した RStudio Connect SAML Authentication に自動的にサインインされます。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

RStudio Connect SAML Authentication を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
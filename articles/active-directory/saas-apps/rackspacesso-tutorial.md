---
title: 'チュートリアル: Azure Active Directory と Rackspace SSO の統合 | Microsoft Docs'
description: Azure Active Directory と Rackspace SSO の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: eed0ea075c5e9eb39810259be2aa6781a59f2f22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92511651"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>チュートリアル: Azure Active Directory と Rackspace SSO の統合

このチュートリアルでは、Rackspace SSO と Azure Active Directory (Azure AD) を統合する方法について説明します。
Rackspace SSO と Azure AD の統合には、次の利点があります。

* Rackspace SSO にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Rackspace SSO に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Rackspace SSO と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Rackspace SSO でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Rackspace SSO では、**SP** によって開始される SSO がサポートされます

## <a name="adding-rackspace-sso-from-the-gallery"></a>ギャラリーからの Rackspace SSO の追加

Azure AD への Rackspace SSO の統合を構成するに、ギャラリーから管理対象 SaaS アプリの一覧に Rackspace SSO を追加する必要があります。

**ギャラリーから Rackspace SSO を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Rackspace SSO**」と入力し、結果パネルから **[Rackspace SSO]** を選択してから、**[追加]** ボタンをクリックしてアプリケーションを追加します。

     ![結果一覧の Rackspace SSO](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Rackspace SSO で Azure AD のシングル サインオンを構成し、テストします。
Rackspace でシングル サインオンを使用する場合、Rackspace ユーザーは、Rackspace ポータルに初めてサインインしたときに自動的に作成されます。 

Rackspace SSO で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Rackspace SSO のシングル サインオンの構成](#configure-rackspace-sso-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Rackspace コントロール パネルでの属性マッピングの設定](#set-up-attribute-mapping-in-the-rackspace-control-panel)** - Rackspace のロールを Azure AD ユーザーに割り当てます。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Rackspace SSO で Azure AD のシングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Rackspace SSO** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、適切な [URL](https://login.rackspace.com/federate/sp.xml) からダウンロードできる **サービス プロバイダー メタデータ ファイル** をアップロードし、次の手順を実行します。

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    ![スクリーンショットは、[メタデータ ファイルをアップロードする] リンクを含む、[基本的な SAML 構成] を示しています。](common/upload-metadata.png)

    b. **フォルダー ロゴ** をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    ![スクリーンショットは、ファイルを選択してアップロードできるダイアログ ボックスを示しています。](common/browse-upload-metadata.png)

    c. メタデータ ファイルが正常にアップロードされると、必要な URL が自動的に設定されます。

    d. **[サインオン URL]** テキスト ボックスに URL として「`https://login.rackspace.com/federate/`」と入力します。

    ![[Rackspace SSO Domain and URLs] (Rackspace SSO のドメインと URL) のシングル サインオン情報](common/sp-signonurl.png)   

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

このファイルが Rackspace にアップロードされて、必要な ID フェデレーションの構成設定が読み込まれます。

### <a name="configure-rackspace-sso-single-sign-on"></a>Rackspace SSO のシングル サインオンを構成する

**Rackspace SSO** 側でシングル サインオンを構成するには:

1. [コントロール パネルへの ID プロバイダーの追加](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/)に関するドキュメントを参照します。
1. その手順に従って以下を実行します。
    1. 新しい ID プロバイダーを作成します。
    1. ユーザーがサインイン時に会社を特定するために使用するメール ドメインを指定します。
    1. Azure コントロール パネルから先にダウンロードした **フェデレーション メタデータ XML** をアップロードします。

これにより、Azure と Rackspace が連携するために必要となる基本的な SSO 設定が正しく構成されます。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Rackspace SSO へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]**、**[Rackspace SSO]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Rackspace SSO]** を選択します。

    ![アプリケーションの一覧の [Rackspace SSO] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Rackspace コントロール パネルでの属性マッピングの設定

Rackspace は、**属性マッピング ポリシー** を使用して Rackspace のロールやグループをシングル サインオン ユーザーに割り当てます。 Azure AD の SAML 要求は、**属性マッピング ポリシー** によって、Rackspace に必要なユーザー構成フィールドに変換されます。 詳細については、Rackspace の[属性マッピングの基礎に関するドキュメント](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/)を参照してください。 いくつかの考慮事項があります。

* Azure AD グループを使用して、さまざまなレベルの Rackspace アクセスを割り当てたい場合は、Azure **Rackspace SSO** のシングル サインオン設定でグループ要求を有効にする必要があります。 それらのグループは、**属性マッピング ポリシー** を使用して、Rackspace の適切なロールとグループに対応付けられます。

    ![グループ要求の設定](common/sso-groups-claim.png)

* 既定では、Azure AD が Azure AD グループの (名前ではなく) UID を SAML 要求で送信します。 ただし、オンプレミスの Active Directory を Azure AD と同期している場合は、実際のグループの名前を送信することもできます。

    ![グループ要求の名前の設定](common/sso-groups-claims-names.png)

次の例の **属性マッピング ポリシー** では、以下を示しています。
1. Rackspace ユーザーの名前を `user.name` SAML 要求に設定します。 任意の要求を使用できますが、これはユーザーのメール アドレスを含んだフィールドに設定するのが最も一般的です。
1. グループ名またはグループ UID で Azure AD グループを突き合わせることによって、Rackspace のロールである `admin` と `billing:admin` をユーザーに設定します。 `roles` フィールドには `"{0}"` の "*代入*" が使用され、`remote` ルール式の結果で置き換えられます。
1. SAML 交換で標準的な既知の SAML 要求を探すことによって Rackspace が追加の SAML フィールドを取得できるよう、" *既定の代入*" である `"{D}"` を使用します。

```yaml
---
mapping:
    rules:
    - local:
        user:
          domain: "{D}"
          name: "{At(http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name)}"
          email: "{D}"
          roles:
              - "{0}"
          expire: "{D}"
      remote:
          - path: |
              (
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='7269f9a2-aabb-9393-8e6d-282e0f945985') then ('admin', 'billing:admin') else (),
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='MyAzureGroup') then ('admin', 'billing:admin') else ()
              )
            multiValue: true
  version: RAX-1
```
> [!TIP]
> ポリシー ファイルの編集する際は YAML 構文が検証されるテキスト エディターを必ず使用してください。

その他の例については、Rackspace の[属性マッピングの基礎に関するドキュメント](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/)を参照してください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Rackspace SSO] タイルをクリックすると、SSO を設定した Rackspace SSO に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

**Rackspace SSO** のシングル サインオン設定にある **[Validate]\(検証\)** ボタンを使用することもできます。

   ![SSO の検証ボタン](common/sso-validate-sign-on.png)

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)
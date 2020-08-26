---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Veracode の統合 | Microsoft Docs
description: Azure Active Directory と Veracode の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: jeedes
ms.openlocfilehash: a8c7c70e7956868bf069704eb5fe34db014dd135
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88532468"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Veracode の統合

このチュートリアルでは、Veracode と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Veracode を統合すると、次のことができます。

* Veracode にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Veracode に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、[Azure Active Directory を使用したアプリケーション アクセスとシングル サインオンの概要](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Veracode でのシングル サインオン (SSO) が有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 Veracode では、ID プロバイダーが開始する SSO のほか、Just In Time ユーザー プロビジョニングがサポートされます。

## <a name="add-veracode-from-the-gallery"></a>ギャラリーからの Veracode の追加

Azure AD への Veracode の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Veracode を追加します。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「Veracode」と入力します。
1. 結果のパネルから **Veracode** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-veracode"></a>Veracode の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Veracode に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Veracode の関連ユーザーとの間にリンクを確立する必要があります。

Veracode に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Veracode SSO の構成](#configure-veracode-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Veracode テスト ユーザーの作成](#create-veracode-test-user)** - Veracode で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Veracode** アプリケーション統合ページで、 **[管理]** セクションを見つけます。 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンを選択して設定を編集します。

   ![鉛筆アイコンが強調表示された [SAML でシングル サインオンをセットアップします] のスクリーンショット](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションでは、アプリケーションは事前に構成されており、必要な URL は既に Azure で事前に設定されています。 **[保存]** を選択します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけます。 **[ダウンロード]** を選択して証明書をダウンロードし、コンピューターに保存します。

    ![[ダウンロード] リンクが強調表示された [SAML 署名証明書] セクションのスクリーンショット](common/certificatebase64.png)

1. Veracode では、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![[ユーザー属性とクレーム] セクションのスクリーンショット](common/default-attributes.png)

1. Veracode は、いくつかの追加の属性が SAML 応答で返されることも予期しています。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性|
    | ---------------| --------------- |
    | firstname |User.givenname |
    | lastname |User.surname |
    | email |User.mail |

1. **[Veracode のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL が強調表示された [Veracode のセットアップ] セクションのスクリーンショット](common/copy-configuration-urls.png)

## <a name="configure-veracode-sso"></a>Veracode の SSO の構成

1. 別の Web ブラウザーのウィンドウで、Veracode 企業サイトに管理者としてサインインします。

1. 上部のメニューから **[設定]**  >  **[Admin] (管理)** を選択します。
   
    ![[設定] アイコンと [管理] (Admin) が強調表示されている Veracode 管理のスクリーンショット](./media/veracode-tutorial/ic802911.png "管理")

1. **[SAML]** タブを選択します。

1. **[組織の SAML 設定]** セクションで、次の手順に従います。

    ![[組織の SAML 設定] セクションのスクリーンショット](./media/veracode-tutorial/ic802912.png "管理")

    a.  **[Issuer] (発行者)** に、Azure portal からコピーした **[Azure AD 識別子]** の値を貼り付けます。

    b. **[Assertion Signing Certificate] (アサーション署名証明書)** で、 **[ファイルの選択]** を選択して、Azure portal からダウンロードした証明書をアップロードします。

    c. **[自己登録]** で、 **[自己登録を有効にする]** を選択します。

1. **[自己登録の設定]** セクションで次の手順を実行し、 **[保存]** を選択します。

    ![[自己登録の設定] セクションのスクリーンショット (さまざまなオプションが強調表示されています)](./media/veracode-tutorial/ic802913.png "管理")

    a. **[新しいユーザーのアクティブ化]** で、 **[アクティブ化不要]** を選択します。

    b. **[ユーザー データの更新]** で、 **[優先 Veracode ユーザー データ]** を選択します。

    c. **[SAML 属性の詳細]** では、以下を選択します。
      * **ユーザー ロール**
      * **ポリシー管理者**
      * **レビュー担当者**
      * **セキュリティ リーダー**
      * **役員**
      * **申請者**
      * **作成者**
      * **すべてのスキャンの種類**
      * **チームのメンバーシップ**
      * **既定のチーム**

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]**  > **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。

   1. **名前**には、`B.Simon`を入力します。  
   1. **[ユーザー名]** に「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** を選び、表示された値を書き留めます。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Veracode へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。
1. アプリケーションの一覧で **[Veracode]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] が強調表示された [管理] セクションのスクリーンショット](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択します。 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] が強調表示された [ユーザーとグループ] ページのスクリーンショット](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログ ボックスで、 **[ユーザー]** から **[B.Simon]** を選択します。 次に、画面の下部にある **[選択]** を選択します。
1. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 次に、画面の下部にある **[選択]** を選択します。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

### <a name="create-veracode-test-user"></a>Veracode テスト ユーザーの作成

Veracode にサインインするには、Azure AD ユーザーを Veracode にプロビジョニングする必要があります。 このタスクは自動化されているため、手動で何かを行う必要はありません。 最初のシングル サインオンの試行中に、必要に応じてユーザーが自動的に作成されます。

> [!NOTE]
> Veracode から提供されている他の Veracode ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングすることもできます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で **[Veracode]** を選択すると、SSO を設定した Veracode に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で Veracode を試す](https://aad.portal.azure.com/)
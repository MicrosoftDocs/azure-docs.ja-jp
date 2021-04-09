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
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: f56f2dc974df58575c72c93a0609026cd7bbf88d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101652625"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Veracode の統合

このチュートリアルでは、Veracode と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Veracode を統合すると、次のことができます。

* Veracode にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Veracode に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Veracode でのシングル サインオン (SSO) が有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 Veracode では、ID プロバイダーが開始する SSO のほか、Just In Time ユーザー プロビジョニングがサポートされます。

## <a name="add-veracode-from-the-gallery"></a>ギャラリーからの Veracode の追加

Azure AD への Veracode の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Veracode を追加します。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「Veracode」と入力します。
1. 結果のパネルから **Veracode** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-veracode"></a>Veracode の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Veracode に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Veracode の関連ユーザーとの間にリンクを確立する必要があります。

Veracode に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Veracode SSO の構成](#configure-veracode-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Veracode テスト ユーザーの作成](#create-veracode-test-user)** - Veracode で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Veracode** アプリケーション統合ページで、 **[管理]** セクションを見つけます。 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンを選択して設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

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

このセクションでは、B.Simon に Veracode へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Veracode]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-veracode-sso"></a>Veracode の SSO の構成

1. 別の Web ブラウザーのウィンドウで、Veracode 企業サイトに管理者としてサインインします。

1. 上部のメニューから **[設定]**  >  **[Admin] (管理)** を選択します。
   
    ![[設定] アイコンと [管理] (Admin) が強調表示されている Veracode 管理のスクリーンショット](./media/veracode-tutorial/admin.png "管理")

1. **[SAML]** タブを選択します。

1. **[組織の SAML 設定]** セクションで、次の手順に従います。

    ![[組織の SAML 設定] セクションのスクリーンショット](./media/veracode-tutorial/saml.png "管理")

    a.  **[Issuer] (発行者)** に、Azure portal からコピーした **[Azure AD 識別子]** の値を貼り付けます。

    b. **[Assertion Signing Certificate] (アサーション署名証明書)** で、**[ファイルの選択]** を選択して、Azure portal からダウンロードした証明書をアップロードします。

    c. **[自己登録]** で、**[自己登録を有効にする]** を選択します。

1. **[自己登録の設定]** セクションで次の手順を実行し、**[保存]** を選択します。

    ![[自己登録の設定] セクションのスクリーンショット (さまざまなオプションが強調表示されています)](./media/veracode-tutorial/save.png "管理")

    a. **[新しいユーザーのアクティブ化]** で、**[アクティブ化不要]** を選択します。

    b. **[ユーザー データの更新]** で、**[優先 Veracode ユーザー データ]** を選択します。

    c. **[SAML 属性の詳細]** では、以下を選択します。
      * **ユーザー ロール**
      * **ポリシー管理者**
      * **レビュー担当者**
      * **セキュリティ リーダー**
      * **役員**
      * **申請者**
      * **Creator**
      * **すべてのスキャンの種類**
      * **チームのメンバーシップ**
      * **既定のチーム**

### <a name="create-veracode-test-user"></a>Veracode テスト ユーザーの作成

このセクションでは、B.Simon というユーザーを Veracode に作成します。 Veracode では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Veracode にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

> [!NOTE]
> Veracode から提供されている他の Veracode ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングすることもできます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Veracode に自動的にサインインされます。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Veracode] タイルをクリックすると、SSO を設定した Veracode に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Veracode を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
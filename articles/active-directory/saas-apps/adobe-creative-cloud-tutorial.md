---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Adobe Creative Cloud の統合 | Microsoft Docs
description: Azure Active Directory と Adobe Creative Cloud の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/14/2020
ms.author: jeedes
ms.openlocfilehash: 81e02bdc483c4b68901d7e81b29f3a7f423480d9
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88537602"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-creative-cloud"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Adobe Creative Cloud の統合

> [!NOTE]
> この記事では、Azure Active Directory (Azure AD) 用の Adobe Admin Console の SAML ベースのカスタム セットアップについて説明します。 新しい構成の場合は、[Azure AD コネクタ](https://helpx.adobe.com/enterprise/using/sso-setup-azure.html)を使用することをお勧めします。 Azure AD コネクタは数分で設定できるため、ドメイン要求、シングル サインオンのセットアップ、およびユーザー同期のプロセスを短時間で終えることができます。

このチュートリアルでは、Adobe Creative Cloud と Azure Active Directory (Azure AD) を統合する方法について説明します。 Adobe Creative Cloud と Azure AD を統合すると、次のことができます。

* Adobe Creative Cloud にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Adobe Creative Cloud に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Adobe Creative Cloud でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Adobe Creative Cloud では、**SP** Initiated SSO がサポートされます
* Adobe Creative Cloud を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>ギャラリーから Adobe Creative Cloud を追加する

Azure AD への Adobe Creative Cloud の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Adobe Creative Cloud を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Adobe Creative Cloud**」と入力します。
1. 結果のパネルから **[Adobe Creative Cloud]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-adobe-creative-cloud"></a>Adobe Creative Cloud の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Adobe Creative Cloud に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Adobe Creative Cloud の関連ユーザーとの間にリンク関係を確立する必要があります。

Adobe Creative Cloud に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Adobe Creative Cloud の SSO の構成](#configure-adobe-creative-cloud-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Adobe Creative Cloud のテスト ユーザーの作成](#create-adobe-creative-cloud-test-user)** - Adobe Creative Cloud で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Adobe Creative Cloud** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに URL として「`https://adobe.com`」と入力します。

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > この識別子の値は実際のものではありません。 「**Adobe Creative Cloud の SSO の構成**」セクションの手順 4. のガイダンスに従ってください。 これにより、**フェデレーション メタデータ XML ファイル**を開いて、そこからエンティティ ID の値を取得し、Azure AD 構成の識別子の値として入力できます。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. Adobe Creative Cloud アプリケーションでは、特定の形式の SAML アサーションを受け取るため、SAML トークン属性の構成にカスタム属性マッピングを追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/edit-attribute.png)

1. その他に、Adobe Creative Cloud アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性|
    |----- | --------- |
    | FirstName | User.givenname |
    | LastName | User.surname |
    | Email | User.mail |

    > [!NOTE]
    > ユーザーは、電子メール要求の値を、SAML 応答で設定するために、 有効な Office 365 ExO ライセンス　を持つ必要があります。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[Federation Data XML]\(フェデレーション データ XML\)** を見つけて **[ダウンロード]** を選択し、XML メタデータ ファイルをダウンロードしてコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Adobe Creative Cloud のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Adobe Creative Cloud へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Adobe Creative Cloud]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-adobe-creative-cloud-sso"></a>Adobe Creative Cloud の SSO の構成

1. 別の Web ブラウザー ウィンドウで、[Adobe Admin Console](https://adminconsole.adobe.com) にシステム管理者としてログインします。

1. 上部のナビゲーション バーの **[Settings]\(設定\)** に移動し、 **[Identity]\(ID\)** を選択します。 ディレクトリの一覧が開きます。 必要なフェデレーション ディレクトリを選択します。

1. **[Directory Details]\(ディレクトリの詳細\)** ページで、 **[Configure]\(構成\)** を選択します。

1. エンティティ ID と ACS URL (Assertion Consumer Service URL、つまり応答 URL) をコピーします。 Azure portal の適切なフィールドに URL を入力します。

    ![アプリ側でのシングル サインオンの構成](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. **[アプリケーション設定の構成]** ダイアログの **[識別子]** として、Adobe によって提供されたエンティティ ID 値を使用します。

    b. **[アプリケーション設定の構成]** ダイアログの **[応答 URL]** として、Adobe によって提供された ACS URL (Assertion Consumer Service URL) 値を使用します。

1. ページの下部で、Azure portal からダウンロードした**フェデレーション データ XML** ファイルをアップロードします。 

    ![フェデレーション データ XML ファイル](https://helpx.adobe.com/content/dam/help/en/enterprise/kb/configure-microsoft-azure-with-adobe-sso/jcr_content/main-pars/procedure/proc_par/step_228106403/step_par/image_copy/saml_signinig_certificate.png "IdP メタデータ XML")

1. **[保存]** を選択します。


### <a name="create-adobe-creative-cloud-test-user"></a>Adobe Creative Cloud のテスト ユーザーの作成

Azure AD ユーザーが Adobe Creative Cloud にサインインできるようにするには、そのユーザーを Adobe Creative Cloud にプロビジョニングする必要があります。 Adobe Creative Cloud の場合、プロビジョニングは手動で実行します。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順に従います。

1. [Adobe Admin Console](https://adminconsole.adobe.com) サイトに管理者としてサインインします。

2. Adobe のコンソール内でフェデレーション ID としてユーザーを追加し、製品のプロファイルに割り当てます。 ユーザーの追加の詳細については、「[Add users in Adobe Admin Console (Adobe Admin Console でのユーザーの追加)](https://helpx.adobe.com/enterprise/using/users.html#Addusers)」を参照してください。 

3. ここで、Adobe サインイン フォームにメール アドレス/UPN を入力し、Tab キーを押すと、Azure AD にフェデレーションされます。
   * Web アクセス: www\.adobe.com > サインイン
   * デスクトップ アプリ ユーティリティ内 > サインイン
   * アプリケーション内 > ヘルプ > サインイン

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Adobe Creative Cloud] タイルをクリックすると、SSO を設定した Adobe Creative Cloud に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で Adobe Creative Cloud を試す](https://aad.portal.azure.com/)

- [ID を設定する (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-identity.html)
  
- [Adobe SSO で使用するために Azure を構成する (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)

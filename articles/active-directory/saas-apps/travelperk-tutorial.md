---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と TravelPerk の統合 | Microsoft Docs
description: Azure Active Directory と TravelPerk の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/23/2020
ms.author: jeedes
ms.openlocfilehash: eefc47e1308dc5b59d8b57d7481a5250a7d2af93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99475745"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-travelperk"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と TravelPerk の統合

このチュートリアルでは、TravelPerk と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と TravelPerk を統合すると、次のことができるようになります。

* TravelPerk にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して TravelPerk に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Premium サブスクリプションのある TravelPerk アカウント。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* TravelPerk では、**SP** Initiated SSO がサポートされます

* TravelPerk では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-travelperk-from-the-gallery"></a>ギャラリーから TravelPerk を追加する

Azure AD への TravelPerk の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に TravelPerk を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**TravelPerk**」と入力します。
1. 結果のパネルから **[TravelPerk]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-travelperk"></a>TravelPerk の Azure AD SSO を構成してテストする

**B.Simon** というテスト ユーザーを使用して、TravelPerk に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと TravelPerk の関連ユーザーとの間にリンク関係を確立する必要があります。

TravelPerk に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[TravelPerk の SSO の構成](#configure-travelperk-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[TravelPerk テスト ユーザーの作成](#create-travelperk-test-user)** - TravelPerk で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **TravelPerk** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<COMPANY>.travelperk.com/`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<COMPANY>.travelperk.com/accounts/saml2/metadata/<APPLICATION_ID>`

    c. **[応答 URL]** ボックスに、`https://<COMPANY>.travelperk.com/accounts/saml2/callback/<APPLICATION_ID>/?acs` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際のサインオン URL、応答 URL、識別子で更新してください。 この値は、TravelPerk アカウント内で、 **[Company Settings]\(会社の設定\)**  >  **[Integrations]\(統合\)**  >  **[Single Sign On]\(シングル サインオン\)** に移動して確認できます。 サポートについては、[TravelPerk ヘルプ センター](https://support.travelperk.com/hc/en-us/articles/360052450271-How-can-I-setup-SSO-for-Azure-SAML-)にアクセスしてください。

1. TravelPerk アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。 既定のマッピングでは、**emailaddress** は **user. mail** にマップされます。 ただし、TravelPerk アプリケーションでは、**emailaddress** を **user.userprincipalname** にマップする必要があります。 TravelPerk の場合、属性マッピングを編集する必要があります。 **[Edit]\(編集\)** アイコンをクリックし、この属性マッピングを変更します。 属性を編集するには、属性をクリックして編集モードを開きます。

    ![image](common/default-attributes.png)

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[TravelPerk のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に TravelPerk へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[TravelPerk]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-travelperk-sso"></a>TravelPerk の SSO の構成

**TravelPerk** 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** と Azure portal からコピーした適切な URL を [TravelPerk のサポート チーム](mailto:trex@travelperk.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-travelperk-test-user"></a>TravelPerk テスト ユーザーの作成

このセクションでは、B.Simon というユーザーを TravelPerk に作成します。 TravelPerk では、Just-In-Time プロビジョニングがサポートされており、これは既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ TravelPerk に存在していない場合は、TravelPerk にアクセスしようとしたときに新しいユーザーが作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

1. Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる TravelPerk のサインオン URL にリダイレクトされます。 

2. TravelPerk のサインオン URL に直接移動し、そこからログイン フローを開始します。

3. Microsoft アクセス パネルを使用することができます。 アクセス パネルで [TravelPerk] タイルをクリックすると、TravelPerk サインオン URL にリダイレクトされます。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

TravelPerk を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

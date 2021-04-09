---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と RSA Archer Suite の統合 | Microsoft Docs
description: Azure Active Directory と RSA Archer Suite の間でシングル サインオンを構成する方法について学習します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2020
ms.author: jeedes
ms.openlocfilehash: 397d59c60ed90e0e25df671baa3d46660eff1d47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181579"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-rsa-archer-suite"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と RSA Archer Suite の統合

このチュートリアルでは、RSA Archer Suite を Azure Active Directory (Azure AD) と統合する方法について学習します。 RSA Archer Suite を Azure AD と統合すると、次のことができます。

* RSA Archer Suite にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して、RSA Archer Suite に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* RSA Archer Suite でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* RSA Archer Suite では、**SP** Initiated SSO がサポートされています
* RSA Archer Suite では、**Just-In-Time** ユーザー プロビジョニングがサポートされています

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-rsa-archer-suite-from-the-gallery"></a>ギャラリーからの RSA Archer Suite の追加

Azure AD への RSA Archer Suite の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に RSA Archer Suite を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**RSA Archer Suite**」と入力します。
1. 結果のパネルから **[RSA Archer Suite]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-rsa-archer-suite"></a>RSA Archer Suite に対して Azure AD SSO を構成してテストする

**B.Simon** というテスト ユーザーを使用して、RSA Archer Suite に対して Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと RSA Archer Suite の関連ユーザーとの間にリンク関係を確立する必要があります。

RSA Archer Suite に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[RSA Archer Suite の SSO を構成する](#configure-rsa-archer-suite-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[RSA Archer Suite のテスト ユーザーを作成する](#create-rsa-archer-suite-test-user)** - RSA Archer Suite で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **RSA Archer Suite** アプリケーション統合ページで、 **[管理]** セクションを見つけ、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<BASE_URL>/default.aspx?IDP=<REALM_NAME>`

    b. **[識別子 (エンティティ ID)]** ボックスに、`RSAArcherSuite_TENANT_STRING` という値を入力します。

    > [!NOTE]
    > サインオン URL は実際の値ではありません。 実際のサインオン URL で値を更新する必要があります。 この値を取得する場合は、[RSA Archer Suite クライアント サポート チーム](mailto:archersupport@rsa.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. RSA Archer Suite アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、RSA Archer Suite アプリケーションでは、以下のような、いくつかの属性が SAML 応答で返されることが想定されています。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 |  ソース属性|
    | -------------- | --------- |
    | FirstName | User.givenname |
    | LastName | User.surname |
    | PhoneNumber | user.telephonenumber |
    | City | user.city |
    | 郵便番号 | user.postalcode |
    | State | user.state |
    | Street | user.streetaddress |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **Set up RSA Archer Suite\(RSA Archer Suite の設定\)** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に RSA Archer Suite へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[RSA Archer Suite]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-rsa-archer-suite-sso"></a>RSA Archer Suite の SSO を構成する

1. 管理者として、別のブラウザーで RSA Archer Suite の Web サイトにサインインします。

1. 次のページで、以下の手順を実行します。

    ![RSA Archer Suite の SSO を構成する](./media/rsa-archer-suite-tutorial/configuring-saml-sso.png)

    a. **[シングル サインオン]** タブに移動し、ドロップダウンから **[シングル サインオン モード]** として **[SAML]** を選択します。

    b. **[Allow manual bypass]\(手動バイパスを許可する\)** チェックボックスをオンにします。

    c. **[Instance Entity ID]\(インスタンス エンティティ ID\)** テキストボックスに、有効な名前を入力します。

    d. **拇印の値** を、 **[証明書の拇印]** テキストボックスに貼り付けます。

    e. **[選択]** ボタンをクリックし、Azure portal からダウンロードした **フェデレーション メタデータ XML** ファイルをアップロードします。

    f. シングル サインオンの設定を **保存** します。 

### <a name="create-rsa-archer-suite-test-user"></a>RSA Archer Suite のテスト ユーザーを作成する

このセクションでは、B. Simon というユーザーを RSA Archer Suite に作成します。 RSA Archer Suite では、Just-In-Time ユーザー プロビジョニングがサポートされており、これは既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 RSA Archer Suite にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

1. Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる RSA Archer Suite のサインオン URL にリダイレクトされます。 

2. RSA Archer Suite のサインオン URL に直接移動し、そこからログイン フローを開始します。

3. Microsoft アクセス パネルを使用することができます。 アクセス パネルで [RSA Archer Suite] タイルをクリックすると、SSO を設定した RSA Archer Suite に自動的にサインインされるはずです。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

 RSA Archer Suite を構成したら、組織の機微なデータを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
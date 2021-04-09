---
title: 'チュートリアル: Azure Active Directory と Autotask Workplace の統合 | Microsoft Docs'
description: Azure Active Directory と Autotask Workplace の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: jeedes
ms.openlocfilehash: 1a2602ee3b8fad6e87a778ab966f7cb1f8aad8c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101649956"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>チュートリアル: Azure Active Directory と Autotask Workplace の統合

このチュートリアルでは、Autotask Workplace と Azure Active Directory (Azure AD) を統合する方法について説明します。 Autotask Workplace を Azure AD と統合すると、次のことができます。

* Autotask Workplace にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Autotask Workplace に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Autotask Workplace でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Autotask Workplace では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます

## <a name="add-autotask-workplace-from-the-gallery"></a>ギャラリーからの Autotask Workplace の追加

Azure AD への Autotask Workplace の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Autotask Workplace を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Autotask Workplace**」と入力します。
1. 結果のパネルから **[Autotask Workplace]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-autotask-workplace"></a>Autotask Workplace の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Autotask Workplace に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Autotask Workplace の関連ユーザーとの間にリンク関係を確立する必要があります。

Autotask Workplace に対する Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Autotask Workplace の SSO の構成](#configure-autotask-workplace-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Autotask Workplace のテスト ユーザーの作成](#create-autotask-workplace-test-user)** - Autotask Workplace で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Autotask Workplace** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![このスクリーンショットは、[基本的な SAML 構成] を示しています。ここで、識別子と応答 U R L を入力し、[保存] を選択できます。](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![このスクリーンショットは、[追加の U R L を設定します] を示しています。ここで、サインオン U R L を入力できます。](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、`https://<subdomain>.awp.autotask.net/loginsso` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Autotask Workplace クライアント サポート チーム](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

7. **[Autotask Workplace のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

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

このセクションでは、Autotask Workplace へのアクセスを許可することで、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Autotask Workplace]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-autotask-workplace-sso"></a>Autotask Workplace の SSO の構成

1. 別の Web ブラウザー ウィンドウで、管理者の資格情報を使って Workplace Online にログインします。

    > [!Note]
    > IdP を構成するときは、サブドメインを指定する必要があります。 正しいサブドメインを確認するには、Workplace Online にログインします。 ログインした後、URL のサブドメインを書き留めます。 サブドメインは "https://" と ".awp.autotask.net/" の間の部分であり、us、eu、ca、au のいずれかでなければなりません。

2. **[Configuration]\(構成\)**  >  **[Single Sign-On]\(シングル サインオン\)** に移動し、以下の手順を実行します。

    ![Autotask のシングル サインオンの構成](./media/autotaskworkplace-tutorial/configuration-1.png)

    a. **[XML Metadata File]\(XML メタデータ ファイル\)** オプションを選び、Azure portal からダウンロードした **フェデレーション メタデータ XML** をアップロードします。

    b. **[ENABLE SSO]\(SSO を有効にする\)** をクリックします。

    ![Autotask シングル サインオン承認の構成](./media/autotaskworkplace-tutorial/configuration-2.png)

    c. **[I confirm this information is correct and I trust this IdP]\(この情報が正しいことを確認し、この IdP を信頼します\)** チェック ボックスをオンにします。

    d. **[APPROVE]\(承認\)** をクリックします。

> [!Note]
> Autotask Workplace の構成でサポートを必要とする場合は、[こちらのページ](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm)を参照して、Workplace アカウントでサポートを受けてください。

### <a name="create-autotask-workplace-test-user"></a>Autotask Workplace のテスト ユーザーの作成

このセクションでは、Autotask Workplace に Britta Simon というユーザーを作成します。 [Autotask Workplace サポート チーム](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm)と連携し、Autotask Workplace プラットフォームにユーザーを追加してください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始する Autotask Workplace のサインオン URL にリダイレクトされます。  

* Autotask Workplace のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Autotask Workplace に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Autotask Workplace] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Autotask Workplace に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Autotask Workplace を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Pipedrive の統合 | Microsoft Docs
description: Azure Active Directory と Pipedrive の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 96abe2209d4298f23c47c62fec970430f69cbaf6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101654427"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pipedrive"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Pipedrive の統合

このチュートリアルでは、Pipedrive と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Pipedrive を統合すると、次のことができます。

* Pipedrive にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Pipedrive に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Pipedrive でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Pipedrive では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます

## <a name="add-pipedrive-from-the-gallery"></a>ギャラリーからの Pipedrive の追加

Azure AD への Pipedrive の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Pipedrive を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Pipedrive**」と入力します。
1. 結果パネルから **[Pipedrive]** を選択し、そのアプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-pipedrive"></a>Pipedrive の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Pipedrive に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Pipedrive の関連ユーザーとの間にリンク関係を確立する必要があります。

Pipedrive に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Pipedrive SSO の構成](#configure-pipedrive-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Pipedrive テスト ユーザーの作成](#create-pipedrive-test-user)** - Pipedrive で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Pipedrive** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp/metadata.xml` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<COMPANY-NAME>.pipedrive.com/` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Pipedrive クライアント サポート チーム](mailto:support@pipedrive.com)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. Pipedrive アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、Pipedrive アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性|
    | ------------ | --------- |
    | email | User.mail |

1. **[SAML によるシングル サインオンのセットアップ]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして自分のコンピューターに保存します。また、 **[アプリのフェデレーション メタデータ URL]** をコピーして自分のコンピューターに保存します。

    ![証明書のダウンロードのリンク](./media/pipedrive-tutorial/certificate-data.png)

1. **[Pipedrive のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B. Simon に Pipedrive へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Pipedrive]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-pipedrive-sso"></a>Pipedrive SSO の構成

1. 別のブラウザー ウィンドウで、Pipedrive Web サイトに管理者としてサインインします。

1. **[User Profile]\(ユーザー プロファイル\)** をクリックし、 **[Settings]\(設定\)** を選択します。

    ![スクリーンショットは、[User Profile]\(ユーザー プロファイル\) メニューの [Settings]\(設定\) が選択されていることを示しています。](./media/pipedrive-tutorial/configure-1.png)

1. [SECURITY CENTER] まで下にスクロールし、 **[Single sign-on]\(シングル サインオン\)** を選択します。

    ![スクリーンショットは、[Security Center]\(セキュリティ センター\) で [Single sign-on]\(シングル サインオン\) が選択されていることを示しています。](./media/pipedrive-tutorial/configure-2.png)

1. **[SAML configuration for Pipedrive]\(Pipedrive の SAML 構成\)** セクションで、次の手順に従います。

    ![スクリーンショットは、[S A M L configuration for Pipedrive]\(Pipedrive の S A M L 構成\) セクションですべてのテキストボックスが強調表示されていることを示しています。](./media/pipedrive-tutorial/configure-3.png)

    a. **[Issuer]\(発行者\)** ボックスに、Azure portal からコピーした **[アプリのフェデレーション メタデータ URL]** の値を貼り付けます。

    b. **[Single Sign On(SSO) url]\(シングル サインオン (SSO) URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    c. **[Single Log Out(SLO) url]\(シングル ログアウト (SLO) URL\)** ボックスに、Azure portal からコピーした **[ログアウト URL]** の値を貼り付けます。

    d. **[x.509 certificate]\(x.509 証明書\)** ボックスで、Azure portal からダウンロードした **証明書 (Base64)** ファイルをメモ帳で開き、その内容をコピーして、 **[x.509 certificate]\(x.509 証明書\)** に貼り付けて、変更を保存します。

### <a name="create-pipedrive-test-user"></a>Pipedrive テスト ユーザーの作成

1. 別のブラウザー ウィンドウで、Pipedrive Web サイトに管理者としてサインインします。

1. [COMPANY] まで下にスクロールし、 **[Manage users]\(ユーザーの管理\)** を選択します。

    ![スクリーンショットは、[Company]\(会社\) メニューで [Manage users]\(ユーザーの管理\) が選択されていることを示しています。](./media/pipedrive-tutorial/user-1.png)

1. **[Add users]\(ユーザーの追加)** をクリックします。
    
    ![スクリーンショットは、[Manage users]\(ユーザーの管理\) ページの右側にある [Add users]\(ユーザーの追加\) ボタンが選択されていることを示しています。](./media/pipedrive-tutorial/user-2.png)

1. **[Manage users]\(ユーザーの管理\)** セクションで、次の手順を実行します。

    ![Pipedrive の構成](./media/pipedrive-tutorial/user-3.png)

    a. **[Email]\(電子メール\)** テキスト ボックスに、ユーザーのメール アドレスを入力します (`B.Simon@contoso.com` など)。

    b. **[First name]\(名\)** ボックスに、ユーザーの名前 (名) を入力します。

    c. **[Last name]\(姓\)** ボックスに、ユーザーの姓を入力します。

    d. **Confirm and invite users\(ユーザーを確認して招待\)** をクリックします。

## <a name="test-sso"></a>SSO のテスト 


このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Pipedrive のサインオン URL にリダイレクトされます。  

* Pipedrive のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Pipedrive に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Pipedrive] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Pipedrive に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Pipedrive を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と Broadcom DX SaaS の統合 | Microsoft Docs'
description: Azure Active Directory と Broadcom DX SaaS の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/19/2021
ms.author: jeedes
ms.openlocfilehash: 5aa13598133b55547484609fc6d26d9375c04045
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102053643"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-broadcom-dx-saas"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と Broadcom DX SaaS の統合

このチュートリアルでは、Broadcom DX SaaS と Azure Active Directory (Azure AD) を統合する方法について説明します。 Broadcom DX SaaS を Azure AD と統合すると、次のことが可能になります。

* Broadcom DX SaaS にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Broadcom DX SaaS に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Broadcom DX SaaS サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Broadcom DX SaaS では、**IDP** Initiated SSO がサポートされます。

* Broadcom DX SaaS では、**Just In Time** ユーザー プロビジョニングがサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-broadcom-dx-saas-from-the-gallery"></a>ギャラリーからの Broadcom DX SaaS の追加

Azure AD への Broadcom DX SaaS の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Broadcom DX SaaS を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Broadcom DX SaaS**」と入力します。
1. 結果パネルから **[Broadcom DX SaaS]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-broadcom-dx-saas"></a>Broadcom DX SaaS の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Broadcom DX SaaS に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと Broadcom DX SaaS の関連ユーザーとの間にリンク関係を確立する必要があります。

Broadcom DX SaaS に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Broadcom DX SaaS の SSO の構成](#configure-broadcom-dx-saas-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Broadcom DX SaaS のテスト ユーザーの作成](#create-broadcom-dx-saas-test-user)** - Broadcom DX SaaS で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Broadcom DX SaaS** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[SAML でシングル サインオンをセットアップします]** ページで、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`DXI_<TENANT_NAME>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://axa.dxi-na1.saas.broadcom.com/ess/authn/<TENANT_NAME>` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[Broadcom DX SaaS クライアント サポート チーム](mailto:dxi-na1@saas.broadcom.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. Broadcom DX SaaS アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. 上記に加えて、Broadcom DX SaaS アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 |  ソース属性|
    | --------------- | --------- |
    | グループ | user.groups |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Broadcom DX SaaS のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Broadcom DX SaaS へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Broadcom DX SaaS]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-broadcom-dx-saas-sso"></a>Broadcom DX SaaS の SSO の構成

1. Broadcom DX SaaS 企業サイトに管理者としてログインします。

2. **[Settings]\(設定\)** に移動し、 **[Users]\(ユーザー\)** タブをクリックします。

    ![ユーザー](./media/broadcom-dx-saas-tutorial/settings.png "ユーザー")

3. **[User management]\(ユーザー管理\)** セクションで、省略記号をクリックし、 **[SAML]** を選択します。

    ![ユーザー管理](./media/broadcom-dx-saas-tutorial/local.png "[ユーザー管理]")

4. **[Identify SAML account]\(SAML アカウントの識別\)** セクションで、次の手順を実行します。
   
    ![アカウント](./media/broadcom-dx-saas-tutorial/broadcom-1.png "Account") 

    a. **[発行者]** ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    b. **[Identity Provider (IDP) Login URL]\(ID プロバイダー (IDP) のログイン URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    c. **[Identity Provider (IDP) Logout URL]\(ID プロバイダー (IDP) のログアウト URL\)** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    d. Azure portal からダウンロードした **証明書 (Base64)** をメモ帳で開き、その内容を **[Identity provider certificate]\(ID プロバイダー証明書\)** ボックスに貼り付けます。

    e. **[NEXT]\(次へ\)** をクリックします。

5. **[Map attributes]\(属性のマッピング\)** セクションで、必要な SAML 属性を次のページに入力し、 **[NEXT]\(次へ\)** をクリックします。

    ![属性](./media/broadcom-dx-saas-tutorial/broadcom-2.png "属性") 


6. **[Identify user group]\(ユーザー グループの識別\)** セクションで、そのグループのオブジェクト ID を入力し、 **[NEXT]\(次へ\)** をクリックします。

    ![グループの追加](./media/broadcom-dx-saas-tutorial/broadcom-3.png "グループの追加") 

7. **[Populate SAML Account]\(SAML アカウントの設定\)** セクションで、設定された値を確認し、 **[SAVE]\(保存\)** をクリックします。

    ![SAML アカウント](./media/broadcom-dx-saas-tutorial/broadcom-4.png "SAML アカウント") 

### <a name="create-broadcom-dx-saas-test-user"></a>Broadcom DX SaaS のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Broadcom DX SaaS に作成します。 Broadcom DX SaaS では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Broadcom DX SaaS にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Broadcom DX SaaS に自動的にサインインされます。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Broadcom DX SaaS] タイルをクリックすると、SSO を設定した Broadcom DX SaaS に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

Broadcom DX SaaS を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。

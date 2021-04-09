---
title: チュートリアル:Azure Active Directory と Zscaler Internet Access Administrator の統合 | Microsoft Docs
description: Azure Active Directory と Zscaler Internet Access Administrator の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: jeedes
ms.openlocfilehash: 70afa0a02f4e303105aec1884b966796854c6f49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102449325"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>チュートリアル:Azure Active Directory と Zscaler Internet Access Administrator の統合

このチュートリアルでは、Zscaler Internet Access Administrator と Azure Active Directory (Azure AD) を統合する方法について説明します。 Zscaler Internet Access Administrator を Azure AD と統合すると、次のことが可能になります。

* Zscaler Internet Access Administrator にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して、Zscaler Internet Access Administrator に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Zscaler Internet Access Administrator でのシングル サインオン (SSO) が有効なサブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Zscaler Internet Access Administrator では、**IDP** Initiated SSO がサポートされます。

## <a name="add-zscaler-internet-access-administrator-from-the-gallery"></a>ギャラリーからの Zscaler Internet Access Administrator の追加

Azure AD への Zscaler Internet Access Administrator の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Zscaler Internet Access Administrator を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Zscaler Internet Access Administrator**」と入力します。
1. 結果パネルから **[Zscaler Internet Access Administrator]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-zscaler-internet-access-administrator"></a>Zscaler Internet Access Administrator の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Zscaler Internet Access Administrator に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと Zscaler Internet Access Administrator の関連ユーザーとの間にリンク関係を確立する必要があります。

Zscaler Internet Access Administrator に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[Zscaler Internet Access Administrator の SSO の構成](#configure-zscaler-internet-access-administrator-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Zscaler Internet Access Administrator のテスト ユーザーの作成](#create-zscaler-internet-access-administrator-test-user)** - Zscaler Internet Access Administrator で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
3. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Zscaler Internet Access Administrator** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[識別子]** テキスト ボックスに、要件に応じて次のいずれかの URL を入力します。

    | 識別子 |
    |------------|
    | `https://admin.zscaler.net` |
    | `https://admin.zscalerone.net` |
    | `https://admin.zscalertwo.net` |
    | `https://admin.zscalerthree.net` |
    | `https://admin.zscloud.net` |
    | `https://admin.zscalerbeta.net` |

    b. **[応答 URL]** テキスト ボックスに、要件に応じて次のいずれかの URL を入力します。

    | [応答 URL] |
    |-----------|
    | `https://admin.zscaler.net/adminsso.do` |
    | `https://admin.zscalerone.net/adminsso.do` |
    | `https://admin.zscalertwo.net/adminsso.do` |
    | `https://admin.zscalerthree.net/adminsso.do` |
    | `https://admin.zscloud.net/adminsso.do` |
    | `https://admin.zscalerbeta.net/adminsso.do` |

5. Zscaler Internet Access Administrator アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性と要求]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** ボタンをクリックして **[ユーザー属性と要求]** ダイアログを開きます。

    ![属性リンク](./media/zscaler-internet-access-administrator-tutorial/attributes.png)

6. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、上の図のように SAML トークン属性を構成し、次の手順を実行します。

    | 名前  | ソース属性  |
    | ---------| ------------ |
    | Role | user.assignedroles |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    b. **[ソース属性]** の一覧から、属性値を選択します。

    c. **[OK]** をクリックします。

    d. **[保存]** をクリックします。

    > [!NOTE]
    > Azure AD でロールを構成する方法については、[こちら](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)をクリックしてください。

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

8. **[Zscaler Internet Access Administrator のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

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

このセクションでは、B.Simon に Zscaler Internet Access Administrator へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[Zscaler Internet Access Administrator]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. 前述のロールを設定した場合、 **[ロールの選択]** ボックスの一覧からそれを選択できます。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-zscaler-internet-access-administrator-sso"></a>Zscaler Internet Access Administrator の SSO の構成

1. 別の Web ブラウザー ウィンドウで、Zscaler Internet Access の管理 UI にログインします。

2. **[Administration]\(管理\) > [Administrator Management]\(管理者の管理\)** に移動し、次の手順を実行して、[Save]\(保存\) をクリックします。

    ![[Administrator Management]\(管理者の管理\) を示すスクリーンショット。S A M L 認証を有効にするオプション、S S L 証明書をアップロードするオプション、および発行者を指定するオプションが表示されています。](./media/zscaler-internet-access-administrator-tutorial/management.png "管理")

    a. **[Enable SAML Authentication]\(SAML 認証を有効にする\)** をオンにします。

    b. **アップロード** をクリックして、Azure portal からダウンロードした Azure SAML 署名証明書を **Public SSL Certificate\(パブリック SSL 証明書\)** にアップロードします。

    c. セキュリティを強化するために、必要に応じて **[Issuer]\(発行者\)** の詳細を追加して、SAML 応答の発行者を確認します。

3. 管理 UI で次の手順を実行します。

    ![管理 U I を示すスクリーンショット。ここでは、次の手順を実行できます。](./media/zscaler-internet-access-administrator-tutorial/activation.png)

    a. 左下の **[Activation]\(アクティブ化\)** メニューにポインターを置きます。

    b. **[アクティブ化]** をクリックします。

### <a name="create-zscaler-internet-access-administrator-test-user"></a>Zscaler Internet Access Administrator のテスト ユーザーの作成

このセクションの目的は、Zscaler Internet Access Administrator で Britta Simon というユーザーを作成することです。 Zscaler Internet Access では、Administrator SSO の Just-In-Time プロビジョニングはサポートされません。 管理者アカウントは、手動で作成する必要があります。
管理者アカウントを作成する手順については、Zscaler のドキュメントを参照してください。

https://help.zscaler.com/zia/adding-admins

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Zscaler Internet Access Administrator に自動的にサインインされます

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Zscaler Internet Access Administrator] タイルをクリックすると、SSO を設定した Zscaler Internet Access Administrator に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Zscaler Internet Access Administrator を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

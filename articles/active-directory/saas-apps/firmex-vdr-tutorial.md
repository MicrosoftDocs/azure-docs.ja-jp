---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と Firmex VDR の統合 | Microsoft Docs'
description: Azure Active Directory と Firmex VDR の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2020
ms.author: jeedes
ms.openlocfilehash: 6dbd39b5c56192ad2ca957c5500338b50e8c8963
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92453386"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-firmex-vdr"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と Firmex VDR の統合

このチュートリアルでは、Firmex VDR と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Firmex VDR を統合すると、次のことができます。

* Firmex VDR にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Firmex VDR に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Firmex VDR でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Firmex VDR では、**SP および IDP** Initiated SSO がサポートされます

* Firmex を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を強制することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-firmex-vdr-from-the-gallery"></a>ギャラリーからの Firmex VDR の追加

Azure AD への Firmex VDR の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Firmex VDR を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Firmex VDR**」と入力します。
1. 結果のパネルから **[Firmex VDR]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-firmex-vdr"></a>Firmex VDR の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Firmex VDR に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Firmex VDR の関連ユーザーとの間にリンク関係を確立する必要があります。

Firmex VDR に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Firmex VDR の SSO の構成](#configure-firmex-vdr-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Firmex VDR のテスト ユーザーの作成](#create-firmex-vdr-test-user)** - Firmex VDR 内で B.Simon に対応するユーザーを作成し、Azure AD 上の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Firmex VDR** アプリケーション統合ページで、**[管理]** セクションを見つけて、**[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. アプリは Azure と事前に統合済みであるため、 **[基本的な SAML 構成]** セクションで実行が必要な手順はありません。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** テキスト ボックスに、URL として「`https://login.firmex.com`」と入力します。

1. **[保存]** をクリックします。

1. Firmex VDR アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、Firmex VDR アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性|
    | ------------ | --------- |
    | email | User.mail |

1. **[SAML によるシングル サインオンのセットアップ]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Firmex VDR のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Firmex VDR へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Firmex VDR]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-firmex-vdr-sso"></a>Firmex VDR SSO の構成

### <a name="before-you-get-started"></a>開始する前に

#### <a name="what-youll-need"></a>必要なもの

-   アクティブな Firmex サブスクリプション
-   SSO サービスとしての Azure AD
-   SSO を構成する IT 管理者
-   SSO が有効になったら、自社のすべてのユーザーは、ログインとパスワードではなく SSO を使用して Firmex にログインする必要があります。

#### <a name="how-long-will-this-take"></a>所要時間

SSO の実装には数分かかります。 Firmex サポートがサイトの SSO を有効にしてから、自社のユーザーが SSO を使用して認証を行えるようになるまでの間に、実質的にダウンタイムはありません。 以下の手順に従ってください。

### <a name="step-1---identify-your-companys-domains"></a>手順 1 - 自社のドメインを特定する

自社のユーザーがログインに使用するドメインを特定します。

次に例を示します。

- @firmex.com
- @firmex.ca

### <a name="step-2---contact-firmex-support-with-your-domains"></a>手順 2 - ドメインを Firmex サポートに連絡する

[Firmex サポート チーム](mailto:support@firmex.com)にメールを送信するか、1888 688 4042 x.11 に電話して、Firmex サポートと連絡を取ります。 ご自分のドメイン情報を伝えてください。 Firmex サポートによって、**要求されたドメイン** として、ご使用の VDR にドメインが追加されます。 これで、管理者が SSO を構成できます。

警告: 要求されたドメインをサイト管理者が構成するまで、自社のユーザーは VDR にログインできません。 会社以外のユーザー (つまり、ゲスト ユーザー) は、引き続きメールとパスワードを使用してログインできます。 構成には数分かかります。

### <a name="step-3---configure-the-claimed-domains"></a>手順 3 - 要求されたドメインを構成する

1. サイト管理者として Firmex にログインします。
1. 左上隅にある自社のロゴをクリックします。
1. **[SSO]** タブを選択します。次に、 **[SSO Configuration]\(SSO の構成\)** を選択します。 構成するドメインをクリックします。

    ![要求されたドメイン](./media/firmex-vdr-tutorial/edit-sso.png)  

1. 次のフィールドの入力を行うよう IT 管理者に依頼します。 フィールドに入力する内容は、ID プロバイダーから取得する必要があります。  

    ![[SSO Configuration]](./media/firmex-vdr-tutorial/SSO-config.png)

    a. **[エンティティ ID]** ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    b. **[Identity Provider URL]\(ID プロバイダーの URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    c. **[Public Key Certificate]\(公開キー証明書\)** - SAML メッセージは、認証を目的として発行者によってデジタル署名される場合があります。 メッセージの署名を検証するために、メッセージの受信者は、発行者のものだとわかっている公開キーを使用します。 同様に、メッセージを暗号化するには、最終的な受信者のものである公開暗号化キーを発行者が知っている必要があります。 署名と暗号化のどちらの場面でも、信頼された公開キーを事前に共有する必要があります。  これは、**フェデレーション メタデータ XML** からの **X509Certificate** です

    d. **[Save]\(保存\)** をクリックして、SSO 構成を完了します。 変更はすぐに有効になります。

1. この時点で、サイトに対して SSO が有効になっています。

### <a name="create-firmex-vdr-test-user"></a>Firmex VDR テスト ユーザーの作成

このセクションでは、Firmex で B.Simon というユーザーを作成します。 [Firmex サポート チーム](mailto:support@firmex.com)と協力して、Firmex プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルの [Firmex VDR] タイルをクリックすると、SSO を設定した Firmex VDR に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD を使用して Firmex VDR を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって Firmex を保護する方法](/cloud-app-security/proxy-intro-aad)
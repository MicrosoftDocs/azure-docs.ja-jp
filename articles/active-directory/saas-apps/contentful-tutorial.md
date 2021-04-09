---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と Contentful の統合 | Microsoft Docs'
description: Azure Active Directory と Contentful の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: jeedes
ms.openlocfilehash: b664a7e4aac571359fb1494cf69d5bbe3e841b4d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92455319"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-contentful"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と Contentful の統合

このチュートリアルでは、Contentful と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Contentful を統合すると、次のことができます。

* Contentful にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Contentful に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Contentful でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Contentful では、**SP および IDP** Initiated SSO がサポートされます
* Contentful では、**Just-In-Time** ユーザー プロビジョニングがサポートされます
* Contentful を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値です。 1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-contentful-from-the-gallery"></a>ギャラリーからの Contentful の追加

Azure AD への Contentful の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Contentful を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左側のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Contentful**」と入力します。
1. 結果から **[Contentful]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-contentful"></a>Contentful の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Contentful に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Contentful の関連ユーザーの間で、リンク関係を確立する必要があります。

Contentful で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Contentful SSO の構成](#configure-contentful-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Contentful テスト ユーザーの作成](#create-contentful-test-user)** - Contentful で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Contentful** アプリケーション統合ページで、**[管理]** セクションを見つけて、**[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    - **[応答 URL]** テキスト ボックスに、Contentful の SSO 設定ページの ACS (Assertion Consumer Service) URL をコピーします。 次のようになります: `https://be.contentful.com/sso/<organization_id>/consume`

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    - **[サインオン URL]** テキスト ボックスに、同じ ACS (Assertion Consumer Service) URL をコピーします。 次のようになります: `https://be.contentful.com/sso/<organization_id>/login`

    > [!NOTE]
    > これらは実際の値ではありません。 ACS (Assertion Consumer Service) URL を Contentful の SSO 設定ページからコピーして、これらの値を実際の応答 URL とサインオン URL で更新します。

1. Contentful アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、Contentful アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 |  ソース属性|
    | --------------- | --------- |
    | email | user.userprincipalname |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Contentful の設定]** セクションで、ログイン URL をコピーして Contentful SSO を構成します。

    ![構成 URL のコピー](media/contentful-tutorial/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Contentful へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Contentful]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、**[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログ ボックスの [ユーザー] の一覧から **[B.Simon]** を選択し、ページの下部にある **[選択]** ボタンをクリックします。
1. SAML アサーションにロール値が必要な場合は、**[ロールの選択]** ダイアログ ボックスでユーザーに適したロールを一覧から選択し、ページの下部にある **[選択]** ボタンをクリックします。
1. **[割り当ての追加]** ダイアログ ボックスで、 **[割り当て]** ボタンをクリックします。

## <a name="configure-contentful-sso"></a>Contentful SSO の構成

**Contentful** 側でシングル サインオンを構成するには、次の手順に従います。

1. [Contentful](https://app.contentful.com) で、**[組織の設定]** にある SSO の設定ページに移動します。
1. **[SSO の設定]** をクリックします。
1. Azure AD の **[Contentful の設定]** セクションからログイン URL をコピーして貼り付けます。
1. Azure AD からダウンロードした Base64 証明書ファイルから証明書をコピーして貼り付けます。
1. SP によって開始されるログインの SSO 名を設定します。
1. **[Enable SSO]\(SSO を有効にする\)** をクリックします。

うまくいかない場合は、[Contentful サポート チーム](mailto:support@contentful.com)にご連絡ください。

### <a name="create-contentful-test-user"></a>Contentful テスト ユーザーの作成

このセクションでは、B. Simon というユーザーを Contentful に作成します。 Contentful では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Contentful にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Contentful] タイルをクリックすると、SSO を設定した Contentful に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Contentful を試す](https://aad.portal.azure.com/)
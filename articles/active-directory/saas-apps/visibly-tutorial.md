---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Visibly の統合 | Microsoft Docs
description: Azure Active Directory と Visibly の間にシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: jeedes
ms.openlocfilehash: de41e1ef021ea3da2b3a5bef4268469aab448a21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92637940"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-visibly"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Visibly の統合

このチュートリアルでは、Visibly を Azure Active Directory (Azure AD) と統合する方法について説明します。 Visibly を Azure AD と統合すると、次のことができます。

* Visibly にアクセスできるユーザーを Azure AD で制御します。
* ユーザーが Azure AD アカウントを使用して Visibly に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Visibly でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Visibly では、**SP** によって開始される SSO がサポートされています。

* Visibly を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-visibly-from-the-gallery"></a>ギャラリーからの Visibly の追加

Azure AD への Visibly の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Visibly を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Visibly**」と入力します。
1. 結果パネルから **[Visibly]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-visibly"></a>Visibly の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Visibly で Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Visibly の関連ユーザーとの間にリンク関係を確立する必要があります。

Visibly で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Visibly SSO の構成](#configure-visibly-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Visibly テスト ユーザーの作成](#create-visibly-test-user)** - Visibly で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Visibly** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** テキスト ボックスに、URL として「`https://app.visibly.io/`」と入力します。

    b. **[応答 URL]** ボックスに、URL として「`https://api.visibly.io/api/v1/verifyResponse`」と入力します。


1. Visibly アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. 上記に加えて、Visibly アプリケーションでは、次に示す属性が SAML 応答で返されることが想定されています。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 |  ソース属性|
    | ----------- | --------- |
    | city | user.city |
    | lastName | User.surname |
    | state | user.state |
    | department | user.department |
    | email | User.mail |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Visibly のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Visibly へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Visibly]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-visibly-sso"></a>Visibly SSO の構成

1. 資格情報を使用して Visibly にログインします。

1. ナビゲーション メニューから、**設定** オプションに移動します。

    ![設定オプションが選択されていることを示すスクリーンショット。](./media/visibly-tutorial/settings.png)

1. [Settings]\(設定\) 内の **[Integrations]\(統合\)** をクリックします。

    ![[Settings]\(設定\) メニューから [Integrations]\(統合\) が選択されていることを示すスクリーンショット。](./media/visibly-tutorial/integrations.png)

1. **[Integrations]\(統合\)** で **[SSO]** を選択します。

    ![[Integrations]\(統合\) から [S S O] が選択されていることを示すスクリーンショット。](./media/visibly-tutorial/sso.png)

1. 次のページで、以下の手順を実行します。

    ![[S S O Integration]\(S S O 統合ページ\) を示すスクリーンショット。ここで、説明されている値を入力できます。](./media/visibly-tutorial/configuration.png)

    a. **[Entity ID]\(エンティティ ID\)** ボックスに、Azure portal からコピーした **エンティティ ID** の値を貼り付けます。

    b. **[SSO URL]** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    c. **[SSO name]\(SSO 名\)** ボックスに、有効な名前を指定します。

    d. Azure portal からダウンロードした **証明書 (Base64)** をメモ帳で開き、その内容を **[Certificate]\(証明書\)** ボックスに貼り付けます。または、 **[Upload Certificate]\(証明書のアップロード)** を選択して、**証明書** をアップロードすることもできます。

    e. [設定] メニューの **[保存]**

### <a name="create-visibly-test-user"></a>Visibly テスト ユーザーの作成

このセクションでは、B.Simon というユーザーを Visibly に作成します。 Visibly では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Visibly にユーザーがまだ存在していない場合は、認証後に新しいユーザーが作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Visibly] タイルをクリックすると、SSO を設定した Visibly に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Visibly を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)
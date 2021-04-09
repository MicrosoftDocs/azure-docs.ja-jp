---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Fivetran の統合 | Microsoft Docs
description: Azure Active Directory と Fivetran の間でシングル サインオンを構成する方法について学習します。
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
ms.openlocfilehash: 88f6da7e43e269816c7e4ae2fd7bbc3f5e26522b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453095"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fivetran"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Fivetran の統合

このチュートリアルでは、Fivetran を Azure Active Directory (Azure AD) と統合する方法について学習します。 Fivetran を Azure AD と統合すると、次のことができます。

* Fivetran にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して、Fivetran に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Fivetran アカウント。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Fivetran では、**IDP** Initiated SSO がサポートされています
* Fivetran では、**Just-In-Time** ユーザー プロビジョニングがサポートされています

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-fivetran-from-the-gallery"></a>ギャラリーから Fivetran を追加する

Azure AD への Fivetran の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Fivetran を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Fivetran**」と入力します。
1. 結果のパネルから **[Fivetran]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-fivetran"></a>Fivetran に対して Azure AD SSO を構成してテストする

**B.Simon** というテスト ユーザーを使用して、Fivetran に対して Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと Fivetran の関連ユーザーとの間にリンク関係を確立する必要があります。

Fivetran に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Fivetran の SSO を構成する](#configure-fivetran-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Fivetran のテスト ユーザーを作成する](#create-fivetran-test-user)** - Fivetran で B.Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Fivetran** アプリケーション統合ページで、 **[管理]** セクションを見つけ、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションでは、アプリケーションは事前に構成されており、必要な URL は既に Azure で事前に設定されています。 構成を保存するには、 **[保存]** ボタンをクリックします。


1. Fivetran アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、Fivetran アプリケーションでは、以下のような、いくつかの属性が SAML 応答で返されることが想定されています。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 |  ソース属性|
    | -------------- | --------- |
    | FirstName | User.givenname |
    | LastName | User.surname |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Set up Fivetran] (Fivetran の設定)** セクションで、 **[ログイン URL]** と **[Azure AD 識別子]** の値をコピーします。

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

このセクションでは、B.Simon に Fivetran へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Fivetran]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-fivetran-sso"></a>Fivetran の SSO を構成する

このセクションでは、**Fivetran** 側でシングル サインオンを構成します。

1. 別の Web ブラウザーのウィンドウで、アカウント所有者として Fivetran アカウントにサインインします。
1. ウィンドウの左上隅にある矢印を選択し、ドロップダウン リストから **[Manage Account]\(アカウントを管理する\)** を選択します。

   ![[Manage Account]\(アカウントを管理する\) メニュー オプションが選択されている画面のスクリーンショット。](media/fivetran-tutorial/fivetran-1.png)

1. **[設定]** ページの **[SAML Config]\(SAML 構成\)** セクションに移動します。

   ![[SAML Config]\(SAML 構成\) ウィンドウのスクリーンショット。構成オプションが強調表示されています。](media/fivetran-tutorial/fivetran-2.png)

   1. **[SAML 認証を有効にする]** には **[オン]** を選択します。
   1. **[サインオン URL]** に、Azure portal からコピーした **ログイン URL** の値を貼り付けます。
   1. **[Issuer]\(発行者\)** に、Azure portal からコピーした **Azure AD 識別子** の値を貼り付けます。
   1. ダウンロードした証明書ファイルをテキスト エディターで開き、証明書をクリップボードにコピーし、 **[公開証明書]** ボックスに貼り付けます。
   1. **[SAVE CONFIG]\(構成の保存\)** を選択します。

### <a name="create-fivetran-test-user"></a>Fivetran のテスト ユーザーを作成する

このセクションでは、B.Simon というユーザーを Fivetran に作成します。 Fivetran では、Just-In-Time ユーザー プロビジョニングがサポートされており、これは既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Fivetran にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

1. Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Fivetran に自動的にサインインされるはずです 

2. Microsoft アクセス パネルを使用することができます。 アクセス パネルで [Fivetran] タイルをクリックすると、SSO を設定した Fivetran に自動的にサインインされるはずです。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

Fivetran を構成したら、組織の機微なデータを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
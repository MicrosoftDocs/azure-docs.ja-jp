---
title: 'チュートリアル: Azure Active Directory と Coupa の統合 | Microsoft Docs'
description: Azure Active Directory と Coupa の間にシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/14/2021
ms.author: jeedes
ms.openlocfilehash: c6932292b86924cfab6d17c315cbd6946bd63f61
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621331"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>チュートリアル: Azure Active Directory と Coupa の統合

このチュートリアルでは、Coupa と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Coupa を統合すると、次のことができます。

* Coupa にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Coupa に自動的にサインインできるように設定する。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

Coupa と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の試用版を入手できます。
* Coupa でのシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Coupa では、**SP** によって開始される SSO がサポートされます

## <a name="add-coupa-from-the-gallery"></a>ギャラリーからの Coupa の追加

Azure AD への Coupa の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Coupa を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Coupa**」と入力します。
1. 結果のパネルから **[Coupa]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-coupa"></a>Coupa の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Coupa に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Coupa の関連ユーザーとの間にリンク関係を確立する必要があります。

Coupa に対する Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Coupa の SSO の構成](#configure-coupa-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Coupa のテスト ユーザーの作成](#create-coupa-test-user)** - Coupa で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Coupa** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、`https://<companyname>.coupahost.com` という形式で URL を入力します。

    > [!NOTE]
    > サインオン URL は実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Coupa クライアント サポート チーム](https://success.coupa.com/Support/Contact_Us?)にお問い合わせください。

    b. **[識別子]** ボックスに、URL として「」と入力します。

    | 環境  | URL |
    |:-------------|----|
    | サンドボックス | `sso-stg1.coupahost.com`|
    | 運用 | `sso-prd1.coupahost.com`|
    | | |

    c. **[応答 URL]** ボックスに、URL として「」と入力します。

    | 環境 | URL |
    |------------- |----|
    | サンドボックス | `https://sso-stg1.coupahost.com/sp/ACS.saml2`|
    | 運用 | `https://sso-prd1.coupahost.com/sp/ACS.saml2`|
    | | |

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Set up Coupa]\(Coupa の設定\)** セクションで、要件どおりの適切な URL をコピーします。

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

このセクションでは、B.Simon に Coupa へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Coupa]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="configure-coupa-sso"></a>Coupa の SSO の構成

1. Coupa 企業サイトに管理者としてサインオンします。

2. **[Setup]\>[Security Control]** の順に移動します。

    ![セキュリティ制御](./media/coupa-tutorial/ic791900.png "シングル サインオンの設定")

3. **[Coupa 資格情報を使用してログイン]** セクションで、次の手順に従います。

    ![Coupa SP メタデータ](./media/coupa-tutorial/ic791901.png "Coupa SP メタデータ")

    a. **[SAML を使用してログイン]** を選択します。

    b. **[参照]** をクリックして、Azure Portal からダウンロードしたメタデータをアップロードします。

    c. **[保存]** をクリックします。

### <a name="create-coupa-test-user"></a>Coupa テスト ユーザーの作成

Azure AD ユーザーが Coupa にログインできるようにするには、そのユーザーを Coupa にプロビジョニングする必要があります。  

* Coupa の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. 管理者として **Coupa** の会社サイトにログインします。

2. 上部のメニューで **[設定]** をクリックし、**[ユーザー]** をクリックします。

    ![ユーザー](./media/coupa-tutorial/ic791908.png "ユーザー")

3. **Create** をクリックしてください。

    ![ユーザーの作成](./media/coupa-tutorial/ic791909.png "ユーザーの作成")

4. **[ユーザーを作成]** セクションで、次の手順を実行します。

    ![ユーザーの詳細](./media/coupa-tutorial/ic791910.png "[ユーザーの詳細]")

    a. 関連テキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの **[ログイン]**、**[名]**、**[姓]**、**[シングル サインオン ID]**、**[メール]** を入力します。

    b. **Create** をクリックしてください。

    >[!NOTE]
    >Azure Active Directory のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。
    >

>[!NOTE]
>他の Coupa ユーザー アカウント作成ツールや、Coupa から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Coupa のサインオン URL にリダイレクトされます。 

* Coupa のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Coupa] タイルをクリックすると、SSO を設定した Coupa に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Coupa を構成すると、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。
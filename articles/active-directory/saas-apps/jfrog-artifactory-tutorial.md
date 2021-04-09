---
title: チュートリアル:Azure Active Directory と JFrog Artifactory の統合 | Microsoft Docs
description: Azure Active Directory と JFrog Artifactory の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: b943be684d84e1e193d9318e9f1c6423dcd38795
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648927"
---
# <a name="tutorial-integrate-jfrog-artifactory-with-azure-active-directory"></a>チュートリアル:JFrog Artifactory と Azure Active Directory の統合

このチュートリアルでは、JFrog Artifactory と Azure Active Directory (Azure AD) を統合する方法について説明します。 JFrog Artifactory と Azure AD を統合すると、次のことができます。

* JFrog Artifactory にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して JFrog Artifactory に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* JFrog Artifactory でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* JFrog Artifactory では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。
* JFrog Artifactory では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。

## <a name="add-jfrog-artifactory-from-the-gallery"></a>ギャラリーからの JFrog Artifactory の追加

Azure AD への JFrog Artifactory の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に JFrog Artifactory を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**JFrog Artifactory**」と入力します。
1. 結果のパネルから **[JFrog Artifactory]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-jfrog-artifactory"></a>JFrog Artifactory の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、JFrog Artifactory に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと JFrog Artifactory の関連ユーザーとの間にリンク関係を確立する必要があります。

JFrog Artifactory に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[JFrog Artifactory SSO の構成](#configure-jfrog-artifactory-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[JFrog Artifactory のテスト ユーザーの作成](#create-jfrog-artifactory-test-user)** - JFrog Artifactory で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **JFrog Artifactory** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`<servername>.jfrog.io` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。
    
    - Artifactory 6.x の場合: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse`
    - Artifactory 7.x の場合: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。
    - Artifactory 6.x の場合: `https://<servername>.jfrog.io/<servername>/webapp/`
    - Artifactory 7.x の場合: `https://<servername>.jfrog.io/ui/login`

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[JFrog Artifactory Client クライアント サポート チーム](https://support.jfrog.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. JFrog Artifactory アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。 **[編集]** アイコンをクリックして [ユーザー属性] ダイアログを開きます。

    ![編集コントロールが強調表示された [ユーザー属性] 画面のスクリーンショット。](common/edit-attribute.png)

1. これに加え、JFrog Artifactory では、いくつかの属性が SAML 応答で返されることが想定されています。 **[グループ要求 (プレビュー)]** ダイアログの **[ユーザー属性とクレーム]** セクションで、次の手順を実行します。

    a. **[Groups returned in claim]\(要求で返されるグループ\)** の横にある **ペン** をクリックします。

    ![このスクリーンショットは、[編集] アイコンが選択された状態の [User Attributes & Claims]\(ユーザー属性と要求\) を示しています。](./media/jfrog-artifactory-tutorial/configuration-4.png)

    ![[すべてのグループ] が選択されている [Group Claims]\(グループ要求\) セクションを示すスクリーンショット。](./media/jfrog-artifactory-tutorial/configuration-5.png)

    b. ラジオ ボタンのリストから **[すべてのグループ]** を選択します。

    c. **[保存]** をクリックします。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](./media/jfrog-artifactory-tutorial/certificate-base.png)

6. [識別子] フィールド (手順 4. を参照) を使用して、Artifactory (SAML サービス プロバイダー名) を構成します。 **[JFrog Artifactory のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

   - Artifactory 6.x の場合: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse` 
   - Artifactory 7.x の場合: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

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

このセクションでは、B.Simon に JFrog Artifactory へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[JFrog Artifactory]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-jfrog-artifactory-sso"></a>JFrog Artifactory SSO の構成

**JFrog Artifactory** 側でシングル サインオンを構成するには、ダウンロードした **証明書 (未加工)** と Azure portal からコピーした適切な URL を [JFrog Artifactory サポート チーム](https://support.jfrog.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-jfrog-artifactory-test-user"></a>JFrog Artifactory のテスト ユーザーの作成

このセクションでは、B. Simon というユーザーを JFrog Artifactory に作成します。 JFrog Artifactory では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 JFrog Artifactory にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる JFrog Artifactory のサインオン URL にリダイレクトされます。  

* JFrog Artifactory のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した JFrog Artifactory に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [JFrog Artifactory] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した JFrog Artifactory に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

JFrog Artifactory を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
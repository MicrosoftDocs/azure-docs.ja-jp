---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Pulse Secure PCS の統合 | Microsoft Docs
description: Azure Active Directory と Pulse Secure PCS の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/11/2020
ms.author: jeedes
ms.openlocfilehash: d38ff5c8f33bda5b12f6267e7a8cdf477db6c7d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92511445"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pulse-secure-pcs"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Pulse Secure PCS の統合

このチュートリアルでは、Pulse Secure PCS と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Pulse Secure PCS を統合すると、次のことができます。

* Pulse Secure PCS にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Pulse Secure PCS に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Pulse Secure PCS でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Pulse Secure PCS では、**SP** Initiated SSO がサポートされます

## <a name="adding-pulse-secure-pcs-from-the-gallery"></a>ギャラリーからの Pulse Secure PCS の追加

Azure AD への Pulse Secure PCS の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Pulse Secure PCS を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Pulse Secure PCS**」と入力します。
1. 結果のパネルから **[Pulse Secure PCS]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-pulse-secure-pcs"></a>Pulse Secure PCS の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Pulse Secure PCS に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Pulse Secure PCS の関連ユーザーとの間にリンク関係を確立する必要があります。

Pulse Secure PCS に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Pulse Secure PCS の SSO の構成](#configure-pulse-secure-pcs-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Pulse Secure PCS のテスト ユーザーの作成](#create-pulse-secure-pcs-test-user)** - Pulse Secure PCS で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Pulse Secure PCS** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<FQDN of PCS>/dana-na/auth/saml-consumer.cgi`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<FQDN of PCS>/dana-na/auth/saml-endpoint.cgi?p=sp1`

    c. **[応答 URL]** ボックスに、`https://[FQDN of PCS]/dana-na/auth/saml-consumer.cgi` のパターンを使用して URL を入力します


    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際のサインオン URL、応答 URL、識別子で更新してください。 これらの値を取得するには、[Pulse Secure PCS クライアント サポート チーム](mailto:support@pulsesecure.net)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Pulse Secure PCS のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Pulse Secure PCS へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Pulse Secure PCS]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-pulse-secure-pcs-sso"></a>Pulse Secure PCS の SSO の構成

このセクションでは、PCS を SAML SP として構成するために必要な SAML 構成について説明します。 その他の基本構成 (領域やロールの作成など) については説明しません。

**Pulse Connect Secure の構成は次のとおりです。**

* SAML メタデータ プロバイダーとしての Azure AD の構成
* SAML 認証サーバーの構成
* それぞれの領域とロールへの割り当て

#### <a name="configuring-azure-ad-as-saml-metadata-provider"></a>SAML メタデータ プロバイダーとしての Azure AD の構成

次のページで、以下の手順を実行します。

![Pulse Connect Secure の構成](./media/pulse-secure-pcs-tutorial/saml-configuration.png)

1. Pulse Connect Secure 管理コンソールにログインします。
1. **[System]\(システム\)、[Configuration]\(構成\)、[SAML]** の順に移動します。
1. **[New Metadata Provider]\(新しいメタデータ プロバイダー\)** をクリックします。
1. **[Name]\(名前\)** ボックスに有効な名前を入力します。
1. Azure portal からダウンロードしたメタデータ XML ファイルを **Azure AD メタデータ ファイル** にアップロードします。
1. **[Accept Unsigned Metadata]\(署名されていないメタデータを受け入れる\)** を選択します。
1. **[Identity Provider]\(ID プロバイダー\)** ロールを選択します。
1. **[Save Changes]\(変更の保存\)** をクリックします。

#### <a name="steps-to-create-a-saml-auth-server"></a>SAML 認証サーバーの作成手順

1. **[Authentication]\(認証\)、[Auth Servers]\(認証サーバー\)** の順に移動します。
1. **[New: SAML Server]\(新規: SAML サーバー\)** を選択し、 **[New Server]\(新しいサーバー\)** をクリックします。

    ![Pulse Connect Secure 認証サーバー](./media/pulse-secure-pcs-tutorial/new-saml-server.png)

1. [Settings]\(設定\) ページで、次の手順を実行します。

    ![Pulse Connect Secure 認証サーバーの設定](./media/pulse-secure-pcs-tutorial/server-settings.png)

    a. テキスト ボックスで **サーバー名** を指定します。

    b. **SAML Version 2.0** を選択し、 **[構成モード]** で **[Metadata]\(メタデータ\)** を選択します。

    c. **[Connect Secure Entity Id]\(Connect Secure エンティティ ID\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** ダイアログ ボックスにある **[Identifier URL]\(識別子 URL\)** ボックスに貼り付けます。

    d. **[Identity Provider Entity Id]\(ID プロバイダー エンティティ ID\) ボックスの一覧** で、Azure AD エンティティ ID を値として選択します。

    e. **[Identity Provider Single Sign-On Service URL]\(ID プロバイダーのシングル サインオン サービス URL\) ボックスの一覧** で、Azure AD ログイン URL を値として選択します。

    f. **シングル ログアウト** は省略可能な設定です。 このオプションが選択されていると、ログアウトした後に新しい認証が要求されます。 このオプションが選択されておらず、ブラウザーを閉じていない場合は、認証なしで再接続できます。

    g. **要求される認証コンテキスト クラス** として **[Password]\(パスワード\)** を、**比較方法** として **[exact]\(完全\)** を選択します。

    h. **[Metadata Validity]\(メタデータの有効性\)** を日数の観点で設定します。
    
    i. **[Save Changes]\(変更の保存\)** をクリックします

### <a name="create-pulse-secure-pcs-test-user"></a>Pulse Secure PCS のテスト ユーザーの作成

このセクションでは、Pulse Secure PCS で Britta Simon というユーザーを作成します。 [Pulse Secure PCS サポート チーム](mailto:support@pulsesecure.net)と連携して、Pulse Secure PCS プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

1. Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Pulse Secure PCS のサインオン URL にリダイレクトされます。 

2. Pulse Secure PCS のサインオン URL に直接移動し、そこからログイン フローを開始します。

3. Microsoft アクセス パネルを使用することができます。 アクセス パネルで [Pulse Secure PCS] タイルをクリックすると、Pulse Secure PCS のサインオン URL にリダイレクトされます。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

Pulse Secure PCS を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
---
title: 'チュートリアル: Azure Active Directory と Asana の統合 | Microsoft Docs'
description: Azure Active Directory と Asana の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: a06c94eed6c90d7b38f28d37f3c8145d4ac1151d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651010"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>チュートリアル: Azure Active Directory と Asana の統合

このチュートリアルでは、Asana と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Asana を統合すると、次のことができます。

* Asana にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Asana に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Asana でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Asana では、**SP** によって開始される SSO がサポートされます

* Asana では、[**自動化** されたユーザー プロビジョニング](asana-provisioning-tutorial.md)がサポートされます

## <a name="add-asana-from-the-gallery"></a>ギャラリーからの Asana の追加

Azure AD への Asana の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Asana を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Asana**」と入力します。
1. 結果のパネルから **[Asana]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-asana"></a>Asana の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Asana に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Asana の関連ユーザーとの間にリンク関係を確立する必要があります。

Asana に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Asana の SSO の構成](#configure-asana-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Asana のテスト ユーザーの作成](#create-asana-test-user)** - Asana で Britta Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Asana** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Asana のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、URL として「`https://app.asana.com/`」と入力します。

    b. **[識別子 (エンティティ ID)]** ボックスに `https://app.asana.com/` という URL を入力します。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Asana の設定]** セクションで、要件どおりの適切な URL をコピーします。

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

このセクションでは、B.Simon に Asana へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Asana]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="configure-asana-sso"></a>Asana の SSO の構成

1. 別のブラウザー ウィンドウで、Asana アプリケーションにサインオンします。 Asana で SSO を構成するために、画面の右上隅にあるワークスペース名をクリックして、ワークスペースの設定にアクセスします。 次に、 **[\<your workspace name\> Settings]\(<ワークスペース名> 設定\)** をクリックします。

    ![Asana SSO 設定](./media/asana-tutorial/settings.png)

2. **[Organization settings (組織の設定)]** ウィンドウで **[Administration (管理)]** をクリックします。 その後、 **[Members must log in via SAML (メンバーは SAML でログインする必要がある)]** をクリックして SSO 構成を有効にします。 次の手順に従います。

    ![[組織の設定] のシングル サインオン構成](./media/asana-tutorial/save.png)  

    a. **[Sign-in page URL (サインイン ページ URL)]** ボックスに、**ログイン URL** を貼り付けます。

    b. Azure Portal からダウンロードした証明書を右クリックし、メモ帳またはお好きなテキスト エディターを使って証明書ファイルを開きます。 証明書タイトルの内容を最初から最後までコピーし、 **[X.509 Certificate]** (X.509 証明書) ボックスに貼り付けます。

3. **[保存]** をクリックします。 さらにサポートが必要な場合は、 [SSO の設定に関する Asana ガイド](https://asana.com/guide/help/premium/authentication#gl-saml) をご覧ください。

### <a name="create-asana-test-user"></a>Asana テスト ユーザーの作成

このセクションの目的は、Asana で Britta Shimon というユーザーを作成することです。 Asana では、自動ユーザー プロビジョニングがサポートされています。この設定は、既定で有効になっています。 自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](asana-provisioning-tutorial.md)をご覧ください。

**ユーザーを手動で作成する必要がある場合、次の手順を実行します。**

このセクションでは、Asana で Britta Simon というユーザーを作成します。

1. **Asana** の左側のパネルで、 **[Teams (チーム)]** セクションに移動します。 プラス記号ボタンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/asana-tutorial/teams.png)

2. ユーザーの電子メール アドレス (例: **britta.simon\@contoso.com**) をテキスト ボックスに入力し、 **[招待]** を選択します。

3. **[Send Invite (招待の送信)]** をクリックします。 新しいユーザーの電子メール アカウントに電子メールが届きます。 ユーザーは、アカウントを作成し、確認する必要があります。

### <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Asana のサインオン URL にリダイレクトされます。 

* Asana のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Asana] タイルをクリックすると、Asana のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Asana を構成すると、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
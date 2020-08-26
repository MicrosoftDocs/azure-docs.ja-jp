---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と BlueJeans for Azure AD の統合 | Microsoft Docs
description: Azure Active Directory と BlueJeans for Azure AD の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/09/2020
ms.author: jeedes
ms.openlocfilehash: 94b8a26a398ad8604a469486185d805fcbb6a8b2
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545405"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bluejeans-for-azure-ad"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と BlueJeans for Azure AD の統合

このチュートリアルでは、BlueJeans for Azure AD と Azure Active Directory (Azure AD) を統合する方法について説明します。 BlueJeans for Azure AD と Azure AD を統合すると、次のことができます。

* BlueJeans for Azure AD にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して BlueJeans for Azure AD に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* BlueJeans for Azure AD でのシングル サインオン (SSO) が有効になったサブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* BlueJeans for Azure AD では、**SP** Initiated SSO がサポートされます

* BlueJeans for Azure AD では、[**自動化された**ユーザー プロビジョニング](bluejeans-provisioning-tutorial.md)がサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-bluejeans-for-azure-ad-from-the-gallery"></a>ギャラリーからの BlueJeans for Azure AD の追加

Azure AD への BlueJeans for Azure AD の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に BlueJeans for Azure AD を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**BlueJeans for Azure AD**」と入力します。
1. 結果のパネルから **[BlueJeans for Azure AD]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-bluejeans-for-azure-ad"></a>BlueJeans for Azure AD の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、BlueJeans for Azure AD に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと BlueJeans for Azure AD の関連ユーザーとの間にリンク関係を確立する必要があります。

BlueJeans for Azure AD に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[BlueJeans for Azure AD の SSO の構成](#configure-bluejeans-for-azure-ad-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[BlueJeans for Azure AD のテスト ユーザーの作成](#create-bluejeans-for-azure-ad-test-user)** - BlueJeans for Azure AD で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **BlueJeans for Azure AD** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、`https://<companyname>.bluejeans.com` という形式で URL を入力します。

    a. **[識別子 (エンティティ ID)]** ボックスに、`http://samlsp.bluejeans.com` という URL を入力します。

    a. **[応答 URL]** ボックスに、`https://bluejeans.com/sso/saml2/` という URL を入力します。

    > [!NOTE]
    > サインオン URL は実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[BlueJeans for Azure AD クライアント サポート チーム](https://support.bluejeans.com/contact)に問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. BlueJeans アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、BlueJeans アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 |  ソース属性|
    | ---------| --------- |
    | Phone | user.telephonenumber |
    | title | user.jobtitle |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[BlueJeans for Azure AD のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、BlueJeans for Azure AD へのアクセスを許可することで、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[BlueJeans for Azure AD]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-bluejeans-for-azure-ad-sso"></a>BlueJeans for Azure AD の SSO の構成

1. 別の Web ブラウザー ウィンドウで、**BlueJeans for Azure AD** 企業サイトに管理者としてサインインします。

2. **[管理] \> [グループ設定] \> [セキュリティ]** の順にクリックします。

    ![管理者](./media/bluejeans-tutorial/ic785868.png "[Admin]")

3. **[セキュリティ]** セクションで、次の手順を実行します。

    ![SAML シングル サインオン](./media/bluejeans-tutorial/ic785869.png "[SAML シングル サインオン]")

    a. **[SAML シングル サインオン]** を選択します。

    b. **[自動プロビジョニングの有効化]** を選択します。

4. 次の手順を実行します。

    ![証明書パス](./media/bluejeans-tutorial/ic785870.png "証明書パス")

    a. **[ファイルの選択]** を選択して、Azure portal からダウンロードした base 64 でエンコードされた証明書をアップロードします。

    b. **[ログイン URL]** テキスト ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    c. **[Password Change URL]\(パスワード変更 URL\)** テキスト ボックスに、Azure portal からコピーした**パスワードの変更 URL** の値を貼り付けます。

    d. **[ログアウト URL]** ボックスに、Azure portal からコピーした**ログアウト URL** の値を貼り付けます。

5. 次の手順を実行します。

    ![[変更の保存]](./media/bluejeans-tutorial/ic785874.png "[変更の保存]")

    a. **[User id]\(ユーザー ID\)** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`」と入力します。

    b. **[電子メール]** ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`」と入力します。

    c. **[SAVE CHANGES]\(変更の保存\)** をクリックします。

### <a name="create-bluejeans-for-azure-ad-test-user"></a>BlueJeans for Azure AD のテスト ユーザーの作成

このセクションの目的は、BlueJeans for Azure AD で B.Simon というユーザーを作成することです。 BlueJeans for Azure AD では、自動ユーザー プロビジョニングがサポートされています。この設定は、既定で有効になっています。 自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](bluejeans-provisioning-tutorial.md)をご覧ください。

**ユーザーを手動で作成する必要がある場合は、次の手順を実行します:**

1. **BlueJeans for Azure AD** 企業サイトに管理者としてサインインします。

2. **[管理] \> [ユーザーの管理] \> [ユーザーの追加]** の順にクリックします。

    ![管理者](./media/bluejeans-tutorial/ic785877.png "[Admin]")

    > [!IMPORTANT]
    > **[ユーザーの追加]** タブは、 **[セキュリティ]** タブの **[自動プロビジョニングの有効化]** がオフになっている場合にのみ使用できます。

3. **[ユーザーの追加]** セクションで、次の手順を実行します。

    ![ユーザーの追加](./media/bluejeans-tutorial/ic785886.png "ユーザーの追加")

    a. **[名]** ボックスに、ユーザーの名を入力します (例: **B**)。

    b. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (例: **Simon**)。

    c. **[Pick a BlueJeans for Azure AD Username]\(BlueJeans for Azure AD ユーザー名の選択\)** ボックスにユーザー名 (**Brittasimon** など) を入力します

    d. **[パスワードの作成]** テキスト ボックスにパスワードを入力します。

    e. **[会社]** テキスト ボックスに会社名を入力します。

    f. **[Email Address]\(メール アドレス\)** ボックスに、ユーザーのメール アドレスを入力します (例: `b.simon\@contoso.com`)。

    g. **[Create a BlueJeans for Azure AD Meeting I.D]\(BlueJeans for Azure AD 会議 I.D の作成\)** ボックスに、会議の ID を入力します。

    h. **[Pick a Moderator Passcode]\(モデレーター パスコードの選択\)** テキスト ボックスに、パスコードを入力します。

    i. **[続行]** をクリックします。

    ![ユーザーの追加](./media/bluejeans-tutorial/ic785887.png "ユーザーの追加")

    J. **[ユーザーの追加]** をクリックします。

> [!NOTE]
> BlueJeans for Azure AD から提供されている他の BlueJeans for Azure AD ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [BlueJeans for Azure AD] タイルをクリックすると、SSO を設定した BlueJeans for Azure AD に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で BlueJeans for Azure AD を試す](https://aad.portal.azure.com/)

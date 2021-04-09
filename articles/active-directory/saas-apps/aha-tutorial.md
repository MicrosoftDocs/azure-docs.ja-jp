---
title: チュートリアル:Azure Active Directory と Aha! | Microsoft Docs
description: Azure Active Directory と Aha! の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: a39371e7a22334b11be1d1a0a9d28557efe177c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101654444"
---
# <a name="tutorial-integrate-aha-with-azure-active-directory"></a>チュートリアル:Aha! と Azure Active Directory を統合する

このチュートリアルでは、Aha! と Azure Active Directory (Azure AD) を統合する方法について説明します。 Aha! を Azure AD と統合すると、次のことができます。

* Aha! にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して Aha! に自動的に サインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Aha! でのシングル サインオン (SSO) が有効なサブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Aha! では、**SP** によって開始される SSO がサポートされます
* Aha! では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="add-aha-from-the-gallery"></a>ギャラリーからの Aha! の追加

Aha! の Azure AD への統合を構成するには、 Aha! をギャラリーから管理対象 SaaS アプリ の一覧に追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Aha!** 」 と入力します。
1. 結果パネルで **[Aha!]** を選択し、 アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-aha"></a>Aha! の Azure AD SSO の構成とテスト

Aha! での Azure AD SSO の構成とテスト **B.Simon** というテスト ユーザーを使用します。 SSO を機能させるために、Azure AD ユーザーと Aha! の関連ユーザーとの間にリンク関係を確立する必要があります。

Aha! に対する Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[Aha! のSSO の構成](#configure-aha-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Aha! のテスト ユーザーの作成](#create-aha-test-user)** - Azure AD でのユーザーにリンクされた、Aha! での Britta Simon の対応するユーザーを作成します。
3. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure Portal の **[Aha!]** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.aha.io/session/new`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.aha.io`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[Aha! クライアント サポート チーム](https://www.aha.io/company/contact)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Aha! のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Aha! へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[Aha!]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-aha-sso"></a>Aha! の SSO

1. Aha! 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Aha! のセットアップ]** をクリックして、 Aha! に移動します シングル サインオンを構成します。 そこから、管理者の資格情報を入力して Aha! にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 8 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Aha! を手動で設定する場合は、 新しい Web ブラウザー ウィンドウを開き、Aha! 企業サイトに管理者としてログインし、次の手順を実行します。

4. 上部のメニューで **[Settings]** をクリックします。

    ![設定](./media/aha-tutorial/setting.png "設定")

5. **[アカウント]** クリックします。

    ![プロファイル](./media/aha-tutorial/account.png "プロファイル")

6. **[セキュリティとシングル サインオン]** クリックします。

    ![[セキュリティとシングル サインオン] メニュー オプションが強調表示されているスクリーンショット。](./media/aha-tutorial/security.png "セキュリティとシングル サインオン")

7. **[シングル サインオン]** セクションで、 **[ID プロバイダー]** として **[SAML2.0]** を選択します。

    ![セキュリティとシングル サインオン](./media/aha-tutorial/saml.png "セキュリティとシングル サインオン")

8. **[シングル サインオン]** 構成ページで、次の手順を実行します。

    ![シングル サインオン](./media/aha-tutorial/sso.png "[Single Sign-On]")

    a. **[名前]** テキスト ボックスに、構成の名前を入力します。

    b. **[Configure using]** には **[メタデータ ファイル]** を選択します。

    c. ダウンロードしたメタデータ ファイルをアップロードするには、 **[参照]** をクリックします。

    d. **[Update]** をクリックします。

### <a name="create-aha-test-user"></a>Aha! のテスト ユーザー の作成

このセクションでは、B. Simon というユーザーを Aha! に作成します。 Aha! では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Aha! にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、 ログイン フローを開始できる Aha! のサインオン URL にリダイレクトされます。 

* Aha! の サインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 アクセス パネルで [Aha!] タイルをクリックすると、Aha! のサインオン URL にリダイレクトされます。 [サインオン URL]。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Aha! を構成したら、 組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
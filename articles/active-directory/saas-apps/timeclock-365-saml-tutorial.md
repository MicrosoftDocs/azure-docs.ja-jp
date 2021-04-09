---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Timeclock 365 SAML の統合 | Microsoft Docs
description: Azure Active Directory と Timeclock 365 SAML の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/28/2021
ms.author: jeedes
ms.openlocfilehash: d0c8364cc85cfce900021272d17456527919122b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99050806"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-timeclock-365-saml"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Timeclock 365 SAML の統合

このチュートリアルでは、Timeclock 365 SAML と Azure Active Directory (Azure AD) を統合する方法について説明します。 Timeclock 365 SAML と Azure AD を統合すると、次のことができます。

* Timeclock 365 SAML にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Timeclock 365 SAML に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Timeclock 365 SAML でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Timeclock 365 SAML では、**SP** Initiated SSO がサポートされます

## <a name="adding-timeclock-365-saml-from-the-gallery"></a>ギャラリーからの Timeclock 365 SAML の追加

Azure AD への Timeclock 365 SAML の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Timeclock 365 SAML を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Timeclock 365 SAML**」と入力します。
1. 結果パネルから **[Timeclock 365 SAML]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-timeclock-365-saml"></a>Timeclock 365 SAML の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Timeclock 365 SAML に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Timeclock 365 SAML の関連ユーザーとの間にリンク関係を確立する必要があります。

Timeclock 365 SAML に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Timeclock 365 SAML の SSO の構成](#configure-timeclock-365-saml-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Timeclock 365 SAML のテスト ユーザーの作成](#create-timeclock-365-saml-test-user)** - Timeclock 365 SAML で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Timeclock 365 SAML** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    **[サインオン URL]** テキスト ボックスに、URL として「`https://live.timeclock365.com/login`」と入力します。


1. **[Set up single sign-on with SAML]\(SAML でシングル サインオンをセットアップします\)** ページの **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

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

このセクションでは、B.Simon に Timeclock 365 SAML へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Timeclock 365 SAML]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-timeclock-365-saml-sso"></a>Timeclock 365 SAML の SSO の構成

1. Timeclock 365 SAML 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Timeclock 365 SAML のセットアップ]** をクリックすると、Timeclock 365 SAML アプリケーションに移動します。 そこから、管理者資格情報を指定して Timeclock 365 SAML にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 から 4 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Timeclock 365 SAML を手動でセットアップする場合は、別の Web ブラウザー ウィンドウで、Timeclock 365 SAML 企業サイトに管理者としてサインインします。

1. 次の手順を実行します。

    ![Timeclock の構成](./media/timeclock-365-saml-tutorial/saml-configuration.png)

    a. **[Settings]\(設定\) > [Company profile]\(会社プロファイル\) > [Settings]\(設定\)** タブに移動します。

    b. **[IDP metadata path]\(IDP メタデータ パス\)** に、Azure portal からコピーした **アプリのフェデレーション メタデータ URL** を貼り付けます。

    c. 次に、 **[Update]\(更新\)** をクリックします。

### <a name="create-timeclock-365-saml-test-user"></a>Timeclock 365 SAML のテスト ユーザーの作成

1. ブラウザーで新しいタブを開き、Timeclock 365 SAML の会社のサイトに管理者としてサインインします。

1. **[Users]\(ユーザー\) > [Add new user]\(新しいユーザーの追加\)** に移動します。

    ![テスト ユーザーの作成 1 ](./media/timeclock-365-saml-tutorial/add-user-1.png)

1. **[User information]\(ユーザー情報\)** ページに必要なすべての情報を入力し、 **[Save]\(保存\)** をクリックします。

    ![テスト ユーザーの作成 2 ](./media/timeclock-365-saml-tutorial/add-user-2.png)

1. **[Create]\(作成\)** ボタンをクリックして、テスト ユーザーを作成します。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Timeclock 365 SAML のサインオン URL にリダイレクトされます。 

* Timeclock 365 SAML のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Timeclock 365 SAML] タイルをクリックすると、Timeclock 365 SAML サインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Timeclock 365 SAML を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

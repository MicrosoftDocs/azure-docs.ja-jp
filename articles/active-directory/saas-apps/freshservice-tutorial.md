---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Freshservice の統合 | Microsoft Docs
description: Azure Active Directory と Freshservice の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/29/2020
ms.author: jeedes
ms.openlocfilehash: 9e53881d5b28b61cd220b14e02c411db53d19d36
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88550998"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Freshservice の統合

このチュートリアルでは、Freshservice と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Freshservice を統合すると、次のことができます。

* Freshservice にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Freshservice に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* FreshService でのシングル サインオン (SSO) が有効なサブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Freshservice では、**SP** によって開始される SSO がサポートされます
* Freshservice を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-freshservice-from-the-gallery"></a>ギャラリーからの Freshservice の追加

Azure AD への Freshservice の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Freshservice を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Freshservice**」と入力します。
1. 結果ウィンドウで **[Freshservice]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-freshservice"></a>Freshservice の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Freshservice で Azure AD の SSO を構成し、テストします。 SSO が機能するために、Azure AD ユーザーと Freshservice の関連ユーザーの間で、リンク関係を確立する必要があります。

Freshservice で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Freshservice のシングル サインオンの構成](#configure-freshservice-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Freshservice テスト ユーザーの作成](#create-freshservice-test-user)** - Freshservice で B.Simon に対応するユーザーを作成し、Azure AD のそのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Freshservice** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、`https://<democompany>.freshservice.com` という形式で URL を入力します。

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<democompany>.freshservice.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[Freshservice クライアント サポート チーム](https://support.freshservice.com/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **Azure portal** の **[Freshservice のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Freshservice へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Freshservice]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-freshservice-sso"></a>Freshservice の SSO の構成

1. Freshservice 内での構成を自動化するには、**[拡張機能のインストール]** をクリックして**マイ アプリによるセキュリティで保護されたサインイン拡張機能**をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[Freshservice のセットアップ]** をクリックすると、Freshservice アプリケーションに移動します。 そこから、管理者資格情報を提供して Freshservice にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. Freshservice を手動で設定する場合は、Freshservice の企業サイトに管理者としてログインします。

1. 左側のメニューで、 **[Admin]\(管理者\)** をクリックし、 **[General Settings]\(全般設定\)** で **[Helpdesk Security]\(ヘルプデスクのセキュリティ\)** を選択します。

    ![管理者](./media/freshservice-tutorial/configure-1.png "[Admin]")

1. **[Security]\(セキュリティ\)** で、 **[Go to Freshworks 360 Security]\(Freshworks 360 Security にアクセスする\)** をクリックします。

    ![Security](./media/freshservice-tutorial/configure-2.png "セキュリティ")

1. **[セキュリティ]** セクションで、次の手順を実行します。

    ![シングル サインオン](./media/freshservice-tutorial/configure-3.png "シングル サインオン")
  
    a. **[Single Sign On]\(シングル サインオン\)** で **[On]\(オン\)** を選択します。

    b. **[Login Method]\(ログイン方法\)** で、 **[SAML SSO]** を選択します。

    c. **[Entity ID provided by the IdP]\(IdP によって提供されたエンティティ ID\)** テキスト ボックスに、Azure portal からコピーした**エンティティ ID** の値を貼り付けます。

    d. **[SAML SSO URL]** テキストボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    e. **[Signing Options]\(署名オプション\)** で、ドロップダウンから **[Only Signed Assertions]\(署名されたアサーションのみ\)** を選択します。

    f. **[ログアウト URL]** テキストボックスに、Azure portal からコピーした**ログアウト URL** の値を貼り付けます。

    g. **[Security Certificate]\(セキュリティ証明書\)** テキスト ボックスに、前に取得した**証明書 (Base64)** の値を貼り付けます。
  
    h. **[保存]** をクリックします。


## <a name="create-freshservice-test-user"></a>Freshservice のテスト ユーザーの作成

Azure AD ユーザーが Freshservice にサインインできるようにするには、そのユーザーを Freshservice にプロビジョニングする必要があります。 FreshService の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **FreshService** 企業サイトに管理者としてサインインします。

2. 左側のメニューで **[Admin]\(管理者\)** をクリックします。

3. **[ユーザー管理]** セクションで、 **[要求者]** をクリックします。

    ![[要求者]](./media/freshservice-tutorial/create-user-1.png "[要求者]")

4. **[新しい要求者]** をクリックします。

    ![[新しい要求者]](./media/freshservice-tutorial/create-user-2.png "新しい要求者")

5. **[New Requester]\(新しい要求者\)** セクションで、必要なフィールドを入力し、 **[Save]\(保存\)** をクリックします。
    ![[新しい要求者]](./media/freshservice-tutorial/create-user-3.png "[新しい要求者]")  

    > [!NOTE]
    > Azure Active Directory のアカウント所有者は、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールを受け取ります。
    >  

    > [!NOTE]
    > 他の FreshService ユーザー アカウント作成ツールや、FreshService から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Freshservice] タイルをクリックすると、SSO を設定した Freshservice に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で Freshservice を試す](https://aad.portal.azure.com/)
---
title: チュートリアル:Azure Active Directory と Atlassian Cloud の統合 | Microsoft Docs
description: Azure Active Directory と Atlassian Cloud の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: celested
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3a54b096019e9e38bc800ae313016a430062dab
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964326"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>チュートリアル:Atlassian Cloud と Azure Active Directory の統合

このチュートリアルでは、Atlassian Cloud と Azure Active Directory (Azure AD) を統合する方法について説明します。 Atlassian Cloud と Azure AD を統合すると、次のことができます。

* Atlassian Cloud にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して Atlassian Cloud に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションをお持ちでない場合は、[ここ](https://azure.microsoft.com/pricing/free-trial/)から 1 か月間の無料試用版を入手できます。
* Atlassian Cloud でのシングル サインオン (SSO) が有効なサブスクリプション
* Atlassian Cloud 製品の Security Assertion Markup Language (SAML) シングル サインオンを有効にするには、Atlassian Access を設定する必要があります。 詳細については、「[Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager)」を参照してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 

* Atlassian Cloud では、**SP と IDP** によって開始される SSO がサポートされます
* Atlassian Cloud では、[自動化されたユーザー プロビジョニングとプロビジョニング解除](atlassian-cloud-provisioning-tutorial.md)がサポートされます

## <a name="adding-atlassian-cloud-from-the-gallery"></a>ギャラリーからの Atlassian Cloud の追加

Azure AD への Atlassian Cloud の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Atlassian Cloud を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Atlassian Cloud**」と入力します。
1. 結果のパネルから **[Atlassian Cloud]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Atlassian Cloud に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Atlassian Cloud の関連ユーザーとの間にリンク関係を確立する必要があります。

Atlassian Cloud に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Atlassian Cloud SSO の構成](#configure-atlassian-cloud-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Atlassian Cloud のテスト ユーザーの作成](#create-atlassian-cloud-test-user)** - Atlassian Cloud で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Atlassian Cloud** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://auth.atlassian.com/saml/<unique ID>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://auth.atlassian.com/login/callback?connection=saml-<unique ID>` のパターンを使用して URL を入力します

    c. **[追加の URL を設定します]** をクリックします。

    d. **[リレー状態]** ボックスに、`https://<instancename>.atlassian.net` のパターンで URL を入力します。

    > [!NOTE]
    > 上記の値は、実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値は、チュートリアルの「**Atlassian Cloud のシングル サインオンを構成する**」で後述する **Atlassian Cloud SAML 構成**画面から取得できます。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<instancename>.atlassian.net` という形式で URL を入力します。

    > [!NOTE]
    > サインオン URL は実際の値ではありません。 Atlassian Cloud 管理ポータルへのサインインに使用するインスタンスの値を貼り付けます。

    ![Configure single sign-on](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-10.png)

1. Atlassian Cloud アプリケーションでは、特定の形式の SAML アサーションを受け取るため、SAML トークン属性の構成にカスタム属性マッピングを追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示しています。ここで、**nameidentifier** は **user.userprincipalname** にマップされています。 Atlassian Cloud アプリケーションでは、**nameidentifier** が **user.mail** にマップされると想定されているため、 **[編集]** アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![image](common/edit-attribute.png)

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Atlassian Cloud のセットアップ]** セクションで、要件のとおりに適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

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

このセクションでは、B.Simon に Atlassian Cloud へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Atlassian Cloud]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-atlassian-cloud-sso"></a>Atlassian Cloud SSO の構成

1. Atlassian Cloud 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして**マイ アプリによるセキュリティで保護されたサインイン拡張機能**をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[Setup Atlassian Cloud]** (Atlassian Cloud のセットアップ) をクリックすると、Atlassian Cloud アプリケーションに移動します。 そこから、管理者資格情報を提供して Atlassian Cloud にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 7 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. Atlassian Cloud を手動でセットアップしたい場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Atlassian Cloud 企業サイトにサインインして、次の手順を実行します。

1. シングル サインオンを構成する前に、ドメインを確認する必要があります。 詳細については、[Atlassian の「Domain verification」(ドメインの確認)](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) ドキュメントを参照してください。

1. 左側のウィンドウで、 **[Security]\(セキュリティ\)**  >  **[SAML single sign-on]\(SAML シングル サインオン\)** を選択します。 まだ Atlassian の Identity Manager に登録していない場合は、登録します。

    ![Configure single sign-on](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-11.png)

1. **[Add SAML configuration]\(SAML 構成の追加\)** ウィンドウで、次の手順を実行します。

    ![Configure single sign-on](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-12.png)

    a. **[ID プロバイダー エンティティ ID]** ボックスに、Microsoft Azure portal でコピーした **[Azure AD 識別子]** を貼り付けます。

    b. **[ID プロバイダー SSO URL]** ボックスに、Microsoft Azure portal でコピーした **[ログイン URL]** を貼り付けます。

    c. Azure Portal からダウンロードした証明書を .txt ファイルで開き、*Begin Certificate (証明書の開始)* 行と *End Certificate (証明書の終了)* 行以外の値をコピーして、 **[Public X509 certificate]\(公開 X509 証明書\)** ボックスに貼り付けます。

    d. **[Save Configuration]** をクリックします。

1. 確実に正しい URL を設定するには、次の手順を実行して Azure AD 設定を更新します。

    ![Configure single sign-on](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-13.png)

    a. SAML ウィンドウで **[SP 識別 ID]** の値をコピーし、Microsoft Azure portal の Atlassian Cloud の **[基本的な SAML 構成]** の下で **[識別子]** ボックスに貼り付けます。

    b. SAML ウィンドウで **[SP Assertion Consumer Service URL]** の値をコピーし、Microsoft Azure portal の Atlassian Cloud の **[基本的な SAML 構成]** の下で **[応答 URL]** ボックスに貼り付けます。 サインオン URL は、ご利用の Atlassian Cloud のテナント URL です。

    > [!NOTE]
    > 既存のお客様であれば、Azure Portal の **[SP Identity ID]\(SP 固有 ID\)** と **[SP Assertion Consumer Service URL]\(SP アサーション利用者サービス URL\)** の値を更新したあと、 **[Yes, update configuration]\(はい、構成を更新します\)** を選択します。 新規のお客様であれば、この手順をスキップしてかまいません。

### <a name="create-atlassian-cloud-test-user"></a>Atlassian Cloud のテスト ユーザーの作成

Azure AD ユーザーが Atlassian Cloud にサインインできるようにするには、Atlassian Cloud で次の手順を実行して、手動でユーザー アカウントをプロビジョニングします。

1. **[Administration]\(管理\)** ウィンドウで、 **[Users]\(ユーザー\)** を選択します。

    ![Atlassian Cloud の [Users]\(ユーザー\) リンク](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. Atlassian Cloud でユーザーを作成するには、 **[Invite user]\(ユーザーの招待\)** を選択します。

    ![Atlassian Cloud ユーザーの作成](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. **[Email address]\(電子メール アドレス\)** ボックスにユーザーのメール アドレスを入力してから、アプリケーション アクセスを割り当てます。

    ![Atlassian Cloud ユーザーの作成](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. ユーザーに招待メールを送信するには、 **[Invite user]\(ユーザーの招待\)** を選択します。 ユーザーに招待メールが送信され、招待が受け入れられると、システム上でそのユーザーがアクティブになります。

> [!NOTE]
> **[Users]\(ユーザー\)** セクションの **[Bulk Create]\(一括作成\)** を選択することで、まとめて複数のユーザーを作成することもできます。

### <a name="test-sso"></a>SSO のテスト

アクセス パネルで [Atlassian Cloud] タイルを選択すると、SSO を設定した Atlassian Cloud に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で Atlassian Cloud を試す](https://aad.portal.azure.com/)

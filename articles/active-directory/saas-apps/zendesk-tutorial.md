---
title: チュートリアル:Azure Active Directory と Zendesk の統合 | Microsoft Docs
description: Azure Active Directory と Zendesk の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 58e9cc2fda7779cf0d62db8e0b6f78e5bb4d95f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98731821"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zendesk"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Zendesk の統合

このチュートリアルでは、Zendesk と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Zendesk を統合すると、次のことができます。

* Zendesk にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Zendesk に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。


## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Zendesk サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Zendesk では、**SP** によって開始される SSO がサポートされます
* Zendesk では、[**自動化された** ユーザー プロビジョニング](zendesk-provisioning-tutorial.md)がサポートされます


## <a name="adding-zendesk-from-the-gallery"></a>ギャラリーからの Zendesk の追加

Azure AD への Zendesk の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Zendesk を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Zendesk**」と入力します。
1. 結果のパネルから **[Zendesk]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-zendesk"></a>Zendesk の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Zendesk に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Zendesk の関連ユーザーとの間にリンク関係を確立する必要があります。

Zendesk に対する Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Zendesk の SSO の構成](#configure-zendesk-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Zendesk テスト ユーザーの作成](#create-zendesk-test-user)** - Zendesk で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Zendesk** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<subdomain>.zendesk.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<subdomain>.zendesk.com`

    c. **[応答 URL]** ボックスに、`https://<subdomain>.zendesk.com/access/saml` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際のサインオン URL、識別子、および応答 URL で更新してください。 これらの値を取得するには、[Zendesk クライアント サポート チーム](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. Zendesk アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 必須の SAML 属性はありませんが、必要に応じて、アプリケーション統合ページの **[ユーザー属性]** セクションから管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![このスクリーンショットは、[編集] アイコンが選択された状態の [User Attributes]\(ユーザー属性\) を示しています。](common/edit-attribute.png)

    > [!NOTE]
    > 既定では、Azure AD に含まれていない属性を追加するには拡張属性を使用します。 「[SAMLに設定できるユーザー属性](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-)」をクリックすると、**Zendesk** が受け入れる SAML 属性の完全な一覧が表示されます。

1. **[SAML 署名証明書]** セクションで **[編集]** ボタンをクリックして、 **[SAML 署名証明書]** ダイアログを開きます。

    ![SAML 署名証明書の編集](common/edit-certificate.png)

1. **[SAML 署名証明書]** セクションで **[Thumbprint Value]\(拇印の値\)** をコピーし、お使いのコンピューターに保存します。

    ![[Thumbprint]\(拇印\) の値をコピーする](common/copy-thumbprint.png)

1. **[Zendesk のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Zendesk へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Zendesk]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-zendesk-sso"></a>Zendesk の SSO の構成

1. **Zendesk** 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![[拡張機能のインストール] ボタンを示すスクリーンショット。](./media/target-process-tutorial/install_extension.png)

1. ブラウザーに拡張機能を追加した後、 **[Zendesk のセットアップ]** をクリックすると、Zendesk アプリケーションに移動します。 そこから、管理者資格情報を提供して Zendesk にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. Zendesk を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Zendesk 企業サイトにサインインして、次の手順を実行します。

1. **Zendesk 管理センター** で、 **[Security]\(セキュリティ\)** タブの **[Security settings]\(セキュリティ設定\)** をクリックします。

    ![[Security settings]\(セキュリティ設定\) が選択されている Zendesk 管理センターを示すスクリーンショット。](./media/zendesk-tutorial/settings.png "セキュリティ")

1. **[Single sign-on]\(シングル サインオン\)** ページにアクセスし、 **[SAML]** で **[Edit]\(編集\)** をクリックします。

    ![[Edit]\(編集\) が選択されている [Single sign-on]\(シングル サインオン\) ページを示すスクリーンショット。](./media/zendesk-tutorial/saml-sso.png "Security")

1. **[SSO]** ページで次の手順を実行します。

    ![シングル サインオン](./media/zendesk-tutorial/saml-configuration.png "シングル サインオン")

    a. **[SAML SSO URL]** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    b. **[Certificate Fingerprint] \(証明書のフィンガープリント)** テキスト ボックスに、Azure Portal からコピーした証明書の **THUMBPRINT** 値を貼り付けます。

    c. **[Remote Logout URL]\(リモート ログアウト URL\)** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    d. **[保存]** をクリックします。

### <a name="create-zendesk-test-user"></a>Zendesk のテスト ユーザーの作成

このセクションの目的は、ZendeskにBritta Simon というユーザーを作成することです。 Zendesk では、自動ユーザー プロビジョニングがサポートされています。この設定は、既定で有効になっています。 自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](Zendesk-provisioning-tutorial.md)をご覧ください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Zendesk のサインオン URL にリダイレクトされます。 

* Zendesk のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Zendesk] タイルをクリックすると、Zendesk のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Zendesk を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
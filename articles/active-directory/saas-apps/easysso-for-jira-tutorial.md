---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と EasySSO for Jira の統合 | Microsoft Docs
description: Azure Active Directory と EasySSO for Jira の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/28/2020
ms.author: jeedes
ms.openlocfilehash: b813f1f77b7bda2627f5275e772a3ee7c9d12834
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92454356"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-jira"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と EasySSO for Jira の統合

このチュートリアルでは、EasySSO for Jira と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と EasySSO for Jira を統合すると、次のことができます。

* Jira にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Jira に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* EasySSO for Jira でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* EasySSO for Jira では、**SP および IDP** Initiated SSO がサポートされます
* EasySSO for Jira では、**Just-In-Time** ユーザー プロビジョニングがサポートされます
* EasySSO for Jira を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-easysso-for-jira-from-the-gallery"></a>ギャラリーからの EasySSO for Jira の追加

Azure AD への EasySSO for Jira の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に EasySSO for Jira を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**EasySSO for Jira**」と入力します。
1. 結果のパネルから **[EasySSO for Jira]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-easysso-for-jira"></a>EasySSO for Jira の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、EasySSO for Jira に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと EasySSO for Jira の関連ユーザーとの間にリンク関係を確立する必要があります。

EasySSO for Jira で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[EasySSO for Jira SSO の構成](#configure-easysso-for-jira-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[EasySSO for Jira テスト ユーザーの作成](#create-easysso-for-jira-test-user)** - EasySSO for Jira で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **EasySSO for Jira** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://<server-base-url>/plugins/servlet/easysso/saml` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<server-base-url>/plugins/servlet/easysso/saml` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<server-base-url>/login.jsp` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値の取得方法がわからない場合には、[EasySSO サポート チーム](mailto:support@techtime.co.nz)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. EasySSO for Jira アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、EasySSO for Jira アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 | ソース属性|
    | ---------------| --------- |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | User.mail |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.4 | User.surname |
    | urn:oid:2.5.4.42 | User.givenname |
    
    Azure AD ユーザーに対して既に **sAMAccountName** を構成済みの場合には、**sAMAccountName** 属性に **urn:oid:0.9.2342.19200300.100.1.1** をマップする必要があります。
    
1. **[SAML によるシングル サインオンのセットアップ]** ページの **[SAML 署名証明書]** セクションで **[証明書 (Base64)]** または **[フェデレーション メタデータ XML]** オプションの **[ダウンロード]** リンクをクリックし、そのいずれかまたは両方をコンピューターに保存します。 これは、後で Jira EasySSO を構成するために必要になります。

    ![証明書のダウンロードのリンク](media/easysso-for-jira-tutorial/azure-ad-SAML-certificate.png)
    
    EasySSO for Jira の構成を証明書を使って手動で実施する予定の場合には、ほかにも以下のセクションから **ログイン URL** と **Azure AD ID** をコピーし、コンピューターに保存しておく必要があります。

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

このセクションでは、EasySSO for Jira へのアクセスを許可することで、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[EasySSO for Jira]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-easysso-for-jira-sso"></a>EasySSO for Jira SSO の構成

1. 管理者権限で Atlassian Jira インスタンスにサインインし、 **[Manage Apps]\(アプリの管理\)** セクションに移動します。 

    ![[Manage Apps]\(アプリの管理\)](media/easysso-for-jira-tutorial/jira-admin-1.png)

2. 左側で **EasySSO** を探し、クリックします。

    ![[EasySSO]](media/easysso-for-jira-tutorial/jira-admin-2.png)

3. **[SAML]** オプションを選択します。 これにより、SAML の構成セクションが表示されます。

    ![SAML](media/easysso-for-jira-tutorial/jira-admin-3.png)

4. 上部の **[Certificates]\(証明書\)** タブを選択すると、次の画面が表示されます。

    ![メタデータ URL](media/easysso-for-jira-tutorial/jira-admin-4.png)

5. **Azure AD SSO** の構成手順で保存した **証明書 (Base64)** または **メタデータ ファイル** を探します。 続行する方法として、次の選択肢があります。

    a. 自分のコンピューター上のローカル ファイルとしてダウンロードした、アプリのフェデレーション **メタデータ ファイル** を使用します。 **[Upload]\(アップロード\)** をクリックし、ご使用のオペレーティング システムに固有のファイルのアップロード ダイアログに従います。

    **OR**

    b. (任意のプレーンテキスト エディターで) アプリのフェデレーション **メタデータ ファイル** を開き、ファイルの内容を確認してクリップボードにコピーします。 **[Input]\(入力\)** オプションを選択し、クリップボードの内容をテキスト フィールドに貼り付けます。

     **OR**

    c. すべて手動で構成します。 (任意のプレーンテキスト エディターで) アプリのフェデレーション **証明書 (Base64)** を開き、ファイルの内容を確認してクリップボードにコピーします。 **[IdP Token Signing Certificates]\(IdP トークン署名証明書\)** テキスト フィールドに貼り付けます。 次に、 **[General]\(全般\)** タブに移動し、 **[POST Binding URL]\(POST バインディング URL\)** および **[Entity ID]\(エンティティ ID\)** フィールドに、前に保存した **[ログイン URL]** と **[Azure AD 識別子]** の値をそれぞれ入力します。
 
6. ページの下部にある **[Save]\(保存\)** ボタンをクリックします。 メタデータ ファイルまたは証明書ファイルの内容が構成フィールドで解析されていることを確認できます。 これで、EasySSO for Jira の構成は完了しました。

7. 最適なテスト エクスペリエンスを得るには、 **[Look & Feel]\(外観\)** タブに移動し、 **[SAML Login Button]\(SAML ログイン ボタン\)** オプションをオンにします。 これにより、Jira ログイン画面の独立したボタンが有効になり、Azure AD SAML 統合をエンド ツー エンドでテストできるようになります。 このボタンをオンのままにすることで、運用モードでの配置、色、および翻訳を構成することもできます。

    ![[Look & Feel]\(外観\)](media/easysso-for-jira-tutorial/jira-admin-5.png)

    > [!NOTE]
    > 問題が発生した場合は、[EasySSO サポート チーム](mailto:support@techtime.co.nz)にお問い合わせください。

### <a name="create-easysso-for-jira-test-user"></a>EasySSO for Jira テスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Jira に作成します。 EasySSO for Jira では Just-In-Time ユーザー プロビジョニングがサポートされており、既定では **無効** になっています。 ユーザー プロビジョニングを有効にするには、EasySSO プラグイン構成の [General]\(全般\) セクションで、 **[Create user on successful login]\(ログイン成功時にユーザーを作成する\)** オプションを明示的にオンにする必要があります。 Jira にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

ただし、ユーザーが初めてログインした時点での自動ユーザー プロビジョニングを有効にしない場合は、Jira インスタンスで使用するバックエンド ユーザー ディレクトリ (LDAP や Atlassian Crowd など) にユーザーが存在する必要があります。

![ユーザー プロビジョニング](media/easysso-for-jira-tutorial/jira-admin-6.png)

## <a name="test-sso"></a>SSO のテスト 

### <a name="idp-initiated-workflow"></a>IdP-Initiated ワークフロー

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [EasySSO for Jira] タイルをクリックすると、SSO を設定した Jira インスタンスに自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

### <a name="sp-initiated-workflow"></a>SP-Initiated ワークフロー

このセクションでは、Jira の **[SAML Login]\(SAML ログイン\)** ボタンを使用して Azure AD のシングル サインオン構成をテストします。

![ユーザーの SAML ログイン](media/easysso-for-jira-tutorial/jira-admin-7.png)

このシナリオは、Jira EasySSO の構成ページ (上記を参照) の **[Look & Feel]\(外観\)** で **[SAML Login Button]\(SAML ログイン ボタン\)** を有効にしていることが前提になっています。 既存のセッションとの干渉を避けるために、シークレット モードにしたブラウザーで Jira のログイン URL を開きます。 **[SAML Login]\(SAML ログイン\)** をクリックすると、Azure AD のユーザー認証フローにリダイレクトされます。 正常に完了すると、認証済みユーザーとして SAML を介して元の Jira インスタンスにリダイレクトされます。

Azure AD からのリダイレクト後に、次の画面が表示される可能性があります。

![EasySSO のエラー画面](media/easysso-for-jira-tutorial/jira-admin-8.png)

このような場合には、[こちらのページの手順]( https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS)に従って **atlassian-jira.log** ファイルにアクセスする必要があります。 EasySSO エラー ページにある参照 ID を使って、エラーの詳細を確認できます。

ログ メッセージの内容について疑問がある場合には、[EasySSO サポート チーム](mailto:support@techtime.co.nz)にお問い合わせください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で EasySSO for Jira を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって EasySSO for Jira を保護する方法](/cloud-app-security/proxy-intro-aad)
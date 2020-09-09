---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と EasySSO for BitBucket の統合 | Microsoft Docs
description: Azure Active Directory と EasySSO for BitBucket の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: jeedes
ms.openlocfilehash: bd15798e9b642f3abc617c0fa224c530d90b5dd6
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555568"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bitbucket"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と EasySSO for BitBucket の統合

このチュートリアルでは、EasySSO for BitBucket と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と EasySSO for BitBucket を統合すると、次のことができます。

* EasySSO for BitBucket にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して EasySSO for BitBucket に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、[Azure Active Directory を使用したアプリケーション アクセスとシングル サインオンの概要](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な EasySSO for BitBucket のサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* EasySSO for BitBucket では、SP-initiated および IdP-initiated SSO がサポートされます。
* EasySSO for BitBucket では、"Just-In-Time" ユーザー プロビジョニングがサポートされます。
* EasySSO for BitBucket を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="add-easysso-for-bitbucket-from-the-gallery"></a>ギャラリーから EasySSO for BitBucket を追加する

Azure AD への EasySSO for BitBucket の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に EasySSO for BitBucket を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**EasySSO for BitBucket**」と入力します。
1. 結果から **[EasySSO for BitBucket]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-easysso-for-bitbucket"></a>EasySSO for BitBucket の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、EasySSO for BitBucket に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと EasySSO for BitBucket の関連ユーザーとの間にリンクされた関係を確立する必要があります。

EasySSO for BitBucket で Azure AD SSO を構成してテストするには、次の手順を完了します。

1. [Azure AD SSO を構成](#configure-azure-ad-sso)して、ユーザーがこの機能を使用できるようにします。
    1. [Azure AD のテスト ユーザーを作成](#create-an-azure-ad-test-user)して、B.Simon を使って Azure AD のシングル サインオンをテストします。
    1. [Azure AD テスト ユーザーを割り当て](#assign-the-azure-ad-test-user)て、B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. [EasySSO for BitBucket SSO の構成](#configure-easysso-for-bitbucket-sso) - アプリケーション側でシングル サインオン設定を構成します。
    1. [EasySSO for BitBucket テスト ユーザーの作成](#create-an-easysso-for-bitbucket-test-user) - EasySSO for BitBucket で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. [SSO をテスト](#test-sso)して、構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **EasySSO for BitBucket** アプリケーション統合ページで、 **[管理]** セクションを見つけます。 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンを選択して設定を編集します。

   ![鉛筆アイコンが強調表示された [SAML によるシングル サインオンのセットアップ] ページのスクリーンショット](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IdP** Initiated モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://<server-base-url>/plugins/servlet/easysso/saml` という形式で URL を入力します。

    b. **[応答 URL]** ボックスに、次のパターンを使用する URL を入力します: `https://<server-base-url>/plugins/servlet/easysso/saml`。

1. アプリケーションを **SP** Initiated モードで構成する場合は、 **[追加の URL を設定します]** を選択して次の手順を実行します。

    - **[サインオン URL]** ボックスに、`https://<server-base-url>/login.jsp` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値の取得方法がわからない場合には、[EasySSO サポート チーム](mailto:support@techtime.co.nz)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. EasySSO for BitBucket アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![既定の属性のスクリーンショット](common/default-attributes.png)

1. EasySSO for BitBucket アプリケーションでは、さらにいくつかの属性も SAML 応答で返されることが想定されています。 それらを次の表に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 | ソース属性|
    | ---------------| --------- |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | User.mail |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.4 | User.surname |
    | urn:oid:2.5.4.42 | User.givenname |
    
    Azure AD ユーザーに対して既に **sAMAccountName** を構成済みの場合には、**sAMAccountName** 属性に **urn:oid:0.9.2342.19200300.100.1.1** をマップする必要があります。
    
1. **[SAML によるシングル サインオンのセットアップ]** ページの **[SAML 署名証明書]** セクションで **[証明書 (Base64)]** または **[フェデレーション メタデータ XML]** オプションの [ダウンロード] リンクを選択します。 そのいずれかまたは両方をコンピューターに保存します。 これは、後で BitBucket EasySSO を構成するために必要になります。

    ![[ダウンロード] リンクが強調表示された [SAML 署名証明書] セクションのスクリーンショット](./media/easysso-for-bitbucket-tutorial/certificate.png)
    
    証明書を使って EasySSO for BitBucket を手動で構成する予定の場合には、**ログイン URL** と **Azure AD ID** をコピーして、コンピューターに保存しておく必要もあります。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 上で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **名前**には、`B.Simon`を入力します。  
   1. **[ユーザー名]** に「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[Show password]\(パスワードの表示\)** チェック ボックスをオンにし、パスワードを書き留めます。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、EasySSO for BitBucket へのアクセスを許可することで、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。
1. アプリケーションの一覧で、 **[EasySSO for BitBucket]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] が強調表示された [管理] セクションのスクリーンショット](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択します。 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] が強調表示された [ユーザーとグループ] ページのスクリーンショット](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログ ボックスの**ユーザー**の一覧で **[B.Simon]** を選択し、画面の下部にある **[選択]** を選択します。
1. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 次に、画面の下部にある **[選択]** を選択します。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

## <a name="configure-easysso-for-bitbucket-sso"></a>EasySSO for BitBucket SSO の構成

1. 管理者権限で Atlassian BitBucket インスタンスにサインインし、 **[Administration]\(管理\)** セクションに移動します。 

    ![歯車アイコンが強調表示された BitBucket インスタンスのスクリーンショット](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-1.png)
1. **[EasySSO]** を見つけて選択します。

    ![[EasySSO] オプションのスクリーンショット](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-2.png)

1. **[SAML]** を選択します。 これにより、SAML の構成セクションが表示されます。

    ![[SAML] が強調表示された [EasySSO Admin]\(EasySSO 管理\) ページのスクリーンショット](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-3.png)

1. **[Certificates]\(証明書\)** タブを選択すると、次の画面が表示されます。

    ![各種オプションが強調表示された [Certificates]\(証明書\) タブのスクリーンショット](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-4.png)

1. このチュートリアルの前のセクションで保存した**証明書 (Base64)** または**メタデータ ファイル**を見つけます。 次のいずれの方法で続行できます。

    - 自分のコンピューター上のローカル ファイルとしてダウンロードした、アプリのフェデレーション **メタデータ ファイル**を使用します。 **[Upload]\(アップロード\)** をクリックし、ご使用のオペレーティング システムに固有のパスに従います。

    - 任意のプレーンテキスト エディターでアプリのフェデレーション **メタデータ ファイル**を開き、ファイルの内容を確認します。 それをクリップボードにコピーします。 **[Input]\(入力\)** を選択し、クリップボードの内容をテキスト フィールドに貼り付けます。
 
    - すべて手動で構成します。 任意のプレーンテキスト エディターでアプリのフェデレーション**証明書 (Base64)** を開き、ファイルの内容を確認します。 それをクリップボードにコピーし、 **[IdP Token Signing Certificates]\(IdP トークン署名証明書\)** テキスト フィールドに貼り付けます。 次に、 **[General]\(全般\)** タブに移動し、 **[POST Binding URL]\(POST バインディング URL\)** および **[Entity ID]\(エンティティ ID\)** フィールドに、前に保存した **[ログイン URL]** と **[Azure AD 識別子]** の値をそれぞれ入力します。
 
1. ページの下部にある **[保存]** を選択します。 メタデータ ファイルまたは証明書ファイルの内容が構成フィールドで解析されていることを確認できます。 これで、EasySSO for BitBucket の構成は完了しました。

1. 構成をテストするには、 **[Look & Feel]\(外観\)** タブに移動し、 **[SAML Login Button]\(SAML ログイン ボタン\)** を選択します。 これにより、BitBucket サインイン画面の独立したボタンが有効になり、Azure AD SAML 統合をエンド ツー エンドでテストできるようになります。 このボタンをオンのままにすることで、運用モードでの配置、色、および翻訳を構成することもできます。

    ![[SAML Login Button]\(SAML ログイン ボタン\) が強調表示された [Look & Feel]\(外観\) タブのスクリーンショット](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-5.png)
    > [!NOTE]
    >問題がある場合は、[EasySSO サポート チーム](mailto:support@techtime.co.nz)にお問い合わせください。

### <a name="create-an-easysso-for-bitbucket-test-user"></a>EasySSO for BitBucket のテスト ユーザーの作成

このセクションでは、BitBucket で Britta Simon というユーザーを作成します。 EasySSO for BitBucket では Just-In-Time ユーザー プロビジョニングがサポートされており、既定では無効になっています。 有効にするには、EasySSO プラグイン構成の **[General]\(全般\)** セクションで、 **[Create user on successful login]\(ログイン成功時にユーザーを作成する\)** オプションを明示的にオンにする必要があります。 BitBucket にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

ただし、ユーザーが初めてサインインしたときに自動ユーザー プロビジョニングを有効にしない場合は、BitBucket のインスタンスが使用するユーザー ディレクトリにユーザーが存在している必要があります。 このディレクトリは、たとえば LDAP や Atlassian Crowd です。

![[Create user on successful login]\(ログイン成功時にユーザーを作成する\) が強調表示された EasySSO プラグイン構成の [General]\(全般\) セクションのスクリーンショット](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-6.png)

## <a name="test-sso"></a>SSO のテスト 

### <a name="idp-initiated-workflow"></a>IdP-Initiated ワークフロー

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

[EasySSO for BitBucket] タイルを選択すると、SSO を設定した BitBucket インスタンスに自動的にサインインします。 詳細については、「[マイ アプリ ポータルからアプリにサインインして開始する](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)」を参照してください。

### <a name="sp-initiated-workflow"></a>SP-Initiated ワークフロー

このセクションでは、BitBucket の **[SAML Login]\(SAML ログイン\)** ボタンを使用して Azure AD のシングル サインオン構成をテストします。

![[SAML Login]\(SAML ログイン\) が強調表示された [Log in]\(ログイン\) 画面のスクリーンショット](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-7.png)

このシナリオは、BitBucket EasySSO の構成ページの **[Look & Feel]\(外観\)** タブで **[SAML Login Button]\(SAML ログイン ボタン\)** を有効にしていることが前提になっています。 既存のセッションとの干渉を避けるために、シークレット モードにしたブラウザーで BitBucket のログイン URL を開きます。 **[SAML Login]\(SAML ログイン\)** を選択すると、Azure AD のユーザー認証フローにリダイレクトされます。 これが正常に完了すると、認証済みユーザーとして SAML を介して元の BitBucket インスタンスにリダイレクトされます。

Azure AD からのリダイレクト後に、次の画面が表示される場合があります。

![参照番号が強調表示された EasySSO エラー画面のスクリーンショット](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-8.png)

その場合には、[こちらのページの手順](https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS)に従って **atlassian-bitbucket.log** ファイルにアクセスする必要があります。 EasySSO エラー ページにある参照 ID を使って、エラーの詳細を確認できます。

問題がある場合は、[EasySSO サポート チーム](mailto:support@techtime.co.nz)にお問い合わせください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリケーションと Azure Active Directory との統合に関するチュートリアル](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で EasySSO for BitBucket を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって EasySSO for BitBucket を保護する](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

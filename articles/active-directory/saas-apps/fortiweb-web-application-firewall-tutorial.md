---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と FortiWeb Web Application Firewall の統合 | Microsoft Docs
description: Azure Active Directory と FortiWeb Web Application Firewall の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/24/2020
ms.author: jeedes
ms.openlocfilehash: e34664bd81023da7a50b8ff4645c670146ef2554
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98731938"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortiweb-web-application-firewall"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と FortiWeb Web Application Firewall の統合

このチュートリアルでは、FortiWeb Web Application Firewall と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と FortiWeb Web Application Firewall を統合すると、次のことができます。

* FortiWeb Web Application Firewall にアクセスできるユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して FortiWeb Web Application Firewall に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* FortiWeb Web Application Firewall でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* FortiWeb Web Application Firewall では、**SP** Initiated SSO がサポートされます。

## <a name="adding-fortiweb-web-application-firewall-from-the-gallery"></a>ギャラリーからの FortiWeb Web Application Firewall の追加

FortiWeb Web Application Firewall の Azure AD への統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に FortiWeb Web Application Firewall を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**FortiWeb Web Application Firewall**」と入力します。
1. 結果パネルから **[FortiWeb Web Application Firewall]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-fortiweb-web-application-firewall"></a>FortiWeb Web Application Firewall の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、FortiWeb Web Application Firewall に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと FortiWeb Web Application Firewall の関連ユーザーとの間にリンク関係を確立する必要があります。

FortiWeb Web Application Firewall に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[FortiWeb Web Application Firewall の SSO の構成](#configure-fortiweb-web-application-firewall-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[FortiWeb Web Application Firewall テスト ユーザーの作成](#create-fortiweb-web-application-firewall-test-user)** - FortiWeb Web Application Firewall で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **FortiWeb Web Application Firewall** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

   a. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://www.<CUSTOMER_DOMAIN>.com`

    b. **[応答 URL]** ボックスに、`https://www.<CUSTOMER_DOMAIN>.com/<FORTIWEB_NAME>/saml.sso/SAML2/POST` のパターンを使用して URL を入力します

    c. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://www.<CUSTOMER_DOMAIN>.com`

    d. **[ログアウト URL]** テキスト ボックスに、`https://www.<CUSTOMER_DOMAIN>.info/<FORTIWEB_NAME>/saml.sso/SLO/POST` のパターンを使用して URL を入力します。
 
    > [!NOTE]
    > `<FORTIWEB_NAME>` は、後で FortiWeb に対して構成を指定するときに使用される名前識別子です。
    > 実際の URL 値を取得するには、[FortiWeb Web Application Firewall サポート チーム](mailto:support@fortinet.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)


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

このセクションでは、B.Simon に FortiWeb Web Application Firewall へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[FortiWeb Web Application Firewall]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-fortiweb-web-application-firewall-sso"></a>FortiWeb Web Application Firewall の SSO の構成

1.  `https://<address>:8443` に移動します。ここで `<address>` は、FortiWeb VM に割り当てられている FQDN またはパブリック IP アドレスです。

2.  FortiWeb VM のデプロイ中に指定された管理者資格情報を使用してサインインします。

1. 次のページで、以下の手順を実行します。

    ![[SAML server]\(SAML サーバー\) ページ](./media/fortiweb-web-application-firewall-tutorial/configure-sso.png)

    a.  左側のメニューで、 **[User]\(ユーザー\)** をクリックします。

    b.  [User]\(ユーザー\) で、 **[Remote Server]\(リモート サーバー\)** をクリックします。

    c.  **[SAML Server]\(SAML サーバー\)** をクリックします。

    d.  [**新規作成**] をクリックします。

    e.  **[Name]\(名前\)** フィールドに、[Azure AD の構成] セクションで使用される `<fwName>` の値を指定します。

    f.  **[エンティティ ID]** ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    g. **[Metadata]\(メタデータ\)** の横にある **[Choose File]\(ファイルの選択\)** をクリックして、Azure portal からダウンロードした **フェデレーション メタデータ XML** ファイルを選択します。

    h.  **[OK]** をクリックします。

### <a name="create-a-site-publishing-rule"></a>サイト公開ルールの作成

1.  `https://<address>:8443` に移動します。ここで `<address>` は、FortiWeb VM に割り当てられている FQDN またはパブリック IP アドレスです。

1.  FortiWeb VM のデプロイ中に指定された管理者資格情報を使用してサインインします。
1. 次のページで、以下の手順を実行します。

    ![[Site Publishing Rule]\(サイト公開ルール\)](./media/fortiweb-web-application-firewall-tutorial/site-publish-rule.png)

    a.  左側のメニューで、 **[Application Delivery]\(アプリケーションのデリバリー\)** をクリックします。
    
    b.  **[Application Delivery]\(アプリケーションのデリバリー\)** の **[Site Publish]\(サイトの公開\)** をクリックします。
    
    c.  **[Site Publish]\(サイトの公開\)** の **[Site Publish]\(サイトの公開\)** をクリックします。
    
    d.  **[Site Publish Rule]\(サイト公開ルール\)** をクリックします。
    
    e.  [**新規作成**] をクリックします。
    
    f.  サイト公開ルールの名前を指定します。
    
    g.  **[Published Site Type]\(公開されたサイトの種類\)** の横にある **[Regular Expression]\(正規表現\)** をクリックします。
    
    i.  **[Published Site]\(公開されたサイト\)** の横で、公開する Web サイトのホスト ヘッダーと一致する文字列を指定します。
    
    j.  **[Path]\(パス\)** の横で「/」と入力します。
    
    k.  **[Client Authentication Method]\(クライアントの認証方法\)** の横で、 **[SAML Authentication]\(SAML 認証\)** を選択します。
    
    l.  **[SAML Server]\(SAML サーバー\)** ドロップダウンで、前に作成した SAML サーバーを選択します。
    
    m.  **[OK]** をクリックします。


### <a name="create-a-site-publishing-policy"></a>サイト公開ポリシーの作成

1.  `https://<address>:8443` に移動します。ここで `<address>` は、FortiWeb VM に割り当てられている FQDN またはパブリック IP アドレスです。

2.  FortiWeb VM のデプロイ中に指定された管理者資格情報を使用してサインインします。

1. 次のページで、以下の手順を実行します。

    ![サイト公開ポリシー](./media/fortiweb-web-application-firewall-tutorial/site-publish-policy.png)

    a.  左側のメニューで、 **[Application Delivery]\(アプリケーションのデリバリー\)** をクリックします。

    b.  **[Application Delivery]\(アプリケーションのデリバリー\)** の **[Site Publish]\(サイトの公開\)** をクリックします。

    c.  **[Site Publish]\(サイトの公開\)** の **[Site Publish]\(サイトの公開\)** をクリックします。

    d.  **[Site Publish Policy]\(サイト公開ポリシー\)** をクリックします。

    e.  [**新規作成**] をクリックします。

    f.  サイト公開ポリシーの名前を指定します。

    g.  **[OK]** をクリックします。

    h.  [**新規作成**] をクリックします。

    i.  **[Rule]\(ルール\)** ドロップダウンで、前に作成したサイト公開ルールを選択します。

    j.  **[OK]** をクリックします。

### <a name="create-and-assign-a-web-protection-profile"></a>Web 保護プロファイルの作成と割り当て

1.  `https://<address>:8443` に移動します。ここで `<address>` は、FortiWeb VM に割り当てられている FQDN またはパブリック IP アドレスです。

2.  FortiWeb VM のデプロイ中に指定された管理者資格情報を使用してサインインします。
3.  左側のメニューで、 **[Policy]\(ポリシー\)** をクリックします。
4.  **[Policy]\(ポリシー\)** で、 **[Web Protection Profile]\(Web 保護プロファイル\)** をクリックします。
5.  **[Inline Standard Protection]\(インラインの標準保護\)** をクリックしてから、 **[Clone]\(クローン\)** をクリックします。
6.  新しい Web 保護プロファイルの名前を指定し、 **[OK]** をクリックします。
7.  新しい Web 保護プロファイルを選択し、 **[Edit]\(編集\)** をクリックします。
8.  **[Site Publish]\(サイトの公開\)** の横で、前に作成したサイト公開ポリシーを選択します。
9.  **[OK]** をクリックします。
 
    ![サイトの公開](./media/fortiweb-web-application-firewall-tutorial/web-protection.png)

10. 左側のメニューで、 **[Policy]\(ポリシー\)** をクリックします。
11. **[Policy]\(ポリシー\)** で **[Server Policy]\(サーバー ポリシー\)** をクリックします。
12. Azure Active Directory を使用して認証したい Web サイトの公開に使用されるサーバー ポリシーを選択します。
13. **[編集]** をクリックします。
14. **[Web Protection Profile]\(Web 保護プロファイル\)** ドロップダウンで、作成したばかりの Web 保護プロファイルを選択します。
15. **[OK]** をクリックします。
16. FortiWeb の Web サイト公開先となる外部 URL に対するアクセスを試行します。 認証のために Azure Active Directory にリダイレクトされるはずです。


### <a name="create-fortiweb-web-application-firewall-test-user"></a>FortiWeb Web Application Firewall テスト ユーザーの作成

このセクションでは、FortiWeb Web Application Firewall で Britta Simon というユーザーを作成します。 [FortiWeb Web Application Firewall サポート チーム](mailto:support@fortinet.com)と連携し、FortiWeb Web Application Firewall プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始する FortiWeb Web Application のサインオン URL にリダイレクトされます。 

* FortiWeb Web Application のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [FortiWeb Web Application] タイルをクリックすると、FortiWeb Web Application のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

FortiWeb Web Application Firewall を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を強制することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
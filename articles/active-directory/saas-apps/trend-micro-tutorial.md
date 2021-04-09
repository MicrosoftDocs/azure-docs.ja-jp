---
title: チュートリアル:Azure AD SSO と Trend Micro Web Security (TMWS) の統合
description: Azure Active Directory と Trend Micro Web Security (TMWS) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/21/2020
ms.author: jeedes
ms.openlocfilehash: b76c41787d7a35fb3024fa18c0122bc966243bbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008397"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-security-tmws"></a>チュートリアル:Trend Micro Web Security (TMWS) と Azure Active Directory のシングル サインオン (SSO) 統合

このチュートリアルでは、Azure Active Directory (Azure AD) と Trend Micro Web Security (TMWS) を統合する方法について説明します。 Azure AD と TMWS を統合すると、次のことができます。

* TMWS にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して TMWS に自動的にサインインできるようになります。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、以下が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* SSO が有効になっている TMWS サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* TMWS では、SP Initiated SSO がサポートされます。
* TMWS を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 Microsoft Cloud App Security を使用してセッション制御を適用する方法については、「[任意のアプリについてアプリの条件付きアクセス制御をオンボードして展開する](/cloud-app-security/proxy-deployment-any-app)」を参照してください。

## <a name="add-tmws-from-the-gallery"></a>ギャラリーから TMWS を追加する

Azure AD への TMWS の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に TMWS を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左側のウィンドウで、 **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Trend Micro Web Security (TMWS)** 」と入力します。
1. 検索結果で **[Trend Micro Web Security (TMWS)]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-tmws"></a>TMWS の Azure AD SSO の構成とテスト

B.Simon というテスト ユーザーを使用して、TMWS に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと TMWS の関連ユーザーとの間にリンクを確立する必要があります。

次の基本的な手順を実行して、TMWS との Azure AD SSO を構成し、テストします。

1. [Azure AD SSO を構成して](#configure-azure-ad-sso)、この機能をユーザーに対して有効にします。
    1. [Azure AD ユーザーを作成](#create-an-azure-ad-test-user)して、Azure AD のシングル サインオンをテストします。
    1. [Azure AD テスト ユーザーに TMWS へのアクセスを許可します](#grant-the-azure-ad-test-user-access-to-tmws)。
    1. [Azure AD でユーザーとグループの同期設定を構成します](#configure-user-and-group-synchronization-settings-in-azure-ad)。
1. アプリケーション側で [TMWS SSO を構成します](#configure-tmws-sso)。
1. [SSO をテスト](#test-sso)して、構成を確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

次の手順を実行して、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) で **Trend Micro Web Security (TMWS)** アプリケーション統合ページの **[管理]** セクションに移動し、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** のペン ボタンを選択して設定を編集します。

   ![[基本的な SAML 構成] の設定を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のボックスに値を入力します。

    a. **[識別子 (エンティティ ID)]** ボックスに、次のパターンで URL を入力します。

    `https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. **[応答 URL]** ボックスに、次の URL を入力します。

    `https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > 前の手順の識別子の値は、実際に入力する値ではありません。 実際の識別子を使用する必要があります。 この値は、Azure AD の **[認証方法]** ページの **[Service Provider Settings for the Azure Admin Portal]\(Azure 管理ポータルのサービス プロバイダー設定\)** セクションで **[管理]> [ディレクトリ サービス]** から取得できます。

1. TMWS は、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットは、既定の属性を示しています。

    ![既定の属性](common/default-attributes.png)

1. 上記のスクリーンショットの属性に加えて、TMWS では、他に 2 つの属性が SAML 応答で返されることが想定されています。 これらの属性を次の表に示します。 これらの属性値は事前に設定されますが、要件に合わせて変更できます。
    
    | 名前 | ソース属性|
    | --------------- | --------- |
    | sAMAccountName | user.onpremisessamaccountname |
    | uPN | user.userprincipalname |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけます。 証明書名の横の **[ダウンロード]** リンクを選択し、証明書をダウンロードしてご利用のコンピューターに保存します。

    ![証明書のダウンロード リンク](common/certificatebase64.png)

1. **[Trend Micro Web Security (TMWS) のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL をコピーする](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左ペインで、 **[Azure Active Directory]** を選択します。 **[ユーザー]** を選択し、 **[すべてのユーザー]** を選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** ボックスに、「`B.Simon`」と入力します。  
   1. **[ユーザー名]** ボックスに、「**_username_@* companydomain *.* extension***」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** をオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **［作成］** を選択します

### <a name="grant-the-azure-ad-test-user-access-to-tmws"></a>Azure AD テスト ユーザーに TMWS へのアクセスを許可する

このセクションでは、B.Simon に TMWS へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[Trend Micro Web Security (TMWS)]** を選択します。
1. アプリの概要ページの **[管理]** セクションで、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] の選択](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] を選択する](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログ ボックスで、**ユーザー** の一覧で **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーションにロール値が必要な場合は、 **[ロールの選択]** ダイアログ ボックスでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Azure AD でのユーザーとグループの同期設定の構成

1. 左ウィンドウで、 **[Azure Active Directory]** を選択します。

1. **[管理]** で、 **[アプリの登録]** を選択し、 **[すべてのアプリケーション]** で新しいエンタープライズ アプリケーションを選択します。

1. **[管理]** で、 **[証明書とシークレット]** を選択します。

1. **[クライアント シークレット]** 領域で、 **[新しいクライアント シークレット]** を選択します。

1. **[クライアント シークレットの追加]** 画面で、必要に応じて説明を追加し、クライアント シークレットの有効期限を選択してから、 **[追加]** を選択します。 **[クライアント シークレット]** 領域に、新しいクライアント シークレットが表示されます。

1. クライアント シークレットの値を記録します。 後ほど、これを TMWS に入力します。

1. **[管理]** の下にある **[API のアクセス許可]** を選択します。 

1. **[API のアクセス許可]** ウィンドウで、 **[アクセス許可の追加]** を選択します。

1. **[API アクセス許可の要求]** ウィンドウの **[Microsoft API]** タブで、 **[Microsoft Graph]** 、 **[アプリケーションの許可]** の順に選択します。

1. 次のアクセス許可を検索して追加します。 

    * Group.Read.All
    * User.Read.All

1. **[アクセス許可の追加]** を選択します. 設定が保存されたことを確認するメッセージが表示されます。 新しいアクセス許可が、 **[API のアクセス許可]** ウィンドウに表示されます。

1. **[同意する]** 領域で、 **[ *<管理者アカウント>* に管理者の同意を与えます (既定のディレクトリ)]** を選択し、 **[はい]** を選択します。 要求したアクセス許可に対して管理者の同意が与えられたことを確認するメッセージが表示されます。

1. **[概要]** を選択します。 

1. 右側のペインに表示される **[アプリケーション (クライアント) ID]** と **[ディレクトリ (テナント) ID]** の値を記録します。 後ほど、この情報を TMWS に入力します。 また、 **[Azure Active Directory] > [管理]** の **[カスタム ドメイン名]** を選択し、右側のペインに表示されるドメイン名を記録してもかまいません。

## <a name="configure-tmws-sso"></a>TMWS SSO を構成する

TMWS SSO を構成するには、アプリケーション側で次の手順を完了します。

1. TMWS 管理コンソールにサインインし、 **[Administration]\(管理\)**  >  **[USERS & AUTHENTICATION]\(ユーザーと認証\)**  >  **[Directory Services]\(ディレクトリ サービス\)** に移動します。

1. 画面の上部の領域の **[here]\(ここ\)** をクリックします。

1. **[Authentication Method]\(認証方法\)** ページで、 **[Azure AD]** を選択します。

1. **[On]\(オン\)** または **[Off]\(オフ\)** を選択して、組織の Azure AD ユーザーのデータが TMWS に同期されていない場合に、ユーザーが TMWS を通じて Web サイトにアクセスできるようにするかどうかを構成します。

    > [!NOTE]
    > Azure AD から同期されないユーザーは、既知の TMWS ゲートウェイまたは組織の専用ポートを介してのみ認証できます。

1. **[Identity Provider Settings]\(ID プロバイダーの設定\)** セクションで、次の手順を実行します。

    a. **[Service URL]\(サービス URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を入力します。

    b. **[Logon name attribute]\(ログオン名属性\)** ボックスに、Azure portal から取得した **ユーザー要求名** を **user.onpremisessamaccountname** ソース属性と共に入力します。

    c. **[Public SSL certificate]\(公開 SSL 証明書\)** ボックスには、Azure portal からダウンロードした **証明書 (Base64)** を使用します。

1. **[Synchronization Settings]\(同期設定\)** セクションで、次の手順を実行します。

    a. **[Tenant]\(テナント\)** ボックスに、Azure portal から取得した **[ディレクトリ (テナント) ID]** または **[カスタム ドメイン名]** の値を入力します。

    b. **[Application ID]\(アプリケーション ID\)** ボックスに、Azure portal から取得した **[アプリケーション (クライアント) ID]** の値を入力します。

    c. **[Client secret]\(クライアント シークレット\)** ボックスに、Azure portal から取得した **クライアント シークレット** を入力します。

    d. **[Synchronization schedule]\(同期スケジュール\)** を選択し、手動で、またはスケジュールに従って Azure AD との同期を行います。 **[Manually]\(手動\)** を選択した場合は、Active Directory ユーザー情報に変更があるたびに必ず **[Directory Services]\(ディレクトリ サービス\)** ページに戻って手動同期を実行し、TMWS の情報を最新の状態に保つようにしてください。

    e. **[Test Connection]\(接続のテスト\)** を選択して、Azure AD サービスに正常に接続できるかどうかを確認します。
    
    f. **[保存]** を選択します。
 
 > [!NOTE]
 > TMWS で Azure AD を構成する方法の詳細については、「[TMWS での Azure AD 設定の構成](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx)」を参照してください。

## <a name="test-sso"></a>SSO のテスト 

Azure AD サービスを構成し、ユーザーの認証方法として Azure AD を指定したら、TMWS プロキシ サーバーにサインインしてセットアップを確認することができます。 Azure AD サインインによってアカウントが確認されたら、インターネットにアクセスできます。

> [!NOTE]
> TMWS では、Azure AD ポータルの **[概要]**  >  **[シングル サインオン]**  >  **[SAML によるシングル サインオンのセットアップ]**  >  **[テスト]** からの新規エンタープライズ アプリケーションのシングル サインオンのテストがサポートされていません。

1. ブラウザーからすべての Cookie をクリアし、ブラウザーを再起動します。 

1. ブラウザーで TMWS プロキシ サーバーにアクセスします。 詳細については、「[PAC ファイルを使用したトラフィックの転送](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B)」を参照してください。

1. インターネットの任意の Web サイトにアクセスします。 TMWS により、TMWS のキャプティブ ポータルに移動します。

1. Active Directory アカウント (形式: *domain*\\*sAMAccountName* または *sAMAccountName*@*domain*)、メール アドレス、または UPN を指定し、 **[Log On]\(ログオン\)** を選択します。 TMWS により Azure AD サインイン ウィンドウにリダイレクトされます。

1. Azure AD サインイン ウィンドウで、Azure AD アカウントの資格情報を入力します。 これで TMWS にサインインできます。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアル](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Trend Micro Web Security を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)

- [高度な可視性とコントロールを使用して Trend Micro Web Security を保護する方法](/cloud-app-security/proxy-intro-aad)
---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Oracle PeopleSoft - Protected by F5 BIG-IP APM の統合 | Microsoft Docs
description: Azure Active Directory と Oracle PeopleSoft - Protected by F5 BIG-IP APM の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: jeedes
ms.openlocfilehash: 3b7c8e024ac8361c08cc41195531a114bb12fcb4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92522293"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Oracle PeopleSoft - Protected by F5 BIG-IP APM の統合

このチュートリアルでは、Oracle PeopleSoft - Protected by F5 BIG-IP APM と Azure Active Directory (Azure AD) を統合する方法について説明します。 Oracle PeopleSoft - Protected by F5 BIG-IP APM を Azure AD と統合すると、次のことができます。

* Oracle PeopleSoft - Protected by F5 BIG-IP APM にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して Oracle PeopleSoft - Protected by F5 BIG-IP APM に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。
* このチュートリアルでは、Oracle PeopleSoft ELM の手順について説明します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

1. Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
1. Oracle PeopleSoft - Protected by F5 BIG-IP APM でのシングル サインオン (SSO) が有効なサブスクリプション。

1. 共同ソリューションをデプロイするには次のライセンスが必要です。

    1. F5 BIG-IP® Best バンドル (または) 
    2. F5 BIG-IP Access Policy Manager™ (APM) スタンドアロン ライセンス 
    3. 既存の BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM) に対する F5 BIG-IP Access Policy Manager™ (APM) アドオン ライセンス
    4. 上記のライセンスに加え、F5 システムには次のライセンスが付属する場合があります。 
        * URL カテゴリ データベースを使用するための URL フィルタリング サブスクリプション 
        * 既知の攻撃者や悪意のあるトラフィックを検出してブロックするための F5 IP Intelligence サブスクリプション 
        * 強力な認証用のデジタル キーを保護、管理するためのネットワーク ハードウェア セキュリティ モジュール (HSM)
1. F5 BIG-IP システムは、APM モジュールと共にプロビジョニングされます (LTM はオプション)。 
1. 必須ではありませんが、F5 システムは [Sync/Failover Device Group](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG) にデプロイすることを強くお勧めします。S/F DG には、アクティブとスタンバイのペアが含まれており、フローティング IP アドレスによる高可用性 (HA) が実現されます。 Link Aggregation Control Protocol (LACP) を使用すれば、さらなるインターフェイスの冗長性を実現できます。 LACP は、接続されている物理インターフェイスを 1 つの仮想インターフェイス (集計グループ) として管理し、そのグループ内のインターフェイスに発生したエラーを検出します。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Oracle PeopleSoft - Protected by F5 BIG-IP APM では、**SP および IDP** Initiated SSO がサポートされます

## <a name="adding-oracle-peoplesoft---protected-by-f5-big-ip-apm-from-the-gallery"></a>ギャラリーからの Oracle PeopleSoft - Protected by F5 BIG-IP APM の追加

Azure AD への Oracle PeopleSoft - Protected by F5 BIG-IP APM の統合を構成するには、マネージド SaaS アプリの一覧にギャラリーから Oracle PeopleSoft - Protected by F5 BIG-IP APM を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Oracle PeopleSoft - Protected by F5 BIG-IP APM**」と入力します。
1. 結果パネルで **[Oracle PeopleSoft - Protected by F5 BIG-IP APM]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>Azure AD SSO for Oracle PeopleSoft - Protected by F5 BIG-IP APM の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Oracle PeopleSoft - Protected by F5 BIG-IP APM に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと Oracle PeopleSoft - Protected by F5 BIG-IP APM の関連ユーザーとの間にリンク関係を確立する必要があります。

Oracle PeopleSoft - Protected by F5 BIG-IP APM で Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Oracle PeopleSoft-Protected by F5 BIG-IP APM SSO の構成](#configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Oracle PeopleSoft - Protected by F5 BIG-IP APM のテスト ユーザーの作成](#create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user)** - Oracle PeopleSoft - Protected by F5 BIG-IP APM で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクします。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Oracle PeopleSoft - Protected by F5 BIG-IP APM** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://<FQDN>.peoplesoft.f5.com` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/post/acs` のパターンを使用して URL を入力します

    c. **[ログアウト URL]** テキスト ボックスに、`https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/redirect/slr` のパターンを使用して URL を入力します。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<FQDN>.peoplesoft.f5.com/` という形式で URL を入力します。

    > [!NOTE]
    >これらは実際の値ではありません。 これらの値は、実際のサインオン URL、識別子、応答 URL、およびログアウト URL で更新してください。 この値を取得するには、[Oracle PeopleSoft - Protected by F5 BIG-IP APM のクライアント サポート チーム](https://support.f5.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. Oracle PeopleSoft - Protected by F5 BIG-IP APM アプリケーションでは、特定の形式の SAML アサーションを受け取るため、SAML トークン属性の構成にカスタム属性マッピングを追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. 上記に加えて、Oracle PeopleSoft - Protected by F5 BIG-IP APM アプリケーションでは、以下のような、SAML 応答で返される少数の属性が想定されています。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 |  ソース属性|
    | ------------------ | --------- |
    | EMPLID | user.employeeid |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**フェデレーション メタデータ XML** と **証明書 (Base64)** をダウンロードし、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/both-certificate.png)

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

このセクションでは、B.Simon に Oracle PeopleSoft - Protected by F5 BIG-IP APM へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[Oracle PeopleSoft - Protected by F5 BIG-IP APM]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso"></a>Oracle PeopleSoft-Protected by F5 BIG-IP APM SSO の構成

### <a name="f5-saml-sp-configuration"></a>F5 SAML SP の構成

後で設定プロセスで使用するメタデータ証明書を F5 にインポートします。 **[System]\(システム\) > [Certificate Management]\(証明書の管理\) > [Traffic Certificate Management]\(トラフィック証明書管理\) > [SSL Certificate List]\(SSL 証明書リスト\)** の順に移動します。 右隅の **[Import]\(インポート\)** を選択します。

![F5 SAML SP の構成](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sp-configuration.png)

#### <a name="setup-the-saml-idp-connector"></a>SAML IDP コネクタの設定 

1. **[Access]\(アクセス\)、[Federation]\(フェデレーション\)、[SAML: Service Provider]\(SAML: サービス プロバイダー\)、[External Idp Connectors]\(外部 Idp コネクタ\)** の順に移動し、 **[Create]\(作成\)、[From Metadata]\(メタデータから\)** の順にクリックします。

    ![F5 SAML IDP コネクタ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/saml-idp-connector.png)

1. 次のページで **[Browse]\(参照\)** をクリックして、xml ファイルをアップロードします。

1. **[Identity Provider Name]\(ID プロバイダー名\)** テキスト ボックスに有効な名前を入力し、 **[OK]** をクリックします。

    ![新しい SAML IDP コネクタ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-idp.png)

1. **[Security Settings]\(セキュリティ設定\)** タブで必要な手順を行い、 **[OK]** をクリックします。

    ![SAML IDP コネクタを編集する](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp.png)

#### <a name="setup-the-saml-sp"></a>SAML SP の設定

1. **[Access]\(アクセス\)、[Federation]\(フェデレーション\)、[SAML Service Provider]\(SAML サービス プロバイダー\)、[Local SP Services]\(ローカル SP サービス\)** の順に移動して、 **[Create]\(作成\)** をクリックします。 次の情報を入力して **[OK]** をクリックします。

    * 名前: `<Name>`
    * エンティティ ID: `https://<FQDN>.peoplesoft.f5.com`
    * [SP Name Settings]\(SP 名の設定\)
        * スキーム: `https`
        * ホスト: `<FQDN>.peoplesoft.f5.com`
        * 説明: `<Description>`

    ![新しい SAML SP サービス](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-sp-service.png)

1. SP 構成である PeopleSoftAppSSO を選択し、 **[Bind/UnBind IdP Connectors]\(IdP コネクタのバインドまたはバインド解除\) をクリック** します。
**[Add New Row]\(新しい行の追加\)** をクリックし、前の手順で作成した **外部 IdP コネクタ** を選択して、 **[Update]\(更新\)** 、 **[OK]** の順にクリックします。

    ![SAML SP サービスを作成する](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp-use-sp.png)

## <a name="configuring-application"></a>アプリケーションの構成

### <a name="create-a-new-pool"></a>新しいプールの作成
1. **[Local Traffic]\(ローカル トラフィック\)、[Pools]\(プール\)、[Pool List]\(プール リスト\)** の順に移動して、 **[Create]\(作成\)** をクリックし、次の情報を入力して **[Finished]\(完了\)** をクリックします。

    * 名前: `<Name>`
    * 説明: `<Description>`
    * 正常性モニター: `http`
    * アドレス: `<Address>`
    * サービス ポート: `<Service Port>`

    ![新しいプールの作成](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/create-pool.png)


### <a name="create-a-new-client-ssl-profile"></a>新しいクライアント SSL プロファイルの作成

**[Local Traffic]\(ローカル トラフィック\)、[Profiles]\(プロファイル\)、[SSL]、[Client]\(クライアント\)、[+]** の順に移動し、以下の情報を指定して、 **[Finished]\(完了\)** をクリックします。

* 名前: `<Name>`
* 証明書: `<Certificate>`
* キー: `<Key>`

![新しいクライアント SSL プロファイル](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/client-ssl-profile.png)

### <a name="create-a-new-virtual-server"></a>新しい仮想サーバーの作成

1. **[Local Traffic]\(ローカル トラフィック\)、[Virtual Servers]\(仮想サーバー\)、[Virtual Server List]\(仮想サーバー リスト\)、[+]** の順に移動し、次の情報を入力して **[Finished]\(完了\)** をクリックします。
    * 名前: `<Name>`
    * 接続先のアドレスおよびマスク: `<Address>`
    * [Service Port]\(サービス ポート\): [Port]\(ポート\) 443 HTTPS
    * HTTP プロファイル (クライアント): http

    ![新しい仮想サーバーの作成](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-list.png)

1. 次のページで、以下の値を入力します。

    * SSL プロファイル (クライアント): `<SSL Profile>`
    * ソース アドレス変換: Auto Map (自動マップ)
    * アクセス プロファイル: `<Access Profile>`
    * 既定のプール: `<Pool>`


    ![新しい仮想サーバー peoplesoft プールを作成する](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-people-soft.png)

## <a name="setting-up-peoplesoft-application-to-support-f5-big-ip-apm-as-the-single-sign-on-solution"></a>シングル サインオン ソリューションとして F5 Big-IP APM をサポートするための PeopleSoft アプリケーションの設定

>[!Note]
> https://docs.oracle.com/cd/E12530_01/oam.1014/e10356/people.htm を参照してください

1. 管理者の資格情報を使用して Peoplesoft コンソール `https://<FQDN>.peoplesoft.f5.com/:8000/psp/ps/?cmd=start` にログオンします (例: PS/PS)

    ![マネージャー セルフ サービス](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-console.png)

1. PeopleSoft アプリケーションで、新しいユーザー プロファイルとして **OAMPSFT** を作成し、**PeopleSoft User** などの低いセキュリティ ロールを関連付けます。
**[Peopletools]、[Security]\(セキュリティ\)、[User Profiles]\(ユーザー プロファイル\)、[User Profiles]\(ユーザー プロファイル\)** の順に移動し、新しいユーザー プロファイル (たとえば **OAMPSFT**) を作成して、**Peoplesoft User** を追加します。

    ![Peoplesoft User](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/user-profile.png)

1. Web プロファイルにアクセスし、パブリック アクセス **ユーザー ID** として「**OAMPSFT**」と入力します。

    ![ユーザー プロファイル](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/web-profile-configuration.png)

1. **[PeopleTools Application Designer]\(PeopleTools アプリケーション デザイナー\)** で、**FUNCLIB_LDAP** レコードを開きます。

    ![Web プロファイルの構成](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/funclib.png)

1. **OAMSSO_AUTHENTICATION** 関数の **PS_SSO_UID** のユーザー ヘッダーを更新します。
**getWWWAuthConfig()** 関数で、 **&defaultUserId** に割り当てられている値を、Web プロファイルで定義した **OAMPSFT** に置き換えます。 レコード定義を保存します。

    ![OAMSSO_AUTHENTICATION](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/record.png)

1. **[Signon PeopleCode]\(サインオン PeopleCode\)** ページ ([PeopleTools]、[Security]\(セキュリティ\)、[Security Objects]\(セキュリティ オブジェクト\)、[Signon PeopleCode]\(サインオン PeopleCode\)) にアクセスし、**OAMSSO_AUTHENTICATION** 機能 (Oracle Access Manager シングル サインオンのためのサインオン PeopleCode) を有効にします。

    ![OAMSSO_AUTHENTICATION ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-on-people-soft.png)

## <a name="setting-up-f5-big-ip-apm-to-populate-ps_sso_uid-http-header-with-the-peoplesoft-user-id"></a>"PS_SSO_UID" HTTP ヘッダーに PeopleSoft ユーザー ID を指定する F5 Big-IP APM の設定

### <a name="configuring-per-request-policy"></a>要求ごとのポリシーの構成
1. **[Access]\(アクセス\)、[Profile/Policies]\(プロファイルまたはポリシー\)、[Per-Request Policies]\(要求ごとのポリシー\)** の順に移動して **[Create]\(作成\)** をクリックし、次の情報を入力して **[Finished]\(完了\)** をクリックします。

    * 名前: `<Name>`
    * [Profile Type]\(プロファイルの種類\): All
    * 言語: `<Language>`

    ![要求ごとのポリシーの構成 ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/per-request.png)

1. 要求ごとのポリシー `<Name>` について **[Edit]\(編集\)** をクリックします ![PeopleSoftSSO の要求ごとのポリシーを編集する ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso.png)

    `Header Name: <Header Name>`   
    `Header Value: <Header Value>`

### <a name="assign-per-request-policy-to-the-virtual-server"></a>仮想サーバーへの要求ごとのポリシーの割り当て

**[Local Traffic]\(ローカル トラフィック\)、[Virtual Servers]\(仮想サーバー\)、[Virtual Server List]\(仮想サーバー リスト\)、[PeopleSoftApp]** の順に移動し、要求ごとのポリシーとして `<Name>` を指定します

![要求ごとのポリシーとしての PeopleSoftSSO ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso-1.png)

## <a name="setting-up-f5-big-ip-apm-to-support-single-logout-from-peoplesoft-application"></a>PeopleSoft アプリケーションからのシングル ログアウトをサポートするための F5 Big-IP APM の設定

すべての PeopleSoft ユーザーに対してシングル ログアウト サポートを追加するには、以下の手順に従います。

1. PeopleSoft ポータルの正しいログアウト URL を確認します
    * PeopleSoft アプリケーションでユーザー セッションを終了するために使用するアドレスを確認するには、次の例に示すように、任意の Web ブラウザーを使用してポータルを開き、ブラウザーのデバッグ ツールを有効にする必要があります。

        ![PeopleSoft ポータルのログアウト URL ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-out.png)

    * `PT_LOGOUT_MENU` ID が設定されている要素を検索し、クエリ パラメーターを使用して URL パスを保存します。 例では、`/psp/ps/?cmd=logout` という値を取得しました

1. ユーザーを APM ログアウト URL `/my.logout.php3` にリダイレクトする LTM iRule を作成します

    * **[Local Traffic]\(ローカル トラフィック\)、[iRule]** の順に移動して、 **[Create]\(作成\)** をクリックし、次の情報を入力して **[Finished]\(完了\)** をクリックします。

        名前: `<Name>`  
        定義は次のとおりです。  
                    _when HTTP_REQUEST { switch -glob -- [HTTP::uri] { `/psp/ps/?cmd=logout` { HTTP::redirect `/my.logout.php3` } } }_

1. 作成した iRule を仮想サーバーに割り当てます

    * **[Local Traffic]\(ローカル トラフィック\)、[Virtual Servers]\(仮想サーバー\)、[Virtual Server List]\(仮想サーバー リスト\)、[PeopleSoftApp]、[Resources]\(リソース\)** の順に移動します。 **[Manage]\(管理\)** ボタンをクリックします。 ボタンをクリックします。   

    * 有効な iRule として `<Name>` を指定し、 **[Finished]\(完了\)** をクリックします

        ![_iRule_PeopleSoftApp ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/irule-people-soft.png)

    * **[Name]\(名前\)** テキスト ボックスに値 `<Name>` を入力します 

        ![完了した _iRule_PeopleSoftApp](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/common-irule.png)


### <a name="create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user"></a>Oracle PeopleSoft-Protected by F5 BIG-IP APM テスト ユーザーの作成

このセクションでは、Oracle PeopleSoft-Protected by F5 BIG-IP APM で B.Simon というユーザーを作成します。 [Oracle PeopleSoft-Protected by F5 BIG-IP APM サポート チーム](https://support.f5.com)と連携し、Oracle PeopleSoft-Protected by F5 BIG-IP APM プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる、Oracle PeopleSoft-Protected by F5 BIG-IP APM のサインオン URL にリダイレクトされます。  

* Oracle PeopleSoft-Protected by F5 BIG-IP APM のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Oracle PeopleSoft-Protected by F5 BIG-IP APM に自動的にサインインされるはずです 

また、Microsoft アクセス パネルを使用して、任意のモードでアプリケーションをテストすることもできます。 アクセス パネルで [Oracle PeopleSoft-Protected by F5 BIG-IP APM] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Oracle PeopleSoft-Protected by F5 BIG-IP APM に自動的にサインインされるはずです。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。


## <a name="next-steps"></a>次のステップ

Oracle PeopleSoft-Protected by F5 BIG-IP APM を構成したら、組織の機密データの流出と侵入をリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
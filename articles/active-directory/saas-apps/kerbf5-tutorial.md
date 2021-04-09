---
title: チュートリアル:Azure AD シングル サインオンと F5 の統合 | Microsoft Docs
description: Azure Active Directory と F5 の間でシングル サインオン (SSO) を構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.openlocfilehash: 2fd96c47e98a6f8a66a5fda555304c4448d772e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646102"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と F5 の統合

このチュートリアルでは、F5 と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と F5 を統合すると、次のことができます。

* F5 にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して F5 に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

* F5 でのシングル サインオン (SSO) が有効なサブスクリプション。

* 共同ソリューションをデプロイするには次のライセンスが必要です。
    * F5 BIG-IP&reg; Best バンドル (または)

    * F5 BIG-IP Access Policy Manager&trade; (APM) スタンドアロン ライセンス

    * 既存の BIG-IP F5 BIG-IP&reg; Local Traffic Manager&trade; (LTM) に対する F5 BIG-IP Access Policy Manager&trade; (APM) アドオン ライセンス。

    * 上記のライセンスに加え、F5 システムには次のライセンスが付属する場合があります。

        * URL カテゴリ データベースを使用するための URL フィルタリング サブスクリプション

        * 既知の攻撃者や悪意のあるトラフィックを検出してブロックするための F5 IP Intelligence サブスクリプション

        * 強力な認証用のデジタル キーを保護、管理するためのネットワーク ハードウェア セキュリティ モジュール (HSM)

* F5 BIG-IP システムは、APM モジュールと共にプロビジョニングされます (LTM はオプション)。

* 必須ではありませんが、F5 システムは [Sync/Failover Device Group](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG) にデプロイすることを強くお勧めします。S/F DG には、アクティブとスタンバイのペアが含まれており、フローティング IP アドレスによる高可用性 (HA) が実現されます。 Link Aggregation Control Protocol (LACP) を使用すれば、さらなるインターフェイスの冗長性を実現できます。 LACP は、接続されている物理インターフェイスを 1 つの仮想インターフェイス (集計グループ) として管理し、そのグループ内のインターフェイスに発生したエラーを検出します。

* Kerberos アプリケーションに関して、制約付き委任に使用するオンプレミス AD サービス アカウント。  AD 委任アカウントの作成については、[F5 のドキュメント](https://support.f5.com/csp/article/K43063049)を参照してください。

## <a name="access-guided-configuration"></a>アクセス ガイド付き構成

* アクセス ガイド付き構成は、F5 TMOS バージョン 13.1.0.8 以降でサポートされます。 BIG-IP システムで実行されているバージョンが 13.1.0.8 未満である場合は、「**詳細な構成**」セクションを参照してください。

* アクセス ガイド付き構成により、効率化されたまったく新しいユーザー エクスペリエンスが得られます。 このワークフローベースのアーキテクチャにより、選択したトポロジに合わせて調整された直感的で再入可能な構成ステップが提供されます。

* 構成に進む前に、最新のユース ケース パックを [downloads.f5.com](https://login.f5.com/resource/login.jsp?ctx=719748) からダウンロードして、ガイド付き構成をアップグレードしてください。 アップグレードするには、以下の手順に従います。

    >[!NOTE]
    >以下のスクリーンショットは、リリースされた最新バージョン (BIG-IP 15.0、AGC バージョン 5.0) のものです。 13.1.0.8 から最新の BIG-IP バージョンでは、このユース ケースに下記の構成手順が有効です。

1. F5 BIG-IP Web UI で **[Access]\(アクセス\) >> [Guide Configuration]\(ガイド付き構成\)** をクリックします。

2. **[Guided Configuration]\(ガイド付き構成\)** ページで、左上隅の **[Upgrade Guided Configuration]\(ガイド付き構成のアップグレード\)** をクリックします。

    ![[Upgrade Guided Configuration]\(ガイド付き構成のアップグレード\) アクションが選択された [Guided Configuration]\(ガイド付き構成\) ページを示すスクリーンショット。](./media/kerbf5-tutorial/configure14.png) 

3. [Upgrade Guide Configuration]\(ガイド付き構成のアップグレード\) ポップアップ画面で、 **[Choose File]\(ファイルの選択\)** を選択し、ダウンロードしたユース ケース パックをアップロードして、 **[Upload and Install]\(アップロードしてインストール\)** ボタンをクリックします。

    ![[Choose File]\(ファイルの選択\) と [Upload and Install]\(アップロードしてインストール\) が選択された [Upgrade Guide Configuration]\(ガイド付き構成のアップグレード\) ポップアップ画面が示されているスクリーンショット。](./media/kerbf5-tutorial/configure15.png) 

4. アップグレードが完了したら、 **[Continue]\(続行\)** ボタンをクリックします。

    ![[Continue]\(続行\) ボタンが選択された [Guided Configuration update is complete]\(ガイド付き構成の更新が完了しました\) ダイアログが示されているスクリーンショット。](./media/kerbf5-tutorial/configure16.png)

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* F5 では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます
* F5 SSO は、次の 3 つの異なる方法で構成できます。

- [Kerberos アプリケーション用に F5 シングル サインオンを構成する](#configure-f5-single-sign-on-for-kerberos-application)

- [ヘッダー ベースのアプリケーション用に F5 シングル サインオンを構成する](headerf5-tutorial.md)

- [Advanced Kerberos アプリケーション用に F5 シングル サインオンを構成する](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>キー認証のシナリオ

先進認証プロトコル (Open ID Connect、SAML、WS-Fed など) に対する Azure Active Directory のネイティブ統合のサポートとは別に、F5 は、Azure AD を使用することで、内部と外部の両方のアクセスに関してレガシベース認証アプリの安全なアクセスを拡張し、それらのアプリケーションへの最新のシナリオ (パスワードレス アクセスなど) を実現します。 これには、次のものが含まれます。

* ヘッダーベースの認証アプリ

* Kerberos 認証アプリ

* 匿名認証または非ビルトイン認証アプリ

* NTLM 認証アプリ (ユーザーに対する二重プロンプトでの保護)

* フォーム ベース アプリケーション (ユーザーに対する二重プロンプトでの保護)

## <a name="adding-f5-from-the-gallery"></a>ギャラリーからの F5 の追加

Azure AD への F5 の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に F5 を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**F5**」と入力します。
1. 結果のパネルから **[F5]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>F5 の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、F5 に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと F5 の関連ユーザーとの間にリンク関係を確立する必要があります。

F5 に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[F5 SSO の構成](#configure-f5-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[F5 テスト ユーザーの作成](#create-f5-test-user)** - F5 で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **F5** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://<YourCustomFQDN>.f5.com/` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<YourCustomFQDN>.f5.com/` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<YourCustomFQDN>.f5.com/` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 この値を取得するには、[F5 クライアント サポート チーム](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** と **[証明書 (Base64)]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[F5 のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に F5 へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[F5]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。
1. **[条件付きアクセス]** をクリックします。
1. **[新しいポリシー]** をクリックします。
1. F5 アプリを条件付きアクセス ポリシーのリソースとして表示し、多要素認証やデバイス ベースのアクセス制御、ID 保護ポリシーなど、任意の条件付きアクセスを適用できます。

## <a name="configure-f5-sso"></a>F5 SSO の構成

- [ヘッダー ベースのアプリケーション用に F5 シングル サインオンを構成する](headerf5-tutorial.md)

- [Advanced Kerberos アプリケーション用に F5 シングル サインオンを構成する](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-kerberos-application"></a>Kerberos アプリケーション用に F5 シングル サインオンを構成する

### <a name="guided-configuration"></a>ガイド付き構成

1. 新しい Web ブラウザー ウィンドウを開き、F5 (Kerberos) 企業サイトに管理者としてサインインして、次の手順を実行します。

1. 後で設定プロセスで使用するメタデータ証明書を F5 にインポートする必要があります。

1. **[System]\(システム\) > [Certificate Management]\(証明書の管理\) > [Traffic Certificate Management]\(トラフィック証明書管理\) > [SSL Certificate List]\(SSL 証明書リスト\)** の順に移動します。 右隅の **[Import]\(インポート\)** を選択します。 **証明書の名前** を指定します (この後の構成で参照されます)。 **[Certificate Source]\(証明書ソース\)** で [Upload File]\(ファイルのアップロード\) を選択し、SAML シングル サインオンの構成時に Azure からダウンロードした証明書を指定します。 **[インポート]** をクリックします。

    ![証明書名が強調表示され、[Upload File]\(ファイルのアップロード\) と [Import]\(インポート\) ボタンが選択されている [SSL Certificate/Key Source]\(SSL 証明書/キー ソース\) ページを示すスクリーンショット。](./media/kerbf5-tutorial/configure01.png) 

1. 加えて、アプリケーション ホスト名の SSL 証明書が必要となります。 **[System]\(システム\) > [Certificate Management]\(証明書の管理\) > [Traffic Certificate Management]\(トラフィック証明書管理\) > [SSL Certificate List]\(SSL 証明書リスト\)** の順に移動します。 右隅の **[Import]\(インポート\)** を選択します。 **[Import Type]\(インポートの種類\)** は **[PKCS 12(IIS)]** になります。 **キー名** (この後の構成で参照されます) を指定し、PFX ファイルを指定します。 PFX の **パスワード** を指定します。 **[インポート]** をクリックします。

    >[!NOTE]
    >この例のアプリ名は `Kerbapp.superdemo.live` です。ワイルド カード証明書を使用しており、キー名は `WildCard-SuperDemo.live` です。

    ![値が入力され、[Import]\(インポート\) ボタンが選択されている [SSL Certificate/Key Source]\(SSL 証明書/キー ソース\) ページを示すスクリーンショット。](./media/kerbf5-tutorial/configure02.png) 
 
1. ここでは、ガイド付きエクスペリエンスを使用して Azure AD のフェデレーションとアプリケーション アクセスを設定します。 F5 BIG-IP の **[Main]\(メイン\)** に移動し、 **[Access]\(アクセス\) > [Guided Configuration]\(ガイド付き構成\) > [Federation]\(フェデレーション\) > [SAML Service Provider]\(SAML サービス プロバイダー\)** の順に選択します。 **[Next]\(次へ\)** をクリックし、 **[Next]\(次へ\)** をクリックして構成を開始します。

    ![[Federation]\(フェデレーション\) アイコンが強調表示され、[SAML Service Provider]\(SAML サービス プロバイダー\) が選択された [Guided Configuration]\(ガイド付き構成\) ページを示すスクリーンショット。](./media/kerbf5-tutorial/configure03.png) 

    ![[Next]\(次へ\) ボタンが選択された [Guided Configuration - SAML Service Provider]\(ガイド付き構成 - SAML サービス プロバイダー\) ページを示すスクリーンショット。](./media/kerbf5-tutorial/configure04.png)

1. **構成名** を指定します。 **エンティティ ID** (Azure AD アプリケーションの構成で構成した内容と同じ) を指定します。 **ホスト名** を指定します。 参考のために **説明** を追加します。 それ以外は既定値のままとし、 **[Save & Next]\(保存して次へ\)** をクリックします。

    ![[Host name]\(ホスト名\) と [Description]\(説明\) のテキスト ボックスが強調表示され、[Save & Next]\(保存して次へ\) ボタンが選択されている [Service Provider Properties]\(サービス プロバイダーのプロパティ\) を示すスクリーンショット。](./media/kerbf5-tutorial/configure05.png) 

1. この例では、新しい仮想サーバーを 192.168.30.200 として作成します。ポートは 443 を使用します。 **[Destination Address]\(接続先のアドレス\)** に仮想サーバーの IP アドレスを指定します。 クライアントの **SSL プロファイル** を選択し、[Create new]\(新規作成\) を選択します。 あらかじめアップロードしておいたアプリケーション証明書 (この例ではワイルド カード証明書) および関連するキーを指定し、 **[Save & Next]\(保存して次へ\)** をクリックします。

    >[!NOTE]
    >この例では、内部 Web サーバーがポート 80 で稼動しており、それを 443 で公開したいと考えています。

    ![[Destination Address]\(宛先アドレス\) テキスト ボックスが強調表示され、[Save & Next]\(保存して次へ\) ボタンが選択されている [Virtual Server Properties]\(仮想サーバーのプロパティ\) ページを示すスクリーンショット。](./media/kerbf5-tutorial/configure06.png)

1. **[Select method to configure your IdP connector]\(IdP コネクタの構成方法を選択してください\)** で [Metadata]\(メタデータ\) を指定し、[Choose File]\(ファイルの選択\) をクリックして、先ほど Azure AD からダウンロードしたメタデータ XML ファイルをアップロードします。 SAML IDP コネクタの一意の **名前** を指定します。 先ほどアップロードした **メタデータ署名証明書** を選択します。 **[Save & Next]\(保存して次へ\)** をクリックします。

    ![[Name]\(名前\) テキスト ボックスが強調表示され、[Save & Next]\(保存して次へ\) ボタンが選択されている [External Identity Provider Connector Settings]\(外部 ID プロバイダー コネクタの設定\) ページを示すスクリーンショット。](./media/kerbf5-tutorial/configure07.png)  

1. **[Select a Pool]\(プールの選択\)** で **[Create New]\(新規作成\)** を選択します (または既存のプールを選択します)。 他の値は既定値のままにしてください。    [Pool Servers]\(プール サーバー\) の **[IP Address/Node Name]\(IP アドレス/ノード名\)** に IP アドレスを入力します。 **ポート** を指定します。 **[Save & Next]\(保存して次へ\)** をクリックします。
 
    ![[IP Address/Node Name]\(IP アドレス/ノード名\) テキスト ボックスが強調表示され、[Save & Next]\(保存して次へ\) ボタンが選択されている [Pool Properties]\(プールのプロパティ\) ページを示すスクリーンショット。](./media/kerbf5-tutorial/configure08.png)

1. [Single Sign-On Settings]\(シングル サインオンの設定\) 画面で **[Enable Single Sign-On]\(シングル サインオンを有効にする\)** を選択します。 **[Selected Single Sign-On Type]\(選択されたシングル サインオンの種類\)** で **[Kerberos]** を選択します。 **[Username Source]\(ユーザー名ソース\)** の **session.saml.last.Identity** を **session.saml.last.attr.name.Identity** に置き換えます (この変数は、Azure AD で要求マッピングを使用して設定されます)。 **[Show Advanced Setting]\(詳細設定の表示\)** を選択します。 **[Kerberos Realm]\(Kerberos 領域\)** に、ドメイン名を入力します。 **[Account Name]\(アカウント名\)/[Account Password]\(アカウント パスワード\)** に、APM 委任アカウントとパスワードを指定します。 **[KDC]** フィールドに、ドメイン コントローラーの IP を指定します。 **[Save & Next]\(保存して次へ\)** をクリックします。

    ![テキスト ボックスが強調表示され、[Save & Next]\(保存して次へ\) ボタンが選択されている [Single Sign-On Settings]\(シングル サインオンの設定\) を示すスクリーンショット。](./media/kerbf5-tutorial/configure09.png)   

1. このガイダンスの目的上、エンドポイントの確認は省略します。  詳細については、F5 のドキュメントを参照してください。  画面上で **[Save & Next]\(保存して次へ\)** を選択します。

    ![[Save & Next]\(保存して次へ\) ボタンが選択されている [Endpoint Checks Properties]\(エンドポイント チェックのプロパティ\) ページを示すスクリーンショット。](./media/kerbf5-tutorial/configure10.png) 

1. 既定値をそのまま使用し、 **[Save & Next]\(保存して次へ\)** をクリックします。 SAML セッション管理設定の詳細については、F5 のドキュメントを参照してください。


    ![[Save & Next]\(保存して次へ\) ボタンが選択されている [Timeout Settings]\(タイムアウト設定\) ページを示すスクリーンショット。](./media/kerbf5-tutorial/configure11.png) 
 
1. 概要画面を確認し、 **[Deploy]\(デプロイ\)** を選択して BIG-IP を構成します。
 
    ![[Summary]\(概要\) セクションが強調表示され、[Deploy]\(デプロイ\) ボタンが選択されている [Your application is ready to be deployed]\(アプリケーションをデプロイする準備ができました\) ページを示すスクリーンショット。](./media/kerbf5-tutorial/configure12.png)

1. アプリケーションが構成されたら、 **[Finish]\(完了\)** をクリックします。

    ![[Finish]\(完了\) ボタンが選択されている [Your application is deployed]\(アプリケーションがデプロイされました\) ページを示すスクリーンショット。](./media/kerbf5-tutorial/configure13.png)

## <a name="advanced-configuration"></a>高度な構成

>[!NOTE]
>参考情報については、[こちら](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)をクリックしてください。

### <a name="configuring-an-active-directory-aaa-server"></a>Active Directory AAA サーバーを構成する

Access Policy Manager (APM) がユーザーの認証に使用するドメイン コントローラーと資格情報を指定するには、APM で Active Directory AAA サーバーを構成します。

1. [Main]\(メイン\) タブで、 **[Access Policy]\(アクセス ポリシー\) > [AAA Servers]\(AAA サーバー\) > [Active Directory]** の順にクリックします。 Active Directory サーバーのリスト画面が表示されます。

2. **Create** をクリックしてください。 新しいサーバーのプロパティ画面が表示されます。

3. **[Name]\(名前\)** フィールドに、認証サーバーの一意の名前を入力します。

4. **[Domain Name]\(ドメイン名\)** フィールドに、Windows ドメインの名前を入力します。

5. **[Server Connection]\(サーバー接続\)** 設定で、次のいずれかのオプションを選択します。

   * AAA サーバーの高可用性を設定するには、 **[Use Pool]\(プールを使用する\)** を選択します。

   * スタンドアロンの機能用に AAA サーバーを設定するには、 **[Direct]\(直接\)** を選択します。

6. **[Direct]\(直接\)** を選択した場合は、 **[Domain Controller]\(ドメイン コントローラー\)** フィールドに名前を入力します。

7. **[Use Pool]\(プールを使用する\)** を選択した場合は、プールを構成します。

   * **[Domain Controller Pool Name]\(ドメイン コントローラーのプール名\)** フィールドに名前を入力します。

   * プール内の **ドメイン コントローラー** を指定するために、それぞれの IP アドレスとホスト名を入力し、 **[Add]\(追加\)** ボタンをクリックします。

   * AAA サーバーの正常性を監視するために、正常性モニターを選択することもできます。この場合に適切なのは **gateway_icmp** モニターのみです。 **[Server Pool Monitor]\(サーバー プール モニター\)** リストから選択してください。

8. **[Admin Name]\(管理者名\)** フィールドに、Active Directory の管理者権限を持つ管理者の名前 (大文字と小文字が区別されます) を入力します。 APM では、 **[Admin Name]\(管理者名\)** および **[Admin Password]\(管理者パスワード\)** フィールドの情報を AD クエリに使用します。 Active Directory が匿名クエリ向けに構成されている場合は、管理者名を指定する必要がありません。 それ以外の場合は、パスワード関連機能をサポートするために、Active Directory サーバーへのバインド、ユーザー グループ情報のフェッチ、Active Directory パスワード ポリシーのフェッチを行うのに十分な権限を持つアカウントが必要です (たとえば、AD クエリ アクションで [Prompt user to change password before expiration]\(有効期限が切れる前にパスワードの変更をユーザーに促す\) オプションを選択した場合、APM はパスワード ポリシーをフェッチする必要があります)。この構成で管理者アカウント情報を指定しなかった場合は、APM ではユーザー アカウントを使用して情報がフェッチされます。 これは、ユーザー アカウントに必要な権限がある場合に機能します。

9. **[Admin Password]\(管理者パスワード\)** フィールドに、ドメイン名に関連付けられている管理者パスワードを入力します。

10. **[Verify Admin Password]\(管理者パスワードの確認\)** フィールドに、 **[Domain Name]\(ドメイン名\)** 設定に関連付けられている管理者パスワードを再度入力します。

11. **[Group Cache Lifetime]\(グループ キャッシュの有効期間\)** フィールドに日数を入力します。 既定の有効期間は 30 日です。

12. **[Password Security Object Cache Lifetime]\(パスワード セキュリティ オブジェクト キャッシュの有効期間\)** フィールドに日数を入力します。 既定の有効期間は 30 日です。

13. **[Kerberos Preauthentication Encryption Type]\(Kerberos 事前認証暗号化の種類\)** リストから暗号化の種類を選択します。 既定値は **[None]\(なし\)** です。 暗号化の種類を指定した場合は、BIG-IP システムにより、最初の認証サービス要求 (AS-REQ) パケット内に Kerberos 事前認証データが追加されます。

14. **[Timeout]\(タイムアウト\)** フィールドに、AAA サーバーのタイムアウト間隔 (秒) を入力します (この設定は省略可能です)。

15. **[Finished]\(完了\)** をクリックします。 新しいサーバーがリストに表示されます。 これで、新しい Active Directory サーバーが Active Directory サーバー リストに追加されます。

    ![[General Properties]\(全般プロパティ\) と [Configuration]\(構成\) のセクションを示すスクリーンショット。](./media/kerbf5-tutorial/configure17.png)

### <a name="saml-configuration"></a>SAML の構成

1. 後で設定プロセスで使用するメタデータ証明書を F5 にインポートする必要があります。 **[System]\(システム\) > [Certificate Management]\(証明書の管理\) > [Traffic Certificate Management]\(トラフィック証明書管理\) > [SSL Certificate List]\(SSL 証明書リスト\)** の順に移動します。 右隅の **[Import]\(インポート\)** を選択します。

    ![[Import]\(インポート\) ボタンが選択されている [Import SSL Certificate/Key Source]\(SSL 証明書/キー ソースのインポート\) ページを示すスクリーンショット。](./media/kerbf5-tutorial/configure18.png)

2. SAML IDP を設定するには、 **[アクセス] > [フェデレーション] > [SAML: サービス プロバイダー] > [外部 Idp コネクタ]** の順に移動し、 **[作成] > [メタデータから]** をクリックします。

    ![[Create]\(作成\) ドロップダウンで [From Metadata]\(メタデータから\) が選択された [SAML Service Provider]\(SAML サービス プロバイダー\) ページを示すスクリーンショット。](./media/kerbf5-tutorial/configure19.png)

    ![[Create New SAML IdP Connector]\(新しい SAML IdP コネクタの作成\) ダイアログを示すスクリーンショット。](./media/kerbf5-tutorial/configure20.png)

    ![[General Settings]\(全般設定\) が選択された [Edit SAML IdP Connector]\(SAML IdP コネクタの編集\) ウィンドウを示すスクリーンショット。](./media/kerbf5-tutorial/configure21.png)

    ![[Single Sign On Service Settings]\(シングル サインオン サービス設定\) が選択された [Edit SAML IdP Connector]\(SAML IdP コネクタの編集\) ウィンドウを示すスクリーンショット。](./media/kerbf5-tutorial/configure22.png)

    ![[Security Settings]\(セキュリティの設定\) が選択された [Edit SAML IdP Connector]\(SAML IdP コネクタの編集\) ウィンドウを示すスクリーンショット。](./media/kerbf5-tutorial/configure23.png)

    ![[SLO Service Settings]\(SLO サービスの設定\) が選択された [Edit SAML IdP Connector]\(SAML IdP コネクタの編集\) ウィンドウを示すスクリーンショット。](./media/kerbf5-tutorial/configure24.png)

1. SAML SP を設定するために、 **[Access]\(アクセス\) > [Federation]\(フェデレーション\) > [SAML Service Provider]\(SAML サービス プロバイダー\) > [Local SP Services]\(ローカル SP サービス\)** の順に移動して、 **[Create]\(作成\)** をクリックします。 次の情報を入力して **[OK]** をクリックします。

    * 種類名: KerbApp200SAML
    * [Entity ID*]\(エンティティ ID*\): https://kerb-app.com.cutestat.com
    * [SP Name Settings]\(SP 名の設定\)
    * [Scheme]\(スキーム\): https
    * [Host]\(ホスト\): kerbapp200.superdemo.live
    * [Description]\(説明\): kerbapp200.superdemo.live

     ![[General Settings]\(全般設定\) が選択された [Edit SAML SP Service]\(SAML SP サービスの編集\) ウィンドウを示すスクリーンショット。](./media/kerbf5-tutorial/configure25.png)

     b. SP 構成である KerbApp200SAML を選択し、 **[Bind/UnBind IdP Connectors]\(IdP コネクタのバインドまたはバインド解除\)** をクリックします。

     ![[KerbAPP200 SAML] が選択されている [SAML Service Provider - Local SP Services]\(SAML サービス プロバイダー - ローカル SP サービス\) ページを示すスクリーンショット。](./media/kerbf5-tutorial/configure26.png)

     ![選択された [Bind/UnBind IdP Connectors]\(IdP コネクタのバインドまたはバインド解除\) ボタンを示すスクリーンショット。](./media/kerbf5-tutorial/configure27.png)

     c. **[Add New Row]\(新しい行の追加\)** をクリックし、前の手順で作成した **外部 IdP コネクタ** を選択して、 **[Update]\(更新\)** 、 **[OK]** の順にクリックします。

     ![[Add New Row]\(新しい行の追加\) ボタンが選択された、[Edit SAML IdPs that use this SP]\(この SP を使用する SAML IdP の編集\) ウィンドウを示すスクリーンショット。](./media/kerbf5-tutorial/configure28.png)

1. Kerberos SSO を構成する場合は、 **[Access]\(アクセス\) > [Single Sign-on]\(シングル サインオン\) > [Kerberos]** に移動し、必要な情報を入力して **[Finished]\(完了\)** をクリックします。

    >[!Note]
    > Kerberos 委任アカウントを作成し、指定する必要があります。 KCD セクションを参照してください (変数リファレンスについては、付録を参照してください)

    * **ユーザー名ソース**: session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname

    * **[User Realm Source]\(ユーザー領域のソース\)** : session.logon.last.domain

        ![[Username Source]\(ユーザー名ソース\) および [User Realm Source]\(ユーザー領域のソース\) のテキスト ボックスが強調表示された [Single Sign-On - Properties]\(シングル サインオン - プロパティ\) ページを示すスクリーンショット。](./media/kerbf5-tutorial/configure29.png)

1. アクセス プロファイルを構成する場合は、 **[Access]\(アクセス\) > [Profile/Policies]\(プロファイルまたはポリシー\) > [Access Profile (per session policies)]\(アクセス プロファイル (セッション ポリシーごと)\)** の順に移動して **[Create]\(作成\)** をクリックし、次の情報を入力して **[Finished]\(完了\)** をクリックします。

    * 名前:KerbApp200
    * [Profile Type]\(プロファイルの種類\): All
    * [Profile Scope]\(プロファイルのスコープ\): プロファイル
    * 言語:英語

        ![[Name]\(名前\)、[Profile Type]\(プロファイルの種類\)、[Languages]\(言語\) のテキスト ボックスが強調表示された、[Profiles/Policies - Properties]\(プロファイル/ポリシー - プロパティ\) ページを示すスクリーンショット。](./media/kerbf5-tutorial/configure30.png)

1. 名前 (KerbApp200) をクリックし、次の情報を入力して、 **[Update]\(更新\)** をクリックします。

    * [Domain Cookie]\(ドメイン Cookie\): superdemo.live
    * [SSO Configuration]\(SSO 構成\): KerAppSSO_sso

        ![[Domain Cookie]\(ドメイン Cookie\) テキスト ボックスと [SSO Configuration]\(SSO 構成\) ドロップダウンが強調表示され、[Update]\(更新\) ボタンが選択されている、[SSO/Auth Domains]\(SSO および認証ドメイン\) ページを示すスクリーンショット。](./media/kerbf5-tutorial/configure31.png)

1. **[Access Policy]\(アクセス ポリシー\)** をクリックして、プロファイル "KerbApp200" の **[Edit Access Policy]\(アクセス ポリシーの編集\)** をクリックします。

    ![[Edit Access Policy for Profile KerbApp200]\(プロファイル KerbApp200 のアクセス ポリシーを編集する\) アクションが選択された [Access Policy]\(アクセス ポリシー\) ページを示すスクリーンショット。](./media/kerbf5-tutorial/configure32.png)

    ![[Access Policy]\(アクセス ポリシー\) ページと [SAML Authentication SP]\(SAML 認証 SP\) ダイアログを示すスクリーンショット。](./media/kerbf5-tutorial/configure33.png)

    ![[Access Policy]\(アクセス ポリシー\) ページと、[Assignment]\(割り当て\) テキスト ボックスが強調表示された [Variable Assign]\(変数割り当て\) ダイアログを示すスクリーンショット。](./media/kerbf5-tutorial/configure34.png)

    * **session.logon.last.usernameUPN   expr {[mcget {session.saml.last.identity}]}**

    * **session.ad.lastactualdomain  TEXT superdemo.live**

        ![[Access Policy]\(アクセス ポリシー\) ページと、[SearchFilter] テキスト ボックスが強調表示された [Active Directory] ダイアログを示すスクリーンショット。](./media/kerbf5-tutorial/configure35.png)

    * **(userPrincipalName=%{session.logon.last.usernameUPN})**

        ![[Access Policy]\(アクセス ポリシー\) ページと [AD Query - Branch Rules]\(AD クエリ - 分岐の規則\) ダイアログを示すスクリーンショット。](./media/kerbf5-tutorial/configure36.png)

        ![強調表示された [Custom Variable]\(カスタム変数\)および [Custom Expression]\(カスタム式\) のテキスト ボックスを示すスクリーンショット。](./media/kerbf5-tutorial/configure37.png)

    * **session.logon.last.username  expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }**

        ![強調表示された [Username from Logon Page]\(ログオン ページのユーザー名\) テキスト ボックスを示すスクリーンショット。](./media/kerbf5-tutorial/configure38.png)

    * **mcget {session.logon.last.username}**
    * **mcget {session.logon.last.password**

1. 新しいノードを追加するために、 **[Local Traffic]\(ローカル トラフィック\) > [Nodes]\(ノード\) > [Node List]\(ノード リスト\) の順に移動して、[Create]\(作成\) をクリックし**、次の情報を入力して **[Finished]\(完了\)** をクリックします。

    * 名前:KerbApp200
    * 説明:KerbApp200
    * Address:192.168.20.200

        ![[Name]\(名前\)、[Description]\(説明\)、[Address]\(アドレス\) のテキスト ボックスが強調表示され、[Finished]\(完了\) ボタンが選択された [New Node]\(新しいノード\) ページを示すスクリーンショット。](./media/kerbf5-tutorial/configure39.png)

1. 新しいプールを作成するために、 **[Local Traffic]\(ローカル トラフィック\) > [Pools]\(プール\) > [Pool List]\(プール リスト\) の順に移動して、[Create]\(作成\) をクリックし**、次の情報を入力して **[Finished]\(完了\)** をクリックします。

    * 名前:KerbApp200-Pool
    * 説明:KerbApp200-Pool
    * [Health Monitors]\(正常性モニター\): http
    * Address:192.168.20.200
    * [Service Port]\(サービス ポート\): 81

        ![値が入力され、[Finished]\(完了\) ボタンが選択された [New Pool]\(新しいプール\) ページを示すスクリーンショット。](./media/kerbf5-tutorial/configure40.png)

1. 新しい仮想サーバーを作成するには、 **[Local Traffic]\(ローカル トラフィック\) > [Virtual Servers]\(仮想サーバー\) > [Virtual Server List]\(仮想サーバー リスト\) > [+]** の順に移動し、次の情報を入力して **[Finished]\(完了\)** をクリックします。

    * 名前:KerbApp200
    * [Destination Address/Mask]\(接続先のアドレス/マスク\): [Host]\(ホスト\) 192.168.30.200
    * [Service Port]\(サービス ポート\): [Port]\(ポート\) 443 HTTPS
    * [Access Profile]\(アクセス プロファイル\): KerbApp200
    * 前の手順で作成したアクセス プロファイルを指定する

        ![[Name]\(名前\)、[Destination Address/Mask]\(接続先のアドレス/マスク\)、および [Service Port]\(サービス ポート\) のテキスト ボックスが強調表示されている [Virtual Server List]\(仮想サーバー リスト\) ページを示すスクリーンショット。](./media/kerbf5-tutorial/configure41.png)

        ![[Access Profile]\(アクセス プロファイル\) ドロップダウンが強調表示されている[Virtual Server List]\(仮想サーバー リスト\) ページを示すスクリーンショット。](./media/kerbf5-tutorial/configure42.png)

### <a name="setting-up-kerberos-delegation"></a>Kerberos 委任の設定 

>[!NOTE]
>参考情報については、[こちら](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)をクリックしてください。

*  **手順 1:** 委任アカウントを作成する

    **例:**
    * ドメイン名: **superdemo.live**

    * Sam アカウント名: **big-ipuser**

    * New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")

* **手順 2:** (APM 委任アカウント上で) SPN を設定する

    **例:**
    * setspn –A **host/big-ipuser.superdemo.live** big-ipuser

* **手順 3:** (App Service アカウントに対する) SPN 委任で F5 委任アカウントに適切な委任を設定する。
    次の例では、FRP-App1.superdemo.live の KCD に対して APM 委任アカウントが構成されています。 live アプリの KCD に対して APM 委任アカウントが構成されています。

    ![F5 (Kerberos) の構成](./media/kerbf5-tutorial/configure43.png)

* 上記の参照ドキュメントの[ここ](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)に記載されているように詳細を指定します。

### <a name="create-f5-test-user"></a>F5 テスト ユーザーの作成

このセクションでは、F5 で B.Simon というユーザーを作成します。 [F5 クライアント サポート チーム](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45)と連携して、F5 プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [F5] タイルをクリックすると、SSO を設定した F5 に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で F5 を試す](https://aad.portal.azure.com/)

- [ヘッダー ベースのアプリケーション用に F5 シングル サインオンを構成する](headerf5-tutorial.md)

- [Advanced Kerberos アプリケーション用に F5 シングル サインオンを構成する](advance-kerbf5-tutorial.md)

- [F5 BIG-IP APM と Azure AD の統合による安全なハイブリッド アクセス](../manage-apps/f5-aad-integration.md)

- [セキュリティで保護されたハイブリッド アクセスのために F5 BIG-IP Virtual Edition VM を Azure IaaS にデプロイするチュートリアル](../manage-apps/f5-bigip-deployment-guide.md)

- [パスワードレス VPN 用の Azure Active Directory シングル サインオンと F5 BIG-IP の統合](../manage-apps/f5-aad-password-less-vpn.md)
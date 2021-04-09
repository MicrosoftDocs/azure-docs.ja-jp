---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と F5 の統合 | Microsoft Docs
description: Azure Active Directory と F5 の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: 28040a92d713707204f0651773f7edce9f4daf6d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651775"
---
# <a name="tutorial-configure-single-sign-on-sso-between-azure-active-directory-and-f5"></a>チュートリアル:Azure Active Directory と F5 の間でのシングル サインオン (SSO) の構成

このチュートリアルでは、F5 と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と F5 を統合すると、次のことができます。

* F5 にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して F5 に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

> [!NOTE]
> F5 BIG-IP APM を[今すぐ購入](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/f5-networks.f5-big-ip-best?tab=Overview)できます。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

* F5 でのシングル サインオン (SSO) が有効なサブスクリプション。

* 共同ソリューションをデプロイするには次のライセンスが必要です。

    * F5 BIG-IP® Best バンドル (または) 

    * F5 BIG-IP Access Policy Manager™ (APM) スタンドアロン ライセンス 

    * 既存の BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM) に対する F5 BIG-IP Access Policy Manager™ (APM) アドオン ライセンス

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

1. **[Guided Configuration]\(ガイド付き構成\)** ページで、左上隅の **[Upgrade Guided Configuration]\(ガイド付き構成のアップグレード\)** をクリックします。

    ![[Upgrade Guided Configuration]\(ガイド付き構成のアップグレード\) リンクがある [Guided Configuration]\(ガイド付き構成\) ページを示すスクリーンショット。](./media/headerf5-tutorial/configure14.png) 

1. [Upgrade Guide Configuration]\(ガイド付き構成のアップグレード\) ポップアップ画面で、 **[Choose File]\(ファイルの選択\)** を選択し、ダウンロードしたユース ケース パックをアップロードして、 **[Upload and Install]\(アップロードしてインストール\)** ボタンをクリックします。

    ![[Choose File]\(ファイルの選択\) が選択されている [Upgrade Guided Configuration]\(ガイド付き構成のアップグレード\) ダイアログ ボックスを示すスクリーンショット。](./media/headerf5-tutorial/configure15.png) 

1. アップグレードが完了したら、 **[Continue]\(続行\)** ボタンをクリックします。

    ![完了メッセージが表示された [Upgrade Guided Configuration]\(ガイド付き構成のアップグレード\) ダイアログ ボックスを示すスクリーンショット。](./media/headerf5-tutorial/configure16.png)

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* F5 SSO は、次の 3 つの異なる方法で構成できます。

- [ヘッダー ベースのアプリケーション用に F5 シングル サインオンを構成する](#configure-f5-single-sign-on-for-header-based-application)

- [Kerberos アプリケーション用に F5 シングル サインオンを構成する](kerbf5-tutorial.md)

- [Advanced Kerberos アプリケーション用に F5 シングル サインオンを構成する](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>キー認証のシナリオ

* 先進認証プロトコル (Open ID Connect、SAML、WS-Fed など) に対する Azure Active Directory のネイティブ統合のサポートとは別に、F5 は、Azure AD を使用することで、内部と外部の両方のアクセスに関してレガシベース認証アプリの安全なアクセスを拡張し、それらのアプリケーションへの最新のシナリオ (パスワードレス アクセスなど) を実現します。 これには、次のものが含まれます。

* ヘッダーベースの認証アプリ

* Kerberos 認証アプリ

* 匿名認証または非ビルトイン認証アプリ

* NTLM 認証アプリ (ユーザーに対する二重プロンプトでの保護)

* フォーム ベース アプリケーション (ユーザーに対する二重プロンプトでの保護)

## <a name="adding-f5-from-the-gallery"></a>ギャラリーからの F5 の追加

Azure AD への F5 の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に F5 を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**F5**」と入力します。
1. 結果のパネルから **[F5]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-f5"></a>F5 の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、F5 に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと F5 の関連ユーザーとの間にリンク関係を確立する必要があります。

F5 に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[F5 SSO の構成](#configure-f5-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[F5 テスト ユーザーの作成](#create-f5-test-user)** - F5 で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **F5** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
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

1. **[SAML によるシングル サインオンのセットアップ]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** と **[証明書 (Base64)]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして自分のコンピューターに保存します。

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
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-f5-sso"></a>F5 SSO の構成

- [Kerberos アプリケーション用に F5 シングル サインオンを構成する](kerbf5-tutorial.md)

- [Advanced Kerberos アプリケーション用に F5 シングル サインオンを構成する](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>ヘッダー ベースのアプリケーション用に F5 シングル サインオンを構成する

### <a name="guided-configuration"></a>ガイド付き構成

1. 新しい Web ブラウザー ウィンドウを開き、F5 (ヘッダー ベース) 企業サイトに管理者としてサインインして、次の手順を実行します。

1. **[System]\(システム\) > [Certificate Management]\(証明書の管理\) > [Traffic Certificate Management]\(トラフィック証明書管理\) > [SSL Certificate List]\(SSL 証明書リスト\)** の順に移動します。 右隅の **[Import]\(インポート\)** を選択します。 **証明書の名前** を指定します (この後の構成で参照されます)。 **[Certificate Source]\(証明書ソース\)** で [Upload File]\(ファイルのアップロード\) を選択し、SAML シングル サインオンの構成時に Azure からダウンロードした証明書を指定します。 **[インポート]** をクリックします。

    ![証明書の名前と証明書ソースを選択する S S L 証明書リストを示すスクリーンショット。](./media/headerf5-tutorial/configure12.png)
 
1. 加えて、アプリケーション ホスト名の SSL 証明書が必要となります。 **[System]\(システム\) > [Certificate Management]\(証明書の管理\) > [Traffic Certificate Management]\(トラフィック証明書管理\) > [SSL Certificate List]\(SSL 証明書リスト\)** の順に移動します。 右隅の **[Import]\(インポート\)** を選択します。 **[Import Type]\(インポートの種類\)** は **[PKCS 12(IIS)]** になります。 **キー名** (この後の構成で参照されます) を指定し、PFX ファイルを指定します。 PFX の **パスワード** を指定します。 **[インポート]** をクリックします。

    >[!NOTE]
    >この例のアプリ名は `Headerapp.superdemo.live` です。ワイルド カード証明書を使用しており、キー名は `WildCard-SuperDemo.live` です。

    ![[S S L Certificate/Key Source]\(S S L 証明書/キーのソース\) ページを示すスクリーンショット。](./media/headerf5-tutorial/configure13.png)

1. ここでは、ガイド付きエクスペリエンスを使用して Azure AD のフェデレーションとアプリケーション アクセスを設定します。 F5 BIG-IP の **[Main]\(メイン\)** に移動し、 **[Access]\(アクセス\) > [Guided Configuration]\(ガイド付き構成\) > [Federation]\(フェデレーション\) > [SAML Service Provider]\(SAML サービス プロバイダー\)** の順に選択します。 **[Next]\(次へ\)** をクリックし、 **[Next]\(次へ\)** をクリックして構成を開始します。

    ![[Federation]\(フェデレーション\) が選択されている [Guided Configuration]\(ガイド付き構成\) ページを示すスクリーンショット。](./media/headerf5-tutorial/configure01.png)

    ![[SAML Service Provider]\(SAML サービス プロバイダー\) ページを示すスクリーンショット。](./media/headerf5-tutorial/configure02.png)
 
1. **構成名** を指定します。 **エンティティ ID** (Azure AD アプリケーションの構成で構成した内容と同じ) を指定します。 **ホスト名** を指定します。 参考のために **説明** を追加します。 それ以外は既定値のままとし、 **[Save & Next]\(保存して次へ\)** をクリックします。

    ![[Service Provider Properties]\(サービス プロバイダーのプロパティ\) ページを示すスクリーンショット。](./media/headerf5-tutorial/configure03.png) 

1. この例では、ポート 443 で新しい仮想サーバーを 192.168.30.20 として作成します。 **[Destination Address]\(接続先のアドレス\)** に仮想サーバーの IP アドレスを指定します。 クライアントの **SSL プロファイル** を選択し、[Create new]\(新規作成\) を選択します。 あらかじめアップロードしておいたアプリケーション証明書 (この例ではワイルド カード証明書) および関連するキーを指定し、 **[Save & Next]\(保存して次へ\)** をクリックします。

    >[!NOTE]
    >この例では、内部 Web サーバーがポート 888 で稼動しており、それを 443 で公開したいと考えています。

    ![[Virtual Server Properties]\(仮想サーバーのプロパティ\) ページを示すスクリーンショット。](./media/headerf5-tutorial/configure04.png) 

1. **[Select method to configure your IdP connector]\(IdP コネクタの構成方法を選択してください\)** で [Metadata]\(メタデータ\) を指定し、[Choose File]\(ファイルの選択\) をクリックして、先ほど Azure AD からダウンロードしたメタデータ XML ファイルをアップロードします。 SAML IDP コネクタの一意の **名前** を指定します。 先ほどアップロードした **メタデータ署名証明書** を選択します。 **[Save & Next]\(保存して次へ\)** をクリックします。

    ![[External Identity Provider Connector Settings]\(外部 ID プロバイダー コネクタの設定\) ページを示すスクリーンショット。](./media/headerf5-tutorial/configure05.png)
 
1. **[Select a Pool]\(プールの選択\)** で **[Create New]\(新規作成\)** を選択します (または既存のプールを選択します)。 他の値は既定値のままにしてください。 [Pool Servers]\(プール サーバー\) の **[IP Address/Node Name]\(IP アドレス/ノード名\)** に IP アドレスを入力します。 **ポート** を指定します。 **[Save & Next]\(保存して次へ\)** をクリックします。

    ![[Pool Properties]\(プールのプロパティ\) ページを示すスクリーンショット。](./media/headerf5-tutorial/configure06.png)

1. [Single Sign-On Settings]\(シングル サインオンの設定\) 画面で **[Enable Single Sign-On]\(シングル サインオンを有効にする\)** を選択します。 [Selected Single Sign-On Type]\(選択されたシングル サインオンの種類\) で **[HTTP header-based]\(HTTP ヘッダーベース\)** を選択します。 [Username Source]\(ユーザー名ソース\) の **session.saml.last.Identity** を **session.saml.last.attr.name.Identity** に置き換えます (この変数は、Azure AD で要求のマッピングを使用して設定されます)。 [SSO Headers]\(SSO ヘッダー\) では次のようにします。

    * **[Header Name]\(ヘッダー名\): MyAuthorization**

    * **[Header Value]\(ヘッダーの値\): %{session.saml.last.attr.name.Identity}**

    * **[Save & Next]\(保存して次へ\)** をクリックします。

    変数と値の完全な一覧については、付録を参照してください。 必要に応じてさらにヘッダーを追加できます。

    >[!NOTE]
    >アカウント名は、作成された F5 委任アカウントです (F5 のドキュメントを確認してください)。

    ![[Single Sign-On Settings]\(シングル サインオンの設定\) ページを示すスクリーンショット。](./media/headerf5-tutorial/configure07.png) 

1. このガイダンスの目的上、エンドポイントの確認は省略します。  詳細については、F5 のドキュメントを参照してください。 **[Save & Next]\(保存して次へ\)** を選択します。

    ![[Endpoint Checks Properties]\(エンドポイント チェックのプロパティ\) ページを示すスクリーンショット。](./media/headerf5-tutorial/configure08.png)

1. 既定値をそのまま使用し、 **[Save & Next]\(保存して次へ\)** をクリックします。 SAML セッション管理設定の詳細については、F5 のドキュメントを参照してください。

    ![[Timeout Settings]\(タイムアウト設定\) ページを示すスクリーンショット。](./media/headerf5-tutorial/configure09.png)

1. 概要画面を確認し、 **[Deploy]\(デプロイ\)** を選択して BIG-IP を構成します。 **[Finish]\(完了\)** をクリックします。

    ![[Your application is ready to be deployed]\(アプリケーションをデプロイする準備ができました\) ページを示すスクリーンショット。](./media/headerf5-tutorial/configure10.png)

    ![[Your application is deployed]\(アプリケーションがデプロイされました\) ページを示すスクリーンショット。](./media/headerf5-tutorial/configure11.png)

## <a name="advanced-configuration"></a>高度な構成

このセクションは、ガイド付き構成を使用できない場合や他のパラメーターを追加または変更したい場合に使用するためのものです。 アプリケーション ホスト名の TLS/SSL 証明書が必要になります。

1. **[System]\(システム\) > [Certificate Management]\(証明書の管理\) > [Traffic Certificate Management]\(トラフィック証明書管理\) > [SSL Certificate List]\(SSL 証明書リスト\)** の順に移動します。 右隅の **[Import]\(インポート\)** を選択します。 **[Import Type]\(インポートの種類\)** は **[PKCS 12(IIS)]** になります。 **キー名** (この後の構成で参照されます) を指定し、PFX ファイルを指定します。 PFX の **パスワード** を指定します。 **[インポート]** をクリックします。

    >[!NOTE]
    >この例のアプリ名は `Headerapp.superdemo.live` です。ワイルド カード証明書を使用しており、キー名は `WildCard-SuperDemo.live` です。
  
    ![高度な構成の [S S L Certificate/Key Source]\(S S L 証明書/キーのソース\) ページを示すスクリーンショット。](./media/headerf5-tutorial/configure17.png)

### <a name="adding-a-new-web-server-to-bigip-f5"></a>BigIP-F5 に新しい Web サーバーを追加する

1. **[Main]\(メイン\) > [IApps] > [Application Services]\(アプリケーション サービス\) > [Application]\(アプリケーション\) > [Create]\(作成\)** の順にクリックします。

1. **名前** を入力し、 **[Template]\(テンプレート\)** で **[f5.http]** を選択します。
 
    ![[Template Selection]\(テンプレートの選択\) が含まれた [Application Services]\(アプリケーション サービス\) ページを示すスクリーンショット。](./media/headerf5-tutorial/configure18.png)

1. この場合、HeaderApp2 を HTTPS として外部に公開します。 **[how should the BIG-IP system handle SSL Traffic?]\(BIG-IP システムで SSL トラフィックを処理する方法\)** では、 **[Terminate SSL from Client, Plaintext to servers (SSL Offload)]\(クライアントからの SSL を終了し、サーバーにプレーンテキストを送信する(SSL オフロード)\)** を指定します。 **[Which SSL certificate do you want to use?]\(使用する SSL 証明書\)** と **[Which SSL private key do you want to use?]\(使用する SSL 秘密キー\)** で、証明書とキーを指定します。 **[What IP Address do you want to use for the Virtual Server?]\(仮想サーバーに使用する IP アドレス\)** で、仮想サーバーの IP を指定します。 

    * **その他の詳細を指定してください**

        * FQDN  

        * 既存のアプリケーション プールを指定するか、新たに作成します。

        * 新しいアプリ サーバーを作成する場合は、**内部 IP アドレス** と **ポート番号** を指定します。

        ![これらの詳細を指定できるペインを示すスクリーンショット。](./media/headerf5-tutorial/configure19.png) 

1. **[Finished]\(完了\)** をクリックします。

    ![完了後のページを示すスクリーンショット。](./media/headerf5-tutorial/configure20.png) 

1. アプリのプロパティを変更できるようにします。 **[Main]\(メイン\) > [IApps] > [Application Services: Applications]\(アプリケーション サービス: アプリケーション\) > [HeaderApp2]** の順にクリックします。 **[Strict Updates]\(更新の制限\)** チェック ボックスをオフにします (一部の設定は GUI 以外で変更します)。 **[Update]\(更新\)** ボタンをクリックします。

    ![[Properties]\(プロパティ\) タブが選択されている [Application Services]\(アプリケーション サービス\) ページを示すスクリーンショット。](./media/headerf5-tutorial/configure21.png) 

1. この時点で、仮想サーバーを参照できるようになります。

### <a name="configuring-f5-as-sp-and-azure-as-idp"></a>F5 を SP として、Azure を IDP として構成する

1.  **[Access]\(アクセス\) > [Federation]\(フェデレーション\) > [SAML Service Provider]\(SAML サービス プロバイダー\) > [Local SP Service]\(ローカル SP サービス\)、[create]\(作成\) または [+ sign]\(+ 署名\)** の順にクリックします。

    ![[About this BIG I P]\(この BIG I P について\) ページを示すスクリーンショット。 ](./media/headerf5-tutorial/configure22.png)

1. サービス プロバイダー サービスの詳細を指定します。 F5 の SP 構成を表す **名前** を指定します。 **エンティティ ID** (通常は、アプリケーションの URL と同じ) を指定します。

    ![[Create New SAML S P Service]\(新しい SAML S P サービスの作成\) ダイアログ ボックスが表示された [SAML Service Provider]\(SAML サービス プロバイダー\) ページを示すスクリーンショット。](./media/headerf5-tutorial/configure23.png)

    ![[Endpoint Settings]\(エンドポイント設定\) が選択されている [Create New SAML S P Service]\(新しい SAML S P サービスの作成\) ダイアログ ボックスを示すスクリーンショット。](./media/headerf5-tutorial/configure24.png)

    ![[Security Settings]\(セキュリティ設定\) が選択されている [Create New SAML S P Service]\(新しい SAML S P サービスの作成\) ダイアログ ボックスを示すスクリーンショット。](./media/headerf5-tutorial/configure25.png)

    ![[Authentication Context]\(認証コンテキスト\) が選択されている [Create New SAML S P Service]\(新しい SAML S P サービスの作成\) ダイアログ ボックスを示すスクリーンショット。](./media/headerf5-tutorial/configure26.png)

    ![[Requested Attributes]\(要求された属性\) が選択されている [Create New SAML S P Service]\(新しい SAML S P サービスの作成\) ダイアログ ボックスを示すスクリーンショット。](./media/headerf5-tutorial/configure27.png)

    ![[Advanced Settings]\(詳細設定\) が選択されている [Edit SAML S P Service]\(SAML S P サービスの編集\) ダイアログ ボックスを示すスクリーンショット。](./media/headerf5-tutorial/configure28.png)

### <a name="create-idp-connector"></a>Idp コネクタを作成する

1. **[Bind/Unbind IdP Connectors]\(IdP コネクタのバインド/バインド解除\)** ボタンをクリックして **[Create New IdP Connector]\(新しい IdP コネクタの作成\)** を選択し、 **[From Metadata]\(メタデータから\)** オプションを選択して、次の手順を実行します。
 
    ![[Create New I d P Connector]\(新しい I d P コネクタの作成\) が選択されている [Edit SAML I d Ps that use this S P]\(この S P を使用する SAML I d P の編集\) ダイアログ ボックスを示すスクリーンショット。](./media/headerf5-tutorial/configure29.png)

    a. Azure AD からダウンロードした metadata.xml ファイルを参照し、**ID プロバイダー名** を指定します。

    b. **[OK]** をクリックします。

    c. コネクタが作成され、metadata xml ファイルから自動的に証明書が準備されます。
    
    ![[Create New SAML I d P Connector]\(新しい SAML I d P コネクタの作成\) ダイアログ ボックスを示すスクリーンショット。](./media/headerf5-tutorial/configure30.png)

    d. すべての要求を Azure AD に送信するように F5BIG-IP を構成します。

    e. **[Add New Row]\(新しい行の追加\)** をクリックし、前の手順で作成した **AzureIDP** を選択して、次のように指定します。 

    f. **[Matching Source]\(一致するソース\) = %{session.server.landinguri}** 

    g. **[Matching Value]\(一致する値\) = /** *

    h. **[update]\(更新\)** をクリックします。

    i. **[OK]**

    j. **SAML IDP の設定は完了です**。
    
    ![[Edit SAML I d Ps that use this S P]\(この S P を使用する SAML I d P の編集\) ダイアログ ボックスを示すスクリーンショット。](./media/headerf5-tutorial/configure31.png)

### <a name="configure-f5-policy-to-redirect-users-to-azure-saml-idp"></a>Azure SAML IDP にユーザーをリダイレクトするよう F5 ポリシーを構成する

1. Azure SAML IDP にユーザーをリダイレクトするよう F5 ポリシーを構成するには、次の手順に従います。

    a. **[Main]\(メイン\) > [Access]\(アクセス\) > [Profile/Policies]\(プロファイルまたはポリシー\) > [Access Profiles]\(アクセス プロファイル\)** の順にクリックします。

    b. **[作成]** ボタンをクリックします。

    ![[Access Profiles]\(アクセス プロファイル\) ページを示すスクリーンショット。](./media/headerf5-tutorial/configure32.png)
 
    c. **名前** を指定します (この例では HeaderAppAzureSAMLPolicy)。

    d. 他の設定をカスタマイズできます。F5 のドキュメントを参照してください。

    ![[General Properties]\(全般プロパティ\) ページを示すスクリーンショット。](./media/headerf5-tutorial/configure33.png)

    ![[General Properties]\(全般プロパティ\) ページ (続き) を示すスクリーンショット。](./media/headerf5-tutorial/configure34.png) 

    e. **[Finished]\(完了\)** をクリックします。

    f. ポリシーの作成が完了したら、そのポリシーをクリックして **[Access Policy]\(アクセス ポリシー\)** タブに移動します。

    ![[General Properties]\(全般プロパティ\) が表示された [Access Policy]\(アクセス ポリシー\) タブを示すスクリーンショット。](./media/headerf5-tutorial/configure35.png)
 
    g. **[Visual Policy editor]\(ビジュアル ポリシー エディター\)** をクリックし、 **[Access Policy for Profile]\(プロファイルのアクセス ポリシー\)** リンクを編集します。

    h. ビジュアル ポリシー エディターで [+ Sign]\(+ 署名\) をクリックし、 **[SAML Auth]\(SAML 認証\)** を選択します。

    ![アクセス ポリシーを示すスクリーンショット。](./media/headerf5-tutorial/configure36.png)

    ![[SAML Auth]\(SAML 認証\) が選択されている検索ダイアログ ボックスを示すスクリーンショット。](./media/headerf5-tutorial/configure37.png)
 
    i. **[Add Item]\(項目の追加\)** をクリックします。

    j. **[Properties]\(プロパティ\)** で **名前** を指定し、 **[AAA Server]\(AAA サーバー\)** で、先ほど構成した SP を選択して、 **[SAVE]\(保存\)** をクリックします。
 
    ![A A A サーバーを含む項目のプロパティを示すスクリーンショット。](./media/headerf5-tutorial/configure38.png)

    k. 基本的なポリシーは完成です。このポリシーをカスタマイズすることで、追加のソースまたは属性ストアを組み込むことができます。

    ![カスタマイズされたポリシーを示すスクリーンショット。](./media/headerf5-tutorial/configure39.png)
 
    l. 上部にある **[Apply Access Policy]\(アクセス ポリシーの適用\)** リンクを必ずクリックしてください。

### <a name="apply-access-profile-to-the-virtual-server"></a>仮想サーバーにアクセス プロファイルを適用する

1. F5 BIG-IP APM で受信トラフィックにプロファイルの設定を適用し、先ほど定義したアクセス ポリシーを実行するために、仮想サーバーにアクセス プロファイルを割り当てます。

    a. **[Main]\(メイン\)**  >  **[Local Traffic]\(ローカル トラフィック\)**  >  **[Virtual Servers]\(仮想サーバー\)** の順にクリックします。

    ![[Virtual Servers List]\(仮想サーバー リスト\) ページを示すスクリーンショット。](./media/headerf5-tutorial/configure40.png)
 
    b. 仮想サーバーをクリックし、 **[Access Policy]\(アクセス ポリシー\)** セクションまでスクロールして、 **[Access Profile]\(アクセス プロファイル\)** ボックスの一覧から、作成済みの SAML ポリシー (この例では HeaderAppAzureSAMLPolicy) を選択します。

    c. **[update]\(更新\)** をクリックします。
 
    ![[Access Policy]\(アクセス ポリシー\) ペインを示すスクリーンショット。](./media/headerf5-tutorial/configure41.png)

    d. 受信したアサーションからカスタム SAML 属性を抽出し、それらを HTTP ヘッダーとしてバックエンドのテスト アプリケーションに渡す F5 BIG-IP iRule® を作成します。 **[Main]\(メイン\) > [Local Traffic]\(ローカル トラフィック\) > [iRules] > [iRule List]\(iRule リスト\) > [create]\(作成\)** の順にクリックします。

    ![[Local Traffic]\(ローカル トラフィック\) の [iRule List]\(iRule リスト\) を示すスクリーンショット。](./media/headerf5-tutorial/configure42.png)
 
    e. 以下の F5 BIG-IP iRule テキストを [Definition]\(定義\) ウィンドウに貼り付けます。

    ![[New iRule]\(新しい iRule\) ページを示すスクリーンショット。](./media/headerf5-tutorial/configure43.png)
 
    when RULE_INIT {  set static::debug 0  }  when ACCESS_ACL_ALLOWED {

    set AZUREAD_USERNAME [ACCESS::session data get "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"] if { $static::debug } { log local0. "AZUREAD_USERNAME = $AZUREAD_USERNAME" } if { !([HTTP::header exists "AZUREAD_USERNAME"]) } { HTTP::header insert "AZUREAD_USERNAME" $AZUREAD_USERNAME }

    set AZUREAD_DISPLAYNAME [ACCESS::session data get "session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/displayname"] if { $static::debug } { log local0. "AZUREAD_DISPLAYNAME = $AZUREAD_DISPLAYNAME" } if { !([HTTP::header exists "AZUREAD_DISPLAYNAME"]) } { HTTP::header insert "AZUREAD_DISPLAYNAME" $AZUREAD_DISPLAYNAME }

    set AZUREAD_EMAILADDRESS [ACCESS::session data get "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress"] if { $static::debug } { log local0. "AZUREAD_EMAILADDRESS = $AZUREAD_EMAILADDRESS" } if { !([HTTP::header exists "AZUREAD_EMAILADDRESS"]) } { HTTP::header insert "AZUREAD_EMAILADDRESS" $AZUREAD_EMAILADDRESS }}

    **サンプル出力 (下記)**

    ![サンプル出力を示すスクリーンショット。](./media/headerf5-tutorial/configure44.png)
 
### <a name="create-f5-test-user"></a>F5 テスト ユーザーの作成

このセクションでは、F5 で B.Simon というユーザーを作成します。 [F5 クライアント サポート チーム](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45)と連携して、F5 プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる F5 のサインオン URL にリダイレクトされます。  

* F5 のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した F5 に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [F5] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した F5 に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

> [!NOTE]
> F5 BIG-IP APM を[今すぐ購入](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/f5-networks.f5-big-ip-best?tab=Overview)できます。

## <a name="next-steps"></a>次の手順

F5 を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
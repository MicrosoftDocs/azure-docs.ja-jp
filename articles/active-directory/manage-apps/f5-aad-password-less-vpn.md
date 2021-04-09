---
title: F5 VPN を使用した Azure AD のセキュリティで保護されたハイブリッド アクセス | Microsoft Docs
description: パスワードレス VPN 用の Azure Active Directory シングル サインオン (SSO) と F5 BIG-IP の統合
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e177f1ce55d803f54bb2553078441557e5c191
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98730889"
---
# <a name="tutorial-for-azure-active-directory-single-sign-on-integration-with-f5-big-ip-for-password-less-vpn"></a>パスワードレス VPN 用の Azure Active Directory シングル サインオンと F5 BIG-IP の統合

このチュートリアルでは、セキュリティで保護されたハイブリッド アクセス (SHA) のために F5 の BIG-IP に基づく Secure Socket Layer 仮想プライベート ネットワーク (SSL) ソリューションと Azure Active Directory (AD) を統合する方法について説明します。

BIG-IP SSL-VPN を Azure AD と統合すると、以下を含む[多くの重要なメリット](f5-aad-integration.md)が得られます。

- [Azure AD の事前認証および認可](../../app-service/overview-authentication-authorization.md)によるゼロ トラスト ガバナンスの強化

- [VPN サービスに対するパスワードレス認証](https://www.microsoft.com/security/business/identity/passwordless)

- 1 つのコントロール プレーンからの ID とアクセスの管理 - [Azure portal](https://portal.azure.com/#home)

しかし、このように優れた価値が追加されるにもかかわらず、従来の VPN は依然として「内部は信頼され、外部は信頼されない」というネットワーク境界の概念を前提としています。 企業の資産がエンタープライズ データ センターの内部に限定されず、固定された境界のないマルチクラウド環境にまたがって存在するようになったため、このモデルでは真のゼロ トラスト体制を効果的に実現できなくなりました。 このため、お客様には、[アプリケーションごとにアクセス](../fundamentals/five-steps-to-full-application-integration-with-azure-ad.md)を管理する際に、ID 主導のアプローチへの移行を検討することをお勧めします。

## <a name="scenario-description"></a>シナリオの説明

このシナリオでは、SSL VPN サービスの BIG-IP APM インスタンスは SAML サービス プロバイダー (SP) として構成され、Azure AD は事前認証を提供する信頼された SAML IDP になります。 その後、Azure AD からのシングル サインオン (SSO) がクレームベース認証によって BIG-IP APM に提供され、シームレスな VPN アクセス エクスペリエンスが提供されます。

![SSL-VPN のアーキテクチャを示す画像](media/f5-sso-vpn/ssl-vpn-architecture.png)

>[!NOTE]
>このガイド全体で参照されている文字列または値の例はすべて、実際の環境に合わせて置き換える必要があります。

## <a name="prerequisites"></a>[前提条件]

F5 BIG-IP に関する事前の経験や知識は必要ありませんが、次のものが必要です。

- Azure AD [無料サブスクリプション](https://azure.microsoft.com/trial/get-started-active-directory/) (またはそれ以上)

- ユーザー ID を[オンプレミス ディレクトリから Azure AD に同期する](../hybrid/how-to-connect-sync-whatis.md)必要がある。

- Azure AD アプリケーション管理者の[アクセス許可](../roles/permissions-reference.md#application-administrator)を持つアカウント

- BIG-IP との間でクライアント トラフィックをルーティングする既存の BIG-IP インフラストラクチャ、または [BIG-IP Virtual Edition を Azure にデプロイする](f5-bigip-deployment-guide.md)。

- BIG-IP で公開された VPN サービスのレコードがパブリック DNS (テスト中はテスト クライアントの localhost ファイル) に存在する必要がある

- HTTPS 経由でサービスを公開するために必要な SSL 証明書を使用して BIG-IP がプロビジョニングされている必要がある。

[F5 BIG-IP の用語](https://www.f5.com/services/resources/glossary)に慣れておくと、このチュートリアルで参照されているさまざまなコンポーネントを理解するのに役立ちます。

>[!NOTE]
>Azure は絶えず進化しているため、このガイドの手順と Azure portal に表示される内容の間に微妙な違いがあっても驚かないでください。 スクリーンショットは BIG-IP v15 のものですが、v 13.1 のものとかなり似ています。

## <a name="add-f5-big-ip-from-the-azure-ad-gallery"></a>Azure AD ギャラリーから F5 BIG-IP を追加する

BIG-IP との間に SAML フェデレーション信頼を設定すると、公開された VPN サービスへのアクセスを許可する前に、Azure AD BIG-IP で事前認証と[条件付きアクセス](../conditional-access/overview.md)を Azure AD に渡すことができます。

1. アプリケーション管理者の権限を持つアカウントを使用して、Azure AD ポータルにサインインします

2. 左のナビゲーション ウィンドウで、 **[Azure Active Directory] サービス** を選択します

3. **[エンタープライズ アプリケーション]** に移動して、上部のリボンから **[新しいアプリケーション]** を選択します。

4. ギャラリーで F5 を検索し、 **[F5 BIG-IP APM Azure AD 統合]** を選択します。

5. アプリケーションの名前を指定してから、 **[追加/作成]** を選択して、それをテナントに追加します。 この名前は、Azure および Office 365 のアプリケーション ポータルでアイコンとしてユーザーに表示されます。 その特定のサービスを反映した名前を指定してください。 たとえば、「VPN」とします。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

1. 新しい F5 アプリケーションのプロパティが表示されている状態で、 **[管理]**  >  **[シングル サインオン]** の順に移動します

2. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。 シングル サインオンの設定を保存するかどうかを確認するメッセージをスキップするため、 **[いいえ、後で保存します]** を選択します。

3. **[SAML でシングル サインオンをセットアップします]** メニューで **[基本的な SAML 構成]** のペン アイコンを選択して、以下の詳細を指定します。

   - 事前に定義された **[識別子 URL]** を、BIG-IP で公開されたサービスの URL に置き換えます。 例: `https://ssl-vpn.contoso.com`

   - **[応答 URL]** テキスト ボックスで同じことを行い、SAML エンドポイントのパスを含めます。 例: `https://ssl-vpn.contoso.com/saml/sp/profile/post/acs`

   - この構成だけでは、アプリケーションは IDP 開始モードで動作します。その場合、Azure AD では、SAML アサーションをユーザーに発行してから BIG-IP SAML サービスにリダイレクトします。 IDP 開始モードをサポートしていないアプリの場合は、BIG-IP SAML サービスの **[サインオン URL]** を指定します。 たとえば、「 `https://ssl-vpn.contoso.com` 」のように入力します。

   - [ログアウト URL] には、公開されているサービスのホスト ヘッダーを先頭に付加した BIG-IP APM シングル ログアウト (SLO) エンドポイントを入力します。 例: `https://ssl-vpn.contoso.com/saml/sp/profile/redirect/slr`

   SLO URL を指定すると、ユーザーのサインアウト後に BIG-IP と Azure AD の両端でユーザー セッションが確実に終了します。BIG-IP APM には、特定のアプリケーション URL を呼び出すときにすべてのセッションを終了するための[オプション](https://support.f5.com/csp/article/K12056)も用意されています。

![基本的な SAML 構成を示す画像](media/f5-sso-vpn/basic-saml-configuration.png).

>[!NOTE]
>TMOS v16 から、SAML SLO エンドポイントが /saml/sp/profile/redirect/slo に変更されました

4. **[保存]** を選択してから、[SAML 構成] メニューを終了し、SSO テストのメッセージをスキップします。

**[ユーザー属性とクレーム]** セクションのプロパティを確認します。Azure AD では、BIG-IP APM 認証のためにこれらがユーザーに発行されるためです。

![ユーザー属性とクレームを示す画像](media/f5-sso-vpn/user-attributes-claims.png)

BIG-IP で公開されたサービスで想定しているその他の特定の要求を自由に追加できます。ただし、既定のセットに追加して定義された要求は、Azure AD に設定された属性として存在する場合にのみ発行されることに注意してください。 同様に、ディレクトリの[ロールまたはグループ](../hybrid/how-to-connect-fed-group-claims.md)のメンバーシップも、要求として発行する前に Azure AD 内のユーザー オブジェクトに対して定義する必要があります。

![フェデレーション メタデータのダウンロード リンクを示す画像](media/f5-sso-vpn/saml-signing-certificate.png)

Azure AD によって作成された SAML 署名証明書には 3 年間の有効期間があるため、Azure AD で公開されたガイダンスを使用して管理する必要があります。

### <a name="azure-ad-authorization"></a>Azure AD 認可

既定では、Azure AD ではサービスへのアクセスが許可されているユーザーにのみトークンが発行されます。

1. アプリケーションの構成がまだ表示されている状態で、 **[ユーザーとグループ]** を選択します

2. **[ユーザーの追加]** を選択し、[割り当ての追加] メニューで **[ユーザーとグループ]** を選択します

3. **[ユーザーとグループ]** ダイアログで、VPN へのアクセスが認可されているユーザーのグループを追加してから、 **[選択]**  >  **[割り当て]** の順に選択します

![ユーザーの追加リンクを示す画像 ](media/f5-sso-vpn/add-user-link.png)

4. これで、SAML フェデレーション信頼の Azure AD の部分が完了しました。 SAML 事前認証のために、SSL-VPN サービスを公開するように BIG-IP APM を設定し、対応する一連のプロパティを使用して信頼を完了するように BIG-IP APM を構成できるようになりました。

## <a name="big-ip-apm-configuration"></a>BIG-IP APM 構成

### <a name="saml-federation"></a>SAML フェデレーション

次のセクションでは、Azure AD で VPN サービスのフェデレーションを完了するために必要な BIG-IP SAML サービス プロバイダーと、対応する SAML IDP オブジェクトを作成します。

1. **[アクセス]**  >  **[フェデレーション]**  >  **[SAML サービス プロバイダー]**  >  **[ローカル SP サービス]** の順に移動して、 **[作成]** をクリックします

![BIG-IP SAML 構成を示す画像](media/f5-sso-vpn/bigip-saml-configuration.png)

2. **[名前]** 、先ほど Azure AD で定義したのと同じ **[エンティティ ID]** の値、アプリケーションへの接続に使用されるホストの FQDN を入力します

![新しい SAML SP サービスの作成を示す画像](media/f5-sso-vpn/create-new-saml-sp.png)

   SP の **[名前]** の設定が必要になるのは、エンティティ ID が発行された URL のホスト名部分と完全に一致しないか、通常のホスト名ベースの URL 形式でない場合のみです。 エンティティ ID が `urn:ssl-vpn:contosoonline` の場合は、公開しているアプリケーションの外部スキームとホスト名を指定します。

3. 下へスクロールして新しい **SAML SP オブジェクト** を選択し、 **[IDP コネクタのバインドまたはバインド解除]** を選択します。

![ローカル SP サービスとのフェデレーションの作成を示す画像](media/f5-sso-vpn/federation-local-sp-service.png)

4. **[新しい IDP コネクタの作成]** を選択し、ドロップダウン メニューから **[メタデータから]** を選択します

![新しい IDP コネクタの作成を示す画像](media/f5-sso-vpn/create-new-idp-connector.png)

5. 先ほどダウンロードしたフェデレーション メタデータ XML ファイルを参照し、外部 SAML IDP を表す APM オブジェクトの **[ID プロバイダー名]** を指定します

6. **[新しい行の追加]** を選択して、新しい Azure AD 外部 IDP コネクタを選択します。

![外部 IDP コネクタを示す画像](media/f5-sso-vpn/external-idp-connector.png)

7. **[更新]** を選択して SAML SP オブジェクトを SAML IDP オブジェクトにバインドし、その後 **[OK]** を選択します。

![SP を使用する SAML IDP を示す画像](media/f5-sso-vpn/saml-idp-using-sp.png)

### <a name="webtop-configuration"></a>Web トップ構成

次の手順では、BIG-IP の独自の Web ポータルでユーザーに SSL VPN を提供できるようにします。

1. **[アクセス]**  >  **[Web トップ]**  >  **[Web トップの一覧]** の順に移動して、 **[作成]** を選択します。

2. ポータルに名前を付け、種類を **[完全]** に設定します。 たとえば、「 `Contoso_webtop` 」のように入力します。

3. 残りの設定を調整してから、 **[完了]** を選択します。

![Web トップ構成を示す画像](media/f5-sso-vpn/webtop-configuration.png)

### <a name="vpn-configuration"></a>VPN 構成

VPN 機能は複数の要素で構成されており、それぞれがサービス全体のさまざまな側面を制御します。

1. **[アクセス]**  >  **[接続/VPN]**  >  **[ネットワーク アクセス (VPN)]**  >  **[IPV4 リース プール]** の順に移動し、 **[作成]** を選択します。

2. VPN クライアントに割り当てられる IP アドレスのプールの名前を指定します。 たとえば、「Contoso_VPN_Pool」とします

3. 種類を **[IP アドレスの範囲]** に設定し、開始 IP と終了 IP を指定してから、 **[追加]** 、 **[完了]** の順に選択します。

![VPN 構成を示す画像](media/f5-sso-vpn/vpn-configuration.png)

ネットワーク アクセス リストでは、VPN プールの IP および DNS 設定とユーザー ルーティングのアクセス許可でサービスをプロビジョニングし、必要に応じてアプリケーションを起動することもできます。

1. **[アクセス]**  >  **[接続/VPN:ネットワーク アクセス (VPN)]**  >  **[ネットワーク アクセスの一覧]** の順に移動し、 **[作成]** を選択します。

2. VPN アクセス リストとキャプションの名前 (たとえば、「Contoso-VPN」) を指定してから、 **[完了]** を選択します。

![ネットワーク アクセス リスト内の VPN 構成を示す画像](media/f5-sso-vpn/vpn-configuration-network-access-list.png)

3. 上部のリボンから **[ネットワーク設定]** を選択し、以下の設定を追加します。

   • **[サポートされている IP バージョン]** :[IPv4]

   • **[IPV4 リース プール]** :先ほど作成した VPN プール (たとえば、「Contoso_VPN_Pool」) を選択します

![Contoso_VPN_Pool を示す画像](media/f5-sso-vpn/contoso-vpn-pool.png)

   [クライアント設定] のオプションを使用すると、VPN の確立時にクライアント トラフィックのルーティング方法に制限を適用できます。

4. **[完了]** を選択し、[DNS/ホスト] タブに移動して設定を追加します。

   • **[IPV4 プライマリ ネーム サーバー]** :使用している環境の DNS サーバーの IP

   • **[DNS の既定のドメイン サフィックス]** :この特定の VPN 接続のドメイン サフィックス。 たとえば、「contoso.com」などです

![既定のドメイン サフィックスを示す画像](media/f5-sso-vpn/domain-suffix.png)

[F5 の記事](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-network-access-11-5-0/2.html)では、好みに応じて残りの設定を調整する方法について詳しく説明されています。

次に、VPN サービスでサポートする必要がある各 VPN クライアントの種類用の設定を構成するために、BIG-IP 接続プロファイルが必要になります。 たとえば、Windows 10、OSX、Android などです。

1. **[アクセス]**  >  **[接続/VPN]**  >  **[接続]**  >  **[プロファイル]** の順に移動し、 **[追加]** を選択します。

2. プロファイル名 (たとえば、「Contoso_VPN_Profile」) を指定し、親プロファイルを **/common/connectivity** に設定します。

![新しい接続プロファイルの作成を示す画像](media/f5-sso-vpn/create-connectivity-profile.png)

F5 の[ドキュメント](https://techdocs.f5.com/kb/en-us/bigip-edge-apps.html)では、クライアント サポートの詳細について説明されています。

## <a name="access-profile-configuration"></a>アクセス プロファイル構成

VPN オブジェクトを構成したら、SAML 認証でそのサービスを使用できるようにするためのアクセス ポリシーが必要です。

1. **[アクセス]**  >  **[プロファイル/ポリシー]**  >  **[アクセス プロファイル (セッション単位のポリシー)]** の順に移動し、 **[作成]** を選択します

2. プロファイル名 (たとえば、「Contoso_network_access」) を指定し、プロファイルの種類として **[すべて]** を選択します

3. 下へスクロールして、 **[許容される言語]** の一覧に少なくとも 1 つの言語を追加し、 **[完了]** を選択します

![全般プロパティを示す画像](media/f5-sso-vpn/general-properties.png)

4. ビジュアル ポリシー エディターを別のブラウザー タブで起動するため、新しいアクセス プロファイルの [セッション単位のポリシー] フィールドで **[編集]** を選択します。

![セッション単位のポリシーを示す画像](media/f5-sso-vpn/per-session-policy.png)

5. **+** 記号を選択し、ポップアップで **[認証]**  >  **[SAML 認証]**  >  **[項目の追加]** の順に選択します。

6. SAML 認証 SP の構成で、先ほど作成した VPN SAML SP オブジェクトを選択してから、 **[保存]** を選択します。

![SAML 認証を示す画像](media/f5-sso-vpn/saml-authentication.png)

7. SAML 認証の成功分岐の **+** を選択します。

8. [割り当て] タブで、 **[高度なリソースの割り当て]** 、 **[項目の追加]** の順に選択します

![高度なリソースの割り当てを示す画像](media/f5-sso-vpn/advance-resource-assign.png)

9. ポップアップで、 **[新しいエントリ]** 、 **[追加/削除]** の順に選択します。

10. 子ウィンドウで、 **[ネットワーク アクセス]** を選択してから、先ほど作成したネットワーク アクセス プロファイルを選択します

![新しいネットワーク アクセス エントリの追加を示す画像](media/f5-sso-vpn/add-new-entry.png)

11. **[Webtop]\(Web トップ\)** タブに切り替えて、先ほど作成した Web トップ オブジェクトを追加します。

![Web トップ オブジェクトの追加を示す画像](media/f5-sso-vpn/add-webtop-object.png)

12. **[更新]** 、 **[保存]** の順に選択します。

13. 上部の [拒否] ボックス内のリンクを選択し、成功の分岐を **[許可]** に変更してから、 **[保存]** を選択します。

![新しいビジュアル ポリシー エディターを示す画像](media/f5-sso-vpn/vizual-policy-editor.png)

14. **[アクセス ポリシーの適用]** を選択してこれらの設定をコミットし、ビジュアル ポリシー エディターのタブを閉じます。

![新しいアクセス ポリシー マネージャーを示す画像](media/f5-sso-vpn/access-policy-manager.png)

## <a name="publish-the-vpn-service"></a>VPN サービスの公開

すべての設定が整ったら、VPN に接続しているクライアントをリッスンするフロントエンド仮想サーバーが APM で必要になります。

1. **[ローカル トラフィック]**  >  **[仮想サーバー]**  >  **[仮想サーバーの一覧]** の順に選択し、 **[作成]** を選択します。

2. VPN 仮想サーバーの **[名前]** (たとえば、「**VPN_Listener**」) を指定します。

3. クライアント トラフィックを受信するために、ルーティングが適用されている未使用の **[宛先 IP アドレス]** を仮想サーバーに提供します

4. [サービス ポート] を **443 HTTPS** に設定し、状態が **[有効]** になっていることを確認します

![新しい仮想サーバーを示す画像](media/f5-sso-vpn/new-virtual-server.png)

5. **[HTTP プロファイル]** を [http] に設定し、前提条件の一部としてプロビジョニングしたパブリック SSL 証明書の [SSL プロファイル (クライアント)] を追加します。

![SSL プロファイルを示す画像](media/f5-sso-vpn/ssl-profile.png)

6. [アクセス ポリシー] で、作成した VPN オブジェクトを使用するように **[アクセス プロファイル]** と **[接続プロファイル]** を設定します。

![アクセス ポリシーを示す画像](media/f5-sso-vpn/access-policy.png)

7. 完了したら、 **[完了]** を選択します。

8.  これで、SSL VPN サービスが公開され、SHA 経由でアクセスできるようになりました。そのためには、その URL に直接アクセスするか、Microsoft のアプリケーション ポータルを使用します。

## <a name="additional-resources"></a>その他のリソース

- [パスワードの終わり、これからはパスワードレス](https://www.microsoft.com/security/business/identity/passwordless)

- [条件付きアクセスとは](../conditional-access/overview.md)

- [リモート作業を可能にする Microsoft ゼロ トラスト フレームワーク](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [すべてのアプリを Azure AD と統合するための 5 つの手順](../fundamentals/five-steps-to-full-application-integration-with-azure-ad.md)

## <a name="next-steps"></a>次の手順

リモートの Windows クライアントでブラウザーを開き、**BIG-IP VPN サービス** の URL を参照します。 Azure AD に対して認証した後、BIG-IP の Web トップ ポータルと VPN ランチャーが表示されます。

![VPN ランチャーを示す画像](media/f5-sso-vpn/vpn-launcher.png)

VPN タイルを選択すると、BIG-IP Edge Client がインストールされ、SHA 用に構成された VPN 接続が確立されます。
F5 VPN アプリケーションは、Azure AD 条件付きアクセスのターゲット リソースとしても表示される必要があります。 条件付きアクセスのポリシーを作成し、ユーザーの Azure AD [パスワードレス認証](https://www.microsoft.com/security/business/identity/passwordless)も有効にするための[ガイダンス](../conditional-access/concept-conditional-access-policies.md)をご覧ください。
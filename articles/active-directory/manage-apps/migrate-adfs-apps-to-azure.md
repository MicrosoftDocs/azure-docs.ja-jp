---
title: アプリケーション認証を AD FS から Azure Active Directory に移動する
description: Azure Active Directory を使用して Active Directory フェデレーション サービス (AD FS) を置き換え、ユーザーがすべてのアプリケーションにシングル サインオンできるようにする方法について説明します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: ee1d863ccb974b30213179a1aba9e27d5a3a2bda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103418471"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>アプリケーション認証を Active Directory フェデレーション サービス (AD FS) から Azure Active Directory に移動する

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) によって提供されるユニバーサル ID プラットフォームでは、ユーザー、パートナー、顧客に対し、任意のプラットフォームやデバイスからアプリケーションにアクセスして共同作業を行うための 1 つの ID が提供されます。 Azure AD には、[ID 管理機能の完全なスイート](../fundamentals/active-directory-whatis.md)が備わっています。 アプリケーション認証と承認を Azure AD に標準化すると、次のような利点があります。

> [!TIP]
> この記事は開発者向けに記述されています。 Azure AD にアプリケーションを移行することを計画しているプロジェクト マネージャーや管理者は、「[Azure AD にアプリケーション認証を移行する](migrate-application-authentication-to-azure-active-directory.md)」を読むことを検討してください。

## <a name="azure-ad-benefits"></a>Azure AD の利点

ユーザー アカウントが含まれるオンプレミスのディレクトリがある場合は、ユーザーが認証を行うアプリケーションが多数存在する可能性があります。 これらの各アプリは、ユーザーが各自の ID を使用してアクセスできるように構成されています。

ユーザーは、オンプレミスの Active Directory で直接認証を行うこともあります。 Active Directory フェデレーション サービス (AD FS) は、標準に基づくオンプレミスの ID サービスです。 これにより、信頼されたビジネス パートナー間でシングル サインオン (SSO) 機能を使用する機能が拡張され、ユーザーが各アプリケーションに個別にサインインする必要がなくなります。 これはフェデレーション ID と呼ばれます。

多くの組織には、Microsoft 365 や Azure AD ベースのアプリの他に、AD FS に直接フェデレーションされたサービスとしてのソフトウェア (SaaS) アプリやカスタム基幹業務アプリがあります。

  ![オンプレミスに直接接続されているアプリケーション](media/migrate-adfs-apps-to-azure/app-integration-before-migration-1.png)

> [!Important]
> アプリケーションのセキュリティを強化するときの目標は、オンプレミスとクラウドの環境全体で 1 セットのアクセス制御とポリシーを使用することです。

  ![Azure AD 経由で接続されたアプリケーション](media/migrate-adfs-apps-to-azure/app-integration-after-migration-1.png)

## <a name="types-of-apps-to-migrate"></a>移行するアプリの種類

すべてのアプリケーション認証を Azure AD に移行するのが最善です。そのようにすると、ID とアクセスを管理のための単一のコントロール プレーンが提供されます。

アプリケーションでは、認証に最新のプロトコルまたはレガシ プロトコルが使用されている場合があります。 Azure AD への移行を計画する場合は、最新の認証プロトコル (SAML や Open ID Connect など) を使用するアプリを最初に移行することを検討してください。 これらのアプリは、Azure アプリ ギャラリーの組み込みコネクタを使用するか、Azure AD にアプリケーションを登録することによって、Azure AD で認証を行うように再構成できます。 古いプロトコルを使用しているアプリは、アプリケーション プロキシを使用して統合できます。

詳細については、次を参照してください。

* [Azure AD アプリケーション プロキシを使用してリモート ユーザー向けにオンプレミス アプリを発行する](what-is-application-proxy.md)。
* [アプリケーション管理とは](what-is-application-management.md)
* [アプリケーションを Azure AD に移行するための AD FS アプリケーション アクティビティ レポート](migrate-adfs-application-activity.md)。
* [Azure AD Connect Health を使用した AD FS の監視](../hybrid/how-to-connect-health-adfs.md)。

### <a name="the-migration-process"></a>移行プロセス

Azure AD にアプリ認証を移動するプロセスの間に、アプリと構成をテストします。 運用環境に移動するときも、移行テスト用に既存のテスト環境を引き続き使用することをお勧めします。 テスト環境を現在使用できない場合は、アプリケーションのアーキテクチャに応じて、[Azure App Service](https://azure.microsoft.com/services/app-service/) または [Azure Virtual Machines](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB) を使用して設定できます。

アプリの構成を開発するためのテスト Azure AD テナントを別に設定することもできます。

移行プロセスは次のようになります。

#### <a name="stage-1--current-state-the-production-app-authenticates-with-ad-fs"></a>ステージ 1 – 現在の状態: 運用アプリは AD FS で認証を行っている

  ![移行ステージ 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)

#### <a name="stage-2--optional-point-a-test-instance-of-the-app-to-the-test-azure-ad-tenant"></a>ステージ 2 – (省略可能) アプリのテスト インスタンスがテスト用の Azure AD テナントを指し示すようにする

アプリのテスト インスタンスがテスト用の Azure AD テナントを指し示すように構成を更新し、必要な変更を行います。 テスト用 Azure AD テナントのユーザーを使用してアプリをテストできます。 開発プロセスの間に、[Fiddler](https://www.telerik.com/fiddler) などのツールを使用して、要求と応答を比較および検証できます。

テスト用のテナントを別に設定できない場合は、このステージをスキップし、下のステージ 3 で説明するように、アプリのテスト インスタンスが運用環境の Azure AD テナントを指し示すようにします。

  ![移行ステージ 2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

#### <a name="stage-3--point-a-test-instance-of-the-app-to-the-production-azure-ad-tenant"></a>ステージ 3 – アプリのテスト インスタンスが運用環境の Azure AD テナントを指し示すようにする

アプリのテスト インスタンスが運用環境の Azure AD テナントを指し示すように構成を更新します。 これで、運用テナントのユーザーを使用してテストできるようになります。 必要に応じて、この記事の、ユーザーの移行に関するセクションを確認してください。

  ![移行ステージ 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

#### <a name="stage-4--point-the-production-app-to-the-production-azure-ad-tenant"></a>ステージ 4 – 運用アプリが運用環境の Azure AD テナントを指し示すようにする

運用アプリの構成を更新して、運用環境の Azure AD テナントを指し示すようにします。

  ![移行ステージ 4 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 AD FS で認証を行うアプリでは、アクセス許可に Active Directory グループを使用する可能性があります。 移行を始める前にオンプレミス環境と Azure AD の間で ID データを同期するには、[Azure AD Connect 同期](../hybrid/how-to-connect-sync-whatis.md)を使用します。 アプリケーションが移行されるときに同じユーザーにアクセス権を付与できるよう、移行前にそれらのグループとメンバーシップを確認します。

### <a name="line-of-business-apps"></a>基幹業務アプリ

基幹業務アプリは、組織が開発したものか、標準のパッケージ製品です。 たとえば、Windows Identity Foundation と (SharePoint Online ではなく) SharePoint アプリを基に構築されたアプリなどです。

OAuth 2.0、OpenID Connect、または WS-Federation を使用する基幹業務アプリは、[アプリの登録](../develop/quickstart-register-app.md)として Azure AD と統合できます。 [Azure portal](https://portal.azure.com/) のエンタープライズ アプリケーション ページで、SAML 2.0 または WS-Federation を使用するカスタム アプリを[ギャラリー以外のアプリケーション](add-application-portal.md)として統合します。

## <a name="saml-based-single-sign-on"></a>SAML ベースのシングル サインオン

認証に SAML 2.0 を使用するアプリは、[SAML ベースのシングル サインオン](what-is-single-sign-on.md) (SSO) 用に構成できます。 SAML ベースの SSO では、SAML 要求で定義する規則に基づいて、ユーザーを特定のアプリケーション ロールにマッピングできます。

SAML ベースの SSO 用に SaaS アプリケーションを構成するには、[クイック スタート: SAML ベースのシングル サインオンを設定する](add-application-portal-setup-sso.md)ことに関するページを参照してください。

  ![SSO SAML ユーザーのスクリーンショット ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

多くの SaaS アプリケーションには[アプリケーション固有のチュートリアル](../saas-apps/tutorial-list.md)が用意されており、SAML ベースの SSO の構成が順を追って説明されています。

  ![アプリのチュートリアル](media/migrate-adfs-apps-to-azure/app-tutorial.png)

一部のアプリは簡単に移行することができます。 カスタム要求などのより複雑な要件を持つアプリでは、Azure AD や Azure AD Connect での追加の構成が必要になる場合があります。 サポートされる要求マッピングの詳細については、「[方法: テナントの特定のアプリケーションに対するトークンに出力された要求のカスタマイズ (プレビュー)](../develop/active-directory-claims-mapping.md)」を参照してください。

属性をマッピングするときは、次の制限事項に注意してください。

* AD FS で発行できるすべての属性が、SAML トークンに出力される属性として Azure AD に表示されるわけではありません (属性が同期される場合でも)。 属性を編集するとき、 **[値]** ドロップダウン リストに、Azure AD で使用できるさまざまな属性が表示されます。 「[Azure AD Connect Sync のトピック](../hybrid/how-to-connect-sync-whatis.md)」の構成を調べて、必要な属性 (**samAccountName** など) が Azure AD に同期されていることを確認します。 拡張属性を使用して、Azure AD の標準ユーザー スキーマの一部ではないすべての要求を出力できます。
* 最も一般的なシナリオでアプリに必要なのは、**NameID** 要求とその他の一般的なユーザー識別子要求だけです。 他にも要求が必要かどうかを判断するには、AD FS からどの要求が発行されているかを調べます。
* 一部の要求は Azure AD で保護されているため、すべての要求を発行できるとは限りません。
* 暗号化された SAML トークンを使用する機能は現在プレビュー段階です。 「[方法: エンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ](../develop/active-directory-saml-claims-customization.md)」をご覧ください。

### <a name="software-as-a-service-saas-apps"></a>サービスとしてのソフトウェア (SaaS) アプリ

ユーザーが Salesforce、ServiceNow、Workday などの SaaS アプリにサインインし、AD FS に統合されている場合は、SaaS アプリに対してフェデレーション サインオンが使用されています。

ほとんどの SaaS アプリケーションは、Azure AD で構成できるようになっています。 [Azure AD アプリ ギャラリー](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)には SaaS アプリに対する事前構成済みの接続が多数用意されており、移行が簡単になります。 SAML 2.0 アプリケーションは、Azure AD アプリ ギャラリーを通じて、または[非ギャラリー アプリケーション](add-application-portal.md)として、Azure AD と統合することができます。

OAuth 2.0 または OpenID Connect を使用するアプリは、[アプリの登録](../develop/quickstart-register-app.md)と同じようにして Azure AD と統合できます。 レガシ プロトコルを使用するアプリでは、[Azure AD アプリケーション プロキシ](application-proxy.md)を使用して、Azure AD で認証を行うことができます。

SaaS アプリのオンボードに関する問題については、[SaaS アプリケーション統合サポートのエイリアス](mailto:SaaSApplicationIntegrations@service.microsoft.com)にお問い合わせください。

### <a name="saml-signing-certificates-for-sso"></a>SSO 用の SAML 署名証明書

署名証明書は、すべての SSO デプロイの重要な部分です。 Azure AD では、署名証明書を作成して、SaaS アプリケーションに対する SAML ベースのフェデレーション SSO が確立されます。 ギャラリー アプリケーションまたは非ギャラリー アプリケーションを追加した後、フェデレーション SSO オプションを使用して追加したアプリケーションを構成します。 「[Azure Active Directory でのフェデレーション シングル サインオンの証明書の管理](manage-certificates-for-federated-single-sign-on.md)」をご覧ください。

### <a name="saml-token-encryption"></a>SAML トークン暗号化

AD FS と Azure AD はどちらもトークン暗号化を提供します。これは、アプリケーションに届く SAML セキュリティ アサーションを暗号化する機能です。 アサーションは公開キーで暗号化され、対応する秘密キーを使用して受信側のアプリケーションによって復号化されます。 トークンの暗号化を構成するときに、X.509 証明書ファイルをアップロードして公開キーを提供します。

Azure AD SAML トークン暗号化とその構成方法については、「[Azure AD SAML トークン暗号化の構成方法](howto-saml-token-encryption.md)」を参照してください。  

> [!NOTE]
> トークン暗号化は、Azure Active Directory (Azure AD) のプレミアム機能です。 Azure AD のエディション、機能、および価格について詳しくは、[Azure AD の価格](https://azure.microsoft.com/pricing/details/active-directory/)に関するページをご覧ください。

### <a name="apps-and-configurations-that-can-be-moved-today"></a>今すぐ移動できるアプリと構成

現時点で簡単に移動できるのは、構成要素と要求の標準セットを使用する SAML 2.0 アプリなどです。 これらの標準項目には、次のものがあります。

* ユーザー プリンシパル名
* 電子メール アドレス
* 指定された名前
* Surname
* Azure AD メール属性、メール プレフィックス、従業員 ID、拡張属性 1 から 15、オンプレミスの **SamAccountName** 属性など、SAML **NameID** のような代替属性。 詳細については、「[NameIdentifier 要求の編集](../develop/active-directory-saml-claims-customization.md)」を参照してください。
* カスタム要求。

以下のものについては、Azure AD に移行するには、追加の構成手順が必要になります。

* AD FS のカスタム承認または多要素認証 (MFA) 規則。 これらは、[Azure AD 条件付きアクセス](../conditional-access/overview.md)機能を使用して構成します。
* 複数の応答 URL エンドポイントを持つアプリ。 これらは、PowerShell を使用して Azure AD で、または Azure portal インターフェイスで構成します。
* SAML バージョン 1.1 のトークンを必要とする SharePoint アプリなどの WS-Federation アプリ。 これらは、PowerShell を使用して手動で構成できます。 また、ギャラリーからの SharePoint アプリケーションと SAML 1.1 アプリケーションに対しては、事前に統合された汎用テンプレートを追加することもできます。 SAML 2.0 プロトコルがサポートされています。
* 複雑な要求発行変換規則。 サポートされる要求マッピングの詳細については、以下を参照してください。
   *  [Azure Active Directory における要求のマッピング](../develop/active-directory-claims-mapping.md)。
   * [Azure Active Directory のエンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ](../develop/active-directory-saml-claims-customization.md)。

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Azure AD で現時点ではサポートされていないアプリと構成

特定の機能を必要とするアプリは、現時点では移行することができません。

#### <a name="protocol-capabilities"></a>プロトコル機能

以下のプロトコル機能を必要とするアプリは、現時点では移行することができません。

* WS-Trust ActAs パターンのサポート
* SAML アーティファクト解決
* 署名済み SAML 要求の署名の検証
  > [!Note]
  > 署名済み要求は受け入れられますが、署名は検証されません。

  ‎Azure AD では、アプリケーションで事前に構成されたエンドポイントにのみトークンが返されるため、ほとんどの場合、署名の検証はおそらく必要ありません。

#### <a name="claims-in-token-capabilities"></a>トークン内の要求の機能

次に示すトークン内の要求の機能を必要とするアプリは、現時点では移行することができません。

* データが Azure AD に同期されていない場合、Azure AD ディレクトリ以外の属性ストアからの要求。 詳細については、「[Azure AD 同期 API の概要](/graph/api/resources/synchronization-overview?view=graph-rest-beta)」をご覧ください。
* ディレクトリの複数値属性の発行。 たとえば、現時点では、プロキシ アドレスに対して複数値の要求を発行することはできません。

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>AD FS から Azure AD へのアプリの設定のマッピング

移行では、まずアプリケーションがオンプレミスでどのように構成されているかを評価し、その構成を Azure AD にマッピングすることが必要です。 AD FS と Azure AD は動作が似ているため、信頼関係の構成、サインオンとサインアウトの URL、および識別子の概念は、両方に当てはまります。 Azure AD で簡単に構成できるように、アプリケーションの AD FS の構成設定を文書化します。

### <a name="map-app-configuration-settings"></a>アプリの構成設定のマッピング

次の表では、AD FS 証明書利用者の信頼と Azure AD エンタープライズ アプリケーションの間での、設定の最も一般的なマッピングについて説明します。

* AD FS – アプリに対する AD FS 証明書利用者の信頼で設定を探します。 証明書利用者を右クリックし、[プロパティ] を選択します。
* Azure AD – 設定は、アプリケーションの SSO プロパティごとに、[Azure portal](https://portal.azure.com/) 内で構成されます。

| 構成設定| AD FS| Azure AD での構成方法| SAML トークン |
| - | - | - | - |
| **アプリのサインオン URL** <p>サービス プロバイダー (SP) によって開始された SAML フローでユーザーがアプリにサインインするための URL。| 該当なし| SAML ベースのサインオンから [基本的な SAML 構成] を開きます| 該当なし |
| **アプリの応答 URL** <p>ID プロバイダー (IdP) から見たアプリの URL。 IdP は、ユーザーが IdP にサインインした後、ここでユーザーとトークンを送信します。  ‎これは、**SAML アサーション コンシューマー エンドポイント** とも呼ばれます。| **[エンドポイント]** タブを選択します| SAML ベースのサインオンから [基本的な SAML 構成] を開きます| SAML トークンの Destination 要素。 値の例: `https://contoso.my.salesforce.com` |
| **アプリのサインアウト URL** <p>これは、ユーザーがアプリからサインアウトしたときにサインアウト クリーンアップ要求が送信される URL です。 IdP は、他のすべてのアプリからユーザーをサインアウトさせる要求も送信します。| **[エンドポイント]** タブを選択します| SAML ベースのサインオンから [基本的な SAML 構成] を開きます| 該当なし |
| **アプリ識別子** <p>これは、IdP から見たアプリの識別子です。 多くの場合、サインオン URL 値が識別子に使用されます (そうでない場合もあります)。  ‎アプリではこれを "エンティティ ID" と呼ぶこともあります。| **[識別子]** タブを選択します|SAML ベースのサインオンから [基本的な SAML 構成] を開きます| SAML トークンの **Audience** 要素にマッピングします。 |
| **アプリのフェデレーション メタデータ** <p>これは、アプリのフェデレーション メタデータの場所です。 エンドポイントや暗号化証明書などの特定の構成設定を自動更新するために、IdP によって使用されます。| **[監視]** タブをクリックします| 該当なし。 Azure AD では、アプリケーション フェデレーション メタデータの使用は直接はサポートされていません。 フェデレーション メタデータは手動でインポートできます。| 該当なし |
| **ユーザー識別子/名前 ID** <p>Azure AD または AD FS のユーザー ID をアプリに一意に示すために使用される属性。  ‎この属性は、通常、ユーザーの UPN またはメール アドレスです。| 要求規則。 ほとんどの場合、要求規則では末尾が **NameIdentifier** の型の要求が発行されます。| この識別子は、ヘッダー **[ユーザー属性と要求]** にあります。 既定では、UPN が使用されます| SAML トークンの **NameID** 要素にマッピングします。 |
| **その他の要求** <p>IdP からアプリによく送信される他の要求情報の例としては、名、姓、メール アドレス、グループ メンバーシップなどがあります。| AD FS では、証明書利用者のその他の要求規則となっています。| この識別子は、ヘッダー **[ユーザー属性と要求]** にあります。 **[表示]** を選択し、他のすべてのユーザー属性を編集します。| 該当なし |

### <a name="map-identity-provider-idp-settings"></a>ID プロバイダー (IdP) の設定のマッピング

SSO 対応の Azure AD に対して AD FS を指し示すようにアプリケーションを構成します。 ここでは、SAML プロトコルを使用する SaaS アプリに焦点を当てます。 ただし、この概念はカスタム基幹業務アプリにも及びます。

> [!NOTE]
> Azure AD の構成値は、{tenant-id} を Azure テナント ID に置き換え、{application-id} をアプリケーション ID に置き換えるパターンに従います。 この情報は、[Azure portal](https://portal.azure.com/) の **[Azure Active Directory] > [プロパティ]** にあります。

* テナント ID を確認するには [ディレクトリ ID] を選択します。
* アプリケーション ID を確認するには [アプリケーション ID] を選択します。

 大まかには、SaaS アプリの主要な構成要素と Azure AD は次のようにマッピングします。

| 要素| 構成値 |
| - | - |
| ID プロバイダーの発行者| https:\//sts.windows.net/{tenant-id}/ |
| ID プロバイダーのログイン URL| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| ID プロバイダーのログアウト URL| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| フェデレーション メタデータの場所| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |

### <a name="map-sso-settings-for-saas-apps"></a>SaaS アプリに対する SSO の設定のマッピング

SaaS アプリでは、認証要求の送信先と、受信したトークンの検証方法がわかっている必要があります。 次の表では、アプリで SSO 設定を構成するための要素と、その値、または AD FS と Azure AD 内での場所について説明します

| 構成設定| AD FS| Azure AD での構成方法 |
| - | - | - |
| **IdP のサインオン URL** <p>アプリから見た IdP のサインオン URL (ユーザーがログインのためにリダイレクトされる場所)。| AD FS のサインオン URL は、AD FS フェデレーション サービス名に "/adfs/ls/" を付加したものです。 <p>例: `https://fs.contoso.com/adfs/ls/`| {tenant-id} をテナント ID に置き換えます。 <p> ‎SAML-P プロトコルを使用するアプリの場合: [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>‎WS-Federation プロトコルを使用するアプリの場合: [https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **IdP のサインアウト URL**<p>アプリから見た IdP のサインアウト URL (ユーザーがアプリのサインアウトを選択したときにリダイレクトされる場所)。| サインアウト URL はサインオン URL と同じであるか、同じ URL に "wa=wsignout1.0" を付加したものです。 例: `https://fs.contoso.com/adfs/ls/?wa=wsignout1.0`| {tenant-id} をテナント ID に置き換えます。<p>SAML-P プロトコルを使用するアプリの場合:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> ‎WS-Federation プロトコルを使用するアプリの場合: [https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **トークン署名証明書**<p>IdP では、発行されたトークンに署名するために、証明書の秘密キーが使用されます。 アプリが信頼するように構成されているのと同じ IdP からトークンが来ていることを確認します。| AD FS トークン署名証明書は、[AD FS の管理] の **[証明書]** の下にあります。| Azure portal で、アプリケーションの **[シングル サインオンのプロパティ]** の **[SAML 署名証明書]** ヘッダーの下にあります。 ここで、アプリにアップロードするための証明書をダウンロードすることができます。  <p>アプリケーションに複数の証明書がある場合は、フェデレーション メタデータ XML ファイルですべての証明書を確認することができます。 |
| **識別子/"発行者"**<p>アプリから見た IdP の識別子 ("発行者 ID" と呼ばれる場合もあります)。<p>‎SAML トークンでは、値は Issuer 要素として表示されます。| AD FS の識別子は、通常、 **[サービス] > [フェデレーション サービスのプロパティの編集]** の下にある [AD FS の管理] のフェデレーション サービス識別子です。 例: `http://fs.contoso.com/adfs/services/trust`| {tenant-id} をテナント ID に置き換えます。<p>https:\//sts.windows.net/{tenant-id}/ |
| **IdP のフェデレーション メタデータ**<p>IdP の一般公開されているフェデレーション メタデータの場所。 (一部のアプリでは、管理者によって個別に構成される URL、識別子、およびトークン署名証明書の代わりに、フェデレーション メタデータを使用します)。| AD FS フェデレーション メタデータ URL は、[AD FS の管理] にあり、これは **[サービス] > [エンドポイント] > [メタデータ] > [種類: フェデレーション メタデータ]** の下で確認できます。 例: `https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`| Azure AD での対応する値は、[https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml) というパターンに従います。 {TenantDomainName} は、"contoso.onmicrosoft.com" という形式のテナントの名前に置き換えます。   <p>詳細については、「[フェデレーション メタデータ](../azuread-dev/azure-ad-federation-metadata.md)」を参照してください。 |

## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>AD FS セキュリティ ポリシーを Azure AD で表す

アプリの認証を Azure AD に移動する場合は、既存のセキュリティ ポリシーから、Azure AD で使用できる同等または代替のバリアントへのマッピングを作成します。 アプリの所有者によって要求されるセキュリティ標準に適合しながら、これらのマッピングを確実に実行できるようにすることで、残りのアプリの移行が大幅に容易になります。

各規則の例について、ここでは、AD FS での規則の表示方法、AD FS の規則言語と同等のコード、および Azure AD にこれをマップする方法を示します。

### <a name="map-authorization-rules"></a>承認規則のマッピング

AD FS の承認規則のさまざまな種類の例と、それらを Azure AD にマップする方法を次に示します。

#### <a name="example-1-permit-access-to-all-users"></a>例 1:すべてのユーザーにアクセスを許可する

AD FS ですべてのユーザーにアクセスを許可する:

  ![[SAML によるシングル サインオンの設定] ダイアログ ボックスを示すスクリーンショット。](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-1.png)

これは、次のいずれかの方法で Azure AD にマッピングされます。

1. **[ユーザーの割り当てが必要]** を **[いいえ]** に設定します。

    ![SaaS アプリのアクセス制御ポリシーを編集する ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    > [!Note]
    > **[ユーザーの割り当てが必要]** を **[はい]** に設定すると、アクセスできるようにするにはユーザーをアプリケーションに割り当てる必要があります。 **[いいえ]** に設定すると、すべてのユーザーがアクセスできます。 このスイッチでは、 **[マイ アプリ]** エクスペリエンスでユーザーに表示されるものは制御されません。

1. **[ユーザーとグループ]** タブで、アプリケーションを **[すべてのユーザー]** 自動グループに割り当てます。 既定の **すべてのユーザー** グループを使用できるようにするには、Azure AD テナントで[動的グループを有効にする](../enterprise-users/groups-create-rule.md)必要があります。

    ![Azure AD でのマイ SaaS アプリ ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)

#### <a name="example-2-allow-a-group-explicitly"></a>例 2:グループを明示的に許可する

AD FS での明示的なグループの承認:

  ![要求規則 Allow domain admins の [規則の編集] ダイアログ ボックスを示すスクリーンショット。](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)

この規則を Azure AD にマップするには、次のようにします。

1. [Azure portal](https://portal.azure.com/) で、AD FS のユーザーのグループに対応する[ユーザー グループを作成します](../fundamentals/active-directory-groups-create-azure-portal.md)。
1. グループにアプリのアクセス許可を割り当てます。

    ![割り当てを追加する ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)

#### <a name="example-3-authorize-a-specific-user"></a>例 3: 特定のユーザーを承認する

AD FS での明示的なユーザーの承認:

  ![[入力方向の要求の種類] に [プライマリ SID] が表示されている要求規則 Allow a specific user の [規則の編集] ダイアログ ボックスを示すスクリーンショット。](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

この規則を Azure AD にマップするには、次のようにします。

* [Azure portal](https://portal.azure.com/) で、次に示すように、アプリの [割り当ての追加] タブを使用してアプリにユーザーを追加します。

    ![Azure でのマイ SaaS アプリ ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

### <a name="map-multi-factor-authentication-rules"></a>多要素認証規則のマッピング

AD FS とフェデレーションしているため、[Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) と AD FS のオンプレミスでの展開は、移行後も引き続き機能します。 ただし、Azure AD の条件付きアクセス ワークフローに関連付けられている Azure の組み込みの MFA 機能に移行することを検討してください。

AD FS における MFA 規則の種類の例と、さまざまな条件に基づいてそれらを Azure AD にマップする方法を次に示します。

AD FS での MFA 規則の設定:

  ![Azure portal の Azure AD の [条件] を示すスクリーンショット。](media/migrate-adfs-apps-to-azure/mfa-settings-common-for-all-examples.png)

#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>例 1:ユーザーまたはグループに基づいて MFA を適用する

ユーザーやグループのセレクターは、グループごと (グループ SID) またはユーザーごと (プライマリ SID) に MFA を適用できる規則です。 ユーザーやグループの割り当てとは別に、AD FS MFA 構成 UI のすべての追加のチェック ボックスは、ユーザーやグループの規則が適用された後に評価される追加の規則として機能します。

Azure AD でユーザーまたはグループに対して MFA 規則を指定します。

1. [新しい条件付きアクセス ポリシー](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)を作成します。
1. **[割り当て]** を選択します。 MFA を適用するユーザーまたはグループを追加します。
1. 次に示すように、 **[アクセス制御]** のオプションを構成します。

    ‎![アクセス権を付与できる [許可] ウィンドウを示すスクリーンショット。](media/migrate-adfs-apps-to-azure/mfa-users-groups.png)

 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>例 2:未登録のデバイスに MFA を適用する

Azure AD で未登録のデバイスに対する MFA 規則を指定します。

1. [新しい条件付きアクセス ポリシー](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)を作成します。
1. **[割り当て]** を **[すべてのユーザー]** に設定します。
1. 次に示すように、 **[アクセス制御]** のオプションを構成します。

    ![アクセス権を付与し、他の制限を指定できる [許可] ウィンドウを示すスクリーンショット。](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

**[複数のコントロールの場合]** オプションを **[選択したコントロールのいずれかが必要]** に設定すると、チェック ボックスで指定した条件のいずれかがユーザーによって満たされると、アプリへのアクセスがユーザーに許可されます。

#### <a name="example-3-enforce-mfa-based-on-location"></a>例 3: 場所に基づいて MFA を適用する

Azure AD でユーザーの場所に基づいて MFA 規則を指定します。

1. [新しい条件付きアクセス ポリシー](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)を作成します。
1. **[割り当て]** を **[すべてのユーザー]** に設定します。
1. [Azure AD でネームド ロケーションを構成します](../reports-monitoring/quickstart-configure-named-locations.md)。 それ以外の場合は、企業ネットワーク内部からのフェデレーションが信頼されます。
1. **条件規則** を構成して、MFA を適用する場所を指定します。

    ![条件ルールの [場所] ウィンドウを示すスクリーンショット。](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

1. 次に示すように、 **[アクセス制御]** のオプションを構成します。

    ![アクセス制御ポリシーのマッピング](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

### <a name="map-emit-attributes-as-claims-rule"></a>出力属性を要求規則としてマッピングする

AD FS における要求としての属性の出力規則:

  ![Emit attributes as Claims の [規則の編集] ダイアログ ボックスを示すスクリーンショット。](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-1.png)

この規則を Azure AD にマップするには、次のようにします。

1. [[Azure portal]](https://portal.azure.com/) で、 **[エンタープライズ アプリケーション]** 、 **[シングル サインオン]** の順に選択して、SAML ベースのサインオン構成を表示します。

    ![エンタープライズ アプリケーションのシングル サインオン ページを示すスクリーンショット。](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-2.png)

1. 属性を変更するには、 **[編集]** (強調表示) を選択します。

    ![これは、ユーザー属性と要求を編集するためのページです](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-3.png)

### <a name="map-built-in-access-control-policies"></a>組み込みアクセス制御ポリシーのマッピング

AD FS 2016 の組み込みアクセス制御ポリシー:

  ![Azure AD の組み込みのアクセス制御](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-1.png)

Azure AD で組み込みのポリシーを実装するには、[新しい条件付きアクセス ポリシー](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)を使用してアクセス制御を構成するか、AD FS 2016 のカスタム ポリシー デザイナーを使用してアクセス制御ポリシーを構成します。 ルール エディターには、あらゆる種類の配列を作成するのに役立つ、許可オプションと除外オプションの完全な一覧があります。

  ![Azure AD のアクセス制御ポリシー](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-2.png)

次の表では、いくつかの便利な許可および除外オプションと、それらが Azure AD にどのようにマッピングするかを示します。

| オプション | Azure AD で許可オプションを構成する方法| Azure AD で除外オプションを構成する方法 |
| - | - | - |
| 特定のネットワークから| Azure AD の[ネームド ロケーション](../reports-monitoring/quickstart-configure-named-locations.md)にマッピングします| [信頼できる場所](../conditional-access/location-condition.md)に対しては **[除外]** オプションを使用します |
| 特定のグループから| [ユーザーやグループの割り当てを設定します](assign-user-or-group-access-portal.md)| ユーザーおよびグループで **[除外]** オプションを使用します |
| 特定の信頼レベルのデバイスから| [割り当て] > [条件] の下にある **[デバイスの状態]** コントロールから設定します| デバイスの状態の条件で **[除外]** オプションを使用し、 **[すべてのデバイス]** を含めます |
| 要求の特定の要求で| この設定を移行することはできません| この設定を移行することはできません |

Azure portal で信頼できる場所に対する [除外] オプションを構成する方法の例を次に示します。

  ![アクセス制御ポリシーのマッピングのスクリーンショット](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-3.png)

## <a name="transition-users-from-ad-fs-to-azure-ad"></a>ユーザーを AD FS から Azure AD に移行する

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Azure AD で AD FS グループを同期する

承認規則をマッピングするときは、AD FS で認証を行うアプリで、アクセス許可に Active Directory グループを使用する場合があります。 そのような場合は、[Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) を使用して、アプリケーションを移行する前に、これらのグループを Azure AD と同期させます。 アプリケーションが移行されるときに同じユーザーにアクセス権を付与できるよう、移行前にそれらのグループとメンバーシップを確認します。

詳細については、「[Active Directory から同期されたグループ属性を使用する場合の前提条件](../hybrid/how-to-connect-fed-group-claims.md)」を参照してください。

### <a name="set-up-user-self-provisioning"></a>ユーザーのセルフプロビジョニングを設定する

一部の SaaS アプリケーションでは、ユーザーが初めてアプリケーションにサインインするときに、ユーザーをセルフプロビジョニングする機能がサポートされています。 Azure AD でのアプリ プロビジョニングという用語は、ユーザーがアクセスする必要のあるクラウド ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) アプリケーションに、ユーザーの ID とロールを自動的に作成することを意味します。 移行されるユーザーは、既に SaaS アプリケーションにアカウントを持っています。 移行後に追加された新しいユーザーはいずれもプロビジョニングする必要があります。 アプリケーションを移行した後、[SaaS アプリのプロビジョニング](../app-provisioning/user-provisioning.md)をテストします。

### <a name="sync-external-users-in-azure-ad"></a>Azure AD で外部ユーザーを同期する

既存の外部ユーザーは、AD FS で、こちらの 2 つの方法によって設定できます。

* **組織内にローカル アカウントを持つ外部ユーザー** — これらのアカウントは、内部ユーザー アカウントの動作と同じ方法で引き続き使用できます。 これらの外部ユーザー アカウントは、組織内にプリンシパル名を持っていますが、アカウントのメール アドレスは外部を指している可能性があります。 移行の進行に合わせて、独自の企業 ID が使用できるときにそれを使用するようにこれらのユーザーを移行することによって、[Azure AD B2B](../external-identities/what-is-b2b.md) で提供される利点を利用できます。 これにより、ユーザーが独自の企業ログオンでサインインすることが多い場合に、ユーザーのサインイン プロセスが効率化されます。 外部ユーザーのアカウントを管理する必要がなくなり、組織の管理も容易になります。
* **フェデレーション外部 ID** — 現在、外部組織とフェデレーションを行っている場合は、いくつかの方法を使用できます。
  * [Azure portal で Azure Active Directory B2B コラボレーション ユーザーを追加します](../external-identities/add-users-administrator.md)。 Azure AD 管理ポータルから取引先組織に対し、個々のメンバーが引き続きこれまでと同じアプリと資産を使用するように、B2B コラボレーションの招待を事前に送信できます。
  * B2B 招待 API を使用して取引先組織の個々のユーザーに対する要求を生成する、[セルフサービス B2B サインアップ ワークフローを作成します](../external-identities/self-service-portal.md)。

既存の外部ユーザーの構成方法にかかわらず、グループ メンバーシップまたは特定のアクセス許可のいずれかで、外部ユーザーがアカウントに関連付けられたアクセス許可を持っている可能性があります。 これらのアクセス許可を移行またはクリーンアップする必要があるかどうかを評価します。 外部ユーザーを表す組織内のアカウントは、ユーザーが外部 ID に移行された後で、無効にする必要があります。 リソースへの接続が中断される可能性があるため、移行プロセスについてビジネス パートナーと検討する必要があります。

## <a name="migrate-and-test-your-apps"></a>アプリを移行してテストする

この記事で詳しく説明されている移行プロセスに従います。 その後、[Azure portal](https://aad.portal.azure.com/) に移動して、移行が成功したかどうかをテストします。

以下の手順に従います。

1. **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** を選択し、一覧から対象のアプリを見つけます。
1. **[管理]**  >  **[ユーザーとグループ]** を選択し、アプリに少なくとも 1 人のユーザーまたはグループを割り当てます。
1. **[管理]**  >  **[条件付きアクセス]** を選択します。 ポリシーの一覧を調べて、[条件付きアクセス ポリシー](../conditional-access/overview.md)によってアプリケーションへのアクセスがブロックされないことを確認します。

アプリの構成方法に応じて、SSO が正常に機能することを確認します。

| 認証の種類| テスト |
| :- | :- |
| OAuth/OpenID Connect| **[エンタープライズ アプリケーション] > [アクセス許可]** の順に選択し、アプリのユーザー設定においてアプリケーションに確実に同意しているようにします。|
| SAML ベースの SSO | **[シングル サインオン]** の下にある [[SAML 設定のテスト]](debug-saml-sso-issues.md) ボタンを使用します。 |
| パスワードベースの SSO |  [マイ アプリによるセキュリティで保護されたサインイン](../user-help/my-apps-portal-end-user-access.md)[-](../user-help/my-apps-portal-end-user-access.md)[拡張機能](../user-help/my-apps-portal-end-user-access.md)をダウンロードしてインストールします。 この拡張機能は、SSO プロセスを使用する必要がある組織の任意のクラウド アプリを開始する場合に役立ちます。 |
| アプリケーション プロキシ | コネクタが実行されていて、アプリケーションに割り当てられていることを確認します。 詳細については、[アプリケーション プロキシのトラブルシューティング ガイド](application-proxy-troubleshoot.md)に関する記事をご覧ください。 |

> [!NOTE]
> 以前の AD FS 環境の Cookie は、ユーザーのコンピューター上で保持されます。 このような Cookie により、ユーザーが新しい Azure AD ログインではなく古い AD FS ログイン環境に移動されて、移行で問題が発生する可能性があります。 手動またはスクリプトで、ユーザーのブラウザーの Cookie をクリアすることが必要になる場合があります。 また、System Center Configuration Manager または同様のプラットフォームを使用することもできます。

### <a name="troubleshoot"></a>トラブルシューティング

移行されたアプリケーションのテストでエラーが発生した場合は、既存の AD FS 証明書利用者にフォールバックする前に、トラブルシューティングを行うことができる場合があります。 「[Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法](debug-saml-sso-issues.md)」を参照してください。

### <a name="rollback-migration"></a>移行をロールバックする

移行が失敗した場合、AD FS サーバー上の既存の証明書利用者はそのままにして、証明書利用者へのアクセスを削除することをお勧めします。 これにより、デプロイの間に必要になった場合、迅速にフォールバックを行うことができます。

### <a name="employee-communication"></a>従業員への通知

計画的な停止期間自体は最小限で済む可能性がありますが、それでも AD FS から Azure AD に切り替えるときは、これらの時間枠を従業員に事前に伝達することを計画する必要があります。 アプリのエクスペリエンスに、[フィードバック] ボタンまたは問題が発生したときのヘルプ デスクへのポインターがあることを確認します。

デプロイが完了したら、ユーザーに、デプロイが成功したことを知らせるとともに、実行する必要がある手順について再確認させることができます。

* [マイ アプリ](https://myapps.microsoft.com)を使用して、移行されたすべてのアプリケーションにアクセスするようにユーザーに指示します。
* MFA の設定の更新が必要な場合があることをユーザーに通知します。
* セルフサービス パスワード リセットがデプロイされている場合、ユーザーは自分の認証方法を更新または確認することが必要な場合があります。 [MFA](https://aka.ms/mfatemplates) と [SSPR](https://aka.ms/ssprtemplates) のエンド ユーザー通信テンプレートを参照してください。

### <a name="external-user-communication"></a>外部ユーザーへの通知

このユーザーのグループは、通常、問題が発生した場合に最も重大な影響を受けます。 セキュリティ対策によって外部パートナーに対して異なる一連の条件付きアクセス規則またはリスク プロファイルが指示されている場合は特にそうです。 外部パートナーがクラウド移行スケジュールを認識していること、および外部コラボレーションに固有のすべてのフローをテストするパイロット デプロイに参加することが推奨される期間が外部パートナーに提供されていることを確認します。 最後に、問題が発生した場合にヘルプ デスクにアクセスする方法があることを確認します。

## <a name="next-steps"></a>次のステップ

* [Azure AD へのアプリケーション認証の移行](https://aka.ms/migrateapps/whitepaper)に関するドキュメントを読みます。
* [条件付きアクセス](../conditional-access/overview.md)と [MFA](../authentication/concept-mfa-howitworks.md) を設定します。
* 段階的にコード サンプルを試してみる: [開発者向けの AD FS から Azure AD へのアプリケーション移行プレイブック](https://aka.ms/adfsplaybook)。

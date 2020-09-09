---
title: アプリケーション認証を AD FS から Azure Active Directory に移動する
description: この記事は、アプリケーションを Azure AD に移動する方法の理解に役立つことを目的としています。特に、フェデレーション SaaS アプリケーションに重点を置いています。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/01/2020
ms.author: kenwith
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9d2f295394d89432f3c6dd99585cc4363d4ff74
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88641366"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>アプリケーション認証を Active Directory フェデレーション サービス (AD FS) から Azure Active Directory に移動する

[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) によって提供されるユニバーサル ID プラットフォームでは、ユーザー、パートナー、顧客に対し、任意のプラットフォームやデバイスからアプリケーションにアクセスして共同作業を行うための 1 つの ID が提供されます。 Azure AD には、[ID 管理機能の完全なスイート](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)が備わっています。 アプリケーション (アプリ) の認証と承認を Azure AD に標準化することで、これらの機能によって提供される利点を利用できます。 

> [!NOTE]
> この記事では、オンプレミスの Active Directory と Active Directory フェデレーション サービスから Azure AD にアプリケーション認証を移動する方法に焦点を当てます。 この移動の計画の概要については、ホワイトペーパー「[Azure AD へのアプリケーションの認証の移行](https://aka.ms/migrateapps/whitepaper)」を参照してください。 このホワイトペーパーでは、移行、テスト、分析情報を計画する方法について説明されています。

## <a name="introduction"></a>はじめに

ユーザー アカウントが含まれるオンプレミスのディレクトリがある場合は、ユーザーが認証を行うアプリケーションが多数存在する可能性があります。 これらの各アプリは、ユーザーが各自の ID を使用してアクセスできるように構成されています。 


ユーザーは、オンプレミスの Active Directory で直接認証を行うこともあります。 Active Directory フェデレーション サービス (AD FS) は、標準に基づくオンプレミスの ID サービスです。 AD FS では、信頼されたビジネス パートナー間でシングル サインオン (SSO) 機能を使用する機能が拡張され、ユーザーが各アプリケーションに個別にサインインする必要がなくなります。 これはフェデレーションと呼ばれます。

多くの組織には、Office 365 や Azure AD ベースのアプリの他に、AD FS に直接フェデレーションされたサービスとしてのソフトウェア (SaaS) アプリやカスタム基幹業務 (LOB) アプリがあります。 

![オンプレミスに直接接続されているアプリケーション](media/migrate-adfs-apps-to-azure/app-integration-before-migration1.png)

**アプリケーションのセキュリティを強化するときの目標は、オンプレミスとクラウドの環境全体で 1 セットのアクセス制御とポリシーを使用することです**。 

![Azure AD 経由で接続されたアプリケーション](media/migrate-adfs-apps-to-azure/app-integration-after-migration1.png)



## <a name="types-of-apps-to-migrate"></a>移行するアプリの種類

すべてのアプリケーション認証を Azure AD に移行するのが最善です。そのようにすると、ID とアクセスを管理のための単一のコントロール プレーンが提供されます。

アプリケーションでは、認証に最新のプロトコルまたはレガシ プロトコルが使用されている場合があります。 最初に最新の認証プロトコル (SAML や Open ID Connect など) を使用するアプリケーションを移行することを検討します。 これらのアプリは、アプリ ギャラリーの組み込みコネクタを使用するか、Azure AD にアプリケーションを登録することによって、Azure AD で認証を行うように再構成できます。 古いプロトコルを使用しているアプリは、[アプリケーション プロキシ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy)を使用して統合できます。 

詳細については、「[Azure AD と統合できるアプリケーションの種類](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-management)」を参照してください。

[Azure AD Connect Health を有効にしてある](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)場合は、[AD FS アプリケーション アクティビティ レポートを使用してアプリケーションを Azure AD に移行する](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity)ことができます。 

### <a name="the-migration-process"></a>移行プロセス

Azure AD にアプリ認証を移動するプロセスの間に、アプリと構成を適切にテストします。 運用環境への移動の移行テストにも、既存のテスト環境を引き続き使用することをお勧めします。 テスト環境を現在使用できない場合は、アプリケーションのアーキテクチャに応じて、[Azure App Service](https://azure.microsoft.com/services/app-service/) または [Azure Virtual Machines](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB) を使用して設定できます。

アプリの構成を開発するときに使用するテスト Azure AD テナントを別に設定することもできます。 

移行プロセスは次のようになります。

**ステージ 1 – 現在の状態: AD FS で認証を行っている運用アプリ**

![移行ステージ 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)

 
**ステージ 2 – オプション: テスト用の Azure テナントを指し示すアプリのテスト インスタンス**

アプリのテスト インスタンスがテスト用の Azure AD テナントを指し示すように構成を更新し、必要な変更を行います。 テスト用 Azure AD テナントのユーザーを使用してアプリをテストできます。 開発プロセスの間に、[Fiddler](https://www.telerik.com/fiddler) などのツールを使用して、要求と応答を比較および検証できます。

テスト用のテナントを別に設定できない場合は、このステージをスキップし、アプリのテスト インスタンスを稼働させ、次のステージ 3 で説明するように、運用 Azure AD テナントを指し示します。

![移行ステージ 2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

**ステージ3 – 運用 Azure テナントを指し示すテスト アプリ**

アプリのテスト インスタンスで Azure の運用インスタンスを指し示すように構成を更新します。 これで、運用インスタンスのユーザーを使用してテストできるようになります。 必要に応じて、ユーザーの移行に関するこの記事のセクションを確認してください。

![移行ステージ 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

**ステージ 4 – 運用 AD テナントを指し示す運用アプリ**

運用アプリケーションの構成を更新して、運用 Azure テナントを指し示すようにします。

![移行ステージ 1 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 AD FS で認証を行うアプリでは、アクセス許可に Active Directory グループを使用する場合があります。 移行を始める前に、オンプレミス環境と Azure AD の間で ID データを同期するには、[Azure AD Connect 同期](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)を使用します。 アプリケーションが移行されるときに同じユーザーにアクセス権を付与できるよう、移行前にそれらのグループとメンバーシップを確認します。

### <a name="line-of-business-lob-apps"></a>基幹業務 (LOB) アプリ

LOB アプリは、組織内で開発されるか、データ センターにインストールされる標準パッケージ製品として利用できます。 たとえば、Windows Identity Foundation と (SharePoint Online ではなく) SharePoint アプリを基に構築されたアプリなどです。 

OAuth 2.0、OpenID Connect、または WS-Federation を使用する LOB アプリは、[アプリの登録](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users)として Azure AD と統合できます。 [Azure portal](https://portal.azure.com/) のエンタープライズ アプリケーション ページで、SAML 2.0 または WS-Federation を使用するカスタム アプリを[ギャラリー以外のアプリケーション](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app)として統合します。

## <a name="saml-based-single-sign-on"></a>SAML ベースのシングル サインオン

認証に SAML 2.0 を使用するアプリは、[SAML ベースのシングル サインオン](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (SAML ベースの SSO) 用に構成できます。 [SAML ベースの SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) では、SAML 要求で定義する規則に基づいて、ユーザーを特定のアプリケーション ロールにマッピングできます。 

SAML ベースのシングル サインオンのために SaaS アプリケーションを構成するには、[SAML ベースのシングル サインオンを構成する](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications)ことに関するページを参照してください。 

![SSO SAML ユーザーのスクリーンショット ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

 
多くの SaaS アプリケーションには[アプリケーション固有のチュートリアル](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)が用意されており、SAML ベースのシングル サインオンの構成が順を追って説明されています。

![アプリのチュートリアル](media/migrate-adfs-apps-to-azure/app-tutorial.png)

一部のアプリは簡単に移行することができます。 カスタム要求などのより複雑な要件を持つアプリでは、Azure AD や Azure AD Connect での追加の構成が必要になる場合があります。 サポートされている要求マッピングの詳細については、「[Azure Active Directory における要求のマッピング](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)」をご覧ください。

属性をマッピングするときは、次の制限事項に注意してください。

* AD FS で発行できるすべての属性が、SAML トークンに出力される属性として Azure AD に表示されるわけではありません (属性が同期される場合でも)。 属性を編集するとき、[値] ボックスの一覧に、Azure AD で使用できるさまざまな属性が表示されます。 [Azure AD Connect 同期](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)の構成を調べて、必要な属性 (samAccountName など) が Azure AD に同期されていることを確認します。 拡張属性を使用して、Azure AD の標準ユーザー スキーマの一部ではないすべての要求を出力できます。

* 最も一般的なシナリオでアプリに必要なのは、NameID 要求とその他の一般的なユーザー識別子要求だけです。 他にも要求が必要かどうかを判断するには、AD FS からどの要求が発行されているかを調べます。

* 一部の要求は Azure AD で保護されているため、すべての要求を発行できるとは限りません。 

* 暗号化された SAML トークンを使用する機能は現在プレビュー段階です。 「[方法: エンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)」をご覧ください。

 

### <a name="software-as-a-service-saas-apps"></a>サービスとしてのソフトウェア (SaaS) アプリ

ユーザーが Salesforce、ServiceNow、Workday などの SaaS アプリにサインインし、AD FS に統合されている場合は、SaaS アプリに対してフェデレーション サインオンが使用されています。 

既に、ほとんどの SaaS アプリケーションを、Azure AD で構成できるようになっています。 [Azure AD アプリ ギャラリー](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)には SaaS アプリに対する事前構成済みの接続が多数用意されており、移行が簡単になります。 SAML 2.0 アプリケーションは、Azure AD アプリ ギャラリーを通じて、または[非ギャラリー アプリケーション](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app)として、Azure AD と統合することができます。 

OAuth 2.0 または OpenID Connect を使用するアプリは、"[アプリの登録](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users)" として同じように Azure AD と統合できます。 レガシ プロトコルを使用するアプリでは、[Azure AD アプリケーション プロキシ](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)を使用して、Azure AD で認証を行うことができます。

SaaS アプリのオンボードに関する問題については、[SaaS アプリケーション統合サポートのエイリアス](mailto:SaaSApplicationIntegrations@service.microsoft.com)にお問い合わせください。

**SSO 用の SAML 署名証明書**: 署名証明書は、すべての SSO デプロイの重要な部分です。 Azure AD では、署名証明書を作成して、SaaS アプリケーションに対する SAML ベースのフェデレーション SSO が確立されます。 ギャラリー アプリケーションまたは非ギャラリー アプリケーションを追加した後、フェデレーション SSO オプションを使用して追加したアプリケーションを構成します。 「[Azure Active Directory でのフェデレーション シングル サインオンの証明書の管理](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on)」をご覧ください。

### <a name="apps-and-configurations-that-can-be-moved-today"></a>今すぐ移動できるアプリと構成

現時点で簡単に移動できるのは、構成要素と要求の標準セットを使用する SAML 2.0 アプリなどです。

* ユーザー プリンシパル名

* 電子メール アドレス

* 指定された名前

* Surname

* Azure AD メール属性、メール プレフィックス、従業員 ID、拡張属性 1 から 15、オンプレミスの **SamAccountName** 属性など、SAML **NameID** のような代替属性。 詳細については、「[NameIdentifier 要求の編集](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)」を参照してください。

* カスタム要求。

以下のものについては、Azure AD に移行するには、追加の構成手順が必要になります。

* AD FS のカスタム承認または Multi-Factor Authentication (MFA) 規則。 これらは、[Azure AD 条件付きアクセス](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)機能を使用して構成します。

* 複数の応答 URL エンドポイントを持つアプリ。 これらは、PowerShell を使用して Azure AD で、または Azure portal インターフェイスで構成します。

* SAML バージョン 1.1 のトークンを必要とする SharePoint アプリなどの WS-Federation アプリ。 これらは、PowerShell を使用して手動で構成できます。 また、ギャラリーからの SharePoint アプリケーションと SAML 1.1 アプリケーションに対しては、事前に統合された汎用テンプレートを追加することもできます。 SAML 2.0 プロトコルがサポートされています。

* 複雑な要求発行変換規則。 サポートされる要求マッピングの詳細については、以下を参照してください。
   *  [Azure Active Directory での要求マッピング](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping) 
   * [Azure Active Directory のエンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

 

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Azure AD で現時点ではサポートされていないアプリと構成

以下の機能を必要とするアプリは、現時点では移行することができません。

**プロトコル機能**

* WS-Trust ActAs パターンのサポート

* SAML アーティファクト解決

* 署名済み SAML 要求の署名の検証  
‎署名済み要求は受け入れられますが、署名は検証されないことに注意してください。  
‎Azure AD では、アプリケーションで事前に構成されたエンドポイントにのみトークンが返されるため、ほとんどの場合、署名の検証は必要ありません。

**トークン内の要求の機能**

* データが Azure AD に同期されていない場合、Azure AD ディレクトリ以外の属性ストアからの要求。 詳細については、「[Azure AD 同期 API の概要](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)」をご覧ください。

* ディレクトリの複数値属性の発行。 たとえば、現時点では、プロキシ アドレスに対して複数値の要求を発行することはできません。

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>AD FS から Azure AD へのアプリの設定のマッピング

移行を開始するには、まず、アプリケーションがオンプレミスでどのように構成されているかを評価し、その構成を Azure AD にマッピングします。 AD FS と Azure AD は動作が似ているため、信頼関係の構成、サインインとサインアウトの URL、および識別子の概念は、両方に当てはまります。 Azure AD で簡単に構成できるように、アプリケーションの AD FS の構成設定を文書化します。

### <a name="map-app-configuration-settings"></a>アプリの構成設定のマッピング

次の表では、AD FS 証明書利用者の信頼と Azure AD エンタープライズ アプリケーションの間での、設定の最も一般的なマッピングについて説明します。

* AD FS – アプリに対する AD FS 証明書利用者の信頼で設定を探します。 証明書利用者を右クリックし、[プロパティ] を選択します。 

* Azure AD – 設定は、アプリケーションのシングル サインオンのプロパティごとに、[Azure portal](https://portal.azure.com/) 内で構成されます。

| 構成設定| AD FS| Azure AD での構成方法| SAML トークン |
| - | - | - | - |
| **アプリのサインオン URL** <p>サービス プロバイダー (SP) によって開始された SAML フローでユーザーがアプリにサインインするための URL。| 該当なし| SAML ベースのサインオンから [基本的な SAML 構成] を開きます| 該当なし |
| **アプリの応答 URL** <p>ID プロバイダー (IdP) から見たアプリの URL。 IdP は、ユーザーが IdP にサインインした後、ここでユーザーとトークンを送信します。  ‎これは、**SAML アサーション コンシューマー エンドポイント**とも呼ばれます。| **[エンドポイント]** タブを選択します| SAML ベースのサインオンから [基本的な SAML 構成] を開きます| SAML トークンの Destination 要素。 値の例: `https://contoso.my.salesforce.com` |
| **アプリのサインアウト URL** <p>これは、ユーザーがアプリからサインアウトしたときに "サインアウト クリーンアップ" 要求が送信される URL です。 IdP は、他のすべてのアプリからユーザーをサインアウトさせる要求も送信します。| **[エンドポイント]** タブを選択します| SAML ベースのサインオンから [基本的な SAML 構成] を開きます| 該当なし |
| **アプリ識別子** <p>これは、IdP から見たアプリの識別子です。 多くの場合、サインイン URL 値が識別子に使用されます (そうでない場合もあります)。  ‎アプリではこれを "エンティティ ID" と呼ぶこともあります。| **[識別子]** タブを選択します|SAML ベースのサインオンから [基本的な SAML 構成] を開きます| SAML トークンの **Audience** 要素にマッピングします。 |
| **アプリのフェデレーション メタデータ** <p>これは、アプリのフェデレーション メタデータの場所です。 エンドポイントや暗号化証明書などの特定の構成設定を自動更新するために、IdP によって使用されます。| **[監視]** タブをクリックします| 該当なし。 Azure AD では、アプリケーション フェデレーション メタデータの使用は直接はサポートされていません。 フェデレーション メタデータは手動でインポートできます。| 該当なし |
| **ユーザー識別子/名前 ID** <p>Azure AD または AD FS のユーザー ID をアプリに一意に示すために使用される属性。  ‎この属性は、通常、ユーザーの UPN またはメール アドレスです。| 要求規則。 ほとんどの場合、要求規則では末尾が NameIdentifier の型の要求が発行されます。| この識別子は、ヘッダー **[ユーザー属性と要求]** にあります。 既定では、UPN が使用されます| SAML トークンの **NameID** 要素にマッピングします。 |
| **その他の要求** <p>IdP からアプリによく送信される他の要求情報の例としては、名、姓、メール アドレス、グループ メンバーシップなどがあります。| AD FS では、証明書利用者のその他の要求規則となっています。| この識別子は、ヘッダー **[ユーザー属性と要求]** にあります。 **[表示]** を選択し、他のすべてのユーザー属性を編集します。| 該当なし |


### <a name="map-identity-provider-idp-settings"></a>ID プロバイダー (IdP) の設定のマッピング

SSO 対応の Azure AD に対して AD FS を指し示すようにアプリケーションを構成します。 ここでは、SAML プロトコルを使用する SaaS アプリに焦点を当てます。 ただし、この概念はカスタム LOB アプリにも拡張されます。

> [!NOTE]
> Azure AD の構成値は、{tenant-id} を Azure テナント ID に置き換え、{application-id} をアプリケーション ID に置き換えるパターンに従います。 この情報は、[Azure portal](https://portal.azure.com/) の [Azure Active Directory] > [プロパティ] にあります。 

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
| **IdP のフェデレーション メタデータ**<p>IdP の一般公開されているフェデレーション メタデータの場所。 (一部のアプリでは、管理者によって個別に構成される URL、識別子、およびトークン署名証明書の代わりに、フェデレーション メタデータを使用します)。| AD FS フェデレーション メタデータ URL は、[AD FS の管理] にあり、これは **[サービス] > [エンドポイント] > [メタデータ] > [種類: フェデレーション メタデータ]** の下で確認できます。 例: `https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`| Azure AD での対応する値は、[https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml) というパターンに従います。 {TenantDomainName} は、"contoso.onmicrosoft.com" という形式のテナントの名前に置き換えます。   <p>詳細については、「[フェデレーション メタデータ](https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-federation-metadata)」を参照してください。 |


## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>AD FS セキュリティ ポリシーを Azure AD で表す

アプリの認証を Azure AD に移動する場合は、既存のセキュリティ ポリシーから、Azure AD で使用できる同等または代替のバリアントへのマッピングを作成します。 アプリの所有者によって要求されるセキュリティ標準に適合しながら、これらのマッピングを確実に実行できるようにすることで、残りのアプリの移行が大幅に容易になります。

各規則の種類とその例について、ここでは、AD FS での規則の表現方法、AD FS の規則言語と同等のコード、および Azure AD でのこのマッピング方法を示します。

### <a name="map-authorization-rules"></a>承認規則のマッピング

AD FS の承認規則の種類の例と、それらを Azure AD にマッピングする方法を次に示します。

#### <a name="example-1-permit-access-to-all-users"></a>例 1:すべてのユーザーにアクセスを許可する

すべてのユーザーにアクセスを許可すると、AD FS では次のようになります。 

![移行ステージ 1 ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


これは、次のいずれかの方法で Azure AD にマッピングされます。

[Azure ポータル](https://portal.azure.com/)で次の操作を行います。
* オプション 1: [ユーザーの割り当てが必要] を [いいえ] に設定します ![SaaS アプリのアクセス制御ポリシーを編集する ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    [ユーザーの割り当てが必要] スイッチを [はい] に設定すると、アクセスできるようにするにはユーザーをアプリケーションに割り当てる必要があることに注意してください。 [いいえ] に設定すると、すべてのユーザーがアクセスできます。 このスイッチでは、[マイ アプリ] エクスペリエンスでユーザーに対して表示されるものは制御されません。 

 
* オプション 2:[ユーザーとグループ] タブで、アプリケーションを [すべてのユーザー] 自動グループに割り当てます。 <p>
既定の "すべてのユーザー" グループを使用できるようにするには、Azure AD テナントで[動的グループを有効にする](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)必要があります。

   ![Azure AD でのマイ SaaS アプリ ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)


#### <a name="example-2-allow-a-group-explicitly"></a>例 2:グループを明示的に許可する

AD FS での明示的なグループの承認:


![発行承認規則 ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)


規則が Azure AD にマッピングされる方法を次に示します。

[Azure portal](https://portal.azure.com/) で、最初に AD FS のユーザーのグループに対応する[ユーザー グループを作成](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)してから、アプリのアクセス許可をそのグループに割り当てます。

![割り当てを追加する ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)


#### <a name="example-3-authorize-a-specific-user"></a>例 3: 特定のユーザーを承認する

AD FS での明示的なユーザーの承認:

![発行承認規則 ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

規則が Azure AD にマッピングされる方法を次に示します。

[Azure portal](https://portal.azure.com/) で、次に示すように、アプリの [割り当ての追加] タブを使用してアプリにユーザーを追加します。

![Azure でのマイ SaaS アプリ ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

 
### <a name="map-multi-factor-authentication-rules"></a>Multi-Factor Authentication の規則のマッピング 

AD FS とフェデレーションしているため、[Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication) と AD FS のオンプレミスでの展開は、移行後も引き続き機能します。 ただし、Azure AD の条件付きアクセス ワークフローに関連付けられている Azure の組み込みの MFA 機能に移行することを検討してください。 

AD FS の MFA 規則の種類の例と、さまざまな条件に基づいてそれらを Azure AD にマッピングする方法を次に示します。

AD FS での MFA 規則の設定:

![Azure AD MFA の設定](media/migrate-adfs-apps-to-azure/mfa-location-1.png)


#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>例 1:ユーザーまたはグループに基づいて MFA を適用する

ユーザーやグループのセレクターは、グループごと (グループ SID) またはユーザーごと (プライマリ SID) に MFA を適用できる規則です。 ユーザーやグループの割り当てとは別に、AD FS MFA 構成 UI のすべての追加のチェック ボックスは、ユーザーやグループの規則が適用された後に評価される追加の規則として機能します。 


Azure AD でユーザーまたはグループに対して MFA 規則を指定します。

1. [新しい条件付きアクセス ポリシー](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)を作成します。

2. **[割り当て]** を選択します。 MFA を適用するユーザーまたはグループを追加します。

3. 次に示すように、 **[アクセス制御]** のオプションを構成します。  
‎

![AAD MFA の設定](media/migrate-adfs-apps-to-azure/mfa-usersorgroups.png)

 
 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>例 2:未登録のデバイスに MFA を適用する

Azure AD で未登録のデバイスに対する MFA 規則を指定します。

1. [新しい条件付きアクセス ポリシー](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)を作成します。

2. **[割り当て]** を **[すべてのユーザー]** に設定します。

3. 次に示すように、 **[アクセス制御]** のオプションを構成します。  
‎

![AAD MFA の設定](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

 
[複数のコントロールの場合] オプションを [選択したコントロールのいずれかが必要] に設定すると、チェック ボックスで指定した条件のいずれかがユーザーによって満たされると、アプリへのアクセスが許可されることを意味します。

#### <a name="example-3-enforce-mfa-based-on-location"></a>例 3: 場所に基づいて MFA を適用する

Azure AD でユーザーの場所に基づいて MFA 規則を指定します。

1. [新しい条件付きアクセス ポリシー](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)を作成します。

1. **[割り当て]** を **[すべてのユーザー]** に設定します。

1. [Azure AD で名前付きの場所を構成します](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations)。それ以外の場合は、企業ネットワーク内部からのフェデレーションが信頼されます。 

1. **条件規則**を構成して、MFA を適用する場所を指定します。

![Azure AD MFA の設定](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

5. 次に示すように、 **[アクセス制御]** のオプションを構成します。


![アクセス制御ポリシーのマッピング](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

 
### <a name="map-emit-attributes-as-claims-rule"></a>出力属性を要求規則としてマッピングする

ここでは、AD FS での属性のマッピング方法の例を示します。

![Azure AD MFA の設定](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-1.png)


規則が Azure AD にマッピングされる方法を次に示します。

次に示すように、[Azure portal](https://portal.azure.com/) で **[エンタープライズ アプリケーション]** > **[シングル サインオン]** の順に選択し、 **[SAML トークン属性]** を追加します。

![Azure AD MFA の設定](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-2.png)



### <a name="map-built-in-access-control-policies"></a>組み込みアクセス制御ポリシーのマッピング

AD FS 2016 では、複数の組み込みアクセス制御ポリシーから選択できます。

![Azure AD の組み込みのアクセス制御](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-1.png)

 
Azure AD で組み込みのポリシーを実装するには、[新しい条件付きアクセス ポリシー](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)を使用してアクセス制御を構成するか、AD FS 2016 のカスタム ポリシー デザイナーを使用してアクセス制御ポリシーを構成します。 ルール エディターには、あらゆる種類の配列を作成するのに役立つ、許可オプションと除外オプションの完全な一覧があります。

![Azure AD のアクセス制御ポリシー](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-2.png)



次の表では、いくつかの便利な許可および除外オプションと、それらが Azure AD にどのようにマッピングするかを示します。 


| オプション | Azure AD で許可オプションを構成する方法| Azure AD で除外オプションを構成する方法 |
| - | - | - |
| 特定のネットワークから| Azure AD の[ネームド ロケーション](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)にマッピングします| [信頼できる場所](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)に対しては **[除外]** オプションを使用します |
| 特定のグループから| [ユーザーやグループの割り当てを設定します](https://docs.microsoft.com/azure/active-directory/manage-apps/assign-user-or-group-access-portal)| ユーザーおよびグループで **[除外]** オプションを使用します |
| 特定の信頼レベルのデバイスから| これは、[割り当て] > [条件] の下にある [デバイスの状態] コントロールから設定します| デバイスの状態の条件で **[除外]** オプションを使用し、 **[すべてのデバイス]** を含めます |
| 要求の特定の要求で| この設定を移行することはできません| この設定を移行することはできません |


Azure portal で信頼できる場所に対する [除外] オプションを構成する方法の例:

![アクセス制御ポリシーのマッピングのスクリーンショット](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-3.png)



## <a name="transition-users-from-ad-fs-to-azure-ad"></a>ユーザーを AD FS から Azure AD に移行する

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Azure AD で AD FS グループを同期する

承認規則をマッピングするときは、AD FS で認証を行うアプリで、アクセス許可に Active Directory グループを使用する場合があります。 そのような場合は、[Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) を使用して、アプリケーションを移行する前に、これらのグループを Azure AD と同期させます。 アプリケーションが移行されるときに同じユーザーにアクセス権を付与できるよう、移行前にそれらのグループとメンバーシップを確認します。

詳細については、「[Active Directory から同期されたグループ属性を使用する場合の前提条件](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims)」を参照してください。

### <a name="setup-user-self-provisioning"></a>ユーザーのセルフプロビジョニングを設定する 

一部の SaaS アプリケーションでは、ユーザーが初めてアプリケーションにサインインするときに、ユーザーをセルフプロビジョニングする機能がサポートされています。 Azure Active Directory (Azure AD) でのアプリ プロビジョニングという用語は、ユーザーがアクセスする必要のあるクラウド ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) アプリケーションにおいてユーザーの ID とロールを自動的に作成することを意味します。 移行されるユーザーは、既に SaaS アプリケーションにアカウントを持っています。 移行後に追加される新しいユーザーは、すべてプロビジョニングする必要があります。 アプリケーションを移行した後、[SaaS アプリのプロビジョニング](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)をテストします。

### <a name="sync-external-users-in-azure-ad"></a>Azure AD で外部ユーザーを同期する

AD FS では、次の 2 つの主な方法で既存の外部ユーザーを設定できます。

#### <a name="external-users-with-a-local-account-within-your-organization"></a>組織内にローカル アカウントを持つ外部ユーザー

これらのアカウントは、内部ユーザー アカウントの動作と同じ方法で引き続き使用できます。 これらの外部ユーザー アカウントは、組織内にプリンシパル名を持っていますが、アカウントのメール アドレスは外部を指している可能性があります。 移行の進行に合わせて、独自の企業 ID が使用できるときにそれを使用するようにこれらのユーザーを移行することによって、[Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) で提供される利点を利用できます。 これにより、ユーザーが独自の企業ログオンでサインインすることが多い場合に、ユーザーのサインイン プロセスが効率化されます。 外部ユーザーのアカウントを管理する必要がなくなり、組織の管理も軽減されます。

#### <a name="federated-external-identities"></a>フェデレーション外部 ID

現在、外部組織とフェデレーションを行っている場合は、いくつかの方法を使用できます。

* [Azure portal で Azure Active Directory B2B コラボレーション ユーザーを追加します](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)。 Azure AD 管理ポータルから取引先組織に対し、個々のメンバーが引き続きこれまでと同じアプリと資産を使用するように、B2B コラボレーションの招待を事前に送信できます。 

* B2B 招待 API を使用して取引先組織の個々のユーザーに対する要求を生成する、[セルフサービス B2B サインアップ ワークフローを作成します](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal)。

既存の外部ユーザーの構成方法にかかわらず、グループ メンバーシップまたは特定のアクセス許可のいずれかで、外部ユーザーがアカウントに関連付けられたアクセス許可を持っている可能性があります。 これらのアクセス許可を移行またはクリーンアップする必要があるかどうかを評価します。 外部ユーザーを表す組織内のアカウントは、ユーザーが外部 ID に移行された後で、無効にする必要があります。 リソースへの接続が中断される可能性があるため、移行プロセスについてビジネス パートナーと検討する必要があります。

## <a name="migrate-and-test-your-apps"></a>アプリを移行してテストする

この記事で詳しく説明されている移行プロセスに従います。

その後、[Azure portal](https://aad.portal.azure.com/) に移動して、移行が成功したかどうかをテストします。 次の手順に従ってください。
1. **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** を選択し、一覧から対象のアプリを見つけます。

1. **[管理]**  >  **[ユーザーとグループ]** を選択し、アプリに少なくとも 1 人のユーザーまたはグループを割り当てます。

1. **[管理]**  >  **[条件付きアクセス]** を選択します。 ポリシーの一覧を調べて、[条件付きアクセス ポリシー](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)によってアプリケーションへのアクセスがブロックされないことを確認します。

アプリの構成方法に応じて、SSO が正常に機能することを確認します。 

| 認証の種類| テスト |
| - | - |
| OAuth/OpenID Connect| **[エンタープライズ アプリケーション] > [アクセス許可]** を選択し、アプリのユーザー設定において組織内で使用されるアプリケーションに同意ていることを確認します。  
‎ |
| SAML ベースの SSO| **[シングル サインオン]** の下にある [[SAML 設定のテスト]](https://docs.microsoft.com/azure/active-directory/develop/howto-v1-debug-saml-sso-issues) ボタンを使用します。  
‎ |
| パスワードベースの SSO| [マイ アプリによるセキュリティで保護されたサインイン](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)[-](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)[拡張機能](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)をダウンロードしてインストールします。 この拡張機能は、SSO プロセスを使用する必要がある組織の任意のクラウド アプリを開始する場合に役立ちます。  
‎ |
| アプリケーション プロキシ| コネクタが実行されていて、アプリケーションに割り当てられていることを確認します。 詳細については、[アプリケーション プロキシのトラブルシューティング ガイド](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot)に関する記事をご覧ください。  
‎ |

> [!NOTE]
> 以前の AD FS 環境の Cookie は、ユーザーのコンピューター上で引き続き保持されています。 このような Cookie により、ユーザーが新しい Azure AD ログインではなく古い AD FS ログイン環境に移動されて、移行で問題が発生する可能性があります。 手動またはスクリプトで、ユーザーのブラウザーの Cookie をクリアすることが必要になる場合があります。 また、System Center Configuration Manager または同様のプラットフォームを使用することもできます。

### <a name="troubleshoot"></a>トラブルシューティング

移行されたアプリケーションのテストでエラーが発生した場合は、既存の AD FS 証明書利用者にフォールバックする前に、トラブルシューティングを行う場合があります。 「[Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-v1-debug-saml-sso-issues)」を参照してください。

### <a name="rollback-migration"></a>移行をロールバックする

移行が失敗した場合、AD FS サーバー上の既存の証明書利用者はそのままにして、証明書利用者へのアクセスを削除することをお勧めします。 これにより、デプロイの間に必要になった場合、迅速にフォールバックを行うことができます。

### <a name="end-user-communication"></a>エンド ユーザーへの伝達

計画的な停止期間自体は最小限で済む可能性がありますが、それでも AD FS から Azure AD に切り替えるときは、これらの時間枠を従業員に事前に伝達することを計画する必要があります。 アプリのエクスペリエンスに、[フィードバック] ボタンまたは問題が発生したときのヘルプ デスクへのポインターがあることを確認します。

デプロイが完了したら、デプロイが成功したことと、実行する必要がある新しい手順の注意を、ユーザーに通知できます。

* [マイ アプリ](https://myapps.microsoft.com)を使用して、移行されたすべてのアプリケーションにアクセスするようにユーザーに指示します。 

* MFA の設定の更新が必要な場合があることをユーザーに通知します。 

* セルフサービス パスワード リセットがデプロイされている場合、ユーザーは自分の認証方法を更新または確認することが必要な場合があります。 [MFA](https://aka.ms/mfatemplates) と [SSPR](https://aka.ms/ssprtemplates) のエンド ユーザー通信テンプレートを参照してください。

外部ユーザーへの通知: このユーザーのグループは、通常、問題が発生した場合に最も重大な影響を受けます。 セキュリティ対策によって外部パートナーに対して異なる一連の条件付きアクセス規則またはリスク プロファイルが指示されている場合は特にそうです。 外部パートナーがクラウド移行スケジュールを認識していること、および外部コラボレーションに固有のすべてのフローをテストするパイロット デプロイに参加することが推奨される期間が外部パートナーに提供されていることを確認します。 最後に、問題が発生した場合に、ヘルプ デスクにアクセスする方法があることを確認します。

## <a name="next-steps"></a>次の手順
[Azure AD へのアプリケーション認証の移行](https://aka.ms/migrateapps/whitepaper)に関するドキュメントを読みます<p>
[条件付きアクセス](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)と [MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) を設定します

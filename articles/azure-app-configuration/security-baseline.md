---
title: Azure App Configuration の Azure セキュリティ ベースライン
description: Azure App Configuration セキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8b68e22e69bf2d4a1655883b249f883f98f565a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101091245"
---
# <a name="azure-security-baseline-for-azure-app-configuration"></a>Azure App Configuration の Azure セキュリティ ベースライン

このセキュリティ ベースラインにより、[Azure セキュリティ ベンチマーク バージョン 2.0](../security/benchmarks/overview.md) のガイダンスが Azure App Configuration に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 内容は、**セキュリティ制御** によってグループ化されています。これは、Azure セキュリティ ベンチマークと、Azure App Configuration に適用できる関連ガイダンスによって定義されています。 Azure App Configuration に適用できない **制御** は、除外されています。

Azure App Configuration を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Azure App Configuration セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)に関するページを参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-controls-v2-network-security.md)」を参照してください。*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: 内部トラフィック用のセキュリティを実装する

**ガイダンス**:Azure App Configuration によって、仮想ネットワークにリソースが直接デプロイされることはありません。 サービスが仮想ネットワークにデプロイされないため、特定のネットワーク機能 (ネットワーク セキュリティ グループ、ルート テーブル、Azure Firewall などの他のネットワーク アプライアンスなど) を活用してサービスの内部トラフィックを保護することはできません。 ただし、App Configuration では、プライベート エンドポイントを使用して、仮想ネットワークから Azure App Configuration に安全に接続できます。

Azure Sentinel を使用して、不安がある従来のプロトコルである SSL/TLSv1、SMBv1、LM/NTLMv1、wDigest、署名なしの LDAP バインド、Kerberos の弱い暗号の使用を検出します。

- [Azure App Configuration でのプライベート エンドポイントの使用](concept-private-endpoint.md)

- [Azure Sentinel の安全でないプロトコルのブック](../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="ns-2-connect-private-networks-together"></a>NS-2: プライベート ネットワークをまとめて接続する

**ガイダンス**:Azure App Configuration は、プライベート エンドポイントを使用した、プライベート リンク経由の安全なデータ送信をサポートしています。 Azure ExpressRoute または Azure 仮想プライベート ネットワーク (VPN) を使用して、コロケーション環境内で Azure のデータセンターとオンプレミスのインフラストラクチャ間のプライベート接続を作成できます。 ExpressRoute 接続はパブリック インターネットを経由しないので、一般的なインターネット接続と比べて信頼性が高く、より高速で、待ち時間も短くなります。 ポイント対サイト VPN とサイト間 VPN では、これらの VPN オプションと Azure ExpressRoute の任意の組み合わせを使用して、オンプレミスのデバイスやネットワークを仮想ネットワークに接続できます。

Azure で 2 つ以上の仮想ネットワークを接続するには、仮想ネットワーク ピアリングを使用します。 ピアリングされた仮想ネットワーク間のネットワーク トラフィックはプライベートであり、Azure のバックボーン ネットワーク上に保持されます。

- [ExpressRoute 接続モデルとは](../expressroute/expressroute-connectivity-models.md)

- [Azure VPN の概要](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Azure サービスへのプライベート ネットワーク アクセスを確立する

**ガイダンス**:Azure Private Link を使用して、インターネットを経由せずに、仮想ネットワークから Azure App Configuration へのプライベート アクセスを有効にします。

プライベート アクセスは、Azure サービスによって提供される認証およびトラフィックのセキュリティに加えて、追加の多層防御手段となります。

- [Azure Private Link について](../private-link/private-link-overview.md)

- [Azure App Configuration でのプライベート リンクの設定方法](concept-private-endpoint.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: 外部ネットワーク攻撃からアプリケーションやサービスを保護する

**ガイダンス**:仮想ネットワーク経由で構成値にアクセスする場合、分散型サービス拒否 (DDoS) 攻撃、アプリケーション固有の攻撃、未承諾で悪意の可能性があるインターネット トラフィックなど、外部ネットワークからの攻撃に対してリソースを保護します。 Azure Firewall を使用して、インターネットや他の外部の場所からの、悪意がある可能性のあるトラフィックから、アプリケーションやサービスを保護します。 Azure 仮想ネットワークで DDoS 標準保護を有効にすることで、DDoS 攻撃から資産を保護します。 Azure Security Center を使用して、ネットワーク関連のリスクに関する構成ミスのリスクを検出します。

Azure App Configuration は、Web アプリケーションを実行するためのものではなく、これらの Web アプリケーションの構成を実現します。 Web アプリケーションを対象とする外部ネットワーク攻撃から保護するために、追加設定の構成や、追加ネットワーク サービスのデプロイを行う必要はありません。

- [Azure Firewall のドキュメント](../firewall/index.yml)

- [Azure portal を使用した Azure DDoS Protection Standard の管理](../ddos-protection/manage-ddos-protection.md)

- [Azure Security Center の推奨事項](../security-center/recommendations-reference.md#recs-networking)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: 侵入検出および侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**:Azure Firewall で脅威インテリジェンス ベースのフィルター処理を使用して、既知の悪意のある IP アドレスおよびドメインとの間のトラフィックに対してアラートを送信したりブロックしたりします。 この IP アドレスとドメインのソースは、Microsoft の脅威インテリジェンス フィードです。 ペイロード検査が必要な場合は、ペイロード検査機能を備えたサードパーティの IDS/IPS ソリューションを、Azure Marketplace からデプロイできます。 または、ネットワーク ベースの IDS/IPS と組み合わせて、またはその代わりに、ホスト ベースの IDS/IPS またはホスト ベースのエンドポイント検出と応答 (EDR) ソリューションを使用することも選択できます。

注:IDS/IPS の使用に関して規制やその他の要件がある場合は、SIEM ソリューションに高品質のアラートが提供されるように、常に調整されていることを確認します。

- [Azure Firewall をデプロイする方法l](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace にはサードパーティの IDS 機能が含まれる](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Microsoft Defender の ATP EDR 機能](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: ネットワーク セキュリティ規則を簡略化する

**ガイダンス**:Azure App Configuration リソース用に構成されたネットワーク セキュリティ グループまたは Azure Firewall でのネットワーク アクセス制御を定義するには、Azure Virtual Network サービス タグを使用します。 アプリケーションのネットワーク内にある送信トラフィックのセキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグ "AppConfiguration" を使用できます。 規則の適切なソースまたはターゲット フィールドにサービス タグ名を指定することで、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

- [サービス タグとその使用方法の概要](../virtual-network/service-tags-overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="identity-management"></a>ID 管理

*詳細については、[Azure セキュリティ ベンチマークの「ID 管理](../security/benchmarks/security-controls-v2-identity-management.md).* 」を参照してください。

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1:Azure Active Directory を中央 ID および認証システムとして標準化する

**ガイダンス**:Azure App Configuration は、Azure の既定の ID およびアクセス管理サービスである Azure Active Directory (Azure AD) と統合されています。 Azure AD を標準化して、次のリソースでの組織の ID とアクセス管理を統制する必要があります。
- Azure portal、Azure Storage、Azure 仮想マシン (Linux と Windows)、Azure Key Vault、PaaS、SaaS アプリケーションなどの Microsoft Cloud リソース。
- Azure 上のアプリケーションや企業ネットワーク リソースなどの組織のリソース。

Azure AD を保護することは、組織のクラウド セキュリティ プラクティスの中で高い優先順位を持つ必要があります。 Azure AD では、Microsoft のベスト プラクティスの推奨事項を基準にした、お客様の ID セキュリティ体制を評価するために役立つ ID セキュリティ スコアが提供されます。 スコアを使用して、構成がベスト プラクティスの推奨事項とどの程度一致しているかを測定し、セキュリティ体制を強化します。

Azure には、Azure AD と OAuth を使用した、App Configuration データへのアクセスを承認するために、次の Azure 組み込みロールが用意されています。

- App Configuration データ所有者: このロールを使用して、App Configuration データへの読み取り/書き込み/削除アクセス権を付与します。 App Configuration リソースへのアクセスは許可されません。

- App Configuration データ閲覧者: このロールを使用して、App Configuration データへの読み取りアクセス権を付与します。 App Configuration リソースへのアクセスは許可されません。

- 共同作成者:このロールを使用して、App Configuration リソースを管理します。 App Configuration データにはアクセス キーを使用してアクセスできますが、このロールでは Azure AD を使用したデータへの直接アクセスは許可されません。

- 閲覧者:このロールを使用して、App Configuration リソースへの読み取りアクセス権を付与します。 リソースのアクセス キーへのアクセスと、App Configuration に格納されているデータへのアクセスは許可されません。

詳細については、次のリファレンスを参照してください。

- [Azure AD インスタンスを作成して構成する方法](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure Active Directory の ID セキュリティ スコアとは](../active-directory/fundamentals/identity-secure-score.md)

- [Azure AD を使用して Azure App Configuration へのアクセスを承認する](concept-enable-rbac.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2:アプリケーション ID を安全かつ自動的に管理する

**ガイダンス**:Azure で管理されている ID を使用して、他の Azure サービスなどの人間以外のアカウントから Azure App Configuration にアクセスします。 余計な資格情報を管理しなくて済むように、リソースにアクセスしたり実行したりするためのより強力な人間のアカウントを作成するのではなく、Azure のマネージド ID 機能を使用することをお勧めします。 Azure App Configuration には、Azure AD Authentication をサポートする他の Azure サービスやリソースにネイティブで認証するために、マネージド ID 自体を割り当てることもできます。 これにより、シークレットを取得するときに、App Configuration から Azure Key Vault に簡単にアクセスできます。 マネージド ID を使用する場合、その ID は Azure プラットフォームによって管理され、ユーザーがシークレットをプロビジョニングしたりローテーションしたりする必要はありません。

Azure App Configuration は、次の 2 つの ID が付与されたアプリケーションをサポートします。
- システム割り当て ID は、構成リソースに関連付けられています。 これは、構成リソースが削除されると削除されます。 構成リソースは 1 つのシステム割り当て ID しか持つことはできません。
- ユーザー割り当て ID は、構成リソースに割り当てることができるスタンドアロンの Azure リソースです。 構成リソースは、複数のユーザー割り当て ID を持つことができます。

マネージド ID を利用できない場合は、Azure App Configuration リソース レベルでアクセス許可が制限されたサービス プリンシパルを作成します。 証明書の資格情報を使用してこれらのサービス プリンシパルを構成し、クライアント シークレットにのみフォールバックします。 どちらの場合も、Azure Key Vault を Azure マネージド ID と組み合わせて使用し、ランタイム環境 (Azure 関数など) でキー コンテナーから資格情報を取得できるようにすることができます。

- [Azure App Configuration でマネージド ID を使用する方法](overview-managed-identity.md)

- [Azure マネージド ID](../active-directory/managed-identities-azure-resources/overview.md)

- [Azure リソースのマネージド ID をサポートするサービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [マネージド ID を使用して App Configuration にアクセスする](howto-integrate-azure-managed-service-identity.md)

- [Azure サービス プリンシパル](/powershell/azure/create-azure-service-principal-azureps) 

- [証明書を使用してサービス プリンシパルを作成する](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [Azure Key Vault を使用してセキュリティ プリンシパルを登録する](../key-vault/general/authentication.md#app-identity-and-security-principals)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3:アプリケーションのアクセスに Azure AD シングル サインオン (SSO) を使用する

**ガイダンス**:Azure App Configuration では、Azure Active Directory (Azure AD) を使用して、Azure リソース、クラウド アプリケーション、オンプレミスのアプリケーションに ID とアクセスの管理を提供します。 これには、従業員などの企業 ID だけでなく、パートナー、ベンダー、サプライヤーなどの外部 ID も含まれます。 Azure AD を使用すると、同期された企業 Active Directory ID を使用し、Azure portal 経由のシングルサインオン (SSO) で App Configuration サービスを管理できます。 すべてのユーザー、アプリケーション、デバイスを Azure AD に接続することで、シームレスで安全なアクセスを実現し、可視性と制御性を高めることができます。

- [Azure AD を使用したアプリケーションの SSO について理解する](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4:すべての Azure Active Directory ベースのアクセスに強力な認証制御を使用する

**ガイダンス**:Azure App Configuration は、多要素認証 (MFA) による強力な認証制御と、強力なパスワードレス手法をサポートする Azure Active Directory を使用します。
- 多要素認証 - Azure AD MFA を有効にし、Azure Security Center の ID とアクセス管理の推奨事項に従って、MFA 構成のベスト プラクティスを導入します。 MFA は、サインインの条件とリスク要因に基づいて、すべてのユーザー、選択されたユーザー、またはユーザー単位のレベルで適用できます。
- パスワードレス認証 - 3 つのパスワードレス認証オプションを使用できます。Windows Hello for Business、Microsoft Authenticator アプリ、スマート カードなどのオンプレミスの認証方法。

管理者と特権ユーザーについて、高いレベルの強力な認証方法が使用されていることを確認し、その後、適切な強力な認証ポリシーを他のユーザーにロールアウトします。

注:MFA は、App Configuration にアクセスして管理しているユーザー アカウントに適用できますが、プログラムによるサービス アカウントには適用できません。 可能な場合は、マネージド ID などのパスワードレス認証を使用して、すべてのユーザー アカウントに MFA を適用します。

- [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Active Directory のパスワードレス認証オプションの概要](../active-directory/authentication/concept-authentication-passwordless.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5:アカウントの異常を監視してアラートを出す

**ガイダンス**:Azure App Configuration は Azure Active Directory と統合されており、次のデータ ソースが利用できます。

-   サインイン - サインイン レポートは、マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報を提供します。

-   監査ログ - Azure AD 内のさまざまな機能によって行われたすべての変更についてログによる追跡可能性を提供します。 ログに記録される変更の監査ログの例としては、ユーザー、アプリ、グループ、ロール、ポリシーの追加や削除などがあります。

-   リスクの高いサインイン - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。

-   リスクのフラグ付きユーザー - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。

これらのデータ ソースは、Azure Monitor、Azure Sentinel、またはサードパーティの SIEM システムと統合できます。

Azure Security Center で、認証試行の失敗回数が多すぎるなど、サブスクリプションにおける特定の不審なアクティビティや、使用すべきでないアカウントに対してアラートを生成することもできます。 

セキュリティ ソリューションである Azure Advanced Threat Protection (ATP) では、オンプレミスの Active Directory シグナル信号を使用することで、高度な脅威、セキュリティ侵害を受けた ID、および悪意のある内部関係者のアクションを特定、検出、および調査できます。

- [Azure AD でアクティビティ レポートを監査する](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [危険なアクティビティのフラグが設定された Azure AD ユーザーを識別する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法](../security-center/security-center-identity-access.md)

- [Azure Security Center の脅威インテリジェンス保護モジュールでのアラート](../security-center/alerts-reference.md)

- [Azure アクティビティ ログを Azure Monitor に統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Azure AD Identity Protection からデータを接続する](../sentinel/connect-azure-ad-identity-protection.md)

- [Azure Advanced Threat Protection](/azure-advanced-threat-protection/what-is-atp)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6:条件に基づいて Azure リソースへのアクセスを制限する

**ガイダンス**:ユーザー定義の条件に基づく、よりきめ細かなアクセス制御 (特定の IP 範囲からのユーザー ログインでの MFA の使用の要求など) のため、Azure App Configuration は Azure Active Directory (Azure AD) の条件付きアクセスをサポートします。 さまざまなユース ケースに対してきめ細かな認証セッション管理ポリシーを使用することもできます。 これらの条件付きアクセス ポリシーは、App Configuration サービスにアクセスして管理するために Azure AD に認証するユーザー アカウントにのみ適用されますが、構成リソースに接続するサービス プリンシパルや接続文字列には適用されません。

- [Azure での条件付きアクセスの概要](../active-directory/conditional-access/overview.md)

- [一般的な条件付きアクセス ポリシー](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [条件付きアクセスを使用して認証セッション管理を構成する](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7:意図しない資格情報の公開を排除する

**ガイダンス**:Azure App Configuration を使用すると、ID やシークレットが含まれる可能性のある構成を格納できます。 構成内の資格情報を特定する資格情報スキャナーを実装することをお勧めします。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

Azure App Configuration サービスは、Azure Key Vault と一緒に使用してください。 資格情報はすべて Key Vault に保存し、App Configuration リソースに Key Vault の参照を作成して、これらの資格情報にリンクします。 App Configuration でこのような参照を作成すると、App Configuration には、その値そのものではなく、Key Vault の値の URI が格納されます。 アプリケーションは App Configuration に接続して、Key Vault から資格情報を取得できます。

GitHub の場合、ネイティブ シークレット スキャン機能を使用して、コード内で資格情報やその他の形式のシークレットを識別できます。

- [ASP.NET Core アプリで Key Vault 参照を使用するためのチュートリアル](use-key-vault-references-dotnet-core.md)

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [GitHub シークレット スキャン](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="privileged-access"></a>特権アクセス

*詳細については、[Azure セキュリティ ベンチマークの「特権アクセス](../security/benchmarks/security-controls-v2-privileged-access.md)」を参照してください。*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1:高い特権を持つユーザーを保護および制限する

**ガイダンス**:高い特権を持つアカウントまたはロールの数は制限し、それらのアカウントを厳格なレベルで保護してください。このような特権を持つユーザーは、Azure 環境内のあらゆるリソースを直接的または間接的に読み取ったり変更したりすることができるためです。

Azure Privileged Identity Management (PIM) を使用して、Azure リソースと Azure AD への Just-In-Time (JIT) の特権アクセスを有効にすることができます。 ユーザーが必要とする場合にのみ特権タスクを実行するための一時的なアクセス許可は、JIT によって付与されます。 PIM を使用すると、Azure AD 組織に不審なアクティビティや安全でないアクティビティがある場合に、セキュリティ アラートを生成することもできます。

アクセス キーは高度な特権を持っているため、セキュリティのベスト プラクティスとして、定期的にローテーションする必要があります。 アクセス キーには、資格情報を含む接続文字列があるため、シークレットと見なされます。 これらのシークレットは Azure Key Vault に格納する必要があり、コードはそれらを取得するために Key Vault に対して認証を行う必要があります。 アクセス キーは、アプリケーションに対して読み取り/書き込みアクセスまたは読み取りアクセスのみを許可できます。 不正アクセスを防ぐため、正しい種類のアクセス キーが発行されていることを確認します。 セキュリティをさらに強化するには、Azure AD のマネージド ID 機能を使用します。 このためにアプリケーションに必要なのは、構成値にアクセスするための構成エンドポイント URL のみです。

- [App Configuration のベスト プラクティス](howto-best-practices.md#app-configuration-bootstrap)

- [マネージド ID を使用して App Configuration にアクセスする](howto-integrate-azure-managed-service-identity.md)
- [Azure AD での管理者ロールのアクセス許可](../active-directory/roles/permissions-reference.md)

- [Azure Privileged Identity Management のセキュリティ アラートを使用する](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Azure AD でのハイブリッドおよびクラウド デプロイ用の特権アクセスをセキュリティで保護する](../active-directory/roles/security-planning.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2:ビジネス クリティカルなシステムへの管理アクセスを制限する

**ガイダンス**:Azure App Configuration は、Azure RBAC を使用して特権アクセスを許可するアカウントを制限することによって、ビジネス クリティカルなシステムへのアクセスを分離します。 Azure RBAC は、リソース レベルの App Configuration でサポートされます。 ビジネス クリティカルな構成を安全に保管するには、この情報を独自の App Configuration リソースに格納します。 リソース内では、読み取り専用アクセスのアカウントやキー、またはラベル付けやタグ付けを使用して、きめ細やかなアクセスを実現できます。

一貫したアクセス制御を確保するため、自分の企業のセグメント化戦略にすべての種類のアクセス制御を合わせる必要があります。

- [管理グループ アクセス](../governance/management-groups/overview.md#management-group-access)

- [Azure サブスクリプション管理者](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [App Configuration で Azure AD を使用して RBAC を統合するには](concept-enable-rbac.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3:ユーザー アクセスを定期的に確認して調整する

**ガイダンス**:Azure App Configuration は、Azure Active Directory (Azure AD) アカウントを使用してリソースを管理し、ユーザー アカウントとアクセス割り当てを定期的にレビューして、アカウントとそのアクセスが有効であることを確認します。 

Azure には、Azure AD と OAuth を使用した、App Configuration データへのアクセスを承認するために、次の Azure 組み込みロールが用意されています。

- App Configuration データ所有者: このロールを使用して、App Configuration データへの読み取り/書き込み/削除アクセス権を付与します。 App Configuration リソースへのアクセスは許可されません。

- App Configuration データ閲覧者: このロールを使用して、App Configuration データへの読み取りアクセス権を付与します。 App Configuration リソースへのアクセスは許可されません。

Azure AD アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロール (上記の App Configuration ロールなど) の割り当てをレビューすることができます。 Azure AD レポートによって、古いアカウントの検出に役立つログが提供されます。 また、Azure AD Privileged Identity Management を使用してアクセス レビュー レポート ワークフローを作成し、レビュー プロセスを容易にすることもできます。

注:マネージド ID は、別のサービスまたはアプリケーションから App Configuration への認証が可能な場合に推奨されます。 App Configuration へのアクセスで構成されたサービス プリンシパルまたは接続文字列は、使用するときに個別に管理する必要があります。

- [Privileged Identity Management (PIM) で Azure リソース ロールのアクセス レビューを作成する](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Azure AD の ID およびアクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

- [Azure AD を使用して Azure App Configuration へのアクセスを承認する](concept-enable-rbac.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4:Azure AD で緊急アクセスを設定する

**ガイダンス**:Azure App Configuration は、リソースを管理するため、Azure Active Directory と統合されています。 Azure AD 組織から誤ってロックアウトされるのを防ぐために、通常の管理者アカウントを使用できない場合にアクセスするための緊急アクセス用アカウントを設定します。 緊急アクセス用アカウントは高い特権を持っており、特定の個人に割り当てることはできません。 緊急アクセス用アカウントは、通常の管理者アカウントを使うことができない "緊急事態" に制限されます。

緊急アクセス用アカウントの資格情報 (パスワード、証明書、スマート カードなど) は安全に保管し、緊急時にのみそれらを使うことを許可された個人のみに知らせる必要があります。

- [Azure AD で緊急アクセス用アカウントを管理する](../active-directory/roles/security-emergency-access.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="pa-5-automate-entitlement-management"></a>PA-5:エンタイトルメント管理を自動化する 

**ガイダンス**:Azure App Configuration は、リソースを管理するため、Azure Active Directory と統合されています。 Azure AD のエンタイトルメント管理機能を使用して、アクセスの割り当て、レビュー、有効期限など、アクセス要求のワークフローを自動化します。 2 段階または複数段階の承認もサポートされています。

- [Azure AD アクセス レビューとは](../active-directory/governance/access-reviews-overview.md)

- [Azure AD エンタイトルメント管理とは](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6:特権アクセス ワークステーションを使用する

**ガイダンス**:セキュリティで保護された分離したワークステーションは、管理者、開発者、重要なサービス オペレーターのような機密性の高い役割のセキュリティには非常に重要です。 App Configuration に関連する管理タスクには、高度にセキュリティ保護されたユーザー ワークステーションや Azure Bastion を使用します。 Azure Active Directory、Microsoft Defender Advanced Threat Protection (ATP)、または Microsoft Intune を使用して、管理タスクのためにセキュリティで保護されたマネージド ユーザー ワークステーションを展開します。 セキュリティで保護されたワークステーションを一元管理して、強力な認証、ソフトウェアとハードウェアのベースライン、制限された論理アクセスとネットワーク アクセスなどのセキュリティで保護された構成を実施できます。

- [特権アクセス ワークステーションを理解する](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [特権アクセス ワークステーションを展開する](/security/compass/privileged-access-deployment)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7:Just Enough Administration (最小限の特権の原則) に従う 

**ガイダンス**:Azure App Configuration は、リソースを管理するため、Azure のロールベースのアクセス制御 (RBAC) と統合されています。 Azure RBAC を使用すると、ロールの割り当てによって Azure リソースへのアクセスを管理できます。 これらのロールを、ユーザー、グループ サービス プリンシパル、およびマネージド ID に割り当てることができます。 Azure App Configuration には、特定のリソースに対して定義済みの組み込みロールがあります。これらのロールは、Azure CLI、Azure PowerShell、Azure portal などのツールを使用してインベントリまたは照会できます。 Azure RBAC を使用してリソースに割り当てる特権は、常に、ロールで必要なものに制限する必要があります。 これは、Azure Active Directory Privileged Identity Management (PIM) のジャスト イン タイム (JIT) アプローチを補完するものであり、定期的に見直す必要があります。

Azure には、Azure AD と OAuth を使用した、App Configuration データへのアクセスを承認するために、次の Azure 組み込みロールが用意されています。
- App Configuration データ所有者: このロールを使用して、App Configuration データへの読み取り/書き込み/削除アクセス権を付与します。 App Configuration リソースへのアクセスは許可されません。
- App Configuration データ閲覧者: このロールを使用して、App Configuration データへの読み取りアクセス権を付与します。 App Configuration リソースへのアクセスは許可されません。

組み込みロールを使用してアクセス許可を割り当て、必要な場合にのみカスタム ロールを作成します。 

App Configuration では、1 つの App Configuration リソースに複数のアプリケーション構成を格納できます。 アプリケーション間の情報アクセスを制限するため、アプリケーションにつき 1 つの App Configuration リソースを作成し、それに合わせて Azure RBAC を設定してください。

- [Azure ロールベースのアクセス制御 (Azure RBAC) とは](../role-based-access-control/overview.md)

- [Azure RBAC を構成する方法](../role-based-access-control/role-assignments-portal.md)

- [Azure AD の ID およびアクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

- [Azure AD を使用して Azure App Configuration へのアクセスを承認する](concept-enable-rbac.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: Microsoft サポートの承認プロセスを選択する  

**ガイダンス**:Microsoft による App Configuration データへのアクセスが必要となる場合に備え、サポート シナリオ用の組織の承認プロセスを確立してください。 カスタマー ロックボックスは現在、App Configuration のサポート シナリオには使用できません。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-controls-v2-data-protection.md)」を参照してください。*

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1:機密データを検出、分類、ラベル付けする

**ガイダンス**:お使いの機密データを検出、分類、ラベル付けすると、組織の技術システムで機密情報が安全に保存、処理、および転送されるコントロールを適切に設計できます。 タグ付けを使用した機密情報のラベル付けは、App Configuration リソースではサポートされていますが、それに含まれる構成値はサポートしていません。 構成ストアに対する読み取りアクセス権または読み取り/書き込みアクセス権があるアプリケーションには、ストア内のすべての構成に対するフル アクセス権があります。

- [Azure Information Protection を使用して機密情報をタグ付けする](/azure/information-protection/what-is-information-protection)

- [Azure でのデータ分類のタグ付け](/azure/cloud-adoption-framework/govern/policy-compliance/data-classification#tagging-data-classification-in-azure)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="dp-2-protect-sensitive-data"></a>DP-2:機密データを保護する

**ガイダンス**:Microsoft によって管理される基になるプラットフォームの場合、Microsoft では顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護します。 Microsoft では、Azure 内の顧客データが確実にセキュリティで保護されるように、データ保護コントロールおよび機能をいくつか実装しています。 App Configuration リソースに対してアクセス キーを定期的にローテーションするようにします。 接続文字列の資格情報は Azure Key Vault に格納する必要があり、コードはそれらを取得するために Key Vault に対して認証を行う必要があります。 アクセス キーは、アプリケーションに対して読み取り/書き込みアクセスまたは読み取りアクセスのみを許可できます。 不正アクセスを防ぐため、正しい種類のアクセス キーが発行されていることを確認します。 セキュリティをさらに強化するには、Azure AD のマネージド ID 機能を使用します。 このためにアプリケーションに必要なのは、構成値にアクセスするための構成エンドポイント URL のみです。

Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、次のようにアクセスを制限します。

- 機密データを独自の App Configuration リソースに分離し、それに合わせて RBAC ポリシーを割り当てて、許可されたアクセスのみが有効になるようにします。 

- ネットワークベースのアクセス制御を使用します。

- Azure サービスでの特定の制御 (SQL データベースやその他のデータベースでの暗号化など) や、一貫したアクセス制御を実現するため、すべての種類のアクセス制御を企業のセグメント化戦略に合わせる必要があります。

- 企業のセグメント化戦略では、機密またはビジネスに重要なデータやシステムの場所からも通知される必要があります。

詳細については、次のリファレンスを参照してください。

- [Azure Active Directory を使用して Azure App Configuration へのアクセスを承認する](concept-enable-rbac.md)

- [App Configuration のデータ暗号化](faq.yml#does-app-configuration-encrypt-my-data)

- [Azure のロールベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md) 

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4:転送中の機密情報を暗号化する

**ガイダンス**:アクセス制御を補完するため、暗号化を使用して、転送中のデータを "帯域外" 攻撃 から保護する必要があります。 これにより、攻撃者がデータを簡単に読み取ったり変更したりすることが確実にできなくなります。

Azure App Configuration は、すべての HTTP 要求に TLS 暗号化を使用します。 Azure インフラストラクチャは、ネットワーク レベルの暗号化レイヤーを提供することで、Azure データセンター間のすべての要求を守ります。 HTTP トラフィックの場合、App Configuration リソースに接続するすべてのクライアントのネゴシエートには、必ず TLS v1.2 以上を使用します。

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5:保存データを暗号化する

**ガイダンス**:保存データを "帯域外" 攻撃 (基になっているストレージへのアクセスなど) から保護するには、アクセス制御を補完する暗号化を使用する必要があります。 これにより、攻撃者がデータを簡単に読み取ったり変更したりすることが確実にできなくなります。

Azure では、既定で保存データが暗号化されます。 機密性の高いデータには、保存されている利用可能なすべての Azure リソースに、オプションで暗号化を追加できます。 既定では Azure によってお使いの暗号化キーが管理されますが、Azure App Configuration に対して、お使いの独自のキー (カスタマー マネージド キー) を管理するオプションが Azure により提供されています。

- [カスタマー マネージド キーを使用して Azure App Configuration のデータを暗号化する](concept-customer-managed-keys.md)

- [Azure での保存時の暗号化の概要](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [暗号化モデルとキー管理テーブル](../security/fundamentals/encryption-atrest.md#azure-resource-providers-encryption-model-support)

- [Azure における保存データの二重暗号化](../security/fundamentals/double-encryption.md#data-at-rest)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="asset-management"></a>アセット管理

*詳細については、[Azure セキュリティ ベンチマークの「アセット管理](../security/benchmarks/security-controls-v2-asset-management.md)」を参照してください。*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1:セキュリティ チームが資産のリスクを確実に可視化できるようにする

**ガイダンス**:セキュリティ チームに Azure テナントとサブスクリプションのセキュリティ閲覧者アクセス許可を付与して、セキュリティ チームが Azure Security Center を使用してセキュリティ上のリスクを監視できるようにします。 

セキュリティ チームの責任がどのように構造化されているかによって、セキュリティ リスクの監視は中央のセキュリティ チームまたはローカル チームの責任になります。 ただし、セキュリティ分析情報とリスクは、常に組織内で一元的に集計する必要があります。 

セキュリティ閲覧者のアクセス許可は、テナント全体 (ルート管理グループ) に幅広く適用することも、管理グループまたは特定のサブスクリプションにスコープ指定することもできます。 

注:ワークロードとサービスを可視化するには、追加のアクセス許可が必要になることがあります。 

- [セキュリティ閲覧者ロールの概要](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理グループの概要](../governance/management-groups/overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2:セキュリティ チームが資産インベントリとメタデータに確実にアクセスできるようにする

**ガイダンス**:セキュリティ チームが、Azure App Configuration などの Azure 上の資産の継続的に更新されるインベントリに確実にアクセスできるようにします。 セキュリティ チームは、組織が新たなリスクにさらされる可能性を評価するため、および継続的なセキュリティ改善への入力として、このインベントリを必要とすることがよくあります。 Azure Active Directory グループを作成して、社内の承認されたセキュリティ チームをそのグループに含め、すべての Azure App Configuration リソースに対する読み取りアクセス権を割り当てます。これは、サブスクリプション内の 1 つに高レベルのロールを割り当てることで簡略化できます。

Azure Security Center インベントリ機能と Azure Resource Graph を使用すると、クエリを実行してサブスクリプション内のすべてのリソース (Azure サービス、アプリケーション、ネットワーク リソースなど) を検出できます。

Azure リソース、リソース グループ、サブスクリプションにタグを適用して、それらを論理的に分類できます。 各タグは、名前と値のペアで構成されます。 たとえば、運用環境のすべてのリソースには名前 "環境" と値 "運用" を適用できます。

- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

- [Azure Security Center の資産インベントリ管理](../security-center/asset-inventory.md)

- 資産のタグ付けの詳細については、「[リソースの名前付けとタグ付けの意思決定ガイド](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)」を参照

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3:承認された Azure サービスのみを使用する

**ガイダンス**:Azure App Configuration は、Azure Policy を使用した、Azure Resource Manager ベースの展開と構成の適用をサポートしています。 Azure Policy を使用して、環境内でユーザーがプロビジョニングできるサービスを監査および制限します。 Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。 また、Azure Monitor を使用して、承認されていないサービスが検出されたときにアラートをトリガーするルールを作成することもできます。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](../governance/policy/samples/built-in-policies.md#general)

- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4:アセット ライフサイクル管理のセキュリティを確保する

**ガイダンス**:大きな影響を与える可能性のある変更について、アセット ライフサイクル管理プロセスに対応するセキュリティ ポリシーを確立または更新します。 これらの変更には、ID プロバイダーとアクセス、データの秘密度、ネットワークの構成、管理特権の割り当てに対する変更が含まれますがこれに限定されません。

不要になったら Azure リソースを削除します。 管理者によるアクセス キーの定期的なローテーションを確実に行い、認証済みユーザーのみが構成リソースにアクセスできるようにします。

- [App Configuration に使用する暗号化キーのローテーション](concept-customer-managed-keys.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: "Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure 条件付きアクセスを使用します。

- [条件付きアクセスを構成して Azure Resource Manager へのアクセスをブロックする方法](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="logging-and-threat-detection"></a>ログと脅威検出

*詳細については、[Azure セキュリティ ベンチマークの「ログと脅威検出](../security/benchmarks/security-controls-v2-logging-threat-detection.md)」を参照してください。*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2:Azure ID とアクセスの管理のために脅威検出を有効にする

**ガイダンス**:App Configuration は、Azure Active Directory (Azure AD) と統合されています。 これにより次のユーザー ログが記録され、これは Azure AD レポートで確認できます。また、より高度な監視と分析のユース ケースの場合は、Azure Monitor、Azure Sentinel、またはその他の SIEM/監視ツールと統合できます。
- サインイン - サインイン レポートは、マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報を提供します。
- 監査ログ - Azure AD 内のさまざまな機能によって行われたすべての変更についてログによる追跡可能性を提供します。 監査ログの例として、ユーザー、アプリ、グループ、ロール、ポリシーの追加や削除など、Azure AD 内のあらゆるリソースに加えられた変更があります。
- リスクの高いサインイン - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。
- リスクのフラグ付きユーザー - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。

Azure Security Center で、認証試行の失敗回数が多すぎるなど、サブスクリプションにおける特定の不審なアクティビティや、非推奨のアカウントに対してアラートを生成することもできます。 基本のセキュリティ検疫監視に加え、Azure Security Center の Threat Protection モジュールは、Azure サービス レイヤーのより詳しいセキュリティ アラートを収集します。 この機能により、個々のリソース内でアカウントの異常を確認できます。

App Configuration 構成リソースにアクセスするための別の方法は、アクセス キーを使用することです。 許可されていないエージェントによる構成リソースへのアクセスを防止するため、これらは、定期的にローテーションする必要があります。 ローテーションは、ポータルでアクセス キーに対して直接行えます。

- [Azure App Configuration でマネージド ID を使用して Azure AD の他の保護リソースにアクセスできるようにする方法](overview-managed-identity.md)

- [Azure App Configuration でマネージド ID を使用する](howto-integrate-azure-managed-service-identity.md)

- [Azure Active Directory の監査アクティビティ レポート](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure Identity Protection を有効にする](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure AD を使用して Azure App Configuration へのアクセスを承認する](concept-enable-rbac.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3:Azure ネットワーク アクティビティのログ記録を有効にする

**ガイダンス**:Azure App Configuration によって、仮想ネットワークにリソースが直接デプロイされることはありません。 ただし、App Configuration では、プライベート エンドポイントを使用して、仮想ネットワークから Azure App Configuration に安全に接続できます。 また、Azure App Configuration では、有効にする必要がある DNS クエリ ログの生成や処理も行われません。

構成済みの App Configuration のプライベート エンドポイントでログ記録を有効にし、次を取得します。
- プライベート エンドポイントによって処理されるデータ (入力/出力)
- Private Link サービスによって処理されるデータ (入力/出力)
- NAT ポートの使用可能性

詳細については、次のリファレンスを参照してください。

- [Azure Private Link の監視](../private-link/private-link-overview.md#logging-and-monitoring)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4:Azure リソースのログ記録を有効にする

**ガイダンス**:自動的に利用できるアクティビティ ログには、読み取り操作 (GET) を除く、App Configuration リソースに対するすべての書き込み操作 (PUT、POST、DELETE) が含まれています。 アクティビティ ログを使用すると、トラブルシューティング時にエラーを見つけたり、組織内のユーザーがリソースをどのように変更したかを監視したりできます。 App Configuration の場合、アクティビティ ログはコントロール プレーンでのみ利用でき、Azure Resource Manager (ARM) に表示されます。 App Configuration の顧客向けデータ プレーンのログ記録は、現在サポートされていません。 Azure リソースのログも構成できません。

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure でのログ記録とログのさまざまな種類について](../azure-monitor/essentials/platform-logs-overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5:セキュリティ ログの管理と分析を一元化する

**ガイダンス**:ログ記録のストレージと分析を一元化して、相関関係を有効にします。 ログ ソースごとに、データ所有者、アクセス ガイダンス、ストレージの場所、データの処理とアクセスに使用するツール、データ保持の要件を割り当てたことを確認します。

Azure アクティビティ ログを一元的なログ記録に統合していることを確認します。 Azure Monitor を介してログを取り込み、エンドポイント デバイス、ネットワーク リソース、およびその他のセキュリティ システムによって生成されたセキュリティ データを集計します。 Azure Monitor で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期のアーカイブ ストレージには Azure Storage アカウントを使用します。

さらに、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードします。 多くの組織では、頻繁に使用される "ホット" データに対しては Azure Sentinel を、使用頻度の低い "コールド" データに対しては Azure Storage を使用することを選択しています。

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="lt-6-configure-log-storage-retention"></a>LT-6:ログの保持期間を構成する

**ガイダンス**:App Configuration ログを格納するために使用されるすべてのストレージ アカウントまたは Log Analytics ワークスペースに、組織のコンプライアンス規則に従ったログの保有期間が設定されていることを確認します。 長期ストレージおよびアーカイブ ストレージには、Azure Storage、Data Lake、または Log Analytics ワークスペースのアカウントを使用します。

Azure Monitor で、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定できます。

- [Log Analytics ワークスペースの保有期間を構成する方法](../azure-monitor/logs/manage-cost-storage.md)

- [ Azure ストレージ アカウントでのリソース ログの格納](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../security/benchmarks/security-controls-v2-incident-response.md)」を参照してください。*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: 準備 – インシデント対応プロセスを Azure 用に更新する

**ガイダンス**:組織において、セキュリティ インシデントに対応するためのプロセスが用意されていること、Azure のこれらのプロセスが更新されていること、それらのプロセスを定期的に使用して準備されていることを確認します。

- [企業環境全体にセキュリティを実装する](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [インシデント対応のリファレンス ガイド](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: 準備 – インシデント通知をセットアップする

**ガイダンス**:Azure Security Center でセキュリティ インシデントの連絡先情報を設定します。 この連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 また、インシデント対応のニーズに応じて、異なる Azure サービスでインシデント アラートと通知をカスタマイズするオプションもあります。 

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: 検出と分析 – 高品質のアラートに基づいてインシデントを作成する

**ガイダンス**:高品質のアラートを作成し、アラートの品質を測定するプロセスがあることを確認します。 これにより、過去のインシデントから教訓を学び、アナリストに対するアラートに優先順位を付けることができるので、擬陽性に時間を無駄にすることがありません。 

高品質のアラートは、過去のインシデントからの経験、検証されたコミュニティ ソース、およびさまざまなシグナル ソースの融合と関連付けによってアラートを生成してクリーンアップするように設計されたツールに基づいて構築できます。 

Azure Security Center では、多数の Azure 資産について高品質のアラートが提供されます。 ASC データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。 Azure Sentinel を使用すると、高度なアラート ルールを作成し、調査のためにインシデントを自動的に生成できます。 

エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートし、Azure リソースへのリスクを特定します。 アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートします。

- [エクスポートを構成する方法](../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: 検出と分析 – インシデントを調査する

**ガイダンス**:アナリストが潜在的なインシデントを調査するときに、さまざまなデータ ソースを照会して使用し、発生した内容の完全なビューを構築できることを確認します。 キル チェーン全体で潜在的な攻撃者のアクティビティを追跡して死角を回避するには、さまざまなログを収集する必要があります。  また、他のアナリストのため、および将来において履歴が参照される場合のために、確実に分析情報と学習がキャプチャされているようにします。  

調査のためのデータ ソースには、スコープ内のサービスおよび実行中のシステムから既に収集されている一元化されたログ ソースが含まれますが、次のものも含まれます。

- ネットワーク データ - ネットワーク セキュリティ グループのフロー ログ、Azure Network Watcher、Azure Monitor を使用して、ネットワーク フロー ログやその他の分析情報をキャプチャします。 

- 実行中のシステムのスナップショット: 

    - Azure 仮想マシンのスナップショット機能を使用して、実行中のシステムのディスクのスナップショットを作成します。 

    - オペレーティング システムのネイティブ メモリ ダンプ機能を使用して、実行中のシステムのメモリのスナップショットを作成します。

    - Azure サービスのスナップショット機能またはソフトウェア独自の機能を使用して、実行中のシステムのスナップショットを作成します。

Azure Sentinel により、事実上すべてのログソースに対して広範な Data Analytics と、インシデントのライフサイクル全体を管理するためのケース管理ポータルが提供されます。 調査中のインテリジェンス情報を、追跡とレポートのためにインシデントに関連付けることができます。 

- [Windows マシンのディスクのスナップショットを作成する](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux マシンのディスクのスナップショットを作成する](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure サポートの診断情報とメモリ ダンプ コレクション](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Azure Sentinel でインシデントを調査します](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: 検出と分析 – インシデントの優先順位を付ける

**ガイダンス**:アラートの重要度と資産の機密性に基づいて、最初に注目するインシデントについてのコンテキストをアナリストに提供します。 

Azure Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

さらに、タグを使用してリソースをマークし、Azure リソース (特に、機密データを処理するもの) を識別して分類するための命名システムを作成します。  インシデントが発生した Azure リソースと環境の重要度に基づいて、アラートの修復に優先順位を付けることは、お客様の責任です。

- [Security alerts in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: 包含、根絶、復旧 – インシデントの処理を自動化する

**ガイダンス**:手動による反復タスクを自動化して、応答時間を短縮し、アナリストの負担を軽減します。 手動タスクの実行には時間がかかり、各インシデントの速度が低下し、アナリストが処理できるインシデントの数が減少します。 手動タスクではアナリストの疲労も増加します。これにより、遅延が発生する人的エラーのリスクが増加し、複雑なタスクに効果的に焦点を当てるアナリストの能力が低下します。 Azure Security Center と Azure Sentinel のワークフロー自動化機能を使用して、自動的にアクションをトリガーしたり、プレイブックを実行して受信したセキュリティ アラートに応答したりします。 プレイブックにより、通知の送信、アカウントの無効化、問題のあるネットワークの特定などのアクションが実行されます。 

- [Security Center でワークフロー自動化を構成する](../security-center/workflow-automation.md)

- [Azure Security Center で脅威への自動対応を設定する](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Azure Sentinel で脅威への自動対応を設定します](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="posture-and-vulnerability-management"></a>体制と脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「体制と脆弱性の管理](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)」を参照してください。*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Azure サービスのセキュリティで保護された構成を確立する 

**ガイダンス**:Azure App Configuration は、以下のサービス固有のポリシーをサポートしています。これは、Azure リソースの構成の監査と適用のために、Azure Security Center に用意されています。 これは、Azure Security Center または Azure Policy イニシアチブで構成できます。
- App Configuration では、カスタマー マネージド キーを使用する必要があります。カスタマー マネージド キーは、暗号化キーを管理できるようにすることで、データ保護を強化します。 これは、多くの場合、コンプライアンス要件を満たすために必要となります。
- App Configuration はプライベート リンクを使用する必要があります。プライベート エンドポイント接続を使用すると、仮想ネットワーク上のクライアントは、プライベート リンク経由で Azure App Configuration に安全にアクセスできます。

Azure Blueprints を使用すると、1 つのブループリント定義で、Azure Resource Manager テンプレート、Azure RBAC コントロール、ポリシーなど、サービスとアプリケーション環境のデプロイと構成を自動化することができます。

- [App Configuration ポリシーに関する詳細情報](../governance/policy/samples/built-in-policies.md#app-configuration)

- [Azure Security Center でのセキュリティ ポリシーの操作](../security-center/tutorial-security-policy.md)

- [エンタープライズ規模のランディング ゾーンでのガードレールの実装の図](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [チュートリアル:コンプライアンスを強制するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint](../governance/blueprints/overview.md)

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Azure サービスのセキュリティで保護された構成を維持する

**ガイダンス**:Azure Security Center を使用して、構成基準を監視し、Azure Policy の使用を強制します。 App Configuration の Azure Policy には次が含まれます。 
- App Configuration では、カスタマー マネージド キーを使用する必要があります。カスタマー マネージド キーは、暗号化キーを管理できるようにすることで、データ保護を強化します。 これは、多くの場合、コンプライアンス要件を満たすために必要となります。
- App Configuration はプライベート リンクを使用する必要があります。プライベート エンドポイント接続を使用すると、仮想ネットワーク上のクライアントは、プライベート リンク経由で Azure App Configuration に安全にアクセスできます。

- [Azure Policy の効果について](../governance/policy/concepts/effects.md) 

- [コンプライアンスを強制するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: 定期的に攻撃シミュレーションを実施する

**ガイダンス**:必要に応じて、Azure リソースの侵入テストまたはレッド チーム アクティビティを実施し、セキュリティに関するすべての重大な調査結果が確実に修復されるようにします。
お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft クラウド侵入テストの実施ルールに従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。

- [Azure での侵入テスト](../security/fundamentals/pen-testing.md)

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="backup-and-recovery"></a>バックアップと回復

*詳細については、[Azure セキュリティ ベンチマークの「バックアップと回復」](../security/benchmarks/security-controls-v2-backup-recovery.md)を参照してください。*

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4:キー紛失のリスクを軽減する

**ガイダンス**:キーの紛失を回避および回復する手段を確保します。 Azure Key Vault で論理的な削除と消去保護を有効にして、キーが偶発的または悪意から削除されないようにします。

- [Key Vault で論理的な削除と消去保護を有効にする方法](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="governance-and-strategy"></a>ガバナンスと戦略

*詳細については、[Azure セキュリティ ベンチマークの「ガバナンスと戦略](../security/benchmarks/security-controls-v2-governance-strategy.md)」を参照してください。*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: 資産の管理とデータ保護の戦略を定義する 

**ガイダンス**:システムとデータを継続的に監視および保護するための明確な戦略が文書化および伝達されるようにします。 ビジネスに不可欠なデータとシステムの検出、評価、保護、監視の優先順位を決定します。 

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

-   ビジネス リスクに応じたデータ分類標準

-   リスクと資産のインベントリに対するセキュリティ組織の可視性 

-   Azure サービスを使用するためのセキュリティ組織の承認 

-   ライフサイクルを通じた資産のセキュリティ

-   組織のデータ分類に従った必要なアクセス制御戦略

-   Azure ネイティブおよびサードパーティのデータ保護機能の使用

-   転送中および保存中のユース ケースのデータ暗号化要件

-   適切な暗号化標準

詳細については、次のリファレンスを参照してください。
- [Azure セキュリティ アーキテクチャに関する推奨事項 - ストレージ、データ、暗号化](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Azure のセキュリティの基礎 - Azure のデータ セキュリティ、暗号化、ストレージ](../security/fundamentals/encryption-overview.md)

- [クラウド導入フレームワーク - Azure のデータ セキュリティと暗号化のベスト プラクティス](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure セキュリティ ベンチマーク - アセット管理](../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure セキュリティ ベンチマーク - データ保護](../security/benchmarks/security-controls-v2-data-protection.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: 企業のセグメント化戦略を定義する 

**ガイダンス**:ID、ネットワーク、アプリケーション、サブスクリプション、管理グループ、その他のコントロールを利用し、アセットへのアクセスをセグメント化する企業規模の戦略を確立します。

セキュリティ分離の必要性と、互いと通信し、データにアクセスする必要があるシステムの日常的操作を有効にするという必要性のバランスを慎重に取ります。

セグメント化戦略は、ネットワーク セキュリティ、ID とアクセス モデル、アプリケーション アクセス許可とアクセス モデル、人的プロセスの制御など、あらゆるコントロールの種類を対象として確実に一貫性をもって実装します。

- [Azure のセグメント化戦略に関するガイド (動画)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure のセグメント化戦略に関するガイド (ドキュメント)](/security/compass/governance#enterprise-segmentation-strategy)

- [ネットワークのセグメント化を企業のセグメント化戦略に合わせる](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: セキュリティ態勢管理の戦略を定義する

**ガイダンス**:個々の資産とそれらがホストされている環境に対するリスクを継続的に測定し、軽減します。 高い価値を持つ資産と、攻撃に晒される可能性の高い部分 (公開されたアプリケーション、ネットワークのイングレス ポイントとエグレス ポイント、ユーザーと管理者のエンドポイントなど) を優先します。

- [Azure セキュリティ ベンチマーク - 体制と脆弱性の管理](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: 組織の役割、責任、責務を整合させる

**ガイダンス**:セキュリティ組織における役割と責任に関する明確な戦略が文書化されて伝えられるようにします。 セキュリティに関する決定についてわかりやすく説明すること、共有される責任モデルについて全員に教育すること、クラウドをセキュリティで保護するテクノロジについて技術チームに教育することを優先とします。

- [Azure のセキュリティのベスト プラクティス 1 – 人: クラウド セキュリティに関する取り組みについてチームを教育する](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure のセキュリティのベスト プラクティス 2 - 人: クラウド セキュリティ テクノロジについてチームを教育する](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure のセキュリティのベスト プラクティス 3 - プロセス: クラウドのセキュリティに関する意思決定のアカウンタビリティを割り当てる](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="gs-5-define-network-security-strategy"></a>GS-5: ネットワーク セキュリティ戦略を定義する

**ガイダンス**:組織全体のセキュリティ アクセス制御戦略の一環として、Azure ネットワーク セキュリティ アプローチを確立します。  

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

-   一元的なネットワーク管理とセキュリティ責任

-   エンタープライズ セグメント化戦略と一致する仮想ネットワーク セグメント化モデル

-   さまざまな脅威と攻撃のシナリオにおける修復戦略

-   インターネット エッジとイングレスおよびエグレス戦略

-   クラウドとオンプレミスのハイブリッド相互依存戦略

-   最新のネットワーク セキュリティ成果物 (例: ネットワーク図、参照ネットワーク アーキテクチャ)

詳細については、次のリファレンスを参照してください。
- [Azure のセキュリティのベスト プラクティス 11 - アーキテクチャ。単一の統合セキュリティ戦略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure セキュリティ ベンチマーク - ネットワーク セキュリティ](../security/benchmarks/security-controls-v2-network-security.md)

- [Azure のネットワーク セキュリティの概要](../security/fundamentals/network-overview.md)

- [エンタープライズ ネットワーク アーキテクチャ戦略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: ID と特権アクセス戦略を定義する

**ガイダンス**:組織全体のセキュリティ アクセス制御戦略の一環として、Azure の ID と特権アクセス アプローチを確立します。  

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

-   一元化された ID と認証システム、および他の内部および外部 ID システムとのその相互接続

-   さまざまなユース ケースおよび条件における強力な認証方法

-   高い特権を持つユーザーの保護

-   異常なユーザー アクティビティの監視と処理  

-   ユーザー ID とアクセスの確認と調整のプロセス

詳細については、次のリファレンスを参照してください。

- [Azure セキュリティ ベンチマーク - ID 管理](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure セキュリティ ベンチマーク - 特権アクセス](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Azure のセキュリティのベスト プラクティス 11 - アーキテクチャ。単一の統合セキュリティ戦略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure ID 管理のセキュリティの概要](../security/fundamentals/identity-management-overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: ログ記録と脅威対応戦略を定義する

**ガイダンス**:コンプライアンス要件を満たしながら脅威を迅速に検出して修復するための、ログ記録と脅威対応戦略を確立します。 統合と手動手順ではなく脅威に集中できるように、高品質のアラートとシームレスなエクスペリエンスをアナリストに提供することを優先します。 

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

-   セキュリティ運用 (SecOps) 組織の役割と責任 

-   NIST または他の業界フレームワークと一致する、明確に定義されたインシデント対応プロセス 

-   脅威の検出、インシデント対応、コンプライアンスのニーズに対応するためのログのキャプチャと保持

-   SIEM、Azure のネイティブ機能、その他のソースを使用した、脅威に関する情報の一元的な可視化と関連付け 

-   顧客、サプライヤー、および関心を持つパブリック パーティに関するコミュニケーションと通知の計画

-   ログ記録と脅威の検出、フォレンジクス、攻撃の修復と根絶など、インシデント処理に対する Azure ネイティブおよびサードパーティのプラットフォームの使用

-   インシデントとインシデント発生後のアクティビティを処理するためのプロセス (教訓や証拠の保持など)

詳細については、次のリファレンスを参照してください。

- [Azure セキュリティ ベンチマーク - ログと脅威検出](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure セキュリティ ベンチマーク - インシデント対応](../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure のセキュリティのベスト プラクティス 4 - プロセス: クラウドのインシデント対応プロセスを更新する](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 導入フレームワーク、ログ、およびレポートの決定ガイド](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure のエンタープライズ スケーリング、管理、監視](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="next-steps"></a>次のステップ

- 「[Azure セキュリティ ベンチマーク V2 の概要](../security/benchmarks/overview.md)」を参照してください。
- [Azure セキュリティ ベースライン](../security/benchmarks/security-baselines-overview.md)の詳細について学習する
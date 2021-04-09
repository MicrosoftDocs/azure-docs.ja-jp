---
title: Virtual WAN 用の Azure セキュリティ ベースライン
description: Virtual WAN セキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e4bbc1565cec2c356e916f813a6a334648d82754
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726735"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Virtual WAN 用の Azure セキュリティ ベースライン

このセキュリティ ベースラインによって、[Azure セキュリティ ベンチマーク バージョン 2.0](../security/benchmarks/overview.md) のガイダンスが Microsoft Azure Virtual WAN に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 内容は、Virtual WAN に適用される Azure セキュリティ ベンチマークとそれに関連するガイダンスで定義されている **セキュリティ コントロール** によってグループ化されています。 Virtual WAN に適用できない **コントロール** は、除外されています。

Azure セキュリティ ベンチマークに対する Virtual WAN の完全なマッピングを確認するには、[Virtual WAN の完全なセキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-controls-v2-network-security.md)」を参照してください。*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: 内部トラフィック用のセキュリティを実装する

**ガイダンス**:Microsoft Azure Virtual WAN にはカスタム ルーティング機能が備わっており、ExpressRoute トラフィックの暗号化ができます。 すべてのルート管理は仮想ハブ ルーターによって提供されます。これにより、仮想ネットワーク間のトランジット接続も可能になります。 ExressRoute トラフィックを Virtual WAN で暗号化すると、パブリック インターネットを経由したり、パブリック IP アドレスを使用したりすることなく、ExpressRoute を経由してオンプレミス ネットワークと Azure 仮想ネットワークの間で暗号化されたトランジットを提供できます。 

- [ExpressRoute トラフィックの暗号化](virtual-wan-about.md#encryption)

- [Virtual WAN で Private Link を使用する](howto-private-link.md)

- [カスタム ルーティングのシナリオ](scenario-any-to-any.md)

- [カスタム ルーティング テーブルのドキュメント](how-to-virtual-hub-routing.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="ns-2-connect-private-networks-together"></a>NS-2: プライベート ネットワークをまとめて接続する 

**ガイダンス**:Microsoft Azure ExpressRoute は、Azure Virtual WAN へのプライベート接続を提供します。 ExpressRoute 接続はパブリック インターネットを経由しないので、一般的なインターネット接続と比べて信頼性が高く、高速で、待ち時間も短くなります。 また、仮想プライベート ネットワークを使用し、サイト間 (S2S) VPN またはポイント対サイト (P2S) VPN を介して Azure に接続することもできます。

- [Virtual WAN 内の ExpressRoute](virtual-wan-expressroute-portal.md)

- [サイト間 VPN の概要](virtual-wan-site-to-site-portal.md)

- [ポイント対サイト VPN の概要](virtual-wan-point-to-site-portal.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: 外部ネットワーク攻撃からアプリケーションやサービスを保護する

**ガイダンス**:Virtual WAN は、外部ネットワークにエンドポイントを公開しません。従来のネットワーク保護でエンドポイントのセキュリティを確保する必要があります。 仮想ネットワーク保護サービスを使用して、スポーク仮想ネットワーク (仮想ハブに接続されている仮想ネットワーク) のリソースを保護できます。 

Azure Firewall を使用して、インターネットや他の外部の場所からの、悪意がある可能性のあるトラフィックから、アプリケーションやサービスを保護します。 

Azure Virtual Network に対する攻撃から資産を保護するには、Azure が提供する DDoS Protection を選択します。 Azure Security Center を使用して、ネットワーク関連リソースに関する構成ミスのリスクを検出します。

- [Azure Firewall のドキュメント](../firewall/index.yml)

- [Azure portal を使用した Azure DDoS Protection Standard の管理](../ddos-protection/manage-ddos-protection.md) 

- [Azure Security Center の推奨事項](../security-center/recommendations-reference.md#recs-networking)

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: 侵入検出および侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**:Virtual WAN はMicrosoft のマネージド サービスです。 ネイティブの侵入検出や侵入防止機能は提供されません。 ただし、Azure Firewall で Virtual WAN に提供されるセキュリティ機能により、統一されたポリシー制御を実現できます。 Azure Firewall ポリシーを作成し、そのポリシーを Virtual WAN ハブにリンクすると、既存の Virtual WAN ハブがセキュリティ保護付き仮想ハブとして機能でき、必要な Azure Firewall リソースをデプロイでできます。

- [仮想 WAN ハブ内で Azure Firewall を構成する](howto-firewall.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: ネットワーク セキュリティ規則を簡略化する

**ガイダンス**:Virtual Network サービス タグを使用し、ネットワーク セキュリティ グループまたは Azure Firewall のネットワーク アクセス制御を定義して、ネットワーク セキュリティ規則を簡略化します。 セキュリティ規則を作成するときに、特定の IP アドレスの代わりにサービス タグを使用できます。 規則のソースまたはターゲット フィールドでサービス タグ名を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

- [サービス タグの概要と使用](../virtual-network/service-tags-overview.md)

- [アプリケーション セキュリティ グループの概要と使用](../virtual-network/network-security-groups-overview.md#application-security-groups)

- [Azure Firewall のドキュメント](../firewall/index.yml)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: セキュリティで保護されたドメイン ネーム サービス (DNS)

**ガイダンス**:セキュリティで保護された DNS 機能は、Azure Firewall を使用して Virtual WAN に提供されます。 クライアント仮想マシンから DNS サーバーへの DNS 要求の仲介役となる DNS プロキシとして機能するように、Azure Firewall を構成します。 カスタム DNS サーバーの構成では、DNS 解決の不一致を回避するために DNS プロキシを有効にし、ネットワーク ルールで完全修飾ドメイン名のフィルタリングを有効にします。 

- [Azure Firewall のドキュメント](../firewall/index.yml)

- [Azure Firewall の DNS 設定](../firewall/dns-settings.md)

- [プライベート リンクを使用して DNS フォワーダーとして Azure Firewall を使用する](https://github.com/adstuart/azure-privatelink-dns-azurefirewall)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="identity-management"></a>ID 管理

*詳細については、[Azure セキュリティ ベンチマークの「ID 管理](../security/benchmarks/security-controls-v2-identity-management.md).* 」を参照してください。

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1:Azure Active Directory を中央 ID および認証システムとして標準化する

**ガイダンス**:Azure Active Directory (Azure AD) は、Azure サービス向けの既定の ID およびアクセス管理サービスです。 これには Virtual WAN も含まれます。 Azure AD を標準化して、以下での組織の ID とアクセス管理を統制します。

- Azure portal、Azure Storage、Azure Virtual Machines (Linux と Windows)、Azure Key Vault、サービスとしてのプラットフォーム (PaaS)、サービスとしてのソフトウェア (SaaS) アプリケーションなどの Microsoft Cloud リソース。
- Azure 上のアプリケーションや企業ネットワーク リソースなどの組織のリソース

組織のクラウド セキュリティ プラクティスの中で高い優先順位を持つ Azure AD をセキュリティ保護します。 Security Center のセキュリティ スコア機能を使用して ID とセキュリティ体制を評価し、構成が Microsoft のベスト プラクティスの推奨事項とどの程度一致しているかを判断します。 必要に応じて、Microsoft のベスト プラクティスの推奨事項を実装して、セキュリティ体制を強化します。

さらに、Azure AD では外部 ID もサポートされます。これにより、Microsoft アカウントを持たないユーザーが、外部 ID を使用してアプリケーションやリソースにサインインできるようになります。 

参照リンクで、ポイント対サイト VPN シナリオでの Azure AD の使用に関する情報を確認してください。

- [P2S OpenVPN プロトコル接続用の Azure Active Directory (AD) テナントを作成する](openvpn-azure-ad-tenant-multi-app.md)

- [ユーザー VPN 用に Azure Active Directory 認証を構成する](virtual-wan-point-to-site-azure-ad.md)

- [Azure Active Directory のテナント](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Azure AD インスタンスを作成して構成する方法](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4:すべての Azure Active Directory ベースのアクセスに強力な認証制御を使用する

**ガイダンス**:現時点では、Azure Active Directory (Azure AD) 認証は、Virtual WAN のポイント対サイト VPN との統合によって提供されます。

Azure Active Directory (Azure AD) は、Azure サービス向けの既定の ID およびアクセス管理サービスです。 Azure AD では、多要素認証および強力なパスワードレスの方法によって、強力な認証制御がサポートされています。

Azure AD では、強力な認証制御を実現するために、次のことが推奨されます。

- 多要素認証 - Azure AD の多要素認証を有効にし、セキュリティのベスト プラクティスに関する Azure Security Center の ID およびアクセス管理の推奨事項に従います。 多要素認証は、すべての選択されたユーザーに対して、またはサインインの条件とリスク要因に基づいてユーザー単位のレベルで適用します。

- パスワードレス認証 - 3 つのパスワードレス認証オプションを使用できます。 これらには、Windows Hello for Business、Microsoft Authenticator アプリ、スマート カードなどのオンプレミスの認証方法が含まれます。

管理者と特権ユーザーには、確実に最高レベルの強力な認証方法を使用し、その後、強力な認証ポリシーを他のユーザーにロールアウトします。

- [Virtual WAN の P2S VPN で MFA を有効にする方法](openvpn-azure-ad-mfa.md)

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6:条件に基づいて Azure リソースへのアクセスを制限する

**ガイダンス**:Azure AD 認証を使用して、VPN ユーザーの Azure Active Directory (Azure AD) 多要素認証を有効にします (ポイント対サイト)。 ユーザーごとに多要素認証を構成するか、条件付きアクセスを使用した多要素認証を利用します。 条件付きアクセスを使用すると、2番目の要素をどのように昇格するかをきめ細かく制御できます。 多要素認証を VPN のみに割り当て、Azure AD テナントに関連付けられている他のアプリケーションを除外することができます。 

Azure AD 認証は、OpenVPN プロトコルを使用するゲートウェイと、Windows を実行するクライアントでのみ使用できます。

- [条件付きアクセスとは](../active-directory/conditional-access/overview.md)

- [ユーザー VPN 用に Azure Active Directory 認証を構成する](virtual-wan-point-to-site-azure-ad.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7:意図しない資格情報の公開を排除する

**ガイダンス**:Virtual WAN のサイト間 VPN では、顧客が Azure Key Vault で検出、作成、管理する事前共有キー (PSK) を使用します。 コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

GitHub の場合、ネイティブ シークレット スキャン機能を使用して、コード内で資格情報やその他の形式のシークレットを識別できます。

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [GitHub シークレット スキャン](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="privileged-access"></a>特権アクセス

*詳細については、[Azure セキュリティ ベンチマークの「特権アクセス](../security/benchmarks/security-controls-v2-privileged-access.md)」を参照してください。*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2:ビジネス クリティカルなシステムへの管理アクセスを制限する

**ガイダンス**:Azure Virtual WAN では、Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、アカウントが属するサブスクリプションと管理グループへの特権アクセスを許可するアカウントを制限することにより、ビジネス クリティカルなシステムへのアクセスを分離します。

また、ビジネス クリティカルなシステムにインストールされたエージェントを使用して、Active Directory ドメイン コントローラー、セキュリティ ツール、システム管理ツールなど、ビジネス クリティカルなアクセスへの管理アクセス権を持つ管理、ID、およびセキュリティ システムへのアクセスも制限します。 これらの管理システムおよびセキュリティ システムを侵害した攻撃者は、それらをすぐに悪用してビジネス クリティカルな資産を侵害することができます。

一貫したアクセス制御を確保するため、自分の企業のセグメント化戦略にすべての種類のアクセス制御を合わせる必要があります。

- [Azure のコンポーネントと参照モデル](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [管理グループ アクセス](../governance/management-groups/overview.md#management-group-access) 

- [Azure サブスクリプション管理者](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-controls-v2-data-protection.md)」を参照してください。*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4:転送中の機密情報を暗号化する

**ガイダンス**:接続要件に応じて、Virtual WAN でポイント対サイト VPN、サイト間 VPN、暗号化された ExpressRoute を使用します。 VPN 暗号化では、"帯域外" 攻撃 (トラフィック キャプチャなど) に対して転送中のデータを保護し、攻撃者がデータの読み取りや変更を行うことができないようにします。 

- [ポイント対サイト VPN](virtual-wan-point-to-site-portal.md)

- [サイト間 VPN](virtual-wan-site-to-site-portal.md)

- [暗号化された ExpressRoute](vpn-over-expressroute.md)

**Azure Security Center の監視**: はい

**責任**: 共有

## <a name="asset-management"></a>アセット管理

*詳細については、[Azure セキュリティ ベンチマークの「アセット管理](../security/benchmarks/security-controls-v2-asset-management.md)」を参照してください。*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1:セキュリティ チームが資産のリスクを確実に可視化できるようにする

**ガイダンス**:セキュリティ チームに Azure テナントとサブスクリプションのセキュリティ閲覧者アクセス許可を付与して、セキュリティ チームが Azure Security Center を使用してセキュリティ上のリスクを監視できるようにします。 

セキュリティ チームの責任がどのように構造化されているかによって、セキュリティ リスクの監視は中央のセキュリティ チームまたはローカル チームの責任になります。 そのため、セキュリティ分析情報とリスクは、常に組織内で一元的に集計する必要があります。 

セキュリティ閲覧者のアクセス許可は、テナント全体 (ルート管理グループ) に幅広く適用することも、管理グループまたは特定のサブスクリプションにスコープ指定することもできます。 

注:ワークロードとサービスを可視化するには、追加のアクセス許可が必要になることがあります。 

- [セキュリティ閲覧者ロールの概要](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理グループの概要](../governance/management-groups/overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2:セキュリティ チームが資産インベントリとメタデータに確実にアクセスできるようにする

**ガイダンス**:Azure リソース、リソース グループ、サブスクリプションにタグを適用して、それらを論理的に分類できます。 各タグは、名前と値のペアで構成されます。 たとえば、運用環境のすべてのリソースには名前 "環境" と値 "運用" を適用できます。 Azure Virtual WAN は Azure Resource Manager ベースのリソースのデプロイもサポートするため、資産テンプレートのエクスポートもできます。 

- [リソースの名前付けとタグ付けの意思決定ガイド](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

- [Azure Security Center の資産インベントリ管理](../security-center/asset-inventory.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3:承認された Azure サービスのみを使用する

**ガイダンス**:Azure Monitor を使用して、承認されていないサービスが検出されたときにアラートをトリガーするルールを作成します。 Virtual WAN は、ネットワーク、セキュリティ、およびルーティングのさまざまな機能をまとめて、1 つの運用インターフェイスを提供します。 Virtual WAN VPN ゲートウェイ、ExpressRoute ゲートウェイ、および Azure Firewall には、Azure Monitor 経由で利用可能なログ記録とメトリックがあります。 
 

- [Virtual WAN のログとメトリック](logs-metrics.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**:"Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure 条件付きアクセスを使用します。

- [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="logging-and-threat-detection"></a>ログと脅威検出

*詳細については、[Azure セキュリティ ベンチマークの「ログと脅威検出](../security/benchmarks/security-controls-v2-logging-threat-detection.md)」を参照してください。*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1:Azure リソースの脅威検出を有効にする

**ガイダンス**:Virtual WAN を使用したポイント対サイト VPN は、Azure Active Directory (Azure AD) と統合されています。 Azure AD では次のユーザー ログが記録され、これは Azure AD レポートで確認できます。また、より高度な脅威監視と分析のユース ケースの場合は、Azure Monitor、Azure Sentinel、SIEM またはその他の監視ツールと統合できます。 次のとおりです。

- サインイン - サインイン レポートは、マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報を提供します。
- 監査ログ - Azure AD 内のさまざまな機能によって行われたすべての変更についてログによる追跡可能性を提供します。 監査ログの例として、ユーザー、アプリ、グループ、ロール、ポリシーの追加や削除など、Azure AD 内のあらゆるリソースに加えられた変更があります。
- リスクの高いサインイン - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によってサインインが試行された可能性があることを示す指標です。
- リスクのフラグ付きユーザー - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。

Azure Security Center を使用して、認証試行の失敗 (サブスクリプションでの非推奨のアカウントなど) 回数が多すぎるなど、特定の不審なアクティビティに対してアラートを生成することができます。 Security Center の脅威保護モジュールを使用すると、基本的なセキュリティ検疫監視に加えて、個々の Azure コンピューティング リソース (仮想マシン、コンテナー、アプリ サービスなど)、データ リソース (SQL DB、ストレージなど)、Azure サービス レイヤーから、さらに詳細なセキュリティ アラートを収集することもできます。 この機能を使用することにより、個々のリソース内でアカウントの異常を確認できます。

- [Azure Active Directory の監査アクティビティ レポート](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure Identity Protection を有効にする](../active-directory/identity-protection/overview-identity-protection.md)

- [仮想 WAN ハブ内で Azure Firewall を構成する](howto-firewall.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2:Azure ID とアクセスの管理のために脅威検出を有効にする

**ガイダンス**:Virtual WAN を使用したポイント対サイト VPN は、Azure Active Directory (Azure AD) と統合されています。 Azure AD では次のユーザー ログが記録され、これは Azure AD レポートで確認できます。また、より高度な脅威監視と分析のユース ケースの場合は、Azure Monitor、Azure Sentinel、SIEM またはその他の監視ツールと統合できます。 次のとおりです。

- サインイン - サインイン レポートは、マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報を提供します。
- 監査ログ - Azure AD 内のさまざまな機能によって行われたすべての変更についてログによる追跡可能性を提供します。 監査ログの例として、ユーザー、アプリ、グループ、ロール、ポリシーの追加や削除など、Azure AD 内のあらゆるリソースに加えられた変更があります。
- リスクの高いサインイン - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によってサインインが試行された可能性があることを示す指標です。
- リスクのフラグ付きユーザー - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。

Azure Security Center を使用して、認証試行の失敗 (サブスクリプションでの非推奨のアカウントなど) 回数が多すぎるなど、特定の不審なアクティビティに対してアラートを生成することができます。 Security Center の脅威保護モジュールを使用すると、基本的なセキュリティ検疫監視に加えて、個々の Azure コンピューティング リソース (仮想マシン、コンテナー、アプリ サービスなど)、データ リソース (SQL DB、ストレージなど)、Azure サービス レイヤーから、さらに詳細なセキュリティ アラートを収集することもできます。 この機能を使用することにより、個々のリソース内でアカウントの異常を確認できます。

- [Azure Active Directory の監査アクティビティ レポート](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure Identity Protection を有効にする](../active-directory/identity-protection/overview-identity-protection.md)

- [仮想 WAN ハブ内で Azure Firewall を構成する](howto-firewall.md)

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3:Azure ネットワーク アクティビティのログ記録を有効にする

**ガイダンス**:Azure Monitor を使用して Azure Virtual WAN を監視します。 Virtual WAN は、ネットワーク、セキュリティ、およびルーティングのさまざまな機能をまとめて、1 つの運用インターフェイスを提供します。 Virtual WAN VPN ゲートウェイ、ExpressRoute ゲートウェイ、および Azure Firewall には、Azure Monitor 経由で利用可能なログ記録とメトリックがあります。 アクティビティ ログ エントリは既定で収集され、また Azure portal に表示できます。 Azure アクティビティ ログ (以前は "操作ログ" と "監査ログ" と呼ばれていた) を使用して、Azure サブスクリプションに送信されるすべての操作を表示できます。

Virtual WAN ではさまざまな診断ログも使用でき、Azure portal でそれらを Virtual WAN リソース用に構成できます。  Log Analytics への送信、イベント ハブへのストリーム、またはストレージ アカウントへの単純なアーカイブから選ぶことができます。 
 

- [Virtual WAN のログとメトリック](logs-metrics.md)

- [Azure Firewall のログとメトリック](../firewall/logs-and-metrics.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4:Azure リソースのログ記録を有効にする

**ガイダンス**:自動的に有効になる Azure アクティビティ ログには、読み取り (GET) 操作を除いて、Azure Virtual WAN リソースに対するすべての書き込み操作 (PUT、POST、DELETE) が含まれます。 アクティビティ ログを使用すると、トラブルシューティング時にエラーを見つけたり、組織内のユーザーがリソースをどのように変更したかを監視したりすることができます。

Virtual WAN 用に Azure リソース ログを有効にします。 Azure Security Center と Azure Policy を使用して、リソース ログとログ データの収集を有効にできます。 これらのログは、後でセキュリティ インシデントを調査し、フォレンジック演習を実行するために重要な場合があります。

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure でのログ記録とログのさまざまな種類について](../azure-monitor/essentials/platform-logs-overview.md) 

- [Azure Security Center のデータ収集について](../security-center/security-center-enable-data-collection.md)

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5:セキュリティ ログの管理と分析を一元化する

**ガイダンス**:Azure Monitor で Virtual WAN のセキュリティ ログを有効にします。 Virtual WAN は、ネットワーク、セキュリティ、およびルーティングのさまざまな機能をまとめて、1 つの運用インターフェイスを提供します。 Virtual WAN VPN ゲートウェイ、ExpressRoute ゲートウェイ、および Azure Firewall には、Azure Monitor 経由で利用可能なログ記録とメトリックがあります。 アクティビティ ログ エントリは既定で収集され、また Azure portal に表示できます。 Azure アクティビティ ログ (以前は "操作ログ" と "監査ログ" と呼ばれていた) を使用して、Azure サブスクリプションに送信されるすべての操作を表示できます。 

Virtual WAN ではさまざまな診断ログも使用でき、Azure portal でそれらを Virtual WAN リソース用に構成できます。 Log Analytics への送信、イベント ハブへのストリーム、またはストレージ アカウントへの単純なアーカイブを行います。 さらに、Azure Sentinel またはサードパーティのセキュリティ情報イベント管理ソリューションに対してデータを有効にしてオンボードします。 
 

- [Virtual WAN のログとメトリック](logs-metrics.md)

- [Azure Firewall のログとメトリック](../firewall/logs-and-metrics.md)

Azure Virtual WAN のセキュリティは、Azure Firewall を通じて提供されます。 

- [Azure Firewall のドキュメント](../firewall/overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="lt-6-configure-log-storage-retention"></a>LT-6:ログの保持期間を構成する

**ガイダンス**:コンプライアンス、規制、ビジネスの要件に従って、ログの保持期間を構成します。 Azure Monitor で、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定できます。 長期ストレージおよびアーカイブ ストレージには、Azure Storage、Data Lake、または Log Analytics ワークスペースのアカウントを使用します。

- [Log Analytics でデータ保持期間を変更する](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Azure Storage アカウント ログの保持ポリシーを構成する方法](../storage/common/manage-storage-analytics-logs.md#configure-logging)

- [Azure Security Center のアラートと推奨事項のエクスポート](../security-center/continuous-export.md)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

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

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: 定期的に攻撃シミュレーションを実施する

**ガイダンス**:必要に応じて、Azure リソースの侵入テストまたはレッド チーム アクティビティを実施し、セキュリティに関するすべての重大な調査結果が確実に修復されるようにします。
お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft クラウド侵入テストの実施ルールに従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。

- [Azure での侵入テスト](../security/fundamentals/pen-testing.md)

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="endpoint-security"></a>エンドポイント セキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「エンドポイントのセキュリティ](../security/benchmarks/security-controls-v2-endpoint-security.md)」を参照してください。*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1:エンドポイントでの検出と対応 (EDR) を使用する

**ガイダンス**:顧客に対して、[エンドポイントの検出と応答] 設定の構成を明示的に許可しません。 しかし、Azure Virtual WAN オファリングで使用される Virtual Machines では、これらの機能を使用します。 これらの一般的な機能の詳細については、参照リンクを参照してください。 

- [Microsoft Defender Advanced Threat Protection の概要](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Windows サーバー向けの Microsoft Defender ATP サービス](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Windows 以外のサーバー向けの Microsoft Defender ATP サービス](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Azure Security Center の監視**: はい

**責任**: 共有

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

- [Azure セキュリティ ベンチマーク - 特権アクセス](../security/benchmarks//security-controls-v2-privileged-access.md)

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
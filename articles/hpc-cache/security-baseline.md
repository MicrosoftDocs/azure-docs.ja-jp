---
title: Azure HPC Cache 用の Azure セキュリティ ベースライン
description: Azure HPC Cache セキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e5a395f71ed1b1ab06d97b8aa6d83278d59d15c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101091954"
---
# <a name="azure-security-baseline-for-azure-hpc-cache"></a>Azure HPC Cache 用の Azure セキュリティ ベースライン

このセキュリティ ベースラインによって、[Azure セキュリティ ベンチマーク バージョン 2.0](../security/benchmarks/overview.md) のガイダンスが Microsoft Azure HPC Cache に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 内容は、**セキュリティ制御** によってグループ化されています。これは、Azure セキュリティ ベンチマークと、Azure HPC Cache に適用できる関連ガイダンスによって定義されています。 Azure HPC Cache に適用できない **制御** は、除外されています。

Azure HPC Cache を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Azure HPC Cache セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-controls-v2-network-security.md)」を参照してください。*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: 内部トラフィック用のセキュリティを実装する

**ガイダンス**:Azure HPC Cache リソースをデプロイする場合は、既存の仮想ネットワークを作成または使用する必要があります。 

すべての Azure 仮想ネットワークが、ビジネス リスクに合わせたエンタープライズ セグメント化の原則に従っていることを確認します。 組織のリスクが高くなる可能性があるシステムは、独自の仮想ネットワーク内に隔離し、ネットワーク セキュリティ グループ (NSG) または Azure Firewall で十分に保護する必要があります。

キャッシュを使用するには、ネットワークに関連した次の 2 つの前提条件を事前に設定しておく必要があります。 

- Azure HPC Cache インスタンスの専用サブネット

- DNS サポート (キャッシュがストレージや他のリソースにアクセスできるようにするため)

Azure HPC Cache には、次の条件を満たした専用のサブネットが必要です。 

- サブネットには、使用できる IP アドレスが少なくとも 64 個必要です。

- そのサブネットで他の VM をホストすることはできません。クライアント マシンなど、関連サービス用の VM であっても同様です。

- 複数の Azure HPC Cache インスタンスを使用する場合、各インスタンスに独自のサブネットが必要です。

ベスト プラクティスとして、キャッシュごとに新しいサブネットを作成することをお勧めします。 キャッシュを作成する過程で新しい仮想ネットワークとサブネットを作成できます。

オンプレミスの NAS ストレージで HPC Cache を使用するには、オンプレミス ネットワーク内の特定のポートで、Azure HPC Cache のサブネットからの制限されていないトラフィックを許可する必要があります。 

- [NFS ストレージ アクセス用のポートを構成する方法](hpc-cache-prerequisites.md#nfs-storage-requirements)

- [セキュリティ規則を使用してネットワーク セキュリティ グループを作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Azure Firewall をデプロイして構成する方法](../firewall/tutorial-firewall-deploy-portal.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="ns-2-connect-private-networks-together"></a>NS-2: プライベート ネットワークをまとめて接続する

**ガイダンス**:Azure ExpressRoute または Azure 仮想プライベート ネットワーク (VPN) を使用して、コロケーション環境内で Azure のデータセンターとオンプレミスのインフラストラクチャ間のプライベート接続を作成できます。 ExpressRoute 接続はパブリック インターネットを経由しないので、一般的なインターネット接続と比べて信頼性が高く、より高速で、待ち時間も短くなります。 ポイント対サイト VPN とサイト間 VPN では、これらの VPN オプションと Azure ExpressRoute の任意の組み合わせを使用して、オンプレミスのデバイスやネットワークを仮想ネットワークに接続できます。 

Azure で 2 つ以上の仮想ネットワークを接続するには、仮想ネットワーク ピアリングを使用します。 ピアリングされた仮想ネットワーク間のネットワーク トラフィックはプライベートであり、Azure のバックボーン ネットワーク上に保持されます。

- [ExpressRoute 接続モデルとは](../expressroute/expressroute-connectivity-models.md) 

- [Azure VPN の概要](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Azure サービスへのプライベート ネットワーク アクセスを確立する

**ガイダンス**:Azure Virtual Network サービス エンドポイントを使用して、HPC Cache へのセキュリティで保護されたアクセスを提供します。 サービス エンドポイントは、インターネットを経由しない、Azure のバックボーン ネットワークを介した最適化されたルートです。 

HPC Cache は、Azure Private Link を使用して、管理エンドポイントをプライベート ネットワークにセキュリティで保護することをサポートしていません。 

プライベート アクセスは、Azure サービスによって提供される認証およびトラフィックのセキュリティに加えて、追加の多層防御手段となります。

- [仮想ネットワーク サービス エンドポイントについて](../virtual-network/virtual-network-service-endpoints-overview.md) 

- [Azure HPC Cache をマウントする方法を理解する](hpc-cache-mount.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: 外部ネットワーク攻撃からアプリケーションやサービスを保護する

**ガイダンス**:分散型サービス拒否 (DDoS) 攻撃、アプリケーション固有の攻撃、未承諾で悪意のある可能性のあるインターネット トラフィックなど、外部ネットワークからの攻撃に対して HPC Cache リソースを保護します。 

Azure には、この保護に対するネイティブ機能が含まれています。 

- Azure Firewall を使用して、インターネットや他の外部の場所からの、悪意がある可能性のあるトラフィックから、アプリケーションやサービスを保護します。 
- Azure 仮想ネットワークで DDoS 標準保護を有効にすることで、DDoS 攻撃から資産を保護します。 
- Azure Security Center を使用して、ネットワーク リソースに関する構成ミスのリスクを検出します。

Azure HPC Cache は Web アプリケーションを実行するためのものではなく、Web アプリケーションを対象とする外部ネットワーク攻撃からこれを保護するために追加で設定を構成したり、追加のネットワーク サービスを配置したりする必要はありません。

- [Azure Firewall のドキュメント](../firewall/index.yml) 

- [Azure portal を使用した Azure DDoS Protection Standard の管理](../ddos-protection/manage-ddos-protection.md) 

- [Azure Security Center の推奨事項](../security-center/recommendations-reference.md#recs-networking)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: 侵入検出および侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**:Azure Firewall で脅威インテリジェンス ベースのフィルター処理を使用して、既知の悪意のある IP アドレスおよびドメインとの間のトラフィックに対してアラートを送信したりブロックしたりします。 この IP アドレスとドメインのソースは、Microsoft の脅威インテリジェンス フィードです。 

ペイロード検査が必要な場合は、ペイロード検査機能を備えたサードパーティの侵入検出および侵入防止システム (IDS/IPS) ソリューションを、Azure Marketplace からデプロイできます。 または、ネットワーク ベースの IDS/IPS と組み合わせて、またはその代わりに、ホスト ベースの IDS/IPS またはホスト ベースのエンドポイント検出と応答 (EDR) ソリューションを使用することも選択できます。

注:IDS/IPS の使用に関して規制やその他の要件がある場合は、SIEM ソリューションに高品質のアラートが提供されるように、常に調整されていることを確認します。

- [Azure Firewall をデプロイする方法l](../firewall/tutorial-firewall-deploy-portal.md) 

- [Azure Marketplace のサードパーティの IDS 機能](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [Microsoft Defender の ATP EDR 機能](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: ネットワーク セキュリティ規則を簡略化する

**ガイダンス**:適用なし。この推奨事項は、Azure 仮想ネットワークにデプロイできる、または効率的な管理のために許可された IP 範囲のグループを定義する機能を備えたサービスを対象としています。 現在、HPC Cache ではサービス タグはサポートされていません。 

ベスト プラクティスとして、キャッシュごとに新しいサブネットを作成することをお勧めします。 キャッシュを作成する過程で新しい仮想ネットワークとサブネットを作成できます。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: セキュリティで保護されたドメイン ネーム サービス (DNS)

**ガイダンス**:DNS セキュリティのベスト プラクティスに従って、未解決の DNS、DNS アンプ攻撃、DNS のポイズニングとスプーフィングなどの一般的な攻撃を軽減します。

Azure HPC Cache がキャッシュのプライベート仮想ネットワークの外部のリソースにアクセスするためには DNS が必要となります。 ワークフローに Azure 外部のリソースが含まれている場合は、Azure DNS を使用するだけでなく、独自の DNS サーバーを設定してセキュリティで保護する必要があります。

- Azure Blob Storage のエンドポイント、Azure ベースのクライアント マシン、またはその他の Azure リソースにアクセスするには、Azure DNS を使用します。
- オンプレミスのストレージにアクセスしたり、Azure の外部のクライアントからキャッシュに接続したりするには、これらのホスト名を解決できるカスタム DNS サーバーを作成する必要があります。
- ワークフローに内部リソースと外部リソースの両方が含まれている場合は、Azure 固有の解決要求を Azure DNS サーバーに転送するようにカスタム DNS サーバーを設定します。 

権限のある DNS サービスとして Azure DNS が使用されている場合は、Azure RBAC とリソース ロックを使用して、DNS ゾーンとレコードが偶発的または悪意のある変更から保護されていることを確認します。

独自の DNS サーバーを構成する場合は、次のセキュリティ ガイドラインに従ってください。

- [セキュリティで保護されたドメイン ネーム システム (DNS) デプロイ ガイド](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) 

- [未解決の DNS エントリを防ぎ、サブドメインの乗っ取りを回避する](../security/fundamentals/subdomain-takeover.md) 

- [HPC Cache の DNS アクセス要件の詳細について確認する](hpc-cache-prerequisites.md#dns-access)

- [Azure DNS の概要](../dns/dns-overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="identity-management"></a>ID 管理

*詳細については、[Azure セキュリティ ベンチマークの「ID 管理](../security/benchmarks/security-controls-v2-identity-management.md).* 」を参照してください。

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1:Azure Active Directory を中央 ID および認証システムとして標準化する

**ガイダンス**:Azure HPC Cache は、内部操作用に Azure Active Directory と統合されません。 ただし、Azure AD を使用して、HPC Cache のデプロイと関連コンポーネントを作成、表示、および管理するために、Azure portal または CLI でユーザーを認証することができます。

Azure Active Directory (Azure AD) は、Azure の既定の ID およびアクセス管理サービスです。 Azure AD を標準化して、次のリソースでの組織の ID とアクセス管理を統制する必要があります。

- Azure portal、Azure Storage、Azure 仮想マシン (Linux と Windows)、Azure Key Vault、PaaS、SaaS アプリケーションなどの Microsoft Cloud リソース。

- Azure 上のアプリケーションや企業ネットワーク リソースなどの組織のリソース。

Azure AD を保護することは、組織のクラウド セキュリティ プラクティスの中で高い優先順位を持つ必要があります。 Azure AD では、Microsoft のベスト プラクティスの推奨事項を基準にした、お客様の ID セキュリティ体制を評価するために役立つ ID セキュリティ スコアが提供されます。 スコアを使用して、構成がベスト プラクティスの推奨事項とどの程度一致しているかを測定し、セキュリティ体制を強化します。

注:Azure AD では、外部 ID がサポートされます。これにより、Microsoft アカウントを持たないユーザーが、外部 ID を使用してアプリケーションやリソースにサインインできるようになります。

- [Azure Active Directory のテナント](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Azure AD インスタンスを作成して構成する方法](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [アプリケーションに外部 ID プロバイダーを使用する](../active-directory/external-identities/identity-providers.md) 

- [Azure Active Directory の ID セキュリティ スコアとは](../active-directory/fundamentals/identity-secure-score.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2:アプリケーション ID を安全かつ自動的に管理する

**ガイダンス**:HPC Cache では、サービスや自動化などの人間以外のアカウントには、Azure マネージド ID が使用されます。 リソースにアクセスしたり実行したりするためのより強力な人間のアカウントを作成するのではなく、Azure のマネージド ID 機能を使用することをお勧めします。 

HPC Cache は、事前に定義されたアクセス許可規則によって Azure AD 認証をサポートする Azure サービスまたはリソースに対してネイティブに認証できます。 これにより、ソース コードまたは構成ファイルでハードコーディングされた資格情報を使用する必要がなくなります。

- [Azure マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) 

- [Azure リソースのマネージド ID をサポートするサービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3:アプリケーションのアクセスに Azure AD シングル サインオン (SSO) を使用する

**ガイダンス**:Azure HPC Cache は、内部操作用に Azure AD と統合されていません。 ただし、Azure AD を使用して、HPC Cache のデプロイと関連コンポーネントを作成、表示、および管理するために、Azure portal または CLI でユーザーを認証することができます。

Azure Active Directory により、Azure リソース、クラウド アプリケーション、オンプレミス アプリケーションに対する ID とアクセスの管理が提供されます。 これには、従業員などの企業 ID だけでなく、パートナー、ベンダー、サプライヤーなどの外部 ID も含まれます。 これにより、オンプレミスおよびクラウド内の組織のデータとリソースへのアクセスを管理し、セキュリティで保護するためのシングルサインオン (SSO) が可能になります。 すべてのユーザー、アプリケーション、デバイスを Azure AD に接続することで、シームレスで安全なアクセスを実現し、可視性と制御性を高めることができます。

- [Azure AD を使用したアプリケーションの SSO について理解する](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4:すべての Azure Active Directory ベースのアクセスに強力な認証制御を使用する

**ガイダンス**:Azure HPC Cache は、内部操作用に Azure AD と統合されていませんが、Azure AD を使用して、HPC Cache のデプロイと関連コンポーネントを作成、表示、および管理するために、Azure portal または CLI でユーザーを認証することができます。  

Azure AD では、多要素認証 (MFA)、および強力なパスワードレスの方法によって、強力な認証制御がサポートされています。

- 多要素認証:Azure AD MFA を有効にし、Azure Security Center の ID とアクセス管理の推奨事項に従って、MFA 構成のベスト プラクティスを導入します。 MFA は、サインインの条件とリスク要因に基づいて、すべてのユーザー、選択されたユーザー、またはユーザー単位のレベルで適用できます。
- パスワードレス認証 - 3 つのパスワードレス認証オプションを使用できます。Windows Hello for Business、Microsoft Authenticator アプリ、スマート カードなどのオンプレミスの認証方法。

管理者と特権のあるユーザーの場合は、強力な認証方法の最高レベルを使用してください。 適切かつ強力な認証ポリシーを他のユーザーにロールアウトします。

Azure HPC Cache では、キャッシュに接続するクライアント システムでの従来のパスワードベースの認証もサポートされています。 これらの種類の接続には、ベースライン パスワード ポリシーに従うクラウド専用アカウント (Azure で直接作成されたユーザー アカウント) や、オンプレミスのパスワード ポリシーに従うハイブリッド アカウント (オンプレミスの Active Directory から移行されたユーザー アカウント) などがあります。 

パスワード ベースの認証を使用すると、Azure AD により、ユーザーが推測されやすいパスワードを設定できないようにするパスワード保護機能が提供されます。 Microsoft では、テレメトリに基づいて更新される禁止されたパスワードのグローバル リストが提供されており、顧客はニーズ (例えば、ブランド、文化の参照など) に基づいてリストを補強することができます。 このパスワード保護は、クラウド専用アカウントとハイブリッド アカウントに使用できます。

注:パスワード資格情報だけに基づく認証は、一般的な攻撃方法の影響を受けやすいものです。 セキュリティを強化するには、MFA や強力なパスワード ポリシーなどの強力な認証を使用します。 既定のパスワードを持つサードパーティ製のアプリケーションとマーケットプレース サービスを使用する場合は、サービスを初めて設定するときに、セキュリティで保護された新しいパスワードを設定します。 

- [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Active Directory のパスワードレス認証オプションの概要](../active-directory/authentication/concept-authentication-passwordless.md)

- [Azure AD の規定のパスワード ポリシー](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Azure AD のパスワード保護を使用して不適切なパスワードを排除する](../active-directory/authentication/concept-password-ban-bad.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5:アカウントの異常を監視してアラートを出す

**ガイダンス**:Azure HPC Cache では、Azure portal からのユーザー管理に Azure Active Directory を使用できます。  Azure Active Directory には、次のデータ ソースが用意されています。

- サインイン - サインイン レポートでは、マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報が得られます。

- 監査ログ - Azure AD 内のさまざまな機能によって行われたすべての変更についてログによる追跡可能性を提供します。 監査ログの例として、ユーザー、アプリ、グループ、ロール、ポリシーの追加や削除など、Azure AD 内のあらゆるリソースに加えられた変更があります。

- リスクの高いサインイン - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。

- リスクのフラグ付きユーザー - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。

これらのデータ ソースは、Azure Monitor、Azure Sentinel、またはサードパーティの SIEM システムと統合できます。

Azure Security Center で、認証試行の失敗回数が多すぎるなど、サブスクリプションにおける特定の不審なアクティビティや、使用すべきでないアカウントに対してアラートを生成することもできます。

セキュリティ ソリューションである Azure Advanced Threat Protection (ATP) では、Active Directory シグナルを使用することで、高度な脅威、セキュリティ侵害を受けた ID、および悪意のある内部関係者のアクションを特定、検出、および調査できます。

- [Azure Active Directory の監査アクティビティ レポート](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md) 

- [危険なアクティビティのフラグが設定された Azure AD ユーザーを識別する方法](../active-directory/identity-protection/overview-identity-protection.md) 

- [Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法](../security-center/security-center-identity-access.md)

 

- [Azure Security Center の脅威インテリジェンス保護モジュールでのアラート](../security-center/alerts-reference.md) 

- [Azure アクティビティ ログを Azure Monitor に統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7:意図しない資格情報の公開を排除する

**ガイダンス**:適用なし。HPC Cache では、実行中の環境に永続化されたデータを配置することはできません。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="privileged-access"></a>特権アクセス

*詳細については、[Azure セキュリティ ベンチマークの「特権アクセス](../security/benchmarks/security-controls-v2-privileged-access.md)」を参照してください。*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2:ビジネス クリティカルなシステムへの管理アクセスを制限する

**ガイダンス**:HPC Cache は、アカウントが属するサブスクリプションと管理グループへの特権アクセスを許可するアカウントを制限することによって、Azure RBAC を使用して、ビジネス クリティカルなシステムへのアクセスを分離します。

専用管理者アカウントの使用に関する標準的な操作手順を作成します。 キャッシュを作成する場合、HPC Cache では、ユーザーが NIC を作成できるだけの権限をサブスクリプションにおいて有している必要があります。 BLOB ストレージを使用している場合、HPC Cache でストレージにアクセスするには、Azure ロールのストレージ アカウント共同作成者とストレージ BLOB データ共同作成者が必要です。 

また、ビジネス クリティカルなシステムにインストールされたエージェントを使用して、Active Directory ドメイン コントローラー (DC)、セキュリティ ツール、システム管理ツールなど、ビジネスクリティカルな資産への管理アクセス権を持つ管理、ID、およびセキュリティ システムへのアクセスも制限します。 これらの管理システムおよびセキュリティ システムを侵害した攻撃者は、それらをすぐに悪用してビジネスクリティカルな資産を侵害することができます。

一貫したアクセス制御を確保するため、自分の企業のセグメント化戦略にすべての種類のアクセス制御を合わせる必要があります。

- [Azure HPC Cache の RBAC アクセス許可](hpc-cache-prerequisites.md#permissions)

- [Azure のコンポーネントと参照モデル](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [管理グループ アクセス](../governance/management-groups/overview.md#management-group-access)

- [Azure サブスクリプション管理者](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3:ユーザー アクセスを定期的に確認して調整する

**ガイダンス**:ユーザー アカウントとアクセスの割り当てを定期的に確認して、アカウントとそのアクセス レベルが有効であることを確実にします。 

Azure HPC Cache では、Azure Active Directory (Azure AD) アカウントを使用して、Azure portal および関連するインターフェイスからユーザー アクセスを管理できます。 Azure AD では、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てのレビューに役立つアクセス レビューが使用できます。 Azure AD レポートによって、古いアカウントの検出に役立つログが提供されます。 また、Azure AD Privileged Identity Management を使用してアクセス レビュー レポート ワークフローを作成し、レビュー プロセスを容易にすることもできます。

また、Azure Privileged Identity Management を構成して、過剰な数の管理者アカウントが作成されたらアラートを生成したり、古くなったり不適切に構成されている管理者アカウントを識別したりできます。

注:一部の Azure サービスでは、Azure AD で管理されていないローカル ユーザーとロールがサポートされています。 これらのユーザーは個別に管理する必要があります。

NFS ストレージ ターゲットを使用する場合は、ネットワーク管理者とファイアウォール マネージャーと連携してアクセス設定を確認し、Azure HPC Cache が NFS ストレージ システムと通信できることを確認する必要があります。

- [HPC Cache のアクセス許可の一覧については、Azure HPC Cache の前提条件に関する記事を参照してください](hpc-cache-prerequisites.md) 

- [Privileged Identity Management (PIM) で Azure リソース ロールのアクセス レビューを作成する](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Azure AD の ID およびアクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4:Azure AD で緊急アクセスを設定する

**ガイダンス**:HPC Cache では、Azure Active Directory を使用して、Azure portal 経由でリソースへのアクセスを管理できます。 Azure AD 組織から誤ってロックアウトされるのを防ぐために、通常の管理者アカウントを使用できない場合にアクセスするための緊急アクセス用アカウントを設定します。 緊急アクセス用アカウントは高い特権を持っており、特定の個人に割り当てることはできません。 緊急アクセス用アカウントは、通常の管理者アカウントを使うことができない "緊急事態" に制限されます。

緊急アクセス用アカウントの資格情報 (パスワード、証明書、スマート カードなど) は安全に保管し、緊急時にのみそれらを使うことを許可された個人のみに知らせる必要があります。

- [Azure AD で緊急アクセス用アカウントを管理する](../active-directory/roles/security-emergency-access.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="pa-5-automate-entitlement-management"></a>PA-5:エンタイトルメント管理を自動化する 

**ガイダンス**:HPC Cache では、Azure Active Directory を使用して、Azure portal 経由でキャッシュ リソースへのアクセスを管理できます。 

Azure AD のエンタイトルメント管理機能を使用して、アクセスの割り当て、レビュー、有効期限など、アクセス要求のワークフローを自動化します。 2 段階または複数段階の承認もサポートされています。 

- [Azure AD アクセス レビューとは](../active-directory/governance/access-reviews-overview.md) 

- [Azure AD エンタイトルメント管理とは](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7:Just Enough Administration (最小限の特権の原則) に従う 

**ガイダンス**:HPC Cache は、Azure ロールベースのアクセス制御 (RBAC) との統合により、そのリソースを管理します。 Azure RBAC を使用すると、ロールの割り当てによって Azure リソースへのアクセスを管理できます。 これらのロールを、ユーザー、グループ サービス プリンシパル、およびマネージド ID に割り当てることができます。 特定のリソースに対して定義済みの組み込みロールがあります。これらのロールは、Azure CLI、Azure PowerShell、Azure portal などのツールを使用してインベントリまたは照会できます。 

Azure RBAC を使用してリソースに割り当てる特権は、常に、ロールで必要なものに制限する必要があります。 これは、Azure AD Privileged Identity Management (PIM) の Just-in-Time (JIT) アプローチを補完するものであり、定期的に見直す必要があります。

組み込みのロールを使用してアクセス許可を割り当て、カスタム ロールは必要な場合にのみ作成します。

- [Azure ロールベースのアクセス制御 (Azure RBAC) とは](../role-based-access-control/overview.md) 

- [Azure RBAC を構成する方法](../role-based-access-control/role-assignments-portal.md) 

- [Azure AD の ID およびアクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-controls-v2-data-protection.md)」を参照してください。*

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1:機密データを検出、分類、ラベル付けする 

**ガイダンス**:HPC Cache では機微なデータの管理を行いますが、機密データの検出、分類、ラベル付けの機能はありません。

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="dp-2-protect-sensitive-data"></a>DP-2:機密データを保護する

**ガイダンス**:機密データを保護するには、Azure のロール ベースのアクセス制御 (Azure RBAC)、ネットワーク ベースのアクセス制御、(SQL などのデータベースでの暗号化など) Azure サービスの特定の制御を使用してアクセスを制限します。

一貫したアクセス制御を確保するには、自分の企業のセグメント化戦略にすべての種類のアクセス制御を合わせる必要があります。 企業のセグメント化戦略では、機密またはビジネスに重要なデータやシステムの場所からも通知される必要があります。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft では顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護します。 Microsoft では、Azure 内の顧客データが確実にセキュリティで保護されるように、データ保護コントロールおよび機能をいくつか実装しています。

- [Azure のロールベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md) 

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3:機密データの不正転送を監視する

**ガイダンス**:HPC Cache は機密データを転送しますが、機密データの不正な転送の監視はサポートしていません。

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4:転送中の機密情報を暗号化する

**ガイダンス**:HPC Cache は、TLS v1.2 以上で転送中のデータの暗号化をサポートしています。

これはプライベート ネットワーク上のトラフィックでは省略できますが、外部ネットワークとパブリック ネットワーク上のトラフィックには重要です。 ご自分の Azure リソースに接続するすべてのクライアントの HTTP トラフィックのネゴシエートには、確実に TLS v1.2 以上を使用してください。 リモート管理には、暗号化されていないプロトコルではなく、(Linux の場合) SSH または (Windows の場合) RDP/TLS を使用します。 SSL、TLS、SSH の古いバージョンとプロトコル、および弱い暗号は無効にする必要があります。

既定では Azure によって、Azure のデータ センター間の転送データが暗号化されます。

- [Azure での転送中の暗号化の概要](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [TLS セキュリティに関する情報](/security/engineering/solving-tls1-problem) 

- [転送中の Azure データの二重暗号化](../security/fundamentals/double-encryption.md#data-in-transit)

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5:保存データを暗号化する

**ガイダンス**:保存データを "帯域外" 攻撃 (例えば、基になっているストレージへのアクセスなど) から保護するには、アクセス制御を補完する暗号化を使用する必要があります。 これにより、攻撃者がデータを簡単に読み取ったり変更したりすることが確実にできなくなります。

Azure では、既定で保存データが暗号化されます。 機密性の高いデータには、保存されている利用可能なすべての Azure リソースに、オプションで暗号化を追加できます。 既定では Azure によってお使いの暗号化キーが管理されますが、特定の Azure サービスに対して、お使いの独自のキー (カスタマー マネージド キー) が管理するオプションが Azure により提供されています。

Azure でキャッシュ ディスクなどに格納されているすべてのデータは、既定で Microsoft のマネージド キーを使用して保存時に暗号化されます。 Azure HPC Cache の設定をカスタマイズするだけで、データの暗号化に使用するキーを管理することができます。

コンピューティング リソースでの準拠に必要な場合は、ホストベースの自動データ損失防止ソリューションなどのサードパーティ ツールを実装して、データがシステムからコピーされた場合でもデータへのアクセス制御を実施します。

- [Azure HPC Cache でカスタマー マネージド暗号化キーを使用する方法](./hpc-cache-create.md?tabs=azure-portal#enable-azure-key-vault-encryption-optional)

- [Azure での保存時の暗号化の概要](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [カスタマー マネージド暗号化キーを構成する方法](../storage/common/customer-managed-keys-configure-key-vault.md) 

- [暗号化モデルとキー管理テーブル](../security/fundamentals/encryption-atrest.md)

 

- [Azure における保存データの二重暗号化](../security/fundamentals/double-encryption.md#data-at-rest)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

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

**ガイダンス**:Azure HPC Cache でタグの使用がサポートされています。 Azure リソース、リソース グループ、サブスクリプションにタグを適用して、それらを論理的に分類できます。 各タグは、名前と値のペアで構成されます。 

たとえば、運用環境のすべてのリソースには名前 "環境" と値 "運用" を適用できます。 キャッシュの作成時だけでなく、キャッシュのデプロイ後にもタグを追加することができます。 

Azure 仮想マシンのインベントリを使用して、Virtual Machines 上のソフトウェアに関する情報の収集を自動化します。 ソフトウェアの名前、バージョン、発行元、および更新時刻は、Azure portal から入手できます。 インストール日やその他の情報にアクセスするには、ゲストレベルの診断を有効にし、Windows イベント ログを Log Analytics ワークスペースに取り込みます。
HPC Cache のリソースでは、アプリケーションを実行することも、ソフトウェアをインストールすることもできません。 

- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center の資産インベントリ管理](../security-center/asset-inventory.md) 

- [リソースの名前付けとタグ付けの意思決定ガイド](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json) 

- [Azure 仮想マシンのインベントリを有効にする方法](../automation/automation-tutorial-installed-software.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3:承認された Azure サービスのみを使用する

**ガイダンス**:HPC Cache では Azure Resource Manager のデプロイがサポートされています。 Azure Policy を使用して、環境内でユーザーがプロビジョニングできるサービスを監査および制限します。 Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。 また、Azure Monitor を使用して、承認されていないサービスが検出されたときにアラートをトリガーするルールを作成することもできます。

- [Azure Policy を構成して管理する](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](../governance/policy/samples/index.md) 

- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4:アセット ライフサイクル管理のセキュリティを確保する

**ガイダンス**: 適用不可。 ライフサイクル管理プロセスの資産のセキュリティを確保するために Azure HPC Cache を使用することはできません。 影響が大きいと見なされる資産の属性とネットワーク構成を維持することは、お客様の責任です。 

お客様にて、属性とネットワーク構成の変更をキャプチャするプロセスを作成し、変更の影響を測定し、必要に応じて修復タスクを作成することをお勧めします。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="logging-and-threat-detection"></a>ログと脅威検出

*詳細については、[Azure セキュリティ ベンチマークの「ログと脅威検出](../security/benchmarks/security-controls-v2-logging-threat-detection.md)」を参照してください。*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1:Azure リソースの脅威検出を有効にする

**ガイダンス**:Azure Security Center の組み込みの脅威検出機能を使用して、HPC Cache リソースに対して Azure Defender (正式名: Azure Advanced Threat Protection) を有効にします。 Azure Defender for HPC Cache は、キャッシュ リソースに対して通常と異なる潜在的に有害なアクセスまたは悪用が試行されたことを検出するセキュリティ インテリジェンスの追加レイヤーを提供します。

HPC Cache から SIEM に、カスタム脅威検出を設定するために使用できるログを転送します。 潜在的な脅威や異常を検出するために、さまざまな種類の Azure 資産を監視していることを確認してください。 アナリストが選別しやすいように、質の高いアラートを取得して誤検知を減らすことに専念します。 アラートは、ログ データ、エージェント、その他のデータを元に生成できます。

- [Azure Security Center での脅威の防止](../security-center/azure-defender.md) 

- [Azure Security Center セキュリティ アラート リファレンス ガイド](../security-center/alerts-reference.md) 

- [脅威を検出するためのカスタム分析規則を作成する](../sentinel/tutorial-detect-threats-custom.md) 

- [Azure Sentinel を使用したサイバー脅威インテリジェンス](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2:Azure ID とアクセスの管理のために脅威検出を有効にする

**ガイダンス**:Azure AD では、次のユーザー ログが記録され、これは Azure AD レポートで確認できます。また、より高度な監視と分析のユース ケースの場合は、Azure Monitor、Azure Sentinel、またはその他の SIEM/監視ツールと統合できます。
- サインイン - サインイン レポートは、マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報を提供します。

- 監査ログ - Azure AD 内のさまざまな機能によって実行されたすべての変更のログを通して追跡可能性を提供します。 監査ログの例として、ユーザー、アプリ、グループ、ロール、ポリシーの追加や削除など、Azure AD 内のあらゆるリソースに加えられた変更があります。

- リスクの高いサインイン - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。

- リスクのフラグ付きユーザー - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。

Azure Security Center で、認証試行の失敗回数が多すぎるなど、サブスクリプションにおける特定の不審なアクティビティや、非推奨のアカウントに対してアラートを生成することもできます。 Azure Security Center の脅威保護モジュールでは、基本的なセキュリティ検疫監視に加えて、個々の Azure コンピューティング リソース (仮想マシン、コンテナー、アプリ サービス)、データ リソース (SQL DB、ストレージ)、Azure サービス レイヤーから、さらに詳細なセキュリティ アラートを収集することもできます。 この機能を使用することにより、個々のリソース内でアカウントの異常を確認できます。

- [Azure Active Directory の監査アクティビティ レポート](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure Identity Protection を有効にする](../active-directory/identity-protection/overview-identity-protection.md) 

- [Azure Security Center での脅威の防止](../security-center/azure-defender.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3:Azure ネットワーク アクティビティのログ記録を有効にする

**ガイダンス**:仮想ネットワーク間を移動するネットワーク パケットを記録するための一般的なパケット キャプチャツールに加えて、VPN ゲートウェイとそのパケット キャプチャ機能を使用することができます。

Azure HPC Cache リソースがデプロイされているネットワーク上にネットワーク セキュリティ グループをデプロイします。 トラフィックの監査のために、ネットワーク セキュリティ グループでネットワーク セキュリティ グループのフロー ログを有効にします。

フロー ログは、ストレージ アカウントに保持されます。 Traffic Analytics ソリューションを有効にして、それらのフロー ログを処理し、Log Analytics ワークスペースに送信します。 Traffic Analytics により、Azure ネットワークのトラフィック フローに関する追加の分析情報が提供されます。 Traffic Analytics を使用することで、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりすることができます。

キャッシュからその仮想ネットワークの外部のリソースにアクセスするためには、DNS が必要です。 使用するリソースによっては、カスタマイズした DNS サーバーをセットアップし、そのサーバーと Azure DNS サーバーとの間の転送を構成する必要があります。 

組織のニーズに応じて、DNS ログ ソリューション用に Azure Marketplace のサードパーティ製ソリューションを実装します。

- [VPN ゲートウェイのパケット キャプチャを構成する](../vpn-gateway/packet-capture.md) 

- [ネットワーク セキュリティ グループのフローのログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md) 

- [Azure Security Center によって提供されるネットワークのセキュリティについて](../security-center/security-center-network-recommendations.md) 

- [DNS の前提条件についての詳細情報](hpc-cache-prerequisites.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4:Azure リソースのログ記録を有効にする

**ガイダンス**:Azure HPC Cache リソースにより、アクティビティ ログが自動的に作成されます。 これらのログには、すべての書き込み操作 (PUT、POST、DELETE) が含まれますが、読み取り操作 (GET) は含まれません。 アクティビティ ログを使用すると、トラブルシューティング時にエラーを見つけたり、組織内のユーザーがリソースをどのように変更したかを監視したりできます。

また、Azure Security Center と Azure Policy を使用して、HPC Cache の Azure リソース ログとログ データの収集を有効にすることもできます。 これらのログは、後でセキュリティ インシデントを調査し、フォレンジック演習を実行するために重要な場合があります。

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure でのログ記録とログのさまざまな種類について](../azure-monitor/essentials/platform-logs-overview.md) 

- [Azure Security Center のデータ収集について](../security-center/security-center-enable-data-collection.md)

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5:セキュリティ ログの管理と分析を一元化する

**ガイダンス**:ログ記録のストレージと分析を一元化して、相関関係を有効にします。 ログ ソースごとに、データ所有者、アクセス ガイダンス、ストレージの場所、データの処理とアクセスに使用するツール、データ保持の要件を割り当てたことを確認します。

Azure アクティビティ ログを一元的なログ記録に統合していることを確認します。 Azure Monitor を介してログを取り込み、エンドポイント デバイス、ネットワーク リソース、およびその他のセキュリティ システムによって生成されたセキュリティ データを集計します。 Azure Monitor で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期のアーカイブ ストレージには Azure Storage アカウントを使用します。

さらに、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードします。

多くの組織では、頻繁に使用される "ホット" データに対しては Azure Sentinel を、使用頻度の低い "コールド" データに対しては Azure Storage を使用することを選択しています。

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../security/benchmarks/security-controls-v2-incident-response.md)」を参照してください。*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: 準備 – インシデント対応プロセスを Azure 用に更新する

**ガイダンス**:組織において、セキュリティ インシデントに対応するためのプロセスが用意されていること、Azure のこれらのプロセスが更新されていること、それらのプロセスを定期的に使用して準備されていることを確認します。

- [企業環境全体にセキュリティを実装する](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [インシデント対応のリファレンス ガイド](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="ir-2-preparation--set-up-incident-notification"></a>IR-2: 準備 – インシデント通知を設定する 

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

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: コンピューティング リソースにセキュリティで保護された構成を確立する

**ガイダンス**:Azure Security Center と Azure Policy を使用して、VM、コンテナーなど、すべてのコンピューティング リソースにセキュリティで保護された構成を確立します。

- [Azure Security Center の推奨事項を監視する方法](../security-center/security-center-recommendations.md) 

- [セキュリティの推奨事項 - リファレンス ガイド](../security-center/recommendations-reference.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

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

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1:定期的な自動バックアップを保証する

**ガイダンス**:Azure HPC Cache はストレージ システムではなくキャッシュ ソリューションであるため、ストレージ ターゲット内のデータを定期的にバックアップすることに重点を置いています。 Azure BLOB コンテナーと、任意のオンプレミス ストレージ ターゲットをバックアップする方法の標準的な手順に従ってください。 

リージョンの障害が発生した場合の中断を最小限に抑えるには、リージョン間のデータ アクセスを確保するための手順を実行します。  

各 Azure HPC Cache インスタンスは、特定のサブスクリプションと 1 つのリージョン内で実行されます。 これは、リージョンで全体的な障害が発生した場合に、キャッシュ ワークフローが中断される可能性があることを意味します。 この中断を最小限に抑えるため、組織は複数のリージョンからアクセスできるバックエンド ストレージを使用する必要があります。 このストレージは、適切な DNS サポートを備えたオンプレミスの NAS システム、またはキャッシュとは別のリージョンに存在する Azure BLOB ストレージのいずれかになります。

プライマリ リージョンでワークフローが進行すると、データはリージョン外の長期的なストレージに保存されます。 キャッシュ領域が使用できなくなった場合は、セカンダリ リージョンに複製された Azure HPC Cache インスタンスを作成し、同じストレージに接続して、新しいキャッシュから作業を再開することができます。

- [リージョン内フェールオーバーの詳細を確認する](hpc-region-recovery.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="br-2-encrypt-backup-data"></a>BR-2:バックアップ データを暗号化する

**ガイダンス**:攻撃に対し、自分のバックアップが確実に保護されるようにする必要があります。 これには、機密性が失われるのを防ぐためにバックアップを暗号化することも含まれます。

Azure Backup を使用するオンプレミスのバックアップによって、指定したパスフレーズを使用した保存時の暗号化が提供されます。 通常の Azure サービスのバックアップでは、バックアップ データは Azure プラットフォーム マネージド キーを使用して自動的に暗号化されます。 バックアップに、カスタマー マネージド キーを選択することもできます。 この場合は、キー コンテナー内のこのカスタマー マネージド キーも確実にバックアップ対象にします。

Azure HPC Cache は、キャッシュ ディスクにカスタマー キーを追加した場合でも、キャッシュされたデータを保持しているマネージド ディスクでの VM ホスト暗号化によっても保護されます。 カスタマー マネージド キーを二重暗号化のために追加することで、セキュリティ ニーズが高いお客様に追加のセキュリティ レベルが提供されます。 詳細については、「Azure Disk Storage のサーバー側暗号化」を参照してください。

バックアップおよびカスタマー マネージド キーを保護するには、Azure Backup、Azure Key Vault、またはその他のリソースでロールベースのアクセス制御を使用します。 さらに、バックアップが変更または削除される前に MFA を求める、高度なセキュリティ機能を有効にすることもできます。

- [VM ホストの暗号化](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)

- [Azure Disk Storage のサーバー側暗号化](../virtual-machines/disk-encryption.md)

- [Azure Backup のセキュリティ機能の概要](../backup/security-overview.md) 

- [カスタマー マネージド キーを使用したバックアップ データの暗号化](../backup/encryption-at-rest-with-cmk.md)  

- [Azure 上で Key Vault のキーをバックアップする方法](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-5.1.0)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**:バックアップされたカスタマー マネージド キーを復元できることを定期的に確認します。

- [Azure でキー コンテナーのキーを復元する方法](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-5.1.0)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

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

**ガイダンス**:コンプライアンス要件を満たしながら脅威を迅速に検出して修復するための、ログ記録と脅威対応戦略を確立します。 アナリストが、統合や手動による手順ではなく、脅威の対応に集中できるように、質の高いアラートとシームレスなエクスペリエンスを提供することを優先してください。 

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
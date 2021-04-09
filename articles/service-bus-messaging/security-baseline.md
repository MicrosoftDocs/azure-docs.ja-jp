---
title: Service Bus 用の Azure セキュリティ ベースライン
description: Service Bus セキュリティ ベースラインによって、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: service-bus-messaging
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8b3a67fc06594a4782ba55c17ab36c0bad9cf2a0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574929"
---
# <a name="azure-security-baseline-for-service-bus"></a>Service Bus 用の Azure セキュリティ ベースライン

Service Bus 用の Azure セキュリティ ベースラインには、デプロイのセキュリティ体制を改善するために役立つ推奨事項が含まれています。 このサービス用のベースラインは、ベスト プラクティス ガイダンスを使用して Azure 上のクラウド ソリューションをセキュリティで保護する方法について推奨事項を提供する [Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview-v1.md) に基づいて作成されています。 詳細については、[Azure セキュリティ ベースラインの概要](../security/benchmarks/security-baselines-overview.md)に関するページを参照してください。

Service Bus を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Service Bus セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する 

**ガイダンス**:Service Bus と Azure Private Link サービスとの統合により、仮想ネットワークにバインドされている仮想マシンなどのワークロードから、メッセージング機能への安全なプライベート アクセスができるようになります。 Service Bus 名前空間へのプライベート エンドポイント接続を作成します。 プライベート エンドポイントは、ご自分の仮想ネットワークからのプライベート IP アドレスを使用して、サービスを実質的に仮想ネットワークに取り込みます。 サービスへのすべてのトラフィックをそのプライベート エンドポイント経由でルーティングできるため、ゲートウェイ、NAT デバイス、ExpressRoute や VPN 接続、パブリック IP アドレスは必要ありません。

また、ファイアウォールを使用して Azure Service Bus 名前空間をセキュリティで保護することもできます。 Azure Service Bus では、受信ファイアウォールをサポートするために IP ベースのアクセス制御に対応しています。 Azure portal または Azure Resource Manager テンプレートを使用するか、Azure CLI または Azure PowerShell を使用してファイアウォール規則を設定することができます。

- [プライベート エンドポイント経由での Azure Service Bus 名前空間へのアクセスを許可する](private-link-service.md)

- [特定の IP アドレスまたは範囲から Azure Service Bus への接続を許可します](service-bus-ip-filtering.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2:仮想ネットワーク、サブネット、ネットワーク インターフェイスの構成とトラフィックを監視してログに記録する

**ガイダンス**:Azure 仮想マシンを使用して Service Bus エンティティにアクセスする場合は、ネットワーク セキュリティ グループ (NSG) フロー ログを有効にし、トラフィック監査のためにログをストレージ アカウントに送信します。 また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。 

Azure Security Center を使用し、ネットワークの保護に関する推奨事項に従って、Azure の Service Bus リソースをセキュリティで保護します。

- [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

- [Network Watcher を有効にする方法](../network-watcher/network-watcher-create.md)

- [Azure Security Center によって提供されるネットワークのセキュリティについて](../security-center/security-center-network-recommendations.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**:Service Bus 名前空間に関連付けられた仮想ネットワーク上で DDoS Protection Standard を有効にして、分散型サービス拒否 (DDoS) 攻撃から保護します。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある、または未使用のインターネット IP アドレスとの通信を拒否します。

- [DDoS 保護を構成する方法](../ddos-protection/manage-ddos-protection.md)

- [Azure Security Center の統合された脅威インテリジェンス](../security-center/azure-defender.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**:Azure 仮想マシンを使用して Service Bus エンティティにアクセスする場合は、Network Watcher パケット キャプチャを使用して異常なアクティビティを調査できます。

- [Network Watcher を有効にする方法](../network-watcher/network-watcher-create.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出または侵入防止システム (IDS または IPS) をデプロイする

**ガイダンス**:Azure 仮想マシンを使用して Service Bus エンティティにアクセスする場合は、ペイロード検査能力を備えた IDS または IPS 機能をサポートするオファーを Azure Marketplace から選択します。 組織で、ペイロード検査に基づく侵入の検出または防止を行う必要がない場合は、Azure Service Bus に組み込まれたファイアウォール機能を使用できます。 ファイアウォール規則を使用して、Service Bus 名前空間へのアクセスを、限られた範囲の IP アドレスまたは特定の IP アドレスに制限することができます。

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [指定された IP アドレスに対して Service Bus 名前空間のファイアウォール規則を追加する方法](service-bus-ip-filtering.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**:仮想ネットワーク サービス タグを使用して、Service Bus リソースとの間のトラフィックのフィルター処理を行うためのネットワーク アクセス制御を、ネットワーク セキュリティ グループまたは Azure ファイアウォールに定義します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたはターゲット フィールドにサービス タグ名 (ServiceBus など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。 

- [サービス タグの概要と使用](../virtual-network/service-tags-overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**:Azure Policy を使用して、Azure Service Bus 名前空間に関連付けられているネットワーク リソースの標準的なセキュリティ構成を定義して実装します。 "Microsoft.ServiceBus" と "Microsoft.Network" の名前空間で Azure Policy エイリアスを使用して、Service Bus 名前空間のネットワーク構成を監査または適用するためのカスタム ポリシーを作成します。 また、次のように、Azure Service Bus に関連する組み込みのポリシー定義を使用することもできます。

- Service Bus は仮想ネットワーク サービス エンドポイントを使用する必要がある
- Service Bus で診断ログを有効にする必要がある

また、組み込みの定義が組織のニーズに合わない場合は、カスタム ポリシー定義を作成することもできます。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Service Bus 名前空間の Azure 組み込みポリシー](./policy-reference.md#azure-service-bus-messaging)

- [ネットワークに関する Azure Policy のサンプル](../governance/policy/samples/built-in-policies.md#network)

- [Azure Blueprint を作成する方法](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**:Service Bus 名前空間に関連付けられており、ネットワーク セキュリティおよびトラフィック フローに関連する仮想ネットワークやその他のリソースに対し、タグを使用します。 個々のネットワーク セキュリティ グループ規則で "説明" フィールドを使用し、Service Bus 名前空間と関連付けられているネットワークとの間でのトラフィックを許可する規則について、ビジネス ニーズ、期間、その他の説明情報を指定します。 

すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ([タグとその値が必要] など) を使用します。 

Azure PowerShell または Azure CLI を使用して、タグに基づいてリソースを検索したり、リソースに対するアクションを実行したりすることもできます。 

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md) 

- [仮想ネットワークを作成する方法](../virtual-network/quick-create-portal.md) 

- [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**:Azure アクティビティ ログを使用して、ネットワーク リソース構成を監視し、Azure Service Bus に関連したネットワーク リソースの変更を検出します。 重要なネットワーク リソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

- [Azure アクティビティ ログ イベントを表示して取得する方法](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Monitor でアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、[Azure セキュリティ ベンチマークの「ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**:Azure Monitor を介してログを取り込み、Service Bus リソースによって生成されたセキュリティ データを集計します。 Azure Monitor で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期またはアーカイブ ストレージのための Azure ストレージ アカウントを構成します。 また、Service Bus と関連するログを Azure Sentinel またはサード パーティ SIEM に送信するように構成することもできます。

- [Azure Service Bus の診断設定を構成する方法](service-bus-diagnostic-logs.md)

- [Azure アクティビティ ログについて](../azure-monitor/essentials/platform-logs-overview.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md) 

- [Azure Monitor とサードパーティの SIEM 統合を開始する方法](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**:Azure Service Bus 名前空間の診断設定を有効にします。 Azure Service Bus では現在、アクティビティおよび操作、または診断の各ログがサポートされています。 アクティビティ ログには、ジョブで実行された操作に関する情報が含まれます。 診断ログでは、API を使用して、または言語 SDK を使用する管理クライアントを使用して、名前空間に対して実行された操作とアクションに関する詳細な情報が提供されます。 具体的には、これらのログは操作の種類 (キューの作成、使用リソース、操作の状態など) をキャプチャします。

- [Azure Service Bus の診断設定を有効にする方法](service-bus-diagnostic-logs.md)

- [Azure アクティビティ ログの診断設定を有効にする方法](../azure-monitor/essentials/activity-log.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**:Azure Monitor 内で、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定し、Service Bus 関連のインシデントを取得して確認します。

- [Log Analytics ワークスペースのログ保持パラメーターを設定する方法](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**:異常な動作がないかログを分析および監視し、Service Bus エンティティに関連する結果を定期的に確認します。 Azure Monitor を使用して、ログを確認したり、Service Bus に関連するログ データにクエリを実行したりします。

- [Log Analytics ワークスペースの詳細について](../azure-monitor/logs/log-analytics-tutorial.md)

- [Azure Monitor でカスタム クエリを実行する方法](../azure-monitor/logs/get-started-queries.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: Log Analytics ワークスペースと共に Azure Security Center を使用し、セキュリティ ログやイベントで検出される異常なアクティビティに対する監視とアラートの送信を行います。 または、Azure Sentinel に対してデータを有効にしてオンボードすることもできます。

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

- [Azure Security Center でアラートを管理する方法](../security-center/security-center-managing-and-responding-alerts.md)

- [Log Analytics のログ データに関するアラートを送信する方法](../azure-monitor/alerts/tutorial-response.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="identity-and-access-control"></a>ID とアクセスの制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Azure のロールベースのアクセス制御 (Azure RBAC) を使用すると、ロールの割り当てを通じて Azure リソースへのアクセスを管理できます。 これらのロールを、ユーザー、グループ サービス プリンシパル、およびマネージド ID に割り当てることができます。 Service Bus に対して定義済みの組み込みロールがあります。これらのロールは、Azure CLI、Azure PowerShell、Azure portal などのツールを使用してインベントリまたは照会できます。

- [Azure Service Bus 用の組み込みロール](authenticate-application.md#azure-built-in-roles-for-azure-service-bus)

- [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](/powershell/module/azuread/get-azureaddirectoryrole) 

- [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**:Service Bus へのコントロール プレーン アクセスは、Azure Active Directory (Azure AD) を介して制御されます。 Azure AD には既定のパスワードという概念がありません。

Service Bus へのデータ プレーン アクセスは、マネージド ID、アプリの登録、または Shared Access Signature を使用して、Azure AD を介して制御されます。 Shared Access Signature は、Service Bus 名前空間に接続するクライアントによって使用され、いつでも再生成することができます。

- [Service Bus の Shared Access Signature について](service-bus-sas.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Azure Security Center ID とアクセス管理を使用して、管理者アカウントの数を監視します。

さらに、専用管理者アカウントを追跡できるように、Azure Security Center または組み込みの Azure ポリシーの次のような推奨事項を使用することもできます。

- 複数の所有者がサブスクリプションに割り当てられている必要がある
- 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある
- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある

また、組み込みの定義が組織のニーズに合わない場合は、カスタム ポリシー定義を作成することもできます。

- [Azure Security Center を使用して ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

- [Azure Policy を使用する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory シングル サインオン (SSO) を使用する

**ガイダンス**:Microsoft Azure では、Azure Active Directory (Azure AD) を利用して、リソースとアプリケーションの統合されたアクセス制御管理が提供されています。 Azure Service Bus で Azure AD を使用する主な利点は、資格情報をコード内に格納する必要がなくなることです。 代わりに、Microsoft ID プラットフォームから OAuth 2.0 アクセス トークンを要求することができます。 トークンを要求するリソース名は https://azure.microsoft.com/services/service-bus/ です。 Azure AD によって、アプリケーションを実行しているセキュリティ プリンシパル (ユーザー、グループ、またはサービス プリンシパル) が認証されます。 認証が成功すると、Azure AD からアプリケーションにアクセス トークンが返されます。アプリケーションでは、このアクセス トークンを使用して Azure Service Bus リソースへの要求を承認できます。

- [Service Bus リソースにアクセスするために Azure AD でアプリケーションを認証する方法](authenticate-application.md)

- [Azure AD を使用した SSO について](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**:Azure Active Directory 多要素認証 (MFA) を有効にし、Service Bus 対応リソースを保護するための Azure Security Center ID とアクセス管理の推奨事項に従います。

- [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: セキュリティで保護された Azure マネージド ワークステーションを管理タスクに使用する

**ガイダンス**:多要素認証 (MFA) が構成された特権アクセス ワークステーション (PAW) を使用してログインし、Service Bus 対応リソースを構成します。

- [特権アクセス ワークステーションについて](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**: Azure Active Directory セキュリティ レポートと監視を使用して、環境内で疑わしいアクティビティや安全でないアクティビティが発生したときに検出します。 Azure Security Center を使用して ID およびアクセス アクティビティを監視します。

- [危険なアクティビティのフラグが設定された Azure AD ユーザーを識別する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: Azure AD のネームド ロケーションを使用して、IP アドレス範囲または国、地域の特定の論理グループからのアクセスのみを許可します。 

- [Azure AD のネームド ロケーションを構成する方法](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**:Service Bus などの Azure リソースの主要な認証および認可システムとして Azure Active Directory (AD) を使用します。 これにより、Azure ロールベースのアクセス制御 (Azure RBAC) で機密性の高いリソースを管理できるようになります。

- [Azure AD インスタンスを作成して構成する方法](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure Active Directory を使用して Service Bus リソースへのアクセスを承認する](authenticate-application.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory (Azure AD) では、古いアカウントの検出に役立つログが提供されます。 また、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。

さらに、Service Bus 名前空間の Shared Access Signature を定期的にローテーションさせます。

- [Azure AD のレポートの概要](../active-directory/reports-monitoring/index.yml)

- [Azure ID アクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

- [Service Bus 名前空間の Shared Access Signature について](service-bus-sas.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**:Azure Active Directory (Azure AD) サインイン アクティビティ、監査、およびリスク イベント ログのソースにアクセスできるため、Azure Sentinel またはサード パーティの SIEM ツールとの統合が可能です。

このプロセスを効率化するには、Azure AD ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 次に Azure Monitor で、ログで発生する特定のアクションに対して必要なログ アラートを構成できます。

- [Azure アクティビティ ログを Azure Monitor に統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Azure Active Directory を使用して Service Bus リソースへのアクセスを承認する](authenticate-application.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: アカウント サインイン動作の偏差に関するアラートを生成する

**ガイダンス**:検出された、Service Bus 対応リソースに関連する疑わしいアクションに対する自動応答を構成するには、Azure Active Directory の Identity Protection とリスク検出機能を使用します。 組織のセキュリティ対応を実装するには、Azure Sentinel によって自動応答を有効にする必要があります。

- [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**:現時点ではご利用いただけません。カスタマー ロックボックスは、Service Bus ではまだサポートされていません。

- [カスタマー ロックボックスでサポートされているサービスの一覧](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**:Service Bus に関連するリソースに対してタグを使用すると、機密情報を格納または処理する Azure リソースの追跡に役立ちます。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:開発、テスト、運用で別々のサブスクリプションと管理グループを実装します。 Service Bus 名前空間は、プライベート エンドポイントが構成された仮想ネットワークで分離し、適切なタグを付ける必要があります。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [タグを作成して利用する方法](../azure-resource-manager/management/tag-resources.md)

- [仮想ネットワークを作成する方法](../virtual-network/quick-create-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**:仮想マシンを使用して Service Bus エンティティにアクセスする場合は、仮想ネットワーク、プライベート エンドポイント、Service Bus ファイアウォール、ネットワーク セキュリティ グループ、サービス タグを使用して、データ流出の可能性を軽減します。

Microsoft では、Azure Service Bus 用の基になるインフラストラクチャを管理し、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。

- [Azure Service Bus 名前空間の IP ファイアウォール規則を構成する](service-bus-ip-filtering.md)

- [特定の仮想ネットワークから Azure Service Bus 名前空間へのアクセスを許可する](private-link-service.md)

- [プライベート エンドポイント経由での Azure Service Bus 名前空間へのアクセスを許可する](private-link-service.md)

- [ネットワーク セキュリティ グループとサービス タグについて](../virtual-network/network-security-groups-overview.md)

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**:Azure Service Bus では、既定で TLS で暗号化された通信が強制されます。 現在、TLS バージョン 1.0、1.1、および 1.2 がサポートされています。 ただし、TLS 1.0 と 1.1 は業界全体で非推奨になる予定であるため、可能であれば TLS 1.2 以降を使用してください。

- [Service Bus のセキュリティ機能については、ネットワーク セキュリティに関するページを参照してください](network-security.md)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**:Azure Service Bus では、データの識別、分類、損失防止機能はまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6:ロールベースのアクセス制御を使用してリソースへのアクセスを制御する

**ガイダンス**:Azure Service Bus では、Azure Active Directory (Azure AD) を使用して Service Bus エンティティへの要求を承認することがサポートされています。 Azure AD では、Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、セキュリティ プリンシパル (ユーザーまたはアプリケーションのサービス プリンシパルである可能性があります) にアクセス許可を付与できます。

- [Azure Service Bus の Azure RBAC と使用可能なロールについて](authenticate-application.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**:Azure Service Bus では、Microsoft のマネージド キーまたはカスタマー マネージド キーのいずれかを使用した保存データの暗号化のオプションがサポートされています。 この機能を使用して、Azure Service Bus の保存データ暗号化に使用されるカスタマー マネージド キーの作成、ローテーション、無効化や、それに対するアクセスの取り消しを実行できます。

- [Azure Service Bus を暗号化するためにカスタマー マネージド キーを構成する方法](configure-customer-managed-key.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**:Azure Service Bus の運用インスタンスや、その他の重要または関連リソースに変更が生じたときにアラートを作成するには、Azure Monitor と Azure アクティビティ ログを使用します。

- [Azure アクティビティ ログ イベントのアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**:Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (Azure Service Bus 名前空間を含む) のクエリや検出を行います。 テナント内の適切な (読み取り) アクセス許可を持っており、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙できることを確認します。

- [Azure Resource Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

- [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC について](../role-based-access-control/overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを提供する Azure リソースにタグを適用すると、それらのリソースが各分類に論理的に整理されます。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**:必要に応じて、タグ付け、管理グループ、個別のサブスクリプションを使用して、Azure Service Bus 名前空間と関連リソースの整理と追跡を行います。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**: 組織のニーズに応じて、承認された Azure リソースとコンピューティング リソース用に承認されたソフトウェアのインベントリを作成します。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類

- 許可されるリソースの種類

また、サブスクリプション内のリソースのクエリと検出を行うには、Azure Resource Graph を使用します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類
- 許可されるリソースの種類

また、組み込みの定義が組織のニーズに合わない場合は、カスタム ポリシー定義を作成することもできます。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](../governance/policy/samples/index.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: Azure 条件付きアクセスを使用して Azure Resource Manager とやりとりするユーザーの機能を制限するには、"Microsoft Azure 管理" アプリに対して [アクセスのブロック] を構成します。

- [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**:Azure Service Bus デプロイの標準セキュリティ構成を定義して実装します。 また、次のように、Azure Service Bus 用の組み込みのポリシー定義を使用することもできます。

- Service Bus で診断ログを有効にする必要がある
- Service Bus で、仮想ネットワーク サービス エンドポイントを使用して、プライベート ネットワークへのネットワーク トラフィックを制限する必要がある

構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.ServiceBus" 名前空間で Azure Policy エイリアスを使用します。

- [Service Bus 用の Azure 組み込みポリシー](./policy-reference.md)

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**:Service Bus 対応リソースまたはアプリケーション全体にセキュリティで保護された設定を適用するには、Azure Policy の [deny] と [deploy if not exist] を使用します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy の効果の詳細について](../governance/policy/concepts/effects.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**:システム構成を警告処理、監査、適用するためのカスタム ポリシーを作成するには、"Microsoft.ServiceBus" 名前空間で Azure Policy エイリアスを使用します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**:システム構成を警告処理、監査、適用するためのカスタム ポリシーを作成するには、"Microsoft.ServiceBus" 名前空間で Azure Policy エイリアスを使用します。 Azure Policy の [audit]、[deny]、[deploy if not exist] を使用して、Azure Service Bus デプロイおよび関連リソースの構成を自動的に適用します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**:Azure 仮想マシン、または Azure App Service 上で実行されている Web アプリケーションが Service Bus エンティティへのアクセスに使用されている場合、マネージド サービス ID を Azure Key Vault と組み合わせて使用して、Azure Service Bus デプロイの Shared Access Signature の管理を簡素化し、セキュリティで保護します。 Key Vault の論理的な削除を確実に有効にします。

- [Service Bus リソースにアクセスするために Azure Active Directory を使用してマネージド ID を認証する](service-bus-managed-service-identity.md)

- [Service Bus 用にカスタマー マネージド キーを構成する](configure-customer-managed-key.md)

- [キー コンテナーを作成する方法](../key-vault/general/quick-create-portal.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**:Azure 仮想マシン、または Azure App Service 上で実行されている Web アプリケーションが Service Bus エンティティへのアクセスに使用されている場合、マネージド サービス ID を Azure Key Vault と組み合わせて使用して、Azure Service Bus の管理を簡素化し、セキュリティで保護します。 Key Vault の論理的な削除を確実に有効にします。

マネージド ID を使用して、Azure Active Directory (Azure AD) で自動的に管理される ID を Azure サービスに提供します。 マネージド ID を使用すると、コード内に資格情報を記述することなく、Azure AD 認証をサポートする任意のサービス (Azure Key Vault を含む) に対して認証できます。

- [Service Bus リソースにアクセスするために Azure Active Directory を使用してマネージド ID を認証する](service-bus-managed-service-identity.md)

- [Service Bus 用にカスタマー マネージド キーを構成する](configure-customer-managed-key.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、[Azure セキュリティ ベンチマークの「マルウェアからの防御](../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**:Azure Service Bus、App Service、Data Lake Storage、Blob Storage、Azure Database for PostgreSQL などの非コンピューティング Azure リソースにアップロードされるコンテンツはすべて、事前にスキャンしてください。Microsoft は、これらのインスタンス内のデータにアクセスできません。

Microsoft のマルウェア対策は、Azure サービスをサポートする基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="data-recovery"></a>データの復旧

*詳しくは、「[Azure Security ベンチマーク:データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的な自動バックアップを保証する

**ガイダンス**:Azure Service Bus の geo ディザスター リカバリーを構成します。 Azure リージョン全体またはデータ センター全体 (可用性ゾーンが使用されていない場合) にダウンタイムが発生した場合、別のリージョンまたはデータ センターでデータ処理が続行されることが重要です。 そのため、geo ディザスター リカバリーと geo レプリケーションは、どの企業にとっても重要な機能です。 Azure Service Bus は、名前空間のレベルで、geo ディザスター リカバリーと geo レプリケーションの両方をサポートします。

- [Azure Service Bus の geo ディザスター リカバリーについて](service-bus-geo-dr.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**:Azure Service Bus では、Azure Storage Service Encryption (Azure SSE) による保存データの暗号化が提供されます。 Service Bus では、データを格納するために Azure Storage が使用されます。既定では、Azure Storage を使用して格納されるすべてのデータは、Microsoft のマネージド キーを使用して暗号化されます。 Azure Key Vault を使用してカスタマー マネージド キーを格納する場合は、必ずキーの定期的な自動バックアップを実行してください。

次の PowerShell コマンドを使用して、Key Vault シークレットの定期的な自動バックアップを必ず実行します。Backup-AzKeyVaultSecret

- [Azure Service Bus の保存データを暗号化するためにカスタマー マネージド キーを構成する方法](configure-customer-managed-key.md)

- [Key Vault のシークレットをバックアップする方法](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**:Service Bus データの暗号化に使用する、バックアップされたカスタマー マネージド キーの復元をテストします。

- [Azure Service Bus の保存データを暗号化するためにカスタマー マネージド キーを構成する方法](configure-customer-managed-key.md)

- [Azure でキー コンテナーのキーを復元する方法](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**: Key Vault で論理的な削除を有効にして、偶発的または悪意のある削除からキーを保護します。 Azure Service Bus では、カスタマー マネージド キーに [論理的な削除] と [Do Not Purge]\(消去しない\) が構成されている必要があります。

- [Key Vault で論理的な削除を有効にする方法](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [キーを使用したキー コンテナーの設定](../event-hubs/configure-customer-managed-key.md)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールと、検出からインシデント後のレビューまでのインシデント対応と管理のフェーズを定義する、書面によるインシデント対応計画があることを確認します。 

- [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [独自のインシデント対応計画を作成するために NIST の「コンピューター セキュリティ インシデント対応ガイド」を使用する](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Azure Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

さらに、タグを使用してサブスクリプションをマークし、Azure リソース (特に、機密データを処理するもの) を識別して分類するための命名システムを作成します。 インシデントが発生した Azure リソースと環境の重要度に基づいてアラートの修復に優先順位を付けることは、お客様の責任です。 

- [Security alerts in Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**: 定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

- [NIST の出版物:「IT 計画と機能に関するテスト、トレーニング、演習プログラムのガイド」を参照してください。](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。 

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**: 連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートすると、Azure リソースへのリスクを特定できます。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。 

- [連続エクスポートを構成する方法](../security-center/continuous-export.md) 

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**: 

Azure Security Center のワークフロー自動化機能を使用すると、セキュリティのアラートと推奨事項に対して自動的に応答をトリガーし、Azure リソースを保護できます。 

- [Security Center でワークフロー自動化を構成する方法](../security-center/workflow-automation.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、[Azure セキュリティ ベンチマークの「侵入テストとレッド チーム演習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft クラウド侵入テストの実施ルールに従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。 

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](../security/benchmarks/overview.md)に関するページを参照する
- [Azure セキュリティ ベースライン](../security/benchmarks/security-baselines-overview.md)の詳細について学習する
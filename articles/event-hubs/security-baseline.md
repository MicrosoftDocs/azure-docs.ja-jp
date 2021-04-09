---
title: Event Hubs 用の Azure セキュリティ ベースライン
description: Event Hubs セキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: event-hubs
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1e1a8cb24b47277b38bd2d64b733a6f28ee8acff
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742505"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Event Hubs 用の Azure セキュリティ ベースライン

このセキュリティ ベースラインにより、[Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview-v1.md) のガイダンスが Event Hubs に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。
内容は、Event Hubs に適用される Azure セキュリティ ベンチマークと関連ガイダンスで定義されている **セキュリティ コントロール** によってグループ化されています。 Event Hubs に適用できない **コントロール** は、除外されています。

 
Event Hubs を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Event Hubs セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**: Azure Event Hubs と仮想ネットワーク サービス エンドポイントの統合により、仮想ネットワークにバインドされた仮想マシンなどのワークロードからメッセージング機能へのセキュリティで保護されたアクセスが可能になり、ネットワーク トラフィック パスは両端でセキュリティ保護されます。

少なくとも 1 つの仮想ネットワーク サブネット サービス エンドポイントにバインドすると、それぞれの Event Hubs 名前空間は、仮想ネットワークの承認されたサブネットを除き、どこからのトラフィックも受け入れなくなります。 仮想ネットワークの観点からは、サービス エンドポイントに Event Hubs 名前空間をバインドすると、仮想ネットワーク サブネットからメッセージング サービスへの分離されたネットワーク トンネルが構成されます。 

さらに、プライベート エンドポイントを作成することもできます。これは、Azure Private Link サービスを利用して Event Hubs サービスに非公開かつ安全に接続するネットワーク インターフェイスです。 プライベート エンドポイントは、ご自分の仮想ネットワークからのプライベート IP アドレスを使用して、サービスを実質的に仮想ネットワークに取り込みます。 サービスへのすべてのトラフィックをプライベート エンドポイント経由でルーティングできるため、ゲートウェイ、NAT デバイス、ExpressRoute または VPN 接続、パブリック IP アドレスは必要ありません。 

また、ファイアウォールを使用して Azure Event Hubs 名前空間をセキュリティで保護することもできます。 Event Hubs では、受信ファイアウォールをサポートするために IP ベースのアクセス制御に対応しています。 Azure portal または Azure Resource Manager テンプレートを使用するか、Azure CLI または Azure PowerShell を使用してファイアウォール規則を設定することができます。

- [Azure Event Hubs で仮想ネットワーク サービス エンドポイントを使用する方法](event-hubs-service-endpoints.md)

- [Azure Event Hubs を Azure Private Link と統合する](private-link-service.md)

- [Azure Event Hubs 名前空間に対する IP ファイアウォール規則を構成する方法](event-hubs-ip-filtering.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 このコントロールに関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.EventHub**:

[!INCLUDE [Resource Policy for Microsoft.EventHub 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2:仮想ネットワーク、サブネット、ネットワーク インターフェイスの構成とトラフィックを監視してログに記録する

**ガイダンス**: Azure Security Center を使用し、ネットワークの保護に関する推奨事項に従って、Azure の Event Hubs リソースをセキュリティで保護します。 Azure 仮想マシンを使用してイベント ハブにアクセスする場合は、ネットワーク セキュリティ グループ フロー ログを有効にし、トラフィック監査のためにログをストレージ アカウントに送信します。

- [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure Security Center によって提供されるネットワークのセキュリティについて](../security-center/security-center-network-recommendations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**:イベント ハブに関連付けられた仮想ネットワーク上で DDoS Protection Standard を有効にして、分散型サービス拒否 (DDoS) 攻撃から保護します。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある、または未使用のインターネット IP アドレスとの通信を拒否します。

- [DDoS 保護を構成する方法](../ddos-protection/manage-ddos-protection.md)

- [Azure Security Center の統合された脅威インテリジェンスの詳細について](/azure/security-center/security-center-alerts-service-layer)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**: Azure 仮想マシンを使用してイベント ハブにアクセスする場合は、ネットワーク セキュリティ グループ フロー ログを有効にし、トラフィック監査のためにログをストレージ アカウントに送信します。 ネットワーク セキュリティ グループのフロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

異常なアクティビティを調査する必要がある場合は、Network Watcher パケット キャプチャを有効にします。

- [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

- [Network Watcher を有効にする方法](../network-watcher/network-watcher-create.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**: Azure 仮想マシンを使用してイベント ハブにアクセスする場合は、Azure Marketplace から、ペイロード検査能力を備えた IDS または IPS 機能をサポートするオファーを選択します。 組織で、ペイロード検査に基づく侵入の検出または防止を行う必要がない場合は、Azure Event Hubs に組み込まれたファイアウォール機能を使用できます。 ファイアウォール規則を使用して、Event Hubs 名前空間へのアクセスを、限られた範囲の IP アドレスまたは特定の IP アドレスに制限することができます。

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [指定された IP アドレスに対して Event Hubs のファイアウォール規則を追加する方法](event-hubs-ip-filtering.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure Policy を使用して、Azure Event Hubs 名前空間に関連付けられているネットワーク リソースの標準的なセキュリティ構成を定義して実装します。 **Microsoft.EventHub** および **Microsoft.Network** 名前空間の Azure Policy エイリアスを使用して、Event Hubs 名前空間のネットワーク構成を監査または適用するためのカスタム ポリシー定義を作成します。 また、次のように、Azure Event Hubs に関連する組み込みのポリシー定義を使用することもできます。

- イベント ハブは仮想ネットワーク サービス エンドポイントを使用する必要があります。

追加情報については、参照先のリンクをご覧ください。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Event Hubs 名前空間用の Azure 組み込みポリシー](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub)

- [ネットワークに関する Azure Policy のサンプル](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Azure Blueprint を作成する方法](../governance/blueprints/create-blueprint-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: イベント ハブに関連付けられているネットワーク セキュリティおよびトラフィック フローに関連したネットワーク サービス グループやその他のリソースにタグを使用します。

- [タグを作成して使用する方法](/azure/azure-resource-manager/resource-group-using-tags)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: Azure アクティビティ ログを使用して、ネットワーク リソース構成を監視し、Azure Event Hubs に関連したネットワーク リソースの変更を検出します。 重要なネットワーク リソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

- [Azure アクティビティ ログ イベントを表示して取得する方法](/azure/azure-monitor/platform/activity-log-view)

- [Azure Monitor でアラートを作成する方法](/azure/azure-monitor/platform/alerts-activity-log)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、[Azure セキュリティ ベンチマークの「ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: Azure Monitor 内で、アクティビティ ログおよびイベント ハブの診断設定内のイベント ハブに関連するログを構成し、クエリ対象の Log Analytics ワークスペースまたは長期アーカイブ ストレージのストレージ アカウントにログを送信します。

- [Azure Event Hubs の診断設定を構成する方法](event-hubs-diagnostic-logs.md)

- [Azure アクティビティ ログについて](/azure/azure-monitor/platform/platform-logs-overview)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: Azure Event Hubs 名前空間の診断設定を有効にします。 Azure Event Hubs の診断設定には、アーカイブ ログ、操作ログ、自動スケーリング ログの 3 つのカテゴリがあります。 操作ログを有効にして、Event Hubs の操作中に発生したことに関する情報 (特に、イベント ハブの作成などの操作の種類、使用されたリソース、操作の状態) を取得します。

さらに、Azure アクティビティ ログの診断設定を有効にして、それを Azure ストレージ アカウント、イベント ハブ、または Log Analytics ワークスペースに送信することもできます。 アクティビティ ログは、Azure Event Hubs およびその他のリソースで実行された操作に関する分析情報を提供します。 アクティビティ ログを使用すると、Azure Event Hubs 名前空間で実行された書き込み操作 (PUT、POST、DELETE) について、"いつ誰が何を" 行ったのかを確認できます。

- [Azure Event Hubs の診断設定を有効にする方法](event-hubs-diagnostic-logs.md)

- [Azure アクティビティ ログの診断設定を有効にする方法](/azure/azure-monitor/platform/diagnostic-settings-legacy)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 このコントロールに関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.EventHub**:

[!INCLUDE [Resource Policy for Microsoft.EventHub 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**:Azure Monitor 内で、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定し、イベント ハブ関連のインシデントを取得して確認します。

- [Log Analytics ワークスペースのログ保持パラメーターを設定する方法](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: 異常な動作についてログを分析および監視し、イベント ハブに関連する結果を定期的に確認します。 ログを確認し、ログ データに対してクエリを実行するには、Azure Monitor の Log Analytics を使用します。 または、Azure Sentinel またはサードパーティのシステム情報およびイベント管理ソリューションに対してデータを有効にしてオンボードすることもできます。

- [Log Analytics ワークスペースの詳細について](/azure/azure-monitor/log-query/get-started-portal)

- [Azure Monitor でカスタム クエリを実行する方法](/azure/azure-monitor/log-query/get-started-queries)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: Azure Monitor 内で、アクティビティ ログおよびイベント ハブの診断設定内の Azure Event Hubs に関連するログを構成し、クエリ対象の Log Analytics ワークスペースまたは長期アーカイブ ストレージのストレージ アカウントにログを送信します。 Log Analytics ワークスペースを使用して、セキュリティ ログおよびイベントで異常なアクティビティが検出された場合のアラートを作成します。

または、Azure Sentinel に対してデータを有効にしてオンボードすることもできます。 

- [Azure アクティビティ ログについて](/azure/azure-monitor/platform/platform-logs-overview)

- [Azure Event Hubs の診断設定を構成する方法](event-hubs-diagnostic-logs.md)

- [Log Analytics ワークスペースのログ データに関するアラートを送信する方法](/azure/azure-monitor/learn/tutorial-response)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Azure Active Directory (Azure AD) には、明示的に割り当てる必要があるためにクエリ可能である組み込みロールがあります。 Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。

- [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](/powershell/module/azuread/get-azureaddirectoryrole)

- [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](/powershell/module/azuread/get-azureaddirectoryrolemember)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**:Event Hubs へのコントロール プレーン アクセスは、Azure Active Directory (Azure AD) を介して制御されます。 Azure AD には既定のパスワードという概念がありません。

Event Hubs へのデータ プレーン アクセスは、マネージド ID またはアプリの登録と Shared Access Signature を使用して、Azure AD を介して制御されます。 Shared Access Signature は、イベント ハブに接続するクライアントによって使用され、いつでも再生成することができます。

- [Event Hubs の Shared Access Signature について](authenticate-shared-access-signature.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Azure Security Center ID とアクセス管理を使用して、管理者アカウントの数を監視します。

さらに、専用管理者アカウントを追跡できるように、Azure Security Center または組み込みの Azure ポリシーの次のような推奨事項を使用することもできます。

- 複数の所有者がサブスクリプションに割り当てられている必要がある

- 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある

- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある

追加情報については、参照先のリンクをご覧ください。

- [Azure Security Center を使用して ID およびアクセスを監視する方法 (プレビュー)](../security-center/security-center-identity-access.md)

- [Azure Policy を使用する方法](../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory シングル サインオン (SSO) を使用する

**ガイダンス**:Microsoft Azure では、Azure Active Directory (Azure AD) を利用して、リソースとアプリケーションの統合されたアクセス制御管理が提供されています。 Azure Event Hubs で Azure AD を使用する主な利点は、資格情報をコード内に格納する必要がなくなることです。 代わりに、Microsoft ID プラットフォームから OAuth 2.0 アクセス トークンを要求することができます。 トークンを要求するリソース名は https:\//eventhubs.azure.net/ です。 Azure AD によって、アプリケーションを実行しているセキュリティ プリンシパル (ユーザー、グループ、またはサービス プリンシパル) が認証されます。 認証が成功すると、Azure AD からアプリケーションにアクセス トークンが返されます。アプリケーションでは、このアクセス トークンを使用して Azure Event Hubs リソースへの要求を承認できます。

- [Event Hubs リソースにアクセスするために Azure AD でアプリケーションを認証する方法](authenticate-application.md)

- [Azure AD を使用した SSO について](../active-directory/manage-apps/what-is-single-sign-on.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory (Azure AD) 多要素認証を有効にし、イベント ハブ対応リソースを保護するための Azure Security Center ID とアクセス管理の推奨事項に従います。

- [Azure で多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**: 多要素認証が構成された特権アクセス ワークステーション (PAW) を使用してログインし、イベント ハブ対応リソースを構成します。

- [特権アクセス ワークステーションについて](/security/compass/privileged-access-devices)

- [Azure で多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**: Azure Active Directory (Azure AD) Privileged Identity Management を使用して、環境内で疑わしいアクティビティまたは安全ではないアクティビティが発生したときにログとアラートを生成します。 Azure AD のリスク検出を使用して、危険なユーザーの行動に関するアラートとレポートを表示します。 追加のログ記録を行うには、Azure Security Center のリスク検出アラートを Azure Monitor に送信し、アクション グループを使用してカスタムのアラートまたは通知を構成します。

- [Azure AD のリスク検出について](/azure/active-directory/reports-monitoring/concept-risk-events)

- [カスタムのアラートおよび通知用にアクション グループを構成する方法](/azure/azure-monitor/platform/action-groups)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: 条件付きアクセスのネームド ロケーションを使用して、IP アドレス範囲または国/地域の特定の論理グループからのアクセスのみを許可します。

- [Azure でネームド ロケーションを構成する方法](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Event Hubs などの Azure リソースの主要な認証および承認システムとして Azure Active Directory (Azure AD) を使用します。 これにより、Azure のロールベースのアクセス制御 (Azure RBAC) で機密性の高いリソースを管理できるようになります。

- [Azure AD インスタンスを作成して構成する方法](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure AD を使用して Event Hubs リソースへのアクセスを承認する](authorize-access-azure-active-directory.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory (Azure AD) では、古いアカウントの検出に役立つログが提供されます。 また、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。

さらに、Event Hubs の Shared Access Signature を定期的にローテーションします。

- [Azure AD のレポートの概要](/azure/active-directory/reports-monitoring/)

- [Azure ID アクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

- [Event Hubs の Shared Access Signature について](authenticate-shared-access-signature.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**: Azure Active Directory (Azure AD) サインイン アクティビティ、監査、およびリスク イベント ログのソースにアクセスできるため、任意のサード パーティのシステム情報およびイベント管理ソリューションや監視ツールとの統合が可能です。

このプロセスを効率化するには、Azure AD ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics 内で必要なログ アラートを構成できます。

- [Azure アクティビティ ログを Azure Monitor に統合する方法](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Azure AD を使用して Event Hubs リソースへのアクセスを承認する](authorize-access-azure-active-directory.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: アカウント サインイン動作の偏差に関するアラートを生成する

**ガイダンス**: 検出された、Event Hubs 対応リソースに関連する疑わしいアクションに対する自動応答を構成するには、Azure Active Directory (Azure AD) の Identity Protection とリスク検出機能を使用します。 組織のセキュリティ対応を実装するには、Azure Sentinel によって自動応答を有効にする必要があります。

- [Azure AD の危険なサインインを表示する方法](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**:現時点ではご利用いただけません。カスタマー ロックボックスは、Event Hubs ではまだサポートされていません。

- [カスタマー ロックボックスでサポートされているサービスの一覧](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: Event Hubs に関連するリソースに対してタグを使用すると、機密情報を格納または処理する Azure リソースの追跡に役立ちます。

- [タグを作成して使用する方法](/azure/azure-resource-manager/resource-group-using-tags)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**: 開発、テスト、運用で別々のサブスクリプションを、必要に応じて管理グループを使用して実装します。 Event Hubs 名前空間は、サービス エンドポイントを有効にした仮想ネットワークで分離し、適切なタグを付ける必要があります。

また、ファイアウォールを使用して Azure Event Hubs 名前空間をセキュリティで保護することもできます。 Azure Event Hubs では、受信ファイアウォールをサポートするために IP ベースのアクセス制御に対応しています。 Azure portal または Azure Resource Manager テンプレートを使用するか、Azure CLI または Azure PowerShell を使用してファイアウォール規則を設定することができます。

- [追加の Azure サブスクリプションを作成する方法](/azure/billing/billing-create-subscription)

- [管理グループを作成する方法](/azure/governance/management-groups/create)

- [Azure Event Hubs 名前空間に対する IP ファイアウォール規則を構成する](event-hubs-ip-filtering.md)

- [タグを作成して利用する方法](/azure/azure-resource-manager/resource-group-using-tags)

- [仮想ネットワークを作成する方法](../virtual-network/quick-create-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: 仮想マシンを使用してイベント ハブにアクセスする場合は、仮想ネットワーク、サービス エンドポイント、Event Hubs ファイアウォール、ネットワーク セキュリティ グループ、サービス タグを使用して、データ流出の可能性を軽減します。

Microsoft では、Azure Event Hubs 用の基になるインフラストラクチャを管理し、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。

- [Azure Event Hubs 名前空間に対する IP ファイアウォール規則を構成する](event-hubs-ip-filtering.md)

- [Azure Event Hubs で使用する仮想ネットワーク サービス エンドポイントについて](event-hubs-service-endpoints.md)

- [Azure Event Hubs を Azure Private Link と統合する](private-link-service.md)

- [ネットワーク セキュリティ グループとサービス タグについて](/azure/virtual-network/security-overview)

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: Azure Event Hubs では、データの識別、分類、損失防止機能はまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6:ロールベースのアクセス制御を使用してリソースへのアクセスを制御する

**ガイダンス**: Azure Event Hubs では、Azure Active Directory (Azure AD) を使用した Event Hubs リソースへの要求の承認がサポートされています。 Azure AD では、Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、セキュリティ プリンシパル (ユーザーまたはアプリケーションのサービス プリンシパルである可能性があります) にアクセス許可を付与できます。

- [Azure RBAC と Azure Event Hubs で使用できるロールを理解する](authorize-access-azure-active-directory.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: Azure Event Hubs で、Microsoft のマネージド キーまたはカスタマー マネージド キーのいずれかを使用した保存データの暗号化のオプションがサポートされています。 この機能を使用すると、Azure Event Hubs の保存データ暗号化に使用されるカスタマー マネージド キーへのアクセスの作成、ローテーション、無効化、取り消しができます。

- [Azure Event Hubs を暗号化するためにカスタマー マネージド キーを構成する方法](configure-customer-managed-key.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**:Azure Event Hubs の運用インスタンスやその他の重要なリソースまたは関連リソースへの変更がいつ発生したかに関するアラートを作成するには、Azure Monitor と Azure アクティビティ ログを使用します。

- [Azure アクティビティ ログ イベントのアラートを作成する方法](/azure/azure-monitor/platform/alerts-activity-log)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**: Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (Azure Event Hubs 名前空間を含む) のクエリや検出を行います。 テナント内の適切な (読み取り) アクセス許可を持っており、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙できることを確認します。

- [Azure Resource Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

- [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC について](../role-based-access-control/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを提供する Azure リソースにタグを適用すると、それらのリソースが各分類に論理的に整理されます。

- [タグを作成して使用する方法](/azure/azure-resource-manager/resource-group-using-tags)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: 必要に応じて、タグ付け、管理グループ、および個別のサブスクリプションを使用して、Azure Event Hubs 名前空間と関連リソースの整理と追跡を行います。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

- [追加の Azure サブスクリプションを作成する方法](/azure/billing/billing-create-subscription)

- [管理グループを作成する方法](/azure/governance/management-groups/create)

- [タグを作成して使用する方法](/azure/azure-resource-manager/resource-group-using-tags)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類

- 許可されるリソースの種類

さらに、Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類

- 許可されるリソースの種類

追加情報については、参照先のリンクをご覧ください。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: Azure 条件付きアクセスを使用して Azure Resource Manager とやりとりするユーザーの機能を制限するには、"Microsoft Azure 管理" アプリに対して [アクセスのブロック] を構成します。

- [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../role-based-access-control/conditional-access-azure-management.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: Azure Event Hubs デプロイの標準セキュリティ構成を定義して実装します。 構成を監査または適用するためのカスタム ポリシーを作成するには、**Microsoft.EventHub** 名前空間の Azure Policy エイリアスを使用します。 また、次のように、Azure Event Hubs 用の組み込みのポリシー定義を使用することもできます。

- イベント ハブの診断ログを有効にする必要がある

- イベント ハブは仮想ネットワーク サービス エンドポイントを使用する必要がある

追加情報については、参照先のリンクをご覧ください。

- [Event Hubs 名前空間用の Azure 組み込みポリシー](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub)

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: Event Hubs 対応リソース全体にセキュリティで保護された設定を適用するには、Azure Policy の [deny] と [deploy if not exist] 効果を使用します。 

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

 
- [Azure Policy の効果の詳細について](../governance/policy/concepts/effects.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**: "Microsoft.EventHub" 名前空間で Azure Policy エイリアスを使用して、システム構成を警告処理、監査、適用するためのカスタム ポリシーを作成します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**: "Microsoft.EventHub" 名前空間で Azure Policy エイリアスを使用して、システム構成を警告処理、監査、適用するためのカスタム ポリシーを作成します。 Azure Policy の [audit]、[deny]、[deploy if not exist] 効果を使用して、Azure Event Hubs デプロイおよび関連リソースの構成を自動的に適用します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**:イベント ハブへのアクセスに使用される Azure App Service で実行されている Azure 仮想マシンまたは Web アプリの場合は、マネージド サービス ID を Azure Key Vault と組み合わせて使用して、Azure Event Hubs デプロイの Shared Access Signature の管理を簡素化し、セキュリティで保護します。 Key Vault は、論理的な削除を有効にして構成してください。

- [Azure Active Directory (Azure AD) を使用して Event Hubs リソースにアクセスするためのマネージド ID を認証する](https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest)

- [Event Hubs 用にカスタマー マネージド キーを構成する](configure-customer-managed-key.md)

- [Azure マネージド ID と統合する方法](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [キー コンテナーを作成する方法](/azure/key-vault/quick-create-portal)

- [マネージド ID で Key Vault の認証を提供する方法](/azure/key-vault/managed-identity)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: イベント ハブへのアクセスに使用される Azure App Service で実行されている Azure 仮想マシンまたは Web アプリの場合は、マネージド サービス ID を Azure Key Vault と組み合わせて使用して、Azure Event Hubs を簡素化し、セキュリティで保護します。 Key Vault は、論理的な削除を有効にして構成してください。

マネージド ID を使用して、Azure Active Directory (Azure AD) で自動的に管理される ID を Azure サービスに提供します。 マネージド ID を使用すると、コード内に資格情報を記述することなく、Azure AD 認証をサポートする任意のサービス (Azure Key Vault を含む) に対して認証できます。

- [Azure AD を使用して Event Hubs リソースにアクセスするためのマネージド ID を認証する](https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest)

- [Event Hubs 用にカスタマー マネージド キーを構成する](configure-customer-managed-key.md)

- [マネージド ID を構成する方法](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Azure マネージド ID と統合する方法](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、[Azure セキュリティ ベンチマークの「マルウェアからの防御](../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: Azure Event Hubs、App Service、Data Lake Storage、Blob Storage、Azure Database for PostgreSQL などの非コンピューティング Azure リソースにアップロードされるコンテンツはすべて、事前にスキャンしてください。Microsoft は、これらのインスタンス内のデータにアクセスできません。

Microsoft のマルウェア対策は、Azure サービス (Azure Cache for Redis など) をサポートしている基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-recovery"></a>データの復旧

*詳細については、[Azure セキュリティ ベンチマークの「データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的な自動バックアップを保証する

**ガイダンス**: Azure Event Hubs の geo ディザスター リカバリーを構成します。 Azure リージョン全体またはデータ センター全体 (可用性ゾーンが使用されていない場合) にダウンタイムが発生した場合、別のリージョンまたはデータ センターでデータ処理が続行されることが重要です。 そのため、geo ディザスター リカバリーと geo レプリケーションは、どの企業にとっても重要な機能です。 Azure Event Hubs では、geo ディザスター リカバリーと geo レプリケーションの両方が名前空間レベルでサポートされています。 

- [Azure Event Hubs の geo ディザスター リカバリーについて](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**: Azure Event Hubs では、Azure Storage Service Encryption (Azure SSE) による保存データの暗号化が提供されます。 Event Hubs では、データを格納するために Azure Storage が使用されます。既定では、Azure Storage を使用して格納されるすべてのデータは、Microsoft のマネージド キーを使用して暗号化されます。 Azure Key Vault を使用してカスタマー マネージド キーを格納する場合は、必ずキーの定期的な自動バックアップを実行してください。

次の PowerShell コマンドを使用して、Key Vault シークレットの定期的な自動バックアップを必ず実行します。Backup-AzKeyVaultSecret

- [Azure Event Hubs の保存データを暗号化するためにカスタマー マネージド キーを構成する方法](configure-customer-managed-key.md)

- [Key Vault のシークレットをバックアップする方法](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**:バックアップされたカスタマー マネージド キーの復元をテストします。

- [Azure でキー コンテナーのキーを復元する方法](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**: Key Vault で論理的な削除を有効にして、偶発的または悪意のある削除からキーを保護します。 Azure Event Hubs では、カスタマー マネージド キーに [論理的な削除] と [Do Not Purge]\(消去しない\) が構成されている必要があります。

Event Hubs データのキャプチャに使用される Azure ストレージ アカウントの論理的な削除を構成します。 この機能は、Azure Data Lake Storage Gen 2 ではまだサポートされていない点にご注意ください。

- [Key Vault で論理的な削除を有効にする方法](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [キーを使用したキー コンテナーの設定](configure-customer-managed-key.md)

- [Azure Storage Blob の論理的な削除](https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 担当者の役割を定義している記述されたインシデント対応計画があること、およびインシデント処理/管理のフェーズが存在することを確認します。

- [Azure Security Center 内でワークフロー自動化を構成する方法](../security-center/security-center-planning-and-operations-guide.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**:Security Center は、アラートに重要度を割り当て、各アラートに対処する優先順位を付けることができます。これにより、リソースが侵害されたときに、すぐにアクセスできるようになります。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**: 定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

- [NIST の出版物「IT 計画と機能に関するテスト、トレーニング、演習プログラムのガイド」を参照してください。](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) で、不正なユーザーまたは権限のないユーザーによるお客様のデータへのアクセスが検出された場合に、Microsoft からの連絡先として使用されます。  事後にインシデントをレビューして、問題が解決されていることを確認します。 

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**:連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。

- [連続エクスポートを構成する方法](../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**:セキュリティ アラートやセキュリティに関する推奨事項に対して "Logic Apps" 経由で応答を自動的にトリガーするには、Azure Security Center のワークフローの自動化機能を使用します。

- [ワークフローの自動化と Logic Apps を構成する方法](../security-center/workflow-automation.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、[Azure セキュリティ ベンチマークの「侵入テストとレッド チーム演習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft クラウド侵入テストの実施ルールに従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。 

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="next-steps"></a>次のステップ

- 「[Azure セキュリティ ベンチマーク V2 の概要](/azure/security/benchmarks/overview)」を参照してください。
- [Azure セキュリティ ベースライン](/azure/security/benchmarks/security-baselines-overview)の詳細について学習する

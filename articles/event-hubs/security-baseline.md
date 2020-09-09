---
title: Event Hubs 用の Azure セキュリティ ベースライン
description: Event Hubs 用の Azure セキュリティ ベースライン
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e49a6b8000def0fe175930629b33203adec1b0f5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537056"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Event Hubs 用の Azure セキュリティ ベースライン

Event Hubs 用の Azure セキュリティ ベースラインには、デプロイのセキュリティ体制を改善するために役立つ推奨事項が含まれています。

このサービス用のベースラインは、ベスト プラクティス ガイダンスを使用して Azure 上のクラウド ソリューションをセキュリティで保護する方法について推奨事項を提供する [Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview.md) に基づいて作成されています。

詳細については、[Azure セキュリティ ベースラインの概要](../security/benchmarks/security-baselines-overview.md)に関するページを参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、「[セキュリティ コントロール: ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1:Virtual Network でネットワーク セキュリティ グループまたは Azure Firewall を使用してリソースを保護する

**ガイダンス**: Event Hubs と仮想ネットワーク サービス エンドポイントの統合により、仮想ネットワークにバインドされた仮想マシンなどのワークロードからメッセージング機能へのセキュリティで保護されたアクセスが可能になり、ネットワーク トラフィック パスは両端でセキュリティ保護されます。

少なくとも 1 つの仮想ネットワーク サブネット サービス エンドポイントにバインドすると、それぞれの Event Hubs 名前空間は、仮想ネットワークの承認されたサブネットを除き、どこからのトラフィックも受け入れなくなります。 仮想ネットワークの観点からは、サービス エンドポイントに Event Hubs 名前空間をバインドすると、仮想ネットワーク サブネットからメッセージング サービスへの分離されたネットワーク トンネルが構成されます。 

さらに、プライベート エンドポイントを作成することもできます。これは、Azure Private Link サービスを利用して Azure Event Hubs サービスに非公開かつ安全に接続するネットワーク インターフェイスです。 プライベート エンドポイントは、ご自分の VNet からのプライベート IP アドレスを使用して、サービスを実質的に VNet に取り込みます。 サービスへのすべてのトラフィックをプライベート エンドポイント経由でルーティングできるため、ゲートウェイ、NAT デバイス、ExpressRoute または VPN 接続、パブリック IP アドレスは必要ありません。 

また、ファイアウォールを使用して Azure Event Hubs 名前空間をセキュリティで保護することもできます。 Azure Event Hubs では、受信ファイアウォールをサポートするために IP ベースのアクセス制御に対応しています。 Azure portal または Azure Resource Manager テンプレートを使用するか、Azure CLI または Azure PowerShell を使用してファイアウォール規則を設定することができます。

Azure Event Hubs で仮想ネットワーク サービス エンドポイントを使用する方法: https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

詳細については、「Azure Event Hubs を Azure Private Link と統合する」 (https://docs.microsoft.com/azure/event-hubs/private-link-service ) を参照してください。

Event Hubs 名前空間で仮想ネットワークの統合とファイアウォールを有効にする: https://docs.microsoft.com/azure/event-hubs/event-hubs-tutorial-virtual-networks-firewalls

Azure Event Hubs 名前空間に対する IP ファイアウォール規則を構成する方法: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2:VNet、サブネット、NIC の構成とトラフィックを監視してログに記録する

**ガイダンス**: Azure Security Center を使用し、ネットワークの保護に関する推奨事項に従って、Azure の Event Hubs リソースをセキュリティで保護します。 Azure 仮想マシンを使用してイベント ハブにアクセスする場合は、ネットワーク セキュリティ グループ (NSG) フロー ログを有効にし、トラフィック監査のためにログをストレージ アカウントに送信します。

NSG フロー ログを有効にする方法: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Azure Security Center によって提供されるネットワークのセキュリティの概要: https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**:イベント ハブに関連付けられた仮想ネットワーク上で DDoS Protection Standard を有効にして、分散型サービス拒否 (DDoS) 攻撃から保護します。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある、または未使用のインターネット IP アドレスとの通信を拒否します。

DDoS 保護を構成する方法: [https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](../virtual-network/manage-ddos-protection.md)

Azure Security Center の統合された脅威インテリジェンスの詳細については、 https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer を参照してください。

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5:ネットワーク パケットとフロー ログを記録する

**ガイダンス**: Azure 仮想マシンを使用してイベント ハブにアクセスする場合は、ネットワーク セキュリティ グループ (NSG) フロー ログを有効にし、トラフィック監査のためにログをストレージ アカウントに送信します。 また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

異常なアクティビティを調査する必要がある場合は、Network Watcher パケット キャプチャを有効にします。

NSG フロー ログを有効にする方法: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Traffic Analytics を有効にして使用する方法: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Network Watcher を有効にする方法: https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**: Azure 仮想マシンを使用してイベント ハブにアクセスする場合は、Azure Marketplace から、ペイロード検査能力を備えた IDS または IPS 機能をサポートするオファーを選択します。 組織で、ペイロード検査に基づく侵入の検出または防止を行う必要がない場合は、Azure Event Hubs に組み込まれたファイアウォール機能を使用できます。 ファイアウォール規則を使用して、Event Hubs 名前空間へのアクセスを、限られた範囲の IP アドレスまたは特定の IP アドレスに制限することができます。

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

指定された IP アドレスに対して Event Hubs のファイアウォール規則を追加する方法:

 https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Azure Security Center の監視**: まだ使用できません

**責任**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure Policy を使用して、Azure Event Hubs 名前空間に関連付けられているネットワーク リソースの標準的なセキュリティ構成を定義して実装します。 "Microsoft.EventHub" と "Microsoft.Network" の名前空間で Azure Policy エイリアスを使用して、Event Hubs 名前空間のネットワーク構成を監査または適用するためのカスタム ポリシーを作成します。 また、次のように、Azure Event Hubs に関連する組み込みのポリシー定義を使用することもできます。

- イベント ハブは仮想ネットワーク サービス エンドポイントを使用する必要があります。

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Event Hubs 名前空間用の組み込み Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub



ネットワークに関する Azure Policy のサンプル: https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network



Azure Blueprint を作成する方法: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: イベント ハブに関連付けられているネットワーク セキュリティおよびトラフィック フローに関連したネットワーク サービス グループやその他のリソースにタグを使用します。

タグを作成して使用する方法: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: Azure アクティビティ ログを使用して、ネットワーク リソース構成を監視し、Azure Event Hubs に関連したネットワーク リソースの変更を検出します。 重要なネットワーク リソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

Azure アクティビティ ログ イベントを表示して取得する方法: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Azure Monitor でアラートを作成する方法: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、「[セキュリティ コントロール: ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**: 適用できません。Microsoft では、Azure Event Hubs などの Azure リソースに使用するタイム ソースをタイムスタンプとしてログ内に保持します。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: Azure Monitor 内で、アクティビティ ログおよびイベント ハブの診断設定内のイベント ハブに関連するログを構成し、クエリ対象の Log Analytics ワークスペースまたは長期アーカイブ ストレージのストレージ アカウントにログを送信します。

Azure Event Hubs の診断設定を構成する方法: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Azure アクティビティ ログについて: https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: Azure Event Hubs 名前空間の診断設定を有効にします。 Azure Event Hubs の診断設定には、アーカイブ ログ、操作ログ、自動スケーリング ログの 3 つのカテゴリがあります。 操作ログを有効にして、Event Hubs の操作中に発生したことに関する情報 (特に、イベント ハブの作成などの操作の種類、使用されたリソース、操作の状態) を取得します。

さらに、Azure アクティビティ ログの診断設定を有効にして、それを Azure ストレージ アカウント、イベント ハブ、または Log Analytics ワークスペースに送信することもできます。 アクティビティ ログは、Azure Event Hubs およびその他のリソースで実行された操作に関する分析情報を提供します。 アクティビティ ログを使用すると、Azure Event Hubs 名前空間で実行された書き込み操作 (PUT、POST、DELETE) について、"いつ誰が何を" 行ったのかを確認できます。

Azure Event Hubs の診断設定を有効にする方法: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Azure アクティビティ ログの診断設定を有効にする方法: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**:Azure Monitor 内で、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定し、イベント ハブ関連のインシデントを取得して確認します。

Log Analytics ワークスペースのログ保持パラメーターを設定する方法: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: 異常な動作についてログを分析および監視し、イベント ハブに関連する結果を定期的に確認します。 ログを確認し、ログ データに対してクエリを実行するには、Azure Monitor の Log Analytics を使用します。 または、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードすることもできます。
 

Log Analytics ワークスペースの詳細については、 https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal を参照してください。

Azure Monitor でカスタム クエリを実行する方法: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

Azure Sentinel をオンボードする方法: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: Azure Monitor 内で、アクティビティ ログおよびイベント ハブの診断設定内の Azure Event Hubs に関連するログを構成し、クエリ対象の Log Analytics ワークスペースまたは長期アーカイブ ストレージのストレージ アカウントにログを送信します。 Log Analytics ワークスペースを使用して、セキュリティ ログおよびイベントで異常なアクティビティが検出された場合のアラートを作成します。

または、Azure Sentinel に対してデータを有効にしてオンボードすることもできます。 

Azure アクティビティ ログについて: https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

Azure Event Hubs の診断設定を構成する方法: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Log Analytics ワークスペースのログ データに関するアラートを送信する方法: https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

Azure Sentinel をオンボードする方法: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center の監視**: まだ使用できません

**責任**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**: 適用できません。Event Hubs では、マルウェア対策ログを処理しません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**: 適用できません。Event Hubs では、DNS 関連のログの処理や生成を行いません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、「[セキュリティ コントロール: ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Azure Active Directory (AD) には、明示的に割り当てる必要があり、クエリ可能な組み込みロールがあります。 Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。 

PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**:Event Hubs へのコントロール プレーン アクセスは、Azure Active Directory (AD) を介して制御されます。 Azure AD には既定のパスワードという概念がありません。

Event Hubs へのデータ プレーン アクセスは、マネージド ID またはアプリの登録と Shared Access Signature を使用して、Azure AD を介して制御されます。 Shared Access Signature は、イベント ハブに接続するクライアントによって使用され、いつでも再生成することができます。

Event Hubs の Shared Access Signature について: https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Azure Security Center ID とアクセス管理を使用して、管理者アカウントの数を監視します。

さらに、専用管理者アカウントを追跡できるように、Azure Security Center または組み込みの Azure ポリシーの次のような推奨事項を使用することもできます。

- 複数の所有者がサブスクリプションに割り当てられている必要がある

- 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある

- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある

Azure Security Center を使用して ID およびアクセスを監視する方法 (プレビュー): https://docs.microsoft.com/azure/security-center/security-center-identity-access

Azure Policy を使用する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

**ガイダンス**:Microsoft Azure では、Azure Active Directory (AD) をベースとする、リソースとアプリケーションの統合されたアクセス制御管理が提供されます。 Azure Event Hubs で Azure AD を使用する主な利点は、資格情報をコード内に格納する必要がなくなることです。 代わりに、Microsoft ID プラットフォームから OAuth 2.0 アクセス トークンを要求することができます。 トークンを要求するリソース名は https:\//eventhubs.azure.net/ です。 Azure AD によって、アプリケーションを実行しているセキュリティ プリンシパル (ユーザー、グループ、またはサービス プリンシパル) が認証されます。 認証が成功すると、Azure AD からアプリケーションにアクセス トークンが返されます。アプリケーションでは、このアクセス トークンを使用して Azure Event Hubs リソースへの要求を承認できます。

Event Hubs リソースにアクセスするために Azure Active Directory でアプリケーションを認証する方法: https://docs.microsoft.com/azure/event-hubs/authenticate-application

Azure AD を使用した SSO について: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory Multi-Factor Authentication (MFA) を有効にし、イベント ハブ対応リソースを保護するための Azure Security Center ID とアクセス管理の推奨事項に従います。

Azure で MFA を有効にする方法: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Azure Security Center 内で ID とアクセスを監視する方法: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**: Multi-Factor Authentication (MFA) が構成された特権アクセス ワークステーション (PAW) を使用してログインし、イベント ハブ対応リソースを構成します。

特権アクセス ワークステーションについて: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Azure で MFA を有効にする方法: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティを記録してアラートを生成する

**ガイダンス**: Azure Active Directory (AD) Privileged Identity Management (PIM) を使用して、環境内で疑わしいアクティビティまたは安全ではないアクティビティが発生したときにログとアラートを生成します。 Azure AD のリスク検出を使用して、危険なユーザーの行動に関するアラートとレポートを表示します。 追加のログ記録を行うには、Azure Security Center のリスク検出アラートを Azure Monitor に送信し、アクション グループを使用してカスタムのアラートまたは通知を構成します。

Privileged Identity Management (PIM) をデプロイする方法: https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Azure AD のリスク検出について: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

カスタムのアラートおよび通知用にアクション グループを構成する方法: https://docs.microsoft.com/azure/azure-monitor/platform/action-groups

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: 条件付きアクセスのネームド ロケーションを使用して、IP アドレス範囲または国/地域の特定の論理グループからのアクセスのみを許可します。



Azure でネームド ロケーションを構成する方法: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Event Hubs などの Azure リソースの主要な認証および承認システムとして Azure Active Directory (AAD) を使用します。 これにより、ロールベースのアクセス制御 (RBAC) で機密性の高いリソースを管理できるようになります。

 Azure AD インスタンスを作成して構成する方法: https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Azure Event Hubs と Azure Active Directory (AAD) を統合する方法については、「Azure Active Directory を使用して Event Hubs リソースへのアクセスを承認する」 (https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory ) を参照してください。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory (AD) では、古いアカウントの検出に役立つログが提供されます。 また、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。

さらに、Event Hubs の Shared Access Signature を定期的にローテーションさせます。

Azure AD のレポートについて: https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Azure ID アクセスレビューの使用方法: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

Event Hubs の Shared Access Signature について: https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 非アクティブ化されたアカウントへのアクセス試行を監視する

**ガイダンス**: Azure Active Directory (AD) サインイン アクティビティ、監査、およびリスク イベント ログのソースにアクセスできるため、任意の SIEM または監視ツールと統合することができます。

このプロセスを効率化するには、Azure AD ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics 内で必要なログ アラートを構成できます。

Azure Activity Logs を Azure Monitor に統合する方法: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Azure Active Directory を使用して Event Hubs リソースへのアクセスを承認する: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラートを生成する

**ガイダンス**: 検出された、Event Hubs 対応リソースに関連する疑わしいアクションに対する自動応答を構成するには、Azure Active Directory の Identity Protection とリスク検出機能を使用します。 組織のセキュリティ対応を実装するには、Azure Sentinel によって自動応答を有効にする必要があります。

Azure AD の危険なサインインを表示する方法: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

ID Protection のリスク ポリシーを構成して有効にする方法: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Azure Sentinel をオンボードする方法: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**:現時点ではご利用いただけません。カスタマー ロックボックスは、Event Hubs ではまだサポートされていません。

カスタマー ロックボックスでサポートされているサービスの一覧: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure Security Center の監視**: 現在は使用できません

**責任**: 現在は使用できません

## <a name="data-protection"></a>データ保護

*詳細については、「[セキュリティ コントロール: データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: Event Hubs に関連するリソースに対してタグを使用すると、機密情報を格納または処理する Azure リソースの追跡に役立ちます。

タグを作成して使用する方法: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 Event Hubs 名前空間は、サービス エンドポイントを有効にした仮想ネットワークで分離し、適切なタグを付ける必要があります。

また、ファイアウォールを使用して Azure Event Hubs 名前空間をセキュリティで保護することもできます。 Azure Event Hubs では、受信ファイアウォールをサポートするために IP ベースのアクセス制御に対応しています。 Azure portal または Azure Resource Manager テンプレートを使用するか、Azure CLI または Azure PowerShell を使用してファイアウォール規則を設定することができます。

追加の Azure サブスクリプションを作成する方法: https://docs.microsoft.com/azure/billing/billing-create-subscription

管理グループを作成する方法: https://docs.microsoft.com/azure/governance/management-groups/create

Azure Event Hubs 名前空間に対する IP ファイアウォール規則を構成する: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

タグを作成して使用する方法: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

仮想ネットワークを作成する方法: https://docs.microsoft.com/azure/virtual-network/quick-create-portal

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: 仮想マシンを使用してイベント ハブにアクセスする場合は、仮想ネットワーク、サービス エンドポイント、Event Hubs ファイアウォール、ネットワーク セキュリティ グループ、サービス タグを使用して、データ流出の可能性を軽減します。

Microsoft では、Azure Event Hubs 用の基になるインフラストラクチャを管理し、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。

Azure Event Hubs 名前空間に対する IP ファイアウォール規則を構成する: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Azure Event Hubs で使用する仮想ネットワーク サービス エンドポイントについて: https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Azure Event Hubs を Azure Private Link と統合する: https://docs.microsoft.com/azure/event-hubs/private-link-service

ネットワーク セキュリティ グループとサービス タグについて: https://docs.microsoft.com/azure/virtual-network/security-overview

Azure での顧客データの保護の概要: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**: Azure Event Hubs は、既定により、TLS で暗号化された通信を適用します。 現在、TLS バージョン 1.0、1.1、および 1.2 がサポートされています。 ただし、TLS 1.0 と 1.1 は業界全体で非推奨になる予定であるため、可能であれば TLS 1.2 を使用してください。

Event Hubs のセキュリティ機能については、ネットワーク セキュリティに関するページ (https://docs.microsoft.com/azure/event-hubs/network-security ) を参照してください。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: Azure Event Hubs では、データの識別、分類、損失防止機能はまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

Azure での顧客データの保護の概要: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**: Azure Event Hubs は、Azure Active Directory (Azure AD) を使用して Event Hubs リソースへの要求を承認することをサポートしています。 Azure AD では、ロールベースのアクセス制御 (RBAC) を使用して、サービス プリンシパル (ユーザーまたはアプリケーションのサービス プリンシパルである可能性があります) にアクセス許可を付与します。

Azure Event Hubs の Azure AD RBAC と使用可能なロールについて: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。

Microsoft では、Event Hubs 用の基になるインフラストラクチャを管理し、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。

Azure での顧客データの保護の概要: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: Azure Event Hubs で、Microsoft のマネージド キーまたはカスタマー マネージド キーのいずれかを使用した保存データの暗号化のオプションがサポートされています。 この機能を使用すると、Azure Event Hubs の保存データ暗号化に使用されるカスタマー マネージド キーへのアクセスの作成、ローテーション、無効化、取り消しができます。

Azure Event Hubs を暗号化するためにカスタマー マネージド キーを構成する方法: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**:Azure Event Hubs の運用インスタンスやその他の重要なリソースまたは関連リソースへの変更がいつ発生したかに関するアラートを作成するには、Azure Monitor と Azure アクティビティ ログを使用します。

Azure アクティビティ ログ イベントのアラートを作成する方法: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、「[セキュリティ コントロール: 脆弱性の管理](../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**: 適用できません。Microsoft では、Event Hubs をサポートしている基になるシステムで脆弱性の管理を行います。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**: 適用できません。Microsoft では、Event Hubs をサポートしている基になるシステムでパッチの管理を行います。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3:サードパーティの自動化されたソフトウェア修正プログラム管理ソリューションを展開する

**ガイダンス**: 適用できません。ベンチマークは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**: 適用できません。Microsoft では、Event Hubs をサポートしている基になるシステムで脆弱性の管理を行います。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**: 適用できません。Microsoft では、Event Hubs をサポートしている基になるシステムで脆弱性の管理を行います。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、「[セキュリティ コントロール: インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-azure-asset-discovery"></a>6.1:Azure Asset Discovery を使用する

**ガイダンス**: Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (Azure Event Hubs 名前空間を含む) のクエリや検出を行います。 テナント内の適切な (読み取り) アクセス許可を持っており、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙できることを確認します。

Azure Resource Graph を使用してクエリを作成する方法: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Azure サブスクリプションを表示する方法: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Azure RBAC の概要: https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを提供する Azure リソースにタグを適用すると、それらのリソースが各分類に論理的に整理されます。

タグを作成して使用する方法: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: 必要に応じて、タグ付け、管理グループ、および個別のサブスクリプションを使用して、Azure Event Hubs 名前空間と関連リソースの整理と追跡を行います。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

追加の Azure サブスクリプションを作成する方法: https://docs.microsoft.com/azure/billing/billing-create-subscription

管理グループを作成する方法: https://docs.microsoft.com/azure/governance/management-groups/create

タグを作成して使用する方法: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4:承認された Azure リソースとソフトウェア タイトルのインベントリを管理する

**ガイダンス**:適用できません。この推奨事項は、コンピューティング リソースと Azure 全体を対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類

- 許可されるリソースの種類

さらに、Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Graph を使用してクエリを作成する方法: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**:適用できません。この推奨事項は、コンピューティング リソースと Azure 全体を対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類

- 許可されるリソースの種類

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy を使用して特定のリソースの種類を拒否する方法: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="610-implement-approved-application-list"></a>6.10:承認されたアプリケーションの一覧を実装する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>ユーザーがスクリプトを使用して Azure Resource Manager と対話する機能を制限する</div>

**ガイダンス**: Azure 条件付きアクセスを使用して Azure Resource Manager とやりとりするユーザーの機能を制限するには、"Microsoft Azure 管理" アプリに対して [アクセスのブロック] を構成します。

条件付きアクセスを構成して Azure Resource Manager へのアクセスをブロックする方法: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12:コンピューティング リソース内でスクリプトを実行するユーザーの機能を制限する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、「[セキュリティ コントロール: セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: Azure Event Hubs デプロイの標準セキュリティ構成を定義して実装します。 構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.EventHub" 名前空間で Azure Policy エイリアスを使用します。 また、次のように、Azure Event Hubs 用の組み込みのポリシー定義を使用することもできます。

- イベント ハブの診断ログを有効にする必要がある

- イベント ハブは仮想ネットワーク サービス エンドポイントを使用する必要がある

Event Hubs 名前空間用の Azure 組み込みポリシー: https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub

使用可能な Azure Policy エイリアスを表示する方法: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: Event Hubs 対応リソース全体にセキュリティで保護された設定を適用するには、Azure ポリシーの [deny] と [deploy if not exist] を使用します。 

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

 
Azure Policy の効果の詳細については、 https://docs.microsoft.com/azure/governance/policy/concepts/effects を参照してください。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: Event Hubs または関連リソースにカスタムの Azure Policy 定義を使用している場合は、Azure Repos を使用してコードを安全に格納して管理します。

Azure DevOps でコードを格納する方法: https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos のドキュメント: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="77-deploy-system-configuration-management-tools"></a>7.7:システム構成管理ツールをデプロイする

**ガイダンス**: "Microsoft.EventHub" 名前空間で Azure Policy エイリアスを使用して、システム構成を警告処理、監査、適用するためのカスタム ポリシーを作成します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用のシステム構成管理ツールをデプロイする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9:Azure サービスの自動構成監視を実装する

**ガイダンス**: "Microsoft.EventHub" 名前空間で Azure Policy エイリアスを使用して、システム構成を警告処理、監査、適用するためのカスタム ポリシーを作成します。 Azure ポリシーの [audit]、[deny]、[deploy if not exist] を使用して、Azure Event Hubs デプロイおよび関連リソースの構成を自動的に適用します。

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**:イベント ハブへのアクセスに使用される Azure App Service で実行されている Azure 仮想マシンまたは Web アプリの場合は、マネージド サービス ID を Azure Key Vault と組み合わせて使用して、Azure Event Hubs デプロイの Shared Access Signature の管理を簡素化し、セキュリティで保護します。 Key Vault の論理的な削除を確実に有効にします。

Azure Active Directory を使用して Event Hubs リソースにアクセスするためのマネージド ID を認証する: https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest

Event Hubs 用にカスタマー マネージド キーを構成する: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Azure マネージド ID と統合する方法: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

キー コンテナーを作成する方法: https://docs.microsoft.com/azure/key-vault/quick-create-portal

マネージド ID で Key Vault の認証を提供する方法: https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: イベント ハブへのアクセスに使用される Azure App Service で実行されている Azure 仮想マシンまたは Web アプリの場合は、マネージド サービス ID を Azure Key Vault と組み合わせて使用して、Azure Event Hubs を簡素化し、セキュリティで保護します。 Key Vault の論理的な削除を確実に有効にします。

マネージド ID を使用して、Azure Active Directory (AD) で自動的に管理される ID を Azure サービスに提供します。 マネージド ID を使用すると、コード内に資格情報を記述することなく、Azure AD 認証をサポートする任意のサービス (Azure Key Vault を含む) に対して認証できます。

Azure Active Directory を使用して Event Hubs リソースにアクセスするためのマネージド ID を認証する: https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest 

Event Hubs 用にカスタマー マネージド キーを構成する: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

マネージド ID を構成する方法: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Azure マネージド ID と統合する方法: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

資格情報スキャナーを設定する方法: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、「[セキュリティ コントロール: マルウェアからの防御](../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1:一元管理されるマルウェア対策ソフトウェアを使用する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

Microsoft マルウェア対策は、Azure サービス (Azure App Service など) をサポートする基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: Azure Event Hubs、App Service、Data Lake Storage、Blob Storage、Azure Database for PostgreSQL などの非コンピューティング Azure リソースにアップロードされるコンテンツはすべて、事前にスキャンしてください。Microsoft は、これらのインスタンス内のデータにアクセスできません。

Microsoft のマルウェア対策は、Azure サービス (Azure Cache for Redis など) をサポートしている基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="data-recovery"></a>データの復旧

*詳細については、「[セキュリティ コントロール: データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的に自動バックアップを行う

**ガイダンス**: Azure Event Hubs の geo ディザスター リカバリーを構成します。 Azure リージョン全体またはデータ センター全体 (可用性ゾーンが使用されていない場合) にダウンタイムが発生した場合、別のリージョンまたはデータ センターでデータ処理が続行されることが重要です。 そのため、geo ディザスター リカバリーと geo レプリケーションは、どの企業にとっても重要な機能です。 Azure Event Hubs では、geo ディザスター リカバリーと geo レプリケーションの両方が名前空間レベルでサポートされています。 

Azure Event Hubs の geo ディザスター リカバリーについて: https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、カスタマー マネージド キーをバックアップする

**ガイダンス**: Azure Event Hubs では、Azure Storage Service Encryption (Azure SSE) による保存データの暗号化が提供されます。 Event Hubs では、データを格納するために Azure Storage が使用されます。既定では、Azure Storage を使用して格納されるすべてのデータは、Microsoft のマネージド キーを使用して暗号化されます。 Azure Key Vault を使用してカスタマー マネージド キーを格納する場合は、必ずキーの定期的な自動バックアップを実行してください。

次の PowerShell コマンドを使用して、Key Vault シークレットの定期的な自動バックアップを必ず実行します。Backup-AzKeyVaultSecret

Azure Event Hubs の保存データを暗号化するためにカスタマー マネージド キーを構成する方法: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Key Vault のシークレットをバックアップする方法: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: バックアップされたカスタマー マネージド キーの復元をテストします。

 

Azure で Key Vault のキーを復元する方法: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を確保する

**ガイダンス**: Key Vault で論理的な削除を有効にして、偶発的または悪意のある削除からキーを保護します。 Azure Event Hubs では、カスタマー マネージド キーに [論理的な削除] と [Do Not Purge]\(消去しない\) が構成されている必要があります。

Event Hubs データのキャプチャに使用される Azure Storage アカウントの論理的な削除を構成します。 この機能は、Azure Data Lake Storage Gen 2 ではまだサポートされていない点にご注意ください。

Key Vault で論理的な削除を有効にする方法: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

キーを使用したキー コンテナーの設定: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Azure Storage Blob の論理的な削除: https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、「[セキュリティ コントロール: インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**:インシデント処理/管理のフェーズと担当者の役割を定義している記述されたインシデント対応計画があることを確認します。

Azure Security Center 内でワークフロー自動化を構成する方法: https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**:Security Center は、アラートに重要度を割り当て、各アラートに対処する優先順位を付けることができます。これにより、リソースが侵害されたときに、すぐにアクセスできるようになります。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**: 定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

NIST の出版物を参照してください。IT の計画と機能に関するテスト、トレーニング、演習プログラムのガイド: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) で、不正なユーザーまたは権限のないユーザーによるお客様のデータへのアクセスが検出された場合に、Microsoft からの連絡先として使用されます。  事後にインシデントをレビューして、問題が解決されていることを確認します。 

Azure Security Center のセキュリティ連絡先を設定する方法: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**:連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用して、アラートの Sentinel のストリーミングを実行できます。

連続エクスポートを構成する方法: https://docs.microsoft.com/azure/security-center/continuous-export

Azure Sentinel にアラートをストリーミングする方法: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**:セキュリティ アラートやセキュリティに関する推奨事項に対して "Logic Apps" 経由で応答を自動的にトリガーするには、Azure Security Center のワークフローの自動化機能を使用します。

ワークフローの自動化と Logic Apps を構成する方法: https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、「[セキュリティ コントロール: 侵入テストとレッド チーム演習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、60 日以内に確実に修復されるようにします

**ガイダンス**: 侵入テストが Microsoft のポリシーに違反しないようにするために、Microsoft の活動規則 (https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 ) に従ってください。
Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施の詳細については、こちらの https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e で確認できます。

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](../security/benchmarks/overview.md)に関するページを参照する
- [Azure セキュリティ ベースライン](../security/benchmarks/security-baselines-overview.md)の詳細について学習する

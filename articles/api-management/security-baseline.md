---
title: API Management 用の Azure セキュリティ ベースライン
description: API Management 用の Azure セキュリティ ベースライン
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d6c954d6336a0b88f57ba1f70571263af5d1c583
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89229805"
---
# <a name="azure-security-baseline-for-api-management"></a>API Management 用の Azure セキュリティ ベースライン

API Management 用の Azure セキュリティ ベースラインには、ご自身のデプロイのセキュリティ体制を改善するために役立つ推奨事項が含まれています。

このサービス用のベースラインは、ベスト プラクティス ガイダンスを使用して Azure 上のクラウド ソリューションをセキュリティで保護する方法について推奨事項を提供する [Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview.md) に基づいて作成されています。

詳細については、「[Azure セキュリティ ベースラインの概要](../security/benchmarks/security-baselines-overview.md)」を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、「[セキュリティ コントロール: ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1:Virtual Network でネットワーク セキュリティ グループまたは Azure Firewall を使用してリソースを保護する

**ガイダンス**: Azure API Management は、Azure Virtual Network (VNet) の内部にデプロイできるため、ネットワーク内でバックエンド サービスにアクセスできます。 開発者ポータルと API Management ゲートウェイは、インターネット (外部) から、または VNet 内 (内部) でのみアクセスできるように構成可能です。
- 外部: API Management のゲートウェイと開発者ポータルには、パブリック インターネットから外部ロード バランサーを使用してアクセスできます。 ゲートウェイは、仮想ネットワーク内のリソースにアクセスできます。
- 内部: API Management のゲートウェイと開発者ポータルには、仮想ネットワークから内部ロード バランサーを使用してアクセスできます。 ゲートウェイは、仮想ネットワーク内のリソースにアクセスできます。

API Management がデプロイされるサブネットへの受信トラフィックと送信トラフィックは、ネットワーク セキュリティ グループを使用して制御できます。

* [仮想ネットワークで Azure API Management を使用する方法](./api-management-using-with-vnet.md)

* [内部仮想ネットワークでの Azure API Management サービスの使用](./api-management-using-with-internal-vnet.md)

* [内部 VNET 内の API Management と Application Gateway の統合](./api-management-howto-integrate-internal-vnet-appgateway.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2:VNet、サブネット、NIC の構成とトラフィックを監視してログに記録する

**ガイダンス**: API Management がデプロイされるサブネットへの受信トラフィックと送信トラフィックは、ネットワーク セキュリティ グループ (NSG) を使用して制御できます。 NSG を API Management サブネットにデプロイし、NSG フロー ログを有効にして、トラフィック監査のためにログを Azure Storage アカウントに送信します。 また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

注意:API Management サブネット上で NSG を構成する場合は、開いておく必要のある一連のポートがあります。 これらのポートのいずれかが利用できない場合、API Management は正しく動作しない可能性があり、アクセス不能になる場合があります。

* [Azure API Management の NSG 構成について](./api-management-using-with-vnet.md#-common-network-configuration-issues)

* [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**: 重要な Web/HTTP API を保護するには、内部モードで Virtual Network (VNet) 内に API Management を構成し、Azure Application Gateway を構成します。 Application Gateway は PaaS サービスの 1 つです。 リバース プロキシとしての役目を果たし、L7 の負荷分散、ルーティング、Web アプリケーション ファイアウォール (WAF)、およびその他のサービスを提供します。

内部 VNet にプロビジョニング済みの API Management と Application Gateway フロントエンドを組み合わせることにより、次のシナリオが実現されます。
- 内部コンシューマーと外部コンシューマーの両方にすべての API を公開するために 1 つの API Management リソースを使用する。
- 外部コンシューマーに API のサブセットを公開するために 1 つの API Management リソースを使用する。
- パブリック インターネットから API Management へのアクセスのオン/オフを切り替える方法を提供する。

注:この機能は、API Management の Premium レベルと Developer レベルで使用できます。

* [内部 VNet 内の API Management と Application Gateway を統合する方法](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [Azure Application Gateway について](../application-gateway/index.yml)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: 内部モードで Virtual Network (VNet) 内に API Management を構成し、Azure Application Gateway を構成します。 Application Gateway は PaaS サービスの 1 つです。 リバース プロキシとしての役目を果たし、L7 の負荷分散、ルーティング、Web アプリケーション ファイアウォール (WAF)、およびその他のサービスを提供します。

内部 VNet にプロビジョニング済みの API Management と Application Gateway フロントエンドを組み合わせることにより、次のシナリオが実現されます。
- 内部コンシューマーと外部コンシューマーの両方にすべての API を公開するために 1 つの API Management リソースを使用する。
- 外部コンシューマーに API のサブセットを公開するために 1 つの API Management リソースを使用する。
- パブリック インターネットから API Management へのアクセスのオン/オフを切り替える方法を提供する。

注:この機能は、API Management の Premium レベルと Developer レベルで使用できます。

API Management のデプロイに関連付けられた VNet 上で Azure DDoS Protection Standard を有効にして、分散型サービス拒否 (DDoS) 攻撃から保護します。

Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある、または未使用のインターネット IP アドレスとの通信を拒否します。

* [内部 VNet 内の API Management と Application Gateway を統合する方法](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [Azure Application Gateway について](../application-gateway/index.yml)

* [Azure DDoS Protection Standard を構成する方法](../virtual-network/manage-ddos-protection.md)

* [Azure Security Center の統合された脅威インテリジェンスについて](../security-center/threat-protection.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5:ネットワーク パケットとフロー ログを記録する

**ガイダンス**: API Management がデプロイされるサブネットへの受信トラフィックと送信トラフィックは、ネットワーク セキュリティ グループ (NSG) を使用して制御できます。 NSG を API Management サブネットにデプロイし、NSG フロー ログを有効にして、トラフィック監査のためにログを Azure Storage アカウントに送信します。 また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

注意:API Management サブネット上で NSG を構成する場合は、開いておく必要のある一連のポートがあります。 これらのポートのいずれかが利用できない場合、API Management は正しく動作しない可能性があり、アクセス不能になる場合があります。

* [Azure API Management の NSG 構成について](./api-management-using-with-vnet.md#-common-network-configuration-issues)

* [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**: 内部モードで Virtual Network (VNet) 内に API Management を構成し、Azure Application Gateway を構成します。 Application Gateway は PaaS サービスの 1 つです。 リバース プロキシとしての役目を果たし、L7 の負荷分散、ルーティング、Web アプリケーション ファイアウォール (WAF)、およびその他のサービスを提供します。 Application Gateway の WAF は、一般的なセキュリティの悪用と脆弱性からの保護を提供し、次の 2 つのモードで実行できます。
- 検出モード:すべての脅威アラートを監視してログに記録します。 [診断] セクションで Application Gateway の診断のログ記録をオンにすることができます。 WAF のログが選択され、オンになっていることを確認する必要があります。 Web アプリケーション ファイアウォールは、検出モードで動作しているときに受信要求をブロックしません。
- 防止モード:規則で検出された侵入や攻撃をブロックします。 攻撃者に "403 不正アクセス" の例外が送信され、接続が終了します。 防止モードでは、このような攻撃を WAF ログに記録します。

内部 VNet にプロビジョニング済みの API Management と Application Gateway フロントエンドを組み合わせることにより、次のシナリオが実現されます。
- 内部コンシューマーと外部コンシューマーの両方にすべての API を公開するために 1 つの API Management リソースを使用する。
- 外部コンシューマーに API のサブセットを公開するために 1 つの API Management リソースを使用する。
- パブリック インターネットから API Management へのアクセスのオン/オフを切り替える方法を提供する。

注:この機能は、API Management の Premium レベルと Developer レベルで使用できます。

* [内部 VNet 内の API Management と Application Gateway を統合する方法](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [Azure Application Gateway の WAF について](../web-application-firewall/ag/ag-overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: Web/HTTP API に送られるトラフィックを管理するには、外部モードまたは内部モードで、App Service Environment に関連付けられた Virtual Network (VNet) に API Management をデプロイします。

内部モードでは、API Management の前に Azure Application Gateway を構成します。 Application Gateway は PaaS サービスの 1 つです。 リバース プロキシとしての役目を果たし、L7 の負荷分散、ルーティング、Web アプリケーション ファイアウォール (WAF)、およびその他のサービスを提供します。 Application Gateway の WAF は、一般的なセキュリティの悪用と脆弱性からの保護を提供します。

内部 VNet にプロビジョニング済みの API Management と Application Gateway フロントエンドを組み合わせることにより、次のシナリオが実現されます。
- 内部コンシューマーと外部コンシューマーの両方にすべての API を公開するために 1 つの API Management リソースを使用する。
- 外部コンシューマーに API のサブセットを公開するために 1 つの API Management リソースを使用する。
- パブリック インターネットから API Management へのアクセスのオン/オフを切り替える方法を提供する。

注:この機能は、API Management の Premium レベルと Developer レベルで使用できます。

* [プライベート API を外部コンシューマーに公開する方法](/azure/architecture/example-scenario/apps/publish-internal-apis-externally)

* [VNet 内で API Management を使用する方法](./api-management-using-with-vnet.md)

* [Azure Application Gateway 上の Azure Web アプリケーション ファイアウォール](../web-application-firewall/ag/ag-overview.md)

* [Azure Application Gateway について](../application-gateway/overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: Virtual Network (VNet) サービス タグを使用して、API Management サブネット上で使用されるネットワーク セキュリティ グループ (NSG) のネットワーク アクセス制御を定義します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 (ApiManagement など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

注意:API Management サブネット上で NSG を構成する場合は、開いておく必要のある一連のポートがあります。 これらのポートのいずれかが利用できない場合、API Management は正しく動作しない可能性があり、アクセス不能になる場合があります。

* [サービス タグの概要と使用方法](../virtual-network/service-tags-overview.md)

* [API Management に必要なポート](./api-management-using-with-vnet.md#-common-network-configuration-issues)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure API Management のデプロイに関連するネットワーク設定の標準的なセキュリティ構成を定義して、実装します。 Azure API Management のデプロイと関連リソースのネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.ApiManagement" および "Microsoft.Network" 名前空間内で Azure Policy エイリアスを使用します。 また、次のように、Azure Virtual Networks 用の組み込みのポリシー定義を使用することもできます。
- DDoS Protection Standard を有効にする必要がある

また、Azure Blueprints を使用して、Azure Resource Manager テンプレート、ロールベースのアクセス制御 (RBAC)、ポリシーなどの主要な環境成果物を単一のブループリント定義にパッケージ化することによって大規模な Azure デプロイを簡略化することもできます。 ブループリントを新しいサブスクリプションと環境に簡単に適用し、バージョン管理によって制御と管理を微調整できます。

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

* [Azure Blueprint を作成する方法](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: ネットワーク セキュリティおよびトラフィック フローに関連したネットワーク セキュリティ グループ (NSG) やその他のリソースにタグを使用します。 個々の NSG 規則については、[説明] フィールドを使用して、ネットワークとの間のトラフィックを許可する規則のビジネス ニーズや期間 (など) を指定できます。

* [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

* [仮想ネットワークを作成する方法](../virtual-network/quick-create-portal.md)

* [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: Azure アクティビティ ログを使用して、ネットワーク リソース構成を監視し、Azure API Management のデプロイに関連付けられたネットワーク リソースへの変更を検出します。 重要なネットワーク リソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

* [Azure アクティビティ ログ イベントを表示して取得する方法](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Azure Monitor でアラートを作成する方法](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、「[セキュリティ コントロール: ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**: Microsoft では、Azure API Management のタイム ソースを保持しています。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: Azure Monitor 内で、Log Analytics ワークスペースを使用してクエリと分析を実行したり、長期/アーカイブ ストレージまたはオフライン分析用にログを Azure Storage に送信したり、Azure Event Hubs を使用して Azure や別の場所にある他の分析ソリューションにログをエクスポートしたりします。 Azure API Management は、既定でログとメトリックを Azure Monitor に出力します。 ログの詳細度は、サービス全体および API ごとに構成できます。

Azure Monitor に加えて、Azure API Management を 1 つまたは複数の Azure Application Insights サービスと統合することもできます。 Application Insights のログ設定は、サービスごとまたは API ごとに構成できます。

必要に応じて、Azure Sentinel またはサードパーティのセキュリティ情報イベント管理 (SIEM) に対してデータを有効にしてオンボードします。

* [診断設定を構成する方法](../azure-monitor/platform/diagnostic-settings.md#create-in-azure-portal)

* [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

* [Azure Monitor とサードパーティの SIEM 統合を開始する方法](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [カスタムのログ記録と分析パイプラインを作成する方法](./api-management-howto-log-event-hubs.md)

* [Azure Application Insights と統合する方法](./api-management-howto-app-insights.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: コントロールプレーンの監査ログについて、Azure アクティビティ ログの診断設定を有効にして、アクティビティ ログをレポートと分析用に Log Analytics ワークスペースに送信したり、長期保管用に Azure Storage に送信したり、Azure や別の場所にある他のソリューションへのエクスポート用に Azure Event Hubs に送信したりします。 Azure アクティビティ ログのデータを使用すると、Azure API Management サービスのコントロール プレーン レベルで実行された書き込み操作 (PUT、POST、DELETE) について、"いつだれが何を" 行ったのかを特定することができます。

データ プレーンの監査ログの場合、診断ログは、監査とトラブルシューティングを行うために重要な、操作とエラーについての豊富な情報を提供します。 診断ログは、アクティビティ ログとは異なります。 アクティビティ ログは、API リソースで実行された操作に関する情報を提供します。 診断ログでは、リソースが実行した操作を調査できます。

* [Azure アクティビティ ログの診断設定を有効にする方法](../azure-monitor/platform/activity-log.md)

* [Azure API Management の診断設定を有効にする方法](./api-management-howto-use-azure-monitor.md#activity-logs)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**:Azure Monitor 内で、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定します。 長期/アーカイブ ストレージには Azure Storage アカウントを使用します。

* [Log Analytics ワークスペースのログの保持期間パラメーターを設定する方法](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

* [ログを Azure Storage アカウントにアーカイブする方法](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: Azure API Management では、ログとメトリックを Azure Monitor に継続的に出力して、API の状態と正常性をほぼリアルタイムで把握できるようにします。 Azure Monitor と Log Analytics ワークスペースを使用すると、API Management や関連リソースから取得されたメトリックとログに対してレビュー、クエリ、視覚化、ルーティング、アーカイブ、アラートの構成、アクションの実行などを行うことができます。 異常な動作がないかどうかログを分析および監視し、結果を定期的に確認します。

必要に応じて、API Management を Azure Application Insights と統合し、プライマリまたはセカンダリの監視、トレース、レポート、およびアラート ツールとして使用します。

* [Azure API Management のログを監視および確認する方法](./api-management-howto-use-azure-monitor.md)

* [Azure Monitor でカスタム クエリを実行する方法](../azure-monitor/log-query/get-started-queries.md)

* [Log Analytics ワークスペースについて](../azure-monitor/log-query/get-started-portal.md)

* [Azure Application Insights と統合する方法](./api-management-howto-app-insights.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**:Azure アクティビティ ログの診断設定と Azure API Management インスタンスの診断設定を有効にし、Log Analytics ワークスペースにログを送信します。 Log Analytics でクエリを実行して、用語の検索、傾向の特定、パターンの分析を行い、収集されたデータに基づいて他の多くの分析情報を提供します。 Log Analytics ワークスペース クエリに基づいてアラートを作成できます。

メトリック アラートを作成して、予想外の出来事が発生したときに通知されるようにします。 たとえば、Azure API Management インスタンスが特定の期間にわたって予想されるピーク時の容量を超えている場合、または承認されていないゲートウェイ要求やエラーが所定の期間にわたって一定数発生した場合に通知を受け取ります。

必要に応じて、API Management を Azure Application Insights と統合し、プライマリまたはセカンダリの監視、トレース、レポート、およびアラート ツールとして使用します。

必要に応じて、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードすることもできます。

* [Azure アクティビティ ログの診断設定を有効にする方法](../azure-monitor/platform/activity-log.md)

* [Azure API Management の診断設定を有効にする方法](./api-management-howto-use-azure-monitor.md#activity-logs)

* [承認されていない要求に対してアラート ルールを構成する方法](./api-management-howto-use-azure-monitor.md#set-up-an-alert-rule-for-unauthorized-request)

* [Azure API Management インスタンスの容量メトリックを表示する方法](./api-management-capacity.md)

* [Azure Application Insights と統合する方法](./api-management-howto-app-insights.md)

* [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**: 適用できません。Azure API Management では、マルウェア対策関連のログの処理や生成を行いません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**:適用できません。Azure API Management では、ユーザー アクセス可能な DNS 関連のログの処理や生成を行いません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="identity-and-access-control"></a>ID とアクセスの制御

*詳細については、「[セキュリティ コントロール: ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Azure API Management コントロール プレーン (Azure portal) への管理アクセス権を持つアカウントのインベントリを維持します。

Azure Active Directory (AD) には、明示的に割り当てる必要があり、クエリ可能な組み込みロールがあります。 API Management では、これらのロールとロールベースのアクセス制御を使用して、API Management のサービスとエンティティに関するきめ細かいアクセス管理を可能にしています。

また、API Management のユーザー システムにはあらかじめ登録された管理者グループが含まれています。 開発者ポータルでの API の可視性は API Management 内のグループによって制御されるため、管理者グループのメンバーはすべての API を表示できます。

Azure Security Center からの推奨事項に従って、管理者アカウントの管理と保守を行います。

* [Azure API Management でロールベースのアクセス制御を使用する方法](./api-management-role-based-access-control.md)

* [Azure API Management インスタンスのユーザーの一覧を取得する方法](/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [PowerShell を使用して Azure AD でディレクトリ ロールに割り当てられたユーザーの一覧を取得する方法](/powershell/module/az.resources/get-azroleassignment?view=azps-3.7.0)

* [PowerShell を使用して Azure AD でディレクトリ ロール定義を取得する方法](/powershell/module/az.resources/get-azroledefinition?view=azps-3.7.0)

* [Azure Security Center からの ID とアクセスの推奨事項について](../security-center/recommendations-reference.md#recs-identity)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure API Management には、既定のパスワード/キーの概念がありません。

ただし、Azure API Management サブスクリプション (API へのアクセスをセキュリティで保護する手段の 1 つ) には、生成されたサブスクリプション キーのペアが付属しています。 顧客は、これらのサブスクリプション キーをいつでも再生成できます。

* [Azure API Management のサブスクリプションについて](./api-management-subscriptions.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Azure Security Center ID とアクセス管理を使用して、管理者アカウントの数を監視します。

さらに、専用管理者アカウントを追跡できるように、Azure Security Center または組み込みの Azure ポリシーの次のような推奨事項を使用することもできます。
- 複数の所有者がサブスクリプションに割り当てられている必要がある
- 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある
- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある

* [Azure Security Center を使用して ID およびアクセスを監視する方法 (プレビュー)](../security-center/security-center-identity-access.md)

* [Azure Policy を使用する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

**ガイダンス**:開発者ポータルでユーザーを認証するための ID プロバイダーとして Azure Active Directory を活用するように Azure API Management を構成することで、Azure AD で提供される SSO 機能のメリットを享受できます。 構成が完了すると、新しい開発者ポータル ユーザーは、最初に Azure AD を使用して認証を行い、認証後にポータルでサインアップ プロセスを完了することで、難しい設定の要らないサインアップ プロセスを実行できます。

* [Azure API Management で Azure Active Directory を使用して開発者アカウントを承認する](./api-management-howto-aad.md)

または、委任を使用してサインイン/サインアップ プロセスをさらにカスタマイズすることもできます。 委任を使用すると、開発者のサインイン/サインアップおよび製品のサブスクリプション処理を、開発者ポータルの組み込みの機能ではなく、お客様の既存の Web サイトを使用して行うことができます。 これにより、お客様の Web サイトでユーザー データを保持し、独自の方法でこれらのステップの検証を実行できます。

* [How to delegate user registration and product subscription (ユーザーの登録と成果物のサブスクリプションを委任する方法) (ユーザーの登録と成果物のサブスクリプションを委任する方法)](./api-management-howto-setup-delegation.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory (AD) Multi-Factor Authentication (MFA) を有効にし、Azure Security Center ID とアクセス管理の推奨事項に従います。

* [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

* [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**: Multi-Factor Authentication (MFA) が構成された特権アクセス ワークステーション (PAW) を使用してログインし、Azure リソースを構成します。

* [特権アクセス ワークステーションについて](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティを記録してアラートを生成する

**ガイダンス**: Azure Active Directory (AD) Privileged Identity Management (PIM) を使用して、環境内で疑わしいアクティビティまたは安全でないアクティビティが発生したときにログとアラートを生成します。

また、Azure AD のリスク検出を使用して、危険なユーザーの行動に関するアラートとレポートを表示します。

* [Privileged Identity Management (PIM) をデプロイする方法](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Azure AD のリスク検出の概要](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: 条件付きアクセスのネームド ロケーションを使用して、IP アドレスの範囲、国、またはリージョンの特定の論理グループからのみ Azure portal へのアクセスを許可します。

* [Azure でネームド ロケーションを構成する方法](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**:可能な限り、Azure AD を中央認証および承認システムとして使用します。 AAD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、Azure AD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。

Azure Active Directory を使用して開発者アカウントを認証するように Azure API Management 開発者ポータルを構成します。

Azure Active Directory (AD) で OAuth 2.0 プロトコルを使用して API を保護するように Azure API Management インスタンスを構成します。

* [Azure API Management で Azure Active Directory を使用して開発者アカウントを承認する方法](./api-management-howto-aad.md)

* [Azure Active Directory と API Management で OAuth 2.0 を使用して API を保護する方法](./api-management-howto-protect-backend-with-aad.md)

* [AAD インスタンスを作成して構成する方法](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory では、古いアカウントの検出に役立つログが提供されます。 顧客は、Azure ID アクセス レビューを利用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、ロールの割り当てを効率的に管理できます。 ユーザー アクセスを定期的にレビューし、正当なユーザーのみが適切なアクセス権を持ち続けるようにすることができます。

顧客は、API Management ユーザー アカウントのインベントリを維持し、必要に応じてアクセスを調整できます。 API Management では、開発者は、API Management を使用して公開された API のコンシューマーになります。 既定では、新しく作成された開発者アカウントは "アクティブ" になり、"開発者" グループに関連付けられます。 アクティブ状態の開発者アカウントを使用すると、サブスクリプションがあるすべての API にアクセスできます。

管理者は、カスタム グループを作成できるほか、関連付けられている Azure Active Directory テナントの外部グループを活用することもできます。 カスタム グループと外部グループをシステム グループと共に使用することにより、開発者には API 成果物の可視性とアクセスが提供されます。

* [Azure API Management でユーザー アカウントを管理する方法](./api-management-howto-create-or-invite-developers.md)

* [API Management ユーザーの一覧を取得する方法](/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [Azure API Management でグループを作成および使用して開発者アカウントを管理する方法](./api-management-howto-create-groups.md)

* [Azure ID アクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 非アクティブ化されたアカウントへのアクセス試行を監視する

**ガイダンス**: Azure API Management で Azure Active Directory を ID プロバイダーとして使用して開発者アカウントを認証するように Azure API Management インスタンスを構成します。

Azure Active Directory (AD) で OAuth 2.0 プロトコルを使用して API を保護するように Azure API Management インスタンスを構成します。

有効なトークンの存在と有効性を守るために、受信 API 要求に適用する JWT 検証ポリシーを構成します。

Azure AD ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics 内に目的のアラートを構成します。 さらに、Log Analytics ワークスペースを Azure Sentinel またはサードパーティの SIEM にオンボードすることができます。

`log-to-eventhub` ポリシーを使用して API Management で高度な監視を構成し、セキュリティ分析に必要な追加のコンテキスト情報をキャプチャして、Azure Sentinel またはサードパーティ の SIEM に送信します。

* [Azure API Management で Azure Active Directory を使用して開発者アカウントを承認する方法](./api-management-howto-aad.md)

* [Azure Active Directory と API Management で OAuth 2.0 を使用して API を保護する方法](./api-management-howto-protect-backend-with-aad.md)

* [API Management のアクセス制限ポリシー](./api-management-access-restriction-policies.md)

* [Azure AD ログを Azure Monitor に統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

* [API の高度な監視](./api-management-log-to-eventhub-sample.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラートを生成する

**ガイダンス**: コントロール プレーン (Azure portal) でのアカウント ログイン動作の偏差について、Azure Active Directory (AD) Identity Protection とリスク検出機能を使用して、ユーザー ID に関連して検出された疑わしいアクションへの自動応答を構成します。 Azure Sentinel にデータを取り込んで、さらに詳しく調査することもできます。

* [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md)

* [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**:現在は使用できません。Azure API Management ではカスタマー ロックボックスは現在サポートされていません。

* [カスタマー ロックボックスでサポートされているサービスの一覧](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="data-protection"></a>データ保護

*詳細については、「[セキュリティ コントロール: データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: 機密情報を格納または処理する Azure リソースを追跡しやすくするには、タグを使用します。

* [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 Azure API Management インスタンスは、仮想ネットワーク (VNet)/サブネットで分離し、適切にタグ付けする必要があります。

* [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

* [管理グループを作成する方法](../governance/management-groups/create.md)

* [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

* [仮想ネットワークで Azure API Management を使用する方法](./api-management-using-with-vnet.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: 現在は使用できません。Azure API Management では、データの識別、分類、損失防止機能は現在使用できません。

Microsoft では、Azure API Management 用の基になるインフラストラクチャを管理し、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。

* [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**: 管理プレーン呼び出しは、TLS 経由で Azure Resource Manager によって行われます。 有効な JSON Web トークン (JWT) が必要です。 データ プレーン呼び出しは、TLS と、サポートされる認証メカニズムのいずれか (例: クライアント証明書や JWT など) で保護できます。

* [Azure API Management でのデータ保護について](./api-management-security-controls.md#data-protection)

* [Azure API Management で TLS 設定を管理する](./api-management-howto-manage-protocols-ciphers.md)

* [Azure API Management で Azure Active Directory を使用して API を保護する](./api-management-howto-protect-backend-with-aad.md)

* [Azure API Management で Azure Active Directory B2C を使用して API を保護する](./howto-protect-backend-frontend-azure-ad-b2c.md)

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: 現在は使用できません。Azure API Management では、データの識別、分類、損失防止機能は現在使用できません。 機密情報を処理している可能性のある Azure API Management サービスにタグを付け、コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

* [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**: Azure API Management へのアクセスを制御するために、ロールベースのアクセス制御を使用します。 Azure API Management では、Azure のロールベースのアクセス制御 (RBAC) の使用により、API Management のサービスとエンティティ (API やポリシーなど) に関するきめ細かいアクセス管理が可能になります。

* [Azure API Management でロールベースのアクセス制御を使用する方法](./api-management-role-based-access-control.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

Microsoft では、Azure API Management 用の基になるインフラストラクチャを管理し、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。

* [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: 証明書、キー、およびシークレットの名前付きの値などの機密データは、サービスで管理される、サービス インスタンスごとのキーで暗号化されます。 すべての暗号化キーはサービス インスタンスごとに存在し、サービスで管理されます。

* [Azure API Management を使用した保存データの保護と暗号化について](./api-management-security-controls.md#data-protection)

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure Functions の運用アプリおよびその他の重要なリソースまたは関連リソースへの変更がいつ発生したかに関するアラートを作成するには、Azure Monitor と Azure アクティビティ ログを使用します。

* [Azure アクティビティ ログ イベントのアラートを作成する方法](../azure-monitor/platform/alerts-activity-log.md)

* [Azure API Management で Azure Monitor と Azure アクティビティ ログを使用する方法](./api-management-howto-use-azure-monitor.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、「[セキュリティ コントロール: 脆弱性の管理](../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**: 現在は使用できません。Azure API Management では、Azure Security Center の脆弱性評価は現在使用できません。

Microsoft でスキャンおよび修正される、基になるプラットフォーム。 利用可能なセキュリティ コントロールを確認して、サービス構成関連の脆弱性を軽減します。

* [Azure API Management で利用可能なセキュリティ コントロールについて](./api-management-security-controls.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3:サードパーティの自動化されたソフトウェア修正プログラム管理ソリューションを展開する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**: 現在は使用できません。Azure API Management では、Azure Security Center の脆弱性評価は現在使用できません。

Microsoft でスキャンおよび修正される、基になるプラットフォーム。 顧客が利用可能なセキュリティ コントロールを確認して、サービス構成関連の脆弱性を軽減します。

* [Azure API Management で利用可能なセキュリティ コントロールについて](./api-management-security-controls.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、「[セキュリティ コントロール: インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-azure-asset-discovery"></a>6.1:Azure Asset Discovery を使用する

**ガイダンス**: Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワーク、ポート、プロトコルなど) のクエリまたは検出を行います。 テナントで適切な (読み取り) アクセス許可を確認し、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙します。

従来の Azure リソースは Resource Graph で検出できますが、今後は Azure Resource Manager リソースを作成して使用することを強くお勧めします。

* [Azure Resource Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

* [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Azure RBAC について](../role-based-access-control/overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを提供する Azure リソースにタグを適用すると、それらのリソースが各分類に論理的に整理されます。

* [タグを作成して利用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: Azure リソースを整理および追跡するには、必要に応じて、タグ付け、管理グループ、個別のサブスクリプションを使用します。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

さらに、次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
- 許可されないリソースの種類
- 許可されるリソースの種類

* [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

* [管理グループを作成する方法](../governance/management-groups/create.md)

* [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4:承認された Azure リソースとソフトウェア タイトルのインベントリを管理する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、サブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
- 許可されないリソースの種類
- 許可されるリソースの種類

Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。 環境に存在するすべての Azure リソースが承認されていることを確認します。

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

* [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
- 許可されないリソースの種類
- 許可されるリソースの種類

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy を使用して特定のリソースの種類を拒否する方法](/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="610-implement-approved-application-list"></a>6.10:承認されたアプリケーションの一覧を実装する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11:スクリプトを使用して Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: Azure 条件付きアクセスを使用して Azure Resource Manager とやりとりするユーザーの機能を制限するには、"Microsoft Azure 管理" アプリに対して [アクセスのブロック] を構成します。

* [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../role-based-access-control/conditional-access-azure-management.md)

* [Azure API Management でのロールベースのアクセス制御](./api-management-role-based-access-control.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12:コンピューティング リソース内でスクリプトを実行するユーザーの機能を制限する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、「[セキュリティ コントロール: セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**:Azure Policy を使用して、Azure API Management サービスの標準的なセキュリティ構成を定義して実装します。 Azure API Management サービスの構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.ApiManagement" 名前空間で Azure Policy エイリアスを使用します。

* [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: Azure Policy を使用して、Azure API Management サービスの標準的なセキュリティ構成を定義して実装します。 Azure API Management インスタンスの構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.ApiManagement" 名前空間で Azure Policy エイリアスを使用します。 Azure リソース全体にセキュリティで保護された設定を適用するには、Azure ポリシー [拒否] と [存在する場合はデプロイする] を使用します。

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy の効果について](../governance/policy/concepts/effects.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: カスタムの Azure Policy 定義を使用する場合は、Azure DevOps または Azure Repos を使用して、Azure API Management サービスの構成を安全に格納して管理します。

* [Azure DevOps でファイルを格納する方法](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos のドキュメント](/azure/devops/repos/index?view=azure-devops)

* [Azure API Management DevOps リソース キットについて](./api-management-security-controls.md#configuration-management)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="77-deploy-system-configuration-management-tools"></a>7.7:システム構成管理ツールをデプロイする

**ガイダンス**: Azure Policy を使用して、Azure API Management サービスの標準的なセキュリティ構成を定義して実装します。 Azure API Management インスタンスの構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.ApiManagement" 名前空間で Azure Policy エイリアスを使用します。 Azure リソース全体にセキュリティで保護された設定を適用するには、Azure ポリシー [拒否] と [存在する場合はデプロイする] を使用します。

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy の効果について](../governance/policy/concepts/effects.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用のシステム構成管理ツールをデプロイする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9:Azure サービスの自動構成監視を実装する

**ガイダンス**:Azure API Management DevOps リソース キットを使用して、Azure API Management の構成管理を行います。

また、Azure Policy を使用して、Azure API Management サービスの標準的なセキュリティ構成を定義して実装します。 Azure API Management インスタンスの構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.ApiManagement" 名前空間で Azure Policy エイリアスを使用します。 Azure リソース全体にセキュリティで保護された設定を適用するには、Azure ポリシー [拒否] と [存在する場合はデプロイする] を使用します。

* [Azure API Management DevOps リソース キットについて](./api-management-security-controls.md#configuration-management)

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy の効果について](../governance/policy/concepts/effects.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**:証明書の管理に Key Vault を使用し、それらをオートローテーションに設定します。 Azure Key Vault を使用してカスタム ドメインの SSL 証明書を管理する場合は、証明書がシークレットではなく、証明書 として Key Vault に挿入されていることを確認してください。

* [Key Vault キーのローテーションに関するガイダンスを使用してカスタム ドメイン名を設定する方法](./configure-custom-domain.md)

**Azure Security Center の監視**: はい

**責任**: Microsoft

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: Azure Active Directory (AD) によって生成された管理対象のサービス ID を使用して、API Management インスタンスが Azure AD で保護された他のリソース (Azure Key Vault など) に簡単かつ安全にアクセスできるようにします。

* [API Management インスタンスのマネージド ID を作成する方法](./api-management-howto-use-managed-service-identity.md)

* [マネージド ID で認証するためのポリシー](./api-management-authentication-policies.md#ManagedIdentity)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

* [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、「[セキュリティ コントロール: マルウェアからの防御](../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1:一元管理されるマルウェア対策ソフトウェアを使用する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

Microsoft のマルウェア対策は、Azure サービス (Azure API Management など) をサポートしている基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: 適用できません。この推奨事項は、データを格納するように設計された非コンピューティング リソースを対象にしています。

Microsoft のマルウェア対策は、Azure サービス (Azure API Management など) をサポートしている基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: 適用できません。この推奨事項は、データを格納するように設計された非コンピューティング リソースを対象にしています。

Microsoft のマルウェア対策は、Azure サービス (Azure API Management など) をサポートしている基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="data-recovery"></a>データの復旧

*詳細については、「[セキュリティ コントロール: データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的に自動バックアップを行う

**ガイダンス**: Azure API Management を介して API の発行と管理を行うことで、そうしなければ手動で設計、実装、および管理する必要のある、フォールト トレランス機能やインフラストラクチャ機能を利用できるようになります。 API Management では、運用のオーバーヘッドを増やさずにデータ プレーンがリージョンの障害の影響を受けにくくする、複数リージョンのデプロイがサポートされています。

API Management のサービスのバックアップと復元の機能では、ディザスター リカバリー戦略を実装するために必要な構成要素が提供されます。 バックアップ操作と復元操作は、手動または自動で実行できます。

* [複数のリージョンに API Management データ プレーンをデプロイする方法](./api-management-howto-deploy-multi-region.md)

* [Azure API Management でサービスのバックアップと復元を使用してディザスター リカバリーを実装する方法](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [API Management のバックアップ操作を呼び出す方法](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup)

* [API Management の復元操作を呼び出す方法](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、カスタマー マネージド キーをバックアップする

**ガイダンス**: Azure API Management によって提供されるバックアップ操作と復元操作では、システムの完全バックアップと復元が実行されます。

マネージド ID を使用すると、API Management のカスタム ドメイン名用に Azure Key Vault から証明書を取得できます。 Azure Key Vault 内に格納されているすべての証明書をバックアップします。

* [Azure API Management でサービスのバックアップと復元を使用してディザスター リカバリーを実装する方法](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [Azure Key Vault の証明書をバックアップする方法](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: バックアップを検証するには、バックアップからのサービスと証明書のテスト復元を実行します。

* [API Management の復元操作を呼び出す方法](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

* [Azure Key Vault の証明書を復元する方法](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を確保する

**ガイダンス**: Azure API Management では、顧客が所有している Azure Storage アカウントにバックアップを書き込みます。 Azure Storage のセキュリティに関する推奨事項に従って、バックアップを保護します。

* [Azure API Management でサービスのバックアップと復元を使用してディザスター リカバリーを実装する方法](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [BLOB ストレージのセキュリティに関する推奨事項](../storage/blobs/security-recommendations.md)

Key Vault で論理的な削除を有効にして、偶発的または悪意のある削除からキーを保護します。

* [Key Vault で論理的な削除を有効にする方法](../storage/blobs/soft-delete-overview.md?tabs=azure-portal)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、「[セキュリティ コントロール: インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。

* [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [独自のインシデント対応計画の作成のための NIST の「コンピューター セキュリティ インシデント対応ガイド」](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

また、サブスクリプション ( 運用、非運用など) をタグを使用して明確にマークし、Azure リソース (特に、機密データを処理するもの) を明確に識別して分類するための命名システムを作成します。 インシデントが発生した Azure リソースと環境の重要度に基づいて、アラートの修復に優先順位を付けることは、お客様の責任です。

* [Security alerts in Azure Security Center](../security-center/security-center-alerts-overview.md)

* [タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:ご利用のシステムのインシデント対応機能を定期的にテストする演習を実施することで、ご利用の Azure リソースの保護を支援できます。 弱点やギャップを特定し、必要に応じて計画を見直します。

* [NIST の出版物 - IT 計画と機能に関するテスト、トレーニング、および演習プログラムのガイド](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。

* [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**: 連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートすると、Azure リソースへのリスクを特定できます。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。

* [連続エクスポートを構成する方法](../security-center/continuous-export.md)

* [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**: セキュリティ アラートやセキュリティに関する推奨事項に対して "Logic Apps" 経由で応答を自動的にトリガーするには、Azure Security Center のワークフローの自動化機能を利用します。

* [ワークフローの自動化と Logic Apps を構成する方法](../security-center/workflow-automation.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、「[セキュリティ コントロール: 侵入テストとレッド チーム演習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1:Azure リソースの定期的な侵入テストを実行し、すべての重要なセキュリティ調査結果を 60 日以内に確実に修復する

**ガイダンス**: * [Microsoft の実施ルールに従って、侵入テストが Microsoft のポリシーに違反しないようにしてください](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

* [Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施の詳細については、こちらを参照してください](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](../security/benchmarks/overview.md)に関するページを参照する
- [Azure セキュリティ ベースライン](../security/benchmarks/security-baselines-overview.md)の詳細について学習する

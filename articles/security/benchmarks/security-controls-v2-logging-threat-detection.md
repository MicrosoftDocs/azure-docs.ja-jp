---
title: Azure セキュリティ ベンチマーク V2 - ログと脅威検出
description: Azure セキュリティ ベンチマーク V2 - ログと脅威検出
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: a274ec1aab3f530700f89ef810fa667fdf4d08e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042982"
---
# <a name="security-control-v2-logging-and-threat-detection"></a>セキュリティ コントロール V2:ログと脅威検出

「ログと脅威検出」は、Azure で脅威を検出するためのコントロール、および Azure サービスの監査ログの有効化、収集、保存のためのコントロールを対象としています。 これには、コントロールを使って検出、調査、修復プロセスを有効にすることが含まれ、これにより、Azure サービスのネイティブ脅威検出を使用して高品質のアラートを生成します。この他に、Azure Monitor によるログの収集、Azure Sentinel によるセキュリティ分析の一元化、時間の同期、ログの保持も含まれます。

該当する組み込み Azure Policy を確認するには、「[Azure セキュリティ ベンチマーク規制コンプライアンスの組み込みイニシアチブ: ログと脅威検出](../../governance/policy/samples/azure-security-benchmark.md#logging-and-threat-detection)」をご覧ください。

## <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1:Azure リソースの脅威検出を有効にする

| Azure ID | CIS コントロール v7.1 ID | NNIST SP 800-53 r4 ID |
|--|--|--|--|
| LT-1 | 6.7 | AU-3、AU-6、AU-12、SI-4 |

潜在的な脅威や異常を検出するために、さまざまな種類の Azure 資産を監視していることを確認してください。 アナリストが選別しやすいように、質の高いアラートを取得して誤検知を減らすことに専念します。 アラートは、ログ データ、エージェント、その他のデータを元に生成できます。

Azure Security Center の組み込みの脅威検出機能を使用します。これは、Azure サービス テレメトリの監視とサービス ログの分析に基づいています。 データは、Log Analytics エージェントを使用して収集されます。このエージェントは、セキュリティ関連のさまざまな構成とイベント ログをシステムから読み取り、分析のためにデータをワークスペースにコピーします。 

さらに、Azure Sentinel を使用して分析ルールを構築し、環境全体で特定の条件に一致する脅威を探します。 ルールにより、条件が一致したときにインシデントが生成されます。これにより、各インシデントを調査できます。 Azure Sentinel にサード パーティの脅威インテリジェンスをインポートして、脅威の検出機能を強化することもできます。 

- [Azure Security Center での脅威の防止](../../security-center/azure-defender.md)

- [Azure Security Center セキュリティ アラート リファレンス ガイド](../../security-center/alerts-reference.md)

- [脅威を検出するためのカスタム分析規則を作成する](../../sentinel/tutorial-detect-threats-custom.md)

- [Azure Sentinel を使用したサイバー脅威インテリジェンス](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [インフラストラクチャとエンドポイント セキュリティ](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [セキュリティ運用](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [体制管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [アプリケーション セキュリティと DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [脅威インテリジェンス](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2:Azure ID とアクセスの管理のために脅威検出を有効にする

| Azure ID | CIS コントロール v7.1 ID | NNIST SP 800-53 r4 ID |
|--|--|--|--|
| LT-2 | 6.8 | AU-3、AU-6、AU-12、SI-4 |

Azure AD では、次のユーザー ログが記録され、これは Azure AD レポートで確認できます。また、より高度な監視と分析のユース ケースの場合は、Azure Monitor、Azure Sentinel、またはその他の SIEM/監視ツールと統合できます。 
-   サインイン - サインイン レポートは、マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報を提供します。

-   監査ログ - Azure AD 内のさまざまな機能によって行われたすべての変更についてログによる追跡可能性を提供します。 監査ログの例として、ユーザー、アプリ、グループ、ロール、ポリシーの追加や削除など、Azure AD 内のあらゆるリソースに加えられた変更があります。

-   リスクの高いサインイン - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。

-   リスクのフラグ付きユーザー - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。

Azure Security Center で、認証試行の失敗回数が多すぎるなど、サブスクリプションにおける特定の不審なアクティビティや、使用すべきでないアカウントに対してアラートを生成することもできます。 Azure Security Center の脅威保護モジュールは、基本的なセキュリティ検疫監視に加えて、個々の Azure コンピューティング リソース (仮想マシン、コンテナー、アプリ サービスなど)、データ リソース (SQL DB、ストレージなど)、Azure サービス レイヤーから、さらに詳細なセキュリティ アラートを収集することもできます。 この機能を使用すると、個々のリソース内でアカウントの異常を確認できます。

- [Azure AD でアクティビティ レポートを監査する](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure Identity Protection を有効にする](../../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center での脅威の防止](../../security-center/azure-defender.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [インフラストラクチャとエンドポイント セキュリティ](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [セキュリティ運用](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [体制管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [アプリケーション セキュリティと DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [脅威インテリジェンス](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3:Azure ネットワーク アクティビティのログ記録を有効にする

| Azure ID | CIS コントロール v7.1 ID | NNIST SP 800-53 r4 ID |
|--|--|--|--|
| LT-3 | 9.3、12.2、12.5、12.8 | AU-3、AU-6、AU-12、SI-4 |

セキュリティ分析で、インシデント調査、脅威ハンティング、セキュリティ アラートの生成をサポートするために、ネットワーク セキュリティ グループ (NSG) のリソース ログ、NSG フロー ログ、Azure Firewall ログ、Web アプリケーション ファイアウォール (WAF) ログを有効にして収集します。 Azure Monitor Log Analytics ワークスペースにフロー ログを送信し、Traffic Analytics を使用して分析情報を提供できます。

他のネットワーク データの関連付けを支援するために、DNS クエリ ログを収集するようにしてください。

- [ネットワーク セキュリティ グループのフローのログを有効にする方法](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure Firewall のログとメトリック](../../firewall/logs-and-metrics.md)

- [Traffic Analytics を有効にして使用する方法](../../network-watcher/traffic-analytics.md)

- [Network Watcher を使用した監視](../../network-watcher/network-watcher-monitoring-overview.md)

- [Azure Monitor の Azure ネットワーク監視ソリューション](../../azure-monitor/insights/azure-networking-analytics.md)

- [DNS Analytics ソリューションを使用して DNS インフラストラクチャに関する分析情報を収集する](../../azure-monitor/insights/dns-analytics.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [インフラストラクチャとエンドポイント セキュリティ](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [セキュリティ運用](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [体制管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [アプリケーション セキュリティと DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [脅威インテリジェンス](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4:Azure リソースのログ記録を有効にする

| Azure ID | CIS コントロール v7.1 ID | NNIST SP 800-53 r4 ID |
|--|--|--|--|
| LT-4 | 6.2、6.3、8.8 | AU-3、AU-12 |

コンプライアンス、脅威の検出、ハンティング、インシデント調査の要件を満たすために、Azure リソースのログを有効にします。 

Azure Security Center と Azure Policy を使用すると、監査ログ、セキュリティ ログ、リソース ログにアクセスするために、Azure リソースのリソース ログとログ データの収集を有効にできます。 自動的に使用できるアクティビティ ログには、イベント ソース、日付、ユーザー、タイムスタンプ、送信元アドレス、送信先アドレス、その他の役立つ要素が含まれています。

- [Azure でのログ記録とログのさまざまな種類について](../../azure-monitor/essentials/platform-logs-overview.md)

- [Azure Security Center のデータ収集について](../../security-center/security-center-enable-data-collection.md)

**責任**: 共有

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [セキュリティ運用](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

インフラストラクチャとエンドポイントのセキュリティ 

- [アプリケーションのセキュリティと DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [脅威インテリジェンス](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5:セキュリティ ログの管理と分析を一元化する

| Azure ID | CIS コントロール v7.1 ID | NNIST SP 800-53 r4 ID |
|--|--|--|--|
| LT-5 | 6.5、6.6 | AU-3、SI-4 |

ログ記録のストレージと分析を一元化して、相関関係を有効にします。 ログ ソースごとに、データ所有者、アクセス ガイダンス、ストレージの場所、データの処理とアクセスに使用するツール、データ保持の要件を割り当てたことを確認します。

Azure アクティビティ ログを一元的なログ記録に統合していることを確認します。 Azure Monitor を介してログを取り込み、エンドポイント デバイス、ネットワーク リソース、およびその他のセキュリティ システムによって生成されたセキュリティ データを集計します。 Azure Monitor で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期のアーカイブ ストレージには Azure Storage アカウントを使用します。

さらに、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードします。

多くの組織では、頻繁に使用される "ホット" データに対しては Azure Sentinel を、使用頻度の低い "コールド" データに対しては Azure Storage を使用することを選択しています。

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../../azure-monitor/essentials/diagnostic-settings.md)

- [Azure Sentinel をオンボードする方法](../../sentinel/quickstart-onboard.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [セキュリティのアーキテクチャ](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [アプリケーションのセキュリティと DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [インフラストラクチャとエンドポイント セキュリティ](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="lt-6-configure-log-storage-retention"></a>LT-6:ログの保持期間を構成する

| Azure ID | CIS コントロール v7.1 ID | NNIST SP 800-53 r4 ID |
|--|--|--|--|
| LT-6 | 6.4 | AU-3、AU-11 |

コンプライアンス、規制、ビジネスの要件に従って、ログの保持期間を構成します。 

Azure Monitor で、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定できます。 長期ストレージおよびアーカイブ ストレージには、Azure Storage、Data Lake、または Log Analytics ワークスペースのアカウントを使用します。

- [Log Analytics でデータ保持期間を変更する](../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Azure Storage アカウント ログの保持ポリシーを構成する方法](../../storage/common/manage-storage-analytics-logs.md#configure-logging)

- [Azure Security Center のアラートと推奨事項のエクスポート](../../security-center/continuous-export.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [セキュリティのアーキテクチャ](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [アプリケーションのセキュリティと DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [セキュリティ運用](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [セキュリティ コンプライアンス管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7:承認された時刻同期ソースを使用する

| Azure ID | CIS コントロール v7.1 ID | NNIST SP 800-53 r4 ID |
|--|--|--|--|
| LT-7 | 6.1 | AU-8 |

Microsoft は、ほとんどの Azure PaaS および SaaS サービスのタイム ソースを保持しています。 特定の要件がない限り、仮想マシンでは、時刻の同期に Microsoft の既定の NTP サーバーを使用してください。 独自のネットワーク タイム プロトコル (NTP) サーバーを立ち上げる必要がある場合は、UDP サービス ポート 123 をセキュリティで保護してください。

Azure 内のリソースによって生成されるすべてのログでは、既定で指定されたタイム ゾーンを使用してタイム スタンプが付けられます。

- [Azure Windows コンピューティング リソースの時刻同期を構成する方法](../../virtual-machines/windows/time-sync.md)

- [Azure Linux コンピューティング リソースの時刻同期を構成する方法](../../virtual-machines/linux/time-sync.md)

- [Azure サービスの受信 UDP を無効にする方法](https://support.microsoft.com/help/4558520/how-to-disable-inbound-udp-for-azure-services)

**責任**: 共有

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [ポリシーと標準](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [アプリケーションのセキュリティと DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [インフラストラクチャとエンドポイント セキュリティ](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)
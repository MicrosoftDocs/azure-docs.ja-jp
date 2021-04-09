---
title: Azure セキュリティ コントロール - ログ記録と監視
description: Azure セキュリティ コントロール - ログ記録と監視
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: ccfbb585ccf50366721925b0b31b17088fd36371
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102612424"
---
# <a name="security-control-logging-and-monitoring"></a>セキュリティ コントロールログ記録と監視

セキュリティのログ記録と監視は、Azure サービスに対する監査ログの有効化、取得、および保存に関連するアクティビティに焦点を当てています。

## <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

Microsoft は Azure リソースのためにタイム ソースを管理していますが、コンピューティング リソースのために時刻同期設定を管理するオプションが用意されています。

- [Azure Windows コンピューティング リソースの時刻同期を構成する方法](../../virtual-machines/windows/time-sync.md)

- [Azure Linux コンピューティング リソースの時刻同期を構成する方法](../../virtual-machines/linux/time-sync.md)

## <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 2.2 | 6.5、6.6 | Customer |

Azure Monitor を介してログを取り込み、エンドポイント デバイス、ネットワーク リソース、およびその他のセキュリティ システムによって生成されたセキュリティ データを集計します。 Azure Monitor 内で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期/アーカイブ ストレージには Azure Storage アカウントを使用します。

または、Azure Sentinel またはサードパーティの SIEM に対してデータを有効にしてオンボードすることもできます。 

- [Azure Sentinel をオンボードする方法](../../sentinel/quickstart-onboard.md)

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../../azure-monitor/essentials/diagnostic-settings.md)

- [Azure Monitor で Azure 仮想マシンの内部ホスト ログを収集する方法](../../azure-monitor/vm/quick-collect-azurevm.md)

- [Azure Monitor とサードパーティの SIEM 統合を開始する方法](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 2.3 | 6.2、6.3 | Customer |

監査ログ、セキュリティ ログ、および診断ログにアクセスするため、Azure リソースに対する診断設定を有効にします。 自動的に使用できるアクティビティ ログには、イベント ソース、日付、ユーザー、タイムスタンプ、送信元アドレス、送信先アドレス、その他の役立つ要素が含まれています。

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../../azure-monitor/essentials/diagnostic-settings.md)

- [Azure でのログ記録とログのさまざまな種類について](../../azure-monitor/essentials/platform-logs-overview.md)

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 2.4 | 6.2、6.3 | Customer |

コンピューティング リソースが Microsoft に所有されている場合、その監視は Microsoft が担当します。 コンピューティング リソースがお客様の組織に所有されている場合、その監視はお客様が担当します。 OS は Azure Security Center を使用して監視できます。 Security Center によってオペレーティング システムから収集されるデータには、OS の種類とバージョン、OS (Windows イベント ログ)、実行中のプロセス、マシン名、IP アドレス、ログイン ユーザーなどがあります。 Log Analytics エージェントは、クラッシュ ダンプ ファイルも収集します。

- [Azure Monitor で Azure 仮想マシンの内部ホスト ログを収集する方法](../../azure-monitor/vm/quick-collect-azurevm.md)

- [Azure Security Center のデータ収集について](../../security-center/security-center-enable-data-collection.md)

## <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 2.5 | 6.4 | Customer |

Azure Monitor 内で、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定します。 長期/アーカイブ ストレージには Azure Storage アカウントを使用します。

- [Log Analytics でデータ保持期間を変更する](../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Azure Storage アカウント ログの保持ポリシーを構成する方法](../../storage/common/manage-storage-analytics-logs.md#configure-logging)

## <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 2.6 | 6.7 | Customer |

異常な動作がないかログの分析と監視を行って、定期的に結果を確認します。 Azure Monitor の Log Analytics ワークスペースを使用してログを確認し、ログ データに対してクエリを実行します。

または、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードすることもできます。 

- [Azure Sentinel をオンボードする方法](../../sentinel/quickstart-onboard.md)

- [Log Analytics ワークスペースについて](../../azure-monitor/logs/log-analytics-tutorial.md)

- [Azure Monitor でカスタム クエリを実行する方法](../../azure-monitor/logs/get-started-queries.md)

## <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 2.7 | 6.8 | Customer |

Log Analytics ワークスペースと共に Azure Security Center を使用し、セキュリティ ログやイベントで検出される異常なアクティビティに対する監視とアラートの送信を行います。

または、Azure Sentinel に対してデータを有効にしてオンボードすることもできます。

- [Azure Sentinel をオンボードする方法](../../sentinel/quickstart-onboard.md)

- [Azure Security Center でアラートを管理する方法](../../security-center/security-center-managing-and-responding-alerts.md)

- [Log Analytics のログ データに関するアラートを送信する方法](../../azure-monitor/alerts/tutorial-response.md)

## <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 2.8 | 8.6 | Customer |

Azure Virtual Machines および Cloud Services についてのマルウェア対策イベント収集を有効にします。

- [Virtual Machines に対して Microsoft Antimalware を構成する方法](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension)

- [Cloud Services に対して Microsoft Antimalware を構成する方法](/powershell/module/servicemanagement/azure.service/set-azureserviceantimalwareextension)

- [Microsoft Antimalware について](../fundamentals/antimalware.md)

## <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 2.9 | 8.7 | Customer |

組織のニーズに応じて、DNS ログ ソリューション用に Azure Marketplace のサードパーティ製ソリューションを実装します。  

## <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 2.10 | 8.8 | Customer |

サポートされているすべての Azure Windows 仮想マシンで Microsoft Monitoring Agent を使用して、プロセス作成イベントとコマンドライン フィールドをログに記録します。   サポートされている Azure Linux 仮想マシンでは、ノード単位でコンソール ログを手動で構成し、Syslog を使用してデータを格納することができます。  さらに、Azure Monitor の Log Analytics ワークスペースを使用してログを確認し、Azure 仮想マシンからログ データに対してクエリを実行します。 

- [Azure Security Center でのデータ収集](../../security-center/security-center-enable-data-collection.md#data-collection-tier)

- [Azure Monitor でカスタム クエリを実行する方法](../../azure-monitor/logs/get-started-queries.md)

- [Azure Monitor の Syslog データ ソース](../../azure-monitor/agents/data-sources-syslog.md)


## <a name="next-steps"></a>次のステップ

- 次のセキュリティ コントロールを参照してください。[ID およびアクセス制御](security-control-identity-access-control.md)
---
title: イベント ハブへの Azure 監視データのストリーム配信
description: パートナー SIEM または分析ツールにデータを取り込むために Azure 監視データをイベント ハブにストリーム配信する方法について学習します。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/20/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 535c74fd161019db28e691ff916ad03eaaf07c90
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260369"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub"></a>イベント ハブへの Azure 監視データのストリーム配信
Azure Monitor では、Azure や他のクラウド、オンプレミスのアプリケーションとサービスに対する包括的なフル スタック監視ソリューションが提供されます。 Azure Monitor を使用してデータを分析し、さまざまな監視シナリオに活用するだけでなく、環境内にある別の監視ツールにそれを送信することが必要な場合もあります。 ほとんどの場合、監視データを外部ツールにストリーム配信するうえで最も効率的なのは、[Azure Event Hubs](/azure/event-hubs/) を使用する方法です。 この記事では、各種ソースからイベント ハブに監視データをストリーム配信する方法について簡単に説明するほか、詳細なガイダンスへのリンクを紹介します。


## <a name="create-an-event-hubs-namespace"></a>Event Hubs 名前空間を作成します

データ ソースのストリーミングを構成する前に、[Event Hubs 名前空間とイベント ハブを作成](../../event-hubs/event-hubs-create.md)しておく必要があります。 この名前空間とイベント ハブが、すべての監視データの送信先です。 Event Hubs 名前空間は、同じアクセス ポリシーを共有するイベント ハブの論理的なグループであり、ストレージ アカウントがそのストレージ アカウント内に個別の BLOB ストレージを持つのと似たようなものです。 監視データのストリーム配信に使用する Event Hubs 名前空間とイベント ハブについては、次の詳細を考慮してください。

* イベント ハブのスループット スケールは、スループット単位の数によって増やすことができます。 通常、必要なスループット ユニットは 1 つだけです。 自分のログの使用量が増加してスケールアップする必要が生じた場合は、名前空間のスループット ユニット数を手動で増やすか、自動インフレを有効にすることができます。
* 多数のコンシューマー間での消費量は、パーティションの数によって並列化することができます。 1 つのパーティションでのサポート上限は 20 MBps です (1 秒あたり約 20,000 件のメッセージ)。 データの消費元のツールによっては、複数のパーティションからの消費をサポートできない場合もあります。 設定するパーティションの数がわからない場合は、まず 4 つのパーティションから始めることをお勧めします。
* イベント ハブでのメッセージの保持期間は 7 日以上に設定してください。 そうすれば、消費元のツールが 1 日以上ダウンした場合でも、ダウンした時点から最大 7 日前までのイベントを復旧することができます。
* イベント ハブには既定のコンシューマー グループを使用してください。 2 つの異なるコンシューマー グループが同じイベント ハブから同じデータを使用するのでないかぎり、他のコンシューマー グループを作成したり、個別のコンシューマー グループを使用する必要はありません。
* Azure アクティビティ ログについては、Event Hubs 名前空間を選択すると、Azure Monitor によって、その名前空間内に _insights-logs-operational-logs_ という名前のイベント ハブが作成されます。 その他のログ タイプについては、既存のイベント ハブを選択するか、Azure Monitor によってログ カテゴリごとにイベント ハブを作成することができます。
* 通常、イベント ハブからのデータを消費するコンピューターまたは VNET では、送信ポート 5671 と 5672 を開く必要があります。


## <a name="monitoring-data-available"></a>利用可能な監視データ
Azure アプリケーションの各種データ階層とそれぞれで利用できる監視データの種類については、「[Azure Monitor で使用する監視データのソース](data-sources.md)」で説明しています。 次の表に、これらの各階層の一覧と、イベント ハブにデータをストリーム配信する方法を示します。 詳細については、記載されているリンクを参照してください。

| レベル | Data | 方法 |
|:---|:---|:---|
| [Azure テナント](data-sources.md#azure-tenant) | Azure Active Directory 監査ログ | ご自分の AAD テナントでテナント診断設定を構成します。 詳細については、「[チュートリアル: Azure Active Directory ログを Azure イベント ハブにストリーム配信する](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)」を参照してください。 |
| [Azure サブスクリプション](data-sources.md#azure-subscription) | [Azure Activity Log (Azure アクティビティ ログ)] | アクティビティ ログ イベントを Event Hubs にエクスポートするログ プロファイルを作成します。  詳細については、「[Azure アクティビティ ログをストレージまたは Azure Event Hubs にエクスポートする](activity-log-export.md)」を参照してください。 |
| [Azure リソース](data-sources.md#azure-resources) | プラットフォームのメトリック<br>診断ログ |どちらの種類のデータも、リソース診断設定を使用してイベント ハブに送信されます。 詳細については、[イベント ハブへの Azure 診断ログのストリーム配信](resource-logs-stream-event-hubs.md)に関するページを参照してください。 |
| [オペレーティング システム (ゲスト)](data-sources.md#operating-system-guest) | Azure Virtual Machines | Azure の Windows と Linux 仮想マシンに、[Azure Diagnostics 拡張機能](diagnostics-extension-overview.md)をインストールします。 Windows VM の場合の詳細については「[Event Hubs を利用してホット パスの Azure Diagnostics データをストリーム配信する](diagnostics-extension-stream-event-hubs.md)」を、Linux VM の場合の詳細については「[Linux Diagnostic Extension を使用して、メトリックとログを監視する](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings)」を参照してください。 |
| [アプリケーション コード](data-sources.md#application-code) | Application Insights | Application Insights には、イベント ハブにデータを直接ストリーム配信する方法は備わっていません。 ストレージ アカウントへの Application Insights データの[連続エクスポートを設定](../../azure-monitor/app/export-telemetry.md)してから、「[ロジック アプリを使用した手動ストリーム配信](#manual-streaming-with-logic-app)」の説明に従って、ロジック アプリを使用してデータをイベント ハブに送信できます。 |

## <a name="manual-streaming-with-logic-app"></a>ロジック アプリを使用した手動ストリーム配信
イベント ハブに直接ストリーム配信できないデータについては、Azure Storage に書き込んでから、時刻トリガー式のロジック アプリを使用し、[データを BLOB ストレージからプル](../../connectors/connectors-create-api-azureblobstorage.md#add-action)して[メッセージとしてイベント ハブにプッシュ](../../connectors/connectors-create-api-azure-event-hubs.md#add-action)できます。 


## <a name="tools-with-azure-monitor-integration"></a>Azure Monitor とツールの統合

Azure Monitor で監視データをイベント ハブにルーティングすると、外部の SIEM や監視ツールに簡単に統合することができます。 Azure Monitor とツールの統合例としては、次のものがあります。

| ツール | 説明 |
|:---|:---|
|  IBM QRadar | Microsoft Azure DSM および Microsoft Azure Event Hub Protocol は、[IBM サポート Web](https://www.ibm.com/support) サイトからダウンロードすることができます。 Azure との統合の詳細については、「[QRadar DSM の構成](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0)」を参照してください。 |
| Splunk | [Splunk 向けの Azure Monitor アドオン](https://splunkbase.splunk.com/app/3534/)が、Splunkbase でオープンソース プロジェクトとして公開されています。 ドキュメントは、「[Splunk 向けの Azure Monitor アドオン](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk)」でご覧いただけいます。<br><br> プロキシの使用時や Splunk Cloud での実行時など、アドオンをご自分の Splunk インスタンスにインストールできない場合は、イベント ハブの新着メッセージによりトリガーされる [Splunk 向け Azure 関数](https://github.com/Microsoft/AzureFunctionforSplunkVS)を使用して、Splunk HTTP イベント コレクターにこれらのイベントを転送できます。 |
| sumologic | 「[イベント ハブから Azure 監査アプリのログを収集する](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)」で、イベント ハブのデータを使用するように SumoLogic を設定する手順が説明されています。 |
| ArcSight | [ArcSight スマート コネクタ コレクション](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852)の一部として、ArcSight Azure イベント ハブ スマート コネクタが提供されています。 |
| Syslog サーバー | Azure Monitor データを Syslog サーバーに直接ストリーム配信したい場合は、[Azure 関数ベースのソリューション](https://github.com/miguelangelopereira/azuremonitor2syslog/)を使用できます。


## <a name="next-steps"></a>次の手順
* [ストレージ アカウントにアクティビティ ログをアーカイブする](../../azure-monitor/platform/archive-activity-log.md)
* [Azure アクティビティ ログの概要を確認する](../../azure-monitor/platform/activity-logs-overview.md)
* [アクティビティ ログ イベントに基づいてアラートを設定する](../../azure-monitor/platform/alerts-log-webhook.md)



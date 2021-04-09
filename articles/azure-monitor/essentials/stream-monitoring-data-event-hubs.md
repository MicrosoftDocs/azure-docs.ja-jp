---
title: イベント ハブと外部パートナーへの Azure 監視データのストリーム配信
description: パートナー SIEM または分析ツールにデータを取り込むために Azure 監視データをイベント ハブにストリーム配信する方法について学習します。
services: azure-monitor
author: bwren
ms.author: bwren
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: a929563df3e7e98575056d07519abfda0d6ac13b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102032961"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-or-external-partner"></a>イベント ハブまたは外部パートナーへの Azure 監視データのストリーム配信

Azure Monitor では、Azure や他のクラウド、オンプレミスのアプリケーションとサービスに対する包括的なフル スタック監視ソリューションが提供されます。 Azure Monitor を使用してデータを分析し、さまざまな監視シナリオに活用するだけでなく、環境内にある別の監視ツールにそれを送信することが必要な場合もあります。 ほとんどの場合、監視データを外部ツールにストリーム配信するうえで最も効率的なのは、[Azure Event Hubs](../../event-hubs/index.yml) を使用する方法です。 この記事では、これを実行する方法について簡単に説明し、データを送信できる一部のパートナーを一覧表示します。 いくつかは、Azure Monitor との特別な統合があり、Azure でホストできます。  

## <a name="create-an-event-hubs-namespace"></a>Event Hubs 名前空間を作成します

データ ソースのストリーミングを構成する前に、[Event Hubs 名前空間とイベント ハブを作成](../../event-hubs/event-hubs-create.md)しておく必要があります。 この名前空間とイベント ハブが、すべての監視データの送信先です。 Event Hubs 名前空間は、同じアクセス ポリシーを共有するイベント ハブの論理的なグループであり、ストレージ アカウントがそのストレージ アカウント内に個別の BLOB ストレージを持つのと似たようなものです。 監視データのストリーム配信に使用する Event Hubs 名前空間とイベント ハブについては、次の詳細を考慮してください。

* イベント ハブのスループット スケールは、スループット単位の数によって増やすことができます。 通常、必要なスループット ユニットは 1 つだけです。 自分のログの使用量が増加してスケールアップする必要が生じた場合は、名前空間のスループット ユニット数を手動で増やすか、自動インフレを有効にすることができます。
* 多数のコンシューマー間での消費量は、パーティションの数によって並列化することができます。 1 つのパーティションでのサポート上限は 20 MBps です (1 秒あたり約 20,000 件のメッセージ)。 データの消費元のツールによっては、複数のパーティションからの消費をサポートできない場合もあります。 設定するパーティションの数がわからない場合は、まず 4 つのパーティションから始めることをお勧めします。
* イベント ハブでのメッセージの保持期間は 7 日以上に設定してください。 そうすれば、消費元のツールが 1 日以上ダウンした場合でも、ダウンした時点から最大 7 日前までのイベントを復旧することができます。
* イベント ハブには既定のコンシューマー グループを使用してください。 2 つの異なるコンシューマー グループが同じイベント ハブから同じデータを使用するのでないかぎり、他のコンシューマー グループを作成したり、個別のコンシューマー グループを使用する必要はありません。
* Azure アクティビティ ログについては、Event Hubs 名前空間を選択すると、Azure Monitor によって、その名前空間内に _insights-logs-operational-logs_ という名前のイベント ハブが作成されます。 その他のログ タイプについては、既存のイベント ハブを選択するか、Azure Monitor によってログ カテゴリごとにイベント ハブを作成することができます。
* 通常、イベント ハブからのデータを消費するコンピューターまたは VNET では、送信ポート 5671 と 5672 を開く必要があります。

## <a name="monitoring-data-available"></a>利用可能な監視データ
Azure アプリケーションの各種データ階層とそれぞれで利用できる監視データの種類については、「[Azure Monitor で使用する監視データのソース](../agents/data-sources.md)」で説明しています。 次の表に、これらの各階層の一覧と、イベント ハブにデータをストリーム配信する方法を示します。 詳細については、記載されているリンクを参照してください。

| レベル | Data | Method |
|:---|:---|:---|
| [Azure テナント](../agents/data-sources.md#azure-tenant) | Azure Active Directory 監査ログ | ご自分の AAD テナントでテナント診断設定を構成します。 詳細については、「[チュートリアル: Azure Active Directory ログを Azure イベント ハブにストリーム配信する](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)」を参照してください。 |
| [Azure サブスクリプション](../agents/data-sources.md#azure-subscription) | [Azure Activity Log (Azure アクティビティ ログ)] | アクティビティ ログ イベントを Event Hubs にエクスポートするログ プロファイルを作成します。  詳細については、「[Azure プラットフォーム ログを Azure Event Hubs にストリーミングする](../essentials/resource-logs.md#send-to-azure-event-hubs)」を参照してください。 |
| [Azure リソース](../agents/data-sources.md#azure-resources) | プラットフォームのメトリック<br> リソース ログ |どちらの種類のデータも、リソース診断設定を使用してイベント ハブに送信されます。 詳細については、[イベント ハブへの Azure リソース ログのストリーム配信](../essentials/resource-logs.md#send-to-azure-event-hubs)に関するページを参照してください。 |
| [オペレーティング システム (ゲスト)](../agents/data-sources.md#operating-system-guest) | Azure の仮想マシン | Azure の Windows と Linux 仮想マシンに、[Azure Diagnostics 拡張機能](../agents/diagnostics-extension-overview.md)をインストールします。 Windows VM の場合の詳細については「[Event Hubs を利用してホット パスの Azure Diagnostics データをストリーム配信する](../agents/diagnostics-extension-stream-event-hubs.md)」を、Linux VM の場合の詳細については「[Linux Diagnostic Extension を使用して、メトリックとログを監視する](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings)」を参照してください。 |
| [アプリケーション コード](../agents/data-sources.md#application-code) | Application Insights | Application Insights には、イベント ハブにデータを直接ストリーム配信する方法は備わっていません。 ストレージ アカウントへの Application Insights データの[連続エクスポートを設定](../app/export-telemetry.md)してから、「[ロジック アプリを使用した手動ストリーム配信](#manual-streaming-with-logic-app)」の説明に従って、ロジック アプリを使用してデータをイベント ハブに送信できます。 |

## <a name="manual-streaming-with-logic-app"></a>ロジック アプリを使用した手動ストリーム配信
イベント ハブに直接ストリーム配信できないデータについては、Azure Storage に書き込んでから、時刻トリガー式のロジック アプリを使用し、[データを BLOB ストレージからプル](../../connectors/connectors-create-api-azureblobstorage.md#add-action)して[メッセージとしてイベント ハブにプッシュ](../../connectors/connectors-create-api-azure-event-hubs.md#add-action)できます。 


## <a name="partner-tools-with-azure-monitor-integration"></a>Azure Monitor とパートナー ツールの統合

Azure Monitor で監視データをイベント ハブにルーティングすると、外部の SIEM や監視ツールに簡単に統合することができます。 Azure Monitor とツールの統合例としては、次のものがあります。

| ツール | Azure でホスト | 説明 |
|:---|:---| :---|
|  IBM QRadar | いいえ | Microsoft Azure DSM および Microsoft Azure Event Hub Protocol は、[IBM サポート Web](https://www.ibm.com/support) サイトからダウンロードすることができます。 Azure との統合の詳細については、「[QRadar DSM の構成](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0)」を参照してください。 |
| Splunk | いいえ | [Splunk 用 Microsoft Azure アドオン](https://splunkbase.splunk.com/app/3757/)が、Splunkbase でオープンソース プロジェクトとして公開されています。 <br><br> プロキシの使用時や Splunk Cloud での実行時など、アドオンをご自分の Splunk インスタンスにインストールできない場合は、イベント ハブの新着メッセージによりトリガーされる [Splunk 向け Azure 関数](https://github.com/Microsoft/AzureFunctionforSplunkVS)を使用して、Splunk HTTP イベント コレクターにこれらのイベントを転送できます。 |
| sumologic | いいえ | 「[イベント ハブから Azure 監査アプリのログを収集する](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)」で、イベント ハブのデータを使用するように SumoLogic を設定する手順が説明されています。 |
| ArcSight | いいえ | [ArcSight スマート コネクタ コレクション](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852)の一部として、ArcSight Azure イベント ハブ スマート コネクタが提供されています。 |
| Syslog サーバー | いいえ | Azure Monitor データを Syslog サーバーに直接ストリーム配信したい場合は、[Azure 関数ベースのソリューション](https://github.com/miguelangelopereira/azuremonitor2syslog/)を使用できます。
| LogRhythm | いいえ| LogRhythm を設定してイベント ハブからログを収集するための手順については、[こちら](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/)を参照してください。 
|Logz.io | はい | 詳細については、[Azure で実行される Java アプリ用の Logz.io を使用した監視とログ記録の概要](/azure/developer/java/fundamentals/java-get-started-with-logzio)に関するページを参照してください。

他のパートナーも利用できる場合があります。 すべての Azure Monitor パートナーとその機能の詳細な一覧については、「[Azure Monitor パートナーとの統合](../partners.md)」を参照してください。

## <a name="next-steps"></a>次の手順
* [ストレージ アカウントにアクティビティ ログをアーカイブする](./activity-log.md#legacy-collection-methods)
* [Azure アクティビティ ログの概要を確認する](../essentials/platform-logs-overview.md)
* [アクティビティ ログ イベントに基づいてアラートを設定する](../alerts/alerts-log-webhook.md)
---
title: Azure Security Center のアラートと推奨事項を SIEM にエクスポートする | Microsoft Docs
description: この記事では、SIEM へのセキュリティ アラートと推奨事項の連続エクスポートを設定する方法について説明します
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: memildin
ms.openlocfilehash: eb7f642e36bd72f963481cb392d7e3a6c2555816
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612386"
---
# <a name="export-security-alerts-and-recommendations"></a>セキュリティ アラートと推奨事項のエクスポート

Azure Security Center では、詳細なセキュリティ アラートと推奨事項が生成されます。 これらは、ポータル内またはプログラム ツールで表示できます。 場合によっては、この情報のエクスポートや、環境内の他の監視ツールへの送信が必要になることもあるでしょう。 

この記事では、アラートと推奨事項を手動または継続的な方法でエクスポートできるようにする一連のツールについて説明します。

これらのツールを使用すると、次のことができます。

* Log Analytics ワークスペースへの連続エクスポート
* Azure Event Hubs への連続エクスポート (サードパーティ SIEM との統合のため)
* CSV ファイルへのエクスポート (1 回限り)



## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般公開|
|価格:|Free レベル|
|必要なロールとアクセス許可:|リソース グループ上での**セキュリティ管理者ロール** (または**所有者**)<br>ターゲット リソースに対する書き込みアクセス許可も必要になります|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) China Gov、その他の Gov|
|||



## <a name="setting-up-a-continuous-export"></a>連続エクスポートを設定する

連続エクスポート先として Log Analytics ワークスペースまたは Azure Event Hubs のどちらを設定する場合でも、次の手順を実行する必要があります。

1. Security Center のサイドバーで、 **[価格と設定]** を選択します。

1. データのエクスポートを構成する特定のサブスクリプションを選択します。
    
1. そのサブスクリプションの設定ページのサイドバーで、 **[連続エクスポート]** を選択します。

    [![Azure Security Center のエクスポート オプション](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) - ここにエクスポート オプションが表示されます。 使用可能なエクスポート ターゲットごとにタブがあります。 

1. エクスポートするデータの種類を選択し、それぞれの種類に対するフィルターを選択します (たとえば、重大度が高いアラートのみをエクスポートするなど)。

1. [エクスポート ターゲット] 領域で、データを保存する場所を選択します。 データは別のサブスクリプションのターゲットにも保存できます (たとえば、中央のイベント ハブ インスタンスや中央の Log Analytics ワークスペースなど)。

1. **[保存]** を選択します。


## <a name="setting-up-continuous-export-via-the-rest-api"></a>REST API による連続エクスポートの設定

連続エクスポート機能は、Azure Security Center の[自動化 API](https://docs.microsoft.com/rest/api/securitycenter/automations) を使用して構成と管理を行うことができます。 この API を使用して、以下の選択可能な出力先にエクスポートするための自動化を作成または更新します。

- Azure Event Hub
- Log Analytics ワークスペース
- Azure Logic Apps 

API には、Azure portal からは使用できない追加の機能が用意されています。以下に例を示します。

* **より大きなボリューム** - API を使用すると、1 つのサブスクリプションに複数のエクスポート構成を作成できます。 Security Center のポータル UI の **[連続エクスポート]** ページでは、サブスクリプションごとに 1 つのエクスポート構成のみがサポートされます。

* **追加の機能** - API には、UI には表示されない追加のパラメーターが用意されています。 たとえば、自動化リソースにタグを追加したり、Security Center のポータル UI の **[連続エクスポート]** ページで提供されているよりも幅広い一連のアラートと推奨設定のプロパティに基づいて、エクスポートを定義したりすることができます。

* **より焦点を絞ったスコープ** - API では、エクスポート構成のスコープに対してよりきめ細かなレベルが提供されます。 API を使用してエクスポートを定義するときには、リソース グループ レベルで定義できます。 Security Center のポータル UI の **[連続エクスポート]** ページを使用している場合は、サブスクリプション レベルで定義する必要があります。

    > [!TIP]
    > API を使用して複数のエクスポート構成を設定した場合や、API のみのパラメーターを使用した場合、それらの追加機能は Security Center UI に表示されません。 代わりに、他の構成が存在することを通知するバナーが表示されます。

自動化 API の詳細については、[REST API のドキュメント](https://docs.microsoft.com/rest/api/securitycenter/automations)を参照してください。



## <a name="configuring-siem-integration-via-azure-event-hubs"></a>Azure Event Hubs を使用した SIEM 統合の構成

Azure Event Hubs は、ストリーミング データをプログラムで使用するための優れたソリューションです。 Azure Security Center のアラートと推奨事項に関して、これはサードパーティの SIEM と統合するための推奨される方法です。

> [!NOTE]
> ほとんどの場合、監視データを外部ツールにストリーム配信するうえで最も効率的なのは、Azure Event Hubs を使用する方法です。 [この記事](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs)では、各種ソースからイベント ハブに監視データをストリーム配信する方法について簡単に説明するほか、詳細なガイダンスへのリンクを紹介しています。

> [!NOTE]
> これまで Azure アクティビティ ログを使用して Security Center アラートを SIEM にエクスポートしていた場合は、代わりに次の手順で同じことができます。

エクスポートされたデータ型のイベント スキーマを表示するには、[イベント ハブのイベント スキーマ](https://aka.ms/ASCAutomationSchemas)にアクセスします。


### <a name="to-integrate-with-a-siem"></a>SIEM と統合するには 

選択した Security Center データを Azure Event Hubs に連続エクスポートするように構成した後、SIEM に適したコネクタを設定できます。

* **Azure Sentinel** - そこで提供されているネイティブの Azure Security Center アラート [データ コネクタ](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)を使用する。
* **Splunk** - [Splunk 向けの Azure Monitor アドオン](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)を使用する
* **IBM QRadar** - [手動で構成されたログ ソース](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_dsm_guide_microsoft_azure_enable_event_hubs.html)を使用する
* **ArcSight** – [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292) を使用する

また、連続エクスポートされたデータを構成済みのイベント ハブから Azure データ エクスプローラーに自動的に移動する場合は、「[イベント ハブから Azure Data Explorer にデータを取り込む](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub)」の手順を使用します。



## <a name="continuous-export-to-a-log-analytics-workspace"></a>Log Analytics ワークスペースへの連続エクスポート

Log Analytics ワークスペース内の Azure Security Center データを分析したり、Security Center と共に Azure アラートを使用したりする場合は、Log Analytics ワークスペースへの連続エクスポートを設定します。

Log Analytics ワークスペースにエクスポートするには、ワークスペースで Security Center の Log Analytics ソリューションが有効になっている必要があります。 Azure portal を使用している場合は、連続エクスポートを有効にすると、Security Center の Free レベルのソリューションが自動的に有効になります。 一方、連続エクスポートの設定をプログラムによって構成する場合は、必要なワークスペースに対して、 **[価格と設定]** 内から手動で Free または Standard の価格レベルを選択する必要があります。  

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics のテーブルとスキーマ

セキュリティのアラートと推奨事項はそれぞれ、*SecurityAlert* テーブルと *SecurityRecommendations* テーブルに格納されます。 これらのテーブルを含む Log Analytics ソリューションの名前は、使用しているレベルが Free か Standard かに応じて ([価格](security-center-pricing.md)を参照してください)、Security('Security and Audit') または SecurityCenterFree。

![Log Analytics の *SecurityAlert* テーブル](./media/continuous-export/log-analytics-securityalert-solution.png)

エクスポートされたデータ型のイベント スキーマを表示するには、[Log Analytics テーブル スキーマ](https://aka.ms/ASCAutomationSchemas)にアクセスします。

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>エクスポートされたセキュリティ アラートと推奨事項を Azure Monitor で表示する

場合によっては、エクスポートされたセキュリティ アラートや推奨事項を [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview) で表示することもできます。 

Azure Monitor は、診断ログ、メトリック アラート、および Log Analytics ワークスペース クエリに基づくカスタム アラートなど、さまざまな Azure アラートの統合されたアラート エクスペリエンスを提供します。

Azure Monitor の Security Center からアラートと推奨事項を表示するには、Log Analytics クエリ (ログ アラート) に基づいてアラート ルールを構成します。

1. Azure Monitor の **[アラート]** ページから、 **[新しいアラート ルール]** を選択します。

    ![Azure Monitor の [アラート] ページ](./media/continuous-export/azure-monitor-alerts.png)

1. ルールの作成ページで、([Azure Monitor でログ アラート ルール](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)を構成するのと同じ方法で) 新しいルールを構成します。

    * **[リソース]** には、セキュリティ アラートと推奨事項のエクスポート先の Log Analytics ワークスペースを選択します。

    * **[条件]** には、 **[Custom log search]\(カスタム ログ検索\)** を選択します。 表示されたページで、クエリ、ルックバック期間、および頻度の期間を構成します。 検索クエリでは、「*SecurityAlert*」または「*Securityalert*」と入力して、Log Analytics への連続エクスポート機能を有効にしたときに Security Center が連続してエクスポートするデータ型に対してクエリを実行できます。 
    
    * 必要に応じて、トリガーする[アクション グループ](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)を構成します。 アクション グループは、メール送信、ITSM チケット、Webhook などをトリガーできます。
    ![Azure Monitor のアラート ルール](./media/continuous-export/azure-monitor-alert-rule.png)

これで、アクション グループの自動トリガーが設定された (指定されている場合)、新しい Azure Security Center アラートまたは推奨事項 (構成によって異なる) が Azure Monitor アラートに表示されるようになりました。

## <a name="manual-one-time-export-of-security-alerts"></a>セキュリティ アラートの 1 回限りの手動エクスポート

アラートまたは推奨事項の CSV レポートをダウンロードするには、 **[セキュリティ アラート]** または **[推奨事項]** ページを開き、 **[レポートを CSV にダウンロード]** ボタンを選択します。

[![アラート データを CSV ファイルとしてダウンロードする](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> これらのレポートには、現在選択されているサブスクリプションのリソースに関するアラートと推奨事項が含まれています。



## <a name="faq---continuous-export"></a>FAQ - 連続エクスポート

### <a name="what-are-the-costs-involved-in-exporting-data"></a>データのエクスポートには、どのようなコストがかかりますか。

連続エクスポートを有効にするためのコストはありません。 Log Analytics ワークスペースでの構成によっては、そこでのデータの取り込みと保持についてコストが発生する可能性があります。 

[Log Analytics ワークスペースの価格](https://azure.microsoft.com/pricing/details/monitor/)の詳細を確認してください。

[Azure Event Hub の価格](https://azure.microsoft.com/pricing/details/event-hubs/)の詳細を確認してください。


## <a name="next-steps"></a>次のステップ

この記事では、推奨事項とアラートの連続エクスポートを構成する方法について説明しました。 また、アラート データを CSV ファイルとしてダウンロードする方法も説明しました。 

関連資料については、次のドキュメントを参照してください。 

- [Azure Event Hubs のドキュメント](https://docs.microsoft.com/azure/event-hubs/)
- [Azure Sentinel のドキュメント](https://docs.microsoft.com/azure/sentinel/)
- [Azure Monitor のドキュメント](https://docs.microsoft.com/azure/azure-monitor/)
- [ワークフローの自動化と連続エクスポートのデータ型スキーマ](https://aka.ms/ASCAutomationSchemas)

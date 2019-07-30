---
title: Azure Monitor のアラートと監視の統合によるクラシックなアラートと監視の置換
description: Azure portal で [アラート] (クラシック) に表示されていたクラシック監視サービスと機能の提供終了の概要。 クラシックなアラートと監視には、Azure リソースのクラシック メトリック アラート、Application Insights のクラシック メトリック アラート、Application Insights のクラシック Web テスト アラート、Application Insights のクラシック カスタム メトリック ベースのアラート、およびApplication Insights SmartDetection v1 のクラシック アラートが含まれます。
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 28dba8af059e24040abb254dd4ecd8e336d8221d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594291"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Azure Monitor のアラートと監視の統合によるクラシックなアラートと監視の置換

Azure Monitor は、フル スタックの統合監視サービスになり、リソース全体で "ワン メトリック" と "ワン アラート" をサポートするようになりました。詳細については、[新しい Azure Monitor に関するブログ記事](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/)を参照してください。この新しい Azure の監視およびアラート プラットフォームは、処理速度が速く、より賢く、拡張できるように構築され、Microsoft のインテリジェント クラウド哲学に沿って、成長するクラウド コンピューティングの拡大に対応します。 

新しい Azure の監視およびアラート プラットフォームの配置に伴い、Azure アラートの "*クラシック アラートの表示*" セクションでホストされている "クラシック" な監視およびアラート プラットフォームは、その提供を終了し、Azure パブリック クラウドで **2019 年 8 月に廃止される予定**です。 [Azure Government クラウド](../../azure-government/documentation-government-welcome.md)と [Azure China 21Vianet](https://docs.azure.cn/) に影響はありません。

> [!NOTE]
> 移行ツールの展開が遅れたことで、クラシック アラート移行の提供終了日が、最初に発表された 2019 年 6 月 30 日から [2019 年 8 月 31 日に延長](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/)されました。

 ![Azure portal のクラシック アラート](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

新しいプラットフォームの使用を開始し、新しいプラットフォームでアラートを再作成することをお勧めします。 多数のアラートを作成しているお客様のために、中断や追加コストなしで既存のクラシック アラートを新しいアラート システムに移動するための[自主的移行ツール](alerts-using-migration-tool.md)を[段階的に展開](alerts-understand-migration.md#rollout-phases)する作業が進んでいます。

> [!IMPORTANT]
> アクティビティ ログで作成されたクラシック アラート ルールは非推奨や移行の対象になることはありません。 アクティビティ ログで作成されたすべてのクラシック アラート ルールには、新しい Azure Monitor のアラートからそのままアクセスし、使用できます。 詳細については、「[Azure Monitor を使用してアクティビティ ログ アラートを作成、表示、管理する](../../azure-monitor/platform/alerts-activity-log.md)」を参照してください。 同様に、Service Health に関するアラートには、新しい Service Health セクションからそのままアクセスし、使用できます。 詳細については、[サービスの正常性通知のアラート](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)に関するページを参照してください。

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Application Insights でのメトリックとアラートの統合

Azure Monitor の新しいメトリック プラットフォームは、Application Insights による監視にパワーを与えています。 この動きは、Application Insights がアクション グループと連携して、以前の電子メールと Webhook 呼び出しを超える多くのオプションを提供できることを意味します。 音声通話、Azure Functions、Logic Apps、SMS、ServiceNow や Automation Runbooks などの ITSM ツールからもアラートをトリガーできるようになりました。 ほぼリアルタイムの監視とアラートによって、Application Insights ユーザーは、新しいプラットフォームで、他の Azure リソースを監視するパワーと Microsoft 製品の土台となるテクノロジーを活用できます。

Application Insights での新しい監視とアラートの統合には、以下が含まれます。

- **Application Insights プラットフォーム メトリック** – Application Insights 製品から一般的な構築済みのメトリックを提供します。 詳細については、[新しい Azure Monitor での Application Insights のプラットフォーム メトリック](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)の使用に関する記事を参照してください。
- **Application insights の可用性と Web テスト** - Web アプリまたはサーバーの応答性と可用性を評価する機能を提供します。 詳細については、[新しい Azure Monitor での Application Insights の可用性テストとアラート](../../azure-monitor/app/monitor-web-app-availability.md)の使用に関する記事を参照してください。
- **Application Insights カスタム メトリック** – 監視とアラート用の独自のメトリックを定義して発行できます。 詳細については、[新しい Azure Monitor での Application Insights のカスタム メトリック](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)の使用に関する記事を参照してください。
- **Application Insights エラーの異常 (スマート検出の一部)** – Web アプリで HTTP 要求または依存する呼び出しの失敗率が異常に増加した場合に、ほぼリアルタイムで自動的に通知します。 詳細については、[[スマート検出 - 失敗の異常]](https://docs.microsoft.com/azure/azure-monitor/app/proactive-failure-diagnostics) の使用に関するこの記事を参照してください。

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>他の Azure リソースでのメトリックとアラートの統合

2018 年 3 月以降、Azure リソースに対して、次世代のアラートと多次元監視を利用できるようになっています。 新しいメトリック プラットフォームとアラートは高速であり、ほぼリアルタイムの機能を提供するようになっています。 さらに重要なのは、新しいメトリック プラットフォームでは、アラートがより高い粒度で提供されることです。これは、新しいプラットフォームには、特定の値の組み合わせ、条件、または操作でフィルター処理できるディメンション オプションが含まれているためです。 新しい Azure Monitor のすべてのアラートと同じように、新しいメトリック アラートは、ActionGroups を使用して拡張でき、電子メールや Webhook による通知に加え、SMS、Voice、Azure Function、Automation Runbook などによる通知まで展開できます。 詳細については、「[Azure Monitor を使用してメトリック アラートを作成、表示、管理する](../../azure-monitor/platform/alerts-metric.md)」を参照してください。
Azure リソースの新しいメトリックは、以下のように使用できます。

- **Azure Monitor の標準プラットフォーム メトリック** – さまざまな Azure サービスと製品から一般的な設定済みのメトリックを提供します。 詳細については、[Azure Monitor でサポートされるメトリック](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported)と[Azure Monitor でのメトリック アラートのサポート](../../azure-monitor/platform/alerts-metric-overview.md#supported-resource-types-for-metric-alerts)に関する記事を参照してください。
- **Azure Monitor のカスタム メトリック**– Azure Diagnostics エージェントを含む、ユーザーが操作するソースからメトリックを提供します。 詳細については、記事「[Azure Monitor のカスタム メトリック](../../azure-monitor/platform/metrics-custom-overview.md)」を参照してください。 カスタム メトリックを使用して、[Windows Azure Diagnostics エージェント](../../azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm.md)と [InfluxData Telegraf エージェント](../../azure-monitor/platform/collect-custom-metrics-linux-telegraf.md)によって収集されたメトリックを公開することもできます。

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>クラシック監視およびアラート プラットフォームの提供終了

前述のとおり、Azure portal の[アラート (クラシック) セクション](../../azure-monitor/platform/alerts-classic.overview.md) で現在使用できるクラシック監視およびアラート プラットフォームは、新しいシステムへの引継ぎが進行中であるため、数か月後に提供終了される予定です。
古いクラシックな監視とアラートは、関連する API、Azure portal のインターフェイス、およびその中のサービスも含めて、2019年 8 月 31 日付けで提供が終了します。 具体的には、次の機能が廃止されます。

- Azure portal の [アラート (クラシック) セクション](../../azure-monitor/platform/alerts-classic.overview.md)で現在利用できる Azure リソースの古い (クラシック) メトリックとアラート; [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) リソースとしてアクセス可能
- Application Insights の古い (クラシック) プラットフォームとカスタム メトリック、Azure portal の[アラート (クラシック) セクション](../../azure-monitor/platform/alerts-classic.overview.md) で現在使用でき、[microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) リソースとしてアクセス可能なアラート
- Azure portal で [Application Insights 内のスマート検出](../../azure-monitor/app/proactive-diagnostics.md)として現在使用できる古い (クラシック) エラーの異常; Azure portal の[アラート (クラシック) セクション](../../azure-monitor/platform/alerts-classic.overview.md)に表示されるように構成されたアラート

対応する [API](https://msdn.microsoft.com/library/azure/dn931945.aspx)、[PowerShell](../../azure-monitor/platform/alerts-classic-portal.md)、[CLI](../../azure-monitor/platform/alerts-classic-portal.md)、[Azure portal ページ](../../azure-monitor/platform/alerts-classic-portal.md)、および[リソース テンプレート](../../azure-monitor/platform/alerts-enable-template.md)を含むすべてのクラシック監視およびアラート システムは、2019 年 8 月末まで使用可能なままです。 

2019 年 8 月末時点の Azure Monitor:

- クラシック監視およびアラート サービスは廃止され、新しいアラート ルールの作成には使用可能できなくなります。
- 2019 年 8 月の後もアラート (クラシック) に引き続き存在するアラート ルールの実行は継続され、通知が発行されますが、変更することはできなくなります。
- 2019 年 9 月から、クラシック監視およびアラートの移行可能なアラート ルールは、Microsoft によって、新しい Azure 監視プラットフォームにある同等のアラート ルールに数週間にわたって段階的に移動されます。 このプロセスは、ダウンタイムなしでシームレスに実行され、お客様の監視範囲が失われることはありません。
- 新しいアラート プラットフォームへのアラート ルールの移行によって、前のような監視範囲が実現されますが、新しいペイロードによる通知が発生します。 クラシック アラート ルールに関連付けられたメール アドレス、Webhook エンドポイント、ロジック アプリ リンクはすべて、移行時に引き継がれますが、新しいプラットフォームではアラートのペイロードが異なるため、正常に機能しない可能性があります
- [自動的に移行できないクラシック アラート ルール](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)はユーザーによる手動アクションが必要であり、これらの実行は 2020 年 6 月まで継続されます。

> [!IMPORTANT]
> Microsoft Azure Monitor では、クラシック アラート ルールを新しいプラットフォームに[自主的に移行するためのツール](alerts-using-migration-tool.md)を段階的に展開してきました。 そして、2019 年 9 月から、まだ存在していて、かつ移行することができる従来のアラート ルールすべてに対して、それが強制的に実行されます。 お客様は、従来のアラート ルールの移行後、従来のアラート ルールのペイロードを使用するオートメーションが、[Application Insights でのメトリックとアラートの統合](#unified-metrics-and-alerts-in-application-insights)または[他の Azure リソースでのメトリックとアラートの統合](#unified-metrics-and-alerts-for-other-azure-resources)からの新しいペイロードの処理に合わせて改変されたことを確認する必要があります。 詳細については、[クラシック アラート ルールの移行に向けての準備](alerts-prepare-migration.md)に関するページを参照してください。

お客様の意思で Azure portal の[アラート (クラシック) セクション](../../azure-monitor/platform/alerts-classic.overview.md) から新しい Azure アラートに移行できるツールがロールアウトされています。 アラート (クラシック) 内に構成済みで新しい Azure Monitor に移行されるすべてのルールは無料のままであり、課金対象になりません。 移行後のクラシック アラート ルールでは、電子メール、Webhook、または LogicApp による通知のプッシュにも料金はかかりません。 ただし、新しい種類の通知またはアクション (SMS、音声通話、ITSM 統合など) の使用では、その使用が、移行後のアラートに追加されたか新しいアラートに追加されたかに応じて課金対象になります。 詳細については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」を参照してください。

さらに、[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)として、以下が課金対象になります。

- 新しい Azure Monitor プラットフォーム上で、無料ユニットを超えて作成されたすべての新しい (移行ではない) アラート ルール
- Azure Monitor に含まれる無料ユニットを超えて取り込まれて保持されるすべてのデータ
- Application Insights によって実行されるすべてのマルチテスト Web テスト
- Azure Monitor に含まれる無料ユニットを超えて格納されるすべてのカスタム メトリック

この記事は継続的に更新されて、新しい Azure の監視とアラートに関するリンクと詳細に加え、ユーザーが新しい Azure Monitor プラットフォームを導入するために役立つツールの提供状況が紹介される予定です。


## <a name="next-steps"></a>次の手順

* [新しい統合された Azure Monitor](../../azure-monitor/overview.md)について確認する。
* 新しい [Azure のアラート](../../azure-monitor/platform/alerts-overview.md)の詳細を確認する。

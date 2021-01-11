---
title: Azure Monitor でクラシック アラートおよび監視を更新する
description: Azure portal で [アラート] (クラシック) に表示されていたクラシック監視サービスと機能の提供終了の説明。
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 720a0e25f3486f32ffed897e54033fd4b68dace4
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87848127"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Azure Monitor のアラートと監視の統合によるクラシックなアラートと監視の置換

Azure Monitor は、フル スタックの統合監視サービスになり、リソース全体で "ワン メトリック" と "ワン アラート" をサポートするようになりました。詳細については、[新しい Azure Monitor に関するブログ記事](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/)を参照してください。この新しい Azure の監視およびアラート プラットフォームは、処理速度が速く、より賢く、拡張できるように構築され、Microsoft のインテリジェント クラウド哲学に沿って、成長するクラウド コンピューティングの拡大に対応します。

新しい Azure 監視およびアラート プラットフォームの導入により、Azure Monitor の従来のアラートは廃止されました。ただし、新しいアラートがまだサポートされていないリソースについては、引き続き制限付きで使用できます。 これらのアラートの提供終了日はさらに延長されています。 残りのアラート移行 ([Azure Government クラウド](../../azure-government/documentation-government-welcome.md)および [Azure China 21Vianet](https://docs.azure.cn/)) については、近日中に新しい日付が発表されます。

 ![Azure portal のクラシック アラート](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

新しいプラットフォームの使用を開始し、新しいプラットフォームでアラートを再作成することをお勧めします。

> [!IMPORTANT]
> アクティビティ ログで作成されたクラシック アラート ルールは非推奨や移行の対象になることはありません。 アクティビティ ログで作成されたすべてのクラシック アラート ルールには、新しい Azure Monitor のアラートからそのままアクセスし、使用できます。 詳細については、「[Azure Monitor を使用してアクティビティ ログ アラートを作成、表示、管理する](./alerts-activity-log.md)」を参照してください。 同様に、Service Health に関するアラートには、新しい Service Health セクションからそのままアクセスし、使用できます。 詳細については、[サービスの正常性通知のアラート](../../service-health/alerts-activity-log-service-notifications-portal.md)に関するページを参照してください。

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Application Insights でのメトリックとアラートの統合

Azure Monitor の新しいメトリック プラットフォームは、Application Insights による監視にパワーを与えています。 この動きは、Application Insights がアクション グループと連携して、以前の電子メールと Webhook 呼び出しを超える多くのオプションを提供できることを意味します。 音声通話、Azure Functions、Logic Apps、SMS、ServiceNow や Automation Runbooks などの ITSM ツールからもアラートをトリガーできるようになりました。 ほぼリアルタイムの監視とアラートによって、Application Insights ユーザーは、新しいプラットフォームで、他の Azure リソースを監視するパワーと Microsoft 製品の土台となるテクノロジーを活用できます。

Application Insights での新しい監視とアラートの統合には、以下が含まれます。

- **Application Insights プラットフォーム メトリック** – Application Insights 製品から一般的な構築済みのメトリックを提供します。 詳細については、[新しい Azure Monitor での Application Insights のプラットフォーム メトリック](../app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)の使用に関する記事を参照してください。
- **Application insights の可用性と Web テスト** - Web アプリまたはサーバーの応答性と可用性を評価する機能を提供します。 詳細については、[新しい Azure Monitor での Application Insights の可用性テストとアラート](../app/monitor-web-app-availability.md)の使用に関する記事を参照してください。
- **Application Insights カスタム メトリック** – 監視とアラート用の独自のメトリックを定義して発行できます。 詳細については、[新しい Azure Monitor での Application Insights のカスタム メトリック](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)の使用に関する記事を参照してください。
- **Application Insights エラーの異常 (スマート検出の一部)** – Web アプリで HTTP 要求または依存する呼び出しの失敗率が異常に増加した場合に、ほぼリアルタイムで自動的に通知します。 詳細については、[[スマート検出 - 失敗の異常]](../app/proactive-failure-diagnostics.md) の使用に関するこの記事を参照してください。

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>他の Azure リソースでのメトリックとアラートの統合

2018 年 3 月以降、Azure リソースに対して、次世代のアラートと多次元監視を利用できるようになっています。 新しいメトリック プラットフォームとアラートは高速であり、ほぼリアルタイムの機能を提供するようになっています。 さらに重要なのは、新しいメトリック プラットフォームでは、アラートがより高い粒度で提供されることです。これは、新しいプラットフォームには、特定の値の組み合わせ、条件、または操作でフィルター処理できるディメンション オプションが含まれているためです。 新しい Azure Monitor のすべてのアラートと同じように、新しいメトリック アラートは、ActionGroups を使用して拡張でき、電子メールや Webhook による通知に加え、SMS、Voice、Azure Function、Automation Runbook などによる通知まで展開できます。 詳細については、「[Azure Monitor を使用してメトリック アラートを作成、表示、管理する](./alerts-metric.md)」を参照してください。
Azure リソースの新しいメトリックは、以下のように使用できます。

- **Azure Monitor の標準プラットフォーム メトリック** – さまざまな Azure サービスと製品から一般的な設定済みのメトリックを提供します。 詳細については、[Azure Monitor でサポートされるメトリック](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported)と[Azure Monitor でのメトリック アラートのサポート](./alerts-metric-overview.md#supported-resource-types-for-metric-alerts)に関する記事を参照してください。
- **Azure Monitor のカスタム メトリック**– Azure Diagnostics エージェントを含む、ユーザーが操作するソースからメトリックを提供します。 詳細については、記事「[Azure Monitor のカスタム メトリック](./metrics-custom-overview.md)」を参照してください。 カスタム メトリックを使用して、[Windows Azure Diagnostics エージェント](./collect-custom-metrics-guestos-resource-manager-vm.md)と [InfluxData Telegraf エージェント](./collect-custom-metrics-linux-telegraf.md)によって収集されたメトリックを公開することもできます。

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>クラシック監視およびアラート プラットフォームの提供終了

前述のように、従来の監視とアラートは廃止されました。これには、関連する API、Azure portal インターフェイス、およびその中のサービスの終了も含まれます。ただし、新しいアラートがまだサポートされていないリソースにおいては、引き続き制限付きで使用できます。 具体的には、次の機能が廃止されます。

- Azure portal の [アラート (クラシック) セクション](./alerts-classic.overview.md)で現在利用できる Azure リソースの古い (クラシック) メトリックとアラート; [microsoft.insights/alertrules](/rest/api/monitor/alertrules) リソースとしてアクセス可能
- Application Insights の古い (クラシック) プラットフォームとカスタム メトリック、Azure portal の[アラート (クラシック) セクション](./alerts-classic.overview.md) で現在使用でき、[microsoft.insights/alertrules](/rest/api/monitor/alertrules) リソースとしてアクセス可能なアラート
- Azure portal で [Application Insights 内のスマート検出](../app/proactive-diagnostics.md)として現在使用できる古い (クラシック) エラーの異常; Azure portal の[アラート (クラシック) セクション](./alerts-classic.overview.md)に表示されるように構成されたアラート

これは、以下を意味します。

- クラシック監視およびアラート サービスは廃止され、新しいアラート ルールの作成には使用可能できなくなります。
- アラート (クラシック) に引き続き存在するアラート ルールの実行は継続され、通知が発行されます。
- クラシック監視およびアラートの移行可能なアラート ルールは、Microsoft によって、新しい Azure 監視プラットフォームにある同等のアラート ルールに数週間にわたって段階的に移動されます。 このプロセスは、ダウンタイムなしでシームレスに実行され、お客様の監視範囲が失われることはありません。
- 新しいアラート プラットフォームへのアラート ルールの移行によって、前のような監視範囲が実現されますが、新しいペイロードによる通知が発生します。 クラシック アラート ルールに関連付けられたメール アドレス、Webhook エンドポイント、ロジック アプリ リンクはすべて、移行時に引き継がれますが、新しいプラットフォームではアラートのペイロードが異なるため、正常に機能しない可能性があります
- [自動的に移行できないクラシック アラート ルール](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts)はユーザーによる手動アクションが必要であり、これらの実行は継続されます。

> [!IMPORTANT]
> Microsoft Azure Monitor では、クラシック アラート ルールを新しいプラットフォームに[自主的に移行するためのツール](alerts-using-migration-tool.md)を段階的に展開してきました。 そして、まだ存在していて、かつ移行することができる従来のアラート ルールすべてに対して、それを強制的に実行します。 お客様は、従来のアラート ルールの移行後、従来のアラート ルールのペイロードを使用するオートメーションが、[Application Insights でのメトリックとアラートの統合](#unified-metrics-and-alerts-in-application-insights)または[他の Azure リソースでのメトリックとアラートの統合](#unified-metrics-and-alerts-for-other-azure-resources)からの新しいペイロードの処理に合わせて改変されたことを確認する必要があります。 詳細については、[クラシック アラート ルールの移行に向けての準備](alerts-prepare-migration.md)に関するページを参照してください。

この記事は継続的に更新されて、新しい Azure の監視とアラートに関するリンクと詳細に加え、ユーザーが新しい Azure Monitor プラットフォームを導入するために役立つツールの提供状況が紹介される予定です。

## <a name="pricing-for-migrated-alert-rules"></a>移行済みのアラート ルールの価格

Azure Monitor の[クラシック アラート](./alerts-classic.overview.md)を新しいアラート エクスペリエンスに移行するのに役立つ移行ツールのロール アウトが進んでいます。 移行済みのアラート ルールとそれに対応する移行済みのアクション グループ (電子メール、Webhook、または LogicApp) は無料のままです。 クラシック アラートが持っていた、しきい値、集計の種類、集計の粒度を編集する機能などは、移行済みのアラート ルールでも引き続き無料で利用できます。 ただし、移行済みのアラート ルールを、新しいアラート プラットフォームの機能、通知、またはアクションの種類を使用するように編集した場合は、該当する料金が適用されます。 アラート ルールと通知の価格の詳細については、[「Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」を参照してください。

アラート ルールに対して料金が発生する可能性があるケースの例を次に示します。

- 新しい Azure Monitor プラットフォーム上で、無料ユニットを超えて作成されたすべての新しい (移行ではない) アラート ルール
- Azure Monitor に含まれる無料ユニットを超えて取り込まれて保持されるすべてのデータ
- Application Insights によって実行されるすべてのマルチテスト Web テスト
- Azure Monitor に含まれる無料ユニットを超えて格納されるすべてのカスタム メトリック
- 頻度、複数のリソース/ディメンション、[動的しきい値](alerts-dynamic-thresholds.md)、リソース/シグナルの変更などの新しいメトリック アラート機能を使用するように編集された移行済みのアラート ルール。
- 新しい通知、または SMS、音声通話、ITSM 統合などのアクションの種類を使用するように編集された移行済みのアクション グループ。

## <a name="next-steps"></a>次のステップ

* [新しい統合された Azure Monitor](../overview.md)について確認する。
* 新しい [Azure のアラート](./alerts-overview.md)の詳細を確認する。


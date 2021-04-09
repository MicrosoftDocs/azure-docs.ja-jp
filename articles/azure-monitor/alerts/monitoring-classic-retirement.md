---
title: Azure Monitor でクラシック アラートおよび監視を更新する
description: Azure portal で [アラート] (クラシック) に表示されていたクラシック監視サービスと機能の提供終了の説明。
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: yalavi
ms.openlocfilehash: bfa92a2fc58d479edd328dba9bf02d57ec66c0c9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041095"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Azure Monitor のアラートと監視の統合によるクラシックなアラートと監視の置換

Azure Monitor は、Azure リソース全体で "1 つのメトリック" と "1 つのアラート" をサポートする統合された監視スタックです。 詳細については、この[ブログ記事](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/)を参照してください。 この新しい Azure の監視およびアラート プラットフォームは、処理速度が速く、より賢く、拡張できるように構築され、Microsoft のインテリジェント クラウド哲学に沿って、成長するクラウド コンピューティングの拡大に対応します。

新しい Azure 監視およびアラート プラットフォームの導入により、パブリック クラウド ユーザーに対する Azure Monitor のクラシック アラートが廃止されました。ただし、**2021 年 5 月 31 日** までは、引き続き制限付きで使用できます。 Azure Government クラウドおよび Azure China 21Vianet 向けの従来のアラートは、**2024 年 2 月 29 日** に廃止されます。

 ![Azure portal のクラシック アラート](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

新しいプラットフォームの使用を開始し、新しいプラットフォームでアラートを再作成することをお勧めします。

> [!IMPORTANT]
> アクティビティ ログで作成されたクラシック アラート ルールは非推奨や移行の対象になることはありません。 アクティビティ ログで作成されたすべてのクラシック アラート ルールには、新しい Azure Monitor のアラートからそのままアクセスし、使用できます。 詳細については、「[Azure Monitor を使用してアクティビティ ログ アラートを作成、表示、管理する](./alerts-activity-log.md)」を参照してください。 同様に、Service Health に関するアラートには、新しい Service Health セクションからそのままアクセスし、使用できます。 詳細については、[サービスの正常性通知のアラート](../../service-health/alerts-activity-log-service-notifications-portal.md)に関するページを参照してください。

## <a name="unified-metrics-and-alerts-for-azure-resources"></a>Azure リソースでのメトリックとアラートの統合

2018 年 3 月に、Azure リソース用の次世代のアラート機能がリリースされました。 新しいメトリック プラットフォームとアラート機能は、より高速で、ディメンションを使用していっそう高い細分性を提供します。 ディメンションを使用すると、特定の値の組み合わせ、条件、または操作でスライスおよびフィルター処理を行うことができます。 新しいメトリック アラートではアクション グループが使用されており、より多くの通知と自動化アクションを実行できます。 [Azure Monitor を使用したメトリック アラートの管理](./alerts-metric.md)に関する詳細を参照してください。

Azure リソースの新しいメトリックは、以下のように使用できます。

- **Azure Monitor の標準プラットフォーム メトリック** – さまざまな Azure サービスと製品から一般的な設定済みのメトリックを提供します。 詳細については、[Azure Monitor でサポートされるメトリック](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported)と[Azure Monitor でのメトリック アラートのサポート](./alerts-metric-overview.md#supported-resource-types-for-metric-alerts)に関する記事を参照してください。
- **Azure Monitor のカスタム メトリック**– Azure Diagnostics エージェントを含む、ユーザーが操作するソースからメトリックを提供します。 詳細については、記事「[Azure Monitor のカスタム メトリック](../essentials/metrics-custom-overview.md)」を参照してください。 カスタム メトリックを使用して、[Windows Azure Diagnostics エージェント](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)と [InfluxData Telegraf エージェント](../essentials/collect-custom-metrics-linux-telegraf.md)によって収集されたメトリックを公開することもできます。

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>クラシック監視およびアラート プラットフォームの提供終了

前述したように、古い従来の監視とアラートのパブリック クラウド ユーザーへの提供は中止されます。 これには、関連する API、Azure portal インターフェイス、およびサービスの終了が含まれますが、**2021 年 5 月31日** までは引き続き制限付きで使用できます。 Azure Government クラウドおよび Azure China 21Vianet 向けの従来のアラートは、**2024 年 2 月 29 日** に廃止されます。

具体的には、提供終了の範囲は、現在、Azure portal の [[アラート (クラシック)] セクション](./alerts-classic.overview.md)で利用でき、[microsoft.insights/alertrules](/rest/api/monitor/alertrules) リソースとしてアクセス可能な、クラシック メトリックです。

これは、以下を意味します。

- クラシック監視およびアラート サービスは廃止され、新しいアラート ルールの作成には使用可能できなくなります。
- アラート (クラシック) に引き続き存在するアラート ルールの実行は継続され、通知が発行されます。
- ほとんどのクラシック アラート ルールは移行されます。 このプロセスは、ダウンタイムなしでシームレスに実行され、お客様の監視範囲が失われることはありません。
- 発生した通知には、新しいペイロード構造が含まれます。 新しい構造で動作するように、ターゲットを適合させる必要があります。
- 一部の[自動的に移行できないクラシック アラート ルール](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts)を引き続き実行するには、ユーザーによる手動アクションが必要です。

> [!IMPORTANT]
> Azure Monitor では、クラシック アラート ルールを新しいプラットフォームに[自主的に移行するためのツール](alerts-using-migration-tool.md)が展開されています。 残りのルールは、サービスが提供終了になると自動的に移行されます。 お客様は、従来のアラート ルールの移行後、従来のアラート ルールのペイロードを使用するオートメーションが、[他の Azure リソース用の統合メトリックとアラート](#unified-metrics-and-alerts-for-azure-resources)からの新しいペイロードを処理するように適合されたことを確認する必要があります。 詳細については、[クラシック アラート ルールの移行に向けての準備](alerts-prepare-migration.md)に関するページを参照してください。

## <a name="pricing-for-migrated-alert-rules"></a>移行済みのアラート ルールの価格

Azure Monitor の[クラシック アラート](./alerts-classic.overview.md)を新しいアラート エクスペリエンスに移行するのに役立つ移行ツールのロール アウトが進んでいます。 移行済みのアラート ルールとそれに対応する移行済みのアクション グループ (電子メール、Webhook、または LogicApp) は無料のままです。 クラシック アラートが持っていた、しきい値、集計の種類、集計の粒度を編集する機能などは、移行済みのアラート ルールでも引き続き無料で利用できます。 ただし、移行済みのアラート ルールを、新しいアラート プラットフォームの機能、通知、またはアクションの種類を使用するように編集した場合は、該当する料金が適用されます。 アラート ルールと通知の価格の詳細については、[「Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」を参照してください。

アラート ルールに対して料金が発生する可能性があるケースの例を次に示します。

- 新しい Azure Monitor プラットフォーム上で、無料ユニットを超えて作成されたすべての新しい (移行ではない) アラート ルール
- Azure Monitor に含まれる無料ユニットを超えて取り込まれて保持されるすべてのデータ
- Application Insights によって実行されるすべてのマルチテスト Web テスト
- Azure Monitor に含まれる無料ユニットを超えて格納されるすべてのカスタム メトリック
- 頻度、複数のリソース/ディメンション、[動的しきい値](../alerts/alerts-dynamic-thresholds.md)、リソース/シグナルの変更などの新しいメトリック アラート機能を使用するように編集された移行済みのアラート ルール。
- 新しい通知、または SMS、音声通話、ITSM 統合などのアクションの種類を使用するように編集された移行済みのアクション グループ。

## <a name="next-steps"></a>次のステップ

* [新しい統合された Azure Monitor](../overview.md)について確認する。
* 新しい [Azure のアラート](./alerts-overview.md)の詳細を確認する。
---
title: Azure DNS メトリックおよびアラート | Microsoft Docs
description: Azure DNS メトリックとアラートについて説明します。
services: dns
documentationcenter: na
author: vhorne
manager: jennoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: victorh
ms.openlocfilehash: 0f50ecb086b45cffb4ff27135a26f962c92926bf
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53434463"
---
# <a name="azure-dns-metrics-and-alerts"></a>Azure DNS メトリックとアラート
Azure DNS は、DNS ドメインのホスティング サービスであり、Microsoft Azure インフラストラクチャを使用した名前解決を提供します。 この記事では、Azure DNS サービスのメトリックとアラートについて説明します。

## <a name="azure-dns-metrics"></a>Azure DNS メトリック

Azure DNS は、お客様の DNS サービス内でホストされている DNS の特定の側面を監視するために使用できるメトリックを提供します。 さらに、Azure DNS メトリックを使用して、関心のある条件に基づいてアラートを受信するよう設定できます。 メトリックは、[Azure Monitor サービス](../azure-monitor/index.yml)を介して提供されます。 Azure DNS は、Azure Monitor を介して、お客様の DNS ゾーンについて次のメトリックを提供します。

-   QueryVolume
-   RecordSetCount
-   RecordSetCapacityUtilization

また、Azure Monitor のドキュメンテーション ページには、[これらのメトリックの定義](../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones)が記載されています。
>[!NOTE]
> 現時点では、Azure DNS でホストされているパブリック DNS ゾーンのメトリックのみが提供されています。 Azure DNS でホストされているプライベート ゾーンがある場合は、これらのメトリックによって、プライベート ゾーンに関するデータは提供されません。 さらに、メトリックとアラート機能は、Azure パブリック クラウドに対してのみサポートされています。 ソブリン クラウドのサポートは今後提供される予定です。 

これらのメトリックのディメンションの詳細度は、DNS ゾーンです。

### <a name="query-volume"></a>クエリ量

Azure DNS 内の *クエリ量*メトリックは、Azure DNS によって受信される、DNS ゾーンの DNS クエリ量 (クエリ トラフィック) を示します。 測定単位はクエリ数で、一定期間中に受信したすべてのクエリの合計数が集計されます。 このメトリックを表示するには、Azure Portal の [監視] タブからメトリックス エクスプローラー エクスペリエンス (プレビュー) を選択します。 [リソース] ドロップダウン リストから DNS ゾーンを選択し、クエリ量メトリックを選択し、[集計] として [合計] を選択します。 次のスクリーン ショットに、例を示します。  メトリックス エクスプローラー エクスペリエンスおよびグラフ作成機能の詳細については、「[Azure Monitor メトリックス エクスプローラー](../azure-monitor/platform/metrics-charts.md)」を参照してください。

![クエリ量](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*図: Azure DNS のクエリ量メトリック*

### <a name="record-set-count"></a>レコード セット数
*レコード セット数*メトリックは、DNS ゾーンの Azure DNS 内にあるレコード セットの数を示します。 ゾーン内で定義されているすべてのレコード セットが集計されます。 測定単位はレコード セット数で、すべてのレコードセットの最大数が集計されます。 このメトリックを表示するには、Azure Portal の **[監視]** タブから**メトリックス エクスプローラー エクスペリエンス (プレビュー)** を選択します。 **[リソース]** ドロップダウン リストから DNS ゾーンを選択し、**レコード セット数**メトリックを選択してから、**[集計]** として **[最大数]** を選択します。 メトリックス エクスプローラー エクスペリエンスおよびグラフ作成機能の詳細については、「[Azure Monitor メトリックス エクスプローラー](../azure-monitor/platform/metrics-charts.md)」を参照してください。 

![レコード セット数](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*図: Azure DNS のレコード セット数メトリック*


### <a name="record-set-capacity-utilization"></a>レコード セットの容量使用率
Azure DNS の*レコード セットの容量使用率*メトリックは、DNS でゾーンのレコード セットの容量使用率をパーセンテージで示します。 Azure DNS 内のすべての DNS ゾーンには、ゾーンで許容されるレコードセットの最大数を定義するレコードセット制限が適用されます ([DNS 制限](dns-zones-records.md#limits)参照)。 このため、このメトリックは、レコードセットの上限にどの程度近づいているかを示します。 たとえば、DNS ゾーンで 500 個のレコードセットが構成されており、ゾーンの既定のレコードセット制限が 5000 だったとすると、RecordSetCapacityUtilization メトリックの値は 10% になります (5000 を 500 で除算した値)。 測定単位は **[Percentage]\(パーセンテージ\)** で、**[集計]** タイプは **[最大]** です。 このメトリックを表示するには、Azure Portal の [監視] タブからメトリックス エクスプローラー エクスペリエンス (プレビュー) を選択します。 [リソース] ドロップダウン リストから DNS ゾーンを選択して、[レコード セット容量使用率] メトリックを選択してから、[集計] として [最大] を選択します。 次のスクリーン ショットに、例を示します。 メトリックス エクスプローラー エクスペリエンスおよびグラフ作成機能の詳細については、「[Azure Monitor メトリックス エクスプローラー](../azure-monitor/platform/metrics-charts.md)」を参照してください。 

![レコード セット数](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*図: Azure DNS のレコード セット容量使用率メトリック*

## <a name="alerts-in-azure-dns"></a>Azure DNS のアラート
Azure Monitor には、使用可能なメトリックの値を基準にアラートを送信する機能があります。 新しいアラート構成エクスペリエンスで DNS メトリックを使用できます。 [Azure Monitor アラート ドキュメント](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)で詳しく説明されているとおり、リソースとして DNS ゾーンを選択し、メトリック信号の種類やアラートのロジック、**期間**や**頻度**などのパラメーターを構成できます。 さらに、アラート条件に一致した場合の[アクション グループ](../azure-monitor/platform/action-groups.md)を定義すれば、選択したアクションを通してアラートを送信することもできます。 Azure Monitor メトリックのアラートを構成する方法の詳細については、[Azure Monitor を使用してアラートを作成、表示、管理する](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)を参照してください。 

## <a name="next-steps"></a>次の手順
- [Azure DNS](dns-overview.md) の詳細を学習する。

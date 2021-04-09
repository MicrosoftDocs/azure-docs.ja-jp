---
title: Linux Azure Diagnostics を使用したイベントの集計
description: Azure Service Fabric クラスターの監視と診断に LAD を使用したイベントの集計と収集について説明します。
author: srrengar
ms.topic: conceptual
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: 453258bb5768a2faa8f4e42cce86d02125283026
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96002172"
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Linux Azure Diagnostics を使用したイベントの集計と収集
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Azure Service Fabric クラスターを実行している場合、1 か所ですべてのノードのログを収集することをお勧めします。 1 か所でログを収集すると、クラスター内の問題と、そのクラスターで実行されているアプリケーションやサービスで発生する問題の分析と解決に役立ちます。

ログをアップロードして収集する方法として、Linux Azure Diagnostics (LAD) 拡張機能を使用できます。この機能を使用すると、ログが Azure Storage にアップロードされますが、Azure Application Insights や Event Hubs にログを送信することもできます。 また、外部プロセスを使用してストレージからイベントを読み取り、[Azure Monitor ログ](./service-fabric-diagnostics-oms-setup.md)などの分析プラットフォーム製品や別のログ解析ソリューションに配置することもできます。

## <a name="log-and-event-sources"></a>ログとイベントのソース

### <a name="service-fabric-platform-events"></a>Service Fabric プラットフォームのイベント
Service Fabric では、操作イベントやランタイム イベントなどのすぐに使用できるログを [LTTng](https://lttng.org) を介して生成します。 これらのログは、クラスターの Resource Manager テンプレートで指定された場所に保存されます。 ストレージ アカウントの詳細を取得または設定するには、タグ **AzureTableWinFabETWQueryable** を検索し、**StoreConnectionString** を探してください。

### <a name="application-events"></a>アプリケーション イベント
 ソフトウェアをインストルメント化するときに指定した、アプリケーションとサービスのコードから生成されたイベント。 テキスト ベースのログ ファイルを書き込む任意のログ記録ソリューションを使用できます (たとえば、LTTng)。 詳細については、アプリケーションでトレースを実行する方法を LTTng に関するドキュメントで参照してください。

[ローカル コンピューターの開発のセットアップでのサービスの監視と診断](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

## <a name="deploy-the-diagnostics-extension"></a>診断拡張機能のデプロイ
ログ収集の最初の手順は、Service Fabric クラスター内の各 VM に診断拡張機能をデプロイすることです。 診断拡張機能を使用すると、各 VM のログが収集され、指定したストレージ アカウントにアップロードされます。 

クラスター作成の一環としてクラスター内の VM に診断拡張機能をデプロイするには、 **[診断]** を **[オン]** に設定します。 クラスターの作成後、ポータルを使用してこの設定を変更することはできません。そのため、Resource Manager テンプレートで適切な変更を行う必要があります。

これにより、LAD エージェントが指定されたログ ファイルを監視するように構成されます。 新しい行がファイルに追加されるたびに、指定したストレージ (テーブル) に送信される syslog エントリが作成されます。


## <a name="next-steps"></a>次のステップ

1. 問題をトラブルシューティングするときに調査する必要があるイベントの詳細については、[LTTng のドキュメント](https://lttng.org/docs)と [LAD の使用](../virtual-machines/extensions/diagnostics-linux.md)に関するページを参照してください。
2. メトリックの収集、クラスターにデプロイされた Containers の監視、ログの視覚化に役立つように [Log Analytics エージェントを設定](service-fabric-diagnostics-event-analysis-oms.md)します。 

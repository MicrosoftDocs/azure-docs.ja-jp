---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.date: 07/09/2020
ms.openlocfilehash: de5d3f8f32e928c77ffd6028ec764793ab7229ac
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86495341"
---
## <a name="business-disaster-recovery"></a>ビジネスのディザスター リカバリー

このセクションでは、障害が発生した場合でもアプリやサービスを実行し続ける Azure Time Series Insights の機能 (*ビジネス ディザスター リカバリー*とも呼ばれます) について説明します。

### <a name="high-availability"></a>高可用性

Azure Time Series Insights は、Azure サービスとして、Azure リージョン レベルでの冗長性を利用して特定の "*高可用性*" 機能を提供します。 たとえば、Azure では、Azure の*複数のリージョンにわたる可用性*機能を通じて、ディザスター リカバリー機能がサポートされています。

Azure を通じて提供される (および、すべての Azure Time Series Insights インスタンスで利用可能な) 追加の高可用性機能として、次のようなものがあります。

- **フェールオーバー**:Azure では [geo レプリケーションと負荷分散](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)が提供されます。
- **データ復元**と**ストレージ復旧**: Azure では、[データを保持および復旧するためのいくつかのオプション](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption)が提供されます。
- **Azure Site Recovery**:Azure は、[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) を通じたサイトの復旧機能を提供します。
- **Azure Backup**:[Azure Backup](https://docs.microsoft.com/azure/backup/backup-architecture) は、Azure VM のオンプレミス バックアップとクラウド内バックアップの両方をサポートしています。

デバイスやユーザーにグローバルで複数のリージョンにわたる高可用性を提供するには、必ず関連する Azure 機能を有効にしてください。

> [!NOTE]
> Azure が複数のリージョンにわたる可用性を有効にするように構成されている場合、Azure Time Series Insights でさらに複数のリージョンにわたる可用性を構成する必要はありません。

### <a name="iot-and-event-hubs"></a>IoT ハブとイベント ハブ

次のような一部の Azure IoT サービスにも、組み込みのビジネス ディザスター リカバリー機能が含まれています。

- [Azure IoT Hub の高可用ディザスター リカバリー](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr) (リージョン内の冗長性が含まれます)
- [Azure Event Hubs ポリシー](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Azure Storage の冗長性](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

Azure Time Series Insights を他のサービスと統合すると、ディザスター リカバリーの可能性が向上します。 たとえば、イベント ハブに送信されたテレメトリが、バックアップ Azure Blob Storage データベースに保持される場合があります。

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

Azure Time Series Insights のデータ、アプリ、およびサービスが中断された場合でも、それらの実行を維持するためのいくつかの方法があります。 

ただし、次の目的で、Azure Time Series 環境の完全なバックアップ コピーも要であると判断する場合があります。

- データとトラフィックのリダイレクト先となる Azure Time Series Insights 専用の "*フェールオーバー インスタンス*" として
- データおよび監査情報を保持するため

一般に、Azure Time Series Insights 環境を複製するための最善の方法は、バックアップ Azure リージョンに 2 つ目の Azure Time Series Insights 環境を作成することです。 イベントは、プライマリ イベント ソースからこのセカンダリ環境にも送信されます。 必ず 2 つ目の専用コンシューマー グループを使用してください。 前述のとおり、そのソースのビジネス ディザスター リカバリーのガイドラインに従います。

重複する環境を作成するには、以下の操作を行います。

1. 2 つ目のリージョンに環境を作成します。 詳細については、[Azure portal で新しい Azure Time Series Insights 環境を作成する](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)方法に関するページを参照してください。
1. イベント ソースの 2 つ目の専用コンシューマー グループを作成します。
1. そのイベント ソースを新しい環境に接続します。 必ず 2 つ目の専用コンシューマー グループを指定してください。
1. Azure Time Series Insights の [IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) と [Event Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) のドキュメントを確認します。

イベントが発生した場合:

1. 障害インシデントの発生時にプライマリ リージョンが影響を受けた場合は、運用をバックアップの Azure Time Series Insights 環境に経路変更します。
1. 2 つ目のリージョンを使用して、すべての Azure Time Series Insights テレメトリとクエリ データをバックアップおよび復元します。

> [!IMPORTANT]
> フェールオーバーが発生した場合:
> 
> * 遅延も発生することがあります。
> * 操作が再ルーティングされるため、メッセージの処理に瞬間的なスパイクが発生する可能性もあります。
> 
> 詳細については、[Azure Time Series Insights の待ち時間の短縮](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency)に関するページを参照してください。


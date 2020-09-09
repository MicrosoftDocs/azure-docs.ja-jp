---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 9ac7966538102273b91d6b7f15b90e18ceedd421
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779927"
---
メトリックも表示することで、デバイスのパフォーマンスを監視したり、場合によっては、デバイスの問題のトラブルシューティングを行ったりできます。

選択したデバイス メトリックのグラフを作成するには、Azure portal 内で次の手順を実行します。

1. ご利用のリソースの場合は、Azure portal 内で、 **[監視] > [メトリック]** に移動し、 **[メトリックの追加]** を選択します。

    ![メトリックを追加する](media/data-box-edge-gateway-view-metrics/view-metrics-1.png)

2. リソースは自動的に設定されます。  

    ![現在のリソース](media/data-box-edge-gateway-view-metrics/view-metrics-2.png)

    別のリソースを指定するには、そのリソースを選択します。 **[リソースの選択]** ブレードで、サブスクリプション、リソース グループ、リソースの種類、およびメトリックを表示する特定のリソースを選択し、 **[適用]** を選択します。

    ![別のリソースを選択する](media/data-box-edge-gateway-view-metrics/view-metrics-3.png)

3. ドロップダウン リストから、ご自分のデバイスを監視するためのメトリックを選択します。 これらのメトリックの全一覧については、「[デバイスのメトリック](#metrics-on-your-device)」を参照してください。

4. ドロップダウン リストからメトリックを選択すると、集計を定義することもできます。 集計は、指定した期間にわたって集計された実際の値を示します。 集計値については、平均値、最小値、または最大値を選択できます。 [平均]、[最大]、または [最小] から集計を選択します。

    ![グラフを表示する](media/data-box-edge-gateway-view-metrics/view-metrics-4.png)

5. 選択したメトリックに複数のインスタンスがある場合は、分割オプションを使用できます。 **[Apply splitting]\(分割の適用\)** を選択し、内訳を表示する値を選択します。

    ![[Apply splitting]\(分割の適用\)](media/data-box-edge-gateway-view-metrics/view-metrics-5.png)

6. いくつかのインスタンスのみの内訳を表示する場合は、データをフィルター処理できます。 たとえば、この場合に、ご自分のデバイス上にある接続されている 2 つのネットワーク インターフェイスのみのネットワーク スループットを表示するには、それらのインターフェイスをフィルター処理できます。 **[フィルターの追加]** を選択し、フィルター処理するネットワーク インターフェイスの名前を指定します。

    ![[フィルターの追加]](media/data-box-edge-gateway-view-metrics/view-metrics-6.png)

7. また、ダッシュボードにグラフをピン留めすると、簡単にアクセスできます。

    ![[ダッシュボードにピン留めする]](media/data-box-edge-gateway-view-metrics/view-metrics-7.png)

8. グラフのデータを Excel スプレッドシートにエクスポートしたり、共有できるグラフへのリンクを入手したりするには、コマンド バーから [共有] オプションを選択します。

    ![データのエクスポート](media/data-box-edge-gateway-view-metrics/view-metrics-8.png)

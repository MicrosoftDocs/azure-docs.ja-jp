---
title: 接続モニターから接続モニターに移行する
titleSuffix: Azure Network Watcher
description: 接続モニターから接続モニターに移行する方法について説明します。
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: d4ab5361d245ad1ee10d43184cc0a2d65fed2054
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101730033"
---
# <a name="migrate-to-connection-monitor-from-connection-monitor-classic"></a>接続モニター (クラシック) から接続モニターに移行する

> [!IMPORTANT]
> 2021 年 7 月 1 日以降、接続モニター (クラシック) に新しい接続モニターを追加することはできませんが、2021 年 7 月 1 日より前に作成された既存の接続モニターは引き続き使用することができます。 現在のワークロードに対するサービスの中断を最小限に抑えるには、2024 年 2 月 29 日より前に、[接続モニター (クラシック) から Azure Network Watcher の新しい接続モニターにテストを移行](migrate-to-connection-monitor-from-connection-monitor-classic.md)します。

数回のクリックで、既存の接続モニターを改善された新しい接続モニターにダウンタイムなしで移行できます。 利点の詳細については、[接続モニター](./connection-monitor-overview.md)に関する記事を参照してください。

## <a name="key-points-to-note"></a>注意する点

この移行は、次の結果を生み出すために役立ちます。

* エージェントとファイアウォールの設定は現状のままです。 変更の必要はありません。 
* 既存の接続モニターは、接続モニター -> テストグループ -> テスト形式にマップされます。 **[編集]** を選択することで、新しい接続モニターのプロパティを表示して変更したり、テンプレートをダウンロードして接続モニターの変更を行い、それを Azure Resource Manager 経由で送信したりできます。 
* Network Watcher 拡張機能を備えた Azure 仮想マシンでは、ワークスペースとメトリックの両方にデータが送信します。 接続モニターでは、古いメトリック(ProbesFailedPercent と AverageRoundtripMs) の代わりに、新しいメトリック (ChecksFailedPercent と RoundTripTimeMs) 経由でデータを使用できるようになります。 古いメトリックは、ProbesFailedPercent -> ChecksFailedPercent および AverageRoundtripMs -> RoundTripTimeMs として、新しいメトリックに移行されます。
* データの監視:
   * **アラート**:新しいメトリックに自動的に移行されます。
   * **ダッシュボードと統合**:メトリック セットを手動で編集する必要があります。 
    
## <a name="prerequisites"></a>前提条件

カスタム ワークスペースを使用している場合は、サブスクリプションと Log Analytics ワークスペースのリージョンで Network Watcher が有効になっていることを確認してください。 

## <a name="migrate-the-connection-monitors"></a>接続モニターを移行する

1. 古い接続モニターを新しいものに移行するには、 **[接続モニター]** を選択し、 **[接続モニターの移行]** を選択します。

    ![接続モニターを接続モニターに移行する例を示すスクリーンショット。](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. 移行するサブスクリプションと接続モニターを選択し、 **[Migrate selected]\(選択したものを移行\)** を選択します。 

数回のクリックで、既存の接続モニターが接続モニターに移行されます。 CM (クラシック) から CM に移行すると、CM (クラシック) でのモニターは表示できなくなります

これで、接続モニターのプロパティのカスタマイズ、既定のワークスペースの変更、テンプレートのダウンロード、および移行の状態を確認できます。 

移行が開始されると、次の変更が行われます。 
* Azure Resource Manager のリソースで新しい接続モニターに変更されます。
    * 接続モニターの名前、リージョン、およびサブスクリプションは未変更のままです。 リソース ID は影響を受けません。
    * 接続モニターをカスタマイズしていない限り、既定の Log Analytics ワークスペースは、サブスクリプションと接続モニターのリージョンに作成されます。 このワークスペースは、監視データが格納される場所です。 テスト結果データも、メトリックに格納されます。
    * 各テストは、 *defaultTestGroup* というテスト グループに移行されます。
    * 移行元と移行先のエンドポイントが作成され、新しいテスト グループで使用されます。 既定の名前は *defaultSourceEndpoint* と *defaultDestinationEndpoint* です。
    * 宛先ポートとプローブ間隔は、*defaultTestConfiguration* というテスト構成に移動されます。 ポートの値に基づいて、プロトコルが設定されます。 成功のしきい値などのオプションのプロパティは、空白のままです。
* メトリック アラートは、接続モニター メトリック アラートに移行されます。 メトリックは異なっているため、変更されます。 詳細については、「[接続モニターによるネットワーク接続の監視](./connection-monitor-overview.md#metrics-in-azure-monitor)」をご覧ください。
* 移行された接続モニターは、古い接続モニター ソリューションとしては表示されなくなります。 それらは、接続モニターでのみ使用できるようになります。
* Power BI や Grafana のダッシュボードなどの外部統合やセキュリティ情報イベント管理 (SIEM) システムとの統合は、手動で移行する必要があります。 これは、ユーザーがセットアップを移行するために実行する必要がある唯一の手順です。

## <a name="next-steps"></a>次の手順

接続モニターの詳細については、以下を参照してください。
* [Network Performance Monitor から接続モニターに移行する](./migrate-to-connection-monitor-from-network-performance-monitor.md)
* [Azure portal を使用して接続モニターを作成する](./connection-monitor-create-using-portal.md)

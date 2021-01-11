---
title: 接続モニターから接続モニター (プレビュー) に移行する
titleSuffix: Azure Network Watcher
description: 接続モニターから接続モニター (プレビュー) に移行する方法について説明します。
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: vinigam
ms.openlocfilehash: ddf6e326df876229d32ef15983f76879836f1fca
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701310"
---
# <a name="migrate-to-connection-monitor-preview-from-connection-monitor"></a>接続モニターから接続モニター (プレビュー) に移行する

1 回のクリックで、既存の接続モニターを改善された新しい接続モニター (プレビュー) ダウンタイムなしで移行できます。 利点の詳細については、「[接続モニター (プレビュー)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)」を参照してください。

## <a name="key-points-to-note"></a>注意する点

* エージェントとファイアウォールの設定は現状のまま (操作の必要なし) です。 
* 既存の接続モニターは、接続モニター (プレビュー) -> テストグループ -> テスト形式にマップされます。 ユーザーは *[編集]* をクリックして新しい接続モニターのプロパティを表示して変更したり、テンプレートをダウンロードして接続モニターの変更を行い、それを Azure Resource Manager 経由で送信したりできます。 
* Network Watcher 拡張機能を備えた Azure 仮想マシンでは、ワークスペースとメトリックの両方にデータが送信します。 接続モニターでは、古いメトリック(ProbesFailedPercent と AverageRoundtripMs) の代わりに、新しいメトリック (ChecksFailedPercent (プレビュー) と RoundTripTimeMs (プレビュー)) 経由でデータを使用できるようになります。 
* データの監視
    * アラート – 移行の一環として新しいメトリックに移行されます。
    * ダッシュボードと統合 – メトリック セットを手動で編集する必要があります。 
    
## <a name="prerequisites"></a>前提条件

カスタム ワークスペースを使用している場合は、Log Analytics ワークスペースのサブスクリプションとリージョンで Network Watcher が有効になっていることを確認してください。 

## <a name="steps-to-migrate-from-connection-monitor-to-connection-monitor-preview"></a>接続モニターから接続モニター (プレビュー) に移行するためのステップ

1. [接続モニター] をクリックし、[Migrate Connection Monitors]\(接続モニターの移行\) に移動して、接続モニターを古いソリューションから新しいソリューションに移行します。

    ![接続モニターを接続モニター (プレビュー) に移行する例を示すスクリーンショット](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. サブスクリプションと接続モニターを選択し、[Migrate selected]\(選択した移行\) をクリックします。 1 回のクリックで既存の接続モニターを接続モニター (プレビュー) に移行する 
1. 接続モニターのプロパティのカスタマイズしたり、既定のワークスペースを変更したり、テンプレートをダウンロードしたり、移行の状態を確認したりできます。 
1. 移行が開始されると、次のような変更が行われます。 
    1. Azure Resource Manager のリソースで新しい接続モニターに変更されます。
        1. 接続モニターの名前、リージョン、およびサブスクリプションは未変更のままです。 そのため、リソース ID は影響を受けません。
        1. カスタマイズしない限り、既定の Log Analytics ワークスペースは、接続モニターのリージョンとサブスクリプションに作成されます。 このワークスペースには、監視データが格納されます。 テスト結果のデータは、メトリックにも格納されます。
        1. 各テストは、* defaultTestGroup* というテスト グループに移行されます。
        1.  移行元と移行先のエンドポイントが作成され、作成されたテスト グループで使用されます。 既定の名前は *defaultSourceEndpoint* と *Defaultsourceendpoint* です。
        1. 宛先ポートとプローブ間隔は、*defaultTestConfiguration* というテスト構成に移動されます。 ポートの値に基づいて、プロトコルが設定されます。 成功のしきい値などのオプションのプロパティは、空白のままです。
    1. メトリック アラートは、接続モニター (プレビュー) メトリック アラートに移行されます。 メトリックは異なる <link to metric section in the doc> であるため、変更されます。
    1. 移行された接続モニターは、古い接続モニター ソリューションには表示されず、接続モニター (プレビュー) でのみ使用できるようになります。
    1. また、外部統合 (Power BI のダッシュボード、Grafana、SIEM システムとの統合など) は、ユーザーが直接移行する必要があります。 これは、ユーザーがセットアップを移行するために実行する必要がある唯一のステップです。

## <a name="next-steps"></a>次のステップ

* [Network Performance Monitor から接続モニター (プレビュー) に移行する方法](migrate-to-connection-monitor-preview-from-network-performance-monitor.md)について説明する
* [Azure portal を使用して接続モニター (プレビュー) を作成する方法](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)について説明する

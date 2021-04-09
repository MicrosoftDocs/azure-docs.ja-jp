---
title: Azure portal で 1 つの仮想マシンまたは仮想マシン スケール セットで Azure Monitor を有効にする
description: Azure portal を使用して、1 つの Azure 仮想マシンまたは仮想マシン スケール セットで VM insights を有効にする方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 076fcab7b0747a7993407edd65f9d08efc27309f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035587"
---
# <a name="enable-azure-monitor-for-single-virtual-machine-or-virtual-machine-scale-set-in-the-azure-portal"></a>Azure portal で 1 つの仮想マシンまたは仮想マシン スケール セットで Azure Monitor を有効にする
この記事では、Azure portal を使用して、仮想マシンまたは仮想マシン スケール セットで VM insights を有効にする方法について説明します。 この手順は、次に対して使用できます。

- Azure 仮想マシン
- Azure 仮想マシン スケール セット
- Azure Arc に接続されたハイブリッド仮想マシン

## <a name="prerequisites"></a>前提条件

- [Log Analytics ワークスペースを作成して構成](./vminsights-configure-workspace.md)します。 あるいは、このプロセス中に新しいワークスペースを作成できます。
- 「[サポートされるオペレーティング システム](./vminsights-enable-overview.md#supported-operating-systems)」を参照して、有効にする仮想マシンまたは仮想マシン スケール セットのオペレーティング システムがサポートされていることを確認してください。 

## <a name="enable-vm-insights"></a>VM insights を有効にする

Azure portal から、 **[仮想マシン]** 、 **[仮想マシン スケール セット]** 、または **[サーバー - Azure Arc]** を選択し、一覧からリソースを選択します。 メニューの **[監視]** セクションで、 **[Insights]\(分析情報\)** 、次に **[Enable]\(有効にする\)** を選択します。 次の例は Azure 仮想マシンを示していますが、このメニューは Azure 仮想マシン スケール セットまたは Azure Arc の場合も同様です。

![VM の VM insights を有効にする](media/vminsights-enable-portal/enable-vminsights-vm-portal.png)

仮想マシンがまだ Log Analytics ワークスペースに接続されていない場合は、ワークスペースを選択するよう求められます。 以前に[ワークスペースを作成](../logs/quick-create-workspace.md)していない場合は、サブスクリプションで仮想マシンまたは仮想マシン スケール セットがデプロイされる場所の既定値を選択できます。 このワークスペースは、まだ存在しない場合、作成されて構成されます。 既存のワークスペースを選択した場合、そのワークスペースは VM insights 用に構成されます (まだ構成されていない場合)。

> [!NOTE]
> 以前に VM insights 用に構成されていないワークスペースを選択した場合、このワークスペースには *VMInsights* 管理パックが追加されます。 これは、VM insights が有効になっているかどうかにかかわらず、既にそのワークスペースに接続されているすべてのエージェントに適用されます。 パフォーマンス データはこれらの仮想マシンから収集され、*InsightsMetrics* テーブルに格納されます。

![ワークスペースを選択](media/vminsights-enable-portal/select-workspace.png)

構成が実行されているときに、ステータス メッセージを受け取ります。

>[!NOTE]
>仮想マシン スケール セットに手動アップグレード モデルを使用する場合は、インスタンスをアップグレードして設定を完了します。 **[インスタンス]** ページの **[設定]** セクションからアップグレードを開始できます。

![VM insights の監視のデプロイ処理を有効にする](media/vminsights-enable-portal/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>次のステップ

* 検出されたアプリケーションの依存関係を表示するには、[VM insights マップの使用](vminsights-maps.md)に関する記事をご覧ください。 
* ボトルネック、全体的な使用率、VM のパフォーマンスを識別するには、[Azure VM のパフォーマンスの表示](vminsights-performance.md)に関するページを参照してください。

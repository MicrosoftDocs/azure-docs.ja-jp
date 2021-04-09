---
title: チュートリアル - Azure Monitor for VMs を使用してハイブリッド マシンを監視する
description: Azure Monitor でハイブリッド マシンからデータを収集して分析する方法について説明します。
ms.topic: tutorial
ms.date: 09/23/2020
ms.openlocfilehash: 409ad0976e02e42e385e22a103cfc06af5a4f3f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100587701"
---
# <a name="tutorial-monitor-a-hybrid-machine-with-azure-monitor-for-vms"></a>チュートリアル:Azure Monitor for VMs を使用してハイブリッド マシンを監視する

[Azure Monitor](../overview.md) は、詳細な分析と相関のために、ハイブリッド マシンから Log Analytics ワークスペースに直接データを収集できます。 通常、この作業には、スクリプト、手動、自動のいずれかの方法で、実際の構成管理基準に従って [Log Analytics エージェント](../../../azure-monitor/agents/agents-overview.md#log-analytics-agent)をマシンにインストールする作業が伴います。 最近、Arc 対応サーバーが、Windows と Linux 用の [VM 拡張機能](../manage-vm-extensions.md)である Log Analytics エージェントと Dependency Agent のインストールに対応するようになったため、Azure Monitor で Azure 以外の VM からデータを収集することが可能になりました。

このチュートリアルでは、簡単な一連の手順に従って Azure Monitor for VMs を有効にし、Linux マシンまたは Windows マシンを構成して、それらの VM からデータを収集する方法について説明します。こうすることによって、エクスペリエンスが効率化され、所要時間が短縮されます。  

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

* VM 拡張機能の機能は、[サポートされているリージョン](../overview.md#supported-regions)の一覧でのみ使用できます。

* 「[サポートされるオペレーティング システム](../../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems)」を参照し、有効にするサーバーのオペレーティング システムが Azure Monitor for VMs でサポートされていることを確認してください。

* 「[Log Analytics エージェントの概要](../../../azure-monitor/agents/log-analytics-agent.md#network-requirements)」で、Log Analytics エージェントのファイアウォールの要件を確認します。 Azure Monitor for VMs マップの Dependency Agent でデータ自体が送信されることはないため、ファイアウォールやポートを変更する必要はありません。

## <a name="sign-in-to-azure-portal"></a>Azure Portal にサインインする

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="enable-azure-monitor-for-vms"></a>Azure Monitor for VMs の有効化

1. Azure portal で Azure Arc サービスを起動します。 **[すべてのサービス]** をクリックし、 **[マシン - Azure Arc]** を探して選択してください。

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="[すべてのサービス] で Arc 対応サーバーを検索する" border="false":::

1. **[マシン - Azure Arc]** ページで、[クイックスタート](quick-enable-hybrid-vm.md)記事で作成した接続済みのマシンを選択します。

1. 左ペインの **[監視]** セクションで、 **[Insights]\(分析情報\)** 、 **[Enable]\(有効にする\)** の順に選択します。

    :::image type="content" source="./media/tutorial-enable-vm-insights/insights-option.png" alt-text="左側のメニューから [Insights]\(分析情報\) オプションを選択する" border="false":::

1. Azure Monitor の **[Insights のオンボード]** ページで、ワークスペースを作成するように求められます。 このチュートリアルでは、既存の Log Analytics ワークスペースを選択することは、既にお持ちだとしてもお勧めしません。 登録されている接続済みのマシンと同じリージョン内の一意の名前が付いたワークスペース (既定値) を選択してください。 このワークスペースは自動的に作成、構成されます。

    :::image type="content" source="./media/tutorial-enable-vm-insights/enable-vm-insights.png" alt-text="[VM 用 Azure Monitor を有効にする] ページ" border="false":::

1. 構成が実行されている間、ステータス メッセージが表示されます。 接続済みのマシンに拡張機能がインストールされるため、このプロセスは数分かかります。

    :::image type="content" source="./media/tutorial-enable-vm-insights/onboard-vminsights-vm-portal-status.png" alt-text="[VM 用 Azure Monitor を有効にする] の進行状況を示すステータス メッセージ" border="false":::

    完了すると、マシンが正常にオンボードされ、分析情報が正常にデプロイされたことを示すメッセージが表示されます。

## <a name="view-data-collected"></a>収集されたデータを表示する

デプロイと構成が完了したら、 **[Insights]\(分析情報\)** を選択し、 **[パフォーマンス]** タブを選択します。[パフォーマンス] タブには、マシンのゲスト オペレーティング システムから収集されるパフォーマンス カウンターの選択グループが表示されます。 下にスクロールすると、さらにカウンターを表示できます。グラフ上にマウスを移動すると、Log Analytics VM 拡張機能がマシンにインストールされた時点から収集された平均とパーセンタイルが表示されます。

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-performance-charts.png" alt-text="選択されたマシンを対象とする Azure Monitor for VMs のパフォーマンス グラフ" border="false":::

**[マップ]** を選択して、マシンで実行されているプロセスとその依存関係を示すマップ機能を開きます。 プロパティ ウィンドウがまだ開いていない場合は、 **[プロパティ]** を選択して開きます。

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-map.png" alt-text="選択されたマシンを対象とする Azure Monitor for VMs のマップ" border="false":::

マシンのプロセスを展開します。 プロセスの 1 つを選択すると、詳細を表示したり、その依存関係を強調表示したりできます。

マシンをもう一度選択し、 **[ログ イベント]** を選択します。 マシンの [Log Analytics] ワークスペースに格納されているテーブルのリストが表示されます。 このリストは、Windows マシンと Linux マシンのどちらを使用しているかによって異なります。 **[イベント]** テーブルを選択します。 **[イベント]** テーブルには、Windows イベント ログのすべてのイベントが含まれます。 Log Analytics が開き、収集されたイベント ログエントリを取得するための単純なクエリが表示されます。

## <a name="next-steps"></a>次の手順

Azure Monitor の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Azure Monitor の概要](../../../azure-monitor/overview.md)
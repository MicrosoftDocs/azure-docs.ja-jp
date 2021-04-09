---
title: Azure Monitor を使用して Azure 仮想マシンを監視する
description: Azure Monitor で Azure 仮想マシンのデータを収集して分析する方法について説明します。
ms.service: azure-monitor
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 03/10/2020
ms.openlocfilehash: 7efd8baf54aeacbd2f55640240a15f2517dcd904
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046926"
---
# <a name="quickstart-monitor-an-azure-virtual-machine-with-azure-monitor"></a>Azure Monitor を使用して Azure 仮想マシンを監視する」を参照してください。
[Azure Monitor](../overview.md) では、作成された時点から、Azure 仮想マシンからのデータ収集が開始されます。 このクイック スタートでは、Azure VM に対して自動的に収集されるデータと、そのデータを Azure portal で表示する方法について簡単に説明します。 次に、VM で [VM insights](../vm/vminsights-overview.md) を有効にします。これにより、VM 上のエージェントで、プロセスとその依存関係を含むゲスト オペレーティング システムからデータを収集して分析できるようになります。

このクイック スタートでは、既存の Azure Virtual Machines があることを前提とします。 ない場合は、VM のクイック スタートに従って、[Windows VM](../../virtual-machines/windows/quick-create-portal.md) を作成するか、[Linux VM](../../virtual-machines/linux/quick-create-cli.md) を作成することができます。

Azure リソースから収集される監視データの詳細な説明については、「[Azure Monitor を使用した Azure 仮想マシンの監視](./monitor-vm-azure.md)」を参照してください。


## <a name="complete-the-monitor-an-azure-resource-quickstart"></a>Azure リソースの監視のクイック スタートを完了する
「[Azure Monitor を使用して Azure リソースを監視する](../essentials/quick-monitor-azure-resource.md)」を完了すると、サブスクリプション内の VM の概要ページ、アクティビティ ログ、およびメトリックを表示できます。 Azure VM では、他の Azure リソースと同じ監視データが収集されますが、これはホスト VM についてのみです。 このクイック スタートの残りの部分では、ゲスト オペレーティング システムとそのワークロードの監視に焦点を当てます。


## <a name="enable-vm-insights"></a>VM insights を有効にする
メトリックとアクティビティ ログはホスト VM について収集されますが、ゲスト オペレーティング システムとそのワークロードから監視データを収集して分析するには、エージェントといくらかの構成が必要です。 VM insights によってこれらのエージェントがインストールされ、仮想マシンを監視するための強力な機能が追加されます。

1. 仮想マシンのメニューに移動します。
2. **[概要]** ページのタイルで **[分析情報に移動]** をクリックするか、**[監視]** メニューで **[分析情報]** をクリックします。

    ![[概要] ページ](media/quick-monitor-azure-vm/overview-insights.png)

3. 仮想マシンで VM insights がまだ有効になっていない場合は、 **[有効にする]** をクリックします。 

    ![分析情報を有効にする](media/quick-monitor-azure-vm/enable-insights.png)

4. 仮想マシンがまだ Log Analytics ワークスペースに接続されていない場合は、既存のワークスペースを選択するか、新しいワークスペースを作成するように求められます。 仮想マシンと同じリージョン内の一意の名前を持つワークスペースである既定値を選択します。

    ![ワークスペースを選択](media/quick-monitor-azure-vm/select-workspace.png)

5. 拡張機能が有効になっていて、仮想マシンにエージェントがインストールされているため、オンボードには数分かかります。 完了すると、分析情報が正常にデプロイされたことを示すメッセージが表示されます。 **[Azure Monitor]** をクリックして、VM insights を開きます。

    ![Azure Monitor を開く](media/quick-monitor-azure-vm/azure-monitor.png)

6. VM と、サブスクリプション内のオンボードされている他の VM が表示されます。 サブスクリプション内のオンボードされていない仮想マシンを表示するには、**[監視されない]** タブを選択します。

    ![はじめに](media/quick-monitor-azure-vm/get-started.png)


## <a name="configure-workspace"></a>ワークスペースの構成
新しい Log Analytics ワークスペースを作成する場合は、ログを収集するように構成する必要があります。 接続されているすべての仮想マシンに構成が送信されるため、この構成は一度だけ実行する必要があります。

1. **[ワークスペースの構成]** を選択し、ワークスペースを選択します。

2. **[詳細設定]** を選択します。

    ![Log Analytics の [詳細設定]](../vm/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)

### <a name="data-collection-from-windows-vm"></a>Windows VM からのデータ収集


2. **[データ]** を選択してから、 **[Windows イベント ログ]** を選択します。

3. イベント ログを追加するには、ログの名前を入力します。  「**System**」と入力し、プラス記号 ( **+** ) を選択します。

4. テーブルで、重大度の **[エラー]** と **[警告]** のチェック ボックスをオンにします。

5. ページの上部にある **[保存]** を選択して構成を保存します。

### <a name="data-collection-from-linux-vm"></a>Linux VM からのデータ収集

1. **[データ]** 、 **[Syslog]** の順に選択します。

2. イベント ログを追加するには、ログの名前を入力します。  「**Syslog**」と入力し、プラス記号 **+** を選択します。  

3. テーブルで、重大度の **[情報]** 、 **[通知]** 、および **[デバッグ]** の各チェック ボックスをオフにします。 

4. ページの上部にある **[保存]** を選択して構成を保存します。

## <a name="view-data-collected"></a>収集されたデータを表示する

7. 仮想マシンをクリックし、**[Monitoring]\(監視\)** メニューの **[Insights]** タイルにある **[パフォーマンス]** タブを選択します。 これは、VM のゲスト オペレーティング システムから収集されたパフォーマンス カウンターの選択グループを示しています。 下にスクロールすると、さらにカウンターを表示できます。グラフ上にマウスを移動すると、平均とパーセンタイルを異なるタイミングで表示できます。

    ![パフォーマンス ペインを表示するスクリーンショット。](media/quick-monitor-azure-vm/performance.png)

9. **[マップ]** を選択して、仮想マシンで実行されているプロセスとその依存関係を示すマップ機能を開きます。 プロパティ ウィンドウがまだ開いていない場合は、**[プロパティ]** を選択して開きます。

    ![マップ ペインを表示するスクリーンショット。](media/quick-monitor-azure-vm/map.png)

11. 仮想マシンのプロセスを展開します。 プロセスの 1 つを選択すると、詳細を表示したり、その依存関係を強調表示したりできます。

    ![展開された仮想マシンのプロセスを示すマップ ペインを表示するスクリーンショット。](media/quick-monitor-azure-vm/processes.png)

12. 仮想マシンをもう一度選択し、**[ログイベント]** を選択します。 

    ![ログ イベント](media/quick-monitor-azure-vm/log-events.png)

13. 仮想マシンの [Log Analytics] ワークスペースに格納されているテーブルのリストが表示されます。 このリストは、Windows 仮想マシンと Linux 仮想マシンのどちらを使用しているかによって異なります。 **[イベント]** テーブルをクリックします。 これには、Windows イベント ログのすべてのイベントが含まれます。 Log Analytics が開き、イベント ログエントリを取得するための単純なクエリが表示されます。

    ![Log Analytics](media/quick-monitor-azure-vm/log-analytics.png)

## <a name="next-steps"></a>次のステップ
このクイック スタートでは、仮想マシンで VM insights を有効にして、ゲスト オペレーティング システムのイベントが収集されるように Log Analytics ワークスペースを構成しました。 データの表示および分析方法を学習する場合は、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Log Analytics でのデータの表示と分析](../logs/log-analytics-tutorial.md)
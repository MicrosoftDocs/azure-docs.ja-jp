---
title: 仮想マシンでのチャーン パターンの監視
description: Azure Site Recovery を使用して保護されている仮想マシンでのチャーン パターンを監視する方法について説明します
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 09/09/2020
ms.author: sharrai
ms.openlocfilehash: aeb89a9d18e4550fa1d6162920d60507fd50c208
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92359868"
---
# <a name="monitoring-churn-patterns-on-virtual-machines"></a>仮想マシンでのチャーン パターンの監視

この記事では、仮想マシンでチャーン パターンを監視するために使用できるさまざまなツールの概要について説明します。 適切なツールを使用することで、どのアプリケーションが高いチャーンの原因であるかを正確に検出し、そのアプリケーションに対して実行できるアクションを簡単に確認できます。

## <a name="for-azure-virtual-machines-windows-or-linux"></a>Azure 仮想マシンの場合 (Windows または Linux)

マシンが Azure でホストされていて、マネージド ディスクまたはアンマネージド ディスクをストレージに使用している場合は、ディスク メトリックを追跡することで、パフォーマンスを簡単に追跡できます。 これにより、アプリケーションの使用パターンを厳密に監視して適切なディスク選択を行うことができます。 さらに、それを使用して、アラート、診断、およびビルド自動化も作成できます。 [詳細については、こちらを参照してください](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/)。

Azure Site Recovery を使用してマシンを保護したら、Azure Monitor Logs と Log Analytics を使用してマシンを監視できます。 [詳細については、こちらを参照してください](./monitor-log-analytics.md)。

使用できるオペレーティング システム固有のツールもあります。

## <a name="for-windows-machines"></a>Windows マシンの場合

Windows オペレーティング システムを実行しているマシンがある場合は、それがオンプレミスであってもそうでなくても、他のいくつかのツールを使用できます。

タスク マネージャーでのディスク使用量の確認とは別に、いつでも **リソース モニター** と **パフォーマンス モニター** を参照できます。 これらのツールは、Windows マシン上に既に存在しています。

### <a name="resource-monitor"></a>リソース モニター

**リソース モニター** には、ハードウェアとソフトウェアのリソースの使用に関する情報がリアルタイムで表示されます。 Windows マシンでリソース モニターを実行するには、次の手順に従います。

1. Win + R キーを押し、「_resmon_」と入力します。
1. resmon ([リソース モニター]) ウィンドウが開いたら、[ディスク] タブに切り替えます。次のビューが表示されます。

    ![リソース モニターの [ディスク] タブ](./media/monitoring-high-churn/resmon-disk-tab.png)

1. 明確な全体像を把握するために、このタブを一定時間継続的に監視する必要があります。 上の例では、_wmiprv.exe_ のチャーンが高いことがわかります。

マシン上で高いチャーンの原因となっているアプリケーションを特定したら、それらのアプリケーションに関連するチャーンに対処するために必要なアクションを実行できます。

### <a name="performance-monitor"></a>パフォーマンス モニター

**パフォーマンス モニター** では、CPU やメモリ使用量などのマシン上のさまざまなアクティビティが監視されます。 Windows マシンでパフォーマンス モニターを実行するには、次の手順に従います。

1. Win + R キーを押し、「_perfmon_」と入力します。
1. Perfmon ([パフォーマンス モニター]) ウィンドウが開くと、次のビューが表示されます。

    ![パフォーマンス モニターの手順 1](./media/monitoring-high-churn/perfmon-step1.png)

1. 右側の **[監視ツール]** フォルダーを展開し、[パフォーマンス モニター] をクリックします。 これにより、次のビューが開き、現在のパフォーマンスに関するリアルタイムの情報が表示されます。

    ![パフォーマンス モニターの手順 2](./media/monitoring-high-churn/perfmon-step1.png)

1. このグラフでは、現在 1 つのモニターだけが監視されています (グラフの右下のテーブルによって示されている [% プロセッサ時間])。 ツールの上部にある **[+]** をクリックすることで、監視する項目をさらに追加できます。
1. 下の図は、カウンターを追加した後、パフォーマンス モニターにどのように表示されるかを示しています。

    ![パフォーマンス モニターの手順 3](./media/monitoring-high-churn/perfmon-step3.png)

パフォーマンス モニターの詳細については、[こちら](/dynamics365/business-central/dev-itpro/administration/monitor-use-performance-monitor-collect-event-trace-data)を参照してください。

## <a name="for-linux-machines"></a>Linux マシンの場合

Linux オペレーティング システムを実行しているマシンがある場合は、それがオンプレミスであってもそうでなくても、チャーン パターンを監視する他のいくつかのツールを使用できます。

### <a name="iotop"></a>Iotop

最も使用されるツールの 1 つは _iotop_ です。 これは、リアルタイムのディスク アクティビティを表示するためのユーティリティです。 I/O を実行しているプロセスと、それらが使用しているディスク帯域幅を一覧表示できます。

コマンド プロンプトを開き、`iotop` コマンド を実行します。

### <a name="iostat"></a>IoStat

IoStat は、システムの入力と出力ストレージ デバイスの統計を収集して表示するシンプルなツールです。 このツールは、多くの場合、デバイス、ローカル ディスク、リモート ディスクなどのストレージ デバイスのパフォーマンスに関する問題をトレースするために使用されます。

コマンド プロンプトを開き、`iostat` コマンド を実行します。

## <a name="next-steps"></a>次の手順

[Azure Monitor](monitor-log-analytics.md) を使用して監視する方法を確認します。
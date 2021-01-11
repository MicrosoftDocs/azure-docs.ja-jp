---
title: VM 用 Azure Monitor とは?
description: AzureVM の正常性とパフォーマンスを監視し、アプリケーション コンポーネントとその依存関係を自動的に検出およびマッピングする Azure Monitor for VMs の概要。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: 5c3cb13d0b2da9370f402083d82397679f2c9343
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89022498"
---
# <a name="what-is-azure-monitor-for-vms"></a>VM 用 Azure Monitor とは?

Azure Monitor for VMs では、実行中のプロセスや他のリソースへの依存関係など、仮想マシンと仮想マシン スケール セットのパフォーマンスと正常性が監視されます。 それは、パフォーマンスのボトルネックとネットワークの問題を識別することによって、重要なアプリケーションのパフォーマンスと可用性を予測するのに役立ち、問題が他の依存関係に関連しているかどうかを把握することにも役立つ可能性があります。

Azure Monitor for VMs では、次で実行する Windows および Linux オペレーティング システムがサポートされます。

- Azure の仮想マシン
- Azure Virtual Machine Scale Sets
- Azure Arc に接続されているハイブリッド仮想マシン
- オンプレミスの仮想マシン
- 別のクラウド環境でホストされている仮想マシン
  



Azure Monitor for VMs では、そのデータを Azure Monitor ログに格納するため、強力な集計とフィルター処理を実現し、時間の経過に伴うデータの傾向を分析できるようになります。 単一の VM 内のこのデータをその仮想マシンから直接表示することも、Azure Monitor を使用して、複数の VM の集計ビューを提供することもできます。

![Azure portal での仮想マシン分析情報のパースペクティブ](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>価格
Azure Monitor for VMs には直接のコストがかかりませんが、Log Analytics ワークスペースでのアクティビティに対して課金されます。 「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」ページに公開されている価格に基づいて、Azure Monitor for VMs は以下に対して課金されます。

- エージェントから取り込まれ、ワークスペースに格納されるデータ。
- ログと正常性データに基づく警告ルール。
- 警告ルールから送信される通知。

ログのサイズは、パフォーマンス カウンターの文字列の長さによって異なり、仮想マシンに割り当てられた論理ディスクおよびネットワーク アダプターの数に応じて大きくなる可能性があります。 Service Map を既に使用している場合、確認できる変更点は、Azure Monitor `InsightsMetrics` データ型に送信される追加のパフォーマンス データだけです。


## <a name="configuring-azure-monitor-for-vms"></a>Azure Monitor for VMs の構成
Azure Monitor for VMs を構成する手順は次のとおりです。 各手順の詳細なガイダンスについては、各リンクに従ってください。

- [Log Analytics ワークスペースを作成します。](vminsights-configure-workspace.md#create-log-analytics-workspace)
- [VMInsights ソリューションをワークスペースに追加します。](vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [監視する仮想マシンと仮想マシン スケールセットにエージェントをインストールします。](vminsights-enable-overview.md)



## <a name="next-steps"></a>次のステップ

- 仮想マシンの監視を有効にするための要件と方法については、[Azure Monitor for VMs のデプロイ](vminsights-enable-overview.md)に関するページを参照してください。


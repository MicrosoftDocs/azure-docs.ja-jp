---
title: VM insights とは
description: Azure VM の正常性とパフォーマンスを監視し、アプリケーション コンポーネントとその依存関係を自動的に検出およびマッピングする VM insights の概要。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: 18e1fdcdee347a057c452f6170f36ec7f1f43244
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046416"
---
# <a name="overview-of-vm-insights"></a>VM insights の概要

VM insights により、実行中のプロセスや他のリソースへの依存関係など、仮想マシンと仮想マシン スケール セットのパフォーマンスと正常性が監視されます。 それは、パフォーマンスのボトルネックとネットワークの問題を識別することによって、重要なアプリケーションのパフォーマンスと可用性を予測するのに役立ち、問題が他の依存関係に関連しているかどうかを把握することにも役立つ可能性があります。

VM insights は、次のマシンで実行される Windows および Linux オペレーティング システムをサポートしています。

- Azure の仮想マシン
- Azure Virtual Machine Scale Sets
- Azure Arc に接続されているハイブリッド仮想マシン
- オンプレミスの仮想マシン
- 別のクラウド環境でホストされている仮想マシン
  

VM insights では、そのデータを Azure Monitor ログに格納するため、強力な集計とフィルター処理を実現し、時間の経過に伴うデータの傾向を分析できるようになります。 単一の VM 内のこのデータをその仮想マシンから直接表示することも、Azure Monitor を使用して、複数の VM の集計ビューを提供することもできます。

![Azure portal での仮想マシン分析情報のパースペクティブ](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>価格
VM insights には直接のコストがかかりませんが、Log Analytics ワークスペースでのアクティビティに対して課金されます。 「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」ページに公開されている価格に基づいて、VM insights は以下に対して課金されます。

- エージェントから取り込まれ、ワークスペースに格納されるデータ。
- ゲストの正常性から収集された正常性状態データ (プレビュー)
- ログと正常性データに基づく警告ルール。
- 警告ルールから送信される通知。

ログのサイズは、パフォーマンス カウンターの文字列の長さによって異なり、仮想マシンに割り当てられた論理ディスクおよびネットワーク アダプターの数に応じて大きくなる可能性があります。 Service Map を既に使用している場合、確認できる変更点は、Azure Monitor `InsightsMetrics` データ型に送信される追加のパフォーマンス データだけです。


## <a name="configuring-vm-insights"></a>VM insights の構成
VM insights を構成する手順は次のとおりです。 各手順の詳細なガイダンスについては、各リンクに従ってください。

- [Log Analytics ワークスペースを作成します。](./vminsights-configure-workspace.md#create-log-analytics-workspace)
- [VMInsights ソリューションをワークスペースに追加します。](./vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [監視する仮想マシンと仮想マシン スケールセットにエージェントをインストールします。](./vminsights-enable-overview.md)



## <a name="next-steps"></a>次のステップ

- 仮想マシンの監視を有効にするための要件と方法については、[VM insights のデプロイ](./vminsights-enable-overview.md)に関するページを参照してください。

---
title: 概念 - Azure Kubernetes Service (AKS) での持続可能なソフトウェア エンジニアリング
description: Azure Kubernetes Service (AKS) での持続可能なソフトウェア エンジニアリングについて説明します。
services: container-service
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: e179b49879b78b2bab738407984c0f50d161114b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572686"
---
# <a name="sustainable-software-engineering-principles-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) での持続可能なソフトウェア エンジニアリングの原則

持続可能なソフトウェア エンジニアリングの原則は、持続可能なアプリケーションの定義、構築、実行に役立つ一連の能力です。 全体的な目標は、アプリケーションのすべての側面の炭素排出量を削減することです。 「[持続可能なソフトウェア エンジニアリングの原則][principles-sse]」に、持続可能なソフトウェア エンジニアリングの原則の概要があります。

持続可能なソフトウェア エンジニアリングについて理解するための重要なアイデアは、それが優先順位と焦点の変化であることです。 多くの場合、ソフトウェアは、高速なパフォーマンスと短い待機時間に焦点を当てる方法で設計および実行されます。 持続可能なソフトウェア エンジニアリングでは、炭素排出量をできるだけ多く削減することに焦点を当てます。 場合によっては、ネットワークの移動全体を減らすなど、持続可能なソフトウェア エンジニアリングの原則を適用することで、パフォーマンスの高速化や待機時間の短縮を実現できます。 一方で、優先度の低いワークロードを遅らせるなどして炭素排出量を削減すると、パフォーマンスの低速化や待機時間の増加を招く場合もあります。 持続可能なソフトウェア エンジニアリングの原則をアプリケーションに適用することを検討する前に、アプリケーションの優先順位、ニーズ、トレードオフを確認してください。

## <a name="measure-and-optimize"></a>測定と最適化

AKS クラスターの炭素排出量を削減するには、クラスターのリソースがどのように使用されているかを理解する必要があります。 [Azure Monitor][azure-monitor] では、メモリと CPU の使用率など、クラスターのリソースの使用状況に関する詳細が提供されます。 このデータから、クラスターの炭素排出量を削減して変更の影響を観察するという決定のための情報が得られます。 [Microsoft Sustainability Calculator][sustainability-calculator] をインストールして、すべての Azure リソースの炭素排出量を確認することもできます。

## <a name="increase-resource-utilization"></a>リソース使用率の向上

炭素排出量を削減する方法の 1 つは、コンピューティング リソースのアイドル時間を減らすことです。 アイドル時間を減らすには、コンピューティング リソースの使用率を上げる必要があります。 たとえば、クラスターに 4 つのノードがあり、それぞれを 50% の容量で実行している場合、4 つのノードすべてで 50% の未使用容量がアイドル状態のままになります。 クラスターのノードを 3 つに削減すると、ワークロードが同じであれば 3 つのノードを 67% の容量で実行することになり、各ノードの未使用容量が 33% に減り、使用率が向上します。

> [!IMPORTANT]
> クラスター内のリソースに変更を加えることを検討するときは、[システム プール][system-pools]に、クラスターのコア システム コンポーネントの安定性を維持するための十分なリソースがあることを確認してください。 決して、クラスターが不安定になる可能性がある水準にまでクラスターのリソースを削減しないでください。

クラスターの使用率を確認した後、[複数のノード プール][multiple-node-pools]によって提供される機能の使用を検討してください。 [ノードのサイズ設定][node-sizing]を使用して、特定の CPU およびメモリ プロファイルを持つノード プールを定義し、ワークロードのニーズに合わせてノードを調整することができます。 ワークロードのニーズに合わせてノードをサイズ設定すると、より高い使用率で少数のノードを実行するために役立ちます。 また、クラスターの[スケーリング][scale-auto]方法を構成し、[水平ポッド オートスケーラー][scale]と[クラスター オートスケーラー][scale-horizontal]を使用して、構成に基づきクラスターを自動的にスケーリングすることもできます。 クラスターのスケーリング方法を制御すると、クラスターのワークロードの変化に対応しながら、すべてのノードを高い使用率で実行し続けることができます。 突然の中断または終了に耐性があるワークロードの場合、[スポット プール][spot-pools]を使用して Azure 内部のアイドル容量を利用できます。 たとえば、バッチ ジョブまたは開発環境には、スポット プールがうまく機能する場合があります。

使用率を上げることで余分なノードを減らすこともでき、これによって、[各ノードのリソース予約][resource-reservations]によって消費されるエネルギーを削減できます。

また、アプリケーションの Kubernetes マニフェストで、CPU とメモリの *要求* と *制限* の見直しを行います。 メモリと CPU に関してこれらの値を下げると、より多くのメモリと CPU を、他のワークロードを実行するためにクラスターで使用できるようになります。 より少ない CPU とメモリでより多くのワークロードを実行すれば、クラスターの割り当て密度が高まり、使用率が向上します。 アプリケーション用の CPU とメモリを減らすときに、これらの値を低く設定しすぎると、アプリケーションの動作が劣化したり不安定になる可能性があります。 CPU とメモリの *要求* と *制限* を変更する前に、いくつかのベンチマーク テストを実行して、これらの値が適切に設定されているかどうかを確認することを検討してください。 また決して、アプリケーションが不安定になる水準にまでこれらの値を下げないでください。

## <a name="reduce-network-travel"></a>ネットワーク移動の削減

クラスターとの間で要求と応答が移動しなければならない距離を減らすと、通常は、ネットワーク デバイスによる消費電力と炭素排出量が削減されます。 ネットワーク トラフィックを見直した後は、ネットワーク トラフィックの発信元により近い[リージョン][regions]にクラスターを作成することを検討してください。 [Azure Traffic Manager][azure-traffic-manager] を使用すると、最も近いクラスターや[近接配置グループ][proiximity-placement-groups]にトラフィックをルーティングして Azure リソース間の距離を短縮するのに役立ちます。

> [!IMPORTANT]
> クラスターのネットワークに変更を加えることを検討するときは、ワークロードの要件を満たすことを犠牲にしてまでネットワークの移動を削減してはなりません。 たとえば、[可用性ゾーン][availability-zones]を使用するとクラスターでネットワーク移動が増えますが、ワークロード要件を満たすためにこの機能を使用することが必要な場合があります。

## <a name="demand-shaping"></a>需要形成

可能であれば、クラスターのリソースの需要を、余剰の容量を使用できる時間またはリージョンにシフトすることを検討してください。 たとえば、[スポット ジョブ][spot-pools]を実行または使用するために、バッチ ジョブの時間またはリージョンを変更することを検討してください。 また、すぐに処理する必要がないワークロードの実行を先送りするために、キューを使用するようにアプリケーションをリファクタリングすることも検討してください。

## <a name="next-steps"></a>次のステップ

この記事で言及した AKS の機能については、以下を参照してください。

* [複数のノード プール][multiple-node-pools]
* [ノードのサイズ設定][node-sizing]
* [クラスターのスケーリング][scale]
* [水平ポッド自動スケーラー][scale-horizontal]
* [クラスター オートスケーラー][scale-auto]
* [スポット プール][spot-pools]
* [システム プール][system-pools]
* [リソース予約][resource-reservations]
* [近接配置グループ][proiximity-placement-groups]
* [可用性ゾーン][availability-zones]

[availability-zones]: availability-zones.md
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md
[azure-traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[proiximity-placement-groups]: reduce-latency-ppg.md
[regions]: faq.md#which-azure-regions-currently-provide-aks
[resource-reservations]: concepts-clusters-workloads.md#resource-reservations
[scale]: concepts-scale.md
[scale-auto]: concepts-scale.md#cluster-autoscaler
[scale-horizontal]: concepts-scale.md#horizontal-pod-autoscaler
[spot-pools]: spot-node-pool.md
[multiple-node-pools]: use-multiple-node-pools.md
[node-sizing]: use-multiple-node-pools.md#specify-a-vm-size-for-a-node-pool
[sustainability-calculator]: https://azure.microsoft.com/blog/microsoft-sustainability-calculator-helps-enterprises-analyze-the-carbon-emissions-of-their-it-infrastructure/
[system-pools]: use-system-pools.md
[principles-sse]: https://docs.microsoft.com/learn/modules/sustainable-software-engineering-overview/
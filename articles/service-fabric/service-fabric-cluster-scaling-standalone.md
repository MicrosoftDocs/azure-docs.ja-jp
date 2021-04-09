---
title: Azure Service Fabric スタンドアロン クラスターのスケーリング
description: Service Fabric スタンドアロン クラスターのスケールインまたはスケールアウトと、スケールアップまたはスケールダウンについて説明します。
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 8184b2455e938fa0500308b462176e78b8dadcab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91843033"
---
# <a name="scaling-service-fabric-standalone-clusters"></a>Service Fabric スタンドアロン クラスターのスケーリング
Service Fabric クラスターは、ネットワークで接続された一連の仮想マシンまたは物理マシンで、マイクロサービスがデプロイおよび管理されます。 クラスターに属しているコンピューターまたは VM を "ノード" と呼びます。 クラスターには、場合によっては数千のノードを含めることができます。 Service Fabric クラスターの作成後は、クラスターを水平方向 (ノードの数を変更する) または垂直方向 (ノードのリソースを変更する) にスケーリングすることができます。  クラスターは、クラスターでワークロードを実行中であっても、いつでもスケーリングできます。  クラスターをスケーリングすると、アプリケーションも自動的にスケーリングされます。

クラスターをスケーリングする理由 アプリケーションの要求は、時間の経過と共に変化します。  増加したアプリケーション ワークロードやネットワーク トラフィックに対処するためにクラスター リソースを増やしたり、需要が低下したときにクラスター リソースを減らしたりする必要が生じる場合があります。

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>スケールインとスケールアウト (水平方向のスケーリング)
クラスター内のノードの数を変更します。  新しいノードがクラスターに参加すると、[Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md) はサービスをそれらのノードに移動します。これにより、既存のノードの負荷が軽減されます。  クラスターのリソースが効率的に使用されていない場合は、ノードの数を削減することもできます。  ノードがクラスターから離れるときには、それらのノードからサービスが移動し、残っているノードで負荷が増加します。  Azure で実行されているクラスター内のノードの数を減らすと、クラスター内の VM のワークロードではなく使用している VM の数に対して支払いをするため、コストを節約できます。  

- 長所:理論上は無限のスケーリング。  スケーラビリティを考慮してアプリケーションが設計されている場合は、より多くのノードを追加することで無制限の拡張が可能になります。  クラウド環境のツールによってノードの追加や削除が容易になっているため、キャパシティの調整は容易で、支払いをするのは使用するリソースに対してのみです。  
- 短所:アプリケーションが[スケーラビリティを考慮して設計されている](service-fabric-concepts-scalability.md)必要があります。  アプリケーション データベースと永続化についても、スケーリングのために追加のアーキテクチャ関連作業が必要な場合があります。  ただし Service Fabric ステートフル サービスの[信頼性の高いコレクション](service-fabric-reliable-services-reliable-collections.md)によって、アプリケーション データのスケーリングが大幅に容易になっています。

スタンドアロン クラスターでは、Service Fabric クラスターをオンプレミスで、または任意のクラウド プロバイダー内にデプロイできます。  ノードの種類は、配置に応じて、物理マシンまたは仮想マシンで構成されます。 Azure で実行されるクラスターと比較して、スタンドアロン クラスターのスケーリング プロセスはもう少し複雑です。  クラスター内のノードの数を手動で変更してから、クラスター構成のアップグレードを実行する必要があります。

ノードを削除すると、複数のアップグレードが開始されることがあります。 一部のノードには `IsSeedNode=”true”` タグが付けられており、[Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest) を使用してクラスター マニフェストをクエリすると、これらのノードを識別できます。 このようなシナリオでノードを削除する場合、シード ノードを削除する必要があるため、通常よりも長く時間がかかる可能性があります。 クラスターは、種類がプライマリ ノードであるノードを 3 つ以上維持する必要があります。

> [!WARNING]
> ノード数はクラスターの[信頼性レベルのクラスター サイズ](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster)を下回らないようにすることをお勧めします。 これにより、Service Fabric システム サービスの機能が妨げられてクラスター間でレプリケートされ、クラスターを不安定にするか、破棄する可能性があります。
>

スタンドアロン クラスターをスケーリングするときには、次のガイドラインに留意してください。
- プライマリ ノードの置き換えは、まとめて削除して後で追加するのではなく、1 ノードづつ実行する必要があります。
- ノードの種類を削除する前に、そのノードの種類を参照しているノードがないかどうかを調べてください。 このようなノードを削除してから、対応するノードの種類を削除します。 すべての対応するノードを削除したら、クラスター構成から NodeType を削除し、[Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) を使用して構成のアップグレードを開始できます。

詳細については、[スタンドアロン クラスターのスケーリング](service-fabric-cluster-windows-server-add-remove-nodes.md)に関するページを参照してください。

## <a name="scaling-up-and-down-or-vertical-scaling"></a>スケールアップとスケールダウン (垂直方向のスケーリング) 
クラスター内のノードのリソース (CPU、メモリ、またはストレージ) を変更します。
- 長所:ソフトウェアやアプリケーションのアーキテクチャは変わりません。
- 短所:スケーリングには限度があります。個々のノード上でリソースをどれだけ増加できるかに制限があるためです。 リソースを追加または削除するために物理マシンまたは仮想マシンをオフラインにする必要があるため、ダウンタイムが生じます。

## <a name="next-steps"></a>次のステップ
* [アプリケーションのスケーラビリティ](service-fabric-concepts-scalability.md)について学習します。
* [Azure クラスターをスケールインまたはスケールアウト](service-fabric-tutorial-scale-cluster.md)します。
* fluent Azure コンピューティング SDK を使用して [Azure クラスターをプログラムでスケーリングします](service-fabric-cluster-programmatic-scaling.md)。
* [スタンドアロン クラスターのスケールインまたはスケールアウトします](service-fabric-cluster-windows-server-add-remove-nodes.md)。


---
title: "Service Fabric クラスター リソース マネージャー - 管理の統合 | Microsoft Docs"
description: "クラスター リソース マネージャーと Service Fabric 管理の統合ポイントの概要"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 956cd0b8-b6e3-4436-a224-8766320e8cd7
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: b2428f93680c12c76000fa8eb1a7138f72a8efe7
ms.openlocfilehash: 9d67f089f4aba03e846a8fe020a91b6b1ac6ea48
ms.lasthandoff: 02/11/2017


---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Service Fabric クラスター管理とクラスター リソース マネージャーの統合
Service Fabric クラスター リソース マネージャーは、管理操作 (アプリケーションのアップグレードなど) を処理する Service Fabric の主要コンポーネントではありませんが、関係はあります。 クラスター リソース マネージャーが管理に役立つ第&1; の点は、クラスターとクラスター内のサービスの望ましい状態を追跡できることです。 クラスター リソース マネージャーは、クラスターを望ましい構成に設定できない場合に正常性レポートを送信します。 たとえば、容量が十分にない場合や、サービスの適切な配置に関する規則の競合がある場合などです。 統合のもう&1; つの利点は、アップグレードのしくみに関係があります。 アップグレード中、クラスター リソース マネージャーは動作を若干変更します。 以下では、統合に関するこれら&2; つの点について説明します。

## <a name="health-integration"></a>正常性の統合
クラスター リソース マネージャーは、サービスについて定義したルールのほか、ノード上およびクラスター内で使用できる容量を常に追跡します。 クラスター リソース マネージャーによってこれらのルールが満たされない場合、または十分な容量がない場合、正常性に関する警告とエラーが出力されます。 たとえば、ノードの容量が超過している場合、クラスター リソース マネージャーはサービスを移動することで状況の修正を試みます。 状況を修正できない場合、容量超過のノードとそのメトリックを示す正常性の警告を出力します。

リソース マネージャーの正常性に関する警告の例として、他には配置の制約違反があります。 たとえば、配置の制約 (`“NodeColor == Blue”` など) を定義してある場合に、リソース マネージャーによってその制約の違反が検出されると、正常性の警告が出力されます。 これには、カスタム制約と既定の制約 (障害ドメインの制約、アップグレード ドメインの制約など) が該当します。

ここでは、この正常性レポートの例を&1; つ取り上げます。 このケースの正常性レポートは、システム サービスのパーティションの&1; つに関するものです。 この正常性メッセージは、このパーティションのレプリカが一時的にまとめられているアップグレード ドメインの数が少なすぎることを示しています。

```posh
PS C:\Users\User > Get-WindowsFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_UpgradeDomain', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130766528804733380
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577821
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528854889931
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577822
                        AggregatedHealthState : Ok

                        ReplicaId             : 130837073190680024
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.PLB
                        Property              : ReplicaConstraintViolation_UpgradeDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: UpgradeDomain Details: Node -- 3d1a4a68b2592f55125328cd0f8ed477  Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

この正常性メッセージによって、次のことがわかります。

1. レプリカ自体はすべて正常である (これは Service Fabric の最優先事項です)。
2. 現在、アップグレード ドメイン分散制約に違反していること (特定のアップグレード ドメインに含まれるレプリカの数が、このパーティションに対して許容されているレプリカ数を超えています)
3. 違反が発生しているレプリカが含まれるノード (ID 3d1a4a68b2592f55125328cd0f8ed477 のノード)。
4. レポートの生成日時 (2015 年 8 月 10 日午後 7 時 13 分 02 秒)

このような情報から運用環境で発生するアラートが生成され、問題点が明らかになります。 このケースでは、リソース マネージャーがアップグレード ドメインにレプリカをまとめる必要があったのはなぜなのか、理由を探ることができます。 その理由として、他のアップグレード ドメインのノードがダウンしていたことが考えられます。

たとえば、サービスを作成する必要があるとしましょう。リソース マネージャーはサービスを配置する場所を見つけようとしていますが、うまくいっていません。 多くの理由が考えられますが、通常は、次の&2; つの状況のいずれかが原因です。

1. 一時的な状態によって、このサービス インスタンスまたはレプリカを正しく配置できない。
2. サービスの要件の構成が誤っているため、その要件を満たすことができない。

それぞれの状況で、クラスター リソース マネージャーから正常性レポートが生成されます。このレポートに記載されている情報を使用して、何が発生しているか、また、なぜサービスを配置できないのかを判断できます。 このプロセスを、"制約除外シーケンス" と呼んでいます。 このプロセスでは、サービスに影響する構成済み制約を確認して、その制約によって除外されたものを記録します。 このようにして、配置できないサービスがある場合は、どのノードがどのような理由で除外されたかを確認できます。

## <a name="constraint-types"></a>制約の種類
これらの正常性レポートに記載されている各種の制約について説明します。 ほとんどの場合、これらの制約によってノードが除外されることはありません。既定では制約がソフト レベルまたは最適化レベルに設定されているためです。 ただし、制約がハード制約として構成されている場合、または制約によってノードが除外されるレアケースでは、これらの制約に関連して正常性メッセージが発生することがあります。

* **ReplicaExclusionStatic** と **ReplicaExclusionDynamic**: これらの制約は、同じパーティションの&2; つのステートフル レプリカまたはステートレス インスタンスを (許可されていない) 同じノードに配置する必要があることを示します。 ReplicaExclusionStatic と ReplicaExclusionDynamic はほぼ同じルールです。 ReplicaExclusionDynamic 制約は、"提案のみのソリューションによってレプリカがここに既に配置されたため、このレプリカをここに配置できませんでした。" という意味です。 この点が、提案された競合ではなく実際の競合を示す ReplicaExclusionStatic 制約と異なります。 このケースでは、ノードにレプリカが既に存在します。 紛らわしいですよね。 はい。 これは重要なのでしょうか。 いいえ。 制約除外シーケンスに ReplicaExclusionStatic 制約または ReplicaExclusionDynamic 制約のいずれかが含まれている場合、クラスター リソース マネージャーによってノードが十分にないと認識されます。 他の制約では通常、ノードが少なすぎる場合の結果がわかります。
* **PlacementConstraint**: このメッセージが表示された場合、サービスの配置の制約に一致しなかったために、ノードが除外されたことを意味します。 現在構成されている配置の制約をこのメッセージの一部としてトレースします。 これは配置の制約を定義していれば正常です。 ただし、ノードが必要以上に除外される配置の制約のバグがある場合、注意する必要があります。
* **NodeCapacity**: この制約は、指定されたノードにレプリカを配置するとそのノードが容量超過になるため、クラスター リソース マネージャーがレプリカを配置できなかったことを意味します。
* **Affinity**: この制約は、影響を受けるノードにレプリカを配置すると、アフィニティ制約違反が発生するため、レプリカを配置できなかったことを示します。 アフィニティの詳細については、[こちらの記事](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)を参照してください。
* **FaultDomain** と **UpgradeDomain**: 指定されたノードにレプリカを配置することで、特定の障害ドメインまたはアップグレード ドメインにまとめられる場合は、この制約によってそのノードが除外されます。 この制約について説明するいくつかの例については、「 [fault and upgrade domain constraints and resulting behavior (障害ドメインとアップグレード ドメインの制約および行われる動作)](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: この制約は既定で最適化レベルで設定されるため、通常、これによりノードがソリューションから削除されることはありません。 さらに、優先される場所の制約は通常、アップグレード中に適用されます。 アップグレードの際に、アップグレード開始時にレプリカがあった場所にレプリカを戻すために使用されます。

### <a name="constraint-priorities"></a>制約の優先順位
これらすべての制約のうち、配置の制約がシステムにおいて最も重要であると考えることもあるでしょう。 配置の制約さえ守られている限り、他の制約 (アフィニティや容量など) に違反してもかまわないと思うかもしれません。

実際に、制約に優先順位を付けることができます。 制約はいくつかの適用レベルで構成できます。適用レベルは “ハード” （０）、“ソフト” (1)、“最適化” (2)、および “オフ” (-1) に要約されます。 制約のほとんどは、既定でハードに定義されます。 たとえば容量の場合、通常はほとんどの人が緩和してもかまわないとは考えないため、ほぼすべての制約が "ハード" または "ソフト" に定義されます。

制約によって優先順位が異なり、他の制約よりも違反の警告が頻繁に表示される制約もあることから、特定の制約については、一時的に緩和 (違反) したくなることもあります。 これらのレベルは、特定の制約が違反されることを意味しているわけではありません。単に、制約が適用される優先順位があるということです。 この優先順位があることで、クラスター リソース マネージャーは、すべての制約を満たすことができない場合に適切な選択を行えます。

高度な状況では、制約の優先順位は変更できます。 たとえば、ノードの容量の問題を解決するためにアフィニティの違反が生じるようにしたい場合は、 アフィニティの制約の優先順位を "ソフト" (1) に設定して、容量の制約の設定を "ハード" (0) のままにしておきます。

さまざまな制約に関する既定の優先順位の値は、構成で指定します。

ClusterManifest.xml

```xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PlacementConstraintPriority" Value="0" />
            <Parameter Name="CapacityConstraintPriority" Value="0" />
            <Parameter Name="AffinityConstraintPriority" Value="0" />
            <Parameter Name="FaultDomainConstraintPriority" Value="0" />
            <Parameter Name="UpgradeDomainConstraintPriority" Value="1" />
            <Parameter Name="PreferredLocationConstraintPriority" Value="2" />
        </Section>
```

スタンドアロン デプロイの ClusterConfig.json 経由または Azure でホストされたクラスターの Template.json 経由:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PlacementConstraintPriority",
          "value": "0"
      },
      {
          "name": "CapacityConstraintPriority",
          "value": "0"
      },
      {
          "name": "AffinityConstraintPriority",
          "value": "0"
      },
      {
          "name": "FaultDomainConstraintPriority",
          "value": "0"
      },
      {
          "name": "UpgradeDomainConstraintPriority",
          "value": "1"
      },
      {
          "name": "PreferredLocationConstraintPriority",
          "value": "2"
      }
    ]
  }
]
```

## <a name="fault-domain-and-upgrade-domain-constraints"></a>障害ドメインとアップグレード ドメインの制約
クラスター リソース マネージャーは、リソース マネージャー エンジン内の制約として、障害ドメインとアップグレード ドメインの間でサービスが分散されるようにモデル化します。 これらの使用方法の詳細については、[クラスター構成](service-fabric-cluster-resource-manager-cluster-description.md)に関する記事を参照してください。

場合によっては、問題が発生するのを回避するために障害ドメインとアップグレード ドメインを厳密に検討する必要がありました。 また、(少しの間ですが) これらのドメインを完全に無視することが必要な場合もありました。 全体的には、制約の優先順位のインフラストラクチャが備えている柔軟性はうまく機能しましたが、頻繁に必要になることはありません。 ほとんどの場合、すべて既定の優先順位のままです。 アップグレード ドメインはソフトな制約のままです。 クラスター リソース マネージャーは、アップグレード、障害、または制約違反に対処するために、いくつかのレプリカをアップグレード ドメインにまとめなければならない場合があります。 この状況は通常、適切な配置を妨げる障害やその他の問題がシステムに存在している場合にのみ生じます。 環境が正しく構成されている場合、(障害の制約とアップグレードの制約を含め) アップグレード中であってもすべての制約が完全に順守されます。 重要なのは、クラスター リソース マネージャーが制約を監視しており、違反が検出された場合はすぐにレポートするという点です。

## <a name="the-preferred-location-constraint"></a>優先される場所の制約
PreferredLocation 制約は少し異なり、この制約だけが “最適化” に設定されているのはそのためです。 この制約はアップグレードの実行中に、サービスをアップグレード前にあった場所に戻す場合に使用します。 これが実際には機能しない場合がある理由はさまざまですが、この最適化は有益です。

## <a name="upgrades"></a>アップグレード
クラスター リソース マネージャーは、アプリケーションとクラスターのアップグレード中にも役に立ちます。この間、クラスター リソース マネージャーは&2; つのジョブを実行します。

* クラスターのルールとパフォーマンスが損なわれないようにする
* アップグレードが円滑に進行するようサポートを試みる

### <a name="keep-enforcing-the-rules"></a>ルール適用の維持
主に注意しなければならないのは、ルール、つまり配置の制約などに関連する厳密な制御は、アップグレード中も適用されるという点です。 配置の制約があるため、ワークロードはアップグレード中も許可された場所のみで実行されます。 環境が著しく制約されている場合、アップグレードには長い時間がかかる可能性があります。 これは、更新にサービス (またはサービスが存在するノード) の停止が必要なときに、サービスを移動できる場所がほとんどないことがあるためです。

### <a name="smart-replacements"></a>スマート置換
アップグレードが始まると、リソース マネージャーは、クラスターにおける現在の配置のスナップショットを取得します。 各アップグレード ドメインの処理が完了すると、元の配置に戻そうとします。 このため、アップグレード中の切り替えは多くとも&2; 回になります (対象のノードから出し、再び戻す)。 アップグレード前の状態にクラスターを戻すと、アップグレードによるクラスターのレイアウトへの影響もなくなります。 アップグレード前にうまく配置されていたクラスターは、アップグレード後の配置も適切です。少なくとも悪くなることはありません。

### <a name="reduced-churn"></a>動きの軽減
アップグレード中、クラスター リソース マネージャーは、アップグレード中のエンティティに対する分散をオフにします。 これは、2 つの異なるアプリケーション インスタンスがあり、そのうち&1; つをアップグレードする場合、そのアプリケーション インスタンスの分散は一時停止されますが、もう&1; つについては停止されないことを意味します。 分散を一時停止すると、アップグレード自体に対する不要な反応 (たとえば、アップグレード用に空にされたノードへのサービスの移動) を回避できます。 実行するアップグレードがクラスターのアップグレードである場合、アップグレード中はクラスター全体の分散が行われません。 制約のチェック (ルールの適用) はアクティブなままで、再分散のみが無効です。

### <a name="relaxed-rules"></a>ルールの緩和
一般に、クラスターが制約を受けたり完全にいっぱいになったりするとしても、ユーザーはアップグレードを完了したいと考えます。 アップグレード中は、クラスターの容量を管理できることが通常よりもいっそう重要になります。 これは、アップグレードがクラスターで順番に行われている間、容量の 5 ～ 20% が一度に停止するのが普通だからです。 通常、この負荷は別の場所で処理される必要があります。 ここで、[バッファー容量](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity)の概念が実際に役立ちます。 バッファー容量は通常の操作では考慮されますが (いくらかのオーバーヘッドが残されますが)、アップグレード中は、クラスター リソース マネージャーによって合計容量がいっぱいになるまで (バッファーがなくなるまで) 使用されます。

## <a name="next-steps"></a>次のステップ
* 最初から開始して、 [Service Fabric クラスター リソース マネージャーの概要を確認するにはこちらを参照してください](service-fabric-cluster-resource-manager-introduction.md)


---
title: Cluster Resource Manager - 管理の統合
description: クラスター リソース マネージャーと Service Fabric 管理の統合ポイントの概要
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: ae80ac5833e90164fc4ff92010fd1830ae932cd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92174048"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Service Fabric クラスター管理とクラスター リソース マネージャーの統合
Service Fabric クラスター リソース マネージャーは Service Fabric でのアップグレードを推進していませんが、関係しています。 クラスター リソース マネージャーが管理に役立つ第 1 の点は、クラスターとクラスター内のサービスの望ましい状態を追跡できることです。 クラスター リソース マネージャーは、クラスターを望ましい構成に設定できない場合に正常性レポートを送信します。 たとえば、容量が不十分な場合、クラスター リソース マネージャーから問題を示す正常性の計画とエラーが送信されます。 統合のもう 1 つの利点は、アップグレードのしくみに関係があります。 クラスター リソース マネージャーは、アップグレード中、動作を若干変更します。  

## <a name="health-integration"></a>正常性の統合
クラスター リソース マネージャーは、サービスの配置用に定義したルールを常に追跡しています。 また、ノードとクラスターの各メトリックと、クラスター全体のメトリックについて、残りの容量も追跡しています。 クラスター リソース マネージャーによってこれらのルールが満たされない場合、または十分な容量がない場合、正常性に関する警告とエラーが出力されます。 たとえば、ノードの容量が超過している場合、クラスター リソース マネージャーはサービスを移動することで状況の修正を試みます。 状況を修正できない場合、容量超過のノードとそのメトリックを示す正常性の警告を出力します。

リソース マネージャーの正常性に関する警告の例として、他には配置の制約違反があります。 たとえば、配置の制約 (`“NodeColor == Blue”` など) を定義してある場合に、リソース マネージャーによってその制約の違反が検出されると、正常性の警告が出力されます。 これには、カスタム制約と既定の制約 (障害ドメインの制約、アップグレード ドメインの制約など) が該当します。

ここでは、この正常性レポートの例を 1 つ取り上げます。 このケースの正常性レポートは、システム サービスのパーティションの 1 つに関するものです。 この正常性メッセージは、このパーティションのレプリカが一時的にまとめられているアップグレード ドメインの数が少なすぎることを示しています。

```posh
PS C:\Users\User > Get-ServiceFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


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
                        violating the Constraint: UpgradeDomain Details: UpgradeDomain ID -- 4, Replica on NodeName -- Node.8 Currently Upgrading -- false Distribution Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

この正常性メッセージによって、次のことがわかります。

1. すべてのレプリカがそれぞれ正常に動作していることそれぞれに、`AggregatedHealthState : Ok` があります。
2. アップグレード ドメインの分散の制約に現在違反していること。 これは、特定のアップグレード ドメインのレプリカ数が、そのパーティションの限度よりも多いことを示します。
3. 違反を起こしているレプリカを含むノード。 この場合、*Node.8* という名前のノードです。
4. このパーティションでアップグレードが現在実行されているかどうか ("Currently Upgrading -- false")
5. このサービスの分散ポリシー:"Distribution Policy -- Packing"。 これは `RequireDomainDistribution` の[配置ポリシー](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing)で制御されます。 "*パッキング*" は、この場合、DomainDistribution が必須では "_なかった_" ことを示しています。このことから、このサービスに配置ポリシーが指定されていなかったことがわかります。 
6. レポートの生成日時 (2015 年 8 月 10 日午後 7 時 13 分 2 秒)

このような情報から運用環境で発生するアラートが生成され、問題点が明らかになります。また、この情報は、正しくないアップグレードを検出し、停止するためにも使用されます。 このケースでは、リソース マネージャーがアップグレード ドメインにレプリカをまとめる必要があったのはなぜなのか、理由を探ることができます。 通常、他のアップグレード ドメインのノードがダウンしていたためなどの理由で、パッキングは一時的なものです。

たとえば、クラスター リソース マネージャーが何らかのサービスを配置しようとしていて、うまくいくソリューションがないとします。 サービスを配置できない場合、通常は次のいずれかの理由があります。

1. 一時的な状態によって、このサービス インスタンスまたはレプリカを正しく配置できない。
2. サービスの配置要件を満たしていない。

このような場合は、クラスター リソース マネージャーの正常性レポートから、サービスを配置できない理由を判断できます。 このプロセスを、"制約除外シーケンス" と呼んでいます。 このプロセスでは、サービスに影響する構成済み制約を確認して、その制約によって除外されたものを記録します。 このようにして、配置できないサービスがある場合は、どのノードがどのような理由で除外されたかを確認できます。

## <a name="constraint-types"></a>制約の種類
これらの正常性レポートに記載されている各種の制約について説明します。 レプリカを配置できない場合、制約に関連する正常性メッセージがあります。

* **ReplicaExclusionStatic** と **ReplicaExclusionDynamic**:これらの制約は、同じパーティションの 2 つのサービス オブジェクトを同じノードに配置する必要があったため、ソリューションが拒否されたことを示しています。 そのノードの障害はそのパーティションに過度に影響するため、これは許可されません。 ReplicaExclusionStatic と ReplicaExclusionDynamic はほぼ同じルールです。違いはあまり問題ではありません。 制約除外シーケンスに ReplicaExclusionStatic 制約または ReplicaExclusionDynamic 制約のいずれかが含まれている場合、クラスター リソース マネージャーによってノードが十分にないと認識されます。 この場合、許可されていないこれらの無効な配置を、残りのソリューションで使用する必要があります。 このシーケンスの他の制約で、通常、最初の段階でノードが除外された理由がわかります。
* **PlacementConstraint**:このメッセージが表示された場合、サービスの配置の制約に一致しなかったために、ノードが除外されたことを意味します。 現在構成されている配置の制約をこのメッセージの一部としてトレースします。 これは配置の制約を定義していれば正常です。 ただし、配置の制約によって、誤ってノードが必要以上に除外される場合、注意する必要があります。
* **NodeCapacity**:この制約は、指定されたノードにレプリカを配置するとそのノードが容量超過になるため、クラスター リソース マネージャーがレプリカを配置できなかったことを意味します。
* **Affinity**:この制約は、影響を受けるノードにレプリカを配置すると、アフィニティ制約違反が発生するため、レプリカを配置できなかったことを示します。 アフィニティの詳細については、[こちらの記事](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)を参照してください。
* **FaultDomain** と **UpgradeDomain**:指定されたノードにレプリカを配置することで、特定の障害ドメインまたはアップグレード ドメインにまとめられる場合は、この制約によってそのノードが除外されます。 この制約について説明するいくつかの例については、「 [fault and upgrade domain constraints and resulting behavior (障害ドメインとアップグレード ドメインの制約および行われる動作)](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**:この制約は既定で最適化として実行されるため、通常、ノードがソリューションから削除されることはありません。 優先される場所の制約は、アップグレード中にも適用されます。 アップグレード中は、アップグレード開始時にサービスがあった場所にサービスを戻すために使用されます。

## <a name="blocklisting-nodes"></a>ノードをブロックリストに登録する
クラスター リソース マネージャーは、ノードがブロックリストに登録されたときにも、正常性メッセージを報告します。 ブロックリストの登録は、自動的に適用される一時的な制約と考えることができます。 そのサービスの種類のインスタンスを起動するときに、繰り返しエラーが発生する場合、ノードはブロックリストに登録されます。 ノードは、サービスの種類ごとにブロックリストに登録されます。 あるサービスの種類ではブロックリストに登録されているノードでも、別のサービスでは登録されていない可能性があります。 

開発時には、ブロックリストの作動が多く見られます。一部のバグは、サービス ホストが起動時にクラッシュする原因となります。Service Fabric によって、サービス ホストの作成が数回試行され、エラーが発生し続けます。 何度かの試行後に、ノードはブロックリストに登録され、クラスター リソース マネージャーは別のノードでサービスの作成を試行します。 複数のノードでそのエラーが発生し続けると、クラスター内のすべての有効なノードがブロックされる結果になる可能性があります。 ブロックリストの登録によって、多数のノードが削除され、目的の規模を満たすサービスを正常に起動できなくなる可能性があります。 一般的に、サービスが目的のレプリカ数またはインスタンス数を下回っていることを示す追加のエラーや警告、最初の段階でブロックリスト登録の原因となったエラーを示す正常性メッセージを、クラスター リソース マネージャーから受け取ります。

ブロックリストの登録は永続的な状態ではありません。 数分後にノードがブロックリストから削除され、Service Fabric がそのノードで再びサービスを有効にすることができるようになる可能性があります。 サービスが引き続き失敗する場合、そのサービスの種類についてそのノードはブロックリストに再登録されます。 

### <a name="constraint-priorities"></a>制約の優先順位

> [!WARNING]
> 制約の優先順位を変更することは推奨されません。クラスターに大きな悪影響が及ぶ可能性があります。 既定の制約の優先順位とその動作については、以下の情報を参照してください。 
>

これらすべての制約のうち、障害ドメインの制約がシステムで最も重要なので、 障害ドメインの制約に違反しないようにするために、他の制約に違反してもよい、と考えるかもしれません。

制約は、さまざまな優先度レベルで構成できます。 次のとおりです。

   - “hard” (0)
   - “soft” (1)
   - “optimization” (2)
   - “off” (-1) 
   
ほとんどの制約は、既定で hard 制約として構成されます。

制約の優先順位の変更は一般的ではありません。 制約の優先順位を変更する必要がある場合もあります。通常は、環境に影響していた何か他のバグや動作を回避するためです。 全体的には、制約の優先順位のインフラストラクチャが備えている柔軟性はうまく機能しましたが、頻繁に必要になることはありません。 ほとんどの場合、すべて既定の優先順位のままです。 

優先度レベルは、特定の制約に _違反する_ ことを示すものではなく、常に満たすものでもありません。 制約の優先順位で、制約を適用する順序を定義します。 すべての制約を満たすことが不可能な場合、優先順位でトレードオフを定義します。 通常、環境内で何か他のことが起こっていない場合、すべての制約を満たすことができます。 制約違反を引き起こすシナリオの例として、制約の競合や、多数の同時エラーがあります。

高度な状況では、制約の優先順位を変更できます。 たとえば、ノードの容量の問題を解決する必要がある場合に、常にアフィニティの違反が生じるようにしたい場合があります。この場合、 アフィニティの制約の優先順位を "ソフト" (1) に設定して、容量の制約の設定を "ハード" (0) のままにしておきます。

さまざまな制約に関する既定の優先順位の値は、次の構成で指定します。

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
クラスター リソース マネージャーは、障害ドメインとアップグレード ドメインにサービスを分散しようとします。 これは、クラスター リソース マネージャーのエンジン内の制約としてモデル化されます。 これらの使用方法とその動作の詳細については、[クラスター構成](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior)に関する記事を参照してください。

クラスター リソース マネージャーは、アップグレード、障害、または他の制約違反に対処するために、いくつかのレプリカをアップグレード ドメインにまとめなければならない場合があります。 障害ドメインとアップグレード ドメインへのパッキングは、通常、適切な配置を妨げる障害やその他の問題がシステムに存在している場合にのみ行われます。 このような状況でもパッキングを回避するには、`RequireDomainDistribution` の[配置ポリシー](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing)を利用します。 ただし、これを行うと、副作用としてサービスの可用性と信頼性に影響が及ぶ可能性があるため、慎重に検討してください。

環境が正しく構成されている場合、アップグレード中であってもすべての制約が完全に順守されます。 重要なのは、クラスター リソース マネージャーが制約を監視している点です。 違反が検出されると、すぐに報告し、問題の解決を試行します。

## <a name="the-preferred-location-constraint"></a>優先される場所の制約
PreferredLocation 制約は、2 つの用途があるため、少し異なります。 この制約の用途の 1 つは、アプリケーションのアップグレード中です。 クラスター リソース マネージャーは、アップグレード中に、この制約を自動的に管理します。 アップグレードの完了後に、レプリカを初期の場所に確実に戻すために使用されます。 PreferredLocation 制約のもう 1 つの用途は、[`PreferredPrimaryDomain` 配置ポリシー](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)用です。 これらの両方が最適化なので、PreferredLocation 制約だけが既定で "最適化" に設定されています。

## <a name="upgrades"></a>アップグレード
クラスター リソース マネージャーは、アプリケーションとクラスターのアップグレード中にも役に立ちます。この間、クラスター リソース マネージャーは 2 つのジョブを実行します。

* クラスターのルールが損なわれないようにする
* アップグレードが円滑に進行するようサポートを試みる

### <a name="keep-enforcing-the-rules"></a>ルール適用の維持
主に注意しなければならないのは、ルール、つまり配置の制約と容量などに関連する厳密な制御は、アップグレード中も適用されるという点です。 配置の制約があるため、ワークロードはアップグレード中も許可された場所のみで実行されます。 サービスの制約が多い場合、アップグレードに時間がかかる可能性があります。 サービスまたはノードが更新のために停止される場合、行き先にはいくつかのオプションがあります。

### <a name="smart-replacements"></a>スマート置換
アップグレードが始まると、リソース マネージャーは、クラスターにおける現在の配置のスナップショットを取得します。 各アップグレード ドメインが完了すると、アップグレード ドメインにあったサービスを元の配置に戻そうとします。 この方法では、アップグレード中に行われるサービスの移行は最大 2 個です。 影響を受けるノードから移動するものと、元の場所に移動するものです。 アップグレード前の状態にクラスターまたはサービスを戻すと、アップグレードによるクラスターのレイアウトへの影響もなくなります。 

### <a name="reduced-churn"></a>動きの軽減
アップグレード中、クラスター リソース マネージャーでは分散をオフする処理も行われます。 分散を一時停止すると、アップグレード自体に対する不要な反応 (たとえば、アップグレード用に空にされたノードへのサービスの移動) を回避できます。 実行するアップグレードがクラスターのアップグレードである場合、アップグレード中はクラスター全体の分散が行われません。 制約チェックはアクティブなままで、メトリックの事前の負荷分散に基づいた移動のみが無効になります。

### <a name="buffered-capacity--upgrade"></a>バッファーの容量とアップグレード
一般に、クラスターが制約を受けたりいっぱいに近づいたりするとしても、ユーザーはアップグレードを完了したいと考えます。 アップグレード中は、クラスターの容量を管理できることが通常よりもいっそう重要になります。 ポリシーアップグレード ドメイン数に応じて、容量の 5% から 20% は、クラスター経由でアップグレード ロールとして移行する必要があります。 この作業は、どこかの場所で実行される必要があります。 このような場合、[バッファーの容量](service-fabric-cluster-resource-manager-cluster-description.md#node-buffer-and-overbooking-capacity)の概念が役立ちます。 通常の操作では、バッファーの容量が遵守されます。 クラスター リソース マネージャーは、アップグレード中に必要に応じて合計容量までノードを使用する (バッファーを消費する) 可能性があります。

## <a name="next-steps"></a>次のステップ
* 最初から開始して、 [Service Fabric クラスター リソース マネージャーの概要を確認するにはこちらを参照してください](service-fabric-cluster-resource-manager-introduction.md)

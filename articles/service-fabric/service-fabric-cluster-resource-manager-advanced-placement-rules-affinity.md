---
title: Service Fabric クラスター リソース マネージャー - アフィニティ
description: Azure Service Fabric サービスのサービス アフィニティの概要と、サービス アフィニティ構成に関するガイダンス。
services: service-fabric
documentationcenter: .net
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c141cd96877fd140b858d0aaed9197f2de80eca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89005736"
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Service Fabric でアフィニティを構成し、使用する
アフィニティは主に、大規模なモノリシック アプリケーションをクラウドとマイクロサービスの世界に移行しやすくするために提供されるコントロールです。 これはサービスのパフォーマンスを向上させるための最適化としても使用されますが、副作用が伴う場合があります。

たとえば、大規模なアプリや、マイクロサービスでの使用を想定していないアプリを Service Fabric (または任意の分散環境) に移行するとしましょう。 この種の移行はよく行われます。 まずはアプリケーション全体を環境内にリフトし、それをパッケージ化して、円滑に実行されていることを確認します。 次に、すべてが相互に通信する小規模なサービスにアプリを分解します。

最終的に、アプリケーションで問題がいくつか発生することがあります。 問題とは、通常、次のいずれかに分類されます。

1. モノリシック アプリのコンポーネント X とコンポーネント Y の間にドキュメントに未記載の依存関係があることが判明したが、これらのコンポーネントを複数のサービスに分解したばかりです。 これらのサービスは現在、クラスター内の個々のノードで実行されているため壊れてしまいました。
2. こうしたコンポーネントは (ローカル名前付きパイプ | 共有メモリ | ディスク上のファイル) を通じて情報をやりとりし、パフォーマンス上の理由から、現時点では共有ローカル リソースへの書き込みを可能にする必要があります。 このハード依存関係は、今後解消されるでしょう。
3. すべて問題ないが、これら 2 つのコンポーネント間の通信が多いこと、またパフォーマンスに影響しやすいことが判明しました。 別々のサービスに分解すると、アプリケーション パフォーマンス全体が低下し、また待機時間が増大しました。 その結果、アプリケーション全体で要件を満たせなくなりました。

これらのケースでは、リファクタリング作業を断念することや、モノリシックに戻すことは望ましくありません。 最後の状況については、単純な最適化として望ましいことがあります。 ただし、サービスとして自然に動作するようにコンポーネントを再設計するまで (または、他の何らかの方法でパフォーマンス予測値を解決できるまで)、あたかもローカルであると感じられるようにする必要があると考えています。

どうすればよいでしょうか。 そこで、アフィニティを有効にします。

## <a name="how-to-configure-affinity"></a>アフィニティの構成方法
アフィニティを設定するには、2 つのサービスの間にアフィニティの関係を定義します。 アフィニティは一方のサービスから別のサービスを “ポイント” し、“このサービスはそのサービスの実行中にだけ実行できる“ と示すことと考えることができます。 アフィニティを親子関係と呼ぶ場合もあります (親から子をポイントする)。 アフィニティでは、サービスのレプリカまたはインスタンスが、別のサービスのものと同じノード上に配置されるようになります。

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> 子サービスが参加できるのは 1 つのアフィニティの関係だけです。 子サービスを一度に 2 つの親サービスに関連付ける必要がある場合は、次のいずれかのオプションを使用できます。
> - 関係を反転させる (parentService1 と parentService2 が現在の子サービスを指すようにします)。
> - 親の 1 つを通常のハブとして指定し、すべてのサービスが、そのサービスを指すように設定する。 
>
> クラスターにおける結果の配置のビヘイビアーは同じになります。
>

## <a name="different-affinity-options"></a>アフィニティのさまざまなオプション
アフィニティは、いくつかの相関スキームのいずれかで表され、2 種類のモードがあります。 アフィニティの最も一般的なモードは、NonAlignedAffinity と呼ばれるものです。 NonAlignedAffinity では、異なるサービスのレプリカまたはインスタンスが同じノード上に配置されます。 もう 1 つのモードは、AlignedAffinity です。 Aligned Affinity を使用できるのはステートフル サービスのみです。 2 つのステートフル サービスを整列したアフィニティが含まれるように構成すると、これらのサービスのプライマリがペアで同じノード上に配置されます。 また、これらのサービスのセカンダリの各ペアも同じノードに配置されます。 ステートフル サービスに対して NonAlignedAffinity を構成することもできます (ただし、あまり一般的ではありません)。 NonAlignedAffinity では、2 つのステートフル サービスの異なるレプリカが同じノードで実行されますが、プライマリは異なるノードになります。

<center>

![アフィニティのモードとその影響][Image1]
</center>

### <a name="best-effort-desired-state"></a>ベスト エフォートの望まれる状態
アフィニティの関係はベスト エフォートです。 同じ実行可能プロセスで実行されているときに実現する併置や信頼性と同等のものは保証されません。 アフィニティの関係内のサービスは根本的に、失敗し、個別に移動する可能性のある異なるエンティティです。 また、アフィニティの関係も、一時的ではありますが、損なわれます。 たとえば、容量制限により、アフィニティ関係の一部のサービス オブジェクトしか、特定のノードに適合しないことがあります。 このような場合、アフィニティの関係が存在する場合でも、その他の制約によりその関係を適用できません。 可能な場合、違反は後で自動的に修正されます。

### <a name="chains-vs-stars"></a>チェーンと星
現在、クラスター リソース マネージャーでは、アフィニティ関係のチェーンをモデル化することはできません。 これは、あるアフィニティの関係で子になっているサービスは、別のアフィニティの関係では親になれないことを意味します。 この種の関係をモデル化するには、実質的にはチェーンではなく、星としてモデル化する必要があります。 チェーンから星に移行するには、最下位の子を子の最初の親にします。 サービスの配置によっては、これを複数回実行する必要があります。 自然な親サービスがない場合は、プレース ホルダーとして機能するサービスを作成する必要があります。 要件によっては、[アプリケーション グループ](service-fabric-cluster-resource-manager-application-groups.md)の調査が必要になる場合があります。

<center>

![アフィニティの関係のコンテキストにおけるチェーンと星][Image2]
</center>

アフィニティの関係について注目すべきもう 1 つの点は、既定では方向があるということです。 つまり、アフィニティ ルールでは、子が親と共に配置されることのみが強制されます。 親が子と共に配置されるということではありません。 そのため、アフィニティの違反があり、何らかの理由で違反を修正する必要がある場合、子を親のノードに移動することはできません。これはたとえ親を子のノードに移動することが違反を修正することになってもです。親が子のノードに移動されることはありません。 構成 [MoveParentToFixAffinityViolation](service-fabric-cluster-fabric-settings.md) を true に設定すると、方向が削除されます。 また、各サービスにそれぞれのライフサイクルがあり、個別に失敗し移動するため、アフィニティ関係を完全なものにしたり、すぐに適用したりできないことにも注意してください。 たとえば、親がクラッシュしたため、突然別のノードにフェールオーバーされたとします。 クラスター リソース マネージャーと Failover Manager では、サービスを提供し続けること、また、整合性と可用性を維持することが優先されるため、最初にフェールオーバーが処理されます。 フェールオーバーが完了すると、アフィニティの関係は損なわれますが、子が親と共に配置されていないことが検出されるまで、クラスター リソース マネージャーでは問題が認識されません。 このようなチェックは定期的に実行されます。 クラスター リソース マネージャーが制約を評価する方法の詳細については、[こちらの記事](service-fabric-cluster-resource-manager-management-integration.md#constraint-types)を参照してください。また、[こちら](service-fabric-cluster-resource-manager-balancing.md)では、こうした制約の評価の頻度を構成する方法を詳しく説明しています。   


### <a name="partitioning-support"></a>パーティション分割のサポート
アフィニティについて注目すべき最後の点は、親がパーティション分割されている場合、アフィニティの関係がサポートされないということです。 パーティション分割サービスは今後サポートされる可能性がありますが、現時点では許可されていません。

## <a name="next-steps"></a>次のステップ
- サービスの構成の詳細については、[サービスの構成についての学習](service-fabric-cluster-resource-manager-configure-services.md)に関する記事を参照してください。
- サービスを一部のマシンに制限したり、サービスの負荷を集約したりするには、[アプリケーション グループ](service-fabric-cluster-resource-manager-application-groups.md)を使用します

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png
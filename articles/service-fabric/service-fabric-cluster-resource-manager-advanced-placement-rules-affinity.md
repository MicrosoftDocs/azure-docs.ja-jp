---
title: "Service Fabric クラスター リソース マネージャー - アフィニティ | Microsoft Docs"
description: "Service Fabric サービスのアフィニティ設定の概要"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 678073e1-d08d-46c4-a811-826e70aba6c4
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 16863217eddf0a4bbd85c52f8481c03e50dd9108
ms.lasthandoff: 01/07/2017


---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Service Fabric でアフィニティを構成し、使用する
アフィニティは主に、大規模なモノリシック アプリケーションをクラウドとマイクロサービスの世界に移行しやすくするために提供されるコントロールです。 特定のケースでは、アフィニティをサービスのパフォーマンスを向上させるための正当な最適化として使用できますが、副作用が伴う場合があります。

たとえば、大規模なアプリや、マイクロサービスでの使用を想定していないアプリを Service Fabric に移行するとしましょう。 この種の移行はよく行われます。 まずはアプリケーション全体を環境内にリフトし、それをパッケージ化して、円滑に実行されていることを確認します。 次に、すべてが相互に通信する小規模なサービスにアプリを分解します。

ここで、困ったことが起こります。 “困ったこと” は、通常、次のいずれかに分類されます。

1. モノリシック アプリのコンポーネント X とコンポーネント Y の間にドキュメントに未記載の依存関係があることが判明したが、これらのコンポーネントを複数のサービスに分解したばかりです。 これらのサービスは現在、クラスター内の個々のノードで実行されているため壊れてしまいました。
2. これらは (ローカル名前付きパイプ | 共有メモリ | ディスク上のファイル) を通じて情報をやりとりしますが、パフォーマンス上の理由から、現時点では共有リソースへの書き込みを可能にする必要があります。 このハード依存関係は今後解消されます。
3. すべて問題ないが、これら&2; つのコンポーネント間の通信が多いこと、またパフォーマンスに影響しやすいことが判明しました。 別々のサービスに分解すると、アプリケーション パフォーマンス全体が低下し、また待機時間が増大しました。 その結果、アプリケーション全体で要件を満たせなくなりました。

これらのケースでは、リファクタリング作業を断念することや、モノリシックに戻すことは望ましくありません。 ただし、サービスとして自然に動作するようにコンポーネントを再設計するまで (または、他の何らかの方法でパフォーマンス予測値を解決できるまで)、あたかもローカルであると感じられるようにする必要があると考えています。

どうすればよいでしょうか。 そこで、アフィニティを有効にします。

## <a name="how-to-configure-affinity"></a>アフィニティの構成方法
アフィニティを設定するには、2 つのサービスの間にアフィニティの関係を定義します。 アフィニティは一方のサービスから別のサービスを “ポイント” し、“このサービスはそのサービスの実行中にだけ実行できる“ と示すことと考えることができます。 アフィニティを親子関係と呼ぶ場合もあります (親から子をポイントする)。 アフィニティでは、サービスのレプリカまたはインスタンスが、別のレプリカまたはインスタンスと同じノード上に配置されるようになります。

``` csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## <a name="different-affinity-options"></a>アフィニティのさまざまなオプション
アフィニティは、いくつかの相関スキームのいずれかで表され、2 種類のモードがあります。 アフィニティの最も一般的なモードは、NonAlignedAffinity と呼ばれるものです。 NonAlignedAffinity では、異なるサービスのレプリカまたはインスタンスが同じノード上に配置されます。 もう&1; つのモードは、AlignedAffinity です。 Aligned Affinity を使用できるのはステートフル サービスのみです。 2 つのステートフル サービスを整列したアフィニティが含まれるように構成すると、これらのサービスのプライマリがペアで同じノード上に配置されます。 また、これらのサービスのセカンダリの各ペアも同じノードに配置されます。 ステートフル サービスに対して NonAlignedAffinity を構成することもできます (ただし、あまり一般的ではありません)。 NonAlignedAffinity では、2 つのステートフル サービスの異なるレプリカが同じノード上に配置されますが、プライマリまたはセカンダリの整列は試行されません。

<center>
![アフィニティのモードとその影響][Image1]
</center>

### <a name="best-effort-desired-state"></a>ベスト エフォートの望まれる状態
アフィニティとモノリシックなアーキテクチャにはいくつかの違いがあります。 これらの多くは、アフィニティの関係がベスト エフォートであることに起因します。 アフィニティの関係内のサービスは根本的に、失敗し、個別に移動する可能性のある異なるエンティティです。 また、アフィニティの関係が損なわれるのにも原因があります。 たとえば、アフィニティ関係の一部のサービス オブジェクトのみを特定のノードに配置できる場合の容量制限が挙げられます。 このような場合、アフィニティの関係が存在する場合でも、その他の制約によりその関係を適用できません。 可能な場合、違反は後で自動的に修正されます。

### <a name="chains-vs-stars"></a>チェーンと星
現在、クラスター リソース マネージャーでは、アフィニティ関係のチェーンをモデル化することはできません。 これは、あるアフィニティの関係で子になっているサービスは、別のアフィニティの関係では親になれないことを意味します。 この種の関係をモデル化するには、実質的にはチェーンではなく、星としてモデル化する必要があります。 チェーンから星に移行するには、最下位の子を子の最初の親にします。 サービスの配置によっては、これを複数回実行する必要があります。 自然な親サービスがない場合は、プレース ホルダーとして機能するサービスを作成する必要があります。 要件によっては、[アプリケーション グループ](service-fabric-cluster-resource-manager-application-groups.md)の調査が必要になる場合があります。

<center>
![アフィニティ関係のコンテキストにおけるチェーンと星][Image2]
</center>

アフィニティの関係について注目すべきもう&1; つの点は、方向があるということです。 これは、"アフィニティ" ルールでは、子が親と同じ場所にあるということのみが強制されることを意味します。親が子と共に配置されるということは強制されません。 たとえば、親が突然別のノードにフェールオーバーされたとします。 その場合、クラスター リソース マネージャーでは、子が親と共に配置されていないことが検出されるまで、問題は認識されません。 これらは別のライフ サイクルを持つ別のサービスなので、アフィニティ関係を完全なものにしたり、すぐに適用することはできません。

### <a name="partitioning-support"></a>パーティション分割のサポート
アフィニティについて注目すべき最後の点は、親がパーティション分割されている場合、アフィニティの関係がサポートされないということです。 これは、最終的にはサポートされる可能性がありますが、今のところ許可されていません。

## <a name="next-steps"></a>次のステップ
* サービスの構成に利用できるその他のオプションについて詳しくは、「[サービスの構成について学習する](service-fabric-cluster-resource-manager-configure-services.md)」にあるその他のクラスター リソース マネージャーに関するトピックをご覧ください
* サービスを少数のマシンに制限してサービスのコレクションの負荷を集計するような場合には、[アプリケーション グループ](service-fabric-cluster-resource-manager-application-groups.md)を使用します

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png


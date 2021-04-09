---
title: メトリックと配置設定を指定する
description: メトリック、配置の制約、およびその他の配置ポリシーを指定することによって Service Fabric サービスを記述する方法について説明します。
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: d4dcd319000edb204ba188ed14b4c797dba5cd38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75610099"
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Service Fabric サービスのために Cluster Resource Manager の設定を構成する
Service Fabric のクラスター リソース マネージャーを使用すると、すべての名前付きサービスを個別に制御するルールを非常にきめ細かく制御できます。 各名前付きサービスで、クラスター内の割り当て方法のルールを指定できます。 また、各名前付きサービスで、それらがサービスにとってどれだけ重要であるかを含め、レポートする一連のメトリックを定義できます。 サービスの構成は次の 3 つの異なるタスクに分かれます。

1. 配置の制約の構成
2. メトリックの構成
3. 高度な配置ポリシーおよびその他のルールの構成 (一般的ではない)

## <a name="placement-constraints"></a>配置の制約
配置の制約は、サービスが実際に実行できるクラスター内のノードの制御に使用されます。 通常は、特定の名前付きサービス インスタンス、または特定の種類のノード上で実行するように制約された指定した種類のすべてのサービスです。 配置の制約は拡張可能です。 ノードの種類ごとに任意の組み合わせのプロパティを定義し、サービスの作成時に、そのプロパティを制約に選択できます。 実行中にサービスの配置の制約を変更することもできます。 そのため、クラスターやサービスの要件の変化に対応できます。 特定のノードのプロパティをクラスターで動的に更新することもできます。 配置の制約の詳細とその構成方法については、[この記事](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)を参照してください。

## <a name="metrics"></a>メトリック
メトリックは、特定の名前付きサービスに必要な一連のリソースです。 サービスのメトリック構成には、そのサービスの各ステートフル レプリカやステートレス インスタンスが既定でどれくらいのリソースを消費するかが含まれます。 また、メトリックには、トレードオフが必要な場合のために、そのサービスに対するそのメトリックの負荷分散の重要度を示す重みも含まれます。

## <a name="advanced-placement-rules"></a>高度な配置ルール
他にも、あまり一般的ではないシナリオで便利な配置ルールがあります。 いくつかの例を次に示します。
- 地理的に分散しているクラスターで役立つ制約
- 特定のアプリケーション アーキテクチャ

その他の配置ルールは、相関関係またはポリシーによって構成されます。

## <a name="next-steps"></a>次のステップ
- メトリックは、Service Fabric クラスター リソース マネージャーが管理するクラスターの利用量と容量を表します。 メトリックの詳細とその構成方法については、[この記事](service-fabric-cluster-resource-manager-metrics.md)を参照してください。
- アフィニティは、サービスに構成できるモードの 1 つです。 一般的ではありませんが、必要な場合、 [こちら](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- さまざまな配置ルールをサービスで構成し、追加のシナリオに対処できます。 さまざまな配置ルールの詳細は [こちら](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- 最初から開始して、 [Service Fabric クラスター リソース マネージャーの概要を確認するにはこちらを参照してください](service-fabric-cluster-resource-manager-introduction.md)
- クラスター リソース マネージャーでクラスターの負荷を管理し、分散するしくみについては、 [負荷分散](service-fabric-cluster-resource-manager-balancing.md)
- Cluster Resource Manager には、クラスターを記述するためのさまざまなオプションがあります。 オプションの詳細については、[Service Fabric クラスターの記述](service-fabric-cluster-resource-manager-cluster-description.md)に関するこの記事を参照してください。

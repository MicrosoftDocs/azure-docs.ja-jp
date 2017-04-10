---
title: "Azure のマイクロサービスにおける指標および配置設定の指定 | Microsoft Docs"
description: "メトリック、配置の制約、およびその他の配置ポリシーを指定することで Service Fabric サービスを記述します。"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 16e135c1-a00a-4c6f-9302-6651a090571a
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: bb27d279396aa7b670187560cebe2ed074576bad
ms.openlocfilehash: c1df3b77f3fa621a60d6ab73f6dc2c24abbc3366


---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Service Fabric サービスのために Cluster Resource Manager の設定を構成する
Service Fabric Cluster Resource Manager を使用すると、すべての名前付きサービスを個別に制御するルールを非常にきめ細かく制御できます。 各名前付きサービス インスタンスで、クラスター内の割り当て方法のルールを指定できます。 また、各名前付きサービスで、それらがサービスにとってどれだけ重要であるかを含め、レポートする一連のメトリックを定義できます。 サービスの構成は次の&3; つの異なるタスクに分かれます。

1. 配置の制約の構成
2. メトリックの構成
3. 高度な配置ポリシーおよびその他のルールの構成 (一般的ではない)

## <a name="placement-constraints"></a>配置の制約
配置の制約は、サービスが実際に実行できるクラスター内のノードの制御に使用されます。 通常は、特定の名前付きサービス インスタンス、または特定の種類のノード上で実行するように制約された指定した種類のすべてのサービスです。 ただし、配置の制約には拡張性があります。ノードの種類ごとに任意のプロパティ セットを定義し、サービスを作成するときにプロパティに対する制約を選択します。 サービスの有効期間中に配置の制約を動的に更新することもでき、クラスターでの変更に対応できます。 特定のノードのプロパティをクラスターで動的に更新することもできます。 配置の制約の詳細とその構成方法については、[この記事](service-fabric-cluster-resource-manager-cluster-description.md#placement-constraints-and-node-properties)を参照してください。

## <a name="metrics"></a>メトリック
メトリックは指定の名前付きサービス インスタンスが必要とする一連のリソースです。 サービスのメトリック構成には、そのサービスの各ステートフル レプリカやステートレス インスタンスが既定でどれくらいのリソースを消費するかが含まれます。 また、メトリックには、トレードオフが必要な場合のために、そのサービスに対するそのメトリックの負荷分散の重要度を示す重みも含まれます。

## <a name="other-placement-rules"></a>配置に関するその他のルール
地理的に分散しているクラスターや、他のやや特殊なシナリオにおいて便利な配置ルールが他にも用意されています。 その他の配置ルールは、相関関係またはポリシーによって構成されます。

## <a name="next-steps"></a>次のステップ
* メトリックは、Service Fabric クラスター リソース マネージャーが管理するクラスターの利用量と容量を表します。 メトリックの詳細とその構成方法については、[この記事](service-fabric-cluster-resource-manager-metrics.md)を参照してください。
* アフィニティは、サービスに構成できるモードの&1; つです。 一般的ではありませんが、必要な場合、 [こちら](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* さまざまな配置ルールをサービスで構成し、追加のシナリオに対処できます。 さまざまな配置ルールの詳細は [こちら](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
* 最初から開始して、 [Service Fabric クラスター リソース マネージャーの概要を確認するにはこちらを参照してください](service-fabric-cluster-resource-manager-introduction.md)
* クラスター リソース マネージャーでクラスターの負荷を管理し、分散するしくみについては、 [負荷分散](service-fabric-cluster-resource-manager-balancing.md)
* Cluster Resource Manager には、クラスターを記述するためのさまざまなオプションがあります。 オプションについて詳しくは、[Service Fabric クラスターの記述](service-fabric-cluster-resource-manager-cluster-description.md)に関する記事をご覧ください。



<!--HONumber=Jan17_HO4-->



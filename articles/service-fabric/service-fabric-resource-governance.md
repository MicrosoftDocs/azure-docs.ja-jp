---
title: "Azure Service Fabric におけるコンテナーとサービスのリソース ガバナンス | Microsoft Docs"
description: "Azure Service Fabric では、内部または外部のコンテナーを実行するサービスのリソース制限を指定できます。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e490dfba28b8a270bf0e6022333f148c965bc6dc
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---

# <a name="resource-governance"></a>リソース ガバナンス 

同じノードまたはクラスター上の複数のサービスを実行するとき、あるサービスがリソースを余計に消費して他のサービスのリソースを枯渇させてしまう可能性があります。 この問題は、"迷惑な隣人問題" と呼ばれます。 Service Fabric では、開発者はリソースを保証するために、1 サービスあたりの予約数と制限を指定できます。また、リソースの使用量も制限できます。 

## <a name="resource-governance-metrics"></a>リソース ガバナンスのメトリック 

Service Fabric では、[Service Package](service-fabric-application-model.md) ごとのリソース ガバナンスがサポートされています。 Service Package に割り当てられたリソースは、さらにコード パッケージ間で分割できます。 リソース制限を指定すると、リソースを予約したことになります。 Service Fabric では、組み込まれた 2 つの[メトリック](service-fabric-cluster-resource-manager-metrics.md)を使用してサービス パッケージごとに CPU と Memory (メモリ) を指定する機能がサポートされています。

* CPU (メトリック名 `ServiceFabric:/_CpuCores`): コアはホスト コンピューターで使用可能な論理コアであり、すべてのノードのすべてのコアが同じ重みになります。
* Memory (メトリック名 `ServiceFabric:/_MemoryInMB`): Memory はメガバイト単位で表され、コンピューター上で使用可能な物理メモリにマップします。

ソフト予約保証のみが提供され、制限を超過した新しいサービス パッケージの使用可能なリソースを開くことをランタイムは拒絶します。 ただし、別の実行可能ファイルまたはコンテナーがノードに追加された場合、元の予約保証に違反する可能性があります。

この 2 つのメトリックに対して、[Cluster Resource Manager](service-fabric-cluster-resource-manager-cluster-description.md) は、クラスターの合計容量、クラスター内の各ノードの負荷、クラスター内の残りのリソースを追跡します。 この 2 つのメトリックは、その他のユーザー定義メトリックまたはカスタム メトリックと同等であり、既存のすべての機能を使用できます。
* クラスターはこの 2 つのメトリックに従って[分散](service-fabric-cluster-resource-manager-balancing.md)できます (既定の動作)。
* クラスターはこの 2 つのメトリックに従って[最適化](service-fabric-cluster-resource-manager-defragmentation-metrics.md)できます。
* [クラスターを記述する](service-fabric-cluster-resource-manager-cluster-description.md)ときに、この 2 つのメトリックに対してバッファーの容量を設定できます。

[動的な負荷レポート](service-fabric-cluster-resource-manager-metrics.md)は、この 2 つのメトリックについてはサポートされておらず、2 つのメトリックの負荷は作成時に定義されます。

## <a name="cluster-set-up-for-enabling-resource-governance"></a>リソース ガバナンスを有効にするためのクラスターのセットアップ

容量はクラスター内の各ノード タイプに手動で次のように定義する必要があります。

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="ServiceFabric:/_CpuCores" Value="4"/>
        <Capacity Name="ServiceFabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```
 
リソース ガバナンスは、ユーザー サービスでのみ許可され、システム サービスでは許可されません。 容量を指定するとき、一部のコアとメモリは、割り当てずにシステム サービス用に残しておく必要があります。 また、最適なパフォーマンスを得るには、クラスター マニフェストで次の設定をオンにする必要があります。 

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" /> 
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```


## <a name="specifying-resource-governance"></a>リソース ガバナンスの指定 

リソース ガバナンスの制限は、次の例で示すようにアプリケーション マニフェスト (ServiceManifestImport セクション) 内で指定します。

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>
  <Parameters>
  </Parameters>
  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric will sum the limits on code packages and uses the sum as 
  the overall ServicePackage limit.
  -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1000" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1000" />
    </Policies>
  </ServiceManifestImport>
```
  
この例では、ServicePackageA というサービス パッケージが配置されたノード上で 1 つのコアを取得しています。 このサービス パッケージには、2 つのコード パッケージ (CodeA1 と CodeA2) が含まれており、どちらも `CpuShares` パラメーターを指定しています。 CpuShares 512:256 の比率で、2 つのコード パッケージ全体のコアを分割しています。 つまり、この例では、コアの 3 分の 2 を CodeA1 が、コアの 3 分の 1 を CodeA2 が取得しています (ソフト保証予約は同じです)。 コード パッケージに CpuShares が指定されていない場合、Service Fabric は均等にコアを分割します。

Memory の制限は絶対的であるため、コード パッケージのメモリは両方とも 1024 MB に制限されます (ソフト保証予約は同じです)。 コード パッケージ (コンテナーまたはプロセス) は、この制限を超えてメモリを割り当てることはできず、割り当てようとするとメモリ不足の例外が発生します。 リソース制限の強制を機能させるには、サービス パッケージ内のすべてのコード パッケージでメモリ制限を指定する必要があります。


## <a name="next-steps"></a>次のステップ
* Cluster Resource Manager の詳細については、こちらの[記事](service-fabric-cluster-resource-manager-introduction.md)をご覧ください。
* アプリケーション モデル、サービス パッケージ、コード パッケージ、およびこれらにレプリカをマップする方法の詳細については、こちらの[記事](service-fabric-application-model.md)をご覧ください。


---
title: Azure Service Fabric におけるコンテナーとサービスのリソース ガバナンス | Microsoft Docs
description: Azure Service Fabric では、内部または外部のコンテナーを実行するサービスのリソース制限を指定できます。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: aljo, subramar
ms.openlocfilehash: e011554e61411fddca034f024c30c2270593e07b
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669250"
---
# <a name="resource-governance"></a>リソース ガバナンス

同じノードまたはクラスター上の複数のサービスを実行するとき、あるサービスがリソースを余計に消費して、プロセスの他のサービスのリソースを枯渇させてしまうことがあります。 この問題は、"迷惑な隣人問題" と呼ばれます。 Service Fabric では、開発者はリソースを保証しリソースの使用量を制限するために、1 サービスあたりの予約数と制限を指定できます。

> この記事を読み進める前に、[Service Fabric のアプリケーション モデル](service-fabric-application-model.md)と [Service Fabric のホスティング モデル](service-fabric-hosting-model.md)を十分に理解しておくことをお勧めします。
>

## <a name="resource-governance-metrics"></a>リソース ガバナンスのメトリック

Service Fabric では、[サービス パッケージ](service-fabric-application-model.md)に応じてリソース ガバナンスがサポートされます。 サービス パッケージに割り当てられたリソースは、さらにコード パッケージ間で分割できます。 リソース制限を指定すると、リソースを予約したことになります。 Service Fabric では、組み込まれた 2 つの[メトリック](service-fabric-cluster-resource-manager-metrics.md)を使用してサービス パッケージごとに CPU とメモリを指定する機能がサポートされています。

* *CPU* (メトリック名 `servicefabric:/_CpuCores`):ホスト コンピューターで利用可能な論理コアです。 すべてのノードのすべてのコアが同じ重みになります。

* *Memory* (メトリック名 `servicefabric:/_MemoryInMB`):Memory はメガバイト単位で表され、コンピューター上で使用可能な物理メモリにマップされます。

この 2 つのメトリックに対して、[クラスター リソース マネージャー](service-fabric-cluster-resource-manager-cluster-description.md)は、クラスターの合計容量、クラスター内の各ノードの負荷、クラスター内の残りのリソースを追跡します。 この 2 つのメトリックは、その他のユーザー定義メトリックまたはカスタム メトリックと同等です。 既存のすべての機能をそれらに使用できます。

* クラスターはこの 2 つのメトリックに従って[分散](service-fabric-cluster-resource-manager-balancing.md)できます (既定の動作)。
* クラスターはこの 2 つのメトリックに従って[最適化](service-fabric-cluster-resource-manager-defragmentation-metrics.md)できます。
* [クラスターを記述する](service-fabric-cluster-resource-manager-cluster-description.md)ときに、この 2 つのメトリックに対してバッファーの容量を設定できます。

[動的な負荷レポート](service-fabric-cluster-resource-manager-metrics.md)は、この 2 つのメトリックについてはサポートされておらず、2 つのメトリックの負荷は作成時に定義されます。

## <a name="resource-governance-mechanism"></a>リソース ガバナンスのメカニズム

現時点では、Service Fabric ランタイムは、リソースの予約を提供していません。 プロセスまたはコンテナーが開かれると、ランタイムは、リソース制限を作成時に定義された負荷に設定します。 さらに、ランタイムは、リソースを超過したときに使用可能な新しいサービス パッケージを開くことを拒否します。 プロセスのしくみを理解するために、2 つの CPU コアを持つノードの例を見てみましょう (メモリ ガバナンスのメカニズムに相当します)。

1. 最初に、コンテナーがノードに配置され、1 つの CPU コアが要求されます。 ランタイムは、コンテナーを開き、CPU 制限を 1 コアに設定します。 コンテナーは 1 つを超えるコアを使用することはできません。

2. 次に、サービスのレプリカがノードに配置され、該当するサービス パッケージが 1 つの CPU コアという制限を指定します。 ランタイムはコード パッケージを開き、その CPU 制限を 1 コアに設定します。

この時点では、制限の合計はノードの容量と同じです。 プロセスとコンテナーはそれぞれ 1 コアで実行され、互いに干渉していません。 コンテナーまたはレプリカが CPU 制限を指定している場合、Service Fabric は、これ以上コンテナーもレプリカも配置しません。

ただし、他のプロセスが CPU を取り合う可能性がある 2 つの状況があります。 そのような状況では、例に示したプロセスとコンテナーが "迷惑な隣人問題" を経験する可能性があります。

* *管理サービスおよびコンテナーと非管理サービスおよびコンテナーの混合*:ユーザーがリソース ガバナンスを指定せずにサービスを作成した場合、ランタイムは、そのサービスがリソースをまったく消費しないと見なして、例で示したノードに配置することができます。 この場合、この新しいプロセスは、実際には、ノードで既に実行されているサービスを犠牲にして CPU を消費します。 この問題には 2 つの解決策があります。 同じクラスターに管理サービスと非管理サービスを混在させないようにするか、[配置制約](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)を使用してこれらの 2 種類のサービスが最終的に同じノード セットに配置されないようにすることです。

* *ノード上で別のプロセス (たとえば OS サービス) が Service Fabric の外部で開始された場合*:この状況では、Service Fabric の外部のプロセスも、既存のサービスと CPU を取り合います。 この問題の解決策は、次のセクションに示すように、OS のオーバーヘッドに対応する適切なノード容量を設定することです。

## <a name="cluster-setup-for-enabling-resource-governance"></a>リソース ガバナンスを有効にするためのクラスターのセットアップ

ノードが起動し、クラスターに参加すると、Service Fabric は使用可能なメモリの量と使用可能なコアの数を検出し、これら 2 つのリソースに対するノード容量を設定します。

オペレーティング システムとノードで実行される可能性がある他のプロセス用のバッファー領域を残すために、Service Fabric は、ノードで使用可能なリソースの 80% のみを使用します。 この割合は構成可能であり、クラスター マニフェストで変更できます。

使用可能な CPU の 50% と使用可能なメモリの 70% を使用するように Service Fabric に指示する方法の例を次に示します。

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

ノード容量を完全に手動で設定する必要がある場合は、クラスター内のノードを記述するための通常のメカニズムを使用できます。 4 つのコアと 2 GB のメモリを持つノードを設定する方法の例を次に示します。

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

使用可能なリソースの自動検出が有効であり、ノード容量がクラスター マニフェストに手動で定義されている場合、Service Fabric は、ユーザーが定義している容量をサポートするための十分なリソースがノードに存在するかどうかをチェックします。

* マニフェストに定義されているノード容量がノードで使用可能なリソース以下である場合、Service Fabric は、マニフェストに指定されている容量を使用します。

* マニフェストに定義されているノード容量が使用可能なリソースよりも大きい場合、Service Fabric は、使用可能なリソースをノード容量として使用します。

使用可能なリソースの自動検出は、不要な場合にはオフにできます。 オフにするには、次の設定を変更します。

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

また、最適なパフォーマンスを得るには、クラスター マニフェストで次の設定をオンにする必要があります。

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

## <a name="specify-resource-governance"></a>リソース ガバナンスの指定

リソース ガバナンスの制限は、次の例で示すようにアプリケーション マニフェスト (ServiceManifestImport セクション) 内で指定します。

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric sums the limits on code packages and uses the sum as 
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

この例では、**ServicePackageA** というサービス パッケージが配置されたノード上で 1 つのコアを取得しています。 このサービス パッケージには、2 つのコード パッケージ (**CodeA1** と **CodeA2**) が含まれており、どちらも `CpuShares` パラメーターを指定しています。 CpuShares 512:256 の比率で、2 つのコード パッケージ全体のコアを分割しています。

つまり、この例では、コアの 3 分の 2 を CodeA1 が、コアの 3 分の 1 を CodeA2 が取得しています (ソフト保証予約は同じです)。 コード パッケージに CpuShares が指定されていない場合、Service Fabric は均等にコアを分割します。

Memory の制限は絶対的であるため、コード パッケージのメモリは両方とも 1024 MB に制限されます (ソフト保証予約は同じです)。 コード パッケージ (コンテナーまたはプロセス) は、この制限を超えてメモリを割り当てることはできず、割り当てようとするとメモリ不足の例外が発生します。 リソース制限の強制を機能させるには、サービス パッケージ内のすべてのコード パッケージでメモリ制限を指定する必要があります。

### <a name="using-application-parameters"></a>アプリケーション パラメーターの使用

リソース ガバナンスを指定するときは、[アプリケーション パラメーター](service-fabric-manage-multiple-environment-app-configuration.md)を使って複数のアプリ構成を管理できます。 次の例では、アプリケーション パラメーターの使い方を示します。

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="4" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="2048" />
    <Parameter Name="MemoryB" DefaultValue="2048" />
  </Parameters>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="[CpuSharesA]" MemoryInMB="[MemoryA]" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="[CpuSharesB]" MemoryInMB="[MemoryB]" />
    </Policies>
  </ServiceManifestImport>
```

この例では、既定のパラメーター値が運用環境に対して設定され、各サービス パッケージは 4 個のコアと 2 GB のメモリになります。 アプリケーション パラメーター ファイルで既定値を変更することができます。 この例では、1 つのパラメーター ファイルをアプリケーションのローカルなテストに使うことができ、運用環境より少ないリソースが設定されます。

```xml
<!-- ApplicationParameters\Local.xml -->

<Application Name="fabric:/TestApplication1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="1024" />
    <Parameter Name="MemoryB" DefaultValue="1024" />
  </Parameters>
</Application>
```

> [!IMPORTANT]
> アプリケーション パラメーターによるリソース ガバナンスの指定は、Service Fabric バージョン 6.1 以降で利用できます。<br>
>
> アプリケーション パラメーターを使ってリソース ガバナンスを指定すると、Service Fabric をバージョン 6.1 より前のバージョンにダウングレードできなくなります。

## <a name="other-resources-for-containers"></a>コンテナー用の他のリソース

CPU とメモリに加え、コンテナー用の他のリソース制限を指定できます。 これらの制限は、コード パッケージ レベルで指定され、コンテナーが開始されたときに適用されます。 CPU とメモリとは異なり、クラスター リソース マネージャーはこれらのリソースを認識せず、それらの容量チェックも負荷分散も実行しません。

* *MemorySwapInMB*:コンテナーが使用できるスワップ メモリの量。
* *MemoryReservationInMB*:ノードでメモリの競合が検出された場合にのみ適用される、メモリ ガバナンスのソフト制限。
* *CpuPercent*:コンテナーが使用できる CPU の割合。 サービス パッケージで CPU 制限が指定された場合、このパラメーターは事実上無視されます。
* *MaximumIOps*:コンテナーが使用できる最大 IOPS (読み取りと書き込み)。
* *MaximumIOBytesps*:コンテナーが使用できる最大 IO (1 秒あたりのバイト数) (読み取りと書き込み)。
* *BlockIOWeight*:その他のコンテナーに対する相対的なブロック IO の重み。

これらのリソースは、CPU とメモリと組み合わせることができます。 コンテナー用の追加リソースを指定する方法の例を次に示します。

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>次の手順

* クラスター リソース マネージャーの詳細については、「[Service Fabric クラスター リソース マネージャーの概要](service-fabric-cluster-resource-manager-introduction.md)」をご覧ください。
* アプリケーション モデル、サービス パッケージ、コード パッケージ、およびこれらにレプリカをマップする方法の詳細については、「[Service Fabric でのアプリケーションのモデル化](service-fabric-application-model.md)」をご覧ください。

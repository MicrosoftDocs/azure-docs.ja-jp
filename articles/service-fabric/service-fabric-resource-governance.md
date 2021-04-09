---
title: コンテナーとサービスのリソース ガバナンス
description: Azure Service Fabric を使用すると、プロセスまたはコンテナーとして実行されているサービスのリソース要求と制限を指定できます。
ms.topic: conceptual
ms.date: 8/9/2017
ms.openlocfilehash: d760766870c8c2be0a2d2384f6d012b75bc92fbd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735660"
---
# <a name="resource-governance"></a>リソース管理

同じノードまたはクラスターで複数のサービスを実行している場合、1 つのサービスがより多くのリソースを消費し、プロセス内の他のサービスのリソースを枯渇させてしまうことがあります。 この問題は、"迷惑な隣人問題" と呼ばれます。 開発者は、Azure Service Fabric を使用して、リソースの使用量を制限するためにサービスごとに要求と制限を指定することで、この動作を制御できます。

> この記事を読み進める前に、[Service Fabric のアプリケーション モデル][application-model-link]と [Service Fabric のホスティング モデル][hosting-model-link]を十分に理解しておくことをお勧めします。
>

## <a name="resource-governance-metrics"></a>リソース ガバナンスのメトリック

Service Fabric では、[サービス パッケージ][application-model-link]に応じてリソース ガバナンスがサポートされます。 サービス パッケージに割り当てられたリソースは、さらにコード パッケージ間で分割できます。 Service Fabric では、次の 2 つの組み込み[メトリック](service-fabric-cluster-resource-manager-metrics.md)を使用した、サービス パッケージごとの CPU とメモリのガバナンスがサポートされています。

* *CPU* (メトリック名 `servicefabric:/_CpuCores`):ホスト マシンで利用可能な論理コアです。 すべてのノードのすべてのコアが同じ重みになります。

* *Memory* (メトリック名 `servicefabric:/_MemoryInMB`):Memory はメガバイト単位で表され、コンピューター上で使用可能な物理メモリにマップされます。

この 2 つのメトリックに対して、[Cluster Resource Manager (CRM)][cluster-resource-manager-description-link] によって、クラスターの合計容量、クラスター内の各ノードの負荷、クラスター内の残りのリソースが追跡されます。 この 2 つのメトリックは、その他のユーザー定義メトリックまたはカスタム メトリックと同等です。 既存のすべての機能をそれらに使用できます。

* クラスターはこの 2 つのメトリックに従って[分散](service-fabric-cluster-resource-manager-balancing.md)できます (既定の動作)。
* クラスターはこの 2 つのメトリックに従って[最適化](service-fabric-cluster-resource-manager-defragmentation-metrics.md)できます。
* [クラスターを記述する][cluster-resource-manager-description-link]ときに、この 2 つのメトリックに対してバッファーの容量を設定できます。

> [!NOTE]
> [動的読み込みレポート](service-fabric-cluster-resource-manager-metrics.md)は、これらのメトリックについてはサポートされておらず、これらのメトリックの読み込みは作成時に定義されます。

## <a name="resource-governance-mechanism"></a>リソース ガバナンスのメカニズム

Service Fabric ランタイムのバージョン 7.2 以降では、CPU およびメモリ リソースの要求と制限の指定がサポートされています。

> [!NOTE]
> Service Fabric ランタイムの 7.2 より以前のバージョンでは、特定のリソース (CPU またはメモリ) に対して、単一の値を **要求** と **制限** の両方として機能させるモデルのみがサポートされています。 これは、このドキュメントで **RequestsOnly** の指定として記載されているものです。

* "*要求:* "CPU およびメモリの要求値は、[Cluster Resource Manager (CRM)][cluster-resource-manager-description-link] によって `servicefabric:/_CpuCores` および `servicefabric:/_MemoryInMB` メトリックに対して使用される負荷に相当します。 言い換えると、CRM ではサービスのリソース使用量を要求値と同じであると見なされ、配置の決定時にこれらの値が使用されます。

* "*制限:* "CPU およびメモリの制限値は、プロセスまたはコンテナーがノード上でアクティブ化されるときに適用される実際のリソース制限に相当します。

Service Fabric では、CPU とメモリに対して **RequestsOnly、LimitsOnly**、そして **RequestsAndLimits** の両方を指定することが可能です。
* RequestsOnly の指定を使用した場合、サービス ファブリックでは要求値が制限としても使用されます。
* LimitsOnly の指定を使用した場合、サービス ファブリックでは要求値が 0 と見なされます。
* RequestsAndLimits の指定を使用する場合、制限値は要求値以上である必要があります。

リソース ガバナンスのメカニズムについての理解を深めるために、CPU リソースに対して **RequestsOnly** 指定を使用した場合の配置シナリオの例を見てみましょう (メモリ ガバナンスのメカニズムも同様です)。 2 つの CPU コアを持ち、そこに 2 つのサービス パッケージが配置されるノードがあるとします。 配置される最初のサービス パッケージは、1 つのコンテナー コード パッケージのみで構成され、CPU コア要求のみを 1 に指定します。 配置される 2 番目のサービス パッケージは、1 つのプロセス ベースのコード パッケージのみで構成され、CPU コア要求のみを 1 に指定します。 どちらのサービス パッケージでも RequestsOnly の指定が使用されているため、それらの制限値は要求値に設定されます。

1. 最初に、CPU コア要求が 1 のコンテナー ベースのサービス パッケージがノード上に配置されます。 ランタイムによってコンテナーがアクティブ化され、CPU 制限が 1 コアに設定されます。 コンテナーは 1 つを超えるコアを使用することはできません。

2. 次に、CPU コア要求が 1 のプロセス ベースのサービス パッケージがノード上に配置されます。 ランタイムによってサービス プロセスがアクティブ化され、CPU 制限が 1 コアに設定されます。

この時点で、要求の合計はノードの容量と同じになります。 CRM によって、CPU 要求のあるコンテナーまたはサービス プロセスがこれ以上このノードに配置されることはありません。 ノード上では、プロセスとコンテナーはそれぞれ 1 コアを使用して実行され、CPU に関して互いに競合が発生することはありません。

それでは、**RequestsAndLimits** の指定を使用する場合の例をもう一度見てみましょう。 今回は、コンテナー ベースのサービス パッケージによって、CPU コア要求が 1、CPU コア制限が 2 に指定されます。 またプロセス ベースのサービス パッケージによって、CPU コア要求と CPU コア制限の両方が 1 に指定されます。
  1. 最初に、コンテナー ベースのサービス パッケージがノード上に配置されます。 ランタイムによってコンテナーがアクティブ化され、CPU 制限が 2 コアに設定されます。 このコンテナーでは、2 つを超えるコアを使用することはできません。
  2. 次に、プロセス ベースのサービス パッケージがノード上に配置されます。 ランタイムによってサービス プロセスがアクティブ化され、CPU 制限が 1 コアに設定されます。

  この時点で、ノード上に配置されているサービス パッケージの CPU 要求の合計は、ノードの CPU 容量と同じになります。 CRM によって、CPU 要求のあるコンテナーまたはサービス プロセスがこれ以上このノードに配置されることはありません。 ただし、ノード上での制限の合計は、(コンテナーの 2 コア + プロセスの 1 コアとなり) 容量である 2 コアを超えています。 コンテナーとプロセスが同時にバーストした場合、CPU リソースの競合が発生する可能性があります。 このような競合は、プラットフォームの基盤となる OS によって管理されます。 この例では、コンテナーによって最大で 2 CPU コアがバーストされる可能性があり、その結果、プロセスの 1 CPU コアの要求が保証されなくなります。

> [!NOTE]
> 前述の例で示したように、CPU とメモリの要求値によって、**ノード上でリソースが予約されるわけではありません**。 これらの値は、配置が決定する際に Cluster Resource Manager によって考慮されるリソース消費量に相当します。 制限値は、プロセスまたはコンテナーがノード上でアクティブ化されるときに適用される、実際のリソース制限に相当します。


CPU の競合が発生する可能性のある状況がいくつかあります。 そのような状況では、例に示したプロセスとコンテナーで "迷惑な隣人" 問題が発生する可能性があります。

* *管理サービスおよびコンテナーと非管理サービスおよびコンテナーの混合*:ユーザーがリソース ガバナンスを指定せずにサービスを作成した場合、ランタイムは、そのサービスがリソースをまったく消費しないと見なして、例で示したノードに配置することができます。 この場合、この新しいプロセスは、実際には、ノードで既に実行されているサービスを犠牲にして CPU を消費します。 この問題には 2 つの解決策があります。 同じクラスターに管理サービスと非管理サービスを混在させないようにするか、[配置制約](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)を使用してこれらの 2 種類のサービスが最終的に同じノード セットに配置されないようにすることです。

* *ノード上で別のプロセス (たとえば OS サービス) が Service Fabric の外部で開始された場合*:この状況では、Service Fabric の外部のプロセスも、既存のサービスと CPU を取り合います。 この問題の解決策は、次のセクションに示すように、OS のオーバーヘッドに対応する適切なノード容量を設定することです。

* *要求と制限が等しくない場合*:前述の RequestsAndLimits の例で説明したように、要求はノード上でのリソースの予約にはなりません。 要求よりも大きい制限を持つサービスがノードに配置されると、そのサービスによってリソースが (利用可能な場合に) 最大で制限まで消費される可能性があります。 このような場合は、同じノード上の他のサービスで、要求値までのリソースが使用できない可能性があります。

## <a name="cluster-setup-for-enabling-resource-governance"></a>リソース ガバナンスを有効にするためのクラスターのセットアップ

ノードが起動し、クラスターに参加すると、Service Fabric は使用可能なメモリの量と使用可能なコアの数を検出し、これら 2 つのリソースに対するノード容量を設定します。

オペレーティング システムとノード上で実行される可能性がある他のプロセス用のバッファー領域を残すために、Service Fabric によって使用されるのは、ノードで使用可能なリソースの 80% のみになります。 この割合は構成可能であり、クラスター マニフェストで変更できます。

使用可能な CPU の 50% と使用可能なメモリの 70% を使用するように Service Fabric に指示する方法の例を次に示します。

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

ほとんどの顧客とシナリオに対しては、CPU とメモリのノード容量の自動検出が推奨される構成です (自動検出は既定でオンになっています)。 ただし、ノード容量を完全に手動で設定する必要がある場合は、クラスター内のノードを記述するメカニズムを使用して、ノードの種類ごとに構成することができます。 4 つのコアと 2 GB のメモリを持つノードの種類を設定する方法の例を次に示します。

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
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
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

> [!IMPORTANT]
> Service Fabric バージョン 7.0 以降では、ノード リソース容量の値をユーザーが手動で指定している場合に、ノード リソース容量の計算方法に関する規則が改訂されました。 次のシナリオを検討してみましょう。
>
> * ノードに合計 10 個の CPU コアがあります
> * SF は、ユーザー サービスに対して総リソースの 80% を使用するように構成されています (既定の設定)。ノード上で実行される他のサービス (Service Fabric システム サービスを含む) 用に 20% のバッファーが残されています。
> * ユーザーは、CPU コア メトリックのノード リソース容量を手動でオーバーライドし、5 コアに設定します。
>
> Service Fabric ユーザー サービスに使用できる容量の計算方法に関する規則は、次のように改訂されています。
>
> * Service Fabric 7.0 より前であれば、ユーザー サービスに使用できる容量を計算すると、**5 コア** になります (容量バッファーの 20% は無視されます)
> * Service Fabric 7.0 以降では、ユーザー サービスに利用できる容量を計算すると、**4 コア** になります (容量バッファー 20% は無視されません)

## <a name="specify-resource-governance"></a>リソース ガバナンスの指定

リソース ガバナンスの要求と制限は、アプリケーション マニフェスト (ServiceManifestImport セクション) 内で指定します。 いくつかの例を見ていきましょう。

**例 1: RequestsOnly の指定**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1024" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1024" />
    </Policies>
  </ServiceManifestImport>
```

この例では、`CpuCores` 属性を使用して、**ServicePackageA** に対して 1 CPU コアの要求を指定しています。 CPU 制限 (`CpuCoresLimit` 属性) が指定されていないため、Service Fabric では、指定された要求値である 1 コアがサービス パッケージの CPU 制限としても使用されます。

**ServicePackageA** が配置されるノードは、**そのノードに配置されているすべてのサービス パッケージの CPU 要求の合計** を差し引いた後に、CPU 容量が 1 コア以上残っているノードだけです。 ノード上では、このサービス パッケージは 1 コアに制限されます。 このサービス パッケージには 2 つのコード パッケージ (**CodeA1** と **CodeA2**) が含まれており、両方とも `CpuShares` 属性が指定されています。 CpuShares 512:256 の比率を使用して、個々のコード パッケージの CPU 制限が計算されます。 したがって、CodeA1 は 1 コアの 3 分の 2 に制限され、CodeA2 は 1 コアの 3 分の 1 に制限されます。 CpuShares がすべてのコード パッケージに対して指定されているわけではない場合、Service Fabric によって CPU の制限が均等に分割されます。

コード パッケージに対して指定された CpuShares は、サービス パッケージ全体の CPU 制限に対する相対的な割合を表しますが、コード パッケージのメモリ値は絶対的な条件で指定されます。 この例では、`MemoryInMB` 属性を使用して、CodeA1 と CodeA2 の両方に 1024 MB のメモリ要求を指定しています。 メモリ制限 (`MemoryInMBLimit` 属性) が指定されていないため、Service Fabric では、指定された要求値がコード パッケージの制限としても使用されます。 サービス パッケージのメモリ要求 (および制限) は、その構成要素であるコード パッケージのメモリ要求 (および制限) 値の合計として計算されます。 したがって、**ServicePackageA** では、メモリの要求と制限は 2048 MB として計算されます。

**ServicePackageA** が配置されるノードは、**そのノードに配置されているすべてのサービス パッケージのメモリ要求の合計** を差し引いた後に、メモリ容量が 2048 MB 以上残っているノードだけです。 ノード上では、両方のコード パッケージがそれぞれ 1024 MB のメモリに制限されます。 コード パッケージ (コンテナーまたはプロセス) では、この制限よりも多くのメモリを割り当てることはできず、そうしようとするとメモリ不足の例外が発生します。

**例 2: LimitsOnly の指定**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCoresLimit="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMBLimit="1024" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMBLimit="1024" />
    </Policies>
  </ServiceManifestImport>
```
この例では `CpuCoresLimit` と `MemoryInMBLimit` 属性を使用しています。これは、SF バージョン 7.2 以降でのみ使用できます。 CpuCoresLimit 属性を使用して、**ServicePackageA** に対して 1 コアの CPU 制限を指定しています。 CPU 要求 (`CpuCores` 属性) は指定されていないため、0 と見なされます。 `MemoryInMBLimit` 属性を使用して、CodeA1 および CodeA2 に対して 1024 MB のメモリ制限を指定しています。また、要求 (`MemoryInMB` 属性) は指定されていないため、0 と見なされます。 したがって、**ServicePackageA** のメモリ要求と制限は、それぞれ 0 および 2048 として計算されます。 **ServicePackageA** に対する CPU とメモリの要求は両方とも 0 であるため、`servicefabric:/_CpuCores` と `servicefabric:/_MemoryInMB` のメトリックに関して、CRM が配置のために考慮すべき負荷は提示されません。 そのため、リソース ガバナンスの観点からは、**ServicePackageA** は **残りの容量に関係なく**、任意のノードに配置できます。 例 1 と同様に、ノード上では、CodeA1 は 1 コアの 3 分の 2 と 1024 MB のメモリに制限され、CodeA2 は 1 コアの 3 分の 1 と 1024 MB のメモリに制限されます。

**例 3: RequestsAndLimits の指定**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1" CpuCoresLimit="2"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1024" MemoryInMBLimit="3072" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="2048" MemoryInMBLimit="4096" />
    </Policies>
  </ServiceManifestImport>
```
この例では、最初の 2 つの例を踏まえて、CPU とメモリに対して要求と制限の両方を指定する方法を示しています。 **ServicePackageA** では、CPU 要求とメモリ要求がそれぞれ 1 コアと 3072 (1024 + 2048) MB です。 これが配置できるノードは、ノードの合計の CPU (およびメモリ) 容量から、そのノードに配置されたすべてのサービス パッケージのすべての CPU (およびメモリ) 要求の合計を差し引いた後に、少なくとも 1 コア (および 3072 MB) の容量が残っているノードだけです。 ノード上では、CodeA1 は 2 コアの 3 分の 2 および 3072 MB のメモリに制限され、CodeA2 は 2 コアの 3 分の 1 と 4096 MB のメモリに制限されます。

### <a name="using-application-parameters"></a>アプリケーション パラメーターの使用

リソース ガバナンスの設定を指定する場合は、[アプリケーション パラメーター](service-fabric-manage-multiple-environment-app-configuration.md)を使って複数のアプリ構成を管理できます。 次の例では、アプリケーション パラメーターの使い方を示します。

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

## <a name="enforcing-the-resource-limits-for-user-services"></a>ユーザー サービスへのリソース制限の適用

Service Fabric サービスにリソース ガバナンスを適用すると、リソース管理の対象となるサービスではリソース クォータを超過しないことが保証される一方で、多くのユーザーは依然として、一部の Service Fabric サービスを非管理モードで実行する必要があります。 非管理の Service Fabric サービスを使用する際に、管理されずに "暴走した" サービスによって、Service Fabric ノード上で使用できるすべてのリソースが使い尽くされる状況に陥る可能性があります。

* ノード上で実行されている他のサービス (Service Fabric システムサービスを含む) でのリソースの不足
* 異常な状態でノードが終了する
* Service Fabric クラスター管理 API が応答しない

このような状況が発生しないように、Service Fabric では、指定された量を超えるリソースがユーザー サービスによって使用されないことを保証するために、(管理および非管理のどちらでも) "*ノード上で実行されるすべての Service Fabric ユーザー サービスにリソース制限を適用*" できます。 ClusterManifest の PlacementAndLoadBalancing セクションにある EnforceUserServiceMetricCapacities config の値を true に設定することで、これを実現できます。 この設定は、既定では無効になっています。

```xml
<SectionName="PlacementAndLoadBalancing">
    <ParameterName="EnforceUserServiceMetricCapacities" Value="false"/>
</Section>
```

追加の注意事項:

* リソース制限の適用は、`servicefabric:/_CpuCores` および `servicefabric:/_MemoryInMB` リソース メトリックのみに該当します
* リソース制限の適用は、(「[リソース ガバナンスを有効にするためのクラスターのセットアップ](service-fabric-resource-governance.md#cluster-setup-for-enabling-resource-governance)」セクションの説明にあるように、) 自動検出メカニズムを介して、あるいは、ユーザーが手動でノード容量を指定することで、リソース メトリックのノード容量が Service Fabric で利用可能な場合にのみ有効です。 ノード容量が構成されていない場合、Service Fabric ではユーザー サービス用に予約すべきリソース量を把握できないため、リソース制限の適用機能は使用できません。 "EnforceUserServiceMetricCapacities" は true になっているがノード容量が構成されていない場合に、Service Fabric  では正常性に関する警告を発行します。

## <a name="other-resources-for-containers"></a>コンテナー用の他のリソース

CPU とメモリに加え、コンテナー用の他のリソース制限を指定できます。 これらの制限は、コード パッケージ レベルで指定され、コンテナーが開始されたときに適用されます。 CPU とメモリとは異なり、クラスター リソース マネージャーはこれらのリソースを認識せず、それらの容量チェックも負荷分散も実行しません。

* *MemorySwapInMB*:コンテナーが使用できるスワップ メモリの量。
* *MemoryReservationInMB*:ノードでメモリの競合が検出された場合にのみ適用される、メモリ ガバナンスのソフト制限。
* *CpuPercent*:コンテナーが使用できる CPU の割合。 サービス パッケージで CPU 要求または制限が指定された場合、このパラメーターは事実上無視されます。
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

## <a name="next-steps"></a>次のステップ

* クラスター リソース マネージャーの詳細については、「[Service Fabric クラスター リソース マネージャーの概要](service-fabric-cluster-resource-manager-introduction.md)」をご覧ください。
* アプリケーション モデル、サービス パッケージ、コード パッケージ、およびこれらにレプリカをマップする方法の詳細については、「[Service Fabric でのアプリケーションのモデル化][application-model-link]」をご覧ください。

<!-- Links -->
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[cluster-resource-manager-description-link]: service-fabric-cluster-resource-manager-cluster-description.md

---
title: "リソース バランサー クラスターの記述 | Microsoft Docs"
description: "障害ドメイン、アップグレード ドメイン、ノードのプロパティ、ノード容量をクラスター リソース マネージャーに指定し、Service Fabric クラスターを記述します。"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: a595a2e24b4c2582998d88f74ca13daba5fe618d

---

# <a name="describing-a-service-fabric-cluster"></a>Service Fabric クラスターの記述
Service Fabric クラスター リソース マネージャーには、クラスターを記述するためのメカニズムが複数用意されています。 クラスター リソース マネージャーは、実行時にこの情報を利用することで、クラスターで実行されているサービスの高可用性を確保します。 クラスター リソース マネージャーはこれらの重要なルールを適用すると同時に、クラスターのリソース消費量の最適化も試みます。

## <a name="key-concepts"></a>主要な概念
クラスター リソース マネージャーでサポートされているクラスターを記述する機能には次のようなものがあります。

* 障害ドメイン
* アップグレード ドメイン
* ノードのプロパティ
* ノード容量

## <a name="fault-domains"></a>障害ドメイン
障害ドメインとは、障害について調整されている領域です。 1 台のコンピューターは&1; つの障害ドメインになります (電源障害、ドライブ障害、問題のある NIC ファームウェアなど、さまざまな理由から単独で停止する可能性があるためです)。 同じイーサネット スイッチに接続されたコンピューターは同じ障害ドメインにあり、これらのコンピューターは単一の電源を共有しています。 ハードウェア障害は当然重複するため、障害ドメインは本質的に階層的であり、Service Fabric での URI として表されます。

独自のクラスターを設定する場合、このような障害のさまざまな領域について熟慮する必要があります。 障害ドメインは正しく設定する必要があります。この情報はサービスを安全に配置するために Service Fabric によって使用されるためです。 Service Fabric では、(なんらかのコンポーネントのエラーによって生じた) 障害ドメインの損失によってサービスが停止するようなサービスの配置は望ましくありません。 Azure 環境では、環境によって提供される障害ドメイン情報を利用して、Service Fabric がユーザーに代わってクラスター内のノードを正しく構成します。

次の図では、障害ドメインに属するすべてのエンティティに色が付けられ、その結果の異なる障害ドメインがすべて表示されています。 この例には、データセンター ("DC")、ラック ("R")、ブレード ("B") があります。 各ブレードが複数の仮想マシンを保持している場合は、障害ドメイン階層に別のレイヤーが存在することがあります。

<center>
![障害ドメインで構成されるノード][Image1]
</center>

Service Fabric クラスター リソース マネージャーは、実行時にクラスター内の障害ドメインを認識し、レイアウトを計画します。  クラスター リソース マネージャーは、特定のサービスのステートフル レプリカまたはステートレス インスタンスを分散させ、別個の障害ドメインに属するように配置を試みます。 このプロセスにより、1 つの障害ドメイン (階層内の任意のレベル) で障害が発生しても、そのサービスの可用性が損なわれることがありません。

Service Fabric のクラスター リソース マネージャーにとって、障害ドメイン階層に存在するレイヤーの数は重要ではありません。 しかし、クラスター リソース マネージャーは、階層の一部の損失がそれより上の層で実行されているサービスに影響しないようにします。 このため、障害ドメイン階層の各レベルのノード数を同じにすることをお勧めします。 レベルのバランスを保つことで、階層の一部にサービスが偏らないようにすることができます。 そうしないと、個々のノードの負荷に不均衡が生じ、特定のドメインで発生した障害の影響が他のドメインの障害よりも大きくなります。

クラスター内の障害ドメインの "ツリー" が不均衡な場合、クラスター リソース マネージャーによるサービスの最適な割り当ての計算が困難になります。 障害ドメインのレイアウトが不均衡の場合、特定のドメインの損失がクラスターの可用性に与える影響が、他のドメインの損失によるものよりも大きくなる可能性があります。 その結果、クラスター リソース マネージャーは&2; つの目的の間で板挟みになります。その目的とは、サービスを配置する際にこの "重たい" ドメインのコンピューターを使用することと、ドメインの損失によって問題が生じないようにサービスを配置することです。 これはどのように表されるでしょうか。

<center>
![異なる&2; つのクラスター レイアウト][Image2]
</center>

上の図では、異なる&2; つのクラスター レイアウトの例を示しています。 最初の例では、ノードは障害ドメイン全体で均等に分散されています。 もう&1; つの例では、1 つの障害ドメインのノードが他よりも多くなっています。 オンプレミスまたは別の環境で独自にクラスターを作成している場合は、検討が必要になります。

Azure では、ノードが含まれる障害ドメインの選択は自動的に行われます。 しかし、プロビジョニングするノードの数によっては、障害ドメインのノードの数に偏りが生じる可能性があります。 たとえば、障害ドメインが&5; つあるときに、特定の NodeType のノードを&7; つプロビジョニングするとします。 この場合、最初の&2; つの障害ドメインではノード数が多くなります。 その後さらに、少数のインスタンスでより多くの NodeType をデプロイすると、問題は悪化します。

## <a name="upgrade-domains"></a>アップグレード ドメイン
アップグレード ドメインも、Service Fabric クラスター リソース マネージャーがクラスターのレイアウトを把握し、障害に対して早めに手を打つための機能です。 アップグレード ドメインは、同時にアップグレードされるノードのセットを定義します。

アップグレード ドメインは障害ドメインに似ていますが、重要な相違点がいくつかあります。 まず、障害ドメインは調整されたハードウェア障害の領域によって厳密に定義されますが、アップグレード ドメインはポリシーによって定義されます。 アップグレード ドメインは、自分で希望の数を決定します。環境によって決定されるわけではありません。 また、アップグレード ドメインは (少なくとも現時点では) 階層にはなっておらず、単純なタグのようなものです。

次の図では、3 つのアップグレード ドメインが&3; つの障害ドメインに分散してストライピングされているようすが示されています。 また、ステートフル サービスの&3; つの異なるレプリカの配置例も示されています。これらのレプリカはそれぞれ、異なる障害ドメインとアップグレード ドメインに含まれています。 この配置であれば、サービスのアップグレードの最中に障害ドメインが&1; つ失われても問題はなく、コードとデータのコピーが&1; つ残ります。

<center>
![障害ドメインとアップグレード ドメインの配置][Image3]
</center>

アップグレード ドメインの数を増やすことは良くもあり、悪くもあります。 アップグレード ドメインを増やすと、アップグレードの各手順が細かくなるため、影響を受けるノードまたはサービスの数が少なくなります。 その結果、一度に移動する必要のあるサービスは減少し、システムに対する変更も少なくなります。 これは信頼性の向上にも寄与する傾向にあります (アップグレード中に発生した問題の影響が及ぶサービスが減少するため)。 アップグレード ドメインを増やすと、アップグレードの影響に対処するために必要とされる、他のノードにおける利用可能なオーバーヘッドも減少します。 たとえば、アップグレード ドメインが 5 つある場合、各アップグレード ドメイン内のノードではトラフィックの約 20% が処理されています。 アップグレードのためにそのアップグレード ドメインを停止する必要がある場合、その負荷は他のドメインで処理される必要があります。 アップグレード ドメインを増やすと、クラスターの他のノードで保持される必要があるオーバーヘッドが少なくなります。

アップグレード ドメインを増やすことの欠点は、アップグレード時間が長くなる傾向があることです。 アップグレード ドメインの完了後、Service Fabric は少しの間待機してから続行します。 この遅延の目的は、アップグレードによって生じた問題が表面化し、検出される機会を用意することです。 正しくない変更が一度に多くのサービスに影響を及ぼすのを防ぐことができるため、このトレードオフは許容されています。

アップグレード ドメインが少なすぎること自体に副作用はほとんどありませんが、各アップグレード ドメインを停止してアップグレードしている間は、全体的な容量の大部分が使用できません。 たとえば、アップグレード ドメインが 3 つしかない場合は、サービスまたはクラスター容量全体の約 3 分の 1 が一度に停止します。 ワークロードを処理するために残りのクラスターの容量を十分確保する必要があるので、そのようにサービスの多くの部分を一度に停止することは望ましくありません。 このバッファーを確保すると、通常、確保しない場合と比べてこれらのノードにかかる負荷が減少し、サービス実行のコストが増加します。

環境内の障害ドメインまたはアップグレード ドメインの合計数に制限はありません。また、重複の制約もありません。 これまで見てきた一般的な構造は次のようになります。

* 1 対 1 でマップされる障害ドメインとアップグレード ドメイン
* ノードごとに&1; つのアップグレード ドメイン (物理または仮想 OS インスタンス)
* "ストライプ" または "マトリックス" モデル (障害ドメインとアップグレード ドメインがマトリックスを形成。通常はコンピューターが表中で対角線を描くように配置される)

<center>
![障害ドメインとアップグレード ドメインのレイアウト][Image4]
</center>

それぞれのレイアウトに長所と短所があり、どれがベストであるかを断言することはできません。 たとえば、1FD 対 1UD モデルは設定が簡単です。 1 UD/ノード モデルは、これまで少数のコンピューターを管理してきたユーザーが慣れているのに似たモデルで、各コンピューターが個別に停止します。

最も一般的なモデル (Azure で使用されているモデル) は、FD/UD マトリックスです。このモデルでは、FD と UD がテーブルを形成し、ノードが対角線に沿って配置されます。 ノードが分散するか密集するかは、FD と UD の数に対するノードの合計数によって決まります。 つまり、クラスターが十分に大きい場合は、上の図の右下にあるオプションに示すように、ほぼすべてが高密度マトリックス パターンのようになります。

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>障害ドメインとアップグレード ドメインの制約および結果の動作
クラスター リソース マネージャーは、障害ドメインとアップグレード ドメイン全体にサービスを分散させたいという希望を制約として処理します。 制約について詳しくは、 [この記事](service-fabric-cluster-resource-manager-management-integration.md)を参照してください。 障害ドメインとアップグレード ドメインの制約は次のことを表します: "特定のサービス パーティションについて、2 つのドメインでのサービス オブジェクト (ステートレス サービス インスタンスまたはステートフル サービス レプリカ) 数の差が "*1 よりも大きく*" なってはならない"。  この実質的な意味は、障害ドメインまたはアップグレード ドメインの制約に違反するため、特定のサービスについて特定の移動または配置が無効になる可能性があるということです。

1 つ例を見てみましょう。 たとえば、6 つのノードを持つクラスターを、5 つの障害ドメインと&5; つのアップグレード ドメインで構成したとします。

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

ここで、TargetReplicaSetSize に&5; を指定してサービスを作成します。 レプリカを N1 ～ N5 に配置します。 実際は、サービスをいくつ作成しても N6 が使用されることはありません。 しかし、なぜでしょうか。 現在のレイアウトと N6 を選択した場合のレイアウトの差異を見てみましょう。

現在のレイアウトと障害ドメインおよびアップグレード ドメインごとのレプリカの合計数は次のようになります。

|  | FD0 | FD1 | FD2 | FD3 | FD4 | 総 UD 数 |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

このレイアウトは、障害ドメインとアップグレード ドメインごとのノードという点でバランスが取れています。 障害ドメインとアップグレード ドメインごとのレプリカ数という点でもバランスが取れています。 各ドメインのノード数、レプリカ数は同じです。

次に、N2 ではなく N6 を使用した場合にどうなるかを見てみましょう。 レプリカはどのように分散されるでしょうか。

|  | FD0 | FD1 | FD2 | FD3 | FD4 | 総 UD 数 |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

お気付きでしょうか。 このレイアウトは、障害ドメインの制約の定義に違反しています。 FD0 には&2; つのレプリカがある一方で FD1 にはレプリカがありません。FD0 と FD1 の差は合計&2; になります。 この配置はクラスター リソース マネージャーによって許可されません。 同様に (N1 と N2 ではなく) N2 と N6 を選択すると、次のようになります。

|  | FD0 | FD1 | FD2 | FD3 | FD4 | 総 UD 数 |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

このレイアウトは障害ドメインの点でバランスが取れていますが、今度はアップグレード ドメインの制約に違反しています (UD0 にはレプリカがない一方で UD1 には&2; つあるため)。 このレイアウトも無効です。

## <a name="configuring-fault-and-upgrade-domains"></a>障害ドメインとアップグレード ドメインの構成
障害ドメインとアップグレード ドメインは、Azure でホストされる Service Fabric デプロイで自動的に定義されます。 Service Fabric によって Azure から環境情報が取得されて使用されます。

独自のクラスターを作成する (または開発で特定のトポロジを実行する) 場合は、障害ドメインとアップグレード ドメインの情報を自分で入力することになります。 この例では、(それぞれ&3; つのラックを備えた)&3; つの "データセンター" にまたがる、ノードが&9; つのローカル開発クラスターを定義します。 このクラスターには、それらの&3; つのデータセンターにストライピングされる&3; つのアップグレード ドメインもあります。 クラスター マニフェスト テンプレートでは、次のようになります。

ClusterManifest.xml

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one-box /one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```

スタンドアロン デプロイの ClusterConfig.json 経由

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> Azure デプロイでは、障害ドメインとアップグレード ドメインは Azure によって割り当てられます。 そのため、Azure のインフラストラクチャ オプション内にあるノードとロールの定義に、障害ドメインまたはアップグレード ドメインに関する情報は含まれません。
>
>

## <a name="placement-constraints-and-node-properties"></a>配置の制約とノードのプロパティ
場合によっては (実際には、ほとんどの場合)、特定のワークロードが、クラスター内の特定のノードまたは特定のノード セットだけで確実に実行されるようにしたいことがあります。 たとえば、ワークロードの中に GPU や SSD を必要とするものとしないものが混在している場合があります。 特定のワークロードをターゲットにするハードウェアの好例は、ほぼすべての n 層アーキテクチャです。 これらのアーキテクチャでは、特定のコンピューターがアプリケーションのフロント エンド/インターフェイス提供側として機能します (そのため、おそらくインターネットに公開されます)。 (多くの場合異なるハードウェア リソースを備えた) 別のセットのコンピューターは、コンピューティングまたはストレージ レイヤーの作業を処理します (通常、インターネットに公開されません)。 Service Fabric では、マイクロサービスであっても、特定のワークロードを特定のハードウェア構成で実行する必要が生じる状況があると想定されています。たとえば、次のような状況です。

* 既存の n 層アプリケーションが Service Fabric 環境に "リフト アンド シフト (移行)" された。
* パフォーマンス、スケール、またはセキュリティの分離の理由により、ワークロードを特定のハードウェアで実行する必要がある。
* ポリシーまたはリソース消費の理由により、特定のワークロードを他のワークロードから切り離す必要がある。

こうした構成をサポートするために、Service Fabric には、ノードに適用できるタグという優れた概念があります。 これは、配置の制約と呼ばれます。 配置の制約を使用すると、特定のサービスを実行する場所を指定できます。 一連の制約は拡張でき、任意のキー/値のペアを使用できます。

<center>
![クラスター レイアウトの異なるワークロード][Image5]
</center>

ノード上の各種キー/値のタグは、ノードの配置 "*プロパティ*" (または単にノード プロパティ) と呼ばれます。 ノード プロパティで指定する値には、string、bool、signed long を使用できます。 サービスを実行できるクラスターの場所が制約されるため、サービスのステートメントは配置の "*制約*" と呼ばれます。 制約として使用できるのは、クラスター内の別のノード プロパティに対して実行される任意のブール ステートメントです。 これらのブール ステートメントでの有効なセレクターは次のとおりです。

1) 特定のステートメントを作成するための条件確認

| ステートメント | 構文 |
| --- |:---:|
| "等しい" | "==" |
| "等しくない" | "!=" |
| "より大きい" | ">" |
| "以上" | ">=" |
| "より小さい" | "<" |
| "以下" | "<=" |

2) グループ化と論理演算のブール ステートメント

| ステートメント | 構文 |
| --- |:---:|
| "および" | "&&" |
| "または" | "&#124;&#124;" |
| "ではない" | "!" |
| "1 つのステートメントとしてグループ化" | "()" |

基本的な制約ステートメントの例をいくつか紹介します。

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

ステートメント全体の評価が "True" のノードのみ、サービスを配置させることができます。 プロパティが定義されていないノードは、そのプロパティを含む配置の制約とは一致しません。

Service Fabric では、自動的に適用できる既定のノード プロパティがいくつか定義されています。これらのプロパティについては、ユーザーが定義する必要はありません。 この記事の執筆時点では、**NodeType** と **NodeName** が、既定のプロパティとして各ノードで定義されています。 したがって、たとえば配置の制約は `"(NodeType == NodeType03)"` と記述できます。 一般的には、NodeType プロパティが最もよく使用されるプロパティの&1; つです。 このプロパティは、コンピューターの種類と 1 対 1 で対応しているため便利です。また、コンピューターの種類は、従来の n 層アプリケーション アーキテクチャのワークロードの種類に対応しています。

<center>
![配置の制約とノードのプロパティ][Image6]
</center>

たとえば、特定のノード タイプに対して次のノード プロパティが定義されているとします。

ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

スタンドアロン デプロイの ClusterConfig.json 経由または Azure でホストされたクラスターの Template.json 経由。 クラスターの Azure Resource Manager テンプレートでは、ノード タイプの名前などはパラメーター化されることが多く、"NodeType01" ではなく "[parameters('vmNodeType1Name')]" のようになります。

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

サービスに対して、次のようにサービスの配置の "*制約*" を作成できます。

C#

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// add other required servicedescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

NodeType01 のすべてのノードが有効であることを確認したら、そのノード タイプも選択できます。

サービスの配置の制約で優れているのは、実行時に動的に更新される点です。 そのため、必要に応じて、クラスター内でのサービスの移動や、要件の追加/削除などを行うことができます。このような変更が進行中でも、Service Fabric によって、サービスは確実に稼働し続け、利用することができます。

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Powershell:

```posh
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

配置の制約 (および、これから説明するその他多数のオーケストレーター コントロール) は、すべてのサービス インスタンスに対してそれぞれ指定されます。 更新すると、以前に指定された内容は常に置き換えられます (上書きされます)。

ノードのプロパティはクラスター定義を介して定義されるため、これらのプロパティを更新するにはクラスターをアップグレードする必要があります。 ノードのプロパティのアップグレードには、影響を受ける各ノードの停止と復帰が必要です。

## <a name="capacity"></a>容量
クラスター内のリソース消費量の管理は、オーケストレーターの重要なジョブの&1; つです。 サービスを効率的に実行するうえで最も望ましくないのは、多数のホット ノードとコールド ノードが混在することです。 ホット ノードはリソースの競合とパフォーマンスの低下を招き、コールド ノードはリソースの浪費/コストの増加につながります。 バランスについて学習する前に、まずはノードのリソース不足を確実に防ぐことだけを考えてみましょう。

Service Fabric はリソースを `Metrics` として表します。 メトリックとは、Service Fabric に対して記述する論理または物理リソースです。 たとえば、"WorkQueueDepth"、"MemoryInMb" などがメトリックです。 メトリックの構成と使用の詳細については、[こちらの記事](service-fabric-cluster-resource-manager-metrics.md)を参照してください

メトリックは、配置の制約ともノード プロパティとも異なります。 ノード プロパティはノード自体の静的な記述子ですが、メトリックは、ノードが所有し、サービスがノードで実行されているときに消費されるリソースに関するものです。 ノード プロパティは "HasSSD" にすることができるほか、true または false に設定できます。 (サービスによって消費される) その SSD の使用可能な容量は、"DriveSpaceInMb" などのメトリックになります。 ノードは、ドライブ上の予約されていない容量の総量に対し、"DriveSpaceInMb" の容量を用意します。 実行時にサービスが使用したメトリックの量は、そのサービスによってレポートされます。

配置の制約とノード プロパティの場合と同様に、メトリックの名前が意味する内容は Service Fabric クラスター リソース マネージャーによって認識されないことに注意が必要です。 メトリックの名前は単なる文字列です。 あいまいな場合は作成したメトリック名の一部として単位を宣言することをお勧めします。

すべてのリソース "*分散*" をオフにしても、Service Fabric クラスター リソース マネージャーは依然として容量超過のノードが出ないように調整しようとします。 これは通常、クラスター全体に余裕がある限り可能です。 容量とは、ノードにどのくらいのリソースがあるかを理解するためにクラスター リソース マネージャーが使用するもう&1; つの "*制約*" です。 容量の残りもクラスター全体で追跡されます。 このサービス レベルの容量と消費量の両方が、メトリックで表現されます。 たとえば、メトリックは "MemoryInMb" であり、ある特定のノードでは "MemoryInMb" の容量が 2,048 です。 そのノードで実行中のサービスで "MemoryInMb" のうち 64 が現在使用されていることが、このサービスによってレポートされます。

実行時、クラスター リソース マネージャーは、各ノードに存在する各リソースの量とリソース残量を追跡します。 これは、そのノードで実行されている各サービスの宣言された使用量をノードの容量から差し引くことで算出されます。 Service Fabric クラスター リソース マネージャーは、ノードが容量超過にならないように、この情報を使用してレプリカを配置または移動する場所を検討します。

C#:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "MemoryInMb";
metric.PrimaryDefaultLoad = 64;
metric.SecondaryDefaultLoad = 64;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,64,64)
```
<center>
![クラスター ノードと容量][Image7]
</center>

クラスター マニフェストで定義した容量を確認できます。

ClusterManifest.xml

```xml
    <NodeType Name="NodeType02">
      <Capacities>
        <Capacity Name="MemoryInMb" Value="2048"/>
        <Capacity Name="DiskInMb" Value="512000"/>
      </Capacities>
    </NodeType>
```

スタンドアロン デプロイの ClusterConfig.json 経由または Azure でホストされたクラスターの Template.json 経由。 クラスターの Azure Resource Manager テンプレートでは、ノード タイプの名前などはパラメーター化されることが多く、"NodeType02" ではなく "[parameters('vmNodeType2Name')]" のようになります。

```json
"nodeTypes": [
    {
        "name": "NodeType02",
        "capacities": {
            "MemoryInMb": "2048",
            "DiskInMb": "512000"
        }
    }
],
```

サービスの負荷が動的に変わる可能性もあります (実際よくあります)。 レプリカの負荷が 64 から 1,024 に変更されたが、それを実行しているノードの残りの容量が 512 ("MemoryInMb" メトリック) だったとします。 このとき、このノードには十分な空き領域がないため、レプリカまたはインスタンスの配置は無効になります。 このノード上のレプリカとインスタンスの合計使用量がノードの容量を超過した場合にも、配置が無効になる可能性があります。 いずれの場合も、クラスター リソース マネージャーが作動してノードの容量超過が抑制されます。 これは、そのノードにある&1; つ以上のレプリカまたはインスタンスを別のノードに移動することによって実行されます。 クラスター リソース マネージャーは、レプリカを移動する際に、これらの移動にかかるコストを最小限に抑えようとします。 移動にかかるコストについては、[こちらの記事](service-fabric-cluster-resource-manager-movement-cost.md)で説明されています。

## <a name="cluster-capacity"></a>クラスターの容量
では、クラスター全体がいっぱいにならないようにするにはどうすればよいのでしょうか。 動的な負荷については、クラスター リソース マネージャーでできることはあまりありません。 サービスにかかる負荷は、クラスター リソース マネージャーの動作に関係なく急増する場合があります。 そのため、今日クラスターに余裕があっても、明日には有名になって力不足になる可能性があります。 とは言うものの、基本的な問題を回避するために制御できることがいくつかあります。 まず、クラスターがいっぱいになる原因となるワークロードが新しく作成されないようにします。

たとえば、ステートレス サービスを作成するとします。このサービスには負荷が関連付けられています (既定および動的な負荷レポートの詳細については後述)。 このサービスでは "DiskSpaceInMb" メトリックが使用されるとします。 また、サービスのインスタンスごとに&5; ユニットの "DiskSpaceInMb" が消費されます。 作成するサービス インスタンスは&3; つです。 そこで、 これでセットアップは終了です。 つまり、これらのサービス インスタンスを作成するには、クラスターに 15 ユニットの "DiskSpaceInMb" が必要です。 クラスター リソース マネージャーは各メトリックの全体の容量と消費量を継続的に計算するため、クラスターに十分な領域があるかどうかを簡単に特定できます。 十分な領域がない場合、クラスター リソース マネージャーはサービス作成の呼び出しを拒否します。

要件は 15 ユニットが使用できることのみであるため、この領域はさまざまな方法で割り当てることができます。 たとえば、異なる 15 個のノードそれぞれに 1 つずつユニットが残っていても、5 つの異なるノードに 3 つずつユニットが残っていてもかまいません。 3 つのノードに&5; つの利用可能なユニットがある状態になるように配置し直せるようであれば、クラスター リソース マネージャーは最終的にサービスを配置します。 クラスター全体がほぼいっぱいである場合、すべてのサービスが "大量の容量を必要とする" 場合、またはその両方である場合を除けば、ほぼ必ずこのような再配置が可能です。

## <a name="buffered-capacity"></a>バッファーの容量
クラスター リソース マネージャーには、クラスター容量を全体管理するのに役立つ機能がもう&1; つあります。それは、各ノードで指定された容量に用意される予約済みのバッファーの概念です。 バッファーの容量では、ノード全体の容量の一部を、アップグレード時とノード障害時のサービス配置に使用する専用の容量として予約できます。 現時点では、バッファーは、すべてのノードを対象にグローバルに、クラスター定義を介してメトリックごとに指定します。 予約容量に選択する値は、クラスターにある障害ドメインとアップグレード ドメインの数の関数であり、希望するオーバーヘッドの数です。 障害ドメインとアップグレード ドメインを増やすと、バッファーの容量により小さい数を指定できます。 ドメインを増やすと、アップグレード時と障害時に使用できないクラスターの量が少なくなると期待できます。 バッファーの割合の指定は、メトリックに対してノード容量を指定した場合にのみ有効です。

バッファー容量を指定する方法の例を次に示します。

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="DiskSpace" Value="0.10" />
            <Parameter Name="Memory" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

スタンドアロン デプロイの ClusterConfig.json 経由または Azure でホストされたクラスターの Template.json 経由:

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "DiskSpace",
          "value": "0.10"
      },
      {
          "name": "Memory",
          "value": "0.15"
      },
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      }
    ]
  }
]
```

メトリックのバッファーの容量がクラスターに不足している場合、新しいサービスの作成は失敗します。 これにより、クラスターで予備のオーバーヘッドが十分に確保され、アップグレードと障害によってノードの容量超過が発生することがなくなります。 バッファーの容量はオプションですが、メトリックの容量を定義するクラスターでは使用することをお勧めします。

クラスター リソース マネージャーでは、PowerShell とクエリ API 経由でこの情報が公開されます。 そのため、クラスターで使用されている各メトリックのバッファー容量の設定、合計容量、および現在の消費量を確認できます。 その出力の例を次に示します。

```posh
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2016 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2016 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## <a name="next-steps"></a>次のステップ
* クラスター リソース マネージャー内のアーキテクチャと情報フローについては、[こちらの記事](service-fabric-cluster-resource-manager-architecture.md)をご確認ください
* 最適化メトリックの定義は、負荷を分散するのではなく、ノードで統合する方法の&1; つです。 最適化を構成する方法については、 [この記事](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* 最初から開始して、 [Service Fabric クラスター リソース マネージャーの概要を確認するにはこちらを参照してください](service-fabric-cluster-resource-manager-introduction.md)
* クラスター リソース マネージャーでクラスターの負荷を管理し、分散するしくみについては、 [負荷分散](service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png



<!--HONumber=Jan17_HO1-->



---
title: Azure Service Fabric クラスターのデプロイを計画する
description: Azure への運用環境 Service Fabric クラスターのデプロイの計画と準備について説明します。
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 9de59811397eb47809c6d71f608e43beae5bfadb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97109625"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>クラスターのデプロイを計画および準備する

運用環境クラスターのデプロイの計画と準備は非常に重要です。  考慮すべき要因が数多くあります。  この記事では、クラスターのデプロイを準備する手順について説明します。

## <a name="read-the-best-practices-information"></a>ベスト プラクティス情報を読む
Azure Service Fabric アプリケーションとクラスターを正常に管理するために、実行することを強くお勧めする操作があります。この操作によって運用環境の信頼性を最適化します。  詳細については、「[Azure Service Fabric のアプリケーションとクラスターに関するベスト プラクティス](service-fabric-best-practices-overview.md)」を参照してください。

## <a name="select-the-os-for-the-cluster"></a>クラスターの OS を選択する
Service Fabric を使用すると、Windows Server または Linux を実行するすべての VM またはコンピューター上に Service Fabric クラスターを作成できます。  クラスターをデプロイする前に、OS を選択する必要があります。Windows または Linux。  クラスター内のすべてのノード (仮想マシン) が同じ OS を実行するため、同じクラスター内で Windows VM と Linux VM を混在させることはできません。

## <a name="capacity-planning"></a>容量計画
容量計画は、運用環境へのデプロイにおいて重要なステップとなります。 ここでは、そのプロセスの一環として考慮すべき事柄の一部を取り上げます。

* クラスターのノード タイプの初期数 
* ノード タイプごとの特性 (サイズ、インスタンス数、プライマリ/非プライマリ、インターネット接続、VM 数など)
* クラスターの信頼性と耐久性の特徴

### <a name="select-the-initial-number-of-node-types"></a>ノード タイプの初期数を選択する
最初に、作成するクラスターの使用目的について考えておく必要があります。 このクラスターにどのような種類のアプリケーションを展開する予定があるでしょうか。 アプリケーションに複数のサービスが存在するか、またその中に、外部に公開 (インターネットに接続) しなければならないサービスはあるか。 アプリケーションの構成要素として、インフラストラクチャ ニーズの異なる複数のサービスが存在するか (大容量の RAM が必要、高い CPU 処理能力が必要など)。 Service Fabric クラスターは、複数のノード タイプ (1 つのプライマリ ノード タイプと 1 つ以上の非プライマリ ノード タイプ) で構成できます。 ノード タイプはそれぞれ 1 つの仮想マシン スケール セットに対応付けられます。 各ノードの種類は、個別にスケール アップまたはスケール ダウンすることができ、さまざまなセットのポートを開き、異なる容量のメトリックスを持つことができます。 [ノードのプロパティと配置の制約][placementconstraints]は、特定のサービスを特定のノード タイプに制約するように設定できます。  詳細については、[Service Fabric クラスターの容量計画](service-fabric-cluster-capacity.md)に関する記事を参照してください。

### <a name="select-node-properties-for-each-node-type"></a>各ノード タイプのノード プロパティを選択する
ノード タイプは、関連付けられたスケール セット内の VM の VM SKU、数、プロパティを定義します。

各ノード タイプの最小 VM サイズは、そのノード タイプに選択した[耐久性レベル][durability]によって決まります。

プライマリ ノード タイプの最低 VM 数は、選択した[信頼性レベル][reliability]によって決まります。

[プライマリ ノード タイプ](service-fabric-cluster-capacity.md#primary-node-type)、[非プライマリ ノード タイプのステートフル ワークロード](service-fabric-cluster-capacity.md#stateful-workloads)、[非プライマリ ノード タイプのステートレス ワークロード](service-fabric-cluster-capacity.md#stateless-workloads)に関する最小推奨事項を参照してください。

最小ノード数を超える数は、このノード タイプで実行するアプリケーション/サービスのレプリカ数に基づいて決定する必要があります。  「[Service Fabric アプリケーションの容量計画](service-fabric-capacity-planning.md)」をご覧いただくと、アプリケーションの実行に必要なリソースを見積もるのに役立ちます。 変化するアプリケーションのワークロードに合わせて調整するために、後でいつでもクラスターをスケールアップまたはスケールダウンできます。 

#### <a name="use-ephemeral-os-disks-for-virtual-machine-scale-sets"></a>仮想マシン スケール セットにエフェメラル OS ディスクを使用する

"*エフェメラル OS ディスク*" は、ローカル仮想マシン (VM) 上に作成されるストレージであり、リモート Azure Storage には保存されません。 エフェメラル OS ディスクは、従来の永続 OS ディスクと比べて、次のような特徴を持っているため、すべての Service Fabric ノードの種類 (プライマリとセカンダリ) で推奨されます。

* OS ディスクへの読み取り/書き込み待機時間が短縮される
* ノード管理操作を素早くリセット/再イメージ化できる
* 全体的なコストが削減される (ディスクは無料であり、追加のストレージ コストは発生しません)

エフェメラル OS ディスクは特定の Service Fabric の機能ではなく、Service Fabric ノードの種類にマップされる Azure "*仮想マシン スケール セット*" の機能です。 これらを Service Fabric で使用するには、クラスターの Azure Resource Manager テンプレートで次のものが必要です。

1. ノードの種類で、エフェメラル OS ディスクに対して [サポートされている Azure VM サイズ](../virtual-machines/ephemeral-os-disks.md)が指定されており、その VM サイズに、OS ディスク サイズをサポートするのに十分なキャッシュ サイズが含まれていることを確認します (下記の「*注意*」を参照)。次に例を示します。

    ```xml
    "vmNodeType1Size": {
        "type": "string",
        "defaultValue": "Standard_DS3_v2"
    ```

    > [!NOTE]
    > VM 自体の OS ディスク サイズ以上のキャッシュを持つ VM サイズを選択してください。そうしないと、(最初は受け入れられても、) Azure のデプロイでエラーが発生する可能性があります。

2. `2018-06-01` 以降の仮想マシン スケール セットのバージョン (`vmssApiVersion`) を指定してください。

    ```xml
    "variables": {
        "vmssApiVersion": "2018-06-01",
    ```

3. デプロイ テンプレートの [virtual machine scale set]\(仮想マシン スケール セット\) セクションで、`diffDiskSettings` に対して `Local` オプションを指定します。

    ```xml
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
        "virtualMachineProfile": {
            "storageProfile": {
                "osDisk": {
                        "caching": "ReadOnly",
                        "createOption": "FromImage",
                        "diffDiskSettings": {
                            "option": "Local"
                        },
                }
            }
        }
    ```

> [!NOTE]
> OS ディスクは、OS のアップグレード時に失われるため、ユーザーアプリケーションは OS ディスクに依存関係、ファイル、成果物を持つことはできません。

> [!NOTE]
> エフェメラル ディスクを使用するために、エフェメラルではない既存のディスク VMSS をインプレース アップグレードすることはできません。
> 移行するには、ユーザーは、エフェメラル ディスクを含む新しい nodeType を[追加](./virtual-machine-scale-set-scale-node-type-scale-out.md)して、ワークロードをその新しい nodeType に移動させ、既存の nodeType を[削除](./service-fabric-how-to-remove-node-type.md)する必要があります。
>

詳細およびその他の構成オプションについては、「[Azure VM のエフェメラル OS ディスク](../virtual-machines/ephemeral-os-disks.md)」を参照してください。 


### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>クラスターの耐久性レベルと信頼性レベルを選択する
耐久性レベルは、ご利用の VM が、基になる Azure インフラストラクチャに対して持つ特権をシステムに表明する目的で使用します。 プライマリ ノード タイプでは、Service Fabric がこの特権を使って、システム サービスやステートフル サービスのクォーラム要件に影響を及ぼす、VM レベルのインフラストラクチャ要求 (VM の再起動、VM の再イメージ化、VM の移行など) を一時停止させることができます。 非プライマリ ノード タイプの場合も、Service Fabric がこの特権の下で、ステートフル サービスのクォーラム要件に影響を及ぼす、VM レベルのインフラストラクチャ要求 (VM の再起動、VM の再イメージ化、VM の移行など) を一時停止させることができます。  各レベルの利点、およびどのレベルをどの時点で使用するかについての推奨事項については、「[クラスターの耐久性の特徴][durability]」を参照してください。

信頼性レベルは、クラスターのプライマリ ノード タイプで実行するシステム サービスのレプリカ数を設定する目的で使用します。 レプリカ数が増えるほど、クラスター内のシステム サービスの信頼性が上がります。  各レベルの利点、およびどのレベルをどの時点で使用するかについての推奨事項については、「[クラスターの信頼性の特徴][reliability]」を参照してください。 

## <a name="enable-reverse-proxy-andor-dns"></a>リバース プロキシおよび/または DNS を有効にする
クラスター内の各ノードは同じローカル ネットワーク上にあるため、クラスター内で相互接続しているサービスは、通常、他のサービスのエンドポイントに直接アクセスできます。 サービス間の接続を容易にするために、Service Fabric には追加サービスとして[DNS サービス](service-fabric-dnsservice.md)と[リバース プロキシ サービス](service-fabric-reverseproxy.md)が用意されています。  クラスターをデプロイするときに両方のサービスを有効にできます。

コンテナー化されたサービスなど多くのサービスには既存の URL 名が含まれるため、標準の DNS プロトコル (Naming Service プロトコルではなく) を使用してこれらを解決できます。これは特にアプリケーションの移行 (リフトアンドシフト) シナリオで便利です。 まさにこれが、DNS サービスの役割です。 DNS 名をサービス名にマップして、エンドポイントの IP アドレスを解決できます。

リバース プロキシは、HTTP エンドポイント (HTTPS を含む) を公開するクラスター内のサービスを処理します。 リバース プロキシは、特定の URI 形式を提供することによって他のサービスの呼び出しを大幅に簡略化します。  リバース プロキシは、あるサービスが別のサービスと通信するために必要な解決、接続、再試行の各ステップも処理します。

## <a name="prepare-for-disaster-recovery"></a>ディザスター リカバリーの準備
高可用性を実現するうえで欠かせないのは、サービスがあらゆる種類の障害を切り抜けられるようにすることです。 これは、計画外の障害や、制御できない障害に関しては特に重要です。 [ディザスター リカバリーの準備](service-fabric-disaster-recovery.md)に関する記事では、正しくモデル化および管理されていない場合に、災害につながる可能性がある一般的な障害モードをいくつか取り上げて説明します。 さらに、災害が発生した場合の軽減策や対処方法についても解説します。

## <a name="production-readiness-checklist"></a>運用環境の準備状況チェックリスト
お使いのアプリケーションとクラスターは、運用環境のトラフィックに対応する準備ができていますか。 クラスターを運用環境にデプロイする前に、「[運用環境の準備状況チェックリスト](service-fabric-production-readiness-checklist.md)」の項目を実行してください。 このチェックリストの項目を実行して、アプリケーションとクラスターの円滑な稼働を維持してください。 運用環境に移行する前に、これらの項目すべてをチェック済みにすることを強くお勧めします。

## <a name="next-steps"></a>次のステップ
* [Windows を実行する Service Fabric クラスターを作成する](service-fabric-best-practices-overview.md)
* [Linux を実行する Service Fabric クラスターを作成する](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster